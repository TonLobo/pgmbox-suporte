# Guia de Integração com Sistemas Externos — PGMBox Suporte

Este guia mostra como integrar o PGMBox com outros sistemas para que dados reais reflitam no painel.

---

## Sumário

1. [API JavaScript (window.PGMBoxAPI)](#1-api-javascript)
2. [Webhook de recebimento de e-mails](#2-webhook-de-recebimento-de-e-mails)
3. [Integração com sistemas de gestão (ERP/CRM)](#3-integração-com-erp-crm)
4. [Função intermediária (Netlify / Cloudflare Workers)](#4-função-intermediária)
5. [Backend Node.js completo](#5-backend-nodejs-completo)
6. [Migrar para banco de dados real](#6-migrar-para-banco-de-dados-real)

---

## 1. API JavaScript

O PGMBox expõe `window.PGMBoxAPI` para qualquer script carregado na mesma página.

```javascript
// ── Criar chamado diretamente ──────────────────────────────
window.PGMBoxAPI.criarChamado({
  titulo: "Erro no módulo de compras",       // obrigatório
  cliente: "Prefeitura de Campinas",         // obrigatório
  prioridade: "alta",                        // baixa | media | alta | critica
  descricao: "Descrição detalhada...",
  base: "Produção",
  modulo: "Compras",
  versao: "3.2.1",
  analista_id: "usr_admin"                   // ID do analista (opcional)
});

// ── Enviar e-mail para a fila de recategorização ───────────
window.PGMBoxAPI.receberEmail({
  remetente: "usuario@prefeitura.sp.gov.br",
  nome: "João Silva",
  assunto: "Sistema não abre",
  corpo: "Bom dia, o sistema apresentou o seguinte erro..."
});

// ── Listar chamados com filtros ────────────────────────────
const abertos = window.PGMBoxAPI.getChamados({ status: 'aberto' });
const criticos = window.PGMBoxAPI.getChamados({ prioridade: 'critica' });
const busca    = window.PGMBoxAPI.getChamados({ busca: 'SCCD-00001' });

// ── Mudar status de um chamado ─────────────────────────────
window.PGMBoxAPI.mudarStatus('cha_123456789', 'resolvido', 'Problema corrigido na versão 3.2.2');
```

### Injetar script na mesma página

Se você tiver outro sistema rodando na mesma página ou abrindo o PGMBox em um iframe:

```javascript
// No sistema externo, após o iframe carregar:
const frame = document.getElementById('pgmbox-frame');
frame.onload = () => {
  frame.contentWindow.PGMBoxAPI.criarChamado({
    titulo: "Chamado criado pelo sistema externo",
    cliente: "Cliente XYZ",
    prioridade: "media"
  });
};
```

---

## 2. Webhook de recebimento de e-mails

O PGMBox é um HTML estático, então não tem servidor para receber webhooks diretamente. A solução é uma **função intermediária** que recebe o webhook externo e chama a API do PGMBox.

### Fluxo

```
Cliente envia e-mail
       ↓
Gmail/Outlook recebe
       ↓
Zapier/Make detecta novo e-mail
       ↓
POST para Netlify Function / Cloudflare Worker
       ↓
Função chama window.PGMBoxAPI.receberEmail()
       ↓
E-mail aparece na fila do PGMBox
```

---

## 3. Integração com ERP/CRM

### Cenário: Criar chamado automaticamente quando cliente abre OS no ERP

No seu ERP, ao criar uma Ordem de Serviço, adicione uma chamada de integração:

```javascript
// Exemplo em JavaScript (adaptável para qualquer linguagem)
async function notificarPGMBox(os) {
  // Se PGMBox está na mesma janela:
  if (window.PGMBoxAPI) {
    window.PGMBoxAPI.criarChamado({
      titulo: os.descricao,
      cliente: os.cliente.nome,
      prioridade: os.urgencia === 'S' ? 'alta' : 'media',
      descricao: os.observacoes,
      modulo: os.modulo,
      versao: os.versaoSistema
    });
  }
  // Se PGMBox está em outro domínio, use a função intermediária:
  else {
    await fetch('https://sua-funcao.netlify.app/api/criar-chamado', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        titulo: os.descricao,
        cliente: os.cliente.nome,
        prioridade: os.urgencia === 'S' ? 'alta' : 'media'
      })
    });
  }
}
```

### Cenário: Sincronizar status de chamado com sistema externo

```javascript
// Quando o PGMBox encerra um chamado, notificar o ERP
// Monitorando mudanças no localStorage:
window.addEventListener('storage', (event) => {
  if (event.key === 'pgmbox_chamados') {
    const chamados = JSON.parse(event.newValue || '[]');
    const encerrados = chamados.filter(c => c.status === 'encerrado');
    // Processar chamados encerrados e atualizar no ERP
    encerrados.forEach(c => atualizarERP(c.numero, 'encerrado'));
  }
});
```

---

## 4. Função intermediária

### Netlify Functions (gratuito)

Crie o arquivo `netlify/functions/email-inbound.js` no seu repositório:

```javascript
// netlify/functions/email-inbound.js
exports.handler = async (event) => {
  if (event.httpMethod !== 'POST') {
    return { statusCode: 405, body: 'Method Not Allowed' };
  }

  try {
    const payload = JSON.parse(event.body);

    // Aqui você salva em um banco de dados intermediário
    // (ex: Supabase, Firebase, Airtable) que o PGMBox lê via polling
    // OU usa uma solução de mensageria como Pusher/Ably

    // Exemplo salvando no Supabase:
    const { createClient } = require('@supabase/supabase-js');
    const supabase = createClient(process.env.SUPABASE_URL, process.env.SUPABASE_KEY);
    
    await supabase.from('fila_emails').insert({
      remetente: payload.remetente || payload.from,
      nome: payload.nome || payload.from_name,
      assunto: payload.assunto || payload.subject,
      corpo: payload.corpo || payload.body_plain,
      chegada_em: new Date().toISOString(),
      status: 'pendente'
    });

    return { statusCode: 200, body: JSON.stringify({ ok: true }) };
  } catch (err) {
    return { statusCode: 500, body: JSON.stringify({ error: err.message }) };
  }
};
```

Deploy no Netlify:
```bash
npm install -g netlify-cli
netlify deploy --prod
```

### Cloudflare Workers (gratuito, 100.000 req/dia)

```javascript
// worker.js
export default {
  async fetch(request, env) {
    if (request.method === 'POST' && new URL(request.url).pathname === '/email-inbound') {
      const payload = await request.json();
      
      // Salvar no KV Store do Cloudflare
      const fila = JSON.parse(await env.PGMBOX_KV.get('fila') || '[]');
      fila.unshift({
        id: Date.now().toString(),
        ...payload,
        chegadaEm: new Date().toISOString(),
        status: 'pendente'
      });
      await env.PGMBOX_KV.put('fila', JSON.stringify(fila));
      
      return new Response(JSON.stringify({ ok: true }), {
        headers: { 'Content-Type': 'application/json' }
      });
    }
    return new Response('Not found', { status: 404 });
  }
};
```

---

## 5. Backend Node.js completo

Se você quiser uma solução com backend real (banco de dados + múltiplos usuários simultâneos):

```javascript
// server.js — Express + SQLite
const express = require('express');
const sqlite3 = require('sqlite3').verbose();
const nodemailer = require('nodemailer');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');

const app = express();
app.use(express.json());

const db = new sqlite3.Database('./pgmbox.db');

// Inicializar tabelas
db.serialize(() => {
  db.run(`CREATE TABLE IF NOT EXISTS users (
    id TEXT PRIMARY KEY, nome TEXT, email TEXT UNIQUE,
    senha_hash TEXT, perfil TEXT, nivel INTEGER, ativo INTEGER DEFAULT 1,
    criado_em TEXT
  )`);
  db.run(`CREATE TABLE IF NOT EXISTS chamados (
    id TEXT PRIMARY KEY, numero TEXT, titulo TEXT, cliente TEXT,
    descricao TEXT, prioridade TEXT, status TEXT, analista_id TEXT,
    aberto_em TEXT, prazo_sla TEXT, encerrado_em TEXT
  )`);
  db.run(`CREATE TABLE IF NOT EXISTS fila_emails (
    id TEXT PRIMARY KEY, remetente TEXT, nome TEXT,
    assunto TEXT, corpo TEXT, chegada_em TEXT, status TEXT DEFAULT 'pendente'
  )`);
});

// ── Rota: receber e-mail inbound ──────────────────────────
app.post('/api/email-inbound', (req, res) => {
  const { remetente, nome, assunto, corpo } = req.body;
  const id = 'fila_' + Date.now();
  db.run(
    'INSERT INTO fila_emails VALUES (?,?,?,?,?,?,?)',
    [id, remetente, nome, assunto, corpo, new Date().toISOString(), 'pendente'],
    (err) => {
      if (err) return res.status(500).json({ error: err.message });
      res.json({ ok: true, id });
    }
  );
});

// ── Rota: criar chamado via API ───────────────────────────
app.post('/api/chamados', (req, res) => {
  const { titulo, cliente, prioridade, descricao, analista_id } = req.body;
  const id = 'cha_' + Date.now();
  const slaHoras = { baixa: 72, media: 24, alta: 8, critica: 2 };
  const prazo = new Date(Date.now() + (slaHoras[prioridade] || 24) * 3600000);
  const numero = 'SCCD-' + String(Date.now()).slice(-5);
  db.run(
    'INSERT INTO chamados VALUES (?,?,?,?,?,?,?,?,?,?,?)',
    [id, numero, titulo, cliente, descricao, prioridade, 'aberto',
     analista_id, new Date().toISOString(), prazo.toISOString(), null],
    (err) => {
      if (err) return res.status(500).json({ error: err.message });
      res.json({ ok: true, chamado: { id, numero } });
    }
  );
});

// ── Rota: listar chamados ─────────────────────────────────
app.get('/api/chamados', (req, res) => {
  const { status, prioridade } = req.query;
  let sql = 'SELECT * FROM chamados WHERE 1=1';
  const params = [];
  if (status) { sql += ' AND status = ?'; params.push(status); }
  if (prioridade) { sql += ' AND prioridade = ?'; params.push(prioridade); }
  db.all(sql, params, (err, rows) => {
    if (err) return res.status(500).json({ error: err.message });
    res.json(rows);
  });
});

// ── Envio de e-mail com Nodemailer ────────────────────────
const transporter = nodemailer.createTransport({
  host: process.env.SMTP_HOST,
  port: 587,
  secure: false,
  auth: {
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS
  }
});

app.post('/api/enviar-email', async (req, res) => {
  const { para, assunto, corpo } = req.body;
  try {
    await transporter.sendMail({
      from: `"PGMBox Suporte" <${process.env.SMTP_USER}>`,
      to: para, subject: assunto, text: corpo
    });
    res.json({ ok: true });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

app.listen(3000, () => console.log('PGMBox Backend rodando na porta 3000'));
```

**Instalar e rodar:**
```bash
npm install express sqlite3 nodemailer bcryptjs jsonwebtoken
node server.js
```

**Variáveis de ambiente (.env):**
```
SMTP_HOST=mail.suaempresa.com.br
SMTP_USER=suporte@suaempresa.com.br
SMTP_PASS=sua-senha-smtp
JWT_SECRET=chave-secreta-longa
```

---

## 6. Migrar para banco de dados real

Para migrar o armazenamento do `localStorage` para um banco de dados real, substitua as funções `gc` e `sc` no início do `index.html` por chamadas ao seu backend:

```javascript
// Substituir no index.html:
const gc = async (n) => {
  const res = await fetch(`/api/${n}`);
  return res.json();
};
const sc = async (n, d) => {
  await fetch(`/api/${n}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(d)
  });
};
```

Isso transforma o sistema em uma SPA completa com persistência no servidor, acessível por múltiplos usuários simultaneamente.
