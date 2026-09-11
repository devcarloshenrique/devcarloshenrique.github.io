---
title: letter-music
description: Plataforma interativa de aprendizado de idiomas com músicas, letras sincronizadas e modo karaokê.
category: Full Stack
tags: [Node.js, Express, TypeScript, React, Vite, Tailwind CSS, Vertical Slice (VSA), Playwright, Docker Compose]
github: https://github.com/devcarloshenrique/letter-music
architecture: Backend em Vertical Slice Architecture, frontend com Feature-Driven Architecture e web scraping headless com Playwright.
image: /projects/letter-music-home.png
gallery: [/projects/letter-music-home.png, /projects/letter-music-lyrics.png, /projects/letter-music-karaoke.png]
featured: true
weight: 3
---

Aplicação full-stack que une música e educação para aprendizado imersivo de idiomas. Construída com um backend robusto em Node.js utilizando Vertical Slice Architecture (VSA) e web scraping resiliente com Playwright/Cheerio para sincronização de letras em tempo real, além de um frontend moderno no tema 'Neon Dark'.

## Decisões de Arquitetura

Separação arquitetural estrita: Backend em Vertical Slice Architecture eliminando acoplamento entre módulos de negócio; Frontend com Feature-Driven Architecture; Web Scraping headless com Playwright para sincronização de timecodes.

## Destaques de Implementação

- **Backend em Vertical Slice Architecture (VSA):** cada funcionalidade isola seu Controller, UseCase, DTO, testes e Swagger.
- **Extração e raspagem de letras sincronizadas** de fontes externas em tempo real com Playwright e Cheerio.
- **Design System 'Neon Dark' premium** com modo karaokê sincronizado e interface rica para acompanhamento vocal.
- **Frontend moderno em React + TypeScript** com Feature-Driven Architecture para baixo acoplamento e alta manutenibilidade.
- **Infraestrutura conteinerizada** pronta para execução com Docker Compose e suporte embutido a túneis ngrok.
