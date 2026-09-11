---
title: mangaink-agent
description: Aplicação self-hosted para download, conversão em lote (KCC) e envio automático de mangás para o Kindle.
category: Full Stack
tags: [Fastify 5, TypeScript, React 19, TanStack Router, TanStack Query, Docker (KCC), Prisma 7, PostgreSQL, Redis, Zod]
github: https://github.com/devcarloshenrique/mangaink-agent
architecture: Arquitetura em monorepo com isolamento de responsabilidades — API Fastify 5, Prisma ORM no PostgreSQL, workers Docker KCC e frontend React 19.
image: /projects/mangaink-cover.jpg
gallery: [/projects/mangaink-cover.jpg, /projects/mangaink-icon.png]
featured: true
weight: 2
---

Solução completa que orquestra download de capítulos em lote, conversão e otimização de imagens através de um container Docker dedicado com Kindle Comic Converter (KCC 10.3.0 + KindleGen), gerando formatos EPUB, MOBI e CBZ. Possui interface moderna em Português com design temático pop-art, TanStack Router e fila de agendamentos.

## Decisões de Arquitetura

Arquitetura em monorepo com isolamento de responsabilidades: API de alta performance em Fastify 5, camada de persistência com Prisma ORM no PostgreSQL, workers Docker isolados para conversão gráfica KCC e frontend moderno com React 19.

## Destaques de Implementação

- **Monorepo orquestrado com pnpm workspaces** (Frontend React 19 + Backend Fastify 5 + Desktop Tauri).
- **Pipeline de conversão dedicado** via container Docker KCC (Kindle Comic Converter 10.3.0 + KindleGen) com bind mounts.
- **Interface em Português Brasileiro** com design temático de quadrinhos pop-art, TanStack Router (file-based routing) e Tailwind v4.
- **Processamento assíncrono em background** e persistência relacional com Prisma 7 + PostgreSQL.
- **Documentação interativa OpenAPI / Swagger** e suíte abrangente de testes automatizados com Vitest.
