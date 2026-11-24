# Samuel — Diário de setup (Kickstart Next.js e Veda)

Registro de tudo o que foi feito para preparar o ambiente de estudos da Contentstack: o **Kickstart Next.js** (Developer Kickstart) e o **Kickstart Veda** (projeto da CMS Developer Certification), com os problemas encontrados e as soluções.

> Última atualização: 23/11/2026
> Status: ✅ Kickstart Next.js funcionando · ✅ Kickstart Veda funcionando

---

## Índice

- [Ambiente](#ambiente)
- [Restrições da conta (leia primeiro)](#restrições-da-conta-leia-primeiro)
- [Parte 1 — Kickstart Next.js](#parte-1--kickstart-nextjs)
- [Parte 2 — Kickstart Veda](#parte-2--kickstart-veda)
- [Problemas e soluções (resumo)](#problemas-e-soluções-resumo)
- [Comandos de referência](#comandos-de-referência)
- [Lições aprendidas](#lições-aprendidas)
- [Links](#links)

---

## Ambiente

| Item | Valor |
| :--- | :--- |
| Sistema | Linux |
| Node.js | 20+ (o Veda exige 20 ou mais; o Kickstart Next.js aceita 18+) |
| CLI | `@contentstack/cli` (comando `csdx`) |
| Região da conta | **GCP-NA** (painel em `gcp-na-app.contentstack.com`) |
| Stack | Training instance da Academy (único stack da conta) |
| Branch | `main` |
| Environments | `development`, `production` |

```text
practice project/
├── kickstart-next/     # Developer Kickstart (Next.js mínimo)
└── kickstart-veda/     # CMS Developer Certification (loja de joias)
```

> ⚠️ Os dois projetos usam a porta **3000**. Rode um de cada vez (Ctrl+C no outro).

---

## Restrições da conta (leia primeiro)

Estas restrições explicam a maioria dos problemas abaixo.

1. **Só é possível ter 1 stack.** Tentativas de criar outro stack falharam, tanto pela CLI ("You have reached the stack creation limit for the minute", que persistiu) quanto pelo Marketplace ("Something went wrong. Please try again").
2. **O único stack é um _training instance_ da Academy.** Ele já vem com conteúdo de treino, **incluindo o Veda completo** (content types `page`, `header`, `category`, `product`, `product_line`, taxonomias e 44 entries) e um site de treino hospedado na Vercel.
3. **Consequência:** todo projeto de estudo precisa ser instalado **dentro desse mesmo stack**, convivendo com o conteúdo que já existe. Conflitos de nomes (UID de content type) e de URLs (ex.: duas entries com URL `/`) são o principal risco.

---

## Parte 1 — Kickstart Next.js

Projeto do learning path **Developer Kickstart**: um app Next.js mínimo conectado à Contentstack, com Live Preview e Visual Builder.

### Passo a passo executado

**1. Conta**

- Cadastro pela página de conta gratuita. Na primeira tentativa apareceu "Sorry, our trial is temporarily unavailable" (falha temporária do cadastro).
- O stack disponível passou a ser o training instance.

**2. Projeto e dependências**

```bash
git clone https://github.com/contentstack/kickstart-next.git
cd kickstart-next
npm install
```

**3. CLI**

```bash
npm install -g @contentstack/cli
csdx config:set:region GCP-NA
csdx auth:login
```

**4. Seed do conteúdo de exemplo**

O comando da documentação cria um stack novo, o que não é possível nesta conta:

```bash
# ❌ Falha: tenta criar um stack novo
csdx cm:stacks:seed --repo "contentstack/kickstart-stack-seed" --org "<ORG_UID>" -n "CS Kickstart Next"
```

Solução: semear o stack existente usando **só** a API Key (sem `--org` e sem `-n`):

```bash
# ✅ Funciona: usa o stack existente
csdx cm:stacks:seed --repo "contentstack/kickstart-stack-seed" --stack-api-key "<API_KEY_DO_STACK>"
```

Resultado: 7 de 8 itens importados; o content type `page` falhou (ver problema P5).

**5. Tokens e `.env`**

- *Settings > Tokens*: Delivery Token com **Create Preview Token** ligado.
- `.env` (renomeado de `.env.example`):

```bash
NEXT_PUBLIC_CONTENTSTACK_API_KEY=<API_KEY>
NEXT_PUBLIC_CONTENTSTACK_DELIVERY_TOKEN=<DELIVERY_TOKEN>
NEXT_PUBLIC_CONTENTSTACK_PREVIEW_TOKEN=<PREVIEW_TOKEN>
NEXT_PUBLIC_CONTENTSTACK_REGION=<REGIAO>
NEXT_PUBLIC_CONTENTSTACK_ENVIRONMENT=<ENVIRONMENT>
NEXT_PUBLIC_CONTENTSTACK_PREVIEW=true
```

**6. Live Preview**

- *Settings > Live Preview*: ligado, com o mesmo environment do `.env`.
- *Settings > Environments*: URL base do environment = `http://localhost:3000` (ver problema P6).

**7. Rodar**

```bash
npm run dev   # http://localhost:3000
```

✅ Resultado: página "Instalado! with Next" carregando do CMS, botão **Start Editing** (Visual Builder) visível e Live Preview atualizando sem publicar.

### Problemas enfrentados no Kickstart

| # | Sintoma | Causa | Solução |
| :- | :--- | :--- | :--- |
| P1 | "Sorry, our trial is temporarily unavailable" no cadastro | Falha temporária do sistema de cadastro | Tentar de novo mais tarde |
| P2 | Dúvida sobre o `--org` do seed | — | O "Org ID" é o **Organization UID** (*Org Admin > Info*) |
| P3 | `Error: You have reached the stack creation limit for the minute`, mesmo depois de esperar | A conta só permite 1 stack | Usar o stack existente com `--stack-api-key` |
| P4 | `--org ... cannot also be provided when using --stack-api-key` | As flags `--org`/`-n` (criar stack) e `--stack-api-key` (usar stack existente) são exclusivas | Usar só `--stack-api-key` |
| P5 | Seed com `CONTENT TYPES 0/1`, falha em `content type: page` | O training instance já tinha um content type `page` (o do Veda) | O Kickstart funcionou com o `page` existente; os avisos `COMPOSABLE_STUDIO_NOT_FOUND` e de Personalize são inofensivos |
| P6 | Live Preview mostrava o site de treino da Academy na Vercel (menu Home / About Us / Blog, logo "logoipsum") e o conteúdo da página ficava cinza | A URL base do environment apontava para o site de treino da Academy | *Settings > Environments*: trocar a URL base para `http://localhost:3000` |
| P7 | Ao clicar num campo, o preview trocava de "Instalado!" para "Home with Next" e ficava vazio | **Duas entries de `page` com URL `/`**. O Kickstart busca `entries[0]` pela URL. A Delivery API (modo normal) só vê entries publicadas; a Preview API vê também rascunhos e devolvia primeiro a "Home" da Academy | Mudar a URL da entry "Home" para `/home`. **URL deve ser única por content type** (marcar o campo como *Unique*) |
| P8 | Preview em branco (possível) | Brave Shields bloqueando o iframe | Desligar os Shields para `app.contentstack.com` e `localhost` |

---

## Parte 2 — Kickstart Veda

Projeto usado como cenário recorrente na **CMS Developer Certification**: catálogo e site institucional de uma marca de joias ("Veda: The Revival Collection"), em Next.js App Router + React + TypeScript + Tailwind. É uma camada de conteúdo e experiência: **não tem** carrinho, checkout, pagamentos nem contas de cliente.

Content types usados pelo app: `page`, `header`, `category`, `product`, `product_line`.

### Passo a passo executado

**1. Tentativa de criar stack novo (falhou)**

- Import pelo Marketplace (Kickstart Veda): "Something went wrong. Please try again."
- Conclusão: limite de **1 stack** por conta (ver [Restrições](#restrições-da-conta-leia-primeiro)).

**2. Backup do stack (recomendado antes de mexer)**

```bash
mkdir -p ~/contentstack-backup
csdx cm:stacks:export --stack-api-key "<API_KEY_DO_STACK>" --data-dir ~/contentstack-backup/training
```

**3. Seed do Veda no stack existente**

```bash
csdx cm:stacks:seed --repo "contentstack/kickstart-veda-seed" --stack-api-key "<API_KEY_DO_STACK>"
```

O resumo mostrou **75,4% de sucesso** (159 de 211), com "falhas" em global fields, content types e entries. A análise dos logs (`logs/<data>/cm-stacks-seed-*/info.log` e `error.log`) mostrou que:

| Módulo | O que o log diz | Interpretação |
| :--- | :--- | :--- |
| Environments | `development` e `production` already exist | Já existiam |
| Taxonomias | `materials`, `product_line`, `product_type` already exist | Já existiam |
| Global fields | `hero` e `list` criados; `rich_text`, `media`, `card` com erro 422 e depois "Updated" | Já existiam e foram atualizados |
| Content types | `page`, `header`, `product`, `product_line`, `category` already exist | Já existiam |
| Entries | "Veda: The Revival Collection", "Veda, our story", 32 produtos, 4 linhas, 5 categorias already exist | Já existiam |
| Publicação | 44/44 concluída | OK |

**Conclusão:** o seed conta "já existe" como falha. O training instance **já vinha com o Veda instalado**. Isso também explica o P5 e o P7 da Parte 1.

**4. Conferir o conteúdo no painel**

- Os produtos não apareciam de início na lista de Entries por causa de **filtros** da tela (content type, branch, idioma).
- Encontrados em *Entries* com filtro **Product**: todos publicados em **development** e **production**.

**5. Clonar e configurar o app**

```bash
git clone https://github.com/contentstack/kickstart-veda.git
cd kickstart-veda
npm install
cp .env.example .env.local
```

`.env.local` (mesmo stack do Kickstart, então mesma API key e região):

```bash
NEXT_PUBLIC_CONTENTSTACK_API_KEY=<API_KEY>
NEXT_PUBLIC_CONTENTSTACK_DELIVERY_TOKEN=<DELIVERY_TOKEN com development no escopo>
NEXT_PUBLIC_CONTENTSTACK_PREVIEW_TOKEN=<PREVIEW_TOKEN>
NEXT_PUBLIC_CONTENTSTACK_ENVIRONMENT=development
NEXT_PUBLIC_CONTENTSTACK_REGION=<REGIAO>
NEXT_PUBLIC_CONTENTSTACK_PREVIEW=true
```

**6. Ajustes no painel**

- *Settings > Tokens*: Delivery Token com `development` no escopo.
- *Settings > Live Preview*: environment `development`.
- *Settings > Environments > development*: URL base `http://localhost:3000`.
- *Entries > Page*: garantir **uma única** entry com URL `/` (a do Veda). A entry do Kickstart deve ir para outra URL, ex.: `/kickstart`.

**7. Rodar**

```bash
node -v       # 20 ou mais
npm run dev   # http://localhost:3000
```

✅ Resultado: Veda rodando localmente com o conteúdo do stack.

### Problemas enfrentados no Veda

| # | Sintoma | Causa | Solução |
| :- | :--- | :--- | :--- |
| V1 | Import pelo Marketplace: "Something went wrong" | Limite de 1 stack | Instalar no stack existente |
| V2 | Seed com 75% e "falhas" em global fields, content types e entries | O conteúdo do Veda **já existia** no training instance | Nenhuma: conferir o `info.log` para distinguir "already exists" de erro real |
| V3 | Erro 422 "Global Field creation failed" (`rich_text`, `media`, `card`) | Global fields já existentes | Nenhuma: foram atualizados em seguida |
| V4 | Produtos "sumidos" no painel | Filtros da lista de Entries | Filtrar por **Product**, branch `main`, idioma en-us |
| V5 | Conflito de URL `/` (Kickstart × Veda) | Mesmo content type `page`, mesma URL | URL única por entry; mover a do Kickstart para `/kickstart` |

---

## Problemas e soluções (resumo)

| Sintoma | Primeira coisa a verificar |
| :--- | :--- |
| Lista vazia / página em branco | A entry está **publicada** no environment do `.env`? O token tem esse environment no escopo? |
| Erro de autenticação (401/412) | API Key e tokens são do mesmo stack? |
| Nada funciona com tokens certos | **Região** errada |
| Live Preview mostra outro site | URL base do environment em *Settings > Environments* |
| Preview mostra conteúdo diferente do site publicado | Rascunho × publicado: duas entries com a mesma URL? |
| Seed/import com "falhas" | Ler o `info.log`: "already exists" não é erro |
| Não dá para criar stack | Limite da conta: usar `--stack-api-key` no stack existente |
| `npm run dev` não sobe | Outro projeto na porta 3000? Node 20+? |

---

## Comandos de referência

```bash
# CLI
csdx config:get:region
csdx config:set:region GCP-NA
csdx auth:login

# Seed num stack existente
csdx cm:stacks:seed --repo "<org/repo-seed>" --stack-api-key "<API_KEY_DO_STACK>"

# Backup do stack
csdx cm:stacks:export --stack-api-key "<API_KEY_DO_STACK>" --data-dir ~/contentstack-backup/<nome>

# Ver erros de um seed/import
grep -i "error" logs/<data>/cm-stacks-seed-*/error.log | head -40
grep -i "already exist" logs/<data>/cm-stacks-seed-*/info.log | head -40

# Rodar um projeto
npm run dev
```

---

## Lições aprendidas

1. **Publicar ≠ salvar.** Só o que é publicado num environment aparece na Delivery API.
2. **Delivery API × Preview API.** A Delivery só enxerga conteúdo publicado; a Preview enxerga rascunhos. Diferença entre "site normal" e "preview" quase sempre vem daí.
3. **URL única por content type.** Os kickstarts buscam a página pela URL e usam o primeiro resultado.
4. **Region não é environment.** A região define os endpoints; o environment define o estágio de publicação.
5. **UID de content type é único no stack.** Num stack compartilhado, projetos diferentes não podem usar o mesmo UID (`page`, `header`).
6. **Logs da CLI enganam pelo resumo.** O resumo conta "já existe" como falha; o `info.log` mostra a verdade.
7. **Management Token só no servidor.** Frontend usa apenas Delivery e Preview tokens.

---

## Links

- [Developer Kickstart (learning path)](https://www.contentstack.com/academy/learning-paths/developer-kickstart)
- [CMS Developer Certification (learning path)](https://www.contentstack.com/academy/learning-paths/cms-developer-certification)
- [Docs — Kickstart Next.js](https://www.contentstack.com/docs/developers/kickstarts/next)
- [Docs — Kickstart Veda](https://www.contentstack.com/docs/headless-cms/veda)
- [Repositório kickstart-next](https://github.com/contentstack/kickstart-next)
- [Repositório kickstart-veda](https://github.com/contentstack/kickstart-veda)
