# Operations — Chief → Head

- Domínio: coordenação operacional e dependências intermodulares
- Chief Agent: `ChiefOperatingOfficer`
- Head Agent: `HeadOfOperations`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `assistant.cross_module_plan_proposed` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.daily_action_plan_optimized` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.approvals_inbox_pending` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.cross_module_simulation_result` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.pattern_change_detected` — reutilizado do catálogo de intenções existente ou adaptado
- `operations.workflow.monitor` — NOVO: contrato/handler necessário
- `operations.exception.escalate` — NOVO: contrato/handler necessário

## Topological 2flow

```2flow
ChiefOperatingOfficer.LoadOperatingPolicy :--: HeadOfOperations.PlanCrossModuleWorkflow :--: HeadOfOperations.ValidateDependencies :--: HeadOfOperations.ExecuteAuthorizedSteps :--: HeadOfOperations.VerifyOutcome
```

```2flow
HeadOfOperations.DetectBlockedWorkflow :--: HeadOfOperations.ClassifyFailure :--: HeadOfOperations.CompensateIfAllowed :--: ChiefOperatingOfficer.ReviewException
```

```2flow
HeadOfOperations.AggregateModuleSignals :--: ChiefOperatingOfficer.ResolvePriorityConflict :--: HeadOfOperations.PublishRevisedPlan
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
execution HeadOfOperations.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfOperations.ValidateDelegation
  <<- HeadOfOperations.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
