# Carlos Henrique | Software Engineer — Portfolio & Tech Blog

Portfólio de Engenharia de Software + blog técnico gerado com **Hugo** (binário Go, sem Node.js e sem `node_modules`), pronto para hospedar no **GitHub Pages**.

## Rodar localmente

Pré-requisito: Hugo extended (v0.166.0+)

```bash
hugo server -D   # preview com live reload em http://localhost:1313
hugo --minify --cleanDestinationDir   # build estático em public/
```

## Estrutura

- `hugo.toml` — configuração (baseURL, menus, taxonomias, params do autor)
- `content/` — Sobre (`_index.md`), `projects/` (3 case studies), `blog/` (5 posts), `contato.md`
- `data/` — experiências, formação e skills em YAML
- `layouts/` — tema próprio replicando o visual atual (IBM Plex + violeta)
- `assets/css/` — CSS puro (sem Tailwind, sem build JS)
- `static/` — `perfil.png`, `projects/*`, `.nojekyll`

## Deploy no GitHub Pages (`devcarloshenrique.github.io`)

1. Crie o repositório `devcarloshenrique.github.io` e suba o código na branch `main`.
2. Em `Settings → Pages → Source`, selecione **GitHub Actions**.
3. O workflow `.github/workflows/deploy.yml` instala o Hugo extended, roda `hugo --minify` e publica `public/` automaticamente a cada push na `main`.

O formulário de contato é um `<form>` HTML puro com `action="https://formspree.io/f/meaqneva"` — sem backend, sem variáveis de ambiente.
