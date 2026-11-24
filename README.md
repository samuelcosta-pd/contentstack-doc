# Contentstack — Aprendizados do time

Repositório coletivo onde cada integrante documenta como instalou os **Kickstarts da Contentstack** (Next.js, Veda etc.), os problemas que enfrentou e como resolveu.

A ideia é simples: **quem vier depois não precisa tropeçar nas mesmas pedras.**

---

## Índice

- [Como este repositório funciona](#como-este-repositório-funciona)
- [Integrantes e documentações](#integrantes-e-documentações)
- [Problemas enfrentados na instalação do Kickstart](#problemas-enfrentados-na-instalação-do-kickstart)
- [Checklist rápido antes de pedir ajuda](#checklist-rápido-antes-de-pedir-ajuda)
- [Como contribuir](#como-contribuir)
- [Links úteis](#links-úteis)

---

## Como este repositório funciona

```text
contentstack-aprendizados/
├── README.md                  # Este arquivo: índice + tabela consolidada de problemas
├── CONTRIBUTING.md            # Regras e passo a passo para contribuir
└── membros/
    ├── _modelo/README.md      # Modelo para copiar
    ├── samuel/README.md       # Documentação do fulano
    └── <seu-nome>/README.md   # A sua documentação
```

- Cada integrante tem **uma pasta própria** em `membros/`. Assim ninguém edita o arquivo do outro e não há conflitos.
- Os problemas de cada um também entram na **tabela consolidada** abaixo, para que todos sejam encontrados num lugar só.

---

## Integrantes e documentações

| Integrante | Kickstarts instalados | Sistema | Status | Documentação |
| :--- | :--- | :--- | :--- | :--- |
| Samuel | Next.js, Veda | Linux | ✅ Funcionando | [membros/samuel](membros/samuel/README.md) |
| _seu nome_ | _ex.: Next.js_ | _ex.: Windows_ | _✅ / 🚧 / ❌_ | _membros/seu-nome_ |

---

## Problemas enfrentados na instalação do Kickstart

Tabela consolidada. **Antes de adicionar uma linha, veja se o problema já está aqui**: se estiver, só acrescente seu nome na coluna "Quem".

| # | Kickstart | Sintoma | Causa | Solução | Quem |
| :- | :--- | :--- | :--- | :--- | :--- |
| 1 | Next.js | "Sorry, our trial is temporarily unavailable" no cadastro | Falha temporária do cadastro | Tentar de novo mais tarde | Samuel |
| 2 | Next.js | `You have reached the stack creation limit for the minute` no seed | A conta de treino só permite **1 stack** | Usar o stack existente: `csdx cm:stacks:seed --repo "<repo>" --stack-api-key "<API_KEY>"` | Samuel |
| 3 | Next.js | `--org ... cannot also be provided when using --stack-api-key` | `--org`/`-n` (criar stack) e `--stack-api-key` (usar existente) são exclusivas | Usar só `--stack-api-key` | Samuel |
| 4 | Next.js | Seed falha em `content type: page` | O training instance já tinha um content type `page` | O Kickstart funciona com o `page` existente | Samuel |
| 5 | Next.js | Live Preview mostra o site de treino da Academy (Vercel) | URL base do environment aponta para o site de treino | *Settings > Environments*: URL base = `http://localhost:3000` | Samuel |
| 6 | Next.js | Ao clicar num campo, o preview troca de página e fica vazio | Duas entries `page` com a URL `/` | Deixar a URL única (ex.: mover uma para `/home`) | Samuel |
| 7 | Next.js | Preview em branco | Bloqueador do navegador (Brave Shields) barrando o iframe | Desligar o bloqueador para `app.contentstack.com` e `localhost` | Samuel |
| 8 | Veda | Seed com ~75% e "falhas" | O conteúdo **já existia**; o resumo conta "already exists" como falha | Conferir o `info.log` do seed | Samuel |
| 9 | Veda | Produtos "sumidos" no painel | Filtros da lista de Entries | Filtrar por content type, branch e idioma | Samuel |

---

## Checklist rápido antes de pedir ajuda

| Verificação | Por quê |
| :--- | :--- |
| A entry está **publicada** no environment do `.env`? | A Delivery API só vê conteúdo publicado |
| API Key e tokens são do **mesmo stack**? | Erros 401/412 |
| A **região** do `.env` é a da sua conta (NA, EU, GCP-NA...)? | Tokens certos + região errada = nada funciona |
| O Delivery Token tem o environment no escopo e o Preview Token ligado? | Sem isso, o Live Preview não funciona |
| Há **outra entry com a mesma URL**? | O Kickstart usa o primeiro resultado |
| Node 18+ (Next.js) / 20+ (Veda)? A porta 3000 está livre? | `npm run dev` não sobe |

---

## Como contribuir

Resumo (detalhes em [CONTRIBUTING.md](CONTRIBUTING.md)):

1. Crie uma branch: `git checkout -b docs/<seu-nome>`
2. Copie o modelo: `cp -r membros/_modelo membros/<seu-nome>`
3. Preencha `membros/<seu-nome>/README.md`
4. Adicione seus problemas na tabela consolidada e seu nome na tabela de integrantes
5. Abra um Pull Request

> ⚠️ **Nunca** coloque API Keys, tokens ou o conteúdo do seu `.env`. Use `<API_KEY>`, `<DELIVERY_TOKEN>` etc.

---

## Links úteis

- [Developer Kickstart (learning path)](https://www.contentstack.com/academy/learning-paths/developer-kickstart)
- [Docs — Kickstart Next.js](https://www.contentstack.com/docs/developers/kickstarts/next)
- [Docs — Kickstart Veda](https://www.contentstack.com/docs/headless-cms/veda)
- [Repositório kickstart-next](https://github.com/contentstack/kickstart-next)
- [Repositório kickstart-veda](https://github.com/contentstack/kickstart-veda)
