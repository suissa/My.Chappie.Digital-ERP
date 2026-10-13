# Accounting — Chief → Head

- Domínio: partidas dobradas, razão, conciliação, obrigações e fechamento
- Chief Agent: `ChiefAccountingOfficer`
- Head Agent: `HeadOfAccounting`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `accounting.double_entry` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.chart_of_accounts` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.accrual_cash` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.ledger` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.bracket_forecast` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.labor_provisions` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.ecl_provisions` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.tax_reform_simulator` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.monophasic_optimizer` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.factor_r` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.regime_optimizer` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.difal_optimizer` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.accountant_package` — reutilizado do catálogo de intenções existente ou adaptado
- `accounting.audit_trail` — reutilizado do catálogo de intenções existente ou adaptado
- `company.obligations` — reutilizado do catálogo de intenções existente ou adaptado
- `company.labor_liability` — reutilizado do catálogo de intenções existente ou adaptado

## Topological 2flow

```2flow
ChiefAccountingOfficer.LoadAccountingPolicy :--: HeadOfAccounting.ValidateSourceEvidence :--: HeadOfAccounting.ResolveAccountMapping :--: HeadOfAccounting.BuildBalancedJournal :--: HeadOfAccounting.ValidateDebitsEqualCredits
```

```2flow
HeadOfAccounting.ReceiveFinancialEvent :--: HeadOfAccounting.PostJournalEntry :--: HeadOfAccounting.UpdateLedgerProjection :--: HeadOfAccounting.RecordImmutableEvidence
```

```2flow
HeadOfAccounting.StartPeriodClose :--: HeadOfAccounting.ReconcileControlAccounts :--: HeadOfAccounting.ValidateOpenExceptions :--: AccountingCloseApprovalGate :--: HeadOfAccounting.PublishCloseReport
```

```2flow
HeadOfAccounting.DetectTaxOrClassificationUncertainty :--: HeadOfAccounting.MarkEstimate :--: ChiefAccountingOfficer.Review :--: HumanOrQualifiedAccountant.ApproveFiling
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
execution HeadOfAccounting.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfAccounting.ValidateDelegation
  <<- HeadOfAccounting.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
