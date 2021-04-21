---
title: Beheerervaring in meerdere tenants
description: Azure gedelegeerd resourcebeheer maakt beheer voor alle tenants mogelijk.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 027d1d5e81d5a652a7e2d5441c40440c661f730f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778621"
---
# <a name="cross-tenant-management-experiences"></a>Beheerervaring in meerdere tenants

Als serviceprovider kunt u [](../overview.md) Azure Lighthouse gebruiken voor het beheren van resources voor meerdere klanten vanuit uw eigen Azure Active Directory (Azure AD)-tenant. Veel taken en services kunnen worden uitgevoerd voor beheerde tenants met behulp van [gedelegeerd Azure-resourcebeheer.](../concepts/azure-delegated-resource-management.md)

> [!TIP]
> Gedelegeerd resourcebeheer van Azure kan ook worden gebruikt binnen een onderneming die meerdere [eigen Azure AD-tenants](enterprise.md) heeft om het beheer van meerdere tenants te vereenvoudigen.

## <a name="understanding-tenants-and-delegation"></a>Informatie over tenants en delegering

Een Azure AD-tenant is een representatie van een organisatie. Het is een toegewezen exemplaar van Azure AD dat een organisatie ontvangt wanneer ze een relatie met Microsoft maken door zich te registreren voor Azure, Microsoft 365 of andere services. Elke Azure AD-tenant is verschillend en gescheiden van andere Azure AD-tenants en heeft een eigen tenant-id (een GUID). Zie Wat is Azure Active Directory? voor [meer Azure Active Directory.](../../active-directory/fundamentals/active-directory-whatis.md)

Normaal gesproken moeten serviceproviders zich voor het beheren van Azure-resources voor een klant aanmelden bij de Azure Portal met een account dat is gekoppeld aan de tenant van die klant, waarvoor een beheerder in de tenant van de klant gebruikersaccounts voor de serviceprovider moet maken en beheren.

Met Azure Lighthouse geeft het onboardingproces gebruikers binnen de tenant van de serviceprovider op die kunnen werken aan gedelegeerde abonnementen en resourcegroepen in de tenant van de klant. Deze gebruikers kunnen zich vervolgens aanmelden bij de Azure Portal met hun eigen referenties. Binnen de Azure Portal kunnen ze resources beheren die behoren tot alle klanten waarvoor ze toegang hebben. U kunt dit doen [](../how-to/view-manage-customers.md) door naar de pagina Mijn klanten in de Azure Portal te gaan of door rechtstreeks binnen de context van het abonnement van die klant te werken, in de Azure Portal of via API's.

Azure Lighthouse biedt meer flexibiliteit voor het beheren van resources voor meerdere klanten zonder dat u zich bij verschillende accounts in verschillende tenants moet aanmelden. Een serviceprovider kan bijvoorbeeld twee klanten hebben met verschillende verantwoordelijkheden en toegangsniveaus. Met Azure Lighthouse kunnen gemachtigde gebruikers zich aanmelden bij de tenant van de serviceprovider voor toegang tot deze resources.

![Diagram met klantbronnen die worden beheerd via één serviceprovider-tenant.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Ondersteuning voor API's en beheerprogramma's

U kunt beheertaken uitvoeren op gedelegeerde resources rechtstreeks in de portal of met behulp van API's en beheerhulpprogramma's (zoals Azure CLI en Azure PowerShell). Alle bestaande API's kunnen worden gebruikt bij het werken met gedelegeerde resources, zolang de functionaliteit wordt ondersteund voor beheer tussen tenants en de gebruiker de juiste machtigingen heeft.

De Azure PowerShell [cmdlet Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) toont standaard de voor `TenantId` de beherende tenant. U kunt de kenmerken en voor elk abonnement gebruiken, zodat u kunt bepalen of een geretourneerd abonnement bij een beheerde tenant hoort of bij uw `HomeTenantId` `ManagedByTenantIds` beherende tenant.

Op dezelfde manier worden in Azure CLI-opdrachten zoals [az account list](/cli/azure/account#az_account_list) de kenmerken en `homeTenantId` `managedByTenants` weergegeven. Als u deze waarden niet ziet wanneer u Azure CLI gebruikt, probeert u uw cache te wissen door uit te gaan `az account clear` gevolgd door `az login --identity` .

In de Azure REST API bevatten de opdrachten [Abonnementen - Get](/rest/api/resources/subscriptions/get) en Abonnementen [-](/rest/api/resources/subscriptions/list) `ManagedByTenant` Lijst.

> [!NOTE]
> Naast tenantgegevens met betrekking tot Azure Lighthouse, kunnen tenants die worden weergegeven door deze API's ook partnerten tenants voor Azure Databricks of door Azure beheerde toepassingen weerspiegelen.

We bieden ook API's die specifiek zijn voor het uitvoeren van Azure Lighthouse taken. Zie de sectie Referentie voor **meer** informatie.

## <a name="enhanced-services-and-scenarios"></a>Verbeterde services en scenario's

De meeste taken en services kunnen worden uitgevoerd op gedelegeerde resources in beheerde tenants. Hieronder vindt u enkele van de belangrijkste scenario's waarin beheer voor meerdere tenants met name effectief kan zijn.

[Azure Arc:](../../azure-arc/index.yml)

- Hybride servers op schaal beheren - [Azure Arc ingeschakelde servers](../../azure-arc/servers/overview.md):
  - [Windows Server- of Linux-machines buiten Azure beheren die zijn](../../azure-arc/servers/onboard-portal.md) verbonden met gedelegeerde abonnementen en/of resourcegroepen in Azure
  - Verbonden machines beheren met behulp van Azure-constructies, zoals Azure Policy en taggen
  - Zorg ervoor dat dezelfde set beleidsregels wordt toegepast op hybride omgevingen van klanten
  - Gebruik Azure Security Center voor het bewaken van naleving in hybride omgevingen van klanten
- Hybride Kubernetes-clusters op schaal beheren - [Azure Arc Kubernetes (preview)](../../azure-arc/kubernetes/overview.md):
  - [Kubernetes-clusters beheren die zijn verbonden](../../azure-arc/kubernetes/quickstart-connect-cluster.md) met gedelegeerde abonnementen en/of resourcegroepen in Azure
  - [GitOps gebruiken voor](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) verbonden clusters
  - Beleid afdwingen voor verbonden clusters

[Azure Automation:](../../automation/index.yml)

- Automation-accounts gebruiken voor toegang tot en werken met gedelegeerde resources

[Azure Backup:](../../backup/index.yml)

- Back-up en herstel van [klantgegevens vanuit on-premises workloads, Azure-VM's, Azure-bestands shares en meer](../..//backup/backup-overview.md#what-can-i-back-up)
- Gegevens weergeven voor alle gedelegeerde klantbronnen in [Backup Center](../../backup/backup-center-overview.md)
- Gebruik de [Back-upverkenner](../../backup/monitor-azure-backup-with-backup-explorer.md) om operationele informatie weer te geven van back-upitems (waaronder Azure-resources die nog niet zijn geconfigureerd voor back-up) en bewakingsinformatie (taken en waarschuwingen) voor gedelegeerde abonnementen. De Back-upverkenner is momenteel alleen beschikbaar voor Azure VM-gegevens.
- Gebruik [Back-uprapporten](../../backup/configure-reports.md) overgedragen abonnementen om historische trends bij te houden, het verbruik van back-upopslag te analyseren en back-ups en herstels te controleren.

[Azure Blueprints:](../../governance/blueprints/index.yml)

- Gebruik Azure Blueprints om de implementatie van resourcesjablonen en andere artefacten te beheren (hiervoor is extra toegang vereist om [het](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) klantabonnement voor te bereiden)

[Azure Cost Management + Billing:](../../cost-management-billing/index.yml)

- Vanuit de beherende tenant kunnen CSP-partners verbruikskosten vóór belasting bekijken, beheren en analyseren voor klanten die onder het Azure-plan vallen. De kosten worden gebaseerd op de detailhandelstarieven en de op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) die de partner heeft voor het abonnement van de klant. Op dit moment kunt u de verbruikskosten bekijken tegen retailtarieven voor elk afzonderlijk klantabonnement op basis van Azure RBAC-toegang.

[Azure Key Vault:](../../key-vault/general/index.yml)

- Sleutelkluizen maken in tenants van klanten
- Een beheerde identiteit gebruiken om sleutelkluizen te maken in tenants van klanten

[Azure Kubernetes Service (AKS)](../../aks/index.yml):

- Gehoste Kubernetes-omgevingen beheren en toepassingen in containers implementeren en beheren in tenants van klanten
- Clusters implementeren en beheren in tenants van klanten
-   Gebruik Azure Monitor voor containers om de prestaties van tenants van klanten te bewaken

[Azure Migrate:](../../migrate/index.yml)

- Migratieprojecten maken in de tenant van de klant en VM's migreren

[Azure Monitor:](../../azure-monitor/index.yml)

- Waarschuwingen voor gedelegeerde abonnementen weergeven, met de mogelijkheid om waarschuwingen voor alle abonnementen weer te geven en te vernieuwen
- Details van activiteitenlogboek weergeven voor gedelegeerde abonnementen
- [Log Analytics:](../../azure-monitor/logs/service-providers.md)query's uitvoeren op gegevens uit externe werkruimten in meerdere tenants (automation-accounts die worden gebruikt voor toegang tot gegevens uit werkruimten in tenants van klanten, moeten worden gemaakt in dezelfde tenant)
- [Waarschuwingen voor activiteitenlogboek maken, weergeven en beheren](../../azure-monitor/alerts/alerts-activity-log.md) in tenants van klanten
- Waarschuwingen maken in tenants van klanten die automatisering activeren, zoals Azure Automation runbooks of Azure Functions, in de beherende tenant via webhooks
- Diagnostische [instellingen maken](../..//azure-monitor/essentials/diagnostic-settings.md) in tenants van klanten om resourcelogboeken te verzenden naar werkruimten in de beherende tenant
- Voor SAP-workloads [controleert u de metrische gegevens van SAP Solutions met een geaggregeerde weergave voor tenants van klanten](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Azure-netwerken:](../../networking/networking-overview.md)

- Azure-netwerkinterfacekaarten [Virtual Network](../../virtual-network/index.yml) virtuele netwerkinterfacekaarten (vNICs) implementeren en beheren binnen beheerde tenants
- Implementatie en configuratie [van Azure Firewall](../../firewall/overview.md) om de resources van klanten Virtual Network beveiligen
- Connectiviteitsservices zoals [Azure Virtual WAN,](../../virtual-wan/virtual-wan-about.md) [ExpressRoute](../../expressroute/expressroute-introduction.md)en [VPN Gateways beheren](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- Gebruik Azure Lighthouse voor de ondersteuning van belangrijke scenario's voor [het Azure-netwerken MSP-programma](../../networking/networking-partners-msp.md)

[Azure Policy:](../../governance/policy/index.yml)

- Beleidsdefinities binnen gedelegeerde abonnementen maken en bewerken
- Beleidsdefinities en beleidstoewijzingen implementeren in meerdere tenants
- Door de klant gedefinieerde beleidsdefinities toewijzen binnen gedelegeerde abonnementen
- Klanten zien beleidsregels die zijn geschreven door de serviceprovider naast alle beleidsregels die ze zelf hebben gemaakt
- Kan [deployIfNotExists herstellen of toewijzingen wijzigen binnen de beheerde tenant](../how-to/deploy-policy-remediation.md)
- Houd er rekening mee dat het weergeven van nalevingsdetails voor niet-compatibele resources in tenants van klanten momenteel niet wordt ondersteund

[Azure Resource Graph:](../../governance/resource-graph/index.yml)

- Bevat nu de tenant-id in de geretourneerde queryresultaten, zodat u kunt identificeren of een abonnement tot een beheerde tenant behoort

[Azure Security Center:](../../security-center/index.yml)

- Zichtbaarheid voor alle tenants
  - Naleving van beveiligingsbeleid bewaken en beveiligingsdekking garanderen voor alle resources van alle tenants
  - Continue bewaking van naleving van regelgeving voor meerdere tenants in één weergave
  - Beveilig, triageer en prioriteer actiebare beveiligingsaanbevelingen met berekening van de beveiligingsscore
- Beheer van beveiligingsstatussen voor alle tenants
  - Beveiligingsbeleid beheren
  - Actie ondernemen op resources die niet voldoen aan de beveiligingsaanbevelingen die kunnen worden uitgevoerd
  - Beveiligingsgegevens verzamelen en opslaan
- Detectie en beveiliging van bedreigingen tussen tenants
  - Bedreigingen voor de resources van tenants detecteren
  - Geavanceerde beveiliging tegen bedreigingen toepassen, zoals JIT-toegang (Just-In-Time)
  - De netwerkbeveiligingsgroep configureren met adaptieve netwerkbeveiliging
  - Zorg ervoor dat op servers alleen de toepassingen en processen worden uitgevoerd die moeten worden uitgevoerd met adaptieve toepassingsregelaars
  - Wijzigingen in belangrijke bestanden en registergegevens bewaken met FIM (File Integrity Monitoring)
- Houd er rekening mee dat het hele abonnement moet worden gedelegeerd aan de beherende tenant; Azure Security Center worden niet ondersteund met gedelegeerde resourcegroepen

[Azure Sentinel:](../../sentinel/multiple-tenants-service-providers.md)

- Resources Azure Sentinel beheren [in tenants van klanten](../../sentinel/multiple-tenants-service-providers.md)
- [Aanvallen bijhouden en beveiligingswaarschuwingen weergeven voor meerdere tenants](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Incidenten weergeven](../../sentinel/multiple-workspace-view.md) in meerdere Azure Sentinel werkruimten verspreid over tenants

[Azure Service Health:](../../service-health/index.yml)

- De status van klantbronnen bewaken met Azure Resource Health
- De status bijhouden van de Azure-services die door uw klanten worden gebruikt

[Azure Site Recovery:](../../site-recovery/index.yml)

- Opties voor herstel na noodherstel beheren voor virtuele Azure-machines in tenants van klanten (u kunt geen accounts gebruiken om `RunAs` VM-extensies te kopiëren)

[Azure Virtual Machines:](../../virtual-machines/index.yml)

- Extensies van virtuele machines gebruiken voor configuratie- en automatiseringstaken na de implementatie op Virtuele Azure-machines
- Diagnostische gegevens over opstarten gebruiken om problemen met Azure-VM's op te lossen
- Toegang tot VM's met seriële console
- VM's integreren met Azure Key Vault voor wachtwoorden, geheimen of cryptografische sleutels voor schijfversleuteling met behulp van beheerde identiteit via beleid [,](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)zodat geheimen worden opgeslagen in een Key Vault in de beheerde tenants
- Houd er rekening mee dat u geen toegangsgegevens Azure Active Directory voor externe aanmelding bij VM's

Ondersteuningsaanvragen:

- [Open ondersteuningsaanvragen **van Help en ondersteuning**](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) in de Azure Portal voor gedelegeerde resources (het selecteren van het ondersteuningsplan dat beschikbaar is voor het gedelegeerde bereik)
- De [Azure Quota-API gebruiken voor](/rest/api/reserved-vm-instances/quotaapi) het weergeven en beheren van Azure-servicequota voor gedelegeerde klantbronnen

## <a name="current-limitations"></a>Huidige beperkingen

Houd bij alle scenario's rekening met de volgende huidige beperkingen:

- Aanvragen die door Azure Resource Manager worden verwerkt, kunnen worden uitgevoerd met behulp Azure Lighthouse. De bewerkings-URI's voor deze aanvragen beginnen met `https://management.azure.com` . Aanvragen die worden verwerkt door een exemplaar van een resourcetype (zoals Key Vault-toegang tot geheimen of toegang tot opslaggegevens) worden echter niet ondersteund met Azure Lighthouse. De bewerkings-URI's voor deze aanvragen beginnen doorgaans met een adres dat uniek is voor uw exemplaar, zoals `https://myaccount.blob.core.windows.net` of `https://mykeyvault.vault.azure.net/` . De laatste zijn doorgaans ook gegevensbewerkingen in plaats van beheerbewerkingen.
- Roltoewijzingen moeten [gebruikmaken van ingebouwde Azure-rollen.](../../role-based-access-control/built-in-roles.md) Alle ingebouwde rollen worden momenteel ondersteund met gedelegeerd Azure-resourcebeheer, met uitzondering van Eigenaar of ingebouwde rollen met [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) machtigingen. De rol Gebruikerstoegangbeheerder wordt alleen ondersteund voor beperkt gebruik bij het [toewijzen van rollen aan beheerde identiteiten.](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)  Aangepaste rollen en [klassieke abonnementsbeheerdersrollen](../../role-based-access-control/classic-administrators.md) worden niet ondersteund.
- Hoewel u abonnementen kunt onboarden die gebruikmaken van Azure Databricks, kunnen gebruikers in de tenant die Azure Databricks beheren, op dit moment geen Azure Databricks in een gedelegeerd abonnement starten.
- Hoewel u abonnementen en resourcegroepen met resourcevergrendelingen kunt onboarden, voorkomen deze vergrendelingen niet dat acties worden uitgevoerd door gebruikers in de beherende tenant. [Toewijzingen](../../role-based-access-control/deny-assignments.md) weigeren die door het systeem beheerde resources beveiligen, zoals resources die zijn gemaakt door door Azure beheerde toepassingen of Azure Blueprints (door het systeem toegewezen toewijzingen voor weigeren), verhinderen dat gebruikers in de beherende tenant op deze resources kunnen handelen; Op dit moment kunnen gebruikers in de tenant van de klant echter geen eigen toewijzingen voor weigeren maken (door de gebruiker toegewezen toewijzingen voor weigeren).
- Delegering van abonnementen in een [nationale cloud](../../active-directory/develop/authentication-national-cloud.md) en de openbare Azure-cloud, of in twee afzonderlijke nationale clouds, wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

- Onboard uw klanten voor Azure Lighthouse, hetzij met behulp van [Azure Resource Manager-sjablonen](../how-to/onboard-customer.md) of door een aanbieding voor persoonlijke of openbare beheerde services te publiceren op [Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Bekijk en beheer klanten](../how-to/view-manage-customers.md) door naar **Mijn klanten** in de Azure Portal.