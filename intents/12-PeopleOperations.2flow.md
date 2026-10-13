# PeopleOperations — Chief → Head

- Domínio: equipe, escalas, ponto, tarefas e obrigações de pessoal
- Chief Agent: `ChiefPeopleOfficer`
- Head Agent: `HeadOfPeopleOperations`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `company.workforce` — reutilizado do catálogo de intenções existente ou adaptado
- `company.schedule` — reutilizado do catálogo de intenções existente ou adaptado
- `company.time_punch` — reutilizado do catálogo de intenções existente ou adaptado
- `company.payroll` — reutilizado do catálogo de intenções existente ou adaptado
- `company.total_cost` — reutilizado do catálogo de intenções existente ou adaptado
- `company.labor_liability` — reutilizado do catálogo de intenções existente ou adaptado
- `company.checklists` — reutilizado do catálogo de intenções existente ou adaptado
- `company.staffing_forecast` — reutilizado do catálogo de intenções existente ou adaptado
- `company.fair_scheduling` — reutilizado do catálogo de intenções existente ou adaptado
- `company.turnover_risk` — reutilizado do catálogo de intenções existente ou adaptado
- `company.agent_workforce` — reutilizado do catálogo de intenções existente ou adaptado
- `company.obligations` — reutilizado do catálogo de intenções existente ou adaptado
- `company.audit_trail` — reutilizado do catálogo de intenções existente ou adaptado
- `people.leave.request` — NOVO: contrato/handler necessário
- `people.shift.approve` — NOVO: contrato/handler necessário

## Topological 2flow

```2flow
ChiefPeopleOfficer.LoadWorkforcePolicy :--: HeadOfPeopleOperations.ValidateRoleAndAvailability :--: HeadOfPeopleOperations.BuildCompliantSchedule :--: HeadOfPeopleOperations.CheckCoverageAndRestRules :--: HeadOfPeopleOperations.PublishSchedule
```

```2flow
HeadOfPeopleOperations.ReceiveTimePunch :--: HeadOfPeopleOperations.ValidateEvidence :--: HeadOfPeopleOperations.ReconcileScheduleVariance :--: HeadOfFinance.PreparePayrollProjection :--: HeadOfAccounting.PostApprovedAccrual
```

```2flow
HeadOfPeopleOperations.DetectSensitivePersonnelRequest :--: HeadOfPeopleOperations.RestrictAccess :--: ChiefPeopleOfficer.Review :--: Human.ApproveIfRequired
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
execution HeadOfPeopleOperations.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfPeopleOperations.ValidateDelegation
  <<- HeadOfPeopleOperations.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
