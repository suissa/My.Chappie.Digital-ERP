# My.Chappie.Digital-ERP

## Delegação de responsabilidades: Chief Agents → Head Agents

Este documento define como o Human configura os agentes executivos (Chief Agents) e como cada um delega políticas, objetivos, limites de autoridade e critérios de sucesso aos agentes funcionais (Head Agents).

A configuração é declarativa: o Human define o resultado desejado, as políticas do negócio e os limites de decisão. O Chief interpreta essa configuração dentro do seu domínio, transforma-a em responsabilidades funcionais verificáveis e delega a execução aos Heads. Os Heads coordenam os workflows do módulo e os agentes especializados que realizam operações concretas.

**Princípio:** cargo define autoridade; função define responsabilidade; agente executa capacidades. A delegação não concede permissões técnicas que o agente não possua, nem substitui validações, contratos, controles de acesso ou aprovações humanas obrigatórias.

## 1. Modelo de responsabilidade

- **Human / Business Owner:** define objetivos, políticas, prioridades, restrições, orçamento, tolerância a risco e aprovações exigidas. Mantém autoridade sobre decisões reservadas a pessoas.
- **Chief Agent:** agente executivo de um domínio. Converte a configuração humana em políticas e objetivos do domínio, define limites e indicadores, resolve prioridades dentro da sua autoridade e delega responsabilidades aos Heads.
- **Head Agent:** agente responsável por um módulo funcional. Converte a delegação do Chief em planos operacionais, workflows, contratos de entrada/saída, regras de validação, monitoramento e encaminhamento de exceções.
- **Specialist / Action Agent:** executa uma capacidade delimitada, como criar pedido de compra, registrar recebimento, reservar estoque, emitir cobrança ou preparar campanha.
- **System of Record:** fonte autoritativa dos dados de cada domínio. Agentes não devem contornar APIs, contratos, validações ou políticas de escrita do sistema responsável pelos dados.

Chiefs e Heads são ambos Agents, mas têm escopos distintos. Não é obrigatório que cada título corresponda a um serviço ou deployment separado. A topologia pode evoluir sem alterar o significado dos contratos.

## 2. Mapa de delegação

| Chief Agent | Head Agent(s) | Delegação principal |
|---|---|---|
| ChiefOperatingOfficer | HeadOfOperations | Coordenação operacional, políticas transversais, capacidade e dependências entre módulos |
| ChiefProcurementOfficer | HeadOfProcurement | Estratégia de compras, cotações, pedidos e condições comerciais |
| ChiefSupplierOfficer (opcional) | HeadOfSupplierManagement | Cadastro, qualificação, documentação, risco e relacionamento com fornecedores |
| ChiefInventoryOfficer | HeadOfInventory | Disponibilidade, níveis de estoque, reservas, reposição, ajustes e inventário |
| ChiefProductOfficer | HeadOfProductCatalog | Catálogo, atributos, unidades, categorias, variantes e ciclo de vida de produtos |
| ChiefSalesOfficer | HeadOfSales | Política comercial, metas, pedidos, descontos dentro de alçadas e conversão |
| ChiefCustomerOfficer | HeadOfCustomerManagement | Cadastro, qualidade de dados, relacionamento, atendimento, retenção e experiência |
| ChiefMarketingOfficer | HeadOfMarketing | Segmentação, campanhas, conteúdo, canais, orçamento e medição de resultados |
| ChiefFinancialOfficer | HeadOfFinance | Caixa, contas a pagar/receber, orçamento, cobrança, pagamentos e planejamento financeiro |
| ChiefAccountingOfficer | HeadOfAccounting | Plano de contas, lançamentos, conciliações, fechamento, relatórios e controles contábeis |
| ChiefFulfillmentOfficer (opcional) | HeadOfFulfillment | Separação, expedição, entrega, devoluções logísticas e prova de cumprimento |
| ChiefPeopleOfficer (opcional) | HeadOfPeopleOperations | Funções, escalas e políticas de equipe, se houver módulo de pessoas |

Os módulos opcionais só devem ser ativados se o negócio tiver esse domínio. Sem um Chief separado para fornecedores, HeadOfSupplierManagement pode responder ao ChiefProcurementOfficer. Se logística estiver sob operações, HeadOfFulfillment pode responder ao ChiefOperatingOfficer.

Esses títulos representam papéis do modelo; não significam que toda empresa precise contratar executivos C-suite. O Human pode configurar Chiefs como agentes virtuais sem criar cargos humanos equivalentes.

## 3. Responsabilidades por delegação

### 3.1 ChiefOperatingOfficer → HeadOfOperations

**O Chief define:** prioridades operacionais globais; objetivos de nível de serviço; limites de capacidade; política de escalonamento; regras para conflitos entre módulos; requisitos de continuidade e recuperação.

**O Head implementa:** coordenação de workflows intermodulares; acompanhamento de estados; detecção de bloqueios; gestão de filas e dependências; escalonamento de falhas e apresentação de evidências operacionais.

**Limites:** não alterar políticas específicas de outros domínios por conta própria, ignorar aprovações ou modificar dados autoritativos fora dos contratos permitidos.

### 3.2 ChiefProcurementOfficer → HeadOfProcurement

**O Chief define:** estratégia de aquisição; categorias prioritárias; critérios de cotação; orçamento e alçadas; regras de negociação; quantidade de propostas exigidas; critérios de seleção; metas de custo, prazo e qualidade.

**O Head implementa:** solicitação de compra; coleta e comparação de cotações; recomendação de fornecedor; criação e acompanhamento de pedidos; validação de quantidades, preços, prazos e condições; encaminhamento para aprovação; comunicação do pedido aprovado.

**Agentes especialistas:** PurchaseRequestAgent, QuotationAgent e PurchaseOrderAgent.

**Limites:** não aprovar a própria exceção; não ultrapassar orçamento ou alçada; não marcar mercadoria como recebida sem evidência; não alterar dados bancários de fornecedor sem o procedimento de verificação previsto.

### 3.3 ChiefSupplierOfficer → HeadOfSupplierManagement

**O Chief define:** critérios de qualificação; documentação obrigatória; classificação de risco; requisitos de conformidade; regras de avaliação e suspensão.

**O Head implementa:** cadastro e atualização de fornecedores; verificação de duplicidade; coleta de documentos; avaliação de desempenho; monitoramento de vencimentos; registro de ocorrências e recomendação de bloqueio ou reativação.

**Limites:** alterações de dados sensíveis, liberação de fornecedor bloqueado e exceções de conformidade exigem as aprovações definidas pelo Human. Se esse Chief não existir, a responsabilidade fica sob Procurement.

### 3.4 ChiefInventoryOfficer → HeadOfInventory

**O Chief define:** estoque mínimo/máximo; ponto de reposição; estoque de segurança; regras de reserva; tolerância a divergências; prioridade entre pedidos; frequência e materialidade de inventários.

**O Head implementa:** consulta de disponibilidade; reserva e liberação; recebimento registrado; movimentações; lotes e validade quando aplicável; contagens; reconciliação de divergências; sugestões de reposição e alertas de ruptura ou excesso.

**Agentes especialistas:** StockReservationAgent, GoodsReceiptAgent, StockMovementAgent, StockReconciliationAgent e ReplenishmentAgent.

**Limites:** estoque físico, reservado, disponível e em trânsito são estados distintos; não confirmar recebimento ou ajuste material sem evidência e autorização exigidas.

### 3.5 ChiefProductOfficer → HeadOfProductCatalog

**O Chief define:** estrutura do catálogo; taxonomia; campos obrigatórios; política de SKU; unidades de medida; regras de publicação; qualidade mínima dos dados e critérios para descontinuação.

**O Head implementa:** criação e atualização de produtos; categorias e variantes; validação de atributos; associação com fornecedores; regras de preço de referência; publicação e descontinuação; detecção de duplicatas.

**Limites:** mudanças em produtos publicados que afetem preço, tributação, estoque ou pedidos devem produzir eventos e acionar os módulos afetados por seus contratos.

### 3.6 ChiefSalesOfficer → HeadOfSales

**O Chief define:** metas de venda; segmentos e canais; política de preço e desconto; alçadas comerciais; condições de pagamento permitidas; regras de qualificação; objetivos de conversão e margem.

**O Head implementa:** oportunidades e propostas; cálculo de preço; validação de descontos; conversão de proposta em pedido; acompanhamento do ciclo de venda; comunicação de status e coordenação com estoque e financeiro.

**Agentes especialistas:** SalesOpportunityAgent, SalesOrderAgent e PricingAgent. Um agente de cotações pode ser compartilhado por contrato entre compras e vendas, mantendo políticas e dados separados.

**Limites:** não prometer estoque indisponível; não conceder desconto acima da alçada; não considerar venda concluída apenas porque uma mensagem foi enviada; respeitar crédito, pagamento e aprovação quando aplicáveis.

### 3.7 ChiefCustomerOfficer → HeadOfCustomerManagement

**O Chief define:** política de relacionamento; qualidade e minimização de dados; segmentação operacional permitida; metas de satisfação e retenção; regras de atendimento, consentimento e escalonamento.

**O Head implementa:** cadastro e deduplicação; atualização de dados; histórico de relacionamento; solicitações e reclamações; preferências e consentimentos; encaminhamento para vendas, marketing ou suporte.

**Limites:** aplicar finalidade e acesso mínimo aos dados pessoais; respeitar consentimento, retenção e solicitações de titulares; não inferir identidade apenas a partir de transcrição ou conteúdo de mensagem.

### 3.8 ChiefMarketingOfficer → HeadOfMarketing

**O Chief define:** objetivos de campanha; públicos permitidos; posicionamento; orçamento; canais; calendário; limites de frequência; métricas e regras de aprovação de conteúdo.

**O Head implementa:** planejamento e execução de campanhas; segmentação autorizada; preparação de conteúdo; agendamento; testes; acompanhamento de entrega, conversão e custo; relatórios e interrupção de campanhas que violem limites.

**Limites:** não enviar comunicações sem base legal/consentimento exigido; respeitar opt-out, orçamento, políticas de canal e aprovações de conteúdo. Engajamento não é, por si só, prova de resultado comercial.

### 3.9 ChiefFinancialOfficer → HeadOfFinance

**O Chief define:** orçamento; política de caixa; limites de pagamento; prazos de cobrança; critérios de crédito; tolerância a inadimplência; alçadas de aprovação e metas de liquidez.

**O Head implementa:** contas a pagar e receber; previsões de caixa; cobrança; conciliação financeira; preparação de pagamentos; controle de vencimentos; relatórios de exposição e encaminhamento de divergências.

**Limites:** preparar uma transação não equivale a autorizá-la ou liquidá-la. Pagamentos, reembolsos, mudanças de beneficiário e exceções devem cumprir segregação de funções, verificação e aprovação aplicáveis.

### 3.10 ChiefAccountingOfficer → HeadOfAccounting

**O Chief define:** política contábil; plano de contas; regras de classificação; calendário de fechamento; materialidade; critérios de conciliação; controles e requisitos de auditoria.

**O Head implementa:** classificação e validação de lançamentos; conciliação; provisões; fechamento periódico; trilha de auditoria; relatórios e identificação de inconsistências para revisão.

**Limites:** preservar rastreabilidade e períodos fechados; não apagar ou reescrever silenciosamente lançamentos confirmados; corrigir por estorno ou ajuste autorizado; separar preparação, aprovação e contabilização conforme política.

### 3.11 ChiefFulfillmentOfficer → HeadOfFulfillment

**O Chief define:** promessas de prazo; regras de separação e expedição; seleção de transportadora; custo máximo; tratamento de falhas; devoluções e evidências de entrega.

**O Head implementa:** picking e packing; geração de expedição; coordenação de transporte; rastreamento; atualização de status com evidências; tratamento de entrega frustrada e devolução.

**Limites:** não declarar entrega concluída sem evidência válida; não alterar a promessa ao cliente sem atualizar os sistemas e comunicar os envolvidos. Se não houver domínio logístico separado, delegar a Operations.

### 3.12 ChiefPeopleOfficer → HeadOfPeopleOperations

**O Chief define:** estrutura de funções; políticas de equipe; requisitos de treinamento; escalas; limites de acesso e regras de aprovação relacionados a pessoas.

**O Head implementa:** atribuição de funções e escalas; verificação de requisitos; registro de treinamento; encaminhamento de solicitações e atualização dos sistemas autorizados.

**Limites:** aplicar acesso mínimo; não conceder privilégios incompatíveis com a função; encaminhar decisões trabalhistas, disciplinares ou sensíveis para revisão humana quando exigido. Este módulo é opcional.

## 4. Configuração definida pelo Human

Cada Chief recebe uma configuração explícita, versionada e validada. O contrato pode ser representado em JSON, YAML ou Semantic-as-Code.

Exemplo ilustrativo para Procurement:

```yaml
agent:
  id: ChiefProcurementOfficer
  domain: procurement
  enabled: true

human_configuration:
  purpose: "Garantir aquisições adequadas ao custo, prazo e qualidade definidos."
  desired_outcomes:
    - id: purchase_readiness
      description: "Pedidos aprovados com fornecedor, itens, preço e prazo válidos."
      acceptance_evidence:
        - approved_purchase_order
        - supplier_confirmation
  policies:
    budget_limit: null
    allowed_supplier_statuses: ["qualified"]
    quotation_policy:
      minimum_quotes: 3
      exceptions_require_human_approval: true
  priorities:
    - compliance
    - total_cost
    - delivery_reliability
    - lead_time
  authority:
    may_recommend_supplier: true
    may_create_draft_purchase_order: true
    may_approve_purchase_order: false
    may_commit_external_purchase: false
  approval_rules:
    - condition: "budget_limit_exceeded"
      approver: human
    - condition: "supplier_not_qualified"
      approver: human
  constraints:
    prohibited_actions:
      - bypass_supplier_validation
      - bypass_purchase_approval
  reporting:
    metrics:
      - purchase_cycle_time
      - supplier_on_time_rate
      - purchase_price_variance

delegation:
  target_head: HeadOfProcurement
  responsibility:
    - manage_purchase_requests
    - collect_and_compare_quotations
    - prepare_purchase_orders
    - track_supplier_confirmations
  execution_contract:
    input_schema: PurchaseRequest
    output_schema: ProcurementResult
    required_evidence:
      - supplier_qualification
      - quotation_comparison
      - approval_status
  escalation:
    on_policy_conflict: human
    on_missing_evidence: request_evidence
    on_unavailable_supplier: propose_alternatives
    on_uncertain_authority: stop_and_escalate
```

Os valores são exemplos, não políticas predefinidas. O Human precisa preencher ou aceitar explicitamente valores que afetem dinheiro, permissões, conformidade e compromissos externos. Um valor ausente deve seguir um default seguro e explícito; nunca significa autorização ilimitada.

### Campos mínimos por Chief

1. **Identidade e domínio:** ID estável, domínio, versão e estado ativo/inativo.
2. **Propósito e resultados:** objetivos de negócio e evidências observáveis de aceitação.
3. **Políticas:** regras, restrições, prioridades e exceções.
4. **Autoridade:** ações permitidas, proibidas e condicionais, com limites explícitos.
5. **Orçamento e risco:** limites numéricos, moeda, período e condições de aprovação, quando aplicável.
6. **Delegação:** Heads autorizados, responsabilidades, interfaces e resultados esperados.
7. **Escalonamento:** quando parar, pedir informação, propor alternativas ou solicitar aprovação humana.
8. **Observabilidade:** métricas, eventos, auditoria e retenção de evidências.
9. **Privacidade e segurança:** dados acessíveis, finalidade, minimização e políticas de acesso.
10. **Critérios de revisão:** condições para reavaliar configuração, política ou delegação.

## 5. Contrato de execução do Head Agent

Cada Head recebe uma delegação estruturada, e não apenas uma instrução em linguagem natural. O contrato contém:

- **Responsabilidade:** resultado pelo qual o Head responde.
- **Escopo:** processos, entidades e capacidades que pode coordenar.
- **Entradas e pré-condições:** dados obrigatórios, origem confiável e validações.
- **Saídas:** resultado tipado, estado final, referências aos registros e evidências.
- **Políticas herdadas:** regras do Chief que o Head não pode enfraquecer.
- **Alçada local:** decisões que o Head pode tomar sem nova aprovação.
- **Ações delegáveis:** especialistas e Actions permitidos e suas interfaces.
- **Invariantes:** condições que precisam continuar verdadeiras durante e após a execução.
- **Falhas e compensações:** retry seguro, idempotência, cancelamento, reconciliação e recuperação.
- **Escalonamento:** condições que interrompem a execução e exigem decisão superior.
- **Observabilidade:** eventos de progresso, resultado, erro e auditoria sem expor dados sensíveis desnecessários.

Exemplo resumido:

```yaml
delegation:
  from: ChiefInventoryOfficer
  to: HeadOfInventory
  version: 1
  objective: "Manter quantidades e disponibilidade de estoque confiáveis."
  allowed_capabilities:
    - read_stock
    - reserve_stock
    - register_receipt_with_evidence
    - propose_stock_adjustment
  prohibited_capabilities:
    - confirm_receipt_without_evidence
    - approve_material_adjustment
  invariants:
    - "available_quantity = on_hand_quantity - reserved_quantity"
    - "Every confirmed movement has an audit reference."
  escalation:
    - condition: "physical_count_variance_exceeds_threshold"
      target: human
  acceptance:
    require:
      - persisted_result
      - inventory_movement_reference
      - audit_evidence
```

As fórmulas são ilustrativas e devem ser adaptadas a estoque em trânsito, bloqueado, comprometido, lotes, unidades e outras regras reais do domínio.

## 6. Ciclo de delegação

1. **Configure:** o Human define e versiona o perfil do Chief.
2. **Validate:** o sistema valida esquema, consistência, permissões e políticas obrigatórias.
3. **Compile delegation:** o Chief produz uma delegação explícita para cada Head, referenciando a versão da configuração que a originou.
4. **Review:** delegações que alterem alçadas, compromissos financeiros, dados sensíveis ou ações externas passam pelas aprovações exigidas.
5. **Execute:** o Head orquestra workflows e delega capacidades delimitadas aos agentes especialistas.
6. **Verify:** o sistema verifica pós-condições, persistência e evidências; uma resposta textual do agente, sozinha, não comprova sucesso.
7. **Report:** o Head devolve estado, resultado, evidências, exceções e métricas ao Chief.
8. **Escalate or learn:** conflitos, incerteza relevante, ausência de evidência e falhas não recuperáveis são escalonados. Mudanças de política são propostas e versionadas, não aplicadas silenciosamente.

## 7. Estados de execução recomendados

- **PROPOSED:** delegação criada, ainda não aprovada quando a aprovação é necessária.
- **VALIDATED:** esquema e políticas validados.
- **AUTHORIZED:** alçada e aprovações verificadas.
- **RUNNING:** execução em andamento.
- **COMPLETED:** pós-condições verificadas com evidência.
- **BLOCKED:** falta informação, permissão ou dependência.
- **ESCALATED:** decisão encaminhada ao Human ou autoridade superior.
- **FAILED:** execução falhou e não há recuperação automática segura.
- **COMPENSATING:** uma operação distribuída está sendo compensada.
- **CANCELLED:** cancelamento confirmado, respeitando efeitos já realizados.

Não confundir COMPLETED com “a ferramenta respondeu com sucesso”. A conclusão exige evidência do estado persistido e, para ações externas, confirmação adequada do resultado externo.

## 8. Regras transversais de segurança e governança

- **Least privilege:** cada Chief, Head e Specialist recebe apenas as capacidades necessárias ao seu escopo.
- **Segregation of duties:** quem prepara uma operação de alto impacto não deve automaticamente aprová-la e executá-la sem controles.
- **Policy inheritance:** Heads podem restringir uma política herdada, mas não enfraquecê-la.
- **Human approval:** a aprovação deve ser autenticada e vinculada à ação, aos parâmetros, ao limite e à versão da política. Uma mensagem ambígua não basta.
- **Fail closed:** se autoridade, identidade, dados essenciais ou evidências forem incertos, não realizar a ação irreversível; solicitar esclarecimento ou escalar.
- **Idempotency:** ações repetíveis usam chaves de idempotência e verificam o estado antes de repetir efeitos externos.
- **Auditability:** registrar ator, intenção, versão da configuração, delegação, decisão, ação, resultado e referências de evidência, evitando dados pessoais ou segredos em plaintext na telemetria.
- **Domain ownership:** cada módulo mantém autoridade sobre seus dados. Outros módulos solicitam mudanças por contratos/eventos, sem escrita direta não autorizada.
- **Policy versioning:** toda execução registra as versões do perfil do Chief, da delegação do Head e dos contratos relevantes.
- **No implicit authority:** ausência de regra, limite ou aprovação não equivale a permissão.

## 9. Resultado esperado

O Human não precisa programar cada workflow manualmente. Define resultados desejados, políticas, restrições, autoridade e critérios de aceitação de cada Chief. Cada Chief transforma essas definições em delegações verificáveis; cada Head traduz a delegação em workflows, contratos e execução observável; os agentes especialistas executam capacidades pequenas e delimitadas.

A arquitetura permite alterar políticas e prioridades sem misturar autoridade executiva com implementação operacional. O comportamento executável continua limitado por contratos tipados, permissões reais, validação determinística, evidências e aprovações explícitas.
