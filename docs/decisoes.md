# Decisões, divergências e pendências

Registro vivo. O PRD (`docs/prd/verso-2.0-prd-v3.md`) não é editado. Tudo o que ajusta, interpreta ou complementa o PRD fica aqui, com data e quem decidiu.

Status: **Aberta** (precisa do Pedro) · **Decidida** · **Para o plano** (entra no plano da etapa, como divergência com pergunta).

---

## Decisões do Pedro

| # | Data | Decisão |
| --- | --- | --- |
| E1 | 05/11/2026 | O projeto é **do zero**, sem código anterior. A proposta de P1 está aprovada. |
| E2 | 05/11/2026 | A stack técnica é proposta pelo agente no plano da Etapa 0 e aprovada pelo Pedro antes de qualquer código. Linha inicial: TypeScript em tudo, Next.js (painel, página pública e PWA), API e workers em Node, PostgreSQL (RLS, restrição de exclusão contra horário duplicado, particionamento) e Redis para filas. |
| E3 | 05/11/2026 | O código fica no repositório `ceoS13/verso` (GitHub), branch padrão `main`. |
| E4 | 05/11/2026 | Plano no formato 10.3 com as Etapas 0 e 1 detalhadas. As Etapas 2 a 15 ficam em alto nível e cada uma é detalhada, com pesquisa das referências, quando chegar a vez dela. |
| E5 | 05/11/2026 | Divergências V2 a V16 do plano v1 **aceitas como propostas**. O Pedro pediu "arruma as divergências". Ajustes por causa de E7: V5 (a tela de mesclagem entra junto com a migração dos dados atuais), V14 (o piloto fechado sai de barbearias que já usam o Verso atual) e a parte de identidade visual de V4 (substituída por E8). |
| E6 | 05/11/2026 | **O Pedro é o revisor humano** dos PRs sensíveis (H6): login, permissões, isolamento, pagamentos, webhooks e IA. Cada PR sensível traz um roteiro de revisão em linguagem simples: o que mudou, o que conferir e a prova dos testes. |
| E7 | 05/11/2026 | **Existe um Verso atual, em produção:** `sauleromorales/Verso-Studio`, no ar em versostudio.com.br. O sistema novo é uma reconstrução que **usa o atual como referência e pode mudar tudo**. Isso refina E1: o código não é reaproveitado, mas o que existe hoje (funcionalidades, regras e dados das barbearias) entra no plano. Inventário do sistema atual em `docs/inventario-verso-atual.md`. |
| E8 | 05/11/2026 | **Design no estilo Apple**, inspirado nas Human Interface Guidelines. Substitui o "tema escuro com destaque âmbar" do perfil 0.2 e da 8.8 do PRD. Proposta de aplicação na seção "Identidade visual" abaixo. Continuam valendo as regras de UX da seção 8 do PRD: uma ação por tela, cliques, estados, acessibilidade AA. |

---

## Identidade visual no estilo Apple (E8): proposta de aplicação

O que "estilo Apple" quer dizer no Verso. Isto vira o ADR 008 e os tokens da Etapa 1.

1. **Claro e escuro seguindo o aparelho**, como nos apps da Apple, com opção de fixar um dos dois em "Minha conta". O PRD previa só o tema escuro no painel. A página pública e o portal seguem o mesmo esquema, usando a cor da barbearia.
2. **Hierarquia pela tipografia, não pela cor.** Fonte do sistema (`system-ui`): aparece como SF Pro no iPhone e no Mac, e como a fonte nativa no Android e no Windows. Títulos grandes e números tabulares. Escala de 5 tamanhos (8.8).
3. **Muito respiro, poucas bordas.** Fundo neutro, conteúdo em cartões e listas agrupadas no estilo dos Ajustes do iPhone, cantos arredondados contínuos, separadores finos. Sombra só para o que flutua.
4. **Uma cor de destaque só: o laranja da marca**, usado como cor de ação ("tint"): botões principais, seleção, links e foco. As cores de estado (sucesso, atenção, erro, informação) seguem a paleta de sistema da Apple, e o estado do agendamento sempre aparece com texto junto (8.5).
5. **Materiais translúcidos com parcimônia.** Vidro fosco só nas barras fixas (barra lateral, barra de abas, cabeçalho ao rolar), nunca atrás de texto longo. Respeita a opção "reduzir transparência".
6. **Componentes com cara de iOS e macOS:**
   - no celular: barra de abas embaixo, formulários em painéis que sobem de baixo ("sheets"), botões grandes, alvos de 44 px;
   - no desktop: barra lateral, painel lateral para formulários, seletor segmentado para Dia, Semana e Lista;
   - em todos: confirmações curtas, com o botão nomeado pela ação.
7. **Movimento suave**, com animações de mola entre 150 e 250 ms. Respeita "reduzir movimento".
8. **Ícones** lineares e consistentes. A licença dos SF Symbols proíbe o uso na web, então a base é o Lucide, que o Verso atual já usa, em traço fino.
9. **O PRD continua valendo**: contraste AA, estados padrão, limite de cliques, desempenho e texto sempre com cor.

Pendente: cor exata do laranja e arquivo do logo, a partir do inventário do Verso atual (pasta `studio/` com os favicons).

---

## Achados da leitura do PRD (05/11/2026)

### P1. O PRD supõe um Verso já existente; o pedido é construir do zero. Status: Decidida (E1)

O PRD foi escrito para evoluir um sistema que já existe. Estes trechos dependem disso:
- 0.1 itens 2, 5 e 6, e o perfil 0.2 ("módulos existentes", campos "a confirmar lendo o repositório").
- Regra 5 ("nada do que existe hoje nos menus atuais pode sumir").
- 4.4.3 item 1 (duplicados existentes), 4.11 (unificar o dashboard atual), 4.13.7 item 1 (assinaturas do módulo Planos atual), seção 6 (métricas atuais).
- 10.1 Etapa 0 (inventário do código) e a regra de liberação ("barbearias atuais recebem convite").
- 10.2, critério geral 1 ("com a flag desligada, a barbearia funciona exatamente como hoje").

Proposta, se for do zero mesmo:
- A Etapa 0 vira a etapa de fundação: escolha da stack, arquitetura, preenchimento do perfil 0.2 e protótipo da navegação. Continua sem código de produção.
- A Parte A deixa de ser régua de paridade e passa a ser escopo de construção.
- As flags por barbearia continuam, para liberar em piloto.
- Os itens de legado ficam fora (N/A). Continuam valendo a importação por CSV (4.12) e a importação assistida de assinaturas cobradas por link no Asaas (4.13.7 item 2), porque as barbearias novas chegam de outros sistemas.

### P2. Segmentação RFV: há clientes sem segmento e clientes com dois. Status: Para o plano (Etapa 9)

A seção 4.15.3 exige que todo cliente com pelo menos 1 atendimento esteja em exatamente um segmento (critério de aceite da Etapa 9). As regras padrão não garantem isso:
- **Sem segmento**: cliente com F = 2 e R ≤ 1,2 que não é assinante. Não é Novo (que exige F = 1), não é Fiel (que exige F ≥ 3) nem Campeão.
- **Sem segmento**: cliente com F = 1, primeiro atendimento há mais de 45 dias e R ≤ 1,2. Acontece com serviço de ciclo longo.
- **Dois segmentos**: assinante com 1 atendimento recente e V no top 20% entra em Novo e em Campeão ao mesmo tempo.
- Também não está definido "top 20%" de qual população.

Proposta: avaliar numa ordem fixa em que a primeira regra que bate vence (Perdido > Hibernando > Em risco > Campeão > Fiel > Novo), mais um segmento de sobra para R ≤ 1,2 (ex.: "Recorrente") ou Fiel a partir de F ≥ 2. A decisão é do Pedro.

### P3. Código de login por WhatsApp quando a barbearia não tem WhatsApp conectado. Status: Para o plano (Etapas 6 e 7)

- O agendamento público exige telefone verificado por código (4.26.5), e o código vai pelo WhatsApp da barbearia (4.19.4).
- O perfil "sem WhatsApp conectado" faz parte da validação obrigatória (10.2).
- No onboarding, a página é publicada antes de conectar o WhatsApp (4.30), e a meta de ativação é o primeiro agendamento pela página em até 1 dia (8.11).

Do jeito que está escrito, uma barbearia sem WhatsApp conectado não recebe agendamento online. Opções:
- (a) enviar o código pelo número central do Verso, usando um modelo com o nome da barbearia. Isso esbarra na premissa de marca própria.
- (b) SMS, que traz custo e um provedor novo.
- (c) código por e-mail.
- (d) exigir WhatsApp conectado antes de publicar a página.

### P4. "Recepção" com dois sentidos. Status: Para o plano (Etapa 1)

"Recepção" é ao mesmo tempo o papel de fábrica do balcão (4.2.2) e a face da IA no WhatsApp (glossário 0.3). Na tela, os dois aparecem juntos (Equipe > papel "Recepção" e Verso IA > aba "Recepção"). Sugestão: manter "Recepção" para a IA e chamar o papel de "Balcão" ou "Recepcionista". A decisão é do Pedro.

### P5. Precedência de estados da assinatura incompleta. Status: Para o plano (Etapa 5)

A regra de empate da 4.13.2 (Cancelada > Suspensa > Em atraso > Pausada > Ativa) não cita Cancelamento agendado, Aguardando ativação nem Recusada. Vou propor a ordem completa no plano da Etapa 5.

### P6. Dependências entre etapas. Status: Para o plano

As dependências de algumas etapas estão incompletas:
- Etapa 11: os agentes do Gestor usam o Clube (Etapa 5), a Cadeira (Etapa 8) e Comissão/rateio (Etapa 10), mas ela declara depender só de 1, 6 e 9.
- Etapa 12: as ferramentas da Recepção usam clube, sinal e lista de espera (Etapas 5 e 8).

Como as etapas vão em sequência, isso não causa problema. Se alguma etapa for adiantada ou feita em paralelo, essas dependências precisam ser respeitadas.

### P7. Referências cruzadas trocadas no PRD. Status: Anotado

- 10.2, critério geral 4: "respeita os cliques de 8.3" deveria ser 8.13.
- 10.2, critério geral 4: "estado vazio conforme 8.4" deveria ser 8.7 e 8.11.

Considero as seções corretas.
