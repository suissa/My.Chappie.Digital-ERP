# CustomerManagement — Chief → Head

- Domínio: cadastro, identidade, consentimento, relacionamento e atendimento
- Chief Agent: `ChiefCustomerOfficer`
- Head Agent: `HeadOfCustomerManagement`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `customer.frictionless_onboarding` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.entity_resolution` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.customer_360` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.rfm_segmentation` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.self_service` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.session` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.guardrails` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.temporal_spend` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.consent_center` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.service_knowledge` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.churn_forecast` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.recognition_treatment` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.audit_trail` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.relationship_automation` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.profile.create` — reutilizado do catálogo de intenções existente ou adaptado
- `customer.profile.update` — reutilizado do catálogo de intenções existente ou adaptado

## Topological 2flow

```2flow
ChiefCustomerOfficer.LoadCustomerPolicy :--: HeadOfCustomerManagement.ResolveIdentity :--: HeadOfCustomerManagement.CheckDuplicateAndConsent :--: HeadOfCustomerManagement.CreateOrUpdateProfile :--: HeadOfCustomerManagement.RecordProvenance
```

```2flow
HeadOfCustomerManagement.ReceivePrivacyRequest :--: HeadOfCustomerManagement.ValidateRequesterAuthority :--: HeadOfCustomerManagement.ApplyDataPolicy :--: HeadOfCustomerManagement.RecordComplianceEvidence
```

```2flow
HeadOfCustomerManagement.DetectChurnOrServiceRisk :--: ChiefCustomerOfficer.SelectAllowedTreatment :--: HeadOfCustomerManagement.ExecuteConsentBoundContact :--: HeadOfCustomerManagement.MeasureOutcome
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
execution HeadOfCustomerManagement.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfCustomerManagement.ValidateDelegation
  <<- HeadOfCustomerManagement.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
