---
title: "Otimizando Consultas e Pipelines de ETL para Sistemas Críticos em Ambientes Bancários"
date: 2025-02-19
summary: Práticas reais para lidar com alto volume transacional, estratégias de particionamento e índices parciais para reduzir latência de relatórios em PostgreSQL.
categories: [Banco de Dados & ETL]
tags: [PostgreSQL, ETL, SQL, Database Optimization, .NET, Node.js]
readingTime: 8 min de leitura
---

## A Realidade dos Sistemas Bancários

Em ambientes financeiros corporativos como o Banco do Nordeste, o maior desafio raramente é a sintaxe de uma linguagem, mas sim **a integridade transacional, a escalabilidade sob concorrência intensa e a latência de relatórios operacionais**.

Quando milhares de transações e contratos são gerados simultaneamente, consultas analíticas concorrentes sobre tabelas com dezenas de milhões de registros podem gerar *table locks*, esgotamento de *connection pool* e degradação severa no tempo de resposta das aplicações voltadas ao usuário final.

---

## 1. O Princípio da Separação OLTP vs. OLAP via ETL

A primeira grande melhoria em ambientes críticos é evitar executar relatórios analíticos pesados diretamente na base transacional (OLTP).

Desenvolvemos fluxos de **ETL (Extract, Transform, Load)** que:

1. **Extraem** dados incrementais com base em marcas temporais (*watermarks*) e triggers de alteração.
2. **Transformam** e agregam os dados estruturados no formato desnormalizado ideal para leitura.
3. **Carregam** em tabelas de destino otimizadas para leitura rápida, reduzindo a contenção de locks na base transacional primária.

---

## 2. Estratégias de Índices que Transformaram a Performance

### Índices Parciais (Partial Indexes)

Em vez de criar um índice em uma tabela inteira de 15 milhões de linhas onde apenas 2% dos registros têm status `PENDENTE_ANALISE`, usamos índices parciais:

```sql
-- Reduz tamanho do índice de 800MB para 12MB e acelera varreduras em 98%
CREATE INDEX idx_contratos_pendentes
ON contratos (data_criacao, id_cliente)
WHERE status = 'PENDENTE_ANALISE';
```

### Particionamento Declarativo por Intervalo (Range Partitioning)

Para tabelas com histórico contínuo de auditoria e lançamentos contábeis, implementamos particionamento por mês/ano:

```sql
CREATE TABLE lancamentos_financeiros (
    id UUID NOT NULL,
    data_lancamento DATE NOT NULL,
    valor NUMERIC(15, 2) NOT NULL,
    descricao TEXT
) PARTITION BY RANGE (data_lancamento);
```

Com a poda de partições (*partition pruning*), consultas restritas ao mês corrente examinam apenas a partição relevante, ignorando anos de dados históricos e liberando memória no *shared_buffers*.

---

## 3. Gestão Consciente de Conexões (Connection Pooling)

A criação de conexões TCP em bancos relacionais possui custo considerável de CPU e memória. Ajustamos ferramentas como **PgBouncer** em modo de *transaction pooling* acoplado a microsserviços Node.js e .NET, garantindo que picos repentinos de requisições não derrubem o servidor de banco de dados por saturação de processos.
