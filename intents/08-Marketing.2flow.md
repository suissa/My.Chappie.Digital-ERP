# Marketing — Chief → Head

- Domínio: segmentação, campanhas, conteúdo, consentimento e mensuração
- Chief Agent: `ChiefMarketingOfficer`
- Head Agent: `HeadOfMarketing`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `marketing.unified_profile` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.communication_fingerprint` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.dynamic_segmentation` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.trigger_library` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.next_best_offer` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.personalized_pricing` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.copy_generation` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.consultative_prompting` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.consent_governance` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.journey_analytics` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.next_purchase_forecast` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.uplift_optimizer` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.zero_trust_consent` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.marketing_automation` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.campaign.create` — reutilizado do catálogo de intenções existente ou adaptado
- `marketing.campaign.report` — reutilizado do catálogo de intenções existente ou adaptado

## Topological 2flow

```2flow
ChiefMarketingOfficer.LoadMarketingPolicy :--: HeadOfMarketing.DefineCampaignObjective :--: HeadOfMarketing.BuildConsentEligibleSegment :--: HeadOfMarketing.PrepareContentVariants :--: HeadOfMarketing.EstimateBudgetAndUplift
```

```2flow
HeadOfMarketing.CheckFrequencyCapAndOptOut :--: MarketingCampaignApprovalGate :--: HeadOfMarketing.ScheduleOrSendCampaign :--: HeadOfMarketing.MeasureAttributedOutcome
```

```2flow
HeadOfMarketing.DetectConsentOrBudgetViolation :--: HeadOfMarketing.StopCampaign :--: ChiefMarketingOfficer.ReviewIncident :--: HeadOfMarketing.RecordAuditEvidence
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
execution HeadOfMarketing.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfMarketing.ValidateDelegation
  <<- HeadOfMarketing.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
