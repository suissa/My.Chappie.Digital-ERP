# ERPSharedServices — Chief → Head

- Domínio: assistente, relatórios, permissões e automações transversais
- Chief Agent: `ChiefOperatingOfficer`
- Head Agent: `HeadOfOperations`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `assistant.ad_hoc_report_generated` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.daily_briefing_delivered` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.business_health_scorecard` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.autonomy_policy_changed` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.scheduled_query_executed` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.report_explanation_provided` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.user_role_updated` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.zero_trust_orchestration_audit` — reutilizado do catálogo de intenções existente ou adaptado
- `assistant.assistant_agent_automation` — reutilizado do catálogo de intenções existente ou adaptado
- `erp.intent.resolve` — NOVO: contrato/handler necessário
- `erp.workflow.status` — NOVO: contrato/handler necessário

## Topological 2flow

```2flow
Human.RequestBusinessOutcome :--: ChiefOperatingOfficer.ClassifyIntentAndDomain :--: HeadOfOperations.ResolveRequiredModuleHeads :--: HeadOfOperations.BuildEvidenceBoundPlan :--: HumanApprovalGate
```

```2flow
HeadOfOperations.ExecuteAuthorizedPlan :--: HeadOfOperations.CollectEvidence :--: HeadOfOperations.VerifyAcceptanceCriteria :--: Human.ReportOutcome
```

```2flow
HeadOfOperations.DetectMissingIntentOrCapability :--: HeadOfOperations.ReturnAON :--: Human.ClarifyIntent :--: ChiefOperatingOfficer.RecompilePlan
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
