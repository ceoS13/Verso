# Plano de implementação v1: Etapas 0 e 1 detalhadas

05/11/2026 · Status: **aguardando aprovação do Pedro** · Base: PRD v3 (`docs/prd/verso-2.0-prd-v3.md`) + decisões E1 a E4 (`docs/decisoes.md`)

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
- **O que eu preciso de você agora:**
  1. aprovar ou ajustar as divergências da seção 1;
  2. resolver os pré-requisitos H2 a H7 da seção 2 (conta de nuvem, domínio, e-mail, logo, revisor de código, GitHub);
  3. validar a matriz de papéis.

---

## 1. Divergências do PRD

Cada linha diz o que muda em relação ao PRD, por quê e o que preciso que você responda. As pendências P2 a P7 do `decisoes.md` continuam valendo e entram no plano das etapas delas.

| # | Onde no PRD | O que muda | Motivo | Pergunta para o Pedro |
| --- | --- | --- | --- | --- |
| V1 | 0.1, 10.1 (Etapa 0) | A Etapa 0 vira a fundação: stack, arquitetura, teste técnico e protótipo. Não há inventário de código. | Projeto do zero (E1) | Decidida |
| V2 | 10.2, critério geral 1 ("com a flag desligada, funciona exatamente como hoje") | Passa a valer assim: com a flag desligada, o módulo não aparece e nada do que já foi entregue muda de comportamento. Teste automatizado continua obrigatório. | Não existe "hoje" para preservar | Ok? |
| V3 | Regra 5 e 8.15 (renomear "Planos" e "Funcionários") | Como não há menu antigo, os nomes já nascem **Clube** e **Equipe**, como na navegação da 8.3. | Sem legado para preservar | Ok? |
| V4 | 0.2 e 8.8 (tokens "extraídos do código") | Os tokens são definidos do zero sobre a identidade descrita: tema escuro, destaque âmbar, logo quadrado laranja com "V". | Não há código | Você tem o logo em arquivo (SVG ou PNG grande) e o código da cor? Sem isso, faço um provisório. |
| V5 | 10.1, Etapa 1 ("telefone normalizado e mesclagem") | Na Etapa 1 entram a normalização, a restrição única no banco e a função de mesclar com desfazer em 7 dias, testada. A **tela** de mesclagem vai para a Etapa 2, junto com Clientes e a importação, que é de onde vêm os duplicados num sistema novo. | Sem clientes antigos, não há duplicados para revisar na Etapa 1 | Ok? |
| V6 | 10.2, Etapa 1 ("cada permissão do catálogo tem teste que nega sem ela e permite com ela") | Na Etapa 1, o teste é feito no avaliador para todas as permissões. O teste na rota entra na etapa que cria cada rota, e o pipeline barra rota sem teste. | Na Etapa 1, a maior parte das rotas ainda não existe | Ok? |
| V7 | 4.26.3 item 2 (2FA por app autenticador ou código pelo WhatsApp do número central) | Até a Etapa 6, o 2FA é só por app autenticador, com códigos de recuperação. O código pelo WhatsApp chega com o número central do Verso. Proponho oferecer também chave de acesso (passkey), que é mais forte e mais fácil no celular. | O número central só existe na Etapa 6 | Ok obrigar app autenticador (Google Authenticator e afins) para Dono e Gerente até lá? |
| V8 | 4.26.3 item 2 | O 2FA obrigatório vale para qualquer papel com permissão perigosa, não só Dono e Gerente. | Sem isso, copiar o Gerente para um papel personalizado escapa do 2FA | Ok? |
| V9 | 4.2 (regra nova) | Ninguém concede um papel, permissão, escopo ou limite maior do que tem. Só o Dono atribui o papel Dono. | O PRD não trata escalada de privilégio | Ok? |
| V10 | 10.1 (Etapas 2 e 3) | Uma tela mínima de Equipe sobe para a Etapa 1: convidar, dar papel de fábrica e unidades, desativar com perda de acesso na hora (4.2.9 item 2). Papéis personalizados, aprovações e delegação continuam na Etapa 2. | Sem isso, a Etapa 1 não tem como provar as permissões com pessoas reais | Ok? |
| V11 | 10.1 (Etapa 1) | Entram na Etapa 1 a saída transacional (outbox) e o e-mail transacional. | A Etapa 1 já envia e-mail (recuperação de senha, convite, alerta de login em dispositivo novo), e a regra 21 exige fila e outbox para todo envio | Ok? |
| V12 | 10.1 (Etapa 2) | A Etapa 2 fica dividida em quatro entregas: 2a configurações, catálogo, clientes e agenda; 2b caixa, comanda, pacotes e estoque; 2c financeiro, comissão, relatórios, notificações, importação e exportação; 2d papéis personalizados, aprovações e delegação. | É a etapa maior. Dividir mantém as entregas pequenas e seguras (regra 7) | Ok? |
| V13 | 10.1 (dependências) | A Etapa 11 depende também das 2, 5, 8 e 10. A Etapa 12 depende também das 5 e 8. | Agentes e ferramentas usam esses módulos (P6) | Informativo |
| V14 | 10.1 (liberação) | Proponho um **piloto fechado** logo após a 2b (agenda, clientes, caixa), com 1 ou 2 barbearias amigas e onboarding feito por nós. O primeiro marco vendável é depois das Etapas 3, 4, 6 e 7. | Num sistema novo, quanto antes houver uso real, menos retrabalho | Você tem barbearias para o piloto fechado? |
| V15 | 4.2.4 (catálogo) | Nova permissão `clube.assinaturas.vender`. Exportar relatórios começa só no Dono e no Financeiro. | Detalhes na matriz anexa | Ok? |
| V16 | 0.3 e 4.2.2 ("Recepção" com dois sentidos, P4) | O papel do balcão passa a se chamar **Recepcionista**. "Recepção" fica para a IA. | Evita confusão na tela | Ok? |

---

## 2. Pré-requisitos humanos

| # | O quê | Responsável | Bloqueia | Observação |
| --- | --- | --- | --- | --- |
| H1 | Aprovar este plano, as divergências da seção 1 e a matriz de papéis | Pedro | Tudo | — |
| H2 | Conta na nuvem escolhida (seção 3.3), com forma de pagamento | Pedro | Ambiente de teste da Etapa 1. O desenvolvimento local não depende disso | Custo inicial estimado na seção 3.3 |
| H3 | Domínio do Verso e padrão de endereço das páginas das barbearias (ex.: `verso.app/b/nome` ou `nome.verso.app`) | Pedro | Etapa 1 (e-mail e ambiente de teste); Etapa 7 (páginas) | O padrão de URL estava "a confirmar" na 4.19.1 |
| H4 | Conta num provedor de e-mail transacional e acesso ao DNS do domínio | Pedro (eu configuro) | Etapa 1 | Para recuperação de senha, convites e alertas |
| H5 | Logo em arquivo e cor de destaque | Pedro | Etapa 1 (tokens) | Sem isso, sigo com um provisório |
| H6 | **Revisor humano de código** nas áreas sensíveis: login, permissões, isolamento, pagamentos, webhooks, IA (4.37.6) | Pedro decide quem | Primeiro PR sensível, na Etapa 1 | Ver a nota abaixo da tabela |
| H7 | GitHub: Actions ativas, proteção da `main` (merge só por PR com checks verdes), Dependabot, segredos do repositório | Pedro (eu digo exatamente o que ligar) | Etapa 1 | — |
| H8 | Conta de monitoramento de erros e de logs | Pedro | Etapa 1 | O plano gratuito serve no começo |
| H9 | Asaas: conta do próprio Verso (para cobrar as barbearias) e acesso ao sandbox | Pedro | Etapas 3 e 4 | D6 pendente: conta própria de cada barbearia ou subcontas |
| H10 | Meta: portfólio de negócios verificado e número central do Verso | Pedro | Etapa 6 | **Começar cedo**: a verificação da Meta pode levar semanas. D13 pendente |
| H11 | Servidor para a Evolution API | Pedro (eu configuro) | Etapa 6 | — |
| H12 | Contas no provedor de LLM e no de transcrição | Pedro | Etapa 11 | Provedores a definir (10.4) |
| H13 | Revisão jurídica: termos de uso, política de privacidade, contrato de tratamento de dados, termo do clube, consentimento de menores, aviso de risco | Pedro + advogado | Liberação das Etapas 5, 6 e 7 | Pode começar em paralelo, com os modelos do Anexo C |
| H14 | Barbearias piloto nos 5 perfis da 10.2, e 5 donos ou recepcionistas mais 5 barbeiros para os testes de uso da 8.14 | Pedro | Liberação de cada etapa a partir da 2 | — |
| H15 | Decisões em aberto D6, D11, D12 e D13 | Pedro | Etapas 3, 4, 6 e 11 | — |
| H16 | Teste de intrusão externo | Pedro | Liberação geral da Recepção (Etapas 12 e 13) | — |

**Nota sobre H6.** O PRD exige revisão de código obrigatória nas áreas sensíveis. Hoje o time somos eu, que escrevo o código, e você, que decide produto. As opções:
- (a) contratar um dev sênior ou consultor de segurança para revisar só os PRs sensíveis, algumas horas por semana;
- (b) aceitar revisão automatizada (testes, análise estática, revisão por IA independente) mais a sua revisão funcional, com teste de intrusão antes de abrir para o público.

Recomendo (a) a partir da Etapa 1. O primeiro código sensível (login e isolamento) é exatamente onde um erro custa mais.

---

## 3. Arquitetura e stack proposta

Esta é a proposta que a Etapa 0 formaliza em registros de decisão técnica (ADR, 4.37.7). Nada aqui vira código antes da sua aprovação.

<<STACK>>

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
    ctx.emitir('membro_desativado', { membroId })        // vai para a outbox, na mesma transação
  },
})
```

O invólucro `acao(...)` faz sempre, nesta ordem:
1. abre a transação com a organização da sessão;
2. confere direito do plano, permissão, escopo e limite, e nega por padrão;
3. executa;
4. grava a auditoria e a outbox na mesma transação.

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
   - 005 filas, outbox e rotinas;
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
   - situação da LGPD sobre transferência internacional, para a escolha de região (ADR 002).
6. **Especificação dos dados de teste**: 5 barbearias fictícias, uma por perfil da 10.2. São usadas em todos os testes e demonstrações. Nunca dado real.
7. **Repositório preparado**: estrutura do monorepo vazia, modelo de PR com checklist (permissão, isolamento, auditoria, i18n, estados de tela), CODEOWNERS para as áreas sensíveis e o pedido de proteção da `main` (H7).

### 4.2 Arquivos

`docs/adr/*`, `docs/perfil-projeto.md`, `docs/dados-de-teste.md`, `.github/pull_request_template.md`, `.github/CODEOWNERS`. Também o esqueleto de pastas da seção 3 e o teste técnico em `spikes/`, que é removido no fim.

### 4.3 Fontes pesquisadas e decisão

<<FONTES_ETAPA0>>

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

---

## 5. Etapa 1: plataforma

**Objetivo:** construir tudo o que é caro de mudar depois. Toda etapa seguinte nasce isolada por barbearia, com permissão, direito de plano, auditoria, tradução e design system, sem retrabalho.

**Tamanho:** G. Entregue em 5 PRs pequenos (1a a 1e), cada um com testes e revisão.

### 5.1 Entregas por PR

**1a. Repositório, pipeline e observabilidade**
- Monorepo com `apps/web`, `apps/api` e `apps/worker`, e os pacotes da seção 3.
- Ambiente local com um comando (banco, fila, e-mail de teste).
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

**1b. Banco multi-inquilino, auditoria, outbox, flags e fuso**
- `organizacao` e `unidade` (4.31.1). Toda barbearia nasce como 1 organização + 1 unidade.
- Isolamento em três camadas (4.26.2):
  1. **segurança por linha no banco**: política em toda tabela, ligada e forçada, e um usuário de aplicação que não consegue contorná-la;
  2. **filtro obrigatório na camada de dados**: só existe acesso ao banco por `ctx.db`, já preso à organização da sessão;
  3. **chaves estrangeiras compostas com `organizacao_id`**: um registro da barbearia A não consegue apontar para um da B, nem por engano.
- A organização da requisição vem só da sessão (4.26.2 item 2). Sem organização definida, o banco devolve zero linhas: falha fechada.
- `auditoria`: só inserção. O usuário da aplicação não tem permissão de alterar nem apagar, e um gatilho no banco reforça isso. Particionada por mês (4.26.11, 4.34.3 item 2).
- `outbox` com processador no worker: toda mensagem ou integração sai daqui (4.34.3 item 6).
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
- Recuperação de senha por e-mail e convite por link de uso único com validade de 7 dias (4.30 item 4). Os dois saem pela outbox.

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
| outbox | tipo, payload, criado_em, processado_em, tentativas, erro | **Novo:** não estava no Anexo A |
| evento_produto | Como no Anexo A | Particionada por mês |
| credencial | tipo, conteúdo cifrado, versão da chave, últimos 4 | **Novo:** guarda o que o Anexo A chama de `credencial_ref` |
| acesso_suporte | Como no Anexo A | Igual |
| cliente | nome, telefone_e164 (único por organização), e-mail, nascimento, anonimizado_em | Só identidade. O resto entra na Etapa 2 |
| mesclagem | principal, secundário, o que foi movido, desfazer_ate, desfeita_em | **Novo:** permite desfazer a mesclagem em 7 dias |

### 5.3 Fontes pesquisadas e decisão

<<FONTES_ETAPA1>>

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
11. **Outbox:** se a transação falha, nada é enviado; se é gravada, o envio acontece uma vez, mesmo com o worker reiniciando.

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
| 2b | Caixa e comanda, pacotes, estoque. **Piloto fechado** (V14) | 2a | Barbearias do piloto fechado | G |
| 2c | Financeiro e DRE, comissão, vales e fechamento, relatórios e dashboard, notificações internas, importação e exportação | 2b | — | G |
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
| 12 | Recepção no WhatsApp, piloto | 11, 5, 8 | Barbearias piloto | GG |
| 13 | Escala: partições, filas com justiça, disjuntores, teste de carga | 12 | Metas 4.34 | G |
| 14 | Onda 2 | Conforme item | — | — |
| 15 | Onda 3 | Conforme item | — | — |

**Marcos:**
- Piloto fechado: após a 2b.
- Primeira venda (plano Essencial): após 2, 3, 4, 6 e 7.
- Diferenciais à venda (Pro): após 5, 8, 9 e 10.
- Verso IA: após 11 e 12.

---

## 7. Próximos passos

1. Você responde às perguntas da seção 1 e valida a matriz de papéis. Pode ser direto aqui no chat, item por item.
2. Você resolve H2 a H7, ou me diz qual caminho prefere em cada um.
3. Com o plano aprovado, executo a Etapa 0 e trago os ADRs, o resultado do teste técnico e o protótipo de navegação para você aprovar.
4. Aprovada a Etapa 0, começo a Etapa 1 pelo PR 1a.
