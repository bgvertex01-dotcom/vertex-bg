# Cartilha — Publicar um site estático no GitHub + Vercel

Guia de referência com todos os passos e comandos usados para colocar o
**PH Pneus** no ar (GitHub → Vercel), para repetir com outros projetos.
Testado no Windows, com Git Bash / PowerShell.

---

## 0. Pré-requisitos (checar uma vez só)

```bash
git --version      # Git para Windows
node --version     # Node.js (necessário para rodar o Vercel CLI via npx)
npx --version
```

Se algo faltar, instale via `winget`:

```powershell
winget install --id Git.Git -e
winget install --id OpenJS.NodeJS.LTS -e
```

---

## 1. Organizar os arquivos do site localmente

Coloque os arquivos do projeto numa pasta própria, por exemplo:
`C:\NomeDoProjeto\nome-repo\`

Estrutura mínima recomendada:

```
nome-repo/
├── index.html        ← obrigatório na raiz (Vercel serve isso em "/")
├── README.md
├── package.json       (opcional, mas ajuda a identificar o projeto)
└── .gitignore
```

> **Importante:** o arquivo principal do site **precisa se chamar
> `index.html`** e estar na raiz do repositório. Se o arquivo original
> tiver outro nome (ex: `ph-pneus.html`), faça uma cópia:
> ```bash
> cp ph-pneus.html index.html
> ```

`.gitignore` sugerido:
```
.DS_Store
*.swp
*.swo
*~
.env
.env.local
node_modules/
dist/
build/
*.log
```

---

## 2. Inicializar o Git e commitar

Dentro da pasta do projeto:

```bash
git init
git add .
git commit -m "Initial commit: <nome do site>"
git branch -M main
```

---

## 3. Criar o repositório no GitHub

1. Acesse **https://github.com/new**
2. **Repository name:** nome do projeto (ex: `ph-pneus`)
3. **Visibility:** Public (ou Private, se preferir)
4. **NÃO** marque nenhuma opção de inicialização (sem README, sem .gitignore, sem license) — o repositório precisa ficar vazio
5. Clique em **Create repository**

---

## 4. Conectar o repositório local ao GitHub e enviar (push)

```bash
git remote add origin https://github.com/<usuario>/<repo>.git
git push -u origin main
```

O Windows usa o **Git Credential Manager (GCM)** por padrão
(`credential.helper=manager`), então o push normalmente abre uma
**janela/aba do navegador** para login no GitHub — não é preciso gerar
token manual na maioria dos casos.

### ⚠️ Problema comum: `403 Permission denied` para outro usuário

Se a máquina já tiver uma sessão do GitHub salva de **outra conta**
(ex: você usa duas contas GitHub no mesmo PC), o push falha com:

```
remote: Permission to <usuario>/<repo>.git denied to <outra-conta>.
fatal: ... 403
```

**Solução:** apagar a credencial salva para `github.com` e tentar de novo
(isso força o GCM a pedir login de novo):

```bash
printf "protocol=https\nhost=github.com\n" | git credential reject
git push -u origin main
```

(Alternativa via Windows: `Painel de Controle → Gerenciador de
Credenciais → Credenciais do Windows`, procurar por entradas
`git:https://github.com` ou `GitHub - https://api.github.com/<usuario>`
e remover manualmente.)

---

## 5. Deploy na Vercel via CLI

Não precisa instalar nada globalmente — usa `npx`:

```bash
npx vercel login
```

Isso mostra um link do tipo:
```
Visit https://vercel.com/oauth/device?user_code=XXXX-XXXX
```
Abra no navegador e confirme o login (GitHub, GitLab, Bitbucket, e-mail ou SSO).

Depois de logado, dentro da pasta do projeto:

```bash
npx vercel --prod --yes
```

Isso cria o projeto na Vercel, sobe os arquivos e publica em produção.
No final aparece a URL pública, algo como:
```
https://<nome-do-projeto>.vercel.app
```

> Sem framework detectado (HTML puro) a Vercel serve os arquivos como
> estão, sem build — não precisa de configuração extra.

---

## 6. Conectar o GitHub ao projeto da Vercel (deploy automático a cada push)

O comando `vercel --prod` **às vezes falha** ao conectar o Git automaticamente:
```
Error: Failed to connect <usuario>/<repo> to project.
```

Nesse caso, conecte manualmente pelo painel:

1. Acesse `https://vercel.com/<time-ou-conta>/<nome-do-projeto>/settings/git`
   (o nome do time/conta e do projeto aparecem no output do `vercel --prod`)
2. Clique em **Connect Git Repository**
3. Escolha **GitHub**
4. Se pedir, instale/autorize o **Vercel GitHub App** para a conta ou
   repositório específico
5. Selecione o repositório na lista e confirme

A partir daí, todo `git push` na branch `main` dispara um novo deploy
automático — não precisa mais rodar `vercel --prod` manualmente.

---

## Resumo rápido (copiar e colar, ajustando os nomes)

```bash
# 1. dentro da pasta do projeto
cp meu-site.html index.html
git init
git add .
git commit -m "Initial commit"
git branch -M main

# 2. depois de criar o repo vazio em github.com/new
git remote add origin https://github.com/<usuario>/<repo>.git
git push -u origin main
# se der 403 de outra conta:
#   printf "protocol=https\nhost=github.com\n" | git credential reject
#   git push -u origin main

# 3. deploy
npx vercel login
npx vercel --prod --yes
# se a conexão com o Git falhar, conectar manualmente em:
# https://vercel.com/<time>/<projeto>/settings/git
```

---

## Referência deste projeto (PH Pneus)

| Item | Valor |
|---|---|
| Repositório GitHub | https://github.com/bgvertex01-dotcom/ph-pneus |
| Conta Vercel/time | `bg-vertex` |
| Projeto Vercel | `ph-pneus-repo` |
| URL de produção | https://ph-pneus-repo.vercel.app |
| Pasta local | `C:\SitePhPneus\ph-pneus-repo` |
