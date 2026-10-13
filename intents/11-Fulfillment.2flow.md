# Fulfillment — Chief → Head

- Domínio: separação, expedição, entrega, rastreamento, devoluções e prova de entrega
- Chief Agent: `ChiefFulfillmentOfficer`
- Head Agent: `HeadOfFulfillment`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `delivery.carrier_profile` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.zone_pricing` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.request` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.fair_dispatch` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.route_batch` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.live_tracking` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.proof_of_delivery` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.instant_settlement` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.carrier_console` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.exception` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.eta_forecast` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.fair_rotation` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.audit_trail` — reutilizado do catálogo de intenções existente ou adaptado
- `delivery.automation` — reutilizado do catálogo de intenções existente ou adaptado
- `shopper.order_tracking_status` — reutilizado do catálogo de intenções existente ou adaptado
- `fulfillment.pick_pack` — NOVO: contrato/handler necessário
- `fulfillment.return.process` — NOVO: contrato/handler necessário

## Topological 2flow

```2flow
ChiefFulfillmentOfficer.LoadFulfillmentPolicy :--: HeadOfFulfillment.ValidateOrderAndAddress :--: HeadOfFulfillment.CheckStockAndServiceArea :--: HeadOfFulfillment.PlanPickPackDispatch
```

```2flow
HeadOfFulfillment.RequestDispatch :--: HeadOfFulfillment.SelectEligibleCarrier :--: HeadOfFulfillment.AssignFairQueue :--: HeadOfFulfillment.PublishTracking
```

```2flow
HeadOfFulfillment.ReceiveProofOfDelivery :--: HeadOfFulfillment.ValidateDeliveryEvidence :--: HeadOfFinance.CalculateAuthorizedSettlement :--: HeadOfFulfillment.CloseFulfillment
```

```2flow
HeadOfFulfillment.DetectLostOrDelayedOrder :--: HeadOfFulfillment.StartExceptionWorkflow :--: ChiefFulfillmentOfficer.ResolveSLAConflict :--: HeadOfFulfillment.NotifyCustomer
```

## Governança e contrato de execução

- Entrada: intent classificado, ator solicitante, contexto/tenant, payload validado, correlação e versão da política.
- Pré-condições: autorização técnica, delegação ativa, dados consultados no System of Record e idempotency key para comandos mutáveis.
- Saída: `Ok<DomainOutcome>` ou `Error<DomainError>`, evidências, estado final e referências de eventos emitidos.
- Ausência de consentimento, alçada insuficiente, divergência, orçamento excedido ou evidência ausente bloqueia a execução.
- Escalonamento: Head → Chief em conflito de política; Chief → Human para decisões reservadas e exceções materiais.
- Toda mutação deve ser idempotente e auditável, e o sucesso só pode ser comunicado após confirmação autoritativa.

## Execution 2flow — contrato de referência

```2flow
execution HeadOfFulfillment.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfFulfillment.ValidateDelegation
  <<- HeadOfFulfillment.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
