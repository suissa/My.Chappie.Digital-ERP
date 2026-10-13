# Inventory — Chief → Head

- Domínio: estoque físico, reservas, recebimentos, movimentações e reposição
- Chief Agent: `ChiefInventoryOfficer`
- Head Agent: `HeadOfInventory`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `stock.summary` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.kardex` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.count` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.loss` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.forecast` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.alerts` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.classify` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.expiry` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.shrinkage` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.cold_space` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.cycle_count_priorities` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.replenishment` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.receive` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.promotion` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.help` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.goods_receipt_3way_matched` — reutilizado do catálogo de intenções existente ou adaptado

## Topological 2flow

```2flow
ChiefInventoryOfficer.LoadStockPolicy :--: HeadOfInventory.ReadStockProjection :--: HeadOfInventory.CalculateAvailableToPromise :--: HeadOfInventory.ReturnStockSummary
```

```2flow
HeadOfInventory.ReceiveGoodsEvidence :--: HeadOfInventory.MatchPurchaseOrderAndInvoice :--: HeadOfInventory.RecordGoodsReceipt :--: HeadOfInventory.PublishStockMovement
```

```2flow
HeadOfInventory.RequestReservation :--: HeadOfInventory.CheckAvailability :--: HeadOfInventory.ReserveStock :--: HeadOfSales.ConfirmOrder
```

```2flow
HeadOfInventory.DetectCountVariance :--: HeadOfInventory.ClassifyVariance :--: InventoryAdjustmentApprovalGate :--: HeadOfInventory.CommitAdjustment
```

```2flow
HeadOfInventory.DetectReorderPoint :--: HeadOfInventory.ProposeReplenishment :--: ChiefInventoryOfficer.ReviewPolicyFit :--: HeadOfProcurement.CreatePurchaseRequest
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
execution HeadOfInventory.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfInventory.ValidateDelegation
  <<- HeadOfInventory.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
