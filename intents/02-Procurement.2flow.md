# Procurement — Chief → Head

- Domínio: requisições, cotações, seleção e pedidos de compra
- Chief Agent: `ChiefProcurementOfficer`
- Head Agent: `HeadOfProcurement`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `supplier.quotation_comparison_map` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.multi_supplier_order_optimized` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.purchase_order_placed` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.optimal_buy_date_forecasted` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.difal_optimizer` — reutilizado do catálogo de intenções existente ou adaptado
- `stock.replenishment` — reutilizado do catálogo de intenções existente ou adaptado
- `procurement.request.create` — NOVO: contrato/handler necessário
- `procurement.quote.compare` — NOVO: contrato/handler necessário
- `procurement.order.approve` — NOVO: contrato/handler necessário

## Topological 2flow

```2flow
ChiefProcurementOfficer.LoadSourcingPolicy :--: HeadOfProcurement.ValidatePurchaseRequest :--: HeadOfProcurement.CollectQuotations :--: HeadOfProcurement.CompareOffers :--: HeadOfProcurement.RecommendSupplier
```

```2flow
HeadOfProcurement.CheckBudgetAndApprovalLimit :--: ProcurementApprovalGate :--: HeadOfProcurement.IssuePurchaseOrder :--: HeadOfSupplierManagement.NotifySupplier
```

```2flow
HeadOfProcurement.ReceiveSupplierInvoice :--: HeadOfInventory.MatchGoodsReceipt :--: HeadOfAccounting.MatchInvoiceAndReceipt :--: HeadOfFinance.SchedulePayable
```

```2flow
HeadOfProcurement.DetectNoQuoteOrBudgetBreach :--: ChiefProcurementOfficer.ReviewException :--: Human.ApproveException
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
execution HeadOfProcurement.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfProcurement.ValidateDelegation
  <<- HeadOfProcurement.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
