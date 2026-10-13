# My.Chappie.Digital-ERP

## Catálogo de Intents em 2flow

Os fluxos ficam em `intents/` e formalizam a delegação `Chief Agent → Head Agent → Specialist/Action Agent`. Os IDs já presentes em [My.Chappie.Digital-Intents.2flow](https://github.com/suissa/My.Chappie.Digital-Intents.2flow/tree/main) foram preservados como referências de compatibilidade; novos IDs são identificados como propostas e ainda exigem contratos, handlers e testes.

### Regras
- Chief define objetivos, políticas, prioridades e alçadas; Head coordena workflows; agentes especialistas executam capacidades limitadas.
- `HumanApprovalGate` é decisão humana explícita, nunca aprovação implícita.
- Sistema autoritativo confirma toda mutação; eventos e mensagens não substituem commit.
- Sem evidência, autorização ou política ativa, o fluxo bloqueia efeitos colaterais e escala a exceção.
- Os arquivos especificam topologia 2flow; não afirmam que runtime/handlers já estão implementados.

### Módulos
- [Governance](./intents/00-Governance.2flow.md) — `Human → ChiefAgentRegistry`
- [Operations](./intents/01-Operations.2flow.md) — `ChiefOperatingOfficer → HeadOfOperations`
- [Procurement](./intents/02-Procurement.2flow.md) — `ChiefProcurementOfficer → HeadOfProcurement`
- [Supplier Management](./intents/03-Suppliers.2flow.md) — `ChiefSupplierOfficer → HeadOfSupplierManagement`
- [Inventory](./intents/04-Inventory.2flow.md) — `ChiefInventoryOfficer → HeadOfInventory`
- [Product Catalog](./intents/05-ProductCatalog.2flow.md) — `ChiefProductOfficer → HeadOfProductCatalog`
- [Sales](./intents/06-Sales.2flow.md) — `ChiefSalesOfficer → HeadOfSales`
- [Customer Management](./intents/07-CustomerManagement.2flow.md) — `ChiefCustomerOfficer → HeadOfCustomerManagement`
- [Marketing](./intents/08-Marketing.2flow.md) — `ChiefMarketingOfficer → HeadOfMarketing`
- [Finance](./intents/09-Finance.2flow.md) — `ChiefFinancialOfficer → HeadOfFinance`
- [Accounting](./intents/10-Accounting.2flow.md) — `ChiefAccountingOfficer → HeadOfAccounting`
- [Fulfillment](./intents/11-Fulfillment.2flow.md) — `ChiefFulfillmentOfficer → HeadOfFulfillment`
- [People Operations](./intents/12-PeopleOperations.2flow.md) — `ChiefPeopleOfficer → HeadOfPeopleOperations`
- [Commerce](./intents/13-Commerce.2flow.md) — `ChiefProductOfficer → HeadOfProductCatalog`
- [ERP Shared Services](./intents/14-ERPSharedServices.2flow.md) — `ChiefOperatingOfficer → HeadOfOperations`
