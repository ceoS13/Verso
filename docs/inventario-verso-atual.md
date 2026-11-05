# Inventário do Verso atual (referência para a reconstrução)

Leitura feita em 05/11/2026 do repositório `sauleromorales/Verso-Studio` (último commit em 01/11/2026), em produção em versostudio.com.br. Ele serve como **referência** (E7): o código não é reaproveitado, mas nenhuma funcionalidade nem nenhum dado das barbearias pode se perder.

Quando a documentação do repositório diverge do código, vale o código.

## 1. Stack e infraestrutura atuais

| Item | Hoje |
| --- | --- |
| Back | NestJS 11, Prisma 5, PostgreSQL 16, JWT com refresh, bcrypt |
| Front | React 18, Vite 5, Tailwind 3, servido por nginx |
| Mobile | Expo 54 / React Native: protótipo do app do cliente, com identidade de outra marca |
| Hospedagem | VPS da Hostinger, com Docker Compose e Caddy (HTTPS automático, limite de requisições). A máquina é compartilhada com outros dois apps |
| Deploy | Push na `main`: SSH na VPS, `git pull` e `docker compose up --build` |
| Backup | `pg_dump` diário, guardado por 14 dias **na mesma VPS**. Não existe cópia fora dela |
| Integrações | Asaas (só a cobrança do SaaS) e Evolution API (WhatsApp: confirmação e lembrete). O envio vem desligado por padrão; se está ligado em produção, não confirmei |
| Domínio e URLs | `versostudio.com.br/<slug>` (vitrine e agendamento), `/<slug>/login` (painel), `/agendamento/<token>` (link mágico), `/master` (operação do SaaS). O domínio antigo `smstetix.com` redireciona para o novo |

## 2. Multi-inquilino

- Um Postgres só, compartilhado entre as barbearias. O isolamento é lógico, pela coluna `barbershopId`, verificado em guards do NestJS. **Não há segurança por linha no banco.**
- A barbearia vem do token, não do front. Divergência entre header e token dá 403.
- Sobraram resquícios de arquiteturas antigas: campos de banco por tenant no MySQL, arquivos SQLite. Estão sem uso.

## 3. O que existe hoje, e onde fica no Verso novo

Pela regra 5 do PRD, toda funcionalidade atual precisa de um lugar no sistema novo.

| Funcionalidade atual | Regra relevante hoje | No Verso novo |
| --- | --- | --- |
| Dashboard com cards configuráveis | Faturamento = soma das entradas pela data de criação do lançamento | 4.11 e seção 6 (Etapa 2c). As fórmulas mudam: precisa de **relatório de impacto** (V21) |
| Agendamentos: lista, calendário, quadro ao vivo, ações em lote, checkout com produtos e serviços extras | Estados SCHEDULED, DELAYED, IN_PROGRESS, COMPLETED, CANCELLED, NO_SHOW. Uma rotina cancela o atraso acima de 60 min. Concluir exige caixa aberto | 4.5 (Etapa 2a), com estados calculados e trava de horário no banco. O "quadro ao vivo" e as ações em lote entram na agenda |
| Clientes e histórico | Telefone único por barbearia. A exclusão é física, em cascata | 4.4 (Etapa 2a), com anonimização no lugar da exclusão física (4.27.4) |
| Funcionários | 3 percentuais de comissão (serviço, produto, assinatura). A exclusão é física, em cascata | Equipe (4.2) e comissão (4.10) |
| Serviços, com comissão por serviço e barbeiro | A comissão por serviço define quem atende cada serviço na página pública | 4.3 e 4.10.1 |
| Estoque | Entrada, saída, ajuste, devolução, alerta de mínimo | 4.8 (Etapa 2b) |
| Caixa (abertura e fechamento) | No máximo um caixa aberto, garantido no banco. A diferença entre esperado e contado vira lançamento | 4.6.2 (Etapa 2b) |
| Fluxo de caixa: lançamentos, estorno, impressão, CSV | Estorno devolve o estoque e apaga as comissões pendentes | 4.6 e 4.9 (Etapas 2b e 2c) |
| Comissões, com recálculo retroativo | O recálculo "forçado" apaga até as comissões já pagas | 4.10 (Etapa 2c), com fechamento imutável. Recálculo só antes do fechamento |
| Planos de assinatura do clube | **Sem gateway**: pagamento registrado à mão, ciclo de +1 mês, uso contado por mês civil | Clube 4.13 (Etapa 5). As assinaturas atuais entram como **legado** (4.13.7 item 1) |
| Performance | Receita de serviços e produtos por barbeiro, evolução diária | Relatórios (Etapa 2c) |
| Ticket médio | Receita dividida pelo número de lançamentos (não de atendimentos) | Seção 6 (comandas avulsas). Relatório de impacto (V21) |
| **Preços e metas**: precificação por custo-hora, preço ideal por serviço, metas mensais, despesas fixas e variáveis | Fórmula de custo-hora com margem e taxa de cartão; meta com previsão linear | **Não está no PRD.** Entra em Gestão > Financeiro (Etapa 2c): despesas viram contas a pagar recorrentes (4.9 item 3), e "Preços e metas" vira uma tela do Financeiro (V20) |
| Configurações: dados da barbearia, horário por dia com vários intervalos e fuso | Não há grade por barbeiro, folgas nem bloqueios | 4.1 e 4.5.1 (Etapa 2a) |
| Página pública de agendamento | Sem login: o cliente é identificado só pelo telefone, **sem código** | 4.19 (Etapa 7), com código por WhatsApp (4.19.4) |
| Link mágico do agendamento (ver e cancelar sem login) | Válido por 30 dias; cancela até 120 min antes | Portal (4.19.3). O link mágico continua, como atalho para o agendamento (V20) |
| WhatsApp: confirmação e lembrete de 24 h | Fila com novas tentativas, janela 8h–21h, via Evolution | 4.24 e 4.5.7 (Etapa 6) |
| Equipe e permissões | 12 permissões e 3 modelos prontos (Barbeiro, Recepcionista, Gerente) | 4.2, com catálogo granular e a matriz aprovada (E9) |
| Minha assinatura (SaaS) e faixa de atraso | Asaas, 5 dias de carência, bloqueio automático | 4.29 (Etapa 3), com a régua gradual que protege o cliente final |
| Painel master: barbearias, planos SaaS, isenção, métricas, auditoria, backup, restauração e exclusão de barbearia | — | Back-office (4.36, Etapa 3). **Sem** "entrar como usuário". Restaurar e apagar dados de uma barbearia viram operações internas com dupla confirmação e auditoria |
| App mobile do cliente | Protótipo, com partes quebradas | PWA da página e do portal (4.39). O protótipo é descartado |

## 4. Problemas encontrados no sistema em produção

Conferi os três primeiros direto no código. Eles afetam o Verso **de hoje** e não esperam o sistema novo.

| # | Problema | Risco | Onde |
| --- | --- | --- | --- |
| A1 | Qualquer pessoa cria uma conta de cliente numa barbearia (`/auth/register/client`, público) e ativa sozinha um plano do clube com status ATIVO, **sem pagar nada** (`POST /subscriptions/subscribe`, sem restrição de papel) | Cortes de graça; números do clube errados | `subscriptions.controller.ts:80`, `subscriptions.service.ts` |
| A2 | Qualquer funcionário dispara o recálculo de comissões. O modo "forçado" **apaga as comissões já pagas** do período e recria só parte delas | Perda do histórico de comissões | `appointments.controller.ts:94` |
| A3 | Backup só na mesma VPS | Se a VPS cair ou for perdida, todos os dados de todas as barbearias se perdem | `docker-compose.prod.yml` |
| A4 | Agendamento e cliente sem verificação de permissão no servidor, só na tela. Qualquer funcionário pode apagar agendamentos (exclusão física) | Perda de dados | `appointments.controller.ts` |
| A5 | A página pública aceita horários sobrepostos com inícios diferentes, e a remarcação não verifica conflito | Dois clientes no mesmo horário | `appointments.service.ts` |
| A6 | "Hoje" calculado em UTC no servidor: entre 21h e meia-noite, o dashboard, as metas e o ticket médio olham o dia errado | Números do dia errados à noite | `reports.service.ts` |
| A7 | Excluir cliente ou funcionário apaga em cascata agendamentos e comissões | Perda de histórico | `clients`, `employees` |
| A8 | `GET /barbershops/public` lista todas as barbearias ativas | Uma barbearia vê as outras (fere a premissa 3 do PRD) | `barbershops.controller.ts` |

## 5. Identidade visual atual

- **Cor da marca: `#FF7A1A`** (símbolo, ícone do app, `theme-color`). A interface hoje usa outro tom, o âmbar `#F59E0B`. No Verso novo, a cor de destaque passa a ser a da marca (E8).
- **Símbolo:** um "V" de traços brancos e creme (`#FFE1C2`) sobre laranja, com um nó na base. Há variantes em cor, branca e escura (`#1C0E0C`) e o logo horizontal.
- **Arquivos:** pasta `studio/` do repositório atual (SVG e PNG de 16 a 1080 px, `favicon.ico`) e `frontend/public/icone-app-512.svg`.
- **Tipografia:** já usa SF Pro, com Inter como alternativa, e há uma referência visual inspirada na Apple (`docs/STYLE_REFERENCE.md`). O estilo Apple (E8) continua essa direção.
- **Acessibilidade:** texto branco sobre `#FF7A1A` tem contraste de cerca de 2,6:1, abaixo do mínimo AA (4,5:1). No ADR 008, o botão laranja usa rótulo escuro, e os textos e links laranja usam um tom mais fechado.

## 6. Sinais de uso

- Pelo menos uma barbearia real com uso diário ("Barba Reis"), com meses de agendamentos, clientes e comissões.
- O total de barbearias e usuários não aparece no repositório.
- A migração precisa de um dump do banco de produção (`barbershop_master`), anonimizado para testes (4.26.10 item 5).
