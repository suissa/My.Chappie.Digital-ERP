# Sales — Chief → Head

- Domínio: oportunidades, preços, pedidos, vendas e descontos
- Chief Agent: `ChiefSalesOfficer`
- Head Agent: `HeadOfSales`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `sales.terminal_transaction_captured` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.cash_sale_voice_registered` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.product_alias_resolved` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.reconcile_amount_mismatch` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.universal_receipt` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.open_tab_created` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.tab_closed` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.guardrail_blocked` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.basket_cross_sell_suggested` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.hourly_sales_forecasted` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.stock_deducted_on_sale` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.conversational_order_received` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.zero_trust_sales_audit` — reutilizado do catálogo de intenções existente ou adaptado
- `sales.sales_agent_automation` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.storefront` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.margin_guard` — reutilizado do catálogo de intenções existente ou adaptado

## Topological 2flow

```2flow
ChiefSalesOfficer.LoadCommercialPolicy :--: HeadOfSales.ResolveCustomerAndProducts :--: HeadOfSales.CalculatePriceAndMargin :--: HeadOfSales.ValidateDiscountAuthority :--: HeadOfSales.CheckCreditAndStock
```

```2flow
HeadOfSales.CreateOrderDraft :--: SalesOrderApprovalGate :--: HeadOfInventory.ReserveStock :--: HeadOfSales.ConfirmOrder :--: HeadOfFinance.CreateReceivable
```

```2flow
HeadOfSales.ReceivePaymentEvidence :--: HeadOfSales.ReconcileTransaction :--: HeadOfInventory.CommitSaleMovement :--: HeadOfAccounting.PostSaleEvidence :--: HeadOfSales.IssueReceipt
```

```2flow
HeadOfSales.DetectOutOfPolicyDiscount :--: HeadOfSales.BlockCommit :--: ChiefSalesOfficer.ReviewException :--: Human.ApproveIfRequired
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
execution HeadOfSales.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfSales.ValidateDelegation
  <<- HeadOfSales.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
