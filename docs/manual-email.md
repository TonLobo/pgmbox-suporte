# Manual de Configuração de E-mail — PGMBox Suporte

O sistema usa o serviço **EmailJS** para enviar e-mails sem necessidade de servidor. Este manual cobre a configuração completa do zero.

---

## Sumário

1. [O que o e-mail faz no sistema](#1-o-que-o-e-mail-faz-no-sistema)
2. [Criar conta no EmailJS](#2-criar-conta-no-emailjs)
3. [Conectar seu servidor de e-mail](#3-conectar-seu-servidor-de-e-mail)
4. [Criar o template de e-mail](#4-criar-o-template-de-e-mail)
5. [Configurar no PGMBox](#5-configurar-no-pgmbox)
6. [Testar o envio](#6-testar-o-envio)
7. [Recebimento de e-mails (Email-to-Ticket)](#7-recebimento-de-e-mails-email-to-ticket)
8. [Opções alternativas ao EmailJS](#8-opções-alternativas-ao-emailjs)
9. [Solução de problemas](#9-solução-de-problemas)

---

## 1. O que o e-mail faz no sistema

O PGMBox envia e-mails automaticamente nos seguintes eventos:

| Evento | Destinatário |
|--------|-------------|
| Abertura de novo chamado | Analista responsável |
| Mudança de status | Analista responsável |
| Envio para consultoria | Consultor designado |
| SLA vencendo (em breve) | Analista responsável |
| Recuperação de senha | Usuário solicitante |

---

## 2. Criar conta no EmailJS

1. Acesse [https://emailjs.com](https://emailjs.com) e clique em **Sign Up** (gratuito)
2. O plano gratuito permite **200 e-mails/mês** — suficiente para times pequenos
3. Para volumes maiores, assine o plano Personal (US$ 15/mês) ou Professional

---

## 3. Conectar seu servidor de e-mail

No painel do EmailJS:

1. Acesse **Email Services → Add New Service**
2. Escolha seu provedor:

### Gmail / Google Workspace (recomendado para início)

1. Selecione **Gmail**
2. Clique em **Connect Account** e autorize com sua conta Google
3. Nome do serviço: `PGMBox Suporte`
4. Clique em **Create Service**
5. Copie o **Service ID** (ex: `service_abc1234`)

### Outlook / Microsoft 365

1. Selecione **Outlook**
2. Clique em **Connect Account** e autorize
3. Copie o **Service ID**

### SMTP personalizado (servidores corporativos)

1. Selecione **Custom SMTP**
2. Preencha:

| Campo | Exemplo |
|-------|---------|
| Host | `mail.suaempresa.com.br` |
| Port | `587` (TLS) ou `465` (SSL) |
| Username | `suporte@suaempresa.com.br` |
| Password | Senha da conta de e-mail |
| Secure | `TLS` para porta 587, `SSL` para 465 |

3. Clique em **Test** para verificar a conexão
4. Copie o **Service ID**

---

## 4. Criar o template de e-mail

1. No painel EmailJS, acesse **Email Templates → Create New Template**
2. Configure os campos:

**To email:** `{{to_email}}`
**To name:** `{{to_name}}`
**Subject:** `{{subject}}`
**From name:** `PGMBox Suporte`
**Reply to:** `suporte@suaempresa.com.br`

**Corpo do e-mail** (cole exatamente assim):

```
Olá {{to_name}},

{{mensagem}}

---
Chamado: {{numero}}
Título: {{titulo}}
Status: {{status}}
Prazo SLA: {{prazo}}

PGMBox Suporte
```

3. Clique em **Save**
4. Copie o **Template ID** (ex: `template_xyz9876`)

---

## 5. Configurar no PGMBox

1. Faça login no PGMBox como Administrador
2. Acesse **Configurações** no menu lateral
3. Role até a seção **Configuração de E-mail (EmailJS)**
4. Preencha os campos:

| Campo | Onde encontrar |
|-------|---------------|
| **Service ID** | EmailJS → Email Services → seu serviço |
| **Template ID** | EmailJS → Email Templates → seu template |
| **Public Key** | EmailJS → Account → General → Public Key |
| **E-mail do remetente** | O e-mail que aparecerá como remetente |
| **Nome do remetente** | Ex: `PGMBox Suporte` |

5. Clique em **Salvar Configurações**

### Onde encontrar a Public Key

1. No painel EmailJS, clique no ícone do seu perfil → **Account**
2. Vá na aba **General**
3. Copie o valor em **Public Key**

---

## 6. Testar o envio

1. Com as configurações salvas, acesse a tela de login
2. Clique em **Esqueci minha senha**
3. Informe seu próprio e-mail cadastrado
4. Clique em **Enviar senha temporária**
5. Verifique se o e-mail chegou na caixa de entrada

Se chegou: ✅ configuração concluída.

Se não chegou: veja a seção [Solução de problemas](#9-solução-de-problemas).

---

## 7. Recebimento de e-mails (Email-to-Ticket)

Para que e-mails enviados pelos clientes abram automaticamente chamados na fila do sistema, você precisa de uma etapa adicional, pois o PGMBox é um arquivo estático sem servidor.

### Opção A — Zapier (sem código, recomendado)

**Pré-requisito:** conta no Zapier (plano gratuito permite 100 tarefas/mês)

1. Acesse [zapier.com](https://zapier.com) e crie um novo Zap
2. **Trigger:** Gmail → "New Email Matching Search" → filtro: `to:suporte@suaempresa.com.br`
3. **Action:** Webhooks by Zapier → POST
4. URL: `https://SEU_SITE.github.io/pgmbox-suporte/inbound` *(ver nota abaixo)*
5. Payload:
```json
{
  "remetente": "{{from_email}}",
  "nome": "{{from_name}}",
  "assunto": "{{subject}}",
  "corpo": "{{body_plain}}"
}
```

> **Nota:** Como o PGMBox é um HTML estático, não existe endpoint HTTP para receber webhooks. Para usar Zapier, você precisa de uma função intermediária (ex: Netlify Function, Cloudflare Worker ou n8n) que receba o webhook e injete o dado via `window.PGMBoxAPI.receberEmail()`. Veja o `guia-integracao.md` para o código pronto.

### Opção B — Make (antigo Integromat)

Similar ao Zapier, com plano gratuito de 1.000 operações/mês. Mesma lógica de trigger no Gmail e webhook para a função intermediária.

### Opção C — EmailJS Inbound (mais simples)

O EmailJS oferece um serviço de e-mail de entrada que pode redirecionar para um webhook:

1. No painel EmailJS: **Email Services → seu serviço → Inbound Email**
2. Configure o endereço de entrada (ex: `suporte@seudominio.emailjs.com`)
3. Defina o webhook para sua função intermediária
4. Oriente os clientes a enviarem para esse endereço

---

## 8. Opções alternativas ao EmailJS

Se você preferir outro provedor:

### Resend.com (moderno, mais barato)
- 3.000 e-mails/mês gratuitos
- API simples em REST
- Requer adaptação do código JS (trocar as chamadas de `emailjs.send` por `fetch` para a API do Resend)

### SendGrid
- 100 e-mails/dia gratuitos
- Ideal para volumes maiores
- Mesma necessidade de adaptação do código

### Servidor SMTP direto via backend
Se você tiver um servidor Node.js/PHP na frente, pode usar Nodemailer (Node.js) ou PHPMailer, sem depender de serviços externos. Veja `guia-integracao.md` para o exemplo de backend.

---

## 9. Solução de problemas

### E-mail não chega

1. Verifique o **Log de E-mails** em Configurações — qual status aparece?
   - `simulado`: SMTP não foi configurado ainda
   - `enviado`: saiu do sistema, problema pode ser filtro de spam do destinatário
   - `erro: ...`: leia a mensagem de erro

2. Verifique se a **Public Key** foi preenchida corretamente (sem espaços extras)

3. No painel EmailJS, verifique **Activity Log** para ver se a requisição chegou

4. Certifique-se de que o e-mail de envio está **verificado** no EmailJS (alguns provedores exigem verificação de domínio)

### E-mail cai no spam

1. Adicione o domínio do EmailJS à lista de remetentes confiáveis do servidor de destino
2. Configure **SPF** e **DKIM** no seu domínio (recomendado para uso profissional):
   - No painel EmailJS: **Email Services → seu serviço → Domain Authentication**
   - Adicione os registros DNS indicados no seu provedor de domínio (Registro.br, GoDaddy, Cloudflare, etc.)

### Limite de e-mails atingido (plano gratuito)

- EmailJS gratuito: 200 e-mails/mês
- Para ultrapassar: assine o plano Personal (US$ 15/mês = 1.000 e-mails)
- Alternativa sem custo: migrar para Resend.com (3.000/mês gratuitos)

### Erro "The Public Key is invalid"

- Acesse EmailJS → Account → General e copie a Public Key novamente
- Certifique-se de que não há espaço no início ou fim do valor
