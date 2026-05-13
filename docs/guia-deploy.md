# Guia de Implantação em Produção — PGMBox Suporte
### GitHub Pages · HTTPS gratuito · Deploy em 10 minutos

---

## Pré-requisitos

| Item | Link |
|------|------|
| Conta no GitHub (gratuita) | [github.com/signup](https://github.com/signup) |
| Git instalado no computador | [git-scm.com/downloads](https://git-scm.com/downloads) |
| Arquivo `PGMBox_Suporte.html` | Fornecido pela consultoria |

---

## PARTE 1 — Configurar o Git pela primeira vez

> Se você já usa Git, pule para a Parte 2.

### 1.1 Instalar o Git

1. Acesse [git-scm.com/downloads](https://git-scm.com/downloads)
2. Baixe a versão para **Windows**
3. Execute o instalador com as opções padrão
4. Ao final, abra o **Git Bash** (aparece no menu Iniciar)

### 1.2 Configurar seu nome e e-mail

Abra o **Git Bash** e execute:

```bash
git config --global user.name "Seu Nome"
git config --global user.email "seu@email.com"
```

---

## PARTE 2 — Criar o repositório no GitHub

### 2.1 Criar conta no GitHub

1. Acesse [github.com/signup](https://github.com/signup)
2. Preencha: nome de usuário, e-mail e senha
3. Confirme o e-mail recebido na caixa de entrada

### 2.2 Criar o repositório

1. Faça login no GitHub
2. Clique no botão **"+"** no canto superior direito → **"New repository"**

   ![New repository](https://docs.github.com/assets/cb-31554/images/help/repository/repo-create.png)

3. Preencha os campos:

   | Campo | Valor |
   |-------|-------|
   | **Repository name** | `pgmbox-suporte` |
   | **Description** | `Sistema de Gestão de Chamados PGMBox` |
   | **Visibility** | `Public` *(obrigatório para GitHub Pages gratuito)* |
   | **Add a README** | ❌ Não marcar |

4. Clique em **"Create repository"**

5. Anote a URL do repositório — será algo como:
   ```
   https://github.com/SEU_USUARIO/pgmbox-suporte.git
   ```

---

## PARTE 3 — Preparar os arquivos localmente

### 3.1 Organizar a estrutura de pastas

Crie uma pasta no seu computador chamada `pgmbox-suporte` e coloque os arquivos assim:

```
pgmbox-suporte/
├── index.html              ← PGMBox_Suporte.html renomeado para index.html
├── README.md
└── docs/
    ├── manual-usuario.md
    ├── manual-email.md
    └── guia-integracao.md
```

> ⚠️ **Importante:** renomeie `PGMBox_Suporte.html` para `index.html`.
> O GitHub Pages serve automaticamente o arquivo `index.html` como página principal.

### 3.2 Abrir o terminal na pasta

**Windows:**
1. Abra a pasta `pgmbox-suporte` no Explorador de Arquivos
2. Clique na barra de endereço, digite `cmd` e pressione Enter
   - Ou: botão direito na pasta → **"Abrir no Terminal"**

**macOS/Linux:**
```bash
cd ~/Desktop/pgmbox-suporte
```

---

## PARTE 4 — Enviar os arquivos para o GitHub

Execute os comandos abaixo **um por vez** no terminal:

### 4.1 Inicializar o repositório Git local

```bash
git init
```

> Saída esperada: `Initialized empty Git repository in .../pgmbox-suporte/.git/`

### 4.2 Conectar ao repositório remoto

```bash
git remote add origin https://github.com/SEU_USUARIO/pgmbox-suporte.git
```

> Substitua `SEU_USUARIO` pelo seu nome de usuário do GitHub.

### 4.3 Adicionar todos os arquivos

```bash
git add .
```

> O ponto `.` significa "adicionar tudo". Nenhuma saída é esperada.

### 4.4 Criar o primeiro commit

```bash
git commit -m "Implantação inicial do PGMBox Suporte"
```

> Saída esperada:
> ```
> [main (root-commit) abc1234] Implantação inicial do PGMBox Suporte
>  4 files changed, ...
> ```

### 4.5 Enviar para o GitHub

```bash
git branch -M main
git push -u origin main
```

> O GitHub pedirá seu **usuário** e **senha (token)**. Veja a Seção 4.6 se for a primeira vez.

### 4.6 Autenticação no GitHub (primeira vez)

O GitHub não aceita mais senha direta — usa **Personal Access Token (PAT)**:

1. No GitHub: clique na sua foto → **Settings**
2. No menu esquerdo, role até **Developer settings** → **Personal access tokens** → **Tokens (classic)**
3. Clique em **"Generate new token (classic)"**
4. Preencha:
   - **Note:** `PGMBox Deploy`
   - **Expiration:** `No expiration`
   - **Scopes:** marque `repo` (acesso completo a repositórios)
5. Clique em **"Generate token"**
6. **Copie o token** (começa com `ghp_...`) — ele aparece só uma vez
7. Quando o terminal pedir a senha, cole o token

---

## PARTE 5 — Ativar o GitHub Pages

1. No GitHub, acesse seu repositório `pgmbox-suporte`
2. Clique na aba **"Settings"** (engrenagem)
3. No menu esquerdo, clique em **"Pages"**
4. Em **"Source"**, selecione:
   - Branch: **`main`**
   - Folder: **`/ (root)`**
5. Clique em **"Save"**

Aguarde **1 a 3 minutos**. O GitHub mostrará:

```
✅ Your site is live at https://SEU_USUARIO.github.io/pgmbox-suporte
```

---

## PARTE 6 — Acessar o sistema em produção

Abra o navegador e acesse:

```
https://SEU_USUARIO.github.io/pgmbox-suporte
```

**Primeiro acesso:**
- E-mail: `everton@pgmbox.com.br`
- Senha: `Admin@2025`
- O sistema pedirá a criação de uma nova senha imediatamente

---

## PARTE 7 — Atualizar o sistema (quando houver nova versão)

Sempre que receber uma versão atualizada do `index.html`:

```bash
# 1. Substitua o arquivo index.html na pasta
# 2. Abra o terminal na pasta e execute:

git add index.html
git commit -m "Atualização do sistema - v2.0"
git push
```

O GitHub Pages publica a atualização automaticamente em **1 a 2 minutos**.

---

## PARTE 8 — Domínio personalizado (opcional)

Para usar um domínio próprio como `suporte.suaempresa.com.br`:

### 8.1 No GitHub Pages

1. Em **Settings → Pages → Custom domain**
2. Digite: `suporte.suaempresa.com.br`
3. Clique em **Save**

### 8.2 No seu provedor de DNS

Adicione um registro CNAME:

| Tipo | Nome | Valor |
|------|------|-------|
| `CNAME` | `suporte` | `SEU_USUARIO.github.io` |

> A propagação de DNS leva de 15 minutos a 24 horas.

### 8.3 Forçar HTTPS

Em **Settings → Pages**, marque **"Enforce HTTPS"** após o domínio ser reconhecido.

---

## Resolução de Problemas Comuns

### ❌ `git push` pede usuário/senha repetidamente

```bash
# Salvar credenciais permanentemente
git config --global credential.helper store
# Execute o push novamente e informe usuário + token uma última vez
git push
```

### ❌ Página não carrega após ativar o Pages

- Verifique se o arquivo se chama exatamente `index.html` (minúsculo)
- Aguarde até 5 minutos para o primeiro deploy
- Verifique em **Settings → Pages** se aparece o link verde

### ❌ Erro `remote: Repository not found`

```bash
# Verifique a URL do remote
git remote -v
# Corrija se necessário
git remote set-url origin https://github.com/SEU_USUARIO/pgmbox-suporte.git
```

### ❌ Erro `failed to push some refs`

```bash
git pull origin main --allow-unrelated-histories
git push
```

---

## Resumo dos Comandos

```bash
# Primeira vez (configuração)
git init
git remote add origin https://github.com/SEU_USUARIO/pgmbox-suporte.git
git add .
git commit -m "Implantação inicial"
git branch -M main
git push -u origin main

# Atualizações futuras
git add .
git commit -m "Descrição da atualização"
git push
```

---

## Checklist Final de Implantação

- [ ] Conta GitHub criada
- [ ] Git instalado e configurado
- [ ] Repositório `pgmbox-suporte` criado como **Public**
- [ ] Arquivo renomeado para `index.html`
- [ ] Arquivos enviados com `git push`
- [ ] GitHub Pages ativado em Settings → Pages
- [ ] Sistema acessível via `https://SEU_USUARIO.github.io/pgmbox-suporte`
- [ ] Senha do admin alterada no primeiro acesso
- [ ] EmailJS configurado (opcional — ver `manual-email.md`)
- [ ] Analistas cadastrados pelo admin

---

*PGMBox Suporte — Guia de Implantação · Consultoria PGMBOX*
