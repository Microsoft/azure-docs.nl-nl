---
title: Beheerervaring in meerdere tenants
description: Azure delegated Resource Management maakt een cross-Tenant beheer mogelijk.
ms.date: 12/16/2020
ms.topic: conceptual
ms.openlocfilehash: dca14781aac9db3cdd47ab4b66506f77f0b78c31
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97616572"
---
# <a name="cross-tenant-management-experiences"></a>Beheerervaring in meerdere tenants

Als service provider kunt u [Azure Lighthouse](../overview.md) gebruiken voor het beheren van resources voor meerdere klanten vanuit uw eigen Azure Active Directory (Azure AD)-Tenant. Veel taken en services kunnen worden uitgevoerd voor beheerde tenants met behulp van [Azure delegated resource management](../concepts/azure-delegated-resource-management.md).

> [!TIP]
> Azure delegated resource management kan ook worden gebruikt [binnen een onderneming met meerdere Azure AD-tenants](enterprise.md) , om cross-Tenant beheer te vereenvoudigen.

## <a name="understanding-tenants-and-delegation"></a>Wat zijn tenants en overdracht?

Een Azure AD-Tenant is een representatie van een organisatie. Het is een toegewezen exemplaar van Azure AD dat een organisatie ontvangt wanneer ze een relatie met micro soft maken door zich aan te melden voor Azure, Microsoft 365 of andere services. Elke Azure AD-Tenant is uniek en gescheiden van andere Azure AD-tenants en heeft een eigen Tenant-ID (een GUID). Zie [Wat is Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md) voor meer informatie.

Om Azure-resources voor een klant te kunnen beheren, moeten service providers zich dus aanmelden bij de Azure Portal met behulp van een account dat is gekoppeld aan de Tenant van die klant, waarbij een beheerder in de Tenant van de klant gebruikers accounts voor de service provider kan maken en beheren.

Met Azure Lighthouse geeft het voorbereidings proces gebruikers in de Tenant van de service provider aan die kunnen werken aan gedelegeerde abonnementen en resource groepen in de Tenant van de klant. Deze gebruikers kunnen zich vervolgens aanmelden bij de Azure Portal met hun eigen referenties. Binnen het Azure Portal kunnen ze resources beheren die horen bij alle klanten waartoe ze toegang hebben. U kunt dit doen door de pagina [mijn klanten](../how-to/view-manage-customers.md) te bezoeken in het Azure portal of door rechtstreeks te werken in de context van het abonnement van de klant, hetzij in de Azure portal-of via-api's.

Azure Lighthouse biedt meer flexibiliteit voor het beheren van resources voor meerdere klanten, zonder dat u zich hoeft aan te melden bij verschillende accounts in verschillende tenants. Een service provider kan bijvoorbeeld twee klanten hebben met verschillende verantwoordelijkheden en toegangs niveaus. Met Azure Lighthouse kunnen geautoriseerde gebruikers zich aanmelden bij de Tenant van de service provider om toegang te krijgen tot deze bronnen.

![Diagram met klant resources die worden beheerd via één service provider Tenant.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Ondersteuning voor Api's en beheer hulpprogramma's

U kunt Beheer taken op gedelegeerde resources rechtstreeks uitvoeren in de portal of met behulp van Api's en beheer hulpprogramma's (zoals Azure CLI en Azure PowerShell). Alle bestaande Api's kunnen worden gebruikt bij het werken met gedelegeerde resources, zolang de functionaliteit wordt ondersteund voor cross-Tenant beheer en de gebruiker de juiste machtigingen heeft.

De Azure PowerShell [Get-AzSubscription-cmdlet](/powershell/module/Az.Accounts/Get-AzSubscription) toont `HomeTenantId` de `ManagedByTenantIds` kenmerken en voor elk abonnement, zodat u kunt bepalen of een geretourneerd abonnement behoort tot een beheerde Tenant of met uw Tenant beheren.

Net als bij Azure CLI-opdrachten, zoals [AZ account list](/cli/azure/account#az-account-list) , worden de-en-kenmerken weer gegeven `homeTenantId` `managedByTenants` . Als u deze waarden niet ziet wanneer u Azure CLI gebruikt, probeert u de cache te wissen door uit te voeren, `az account clear` gevolgd door `az login --identity` .

In de Azure REST API zijn de opdrachten [abonnementen ophalen](/rest/api/resources/subscriptions/get) en [abonnementen lijst](/rest/api/resources/subscriptions/list) opnemen `ManagedByTenant` .

> [!NOTE]
> Naast de Tenant informatie met betrekking tot Azure Lighthouse kunnen tenants die door deze Api's worden weer gegeven ook partner tenants weer spie gelen voor Azure Databricks of door Azure beheerde toepassingen.

We bieden ook Api's die specifiek zijn voor het uitvoeren van Azure Lighthouse-taken. Zie de sectie **Naslag informatie** voor meer informatie.

## <a name="enhanced-services-and-scenarios"></a>Verbeterde services en scenario's

De meeste taken en services kunnen worden uitgevoerd op gedelegeerde resources in beheerde tenants. Hieronder vindt u enkele van de belangrijkste scenario's waarbij beheer van meerdere tenants met name effectief kan zijn.

[Azure-boog](../../azure-arc/index.yml):

- Hybride servers op schaal beheren- [servers met Azure-Arc-functionaliteit](../../azure-arc/servers/overview.md):
  - [Windows Server-of Linux-computers buiten Azure beheren die zijn verbonden](../../azure-arc/servers/onboard-portal.md) met gedelegeerde abonnementen en/of resource groepen in azure
  - Verbonden computers beheren met Azure-constructs, zoals Azure Policy en tagging
  - Zorg ervoor dat dezelfde set beleids regels wordt toegepast op hybride omgevingen van klanten
  - Azure Security Center gebruiken om de naleving van hybride omgevingen van klanten te bewaken
- Hybride Kubernetes-clusters op schaal beheren- [Kubernetes van Azure Arc ingeschakeld (preview)](../../azure-arc/kubernetes/overview.md):
  - [Kubernetes-clusters beheren die zijn verbonden](../../azure-arc/kubernetes/connect-cluster.md) met gedelegeerde abonnementen en/of resource groepen in azure
  - [GitOps gebruiken](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) voor verbonden clusters
  - Beleid afdwingen in verbonden clusters

[Azure Automation](../../automation/index.yml):

- Automation-accounts gebruiken om toegang te krijgen tot en om te werken met gedelegeerde resources

[Azure backup](../../backup/index.yml):

- Back-ups maken van klant gegevens en deze herstellen [van on-premises workloads, Azure-vm's, Azure-bestands shares en meer](../..//backup/backup-overview.md#what-can-i-back-up)
- Gebruik de [back-upverkenner](../../backup/monitor-azure-backup-with-backup-explorer.md) voor het weer geven van operationele gegevens van back-upitems (inclusief Azure-resources die nog niet zijn geconfigureerd voor back-up) en controle-informatie (taken en waarschuwingen) voor gedelegeerde abonnementen. Backup Explorer is momenteel alleen beschikbaar voor Azure VM-gegevens.
- Gebruik [back-uprapporten](../../backup/configure-reports.md) over gedelegeerde abonnementen om historische trends bij te houden, het verbruik van back-ups te analyseren en back-ups te controleren en te herstellen.

[Azure-blauw drukken](../../governance/blueprints/index.yml):

- Azure-blauw drukken gebruiken om de implementatie van resource sjablonen en andere artefacten te organiseren (hiervoor is [extra toegang](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) vereist om het abonnement van de klant voor te bereiden)

[Azure Cost Management en facturering](../../cost-management-billing/index.yml):

- Van de beheer-Tenant kunnen CSP-partners de kosten voor het gebruik van de belasting (niet inclusief aankopen) bekijken, beheren en analyseren voor klanten die zich onder het Azure-abonnement bevinden. De kosten zijn gebaseerd op de retail tarieven en de Azure RBAC-toegang (op rollen gebaseerd toegangs beheer) die de partner heeft voor het abonnement van de klant.

[Azure Key Vault](../../key-vault/general/index.yml):

- Sleutel kluizen maken in de tenants van de klant
- Een beheerde identiteit gebruiken voor het maken van sleutel kluizen in de tenants van de klant

[Azure Kubernetes service (AKS)](../../aks/index.yml):

- Gehoste Kubernetes-omgevingen beheren en toepassingen in containers implementeren en beheren binnen de tenants van de klant
- Clusters implementeren en beheren in de tenants van de klant
-   Gebruik Azure Monitor voor containers om de prestaties van de tenants van de klant te bewaken

[Azure migrate](../../migrate/index.yml):

- Migratie projecten maken in de Tenant van de klant en Vm's migreren

[Azure monitor](../../azure-monitor/index.yml):

- Waarschuwingen voor gedelegeerde abonnementen weer geven, met de mogelijkheid om waarschuwingen voor alle abonnementen weer te geven en te vernieuwen
- Details van het activiteiten logboek voor gedelegeerde abonnementen weer geven
- Log Analytics: gegevens opvragen uit externe werk ruimten in meerdere tenants
- Maak waarschuwingen in klant tenants die automatisering activeren, zoals Azure Automation runbooks of Azure Functions, in de Tenant beheren via webhooks
- Diagnostische instellingen maken in de tenants van de klant om resource logboeken te verzenden naar werk ruimten in de Tenant beheren
- Voor SAP-workloads [bewaakt u de metrische gegevens van SAP-oplossingen met een geaggregeerde weer gave van alle tenants van de klant](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Azure-netwerken](../../networking/networking-overview.md):

- [Azure Virtual Network](../../virtual-network/index.yml) en virtuele netwerk interface kaarten (vnic's) in beheerde tenants implementeren en beheren
- [Azure firewall](../../firewall/overview.md) implementeren en configureren om Virtual Network Resources van klanten te beveiligen
- Connectiviteits Services beheren, zoals [virtuele WAN](../../virtual-wan/virtual-wan-about.md)-, [ExpressRoute](../../expressroute/expressroute-introduction.md)-en [VPN-gateways](../../vpn-gateway/vpn-gateway-about-vpngateways.md) van Azure
- Azure Lighthouse gebruiken ter ondersteuning van belang rijke scenario's voor het [MSP-programma van Azure Networking](../../networking/networking-partners-msp.md)

[Azure Policy](../../governance/policy/index.yml):

- Beleids definities maken en bewerken binnen gedelegeerde abonnementen
- Door de klant gedefinieerde beleids definities toewijzen binnen gedelegeerde abonnementen
- Klanten zien beleid dat door de service provider is ontworpen naast het beleid dat ze zelf hebben ontworpen
- Kan [deployIfNotExists herstellen of toewijzingen wijzigen binnen de beheerde Tenant](../how-to/deploy-policy-remediation.md)

[Azure-resource grafiek](../../governance/resource-graph/index.yml):

- Bevat nu de Tenant-ID in geretourneerde query resultaten, zodat u kunt bepalen of een abonnement bij een beheerde Tenant hoort

[Azure Security Center](../../security-center/index.yml):

- Zicht baarheid van meerdere tenants
  - Naleving van beveiligings beleid controleren en de beveiliging van alle resources van de Tenant garanderen
  - Voortdurende naleving van regelgeving voor meerdere tenants in één weer gave
  - Bewaak, sorteren en prioriteit geven aan bruikbare beveiligings aanbevelingen met een beveiligde score berekening
- Beheer van cross-Tenant beveiliging postuur
  - Beveiligingsbeleid beheren
  - Onderneem actie ondernemen voor bronnen die niet meer compatibel zijn met voor actie bare beveiligings aanbevelingen
  - Aan beveiliging gerelateerde gegevens verzamelen en opslaan
- Bedreigings detectie en-beveiliging tussen tenants
  - Bedreigingen detecteren voor bronnen van tenants
  - Geavanceerde beveiliging tegen bedreigingen Toep assen, zoals just-in-time-VM-toegang
  - Configuratie van beveiligings groep met adaptieve netwerk beveiliging
  - Zorg ervoor dat servers alleen de toepassingen en processen uitvoeren die ze moeten hebben met adaptieve toepassings besturings elementen
  - Wijzigingen in belang rijke bestanden en register vermeldingen bewaken met FIM (File Integrity Monitoring)
- Houd er rekening mee dat het hele abonnement moet worden gedelegeerd naar de beheer Tenant. Azure Security Center scenario's worden niet ondersteund met gedelegeerde resource groepen

[Azure-Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Azure-Sentinel-resources beheren [in de tenants van de klant](../../sentinel/multiple-tenants-service-providers.md)
- [Aanvallen bijhouden en beveiligings waarschuwingen op meerdere tenants weer geven](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Incidenten weer geven](../../sentinel/multiple-workspace-view.md) in meerdere Sentinel-werk ruimten die over de tenants worden verspreid

[Azure service Health](../../service-health/index.yml):

- Bewaak de status van klant bronnen met Azure Resource Health
- De status bijhouden van de Azure-Services die door uw klanten worden gebruikt

[Azure site Recovery](../../site-recovery/index.yml):

- Opties voor herstel na nood gevallen voor virtuele Azure-machines in de tenants van de klant beheren (u kunt geen `RunAs` accounts gebruiken om VM-extensies te kopiëren)

[Azure virtual machines](../../virtual-machines/index.yml):

- Virtuele-machine uitbreidingen gebruiken om configuratie-en automatiserings taken na de implementatie op te geven op virtuele machines van Azure
- Diagnostische gegevens over opstarten gebruiken om problemen met Azure Vm's op te lossen
- Toegang tot Vm's met seriële console
- Integreer Vm's met Azure Key Vault voor wacht woorden, geheimen of cryptografische sleutels voor schijf versleuteling met behulp van [beheerde identiteit via beleid](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret), zodat geheimen worden opgeslagen in een Key Vault in de beheerde tenants
- Houd er rekening mee dat u Azure Active Directory niet kunt gebruiken voor externe aanmelding bij Vm's

Ondersteunings aanvragen:

- [Ondersteunings aanvragen openen vanuit **Help en ondersteuning**](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) in de Azure portal voor gedelegeerde resources (het ondersteunings abonnement selecteren dat beschikbaar is voor het gedelegeerde bereik)

## <a name="current-limitations"></a>Huidige beperkingen

Houd bij alle scenario's rekening met de volgende beperkingen:

- Aanvragen die door Azure Resource Manager worden verwerkt, kunnen worden uitgevoerd met behulp van Azure Lighthouse. De bewerkings-Uri's voor deze aanvragen beginnen met `https://management.azure.com` . Aanvragen die worden verwerkt door een exemplaar van een resource type (zoals Key Vault geheimen toegang of toegang tot opslag gegevens), worden echter niet ondersteund met Azure Lighthouse. De bewerkings-Uri's voor deze aanvragen beginnen meestal met een adres dat uniek is voor uw exemplaar, zoals `https://myaccount.blob.core.windows.net` of `https://mykeyvault.vault.azure.net/` . De laatste is ook gegevens bewerkingen in plaats van beheer bewerkingen.
- Roltoewijzingen moeten gebruikmaken [van ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md). Alle ingebouwde rollen worden momenteel ondersteund met Azure delegated resource management, met uitzonde ring van eigenaar of ingebouwde rollen met [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) machtiging. De rol beheerder van gebruikers toegang wordt alleen ondersteund voor beperkt gebruik bij het [toewijzen van rollen aan beheerde identiteiten](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Aangepaste rollen en [beheerders rollen voor klassieke abonnementen](../../role-based-access-control/classic-administrators.md) worden niet ondersteund.
- Hoewel u abonnementen kunt opdoen die gebruikmaken van Azure Databricks, kunnen gebruikers in de Tenant beheren Azure Databricks werk ruimten op dit moment niet starten.
- Hoewel u abonnementen en resource groepen met resource vergrendelingen kunt uitvoeren, worden deze vergren delingen niet voor komen dat door gebruikers in de Tenant beheren de acties worden uitgevoerd. [Weiger toewijzingen](../../role-based-access-control/deny-assignments.md) die door het systeem beheerde bronnen beveiligen, zoals die zijn gemaakt door door Azure beheerde toepassingen of Azure-blauw drukken (door het systeem toegewezen weigerings toewijzingen), voor komen dat gebruikers in de Tenant beheren op deze resources. op dit moment kunnen gebruikers in de Tenant van de klant echter geen eigen weigerings toewijzingen maken (toegewezen weigerings toewijzingen).

## <a name="next-steps"></a>Volgende stappen

- Laat uw klanten onboarden naar Azure Lighthouse, door [gebruik te maken van Azure Resource Manager sjablonen](../how-to/onboard-customer.md) of door [een aanbieding met persoonlijke of open bare services te publiceren op Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Bekijk en beheer klanten](../how-to/view-manage-customers.md) door naar **mijn klanten** te gaan in de Azure Portal.
