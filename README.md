# PGMBox Suporte

> Sistema de Gestão de Chamados de Suporte — aplicação web completa, sem servidor, pronta para implantação.

---

## ✨ Funcionalidades

| Módulo | Descrição |
|--------|-----------|
| **Autenticação** | Login seguro, recuperação de senha, troca obrigatória no primeiro acesso |
| **Chamados** | Fluxo completo: Aberto → Em Atendimento → Aguardando Cliente → Resolvido → Encerrado |
| **Backlog** | Fila de e-mails aguardando recategorização, tempo de espera em tempo real, SLA pausado |
| **SLA** | Configurável por prioridade; dashboard com gráficos, filtros por período e analista |
| **Analistas** | Perfis Admin, Consultor, Analista N1/N2/N3 com controle de acesso |
| **Consultoria** | Envio com checklist obrigatório de requisitos mínimos |
| **Notificações** | Internas + e-mail (EmailJS) a cada mudança de status |
| **Relatório SLA** | Gráficos, ranking de analistas, filtros avançados, chamados fora do prazo |
| **Email-to-Ticket** | Integração via EmailJS, Zapier, Make ou webhook |
| **API Pública** | `window.PGMBoxAPI` para integração com sistemas externos |

---

## 🚀 Implantação — GitHub Pages (recomendado)

```bash
# 1. Suba o repositório no GitHub
# 2. Settings → Pages → Branch: main → / (root) → Save
# Sistema disponível em: https://SEU_USUARIO.github.io/pgmbox-suporte
```

**Alternativas:** Netlify Drop, Vercel, ou qualquer servidor web estático.

---

## 🔑 Primeiro Acesso

| Campo | Valor |
|-------|-------|
| E-mail | `everton@pgmbox.com.br` |
| Senha | `Admin@2025` |

> Troque a senha imediatamente após o primeiro acesso em **Configurações**.

---

## 🔐 Segurança

- Senhas armazenadas como hash — nunca em texto puro
- Sessão isolada por domínio (`localStorage` + `sessionStorage`)
- Sem envio de dados externos (exceto EmailJS se configurado)
- **Hospedar sempre em HTTPS** em produção (GitHub Pages, Netlify, Vercel)
- Para base centralizada entre múltiplos usuários: ver `docs/guia-integracao.md`

---

## 🛠️ Estrutura

```
pgmbox-suporte/
├── index.html                ← Sistema completo
├── README.md
└── docs/
    ├── manual-usuario.md     ← Manual para analistas
    ├── manual-email.md       ← Configuração de e-mail
    └── guia-integracao.md    ← Integração com sistemas externos
```

---

*PGMBox Suporte — Consultoria PGMBOX · Licença MIT*
