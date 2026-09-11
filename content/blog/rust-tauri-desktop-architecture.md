---
title: "Construindo Aplicações Desktop com Rust e Tauri v2: Por que abandonei o Electron no mec-notes"
date: 2026-09-09
summary: Como reduzi o consumo de memória de 350MB para menos de 25MB e o tempo de boot para 180ms criando um aplicativo desktop flutuante em Rust e React.
categories: [Sistemas & Rust]
tags: [Rust, Tauri v2, React, Desktop, Performance, SQLite]
readingTime: 7 min de leitura
---

## A Problemática do Electron para Ferramentas Utilitárias

Durante anos, desenvolvedores web recorreram ao Electron para empacotar aplicações React no desktop. Embora a agilidade de prototipagem seja inegável, o custo em recursos de hardware é proibitivo para ferramentas utilitárias que devem permanecer abertas em segundo plano o dia todo:

- **Instância dedicada do Chromium**: Cada janela consome centenas de megabytes.
- **Node.js runtime embutido**: Aumenta o tamanho final do binário para mais de 120MB.
- **Latência de inicialização perceptível**: Inicialização a frio frequentemente superior a 1,5 segundos.

Quando iniciei o desenvolvimento do **mec-notes** — um bloco de notas flutuante e frameless voltado para captura rápida de ideias enquanto o usuário programa ou pesquisa —, a premissa fundamental era ser imperceptível no consumo da CPU e memória RAM.

---

## Por que Tauri v2 com Rust?

O Tauri adota uma abordagem radicalmente diferente: em vez de incluir o Chromium, ele reutiliza o motor de renderização nativo do sistema operacional (WebView2 no Windows) e delega toda a camada lógica pesada, threads, sistema de arquivos e persistência para binários compilados em **Rust**.

### Comparativo de Recursos

| Métrica | Electron Médio | mec-notes (Tauri v2 + Rust) | Ganho |
| :--- | :--- | :--- | :--- |
| Consumo de RAM (Idle) | ~280MB - 420MB | **18MB - 24MB** | **~93% menos** |
| Tamanho do Instalador | ~90MB | **~4.2MB** | **~95% menor** |
| Tempo de Inicialização | ~1.8s | **~180ms** | **10x mais rápido** |

---

## Comunicação Segura e Tipada via IPC

No Tauri, a fronteira entre JavaScript e Rust é mediada por chamadas de comando IPC assíncronas. Podemos invocar funções Rust diretamente a partir de hooks do React com forte segurança de tipos:

```rust
// src-tauri/src/commands.rs
#[tauri::command]
pub async fn save_note_content(
    state: tauri::State<'_, AppDb>,
    id: String,
    content: String,
) -> Result<NoteResponse, String> {
    state.sqlite_pool
        .execute("UPDATE notes SET content = ?1, updated_at = datetime('now') WHERE id = ?2", [content, id])
        .map_err(|e| e.to_string())?;

    Ok(NoteResponse { success: true })
}
```

No lado do React, a invocação é limpa e intuitiva:

```typescript
// src/hooks/useNotes.ts
import { invoke } from '@tauri-apps/api/core';

export async function persistNote(id: string, content: string) {
  try {
    await invoke('save_note_content', { id, content });
  } catch (error) {
    console.error('Falha ao persistir nota no SQLite nativo:', error);
  }
}
```

---

## Gerenciamento de Janelas e Persistência Atômica

Para o mec-notes, configuramos comportamentos de janela avançados usando a API de janelas do Tauri:

1. **Always-on-top condicional**: Alternável via atalho global de teclado (`Ctrl + Shift + Space`).
2. **Janela Frameless com sombras nativas**: Sem barra de título padrão do Windows, com controles personalizados em React e drag nativo.
3. **Persistência em SQLite via WAL mode**: Write-Ahead Logging para evitar concorrência de I/O de disco durante digitações velozes com debouncing inteligente.

### Principais Lições Aprendidas

1. **Rust não é um bicho de sete cabeças**: Para quem vem de TypeScript tipado rigorosamente, o compilador do Rust atua como um parceiro estrito que impede memory leaks em tempo de compilação.
2. **Distribuição Simplificada**: Gerar instaladores MSI e pacotes leves sem precisar de instaladores inchados melhora significativamente a taxa de adoção do usuário final.
