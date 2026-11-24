# Como contribuir

## 1. Clonar o repositório

```bash
git clone https://github.com/<DONO>/contentstack-aprendizados.git
cd contentstack-aprendizados
```

## 2. Criar sua branch

```bash
git checkout -b docs/<seu-nome>
```

## 3. Criar sua pasta a partir do modelo

```bash
cp -r membros/_modelo membros/<seu-nome>
```

Use o nome em minúsculas e sem acento (ex.: `joao-silva`).

## 4. Preencher sua documentação

Edite `membros/<seu-nome>/README.md`. O mais importante é a seção **Problemas enfrentados**: sintoma, causa e solução. Prints vão em `membros/<seu-nome>/img/`.

## 5. Atualizar o README principal

No `README.md` da raiz:

- Adicione uma linha na tabela **Integrantes e documentações**.
- Adicione seus problemas na tabela **Problemas enfrentados**. Se o problema já existir, só acrescente seu nome na coluna "Quem".

## 6. Commit e Pull Request

```bash
git add .
git commit -m "docs: adiciona documentação de <seu-nome>"
git push -u origin docs/<seu-nome>
```

Depois abra o Pull Request no GitHub (o link aparece no terminal após o push).

## Regras

| ✅ Faça | ❌ Não faça |
| :--- | :--- |
| Use placeholders: `<API_KEY>`, `<DELIVERY_TOKEN>` | Colar tokens, API Keys ou o `.env` |
| Copie a mensagem de erro exata em bloco de código | Escrever "deu erro" sem a mensagem |
| Edite só a sua pasta e as tabelas da raiz | Editar a pasta de outro integrante |
| Registre também o que **não** funcionou | Documentar só o caminho feliz |
