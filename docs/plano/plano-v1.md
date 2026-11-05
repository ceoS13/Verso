# Plano de implementação v1: Etapas 0 e 1 detalhadas

05/11/2026 · Versão 1.1 · Status: **aguardando aprovação do Pedro** · Base: PRD v3 (`docs/prd/verso-2.0-prd-v3.md`) + decisões E1 a E10 (`docs/decisoes.md`) + inventário do Verso atual (`docs/inventario-verso-atual.md`)

Na versão 1.1:
- divergências V2 a V16 aceitas (E5);
- matriz de papéis aprovada (E9);
- plano ajustado ao Verso atual em produção (E7), ao estilo Apple (E8) e à liberação sem piloto fechado (E10);
- novas divergências V17 a V21;
- nova Etapa M, de migração e virada.

Formato da seção 10.3 do PRD:
- no topo, as divergências e os pré-requisitos humanos;
- depois, para cada etapa: objetivo, arquivos e tabelas, fontes e decisão, riscos, testes e critério de aceite.

As Etapas 2 a 15 estão em alto nível. Cada uma ganha um plano detalhado, com pesquisa das referências, quando chegar a vez dela (E4).

Anexo: `docs/plano/matriz-papeis-fabrica.md` (papéis de fábrica × permissões, para validar).

---

## Resumo em 1 minuto

- **Etapa 0 (fundação, sem código de produção):**
  - stack e hospedagem decididas e registradas;
  - um teste técnico descartável para provar as três partes mais arriscadas (isolamento entre barbearias no banco, login com 2FA por papel, trava de horário duplicado);
  - protótipo clicável da navegação para você validar.
- **Etapa 1 (plataforma):** tudo o que é caro de mudar depois. Organização e unidade, isolamento testado, permissões com os 6 papéis, direitos do plano como dados, login com 2FA, PIN, auditoria, cofre de segredos, flags, fuso, telefone normalizado, traduções, design system e pipeline. Ao final você entra no Verso, convida uma pessoa, dá um papel e vê o que ela pode e não pode fazer. Ainda não há agenda nem caixa: isso começa na Etapa 2.
- **Etapa M (migração e virada):** traz as barbearias que usam o Verso atual para o sistema novo sem perder histórico, mantendo os links que elas já divulgaram.
- **O que ainda falta de você:** decidir a hospedagem (H2), o e-mail (H4), o GitHub (H7) e o dump do banco atual (H17). Divergências, matriz, revisor, domínio e logo já estão resolvidos.

---

## 1. Divergências do PRD

Cada linha diz o que muda em relação ao PRD, por quê e o status da decisão. As pendências P2 a P7 do `decisoes.md` continuam valendo e entram no plano das etapas delas.

| # | Onde no PRD | O que muda | Motivo | Status |
| --- | --- | --- | --- | --- |
| V1 | 0.1, 10.1 (Etapa 0) | A Etapa 0 vira a fundação: stack, arquitetura, teste técnico e protótipo. Não há inventário de código. | Projeto do zero (E1) | Decidida (E1, refinada por E7) |
| V2 | 10.2, critério geral 1 ("com a flag desligada, funciona exatamente como hoje") | Passa a valer assim: com a flag desligada, o módulo não aparece e nada do que já foi entregue muda de comportamento. Teste automatizado continua obrigatório. | As barbearias atuais continuam no Verso atual até a virada (Etapa M). Para elas, o "como hoje" é garantido pelo sistema atual | Aceita (E5) |
| V3 | Regra 5 e 8.15 (renomear "Planos" e "Funcionários") | Os nomes já nascem **Clube** e **Equipe**, como na navegação da 8.3. | Na virada, as barbearias atuais recebem um aviso dentro do produto explicando os nomes novos (10.1: nunca mudança silenciosa) | Aceita (E5) |
| V4 | 0.2 e 8.8 (tokens "extraídos do código") | Os tokens são definidos de novo no estilo Apple (V18), partindo da marca atual: laranja `#FF7A1A` e o símbolo em "V" da pasta `studio/` do Verso atual. | Reconstrução com novo visual (E8) | Aceita (E5, ajustada por E8) |
| V5 | 10.1, Etapa 1 ("telefone normalizado e mesclagem") | Na Etapa 1 entram a normalização, a restrição única no banco e a função de mesclar com desfazer em 7 dias, testada. A **tela** de mesclagem vai para a Etapa 2a, e é usada na migração dos clientes atuais (Etapa M), que é de onde vêm os duplicados. | Os duplicados aparecem quando os dados atuais forem importados | Aceita (E5, ajustada por E7) |
| V6 | 10.2, Etapa 1 ("cada permissão do catálogo tem teste que nega sem ela e permite com ela") | Na Etapa 1, o teste é feito no avaliador para todas as permissões. O teste na rota entra na etapa que cria cada rota, e o pipeline barra rota sem teste. | Na Etapa 1, a maior parte das rotas ainda não existe | Aceita (E5) |
| V7 | 4.26.3 item 2 (2FA por app autenticador ou código pelo WhatsApp do número central) | Até a Etapa 6, o 2FA é só por app autenticador, com códigos de recuperação. O código pelo WhatsApp chega com o número central do Verso. Proponho oferecer também chave de acesso (passkey), que é mais forte e mais fácil no celular. | O número central só existe na Etapa 6 | Aceita (E5) |
| V8 | 4.26.3 item 2 | O 2FA obrigatório vale para qualquer papel com permissão perigosa, não só Dono e Gerente. | Sem isso, copiar o Gerente para um papel personalizado escapa do 2FA | Aceita (E5) |
| V9 | 4.2 (regra nova) | Ninguém concede um papel, permissão, escopo ou limite maior do que tem. Só o Dono atribui o papel Dono. | O PRD não trata escalada de privilégio | Aceita (E5) |
| V10 | 10.1 (Etapas 2 e 3) | Uma tela mínima de Equipe sobe para a Etapa 1: convidar, dar papel de fábrica e unidades, desativar com perda de acesso na hora (4.2.9 item 2). Papéis personalizados, aprovações e delegação continuam na Etapa 2. | Sem isso, a Etapa 1 não tem como provar as permissões com pessoas reais | Aceita (E5) |
| V11 | 10.1 (Etapa 1) | Entram na Etapa 1 a fila transacional e o e-mail transacional. A fila transacional cumpre o papel da outbox: o trabalho é gravado na mesma transação do dado. | A Etapa 1 já envia e-mail (recuperação de senha, convite, alerta de login em dispositivo novo), e a regra 21 exige fila e outbox para todo envio | Aceita (E5) |
| V12 | 10.1 (Etapa 2) | A Etapa 2 fica dividida em quatro entregas: 2a configurações, catálogo, clientes e agenda; 2b caixa, comanda, pacotes e estoque; 2c financeiro, comissão, relatórios, notificações, importação e exportação; 2d papéis personalizados, aprovações e delegação. | É a etapa maior. Dividir mantém as entregas pequenas e seguras (regra 7) | Aceita (E5) |
| V13 | 10.1 (dependências) | A Etapa 11 depende também das 2, 5, 8 e 10. A Etapa 12 depende também das 5 e 8. | Agentes e ferramentas usam esses módulos (P6) | Aceita (E5) |
| V14 | 10.1 (liberação por barbearias piloto) | **Sem piloto fechado.** Cada etapa é liberada direto, depois de passar nos critérios de aceite. Continuam valendo: (a) o ensaio da migração com cópia anonimizada antes da virada (Etapa M), que protege os dados e não é um piloto; (b) as flags por barbearia, como interruptor de segurança; (c) a liberação gradual da Verso IA (4.23, camada 12), que é uma camada de proteção | O Verso atual já foi usado e aprovado pelas barbearias (E10) | Decidida (E10) |
| V15 | 4.2.4 (catálogo) | Nova permissão `clube.assinaturas.vender`. Exportar relatórios começa só no Dono e no Financeiro. | Detalhes na matriz anexa | Aceita (E5) |
| V16 | 0.3 e 4.2.2 ("Recepção" com dois sentidos, P4) | O papel do balcão passa a se chamar **Recepcionista**. "Recepção" fica para a IA. | Evita confusão na tela | Aceita (E5) |
| V17 | 0.1 e 10.1 | O Verso atual em produção é referência e fonte de dados. A Etapa 0 inclui o inventário (feito: `docs/inventario-verso-atual.md`) e o desenho da migração. Nova **Etapa M**: migração e virada das barbearias atuais | Existe sistema em produção, com dados reais (E7) | Decidida (E7) |
| V18 | 0.2 e 8.8 (tema escuro, destaque âmbar) | **Estilo Apple**: claro e escuro seguindo o aparelho, o laranja da marca `#FF7A1A` como única cor de destaque, tipografia do sistema, listas agrupadas, painéis que sobem de baixo no celular. Detalhes em `docs/decisoes.md` | Pedido do Pedro (E8) | Decidida (E8) |
| V19 | 4.19.1 (padrão de URL "a confirmar") | Mantém o padrão atual: `versostudio.com.br/<slug>` para a página, e o link mágico `/agendamento/<token>` continua abrindo | Não quebrar links e QR Codes que as barbearias já divulgaram | Decidida (consequência de E7) |
| V20 | Regra 5 | Funcionalidades atuais que o PRD não cita ganham lugar: **Preços e metas** (custo-hora, preço ideal, metas, despesas) no Financeiro; link mágico do agendamento; quadro ao vivo e ações em lote na agenda; impressão do caixa | Nada do que existe hoje pode sumir | Decidida (regra 5) |
| V21 | 4.11 e seção 6 | Métricas atuais com fórmula diferente da seção 6 mudam para a fórmula do PRD, com **relatório de impacto por barbearia** (valor antigo, novo e motivo) entregue na virada. Exemplos: faturamento pela data do lançamento em vez do recebimento, ticket médio por lançamento em vez de por comanda, "hoje" em UTC em vez do fuso da barbearia | Regra do PRD para métricas com o mesmo nome e fórmulas diferentes | Decidida (regra 4.11) |

---

## 2. Pré-requisitos humanos

| # | O quê | Responsável | Bloqueia | Observação |
| --- | --- | --- | --- | --- |
| H1 | Aprovar este plano, as divergências da seção 1 e a matriz de papéis | Pedro | Tudo | — |
| H2 | **Aberta.** Hospedagem: Google Cloud em São Paulo (proposta da seção 3.3) ou continuar na VPS da Hostinger de hoje. Depois, a conta com forma de pagamento | Pedro | Ambiente de teste da Etapa 1. O desenvolvimento local não depende disso | Hoje a VPS é compartilhada com outros dois apps e o backup fica na própria VPS |
| H3 | **Resolvido:** `versostudio.com.br`, com o padrão atual `/<slug>` (V19). Falta só o acesso ao DNS na hora de configurar | Pedro | Etapa 1 | — |
| H4 | Conta num provedor de e-mail transacional e acesso ao DNS do domínio | Pedro (eu configuro) | Etapa 1 | Para recuperação de senha, convites e alertas |
| H5 | **Resolvido:** logo e cor `#FF7A1A` na pasta `studio/` do Verso atual | — | — | — |
| H6 | **Resolvido:** o Pedro revisa os PRs sensíveis (E6), com roteiro de revisão em cada um | — | — | — |
| H7 | GitHub: Actions ativas, proteção da `main` (merge só por PR com checks verdes), Dependabot, segredos do repositório | Pedro (eu digo exatamente o que ligar) | Etapa 1 | — |
| H8 | Conta de monitoramento de erros e de logs | Pedro | Etapa 1 | O plano gratuito serve no começo |
| H9 | Asaas: a conta do Verso já existe (cobra as barbearias hoje). Falta o acesso ao sandbox | Pedro | Etapas 3 e 4 | D6 pendente: conta própria de cada barbearia ou subcontas |
| H10 | Meta: portfólio de negócios verificado e número central do Verso | Pedro | Etapa 6 | **Começar cedo**: a verificação da Meta pode levar semanas. D13 pendente |
| H11 | Evolution API: já existe uma instância para os lembretes atuais. Falta decidir se ela continua ou se sobe uma nova, com uma instância isolada por barbearia (5.3) | Pedro | Etapa 6 | — |
| H12 | Contas no provedor de LLM e no de transcrição | Pedro | Etapa 11 | Provedores a definir (10.4) |
| H13 | Revisão jurídica: termos de uso, política de privacidade, contrato de tratamento de dados, termo do clube, consentimento de menores, aviso de risco | Pedro + advogado | Liberação das Etapas 5, 6 e 7 | Pode começar em paralelo, com os modelos do Anexo C |
| H14 | Sem piloto fechado (E10). Os testes de uso da 8.14, com 5 donos ou recepcionistas e 5 barbeiros, ficam opcionais | Pedro | — | Recomendo pelo menos 2 barbeiros testando o fluxo do celular antes da virada |
| H15 | Decisões em aberto D6, D11, D12 e D13 | Pedro | Etapas 3, 4, 6 e 11 | — |
| H16 | Teste de intrusão externo | Pedro | Liberação geral da Recepção (Etapas 12 e 13) | — |
| H17 | Dump do banco de produção do Verso atual (`barbershop_master`), para montar e ensaiar a migração. Anonimizado antes de ir para o ambiente de teste (4.26.10 item 5) | Pedro | Etapa M | O próprio servidor tem o script de backup |
| H18 | Lista das barbearias que usam o Verso hoje e um contato em cada uma, para avisar da virada | Pedro | Etapa M | — |

**Nota sobre H6.** O Pedro é o revisor (E6). Cada PR sensível traz um roteiro curto: o que mudou em linguagem simples, o que conferir na tela e a prova dos testes de isolamento e permissão. O teste de intrusão externo (H16) continua antes de abrir a Recepção para todo mundo.

---

## 3. Arquitetura e stack proposta

Esta é a proposta que a Etapa 0 formaliza em registros de decisão técnica (ADR, 4.37.7). Nada aqui vira código antes da sua aprovação.

### 3.1 Visão geral

```
 Equipe (painel, PWA) ─┐
 Cliente (página,      ├──▶ apps/web  (Next.js)
  portal)              ┘        │  RPC tipado, sessão em cookie seguro
                                ▼
 Asaas, Meta, Evolution ──▶ apps/api  (Fastify)
  (webhooks)                     │  autenticação, RPC do painel, webhooks,
                                 │  e na onda 3 a API pública
                                 ▼
                          packages/core   ◀── apps/worker (fila, rotinas, envios, IA)
                     ações de negócio: permissão + direito do plano +
                     auditoria + fila transacional  (a "mesma porta", regra 17)
                                 │
                                 ▼
                     PostgreSQL 18  (segurança por linha, trava de horário,
                                     partições, fila de trabalhos)
```

O mesmo repositório (monorepo) tem três aplicações e pacotes compartilhados:
- `core`: ações de negócio;
- `db`: schema, migrações e acesso com organização;
- `auth`: login, 2FA e PIN;
- `permissoes`: catálogo e avaliador;
- `direitos`: plano do Verso;
- `i18n`: textos e formatação;
- `ui`: tokens e componentes;
- `observabilidade`: logs mascarados e rastreamento;
- `eventos`: plano de eventos de produto;
- `testes`: dados fictícios e baterias de isolamento e permissão.

### 3.2 Escolhas

Versões conferidas na pesquisa de 05/11/2026. Cada ADR fixa a versão exata.

| Camada | Escolha | Por quê | Alternativa descartada |
| --- | --- | --- | --- |
| Linguagem | TypeScript em tudo. Versão 6.0, com configuração já compatível com a 7 | Tipos compartilhados entre front, back, worker e IA. A 7 (compilador nativo, 8 a 12x mais rápido) ainda não tem a API que o lint usa; troco quando a 7.1 estabilizar | — |
| Runtime | Node.js 26 LTS (suporte até abr/2029) | LTS ativa desde 28/10/2026 | Node 24, que já está em manutenção |
| Front | Next.js 16.4 (no mínimo 16.4.5, por causa de falhas de segurança nas anteriores) com React 19.3. Um app com três áreas: painel (tema escuro), página pública e portal (marca da barbearia) | Página pública rápida no 4G com cache (8.12, 4.34.3 item 10), manifesto de PWA nativo, um só design system | Apps separados para painel e página (duplicaria componentes) |
| API | Fastify, com RPC tipado (tRPC) para o painel | Webhooks respondem em menos de 1 s sem depender do deploy do front (critério da Etapa 4). Escala separada (4.34.3 item 3). A mesma base serve à API pública da onda 3 | Rotas de API dentro do Next.js (misturam o front com receptores críticos) |
| Banco | PostgreSQL 18, no mínimo 18.6 | Segurança por linha (4.26.2). Trava de horário com `EXCLUDE USING gist` e `tstzrange`, ignorando cancelados (4.5.6). `uuidv7()` nativo, partições por tempo. A 18.6 corrige o CVE-2026-14666, que afetava a segurança por linha | MySQL (sem RLS nem EXCLUDE) |
| Acesso ao banco | Drizzle ORM 0.45 (no mínimo 0.45.2, por causa de uma injeção de SQL nas anteriores). Migro para a 1.0 quando sair, antes do primeiro dado real | Declara as políticas de segurança por linha no próprio schema e aceita migração em SQL puro para o que nenhum ORM modela (EXCLUDE, FORCE RLS, partições) | Prisma: o 7 não tem RLS nem EXCLUDE no schema, e o 8 ainda é versão candidata, com formato novo |
| Fila de trabalhos | pg-boss 12, dentro do próprio Postgres, atrás de uma interface própria | O trabalho é gravado **na mesma transação** da mudança de dado, o que já cumpre a saída transacional da 4.34.3 item 6. Os trabalhos entram no backup junto com o resto, e há um serviço a menos para operar. A interface permite trocar por BullMQ com Valkey na Etapa 13, se o teste de carga pedir | BullMQ + Redis desde já. Mais um serviço, e o Redis 8 tem licença AGPL/SSPL; se for preciso, usamos Valkey, que é BSD |
| Autenticação | Better Auth 1.7 (no mínimo 1.7.7, por causa de falhas de segurança nas anteriores): senha, 2FA TOTP com códigos de recuperação, passkey, verificação de senha vazada, lista e revogação de sessões, limite de tentativas guardado no banco | Cobre a 4.26.3. Papéis e permissões ficam nas nossas tabelas, não no plugin de organização dele, porque o nosso modelo é mais fino | Auth.js (só recebe correções de segurança, e o próprio time recomenda o Better Auth) e Lucia (deprecado) |
| Validação | Zod | Mesmo formato de entrada no painel, na IA e na API | — |
| Interface | Tailwind CSS 4 com tokens CSS (`@theme`) e componentes do shadcn/ui sobre Base UI, padrão desde jul/2026 | Componentes acessíveis de base. Tokens num único arquivo (8.8) | Biblioteca de componentes fechada (difícil de seguir os tokens) |
| Traduções | next-intl 4 (mensagens ICU) | Compatível com o Next 16 e com renderização no servidor. Formatação por organização (4.38) | — |
| Datas | date-fns com fuso (`@date-fns/tz`) | Tudo em UTC no banco, com conversão no fuso da unidade | — |
| Telefone | libphonenumber-js com regras brasileiras próprias (nono dígito, código de operadora) | Biblioteca mantida, com versão nova a cada duas semanas | — |
| Testes | Vitest 5, Testcontainers (Postgres real), Playwright 1.64 com axe-core | Isolamento e permissões testados contra um banco de verdade, e acessibilidade automática (8.9) | Banco simulado (não prova o isolamento) |
| Observabilidade | OpenTelemetry 3 (traces, métricas e logs estáveis), Sentry com coleta de dados pessoais **desligada** (na v11 ela vem ligada por padrão) | Rastreamento por requisição e por conversa (4.28) sem expor dado pessoal (4.26.10 item 4) | — |
| Monorepo | pnpm + Turborepo | Builds e testes só do que mudou | — |

### 3.3 Hospedagem: Google Cloud em São Paulo (southamerica-east1)

| Peça | Serviço |
| --- | --- |
| Web e API | Cloud Run |
| Worker (fila e rotinas) | Cloud Run worker pools, para processos longos (disponível desde abr/2026) |
| Banco | Cloud SQL para PostgreSQL 18, edição Enterprise: recuperação a um ponto no tempo de até 7 dias, mais backups diários com retenção configurável, que ajusto para os 30 dias da 4.28 item 4; extensão btree_gist disponível |
| Arquivos | Cloud Storage com link assinado de curta duração (4.26.9) |
| Chave-mestra do cofre | Cloud KMS |
| Cache, só se precisar | Memorystore Valkey |
| Borda | CDN e proteção contra abuso na frente da página pública, para cache e limite de requisições (4.26.5). Opções: Cloud CDN com Cloud Armor, ou Cloudflare só como borda. A escolha fica no ADR 002, por custo |

**Hoje** o Verso roda numa VPS da Hostinger, compartilhada com outros dois apps. É uma instância só, e o backup fica na própria VPS. Continuar nela é possível, mas faltam banco gerenciado com recuperação a um ponto no tempo, serviço de chaves e escala separada para o worker (4.34). A decisão é sua (H2).

**Por que o Google Cloud:**
- é o caminho **mais simples com um único fornecedor que atende tudo em São Paulo**: banco com segurança por linha e PITR, worker de longa duração, arquivos com link assinado e serviço de chaves;
- a AWS também atende, mas exige montar mais peças (rede, balanceador, NAT, permissões), e o serviço mais simples dela (App Runner) fechou para novos clientes;
- Vercel + Supabase seriam três fornecedores, e a Vercel não roda worker longo;
- Fly.io é mais barato, mas precisaria da AWS para arquivos e chaves, e a extensão btree_gist não está garantida no banco gerenciado de lá;
- Render, Railway e Cloudflare R2 não têm região ou dados no Brasil.

**Custo inicial estimado** (sem cotação; os preços de São Paulo não foram confirmados nas páginas oficiais):
- **Produção: US$ 90 a 170 por mês.** Banco dedicado pequeno US$ 50 a 100, Cloud Run (web, API e worker) US$ 30 a 60, armazenamento e chaves abaixo de US$ 5.
- **Teste: US$ 30 a 60 por mês.** Banco mínimo e Cloud Run escalando a zero.
- Na Etapa 0, confirmo os valores na calculadora oficial antes de fechar o ADR 002.

**LGPD.** Hospedar em São Paulo reduz as transferências internacionais, mas não elimina todas: e-mail, monitoramento de erros, WhatsApp e LLM ainda saem do Brasil. As cláusulas-padrão da ANPD (Resolução CD/ANPD 19/2024) são obrigatórias desde 23/08/2025. Todo fornecedor estrangeiro que recebe dado pessoal precisa delas no contrato. Isso entra na revisão jurídica (H13) e na lista pública de suboperadores (4.27.2).

**Duas consequências técnicas que a pesquisa trouxe:**
1. **A tabela de agendamentos não pode ser particionada por tempo.** No Postgres, uma trava de sobreposição numa tabela particionada só funciona se comparar a chave da partição por igualdade. Por isso particiono por tempo só as tabelas que só recebem inserções, que são justamente as da 4.34.3 item 2: auditoria, eventos, mensagens, uso de IA e transições de agendamento. Se um dia a tabela de agendamentos precisar ser dividida, a divisão é por organização.
2. **Desligar a coleta automática de dados pessoais no monitoramento de erros** (o padrão do Sentry v11 é ligada) e mascarar logs desde o primeiro commit.

### 3.4 Como uma ação passa pelo sistema ("a mesma porta", regra 17)

Toda ação de negócio é uma função única em `packages/core` que declara:
- a permissão que exige;
- o direito do plano que a libera;
- o formato da entrada;
- o nível de confirmação;
- o que vai para a auditoria;
- os eventos que dispara.

Essa mesma função é chamada por todas as entradas: o painel (pela API), os workers e rotinas, a Verso IA (Etapa 11) e a API pública (onda 3). Nenhuma delas tem atalho para o banco.

```ts
// packages/core/equipe/desativar-membro.ts (ilustrativo)
export const desativarMembro = acao({
  permissao: 'equipe.gerenciar',
  direito: null,                       // recurso da base, liberado em todos os planos
  confirmacao: 'confirmar',            // nível da 8.6
  entrada: z.object({ membroId: z.uuid() }),
  async executar(ctx, { membroId }) {
    const antes = await ctx.db.membros.buscar(membroId)   // já filtrado pela organização da sessão
    await ctx.db.membros.desativar(membroId)
    await ctx.sessoes.encerrarTodasDoUsuario(antes.usuarioId)
    ctx.auditar({ acao: 'membro.desativado', objeto: antes, depois: { ativo: false } })
    ctx.emitir('membro_desativado', { membroId })        // vai para a fila, na mesma transação
  },
})
```

O invólucro `acao(...)` faz sempre, nesta ordem:
1. abre a transação com a organização da sessão;
2. confere direito do plano, permissão, escopo e limite, e nega por padrão;
3. executa;
4. grava a auditoria e os trabalhos da fila (envios, integrações) na mesma transação.

Se qualquer passo falhar, nada é gravado.

### 3.5 Convenções

- **Nomes de domínio em português, sem acento**, como no Anexo A: `organizacao`, `unidade`, `agendamento`, `comanda`. Código de infraestrutura segue as convenções em inglês das bibliotecas.
- **Ids** em UUID v7: ordenáveis por tempo, bons para índice e para particionamento.
- **Datas** sempre `timestamptz` (UTC no banco), exibidas no fuso da unidade.
- **Dinheiro** em centavos, como número inteiro. Nunca ponto flutuante.

---

## 4. Etapa 0: fundação

**Objetivo:** tomar e registrar as decisões técnicas, provar que as três partes mais arriscadas funcionam e validar com você a navegação antes de construir qualquer tela de verdade.

**Tamanho:** P.

### 4.1 Entregas

1. **Registros de decisão técnica (ADRs)** em `docs/adr/`:
   - 001 stack;
   - 002 hospedagem, regiões e residência de dados;
   - 003 multi-inquilino com segurança por linha no banco;
   - 004 autenticação, 2FA e PIN;
   - 005 fila transacional e rotinas;
   - 006 cofre de segredos;
   - 007 i18n e formatação;
   - 008 design system e tokens;
   - 009 estratégia de testes (isolamento, permissões, ponta a ponta, acessibilidade);
   - 010 convenções de código e de banco.
2. **Perfil do projeto preenchido** (seção 0.2 do PRD) em `docs/perfil-projeto.md`: stack, quem decide técnica, mecanismo de flag, controle de acesso, design system.
3. **Teste técnico descartável** (fora de `apps/` e `packages/`, apagado ao fim da etapa), provando três coisas:
   - **isolamento:** uma sessão da organização A não lê nem grava dados da B, com o pool de conexões no modo de produção;
   - **login:** 2FA obrigatório por papel numa pessoa que está em duas barbearias, com papéis diferentes em cada;
   - **horário duplicado:** duas reservas simultâneas do mesmo barbeiro no mesmo horário resultam em uma só, garantido pela restrição do banco (4.5.6).
4. **Protótipo clicável da navegação**, publicado como página para você abrir no celular e no computador:
   - barra lateral do desktop e barra inferior por papel (8.3);
   - esqueleto das telas principais da 8.4 em baixa fidelidade;
   - você valida a navegação antes da Etapa 1.
5. **Confirmações de política que o PRD pede na Etapa 0:**
   - política vigente da Meta sobre IA no WhatsApp (4.21.2 item 3);
   - transferência internacional na LGPD: a pesquisa inicial está na seção 4.3; o ADR 002 registra a lista de fornecedores que precisam das cláusulas-padrão.
6. **Especificação dos dados de teste**: 5 barbearias fictícias, uma por perfil da 10.2. São usadas em todos os testes e demonstrações. Nunca dado real.
7. **Inventário do Verso atual e desenho da migração.** O inventário está feito (`docs/inventario-verso-atual.md`). O desenho da migração entra em `docs/migracao.md`:
   - mapa de dados do esquema atual para o novo, tabela por tabela;
   - regras de conversão: estados de agendamento; comissão em % no funcionário e em fração no serviço; normalização de telefone; assinaturas do clube como legado (4.13.7);
   - senhas: o hash atual (bcrypt) é aceito no primeiro login e trocado pelo novo, sem forçar ninguém a criar senha;
   - roteiro de virada por barbearia: congelar, importar, conferir, redirecionar;
   - lista de verificação dos totais (clientes, agendamentos, comissões, saldos) antes e depois.
8. **Repositório preparado**: estrutura do monorepo vazia, modelo de PR com checklist (permissão, isolamento, auditoria, i18n, estados de tela), CODEOWNERS para as áreas sensíveis e o pedido de proteção da `main` (H7).

### 4.2 Arquivos

`docs/adr/*`, `docs/perfil-projeto.md`, `docs/dados-de-teste.md`, `.github/pull_request_template.md`, `.github/CODEOWNERS`. Também o esqueleto de pastas da seção 3 e o teste técnico em `spikes/`, que é removido no fim.

### 4.3 Fontes pesquisadas e decisão

Pesquisa técnica de 05/11/2026. A política de rede deste ambiente bloqueou a maior parte dos sites oficiais: nextjs.org, postgresql.org, as páginas da AWS, do Google Cloud, da Vercel, do Fly, do Supabase, do Neon e o gov.br. Os fatos vieram de quatro caminhos: registro do npm (versões, datas e alertas de segurança), código-fonte e documentação oficial no GitHub, nodejs.org, e trechos de páginas oficiais devolvidos pela busca. O que veio só da busca, principalmente preço, fica confirmado na Etapa 0 antes do ADR.

| Tema | O que a pesquisa mostrou | Decisão |
| --- | --- | --- |
| Versões e segurança das bibliotecas | Next.js abaixo de 16.4.5, Better Auth abaixo de 1.7.7, Drizzle abaixo de 0.45.2 e PostgreSQL abaixo de 18.6 têm falhas de segurança publicadas. As que afetam o PostgreSQL e o Better Auth atingem justamente a segurança por linha e o 2FA | Versão mínima fixada em cada ADR. O pipeline barra dependência com alerta de segurança (4.26.10 item 2) |
| ORM | Prisma 7 sem RLS nem EXCLUDE no schema. Prisma 8 ainda é versão candidata. Drizzle declara RLS no schema e aceita SQL puro nas migrações | **Drizzle** (seção 3.2) |
| Fila | pg-boss 12 é maduro e roda no Postgres. BullMQ 6 ganhou um backend Postgres há só 3 meses. Redis 8 tem licença AGPL/SSPL; Valkey é BSD | **pg-boss** agora, com troca possível na Etapa 13 |
| Autenticação | Better Auth cobre TOTP, passkey, senha vazada, sessões e limite de tentativas. Auth.js só recebe correções e Lucia está deprecado | **Better Auth** |
| Trava de horário | O Postgres confirma `btree_gist` + `EXCLUDE` como o caminho recomendado. A forma nova (`WITHOUT OVERLAPS`) não aceita condição para ignorar cancelados | **EXCLUDE com condição** só nos estados ativos (4.5.6) |
| Hospedagem | Neon, Supabase e Fly têm banco em São Paulo, mas só o Google Cloud e a AWS entregam, num único fornecedor, banco, worker de longa duração, arquivos e serviço de chaves em São Paulo. Render, Railway e R2 não têm região no Brasil. O App Runner da AWS fechou para novos clientes | **Google Cloud em São Paulo** (seção 3.3) |
| LGPD | Cláusulas-padrão da ANPD obrigatórias para transferência internacional desde 23/08/2025 | Entra na revisão jurídica (H13) e no ADR 002 |
| Verso atual | NestJS, Prisma e React numa VPS da Hostinger. Um banco compartilhado, sem segurança por linha. Clube sem gateway. Página pública sem código de verificação. Oito problemas de segurança e de dados (inventário, seção 4) | Referência de funcionalidades e regras (E7). Os problemas viram critérios de aceite das etapas correspondentes: trava de horário no banco (2a), comissão fechada imutável (2c), código na página pública (7), adesão só depois do pagamento (5) |

**Pesquisas que ficam para a execução da Etapa 0:**
1. Política vigente da Meta sobre IA no WhatsApp (4.21.2 item 3).
2. Provedor de e-mail transacional com cláusulas-padrão da ANPD (H4).
3. Preços oficiais de São Paulo na calculadora do Google Cloud.
4. Revisão dos alertas de segurança das versões escolhidas.

Para que eu leia as fontes oficiais direto, vale liberar os domínios acima nas configurações de rede do ambiente.

### 4.4 Riscos

| Risco | Mitigação |
| --- | --- |
| Escolher hospedagem cara ou difícil de operar cedo demais | Começar no que a seção 3.3 propõe, com a saída para outra nuvem registrada no ADR 002 |
| A biblioteca de autenticação não suportar 2FA obrigatório por papel em várias barbearias | O teste técnico prova isso antes da Etapa 1. Se falhar, o plano B fica registrado no ADR 004 |
| O protótipo virar tela final sem passar pelo design system | O protótipo é baixa fidelidade e descartável. As telas reais saem dos componentes da Etapa 1 |

### 4.5 Critério de aceite da Etapa 0

1. Os 10 ADRs estão escritos e aprovados por você.
2. O teste técnico mostra as três provas passando, com o resultado registrado no ADR correspondente.
3. Você aprovou o protótipo da navegação, ou os ajustes viraram itens da Etapa 1.
4. O perfil 0.2 está preenchido, sem nenhum "a confirmar".
5. O desenho da migração cobre todas as tabelas do Verso atual com dados reais, e cada funcionalidade do inventário tem um lugar no Verso novo (V20).

---

## 5. Etapa 1: plataforma

**Objetivo:** construir tudo o que é caro de mudar depois. Toda etapa seguinte nasce isolada por barbearia, com permissão, direito de plano, auditoria, tradução e design system, sem retrabalho.

**Tamanho:** G. Entregue em 5 PRs pequenos (1a a 1e), cada um com testes e revisão.

### 5.1 Entregas por PR

**1a. Repositório, pipeline e observabilidade**
- Monorepo com `apps/web`, `apps/api` e `apps/worker`, e os pacotes da seção 3.
- Ambiente local com um comando (banco, que também guarda a fila, e e-mail de teste).
- Pipeline obrigatório (4.37), com todas as verificações abaixo bloqueando o merge:
  - verificação de tipos e lint sem avisos;
  - testes unitários e de integração com banco real em container;
  - teste de isolamento e teste de permissões;
  - ponta a ponta e acessibilidade;
  - textos fora de tradução;
  - dependências vulneráveis e segredos no commit;
  - migração perigosa.
- Logger estruturado que mascara telefone, remove conteúdo de mensagem e segredos (4.26.10 item 4), com teste.
- Rastreamento entre web, api e worker com um id por requisição (4.28 item 2).
- Captura de erros e endpoints de saúde.
- Ambientes de desenvolvimento, teste e produção (4.37 item 1).

**1b. Banco multi-inquilino, auditoria, fila transacional, flags e fuso**
- `organizacao` e `unidade` (4.31.1). Toda barbearia nasce como 1 organização + 1 unidade.
- Isolamento em três camadas (4.26.2):
  1. **segurança por linha no banco**: política em toda tabela, ligada e forçada, e um usuário de aplicação que não consegue contorná-la;
  2. **filtro obrigatório na camada de dados**: só existe acesso ao banco por `ctx.db`, já preso à organização da sessão;
  3. **chaves estrangeiras compostas com `organizacao_id`**: um registro da barbearia A não consegue apontar para um da B, nem por engano.
- A organização da requisição vem só da sessão (4.26.2 item 2). Sem organização definida, o banco devolve zero linhas: falha fechada.
- `auditoria`: só inserção. O usuário da aplicação não tem permissão de alterar nem apagar, e um gatilho no banco reforça isso. Particionada por mês (4.26.11, 4.34.3 item 2).
- Fila transacional (pg-boss) com processador no worker. Toda mensagem ou integração sai daqui, gravada na mesma transação do dado (4.34.3 item 6). Há uma fila por tipo de trabalho desde já (4.34.3 item 3).
- `flag` e `flag_atribuicao`: por organização, grupo ou percentual, com dono e data de remoção. O pipeline avisa quando uma flag passa da data (4.37 item 3).
- Fuso: cada unidade tem fuso IANA, sugerido pela UF do endereço e editável (4.1). Funções únicas de "dia local", "agora" e "amanhã às 10h" no fuso da unidade. Relógio injetável nos testes.

**1c. Identidade, sessão, 2FA e PIN**
- Login por e-mail e senha (4.26.3):
  - mínimo de 10 caracteres;
  - verificação contra senhas vazadas;
  - bloqueio progressivo após tentativas erradas;
  - alerta por e-mail de login em dispositivo desconhecido.
- 2FA: app autenticador com códigos de recuperação, e passkey opcional (V7). Obrigatório para papéis com permissão perigosa (V8): sem o segundo fator, a pessoa só acessa a tela de configurar 2FA.
- Sessões:
  - expiram por inatividade (12 h no painel; 30 dias com dispositivo confiável);
  - "Minha conta" lista as sessões ativas, com "encerrar";
  - troca de senha, e-mail, telefone ou papel encerra as outras sessões.
- PIN de 6 dígitos para ações críticas: diferente da senha, guardado com hash forte, bloqueado após 5 erros. O desbloqueio exige senha e 2FA.
- Recuperação de senha por e-mail e convite por link de uso único com validade de 7 dias (4.30 item 4). Os dois saem pela fila transacional.

**1d. Permissões, direitos do plano e equipe mínima**
- Catálogo de permissões no código (`modulo.recurso.acao`), versionado. Cada entrada traz:
  - escopos possíveis e tipo de limite (% ou R$);
  - se é perigosa;
  - o direito do plano que a libera;
  - textos em linguagem simples e o efeito visível ("esta pessoa vai ver o faturamento da barbearia"), já para a tela de papéis da Etapa 2.
- Os 6 papéis de fábrica conforme a matriz anexa.
- Avaliador no servidor (4.2.1):
  - libera só se direito do plano **e** atribuição ativa no escopo do registro **e** valor dentro do limite;
  - acima do limite, devolve "precisa de aprovação" (o pedido de aprovação é da Etapa 2);
  - na dúvida, nega e registra.
- Direitos do plano como dados (4.29.1): `plano_verso`, `direito_plano`, `uso_limite`. Na Etapa 1 existe só o plano interno "Piloto", com tudo liberado. Os planos comerciais são da Etapa 3 (D12).
- Atribuição com unidades e validade (`inicio`, `fim`), já pronta para a delegação da Etapa 2.
- Regra contra escalada de privilégio (V9).
- Visibilidade de dados sensíveis (4.2.5): telefone e e-mail mascarados no próprio servidor, conforme a permissão. O dado completo não chega ao navegador.
- Equipe mínima (V10): listar, convidar, dar papel de fábrica e unidades, desativar.
- Seletor de barbearia e unidade sempre visível (4.2.8). Nenhuma tela mistura duas barbearias.
- Autorização de acesso do suporte (4.26.7): o Dono concede com prazo (padrão 24 h) e escopo (leitura, ou leitura e escrita). Tudo vai para a auditoria, e ela aparece para o Dono. O uso pela equipe do Verso vem com o back-office na Etapa 3. Não existe "entrar como usuário".
- Cofre de segredos (4.26.6 item 4, regra 18):
  - criptografia envelope, com a chave-mestra no serviço de chaves da nuvem, fora do banco;
  - os segredos nunca saem do servidor;
  - a tela mostra só os 4 últimos caracteres;
  - a rotação da chave não perde dados.
  Na Etapa 1 o cofre fica pronto e testado. O primeiro uso real é a chave do Asaas, na Etapa 4.

**1e. Interface base, traduções, cliente e eventos**
- Design tokens num único arquivo (8.8):
  - três níveis de fundo escuro, texto em três níveis, destaque âmbar;
  - cores de sucesso, atenção, erro e informação;
  - uma cor por estado do agendamento;
  - escala tipográfica de 5 tamanhos com números tabulares;
  - espaçamento em múltiplos de 4;
  - 2 raios, 2 sombras e movimento entre 150 e 250 ms.
  Um **teste automático de contraste** reprova qualquer par de cores abaixo de AA.
- Componentes base (8.5 a 8.7):
  - formulário em painel lateral e modal de confirmação;
  - confirmações em três níveis: desfazer, confirmar e crítica com PIN;
  - chip de estado (texto mais cor), tabela, notificação breve com "desfazer";
  - skeleton e os estados padrão (vazio, erro, sem conexão, sem permissão, recurso do plano superior, integração necessária, manutenção);
  - shell com barra lateral, barra inferior por papel, busca global (atalho Ctrl+K; os resultados chegam na Etapa 2), central de notificações e indicador de saúde.
  Tudo publicado num catálogo de componentes que você pode abrir.
- Traduções (4.38): todo texto de interface em arquivos pt-BR desde o primeiro componente. O pipeline reprova texto escrito direto no código. Moeda, data, hora, número e telefone formatados pela configuração da organização.
- Cliente (identidade, 4.4.1):
  - tabela `cliente` com telefone em E.164 e restrição única por organização;
  - normalização brasileira: com e sem +55, com e sem o nono dígito, com e sem código de operadora;
  - casos ambíguos vão para revisão.
  Atenção: o WhatsApp às vezes informa celulares brasileiros antigos sem o nono dígito. O normalizador guarda a forma canônica, e o identificador do WhatsApp fica separado (usado na Etapa 6).
- Função de mesclar com desfazer em 7 dias (4.4.3). Cada módulo futuro registra como mover os próprios dados. A tela vem na Etapa 2 (V5).
- Plano de eventos de produto versionado (4.36.4), com os marcos de ativação da 4.30 já definidos. O pipeline barra evento com propriedade que pareça dado pessoal.
- Telas da Etapa 1:
  - Entrar, Recuperar senha, Configurar 2FA, Criar PIN;
  - Escolher barbearia e unidade;
  - Início vazio;
  - Minha conta (senha, 2FA, PIN, sessões);
  - Equipe;
  - Auditoria (Dono);
  - Acesso do suporte (Dono);
  - Sem permissão.

### 5.2 Tabelas da Etapa 1

Todas com `organizacao_id`, exceto as marcadas como globais. Também têm `criado_em` e `atualizado_em`, e índices começando por `organizacao_id`.

| Tabela | Campos principais | Diferença em relação ao Anexo A |
| --- | --- | --- |
| organizacao | nome, documento, status_conta, idioma, moeda, configuracoes | Igual |
| unidade | nome, endereço, UF, fuso, DDD padrão, configuracoes_sobrescritas, ativa | **Novo:** UF e DDD padrão (para sugerir o fuso e normalizar telefone sem DDD) |
| usuario (global) | nome, e-mail, telefone_e164, senha_hash, 2FA, pin_hash, tentativas, bloqueado_ate | Adaptada às tabelas da biblioteca de autenticação |
| sessao (global) | usuario, dispositivo, IP, criada_em, ultimo_uso, encerrada_em, 2fa_verificado | **Novo:** `2fa_verificado` |
| membro_equipe | usuario, vinculo, especialidades, ativo, desativado_em | **Novo:** `desativado_em` |
| permissao (global) | chave, módulo, ação, escopos, tipo_limite, perigosa, direito | **Novo:** espelha o catálogo do código, para as chaves estrangeiras |
| papel, papel_permissao | Como no Anexo A | Igual |
| atribuicao, atribuicao_unidade | membro, papel, unidades, inicio, fim, concedida_por | As unidades ficam numa tabela própria em vez de lista |
| plano_verso, direito_plano (globais) | Como no Anexo A | Igual |
| assinatura_verso | organização → plano, estado | Mínima. A cobrança é da Etapa 3 |
| uso_limite | chave, período, usado | Igual |
| flag (global), flag_atribuicao | Como no Anexo A | Igual |
| auditoria | autor_tipo, autor_id, a_pedido_de, ação, objeto, antes, depois, canal, IP, id da requisição | **Novo:** `a_pedido_de` (para "Verso IA a pedido de X") e id da requisição |
| tabelas da fila (pg-boss) | trabalho, fila, estado, tentativas, agendado_para | **Novo:** fazem o papel da outbox. Como são tabelas internas da biblioteca, cada trabalho leva o `organizacao_id` no conteúdo, e o processador abre a transação com essa organização. O conteúdo leva só ids, nunca dado pessoal. O teste de isolamento do catálogo trata o schema da fila como exceção declarada |
| evento_produto | Como no Anexo A | Particionada por mês |
| credencial | tipo, conteúdo cifrado, versão da chave, últimos 4 | **Novo:** guarda o que o Anexo A chama de `credencial_ref` |
| acesso_suporte | Como no Anexo A | Igual |
| cliente | nome, telefone_e164 (único por organização), e-mail, nascimento, anonimizado_em | Só identidade. O resto entra na Etapa 2 |
| mesclagem | principal, secundário, o que foi movido, desfazer_ate, desfeita_em | **Novo:** permite desfazer a mesclagem em 7 dias |

### 5.3 Fontes pesquisadas e decisão

Pesquisa de 05/11/2026 sobre como as referências fazem hoje os temas desta etapa. As páginas de ajuda da Booksy (support.booksy.com) e da Fresha (fresha.com) foram bloqueadas pela política de rede deste ambiente. Para elas, usei os trechos das páginas oficiais que a busca devolve. AppBarber, BestBarbers, Belasis e Space Barber publicam pouca documentação.

| Tema | O que as referências fazem | Decisão para o Verso | Motivo |
| --- | --- | --- | --- |
| Papéis e permissões | Booksy: 5 níveis fixos, com poucos ajustes. Fresha: papéis personalizados com permissão por ação ("baixar clientes", "responder conversas", "ver dados de toda a equipe"). Trinks: perfis padrão mais grupos personalizados (desde ago/2025), com colunas separadas para ver telefone, e-mail e CPF; a mudança de perfil leva até 10 minutos para valer. SQUIRE: liga e desliga permissões por barbeiro. theCut: só dono e barbeiro. **Nenhuma referência tem limite de valor nem aprovação acima do limite.** | **Melhorar** | O modelo do PRD já é o mais completo do mercado. Dois ajustes da pesquisa: (1) a permissão retirada vale **na hora**, nunca com atraso como no Trinks, porque o avaliador lê as atribuições a cada requisição; (2) mantenho uma só permissão de contato, `clientes.ver_contato`, porque o Verso não guarda CPF de cliente |
| Exposição de contatos | Booksy: quem cadastra o contato não volta a vê-lo, e há um alerta quando alguém abre muitos contatos em pouco tempo | **Copiar o alerta (proposta para a Etapa 2a)** | Protege a base da barbearia contra quem sai levando a lista. Pergunta abaixo |
| Comissão do barbeiro | Fresha: cada membro vê o próprio extrato. Booksy: o barbeiro não vê comissão, e o dono manda o relatório. BestBarbers: só no plano pago | **Copiar a Fresha** | Já está na matriz: `comissoes.ver` no escopo Próprio para Barbeiro e Barbeiro parceiro |
| 2FA | Ninguém exige. SQUIRE anunciou "em breve" (abr/2025). Booksy só recomenda MFA no e-mail. Trinks pede token por SMS só na conta digital. Fresha pede código por SMS ou e-mail em ações sensíveis | **Manter o PRD** (2FA obrigatório para papéis com permissão perigosa, V8) | Sai à frente do mercado sem custo de uso, com app autenticador e passkey |
| PIN no balcão | Fresha: PIN individual para trocar de usuário no mesmo aparelho, com bloqueio por inatividade ou após cada checkout | **Copiar (proposta para a Etapa 2b)** | Na recepção, várias pessoas usam o mesmo tablet. Sem troca rápida, todo mundo usa o login do dono, e a auditoria e as permissões perdem o sentido. Pergunta abaixo |
| Auditoria | Fresha: logs pontuais (não registra mudança de salário). Trinks: registra quem editou a ficha. Avec: cita logs na política, sem tela para o dono | **Melhorar** | Log único, imutável e visível ao Dono, cobrindo permissões, comissões e dinheiro (4.26.11) |
| Multi-unidade | SQUIRE: marca > loja > barbeiro, configuração "igual para todas ou por loja", bloqueio de cliente na marca ou só na loja. Fresha: unidades num mesmo espaço, serviço marcado por unidade, seletor no calendário, permissões por membro (não por unidade). Trinks: cada unidade é um estabelecimento separado, cliente achado na rede por CPF ou e-mail, consolidado como estabelecimento à parte. Booksy e Avec: uma conta por endereço | **Copiar SQUIRE e Fresha, descartar o resto** | O modelo organização > unidade com cliente único na rede (4.31.1) bate com o melhor do mercado, e o escopo por unidade supera a Fresha. Para a Etapa 6: bloqueio de número na organização ou só na unidade (SQUIRE) |
| Acesso do suporte | **Ninguém** documenta "conceder acesso ao suporte" com prazo. A SQUIRE mexe direto na conta do cliente no onboarding. A Fresha gera um código temporário para confirmar a identidade do dono numa ligação | **Manter o PRD e copiar o código da Fresha (Etapa 3)** | O acesso com prazo, autorizado e auditado (4.26.7) é diferencial. O código de identidade evita golpe de alguém se passando pelo dono no suporte |
| Planos e inadimplência do software (insumo para a Etapa 3) | Cobrança por profissional: Booksy (US$ 29,99 + US$ 20 por membro), Fresha (por membro com agenda ativa). Por faixa de profissionais: AppBarber (R$ 79,90 a R$ 219,90), Trinks, Avec. Por loja: SQUIRE (US$ 50 a US$ 150). theCut: cada barbeiro paga o próprio. Atraso no Trinks: "Atrasada", nova tentativa diária e depois "Suspensa", com desbloqueio imediato via Pix. Atraso na Fresha: pausa o acesso todo. Atraso no theCut: a barbearia some da busca | **Para a Etapa 3 (D12)** | Confirma a régua gradual da 4.29.3, que protege o cliente final, contra a pausa total da Fresha. Na 3, comparo por profissional ativo e por faixa |

Fontes principais:
- SQUIRE: [permissões do barbeiro](https://help.getsquire.com/s/article/Understanding-Commission-Rental-Barber-Permissions), [contas de marca](https://help.getsquire.com/s/article/Barber-Management-for-brand-accounts), [preços](https://getsquire.com/pricing)
- Booksy: [níveis de acesso](https://support.booksy.com/hc/en-us/articles/16535688940946), [contato do cliente](https://support.booksy.com/hc/en-us/articles/16540105025298)
- Fresha: [papéis](https://www.fresha.com/help-center/knowledge-base/team/100687-create-permission-roles), [troca por PIN](https://www.fresha.com/help-center/knowledge-base/personal-account/234-set-up-and-manage-pin-switching), [unidades](https://www.fresha.com/help-center/knowledge-base/workspace-settings/42-create-and-manage-business-locations)
- Trinks: [perfil de acesso](https://ajuda.trinks.com/perfil-de-acesso-do-profissional), [novidades 2025](https://ajuda.trinks.com/novidades-2025), [novidades 2026](https://ajuda.trinks.com/novidades-2026), [cliente em rede](https://ajuda.trinks.com/historico-de-cliente-em-rede)
- theCut: [funções do dono](https://thecut.co/owner-features)
- AppBarber: [funcionalidades](https://www.appbarber.com.br/funcionalidades/)

**Perguntas que a pesquisa levantou:**
1. **Alerta de contatos (Etapa 2a).** Quer que o Dono seja avisado quando alguém da equipe abrir muitos contatos de clientes em pouco tempo?
2. **Troca de usuário por PIN no balcão (Etapa 2b).** Quer o tablet da recepção compartilhado, com cada pessoa entrando com o próprio PIN e bloqueio após inatividade? Isso não está no PRD.

### 5.4 Riscos

| Risco | Impacto | Mitigação |
| --- | --- | --- |
| Configuração errada da segurança por linha, por exemplo com o pool de conexões reaproveitando a organização de outra requisição | Vazamento entre barbearias | A organização é definida por transação, nunca por conexão. O usuário da aplicação não consegue contornar a política. Há teste que reaproveita conexões de propósito, e o teste do catálogo de tabelas roda em todo PR |
| Motor de permissões complexo demais antes de existirem os módulos | Atraso | Avaliador simples sobre dados. O catálogo cresce por etapa, sem mudar o motor |
| Regra de texto direto no código atrapalhando o desenvolvimento | Atrito | Lista de exceções só para textos técnicos, revisada no PR |
| Ninguém revisando o código sensível (H6) | Falha de segurança passar | Resolver H6 antes do PR 1c |
| Etapa 1 inchar | Atraso | Os 5 PRs têm escopo fechado. O que surgir vira item de etapa futura |

### 5.5 Testes

1. **Isolamento, em três níveis:**
   - catálogo do banco: toda tabela que não é global tem `organizacao_id` obrigatório e política ligada e forçada; as de operação também têm `unidade_id`;
   - rotas: para cada rota e procedimento registrado, a sessão da A tenta ler e gravar dados da B e falha;
   - o pipeline reprova rota nova sem caso de isolamento.
2. **Permissões:**
   - para cada permissão do catálogo, o avaliador nega sem e permite com;
   - o escopo Próprio é testado com registro próprio e com registro alheio;
   - o limite é testado no valor exato e acima dele;
   - toda rota declara a permissão que exige, e rota sem declaração reprova.
3. **Direito do plano:** recurso fora do plano é negado no servidor, mesmo chamando a rota direto.
4. **2FA:** Dono, Gerente e papel com permissão perigosa não passam do login sem o segundo fator.
5. **Desativação:** membro desativado perde as sessões abertas na hora.
6. **Auditoria:** alterar ou apagar uma linha é recusado pelo banco.
7. **Segredos:** um segredo de teste nunca aparece em resposta de API nem em log.
8. **Telefone:** uma bateria de formatos brasileiros normaliza para o mesmo número; dois cadastros com o mesmo telefone são recusados pelo banco.
9. **Fuso:** "amanhã às 10h" resolvido em São Paulo, Manaus e Fernando de Noronha, e também num fuso com horário de verão, para o caso de ele voltar.
10. **Interface:** acessibilidade automática sem violações nas telas da Etapa 1; teste de contraste dos tokens; teste de ponta a ponta de login com 2FA, troca de barbearia e tela sem permissão.
11. **Fila transacional:** se a transação falha, nada é enviado. Se é gravada, o envio acontece uma vez, mesmo com o worker reiniciando, porque o processador é idempotente.

### 5.6 Critério de aceite da Etapa 1

São os critérios da Etapa 1 na 10.2, adaptados:
1. Uma requisição com sessão da organização A nunca lê nem grava dado da B, em todas as rotas.
2. Toda tabela tem `organizacao_id`, e as de operação têm `unidade_id`.
3. Cada permissão do catálogo tem teste que nega sem ela e permite com ela (V6).
4. Recurso fora do plano é negado no servidor.
5. Dono e Gerente não entram sem segundo fator.
6. Nenhum texto de interface fora dos arquivos de tradução.
7. Nenhum par de clientes com o mesmo telefone normalizado na mesma organização.

E os itens deste plano:
- A auditoria é imutável.
- Segredos não aparecem em resposta nem em log.
- Desativar um membro corta o acesso na hora.
- Os tokens passam no teste de contraste AA.
- As telas passam no teste de acessibilidade.
- Abrir o painel já logado mostra conteúdo útil em até 2 s (8.12), com a medição reportada.
- O primeiro teste de restauração do backup está documentado (4.28 item 4).
- Os cliques reais de "entrar com 2FA" e de "convidar pessoa e dar papel" estão reportados.

### 5.7 O que você vai conseguir testar ao fim da Etapa 1

Você entra no ambiente de teste com 2FA, cria seu PIN e convida duas pessoas: uma como Recepcionista, outra como Barbeiro. Cada uma entra com a própria conta. Você vê o que cada papel enxerga e o que aparece como "sem permissão", desativa uma delas e vê o acesso cair na hora. Depois abre a auditoria e encontra tudo registrado.

---

## 6. Etapas 2 a 15 em alto nível

Cada etapa ganha um plano detalhado quando chegar a vez dela, com a pesquisa das referências e as pendências do `decisoes.md`. Tamanho relativo: P, M, G, GG.

| Etapa | Entrega (resumo) | Depende de | Precisa de você antes | Tamanho |
| --- | --- | --- | --- | --- |
| 2a | Configurações da barbearia e funcionamento (feriados), catálogo de serviços e produtos, clientes e dependentes (com a tela de mesclagem), agenda completa (estados calculados, trava no banco, recorrência fixa, tempo real) | 1 | — | G |
| 2b | Caixa e comanda, pacotes, estoque | 2a | — | G |
| 2c | Financeiro e DRE, comissão, vales e fechamento, relatórios e dashboard (incluindo Performance e Ticket médio), **Preços e metas** (V20), notificações internas, importação e exportação | 2b | — | G |
| 2d | Papéis personalizados, aprovações, delegação, relatório "quem pode fazer o quê" | 1 (paralela à 2a–2c) | — | M |
| 3 | Plano do Verso e cobrança das barbearias (Asaas do Verso), régua de inadimplência, onboarding self-service e checklist, back-office | 1 | D12 (planos e preços), régua 4.29.3, H9 | G |
| 4 | Asaas: conexão, webhooks com idempotência e fila, reconsulta, "para revisar", taxas no financeiro, Pix dinâmico no caixa | 1, 2b | D6, H9 | M |
| 5 | Clube completo: planos, estados, Pix Automático e cartão, regras de uso, régua, pausa e cancelamento self-service, importação de assinaturas do Asaas, saúde do clube | 2, 4 | P5, termo do clube (H13) | GG |
| 6 | WhatsApp sem IA: oficial e não oficial com aviso, número central, modelos, emissor com janela de 24 h, Conversas, consentimento, lembretes, códigos de login. 2FA pelo WhatsApp | 1 | D13, H10, H11, P3 | G |
| 7 | Página da barbearia e portal do cliente | 5, 6 | P3, H3, política de privacidade (H13) | G |
| 8 | Sinal, score, lista de espera, cadeira vazia | 4, 6 | — | M |
| 9 | Ciclo de corte, RFV, ficha calculada, campanhas, pós-atendimento e avaliação sem filtro | 6 | P2 | G |
| 10 | Rateio do clube no fechamento e ficha técnica | 2c, 5 | Rateio padrão (10.4) | M |
| 11 | Verso IA: fundação e Gestor, créditos e chave própria, 12 camadas, conjunto de avaliação, custo por barbearia | 2, 5, 6, 8, 9, 10 (V13) | D11, H12 | GG |
| 12 | Recepção no WhatsApp, com liberação gradual (camada 12) | 11, 5, 8 | Primeiras barbearias da IA | GG |
| 13 | Escala: partições, filas com justiça, disjuntores, teste de carga | 12 | Metas 4.34 | G |
| **M** | **Migração e virada das barbearias atuais**: importação dos dados do Verso atual (barbearias, equipe e acessos, clientes, serviços e comissões por serviço, agendamentos, caixa e fluxo, comissões, estoque, preços, metas e despesas, assinaturas do clube como legado), mesclagem de duplicados, relatório de impacto das métricas (V21), ensaio com cópia anonimizada, virada por barbearia com redirecionamento das URLs antigas e aviso dentro do produto | 2, 3, 6, 7 | H17, H18 | G |
| 14 | Onda 2 | Conforme item | — | — |
| 15 | Onda 3 | Conforme item | — | — |

**Marcos:**
- Virada das barbearias atuais: após 2, 3, 6, 7 e M. É quando o Verso novo cobre tudo o que elas usam hoje (V20), inclusive página pública e lembretes.
- Primeira venda do Verso novo (plano Essencial): junto com a virada, ou logo depois, com a Etapa 4.
- Diferenciais à venda (Pro): após 5, 8, 9 e 10.
- Verso IA: após 11 e 12.

---

## 7. Próximos passos

1. Você decide H2 (hospedagem), H4 (e-mail) e H7 (GitHub), e libera o dump do H17.
2. Você responde às duas perguntas da pesquisa (seção 5.3): alerta de contatos e PIN no balcão.
3. Com o plano aprovado, executo a Etapa 0 e trago os ADRs, o resultado do teste técnico e o protótipo de navegação para você aprovar.
4. Aprovada a Etapa 0, começo a Etapa 1 pelo PR 1a.
