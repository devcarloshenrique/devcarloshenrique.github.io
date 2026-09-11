---
title: mec-notes
description: Bloco de notas flutuante e sticky notes moderno para Windows com persistência SQLite e engine WYSIWYG.
category: Sistemas & Desktop
tags: [Tauri v2, Rust, React 18, TypeScript, Tailwind CSS, SQLite (rusqlite), WYSIWYG Muya, Prism.js]
github: https://github.com/devcarloshenrique/mec-notes
architecture: Frontend em React 18/TypeScript com Tailwind CSS sobre o runtime de baixo overhead do Tauri v2. Backend em Rust com Rusqlite para persistência local atômica e gerenciamento nativo de janelas Win32.
image: /projects/mec-notes.png
gallery: [/projects/mec-notes.png]
featured: true
weight: 1
---

Desenvolvido com foco em máxima produtividade e performance, o mec-notes opera como uma janela flutuante com suporte a transparência, atalhos globais de sistema (Ctrl+Shift+Space), persistência local ultra-rápida em SQLite nativo e baixo consumo de memória RAM (< 25MB). Conta com suporte a notas adesivas destacáveis com sincronização bidirecional em tempo real e engine WYSIWYG Muya.

## Decisões de Arquitetura

Frontend em React 18/TypeScript com Tailwind CSS sobre o runtime de baixo overhead do Tauri v2. Backend em Rust com Rusqlite para persistência local atômica e gerenciamento nativo de janelas Win32.

## Destaques de Implementação

- **Modo Flutuante e Modo Janela:** janela compacta sem bordas (Always on Top) ou janela expandida com decorações nativas do Windows.
- **Notas Adesivas Destacáveis (Sticky Notes):** janelas independentes estilo post-it com persistência de posição (x, y) e dimensões no SQLite.
- **Sincronização bidirecional em tempo real** entre a janela principal e as notas adesivas via barramento de eventos IPC do Tauri.
- **Atalho global de sistema** reconfigurável e integração com a bandeja do sistema (System Tray) com menu de contexto.
- **Engine de edição WYSIWYG Muya** adaptada do MarkText com syntax highlighting via Prism.js e suporte a auto-save.
