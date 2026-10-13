# ProductCatalog — Chief → Head

- Domínio: cadastro mestre, SKU, categorias, atributos, unidades e publicação
- Chief Agent: `ChiefProductOfficer`
- Head Agent: `HeadOfProductCatalog`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `commerce.catalog_master` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.aliases` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.tax_classification` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.bundles` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.channel_prices` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.price_history` — reutilizado do catálogo de intenções existente ou adaptado
- `product.catalog.create` — NOVO: contrato/handler necessário
- `product.catalog.update` — NOVO: contrato/handler necessário
- `product.catalog.publish` — NOVO: contrato/handler necessário
- `product.catalog.retire` — NOVO: contrato/handler necessário

## Topological 2flow

```2flow
ChiefProductOfficer.LoadCatalogPolicy :--: HeadOfProductCatalog.ValidateRequiredAttributes :--: HeadOfProductCatalog.ResolveDuplicateSKU :--: HeadOfProductCatalog.ValidateUnitsAndTaxonomy :--: HeadOfProductCatalog.SaveDraft
```

```2flow
HeadOfProductCatalog.RequestPublication :--: HeadOfProductCatalog.CheckPriceTaxAndStockDependencies :--: ProductPublicationApprovalGate :--: HeadOfProductCatalog.PublishProduct
```

```2flow
HeadOfProductCatalog.DetectMaterialProductChange :--: HeadOfProductCatalog.EmitCatalogChanged :--: HeadOfInventory.RefreshProductProjection :--: HeadOfSales.RefreshOfferProjection
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
execution HeadOfProductCatalog.ExecuteAuthorizedIntent
  -> Ok<ValidatedIntent>
  ->> HeadOfProductCatalog.ValidateDelegation
  <<- HeadOfProductCatalog.ValidateDelegation

  Ok<DomainOutcome>
    <- Ok<DomainOutcome>

  Error<DomainError>
    <- Error<DomainError>
```

> Os nomes dos nós são contratos arquiteturais propostos. A existência deste arquivo não significa que handlers, integrações ou testes de runtime estejam implementados.
