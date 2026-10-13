# Finance — Chief → Head

- Domínio: caixa, contas a pagar/receber, cobrança, pagamentos e planejamento
- Chief Agent: `ChiefFinancialOfficer`
- Head Agent: `HeadOfFinance`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `financial.cash_bank_ledger` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.payment_reconciliation` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.daily_pnl` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.store_credit` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.prepaid_wallet` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.automatic_provisioning` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.cashflow_forecast` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.working_capital_optimizer` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.credit_scoring` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.dunning_automation` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.payout_split` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.financial_receipt` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.zero_trust_audit` — reutilizado do catálogo de intenções existente ou adaptado
- `financial.treasury_automation` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.payable_title_scheduled` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.purchase_payment_executed` — reutilizado do catálogo de intenções existente ou adaptado

## Topological 2flow

```2flow
ChiefFinancialOfficer.LoadTreasuryPolicy :--: HeadOfFinance.ProjectCashPosition :--: HeadOfFinance.MatchReceivablesAndPayables :--: HeadOfFinance.ProposePaymentSchedule
```

```2flow
HeadOfFinance.PreparePayment :--: HeadOfFinance.ValidateBeneficiaryAndAmount :--: PaymentApprovalGate :--: PaymentProvider.Execute :--: HeadOfFinance.ReconcileSettlement
```

```2flow
HeadOfFinance.DetectLiquidityOrFraudRisk :--: HeadOfFinance.BlockOrHoldPayment :--: ChiefFinancialOfficer.ReviewRisk :--: Human.ApproveException
```

```2flow
HeadOfFinance.PublishReconciledFinancialEvent :--: HeadOfAccounting.PostAccountingProjection
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
execution HeadOfFinance.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfFinance.ValidateDelegation
  <<- HeadOfFinance.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
