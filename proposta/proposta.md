# Proposta comercial — Marketplace de serviços locais

Oct 22, 2026 · @Wilham

## Resumo do app

Marketplace mobile que conecta quem precisa de um serviço a profissionais locais (encanadores, eletricistas, diaristas, instaladores de ar-condicionado e outros prestadores), em um único lugar, de forma simples, transparente e confiável.

- **Contratante:** encontra profissionais por categoria e localização, vê perfis, avaliações, serviços e preços, solicita orçamentos, agenda horários e acompanha todo o serviço pelo aplicativo.
- **Prestador:** cria seu perfil profissional e cadastra serviços com preço fixo, por hora ou sob orçamento. Configura requisitos e adicionais, define agenda, áreas de atendimento e taxas de deslocamento, e recebe e gerencia as solicitações dos clientes.

A solução tem quatro partes: o aplicativo mobile, um painel administrativo, uma API backend em NestJS e o armazenamento de imagens no Cloudflare R2.

## Ordem de serviço

Foi estimado inicialmente o valor de **R$ 5.000** de desenvolvimento para as funcionalidades descritas neste documento, cobrindo o aplicativo, o painel administrativo e a API backend.

| Entrega | O que inclui |
| --- | --- |
| Aplicativo mobile | Busca de profissionais por categoria e localização, perfis, avaliações, serviços e preços, solicitação de orçamento, agendamento e acompanhamento do serviço (lado contratante) e perfil profissional, cadastro de serviços (preço fixo, por hora ou sob orçamento), requisitos e adicionais, agenda, áreas de atendimento, taxas de deslocamento e gestão de solicitações (lado prestador) |
| Painel administrativo | Gestão da plataforma (usuários, prestadores, serviços e solicitações) |
| API backend | NestJS, base dos dois aplicativos e do painel |
| Imagens | Armazenamento no Cloudflare R2 |

O valor cobre mão de obra de desenvolvimento. Os custos para manter o aplicativo no ar estão na seção seguinte e não fazem parte desta estimativa.

Pagamentos, repasse ao prestador, devolução de valores, cadastro de prestadores, mapa e nota fiscal dependem das respostas da seção Perguntas e pontos em aberto. Se alguma resposta ampliar o escopo, o valor é reavaliado antes de seguir.

## Custos para manter o app (sem mão de obra)

O custo fixo de manutenção fica entre ≈ R$ 205 e ≈ R$ 1.070 por mês conforme o volume (simulação adiante), e as tarifas de pagamento variam por transação. Valores em dólar usam R$ 5,50 por US$ 1 como premissa.

| Item | Valor | Frequência | Observação |
| --- | --- | --- | --- |
| Domínio .com.br | R$ 40 | por ano | Registro.br; registradoras podem cobrar diferente |
| Domínio .com | ≈ US$ 10–12 (≈ R$ 55–65) | por ano | Cloudflare Registrar cobra o preço do registro, sem margem |
| Cloudflare R2 (imagens) | Grátis até 10 GB; depois US$ 0,015 por GB | por mês | Saída de dados gratuita. 50 GB ≈ R$ 3,30; 100 GB ≈ R$ 7,40 |
| Apple Developer | US$ 99 (≈ R$ 545) | por ano | Necessário para publicar no iOS |
| Google Play | US$ 25 (≈ R$ 140) | uma vez | Conta pessoal nova exige teste fechado com 12 testadores por 14 dias |
| Asaas: conta | Sem mensalidade nem adesão |  | Paga-se por cobrança recebida |
| Asaas: Pix e boleto | R$ 1,99 por cobrança recebida | por transação | R$ 0,99 nos 3 primeiros meses |
| Asaas: cartão à vista | R$ 0,49 + 2,99% | por transação | Parcelado: 3,49% (2 a 6x) e 3,99% (7 a 12x) |
| Asaas: antecipação do cartão | 1,25% ao mês (à vista) | quando usada | Sujeita a análise de crédito |
| Asaas: nota fiscal de serviço | R$ 0,49 por nota | por nota | Exige inscrição municipal e homologação na prefeitura (exceto MEI) |
| Asaas: subcontas e escrow | A confirmar |  | Subcontas podem ter taxa cobrada por dia aberta; o escrow tem taxa fixa mensal. Valores não publicados |
| Hospedagem da API e banco | Estimativa: R$ 150 a R$ 600 por mês | por mês | Estimativa nossa, depende do volume e do provedor |
| Mapas (se houver) | Cota gratuita de 10 mil carregamentos por mês | por mês | Geocoding: 10 mil gratuitos, depois US$ 5 por 1.000 |
| SMS ou WhatsApp de verificação, e-mail, monitoramento | A orçar |  | Dependem do uso |

## Modelos de precificação

A plataforma pode ganhar com uma mensalidade fixa do prestador (R$ 49 por mês, valor ilustrativo) ou com uma comissão de 10% sobre cada serviço, separada automaticamente por split no Asaas. Nos dois casos, as tarifas do Asaas ficam com o prestador.

| Critério | A) Mensalidade fixa | B) Comissão de 10% via split |
| --- | --- | --- |
| Como funciona | Prestador paga um valor fixo por mês para anunciar | 10% do valor líquido de cada serviço pago pelo app vai para a plataforma no momento do pagamento |
| Receita depende do volume de serviços? | Não | Sim, sobe e cai com o uso |
| Barreira de entrada do prestador | Alta: paga antes de vender | Baixa: só paga quando vende |
| Risco de o serviço ser combinado por fora | Menor, pois a receita não depende do pagamento no app | Maior: se o pagamento não passar pelo app, não há comissão |
| Apple e Google | A mensalidade de acesso pode ser tratada como serviço digital e exigir o billing das lojas (15% a 30%) se cobrada dentro do app | Sem comissão das lojas: o serviço é consumido fora do app |
| Tecnologia | Cobrança recorrente simples | API de split, subcontas para cada prestador e cadastro (KYC) no Asaas |
| Tarifas do Asaas | Uma cobrança por mensalidade (ex.: Pix R$ 1,99) | Descontadas antes da divisão; o percentual incide sobre o valor líquido |
| Ponto de equilíbrio | R$ 49 equivale a 10% de R$ 490 por mês em serviços | Cerca de 3 serviços de R$ 180 por prestador por mês |

Como o split do Asaas funciona:

- Só está disponível pela API e divide o valor entre contas Asaas; cada prestador precisa de uma subconta.
- O percentual é calculado sobre o valor líquido, depois das tarifas, então plataforma e prestador dividem a tarifa na proporção do split.
- Não há split abaixo de R$ 1, e o split não é indicado quando o repasse precisa ocorrer numa data futura ou depender de aprovação posterior (veja a pergunta sobre retenção).

É possível também combinar os dois modelos (mensalidade menor mais comissão menor).

## Simulação mensal

Com ticket médio de R$ 180 e comissão de 10%, a plataforma sobra ≈ R$ 1.645 por mês com R$ 20 mil em serviços contratados e ≈ R$ 18.430 com R$ 200 mil, antes de impostos, marketing e suporte.

Premissas:

- "Vendas na plataforma" é o valor total dos serviços pagos pelo app por mês.
- Ticket médio de R$ 180, estimado a partir de serviços pequenos (R$ 120 a R$ 250) puxados para cima por instalações de ar-condicionado.
- Cada prestador faz 5 serviços por mês pela plataforma (a faixa de 3 a 8 aparece na tabela).
- Mix de pagamento: 60% Pix e 40% cartão à vista, o que dá tarifa média de ≈ R$ 3,54 por serviço no Asaas.
- Comissão de 10% sobre o valor líquido, com a tarifa do Asaas paga pelo prestador na proporção do split.
- Uma nota fiscal por mês por prestador, referente à comissão da plataforma.

|  | R$ 5 mil | R$ 20 mil | R$ 100 mil | R$ 200 mil |
| --- | --- | --- | --- | --- |
| Serviços por mês (ticket R$ 180) | 28 | 111 | 556 | 1.111 |
| Prestadores ativos (5 serviços cada) | 6 | 22 | 111 | 222 |
| Faixa de prestadores (8 a 3 serviços cada) | 4 a 9 | 14 a 37 | 69 a 185 | 139 a 370 |
| Tarifas do Asaas somadas | R$ 98 | R$ 394 | R$ 1.968 | R$ 3.936 |
| **Comissão líquida da plataforma (10%)** | **R$ 490** | **R$ 1.961** | **R$ 9.803** | **R$ 19.606** |
| Nota fiscal da comissão | R$ 3 | R$ 11 | R$ 54 | R$ 109 |
| Custos fixos estimados | ≈ R$ 205 | ≈ R$ 305 | ≈ R$ 660 | ≈ R$ 1.070 |
| **Sobra mensal** | **≈ R$ 280** | **≈ R$ 1.645** | **≈ R$ 9.090** | **≈ R$ 18.430** |
| Para comparar: mensalidade de R$ 49 por prestador | R$ 294 | R$ 1.078 | R$ 5.439 | R$ 10.878 |

Os custos fixos somam domínios .com.br e .com (≈ R$ 9 por mês), Apple Developer (≈ R$ 45 por mês), hospedagem da API e banco (estimativa de R$ 150, R$ 250, R$ 600 e R$ 1.000, conforme o cenário) e Cloudflare R2 (de zero a ≈ R$ 15). O Google Play é uma taxa única de ≈ R$ 140. Ficam fora SMS ou WhatsApp, e-mail, monitoramento, impostos, marketing, suporte e a taxa de subcontas e escrow do Asaas.

O ticket médio muda a quantidade de prestadores: no cenário de R$ 20 mil, um ticket de R$ 120 pede ≈ 167 serviços (33 prestadores) e um ticket de R$ 250 pede 80 serviços (16 prestadores). Com a sobra acima, o investimento de R$ 5.000 em desenvolvimento se paga em ≈ 3 meses no cenário de R$ 20 mil e em ≈ 18 meses no de R$ 5 mil, sem contar marketing.

## Apple e Google

Apple e Google não cobram comissão sobre o serviço contratado pelo app; o ponto de atenção é a mensalidade do prestador, se for cobrada dentro do app.

- **Apple:** a regra 3.1.3(e) das diretrizes manda usar meio de pagamento que não seja a compra no app (cartão, Pix, Apple Pay) para serviços físicos consumidos fora do app. Sem compra no app, não há comissão. Custo fixo: US$ 99 por ano.
- **Google Play:** a política de pagamentos lista serviços físicos, como limpeza e transporte, entre os casos em que o billing do Google Play não é exigido. Custo fixo: US$ 25 uma única vez.
- **Mensalidade do prestador:** o Google exige o billing próprio para assinaturas e "service upgrades", e há caso público de app para freelancers rejeitado pela Apple por oferecer serviço digital pago sem compra no app. Cobrar a mensalidade fora do app (painel web) é a saída comum, mas as regras de direcionamento da Apple devem ser validadas antes de fechar esse modelo.

A revisão da Apple tem componente subjetivo, então isso é uma leitura das regras publicadas, não uma garantia de aprovação.

## Perguntas e pontos em aberto

Oito pontos aguardam resposta do cliente e um já está decidido: a tarifa do Asaas é paga pelo prestador. As respostas podem mudar o escopo e o valor da ordem de serviço.

| # | Pergunta | Opções e impacto | Status |
| --- | --- | --- | --- |
| 1 | O dinheiro será repassado ao prestador logo após o pagamento ou aguardamos o cliente dar o ok para liberar? | **Repasse imediato:** split automático no pagamento, mais simples, mas sem o valor retido em caso de disputa. **Aguardar o ok:** protege o cliente; exige conta escrow (taxa fixa mensal no Asaas) ou transferência posterior, pois o split não é indicado para repasse futuro, e convém definir um prazo para liberação automática | Em aberto |
| 2 | Como funciona a devolução do valor ao cliente: a plataforma faz o estorno por conta própria e assume essa burocracia? | **Plataforma assume:** mais confiança do cliente; exige tela de disputas no painel admin e alguém para analisar cada pedido. **Cliente e prestador resolvem entre si:** menos trabalho, mais risco de reclamação contra a plataforma. Com repasse imediato, o estorno depende de o prestador ter saldo na subconta (a confirmar com o Asaas) | Em aberto |
| 3 | Qualquer pessoa pode se cadastrar como prestador ou ele solicita permissão e passa por revisão manual da equipe? | **Cadastro livre:** entrada rápida, mais risco de perfis falsos ou de baixa qualidade. **Revisão manual:** mais qualidade e segurança; exige tela de aprovação no painel admin e alguém para revisar, além do cadastro de cada prestador no Asaas (subconta) | Em aberto |
| 4 | O pagamento é sempre feito pelo app ou o cliente pode pagar direto ao prestador? | **Obrigatório no app:** garante a comissão e o fluxo de avaliação depois do serviço. **Opcional:** sem comissão garantida, pois o serviço pode ser combinado por fora | Em aberto |
| 5 | Vocês querem que o sistema controle a nota fiscal de alguma forma? | **Nível 0:** só a nota da comissão da plataforma. **Nível 1:** o prestador emite por conta própria e anexa a nota ao pedido. **Nível 2:** emissão automática pelo Asaas, que exige as credenciais do prestador na prefeitura (token, login ou certificado digital), com responsabilidade da plataforma sobre esses dados e mais burocracia no cadastro. MEI emite pelo Portal Nacional | Em aberto |
| 6 | Mostrar um mapa ou apenas sugerir os profissionais mais próximos, sem visualizar? | **Sem mapa:** distância calculada no backend, custo de API próximo de zero. **Com mapa:** cota gratuita de 10 mil carregamentos por mês, mais horas de desenvolvimento | Em aberto |
| 7 | Qual modelo de cobrança: mensalidade fixa, comissão de 10% ou combinação dos dois? | Comparativo na seção Modelos de precificação | Em aberto |
| 8 | Como verificar o telefone dos usuários: SMS, WhatsApp ou só e-mail? | Custo variável por usuário, a orçar | Em aberto |
| 9 | Quem paga a tarifa do Asaas? | O prestador, via split sobre o valor líquido | Decidido |

## Premissas e referências

Os valores deste documento são estimativas de 22/10/2026 e devem ser reconfirmados nas páginas oficiais antes do fechamento.

- Dólar a R$ 5,50; a variação altera domínio .com, Apple, Google Play e Cloudflare R2.
- Tarifas do Asaas na tabela padrão; a promoção dos 3 primeiros meses não entra nas projeções.
- Hospedagem da API e banco, e o ticket médio de diarista (R$ 120 a R$ 200), são estimativas nossas, sem fonte.
- Impostos sobre a receita, marketing, suporte e a taxa de subcontas e escrow do Asaas não estão incluídos.
- Nota fiscal e enquadramento tributário da comissão devem ser confirmados com um contador. Este documento não é consultoria jurídica, contábil ou financeira.

Referências (resultados de busca consultados em 22/10/2026):

- [Registro.br: preço do .com.br](https://www.homehost.com.br/blog/?p=15114)
- [Cloudflare Registrar](https://www.cloudflare.com/id-id/products/registrar)
- [Cloudflare R2: preços](https://filebase.com/blog/cloudflare-r2-pricing-costs-savings-and-alternatives-in-2026/)
- [Preços e taxas do Asaas](https://www.asaas.com/precos-e-taxas)
- [Asaas: tarifas públicas por cobrança](https://quasa.io/pt/media/asaas-ou-iugu-tarifa-publicada-vence-ate-o-volume-pedir-proposta)
- [Asaas: FAQ de split](https://docs.asaas.com/docs/duvidas-frequentes-split)
- [Asaas: split e valor líquido](https://docs.asaas.com/docs/split-asaas-no-pluga)
- [Asaas: criação de subcontas](https://docs.asaas.com/docs/criacao-de-subcontas)
- [Asaas: Termos e Condições (escrow e subcontas)](https://central.ajuda.asaas.com/hc/pt-br/articles/32096847160859-Termos-e-Condi%C3%A7%C3%B5es-de-Uso)
- [Asaas: emissão de nota fiscal](https://www.asaas.com/nota-fiscal)
- [Asaas: informações fiscais (credenciais da prefeitura)](https://docs.asaas.com/reference/criar-e-atualizar-informacoes-fiscais)
- [Apple: serviços entre pessoas e comissão (fórum de desenvolvedores)](https://developer.apple.com/forums/thread/723322)
- [Apple: app de freelancers rejeitado (fórum de desenvolvedores)](https://developer.apple.com/forums/thread/685869)
- [Política de pagamentos do Google Play](https://support.google.com/googleplay/android-developer/answer/9858738?hl=en)
- [Custo de publicar app (Choicely)](https://www.choicely.com/tutorials/how-much-does-it-cost-to-publish-an-app)
- [Google Maps Platform: preços](https://developers.google.com/maps/billing-and-pricing/pricing?hl=pt)
- [Eletricista: preços 2026](https://larpontual.com.br/portal/quanto-cobra-um-eletricista)
- [Encanador: preços 2026](https://larpontual.com.br/portal/quanto-custa-um-encanador)
- [Chuveiro elétrico: preços 2026](https://catracalivre.com.br/noticias/quanto-custa-instalar-um-chuveiro-eletrico-qual-o-preco-de-um-eletricista-para-fazer-o-servico-em-2026/)
- [Ar-condicionado: preços](https://revistaoeste.com.br/oestegeral/?p=45552)
