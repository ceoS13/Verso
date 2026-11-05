# Matriz dos papéis de fábrica

**Aprovada pelo Pedro em 05/11/2026 (E9).** Os 5 pontos de validação do fim do arquivo estão aceitos.

Anexo do plano v1, entrega da Etapa 1. A matriz parte do resumo de cada papel na 4.2.2 e do catálogo base da 4.2.4 do PRD. Onde o PRD não define, a célula é proposta minha e está marcada com **(p)**.

**Como ler**

- **O**: vale em toda a organização (todas as unidades).
- **U**: vale só nas unidades atribuídas à pessoa. Numa barbearia de uma unidade, dá no mesmo que O.
- **P**: vale só nos registros próprios: atendimentos dela, clientes que ela atendeu ou dos quais é a barbeira preferida, lançamentos que ela fez.
- **—**: o papel não tem a permissão.
- Valores com % ou R$ são **limites**. Acima do limite, a ação vira pedido de aprovação (4.2.6).
- ⚠ marca uma **permissão perigosa** (4.2.3 item 3): aparece destacada e pede confirmação ao ser ligada num papel personalizado.

O papel Dono tem tudo em O e não pode ser editado. A coluna dele fica de fora para a tabela caber; nas linhas com limite, o Dono não tem limite.

Abreviações das colunas: Ger = Gerente, Rec = Recepção (ver pendência P4 sobre o nome), Barb = Barbeiro, Parc = Barbeiro parceiro, Fin = Financeiro.

## Regras de segurança da matriz (proposta, entram na Etapa 1)

1. **Sem escalada de privilégio.** Ninguém concede um papel, uma permissão, um escopo ou um limite maior do que os que tem. Só o Dono atribui o papel Dono.
2. **2FA obrigatório também em papéis personalizados.** Vale para todo papel que contenha uma permissão ⚠, não só para Dono e Gerente. Sem isso, bastaria copiar o Gerente para um papel novo e escapar do 2FA. O PRD só obriga Dono e Gerente; esta regra é uma extensão minha.
3. Uma pessoa com mais de uma atribuição recebe a união das permissões. O limite efetivo é o maior entre as atribuições ativas.

## Agenda

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| agenda.ver | U | U | P | P | — |
| agenda.criar | U | U | P | P | — |
| agenda.editar | U | U | P | P | — |
| agenda.cancelar | U | U | P (p) | P (p) | — |
| agenda.marcar_falta | U | U | P | P | — |
| agenda.corrigir_estado (com motivo) | U | — | — | — | — |
| agenda.encaixe_fora_grade | U | — (pede aprovação) | — | — | — |
| agenda.bloqueios.gerenciar | U | U (p) | P (p) | P (p) | — |
| agenda.recorrencia.gerenciar | U | U | P (p) | P (p) | — |

## Clientes

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| clientes.ver | O | O | P | P | — |
| clientes.ver_contato | O | O | — (p) | — (p) | — |
| clientes.criar | O | O | P (p) | P (p) | — |
| clientes.editar | O | O | P (p) | P (p) | — |
| clientes.mesclar | O (p) | — | — | — | — |
| ⚠ clientes.excluir | — | — | — | — | — |
| ⚠ clientes.exportar | — | — | — | — | — |
| clientes.importar | O (p) | — | — | — | — |
| clientes.ver_score | O | O | P | P | — |
| clientes.ver_financeiro | O | — (p) | — | — | O |
| clientes.pedidos_titular.atender | O (p) | — | — | — | — |

- Barbeiro sem `ver_contato` é escolha deliberada, apoiada na 4.2.5 item 1: ele fala com o cliente pelos botões do Verso, sem ver o número. Isso protege a base da barbearia quando um barbeiro sai.
- Exportar e excluir clientes ficam só com o Dono, como manda a 4.12.

## Ficha técnica

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| ficha.ver | O | — (p) | P | P | — |
| ficha.criar | O | — | P | P | — |
| ficha.fotos.ver | O | — | P | P | — |
| ficha.fotos.excluir | O | — | P (p) | P (p) | — |

## Catálogo

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| servicos.gerenciar | O | — | — | — | — |
| produtos.gerenciar | O | — | — | — | — |
| precos.editar | O | — | — | — | — |

## Caixa

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| caixa.abrir_fechar | U | U | — | — | — |
| caixa.vender | U | U | — ("só se liberado") | — | — |
| caixa.desconto.aplicar | U, até 20% (p) | U, até 10% (p) | — | — | — |
| ⚠ caixa.venda.cancelar | U | — (pede aprovação) | — | — | — |
| caixa.sangria_suprimento | U | U | — | — | — |
| caixa.relatorio.ver | U | U (p) | — | — | O (leitura) |

O PRD diz que o Barbeiro só tem caixa "se liberado". A proposta: o Dono libera criando um papel personalizado a partir do Barbeiro, com o assistente de cópia de 4.2.3. Não haveria um interruptor escondido dentro do papel de fábrica.

## Pacotes e estoque

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| pacotes.vender | U | U | — | — | — |
| pacotes.gerenciar_modelos | O | — | — | — | — |
| pacotes.ajustar_saldo | U | — | — | — | — |
| estoque.ver | U | U | — | — | O |
| estoque.movimentar | U | — (p) | — | — | — |
| estoque.ajustar | U | — | — | — | — |

## Financeiro e cobranças

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| financeiro.ver | O (leitura) | — | — | — | O |
| financeiro.lancar | — | — | — | — | O |
| financeiro.editar | — | — | — | — | O |
| ⚠ financeiro.exportar | — | — | — | — | O |
| financeiro.dre.ver | O | — | — | — | O |
| ⚠ financeiro.faturamento_total.ver | O | — | — | — | O |
| cobrancas.ver | O | U (p) | — | — | O |
| cobrancas.criar | O | U (p) | — | — | — |
| ⚠ cobrancas.estornar | — (R$ 0) | — | — | — | — |

## Comissões

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| comissoes.ver | O (todos) | — | P | P | O (todos) |
| comissoes.preparar | O | — | — | — | O |
| ⚠ comissoes.fechar | — (só por delegação, 4.2.7) | — | — | — | — |
| comissoes.marcar_pago | — | — | — | — | O (p) |
| vales.lancar | O, até R$ 200 (p) | — | — | — | — |

## Clube

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| clube.planos.gerenciar | — (p) | — | — | — | — |
| clube.assinaturas.ver | O | O | — | — | O |
| clube.assinaturas.vender **(nova, p)** | O | O | — | — | — |
| clube.assinaturas.operar | O | O | — | — | — |
| ⚠ clube.assinaturas.cancelar | O | — (p) | — | — | — |
| clube.excecao_regra.liberar | O | — | — | — | — |
| clube.saude.ver | O | — | — | — | O |
| clube.rateio.configurar | — | — | — | — | — |

- **`clube.assinaturas.vender` é nova.** A 4.2.2 diz que a Recepção vende planos, mas o catálogo da 4.2.4 não tem uma permissão para iniciar a adesão no balcão. Proponho incluir.
- Criar e mudar plano (preço e regras de uso) fica com o Dono, porque mexe na margem. O Gerente opera os assinantes.

## Retorno e campanhas

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| crm.segmentos.ver | O | — | — | — | — |
| campanhas.criar | O | — | — | — | — |
| ⚠ campanhas.enviar | O | — | — | — | — |
| campanhas.resultados.ver | O | — | — | — | — |

## Conversas

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| conversas.ver | O (todas) | O (todas) | — | — | — |
| conversas.responder | O | O | — | — | — |
| conversas.assumir | O | O | — | — | — |
| conversas.atribuir | O | O | — | — | — |
| conversas.bloquear_numero | O | — (p) | — | — | — |

## Verso IA

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| ia.gestor.usar | O | O | P | P | O |
| ia.configurar | — | — | — | — | — |
| ia.recepcao.ligar_desligar | O (p) | — | — | — | — |
| ia.creditos.comprar | — | — | — | — | — |
| ⚠ ia.chave_propria.gerenciar | — | — | — | — | — |

O Gestor age sempre com as permissões de quem pede (4.20.1 item 3). Ter `ia.gestor.usar` não dá acesso a nada que a pessoa não tenha no painel.

## Relatórios

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| relatorios.operacionais.ver | O | U (p) | P | P | — |
| relatorios.financeiros.ver | O | — | — | — | O |
| ⚠ relatorios.exportar | — (p) | — | — | — | O (só financeiros, p) |

**Pergunta para o Pedro:** a 4.11 diz que todos os relatórios são exportáveis em CSV, e a 4.12 diz que exportar fica só com o Dono. Proponho: exportar começa só no Dono e no Financeiro (financeiros), e o Dono libera para outros papéis se quiser. Ok?

## Equipe, configurações, integrações, plano e segurança

| Permissão | Ger | Rec | Barb | Parc | Fin |
| --- | --- | --- | --- | --- | --- |
| equipe.ver | O | O (nomes) | O (nomes) | — | O (nomes) |
| equipe.gerenciar | O (sem dar papel acima do próprio) | — | — | — | — |
| ⚠ permissoes.gerenciar | — | — | — | — | — |
| config.barbearia.editar | O | — | — | — | — |
| config.agenda.editar | O | — | — | — | — |
| config.comunicacao.editar | O | — | — | — | — |
| config.pagina.editar | O | — | — | — | — |
| ⚠ integracoes.gerenciar | — | — | — | — | — |
| conta.plano.ver | — | — | — | — | — |
| ⚠ conta.plano.alterar | — | — | — | — | — |
| conta.faturas.ver | — | — | — | — | O (p) |
| auditoria.ver | — (p) | — | — | — | — |
| ⚠ suporte.autorizar | — | — | — | — | — |
| sessoes.encerrar_de_outros | — (p) | — | — | — | — |

## O que eu preciso que você valide

1. Os limites padrão de desconto (Gerente 20%, Recepção 10%) e de vale (Gerente R$ 200).
2. O Barbeiro **não** ver o telefone do cliente por padrão.
3. A permissão nova `clube.assinaturas.vender`.
4. A regra de exportar relatórios (pergunta na seção Relatórios).
5. A extensão do 2FA obrigatório para qualquer papel com permissão perigosa.
