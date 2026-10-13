# SupplierManagement — Chief → Head

- Domínio: cadastro, qualificação, conformidade e desempenho de fornecedores
- Chief Agent: `ChiefSupplierOfficer`
- Head Agent: `HeadOfSupplierManagement`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `supplier.supplier_enriched` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.supplier_reliability_score` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.supplier_portal_interaction` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.zero_trust_procurement_audit` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.profile.create` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.compliance.review` — reutilizado do catálogo de intenções existente ou adaptado
- `supplier.bank_details.change` — reutilizado do catálogo de intenções existente ou adaptado

## Topological 2flow

```2flow
ChiefSupplierOfficer.LoadSupplierPolicy :--: HeadOfSupplierManagement.ResolveSupplierIdentity :--: HeadOfSupplierManagement.CollectRequiredDocuments :--: HeadOfSupplierManagement.ValidateCompliance :--: HeadOfSupplierManagement.PublishSupplierStatus
```

```2flow
HeadOfSupplierManagement.DetectSensitiveSupplierChange :--: HeadOfSupplierManagement.VerifyOutOfBandEvidence :--: Human.ApproveSensitiveChange :--: HeadOfSupplierManagement.RecordDecision
```

```2flow
HeadOfSupplierManagement.MeasureSupplierPerformance :--: ChiefSupplierOfficer.ReviewRisk :--: HeadOfSupplierManagement.RecommendSuspendOrReinstate :--: Human.ApproveIfRequired
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
execution HeadOfSupplierManagement.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfSupplierManagement.ValidateDelegation
  <<- HeadOfSupplierManagement.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
