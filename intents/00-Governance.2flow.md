# Governance — Chief → Head

- Domínio: delegação, autorização e configuração
- Chief Agent: `Human`
- Head Agent: `ChiefAgentRegistry`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `erp.delegation.configure` — NOVO: contrato/handler necessário
- `erp.delegation.validate` — NOVO: contrato/handler necessário
- `erp.delegation.publish` — NOVO: contrato/handler necessário
- `erp.delegation.revoke` — NOVO: contrato/handler necessário
- `erp.agent.approval.resolve` — NOVO: contrato/handler necessário

## Topological 2flow

```2flow
Human.ConfigureDelegation :--: ChiefAgentRegistry.ValidatePolicy :--: ChiefAgentRegistry.CompileDelegation :--: Human.ReviewAndAuthorize :--: ChiefAgentRegistry.PublishVersion
```

```2flow
Human.RequestApproval :--: ChiefAgentRegistry.ResolveApprover :--: Human.ApproveOrReject :--: ChiefAgentRegistry.RecordDecision
```

```2flow
ChiefAgentRegistry.DetectPolicyConflict :--: ChiefAgentRegistry.BlockExecution :--: Human.ResolveConflict :--: ChiefAgentRegistry.Revalidate
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
execution ChiefAgentRegistry.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> ChiefAgentRegistry.ValidateDelegation
  <<- ChiefAgentRegistry.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
