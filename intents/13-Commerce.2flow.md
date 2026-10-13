# Commerce — Chief → Head

- Domínio: catálogo comercial, sortimento, preços por canal e ofertas
- Chief Agent: `ChiefProductOfficer`
- Head Agent: `HeadOfProductCatalog`
- IDs existentes são reutilizados; IDs novos requerem definição de payload, permissões, handler e testes.

## Intents

- `commerce.catalog_master` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.aliases` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.net_margin` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.channel_prices` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.storefront` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.assortment` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.tax_classification` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.bundles` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.elasticity_forecast` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.category_optimizer` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.cost_pass_through` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.zombie_items` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.margin_guard` — reutilizado do catálogo de intenções existente ou adaptado
- `commerce.price_history` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.intent_discovery` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.standing_intent` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.group_buy_campaign` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.reverse_rfq` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.zero_inventory` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.cross_border_responsibility` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.territorial_exclusivity` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.recurring_supply` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.assortment_gap` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.trend_transfer` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.demand_liquidity_forecast` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.landed_margin_optimizer` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.zero_trust_merchant_audit` — reutilizado do catálogo de intenções existente ou adaptado
- `merchant.merchant_operation_automation` — reutilizado do catálogo de intenções existente ou adaptado

## Topological 2flow

```2flow
ChiefProductOfficer.LoadCommercialCatalogPolicy :--: HeadOfProductCatalog.ResolveProductIntent :--: HeadOfProductCatalog.CheckAvailabilityAndChannelPrice :--: HeadOfProductCatalog.ReturnOffer
```

```2flow
HeadOfProductCatalog.CalculateLandedCostAndMargin :--: HeadOfProductCatalog.CheckMarginFloor :--: CommercePriceApprovalGate :--: HeadOfProductCatalog.PublishPriceChange
```

```2flow
HeadOfProductCatalog.DetectCatalogOrCostChange :--: HeadOfProductCatalog.RecalculateAffectedOffers :--: HeadOfSales.RefreshActiveQuotes
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
