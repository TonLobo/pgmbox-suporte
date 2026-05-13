# Manual de Usabilidade — PGMBox Suporte

**Para:** Analistas e novos usuários do sistema
**Versão:** 1.0

---

## Sumário

1. [Acesso ao sistema](#1-acesso-ao-sistema)
2. [Perfis de usuário](#2-perfis-de-usuário)
3. [Dashboard](#3-dashboard)
4. [Abrindo um novo chamado](#4-abrindo-um-novo-chamado)
5. [Gerenciando chamados](#5-gerenciando-chamados)
6. [Fila de E-mails](#6-fila-de-e-mails)
7. [Enviando para Consultoria](#7-enviando-para-consultoria)
8. [Relatório de SLA](#8-relatório-de-sla)
9. [Notificações](#9-notificações)
10. [Administração de usuários](#10-administração-de-usuários)
11. [Perguntas frequentes](#11-perguntas-frequentes)

---

## 1. Acesso ao sistema

### Primeiro acesso

1. Abra o sistema no navegador (Chrome, Edge ou Firefox recomendados)
2. Na tela de login, informe o **e-mail** e a **senha** fornecidos pelo administrador
3. Clique em **Entrar**

> Se você recebeu uma senha temporária, o sistema pedirá que você crie uma nova senha imediatamente após o login. A nova senha deve ter no mínimo 8 caracteres, incluindo uma letra maiúscula e um número.

### Esqueci minha senha

1. Na tela de login, clique em **Esqueci minha senha**
2. Informe seu e-mail cadastrado
3. Clique em **Enviar senha temporária**
4. Verifique sua caixa de entrada — você receberá uma senha temporária
5. Use-a para fazer login e cadastre uma nova senha quando solicitado

---

## 2. Perfis de usuário

O sistema possui 4 perfis com permissões diferentes:

| Perfil | Pode fazer |
|--------|-----------|
| **👑 Administrador** | Tudo — incluindo criar usuários, configurar SLA e e-mail |
| **🎯 Consultor** | Ver e trabalhar nos chamados encaminhados para consultoria |
| **🔧 Analista N3** | Abrir, gerenciar e encerrar qualquer chamado |
| **🔧 Analista N2** | Abrir e gerenciar chamados; enviar para consultoria |
| **🔧 Analista N1** | Abrir e gerenciar chamados atribuídos a si |

---

## 3. Dashboard

O Dashboard é a tela inicial após o login. Ele mostra:

- **Cards de resumo:** total de chamados, abertos, em atendimento, na consultoria, SLA vencido e vencendo em breve
- **Tabela de chamados recentes:** os últimos chamados com status, prioridade e contagem regressiva do SLA
- Clique em qualquer card para filtrar a lista de chamados por aquela condição
- Clique em qualquer linha da tabela para abrir o detalhe do chamado

---

## 4. Abrindo um novo chamado

1. Clique em **➕ Novo Chamado** (menu lateral ou botão no Dashboard)
2. Preencha os campos:

| Campo | Obrigatório | Dica |
|-------|-------------|------|
| Título | ✅ | Seja objetivo: "Erro ao gerar relatório PDF no módulo X" |
| Cliente | ✅ | Nome da entidade/órgão do cliente |
| Base / Ambiente | — | Produção, Homologação, Desenvolvimento |
| Módulo | — | Financeiro, RH, Compras... |
| Versão | — | Versão do sistema do cliente |
| Prioridade | ✅ | Veja a tabela abaixo |
| Analista | — | Padrão: você mesmo |
| Descrição | ✅ | Mínimo 20 caracteres; quanto mais detalhes, melhor |
| Passo a passo | — | Clique em "+ Adicionar passo" para cada ação que reproduce o erro |

**Tabela de prioridades:**

| Prioridade | Use quando | SLA padrão |
|-----------|-----------|-----------|
| 🔴 Crítica | Sistema completamente parado, impacto total | 2 horas |
| ▲ Alta | Funcionalidade principal indisponível | 8 horas |
| ● Média | Funcionalidade parcialmente afetada | 24 horas |
| ▼ Baixa | Dúvida, melhoria ou impacto mínimo | 72 horas |

3. Clique em **Abrir Chamado**
4. O sistema gera automaticamente o número SCCD-XXXXX e calcula o prazo do SLA

---

## 5. Gerenciando chamados

### Fluxo de atendimento

```
Aberto → Em Atendimento → Aguardando Cliente → Resolvido → Encerrado
```

Para avançar o status, abra o chamado e clique no botão de ação no canto superior direito:

| Status atual | Botão exibido | Próximo status |
|-------------|--------------|---------------|
| Aberto | Iniciar Atendimento | Em Atendimento |
| Em Atendimento | Aguardar Cliente | Aguardando Cliente |
| Aguardando Cliente | Marcar Resolvido | Resolvido |
| Resolvido | Encerrar Chamado | Encerrado |

> Cada mudança de status gera uma notificação automática para o analista responsável e registra no histórico do chamado.

### Adicionando informações ao chamado

Dentro de um chamado aberto, você pode:

- **Passos:** Clique em "+ Adicionar" na seção "Passo a Passo" para registrar cada ação executada no sistema
- **Evidências:** Clique em "+ Anexar" para adicionar prints, vídeos, PDFs ou arquivos de log
- **Observações:** Use o campo de texto no painel de histórico para registrar anotações

### Checklist de requisitos

Na parte inferior do chamado, há um painel que mostra os **4 requisitos mínimos** para envio à consultoria:

- ✓ Descrição do problema preenchida (mín. 20 caracteres)
- ✓ Passo a passo informado
- ✓ Evidência anexada (qualquer arquivo)
- ✓ Evidência visual anexada (print ou vídeo)

Quando todos estiverem com ✓ verde, o chamado pode ser enviado à consultoria.

---

## 6. Fila de E-mails

A fila de e-mails recebe automaticamente mensagens enviadas para o endereço de suporte configurado e as apresenta para recategorização.

### Como funciona

1. Um cliente envia e-mail para `suporte@suaempresa.com.br`
2. O e-mail aparece na **Fila de E-mails** (menu lateral com badge de contagem)
3. O analista vê o conteúdo do e-mail e preenche os campos de categorização:
   - Título do chamado
   - Cliente
   - Prioridade
   - Analista responsável
4. Clique em **✓ Criar chamado** para converter em chamado formal
5. Ou **✕** para descartar (spam, e-mail fora do escopo, etc.)

### Como integrar e-mail real

Clique no botão **"Como integrar e-mail real →"** na página da fila para ver as opções de integração (EmailJS Inbound, Zapier, Make ou webhook customizado).

---

## 7. Enviando para Consultoria

Quando o analista não consegue resolver o chamado internamente, ele pode escalá-lo para a consultoria.

**Pré-requisitos obrigatórios** (verificados automaticamente):
1. Descrição do problema com pelo menos 20 caracteres
2. Passo a passo para reproduzir o erro registrado
3. Ao menos uma evidência anexada
4. Uma evidência visual (print ou vídeo)

**Como enviar:**
1. Abra o chamado
2. Clique em **→ Consultoria** (botão no canto superior direito)
3. Opcionalmente selecione um consultor específico
4. Preencha as observações para a consultoria
5. Clique em **Enviar para Consultoria**

> Se algum requisito não estiver cumprido, o botão "Enviar" ficará desabilitado e a lista de pendências será exibida em vermelho.

---

## 8. Relatório de SLA

O relatório de SLA oferece uma visão analítica do desempenho do time.

### Filtros de período

- **7 dias / Este mês / Trimestre / Este ano:** clique no botão correspondente
- **Personalizado:** clique em "Personalizado" e informe as datas de início e fim

### O que cada métrica significa

| Métrica | Significado |
|---------|------------|
| Total no período | Chamados abertos no intervalo selecionado |
| Encerrados no prazo | Chamados finalizados antes do vencimento do SLA |
| Encerrados fora do prazo | Chamados finalizados após o vencimento — exibidos na tabela detalhada abaixo |
| Taxa de cumprimento SLA | % de chamados encerrados dentro do prazo (meta: ≥ 80%) |
| Tempo médio de atendimento | Média em horas entre abertura e encerramento |
| SLA vencido | Chamados ainda em aberto com SLA já expirado |
| Vencendo em <2h | Chamados em aberto com prazo expirando em menos de 2 horas |

### Ranking de analistas

Exibe os analistas ordenados pela taxa de cumprimento de SLA no período. Use para identificar quem precisa de apoio e reconhecer os melhores desempenhos.

---

## 9. Notificações

O sino 🔔 no canto superior direito mostra notificações do sistema.

Você recebe notificações quando:
- Um chamado é atribuído a você
- O status de um chamado seu é alterado
- Um chamado é enviado para consultoria (para consultores)
- Um e-mail entra na fila de recategorização

Clique no sino para ver todas as notificações. Clique em uma notificação para ir direto ao chamado relacionado. Use **"Marcar todas lidas"** para limpar o badge.

---

## 10. Administração de usuários

*Disponível apenas para o perfil Administrador.*

### Criar novo usuário

1. Acesse **Analistas** no menu lateral
2. Clique em **+ Novo Usuário**
3. Preencha nome, e-mail, perfil e senha inicial
4. Para perfil Analista, selecione o nível (N1, N2 ou N3)
5. Clique em **Criar Usuário**

> O usuário receberá a senha via e-mail (se SMTP configurado) ou você informa pessoalmente a senha inicial. Ele precisará trocá-la no primeiro acesso.

### Editar ou desativar usuário

1. Acesse **Analistas**
2. Clique em **Editar** na linha do usuário desejado
3. Altere nome, perfil, nível ou defina nova senha
4. Para desativar (sem excluir), clique em **Desativar** — o usuário não conseguirá mais fazer login

---

## 11. Perguntas frequentes

**O SLA do meu chamado venceu. O que faço?**
Priorize o atendimento imediatamente. O chamado ficará marcado com "⚠ Vencido" em vermelho. Informe o cliente sobre o atraso e registre uma observação no chamado com a justificativa.

**Posso transferir um chamado para outro analista?**
Sim. Abra o chamado, clique em "Editar" (se disponível para seu perfil) e altere o campo Analista. O novo analista receberá uma notificação.

**O cliente respondeu o e-mail. Como registro?**
Abra o chamado, vá na seção de histórico e adicione uma observação. Se o cliente enviou informações novas (print, log), anexe na seção de evidências.

**Como saber se meu e-mail de notificação está funcionando?**
Acesse **Configurações** e verifique o **Log de E-mails**. Se aparecer "simulado" é porque o SMTP ainda não foi configurado — veja o `manual-email.md` para configurar.

**Posso reabrir um chamado encerrado?**
Atualmente chamados encerrados não podem ser reabertos. Para um problema recorrente, abra um novo chamado e referencie o número do anterior na descrição.
