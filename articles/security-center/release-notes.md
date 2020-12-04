---
title: Releaseopmerkingen voor Azure Security Center
description: Een beschrijving van wat er nieuw is en gewijzigd is in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2020
ms.author: memildin
ms.openlocfilehash: f26fffe8e6b2be6ede064b4750d012b3e4535c66
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433322"
---
# <a name="whats-new-in-azure-security-center"></a>Wat is er nieuw in Azure Security Center?

Azure Security Center is in actieve ontwikkeling en wordt voortdurend verbeterd. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt deze pagina u informatie over nieuwe functies, opgeloste fouten en afgeschafte functionaliteit.

Deze pagina wordt regelmatig bijgewerkt. Kom hier daarom regelmatig terug. 

Zie [Belangrijke aanstaande wijzigingen aan Azure Security Center](upcoming-changes.md) voor meer informatie over *geplande* wijzigingen die binnenkort in Security Center worden doorgevoerd. 

> [!TIP]
> Als u op zoek bent naar items die ouder zijn dan zes maanden, vindt u deze in het [Archief voor nieuwe functies in Azure AD in Azure Security Center](release-notes-archive.md).


## <a name="november-2020"></a>November 2020

Updates in november omvatten:

- [Er zijn 29 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 is toegevoegd aan het nalevingsdashboard van de Security Center](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Er zijn filters opgenomen in de lijst met aanbevelingen](#recommendations-list-now-includes-filters)
- [De ervaring voor automatische inrichting is verbeterd en uitgebreid](#auto-provisioning-experience-improved-and-expanded)
- [Beveiligingsscore is nu beschikbaar in continue export (preview)](#secure-score-is-now-available-in-continuous-export-preview)
- [De aanbeveling 'Er moeten systeemupdates worden geïnstalleerd op uw computers' bevat nu subaanbevelingen](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations)
- [Op de pagina Beleidsbeheer in de Azure-portal wordt nu de status van standaardbeleidstoewijzingen weergegeven](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Er zijn 29 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen

Azure Security Benchmark is de door Microsoft ontworpen, Azure-specifieke set richtlijnen voor best practices voor beveiliging en naleving op basis van algemene nalevingsframeworks. [Meer informatie over Azure Security-benchmark](../security/benchmarks/introduction.md).

De volgende 29 preview-aanbevelingen zijn toegevoegd aan Security Center om de dekking van deze benchmark te verhogen.

Preview-aanbevelingen zorgen er niet voor dat een resource als beschadigd wordt weergegeven en ze worden niet opgenomen in de berekeningen van uw beveiligde score. Herstel ze waar mogelijk, zodat zij wanneer de preview-periode afloopt zullen bijdragen aan uw score. Zie [Aanbevelingen oplossen in Azure Security Center](security-center-remediate-recommendations.md) voor meer informatie over hoe u kunt reageren op deze aanbevelingen.

| Beveiligingsmaatregelen                     | Nieuwe aanbevelingen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Actieve gegevens versleutelen              | - SSL-verbinding afdwingen moet zijn ingeschakeld voor PostgreSQL-databaseservers<br>- SSL-verbinding afdwingen moet zijn ingeschakeld voor MySQL-databaseservers<br>- TLS moet zijn bijgewerkt naar de nieuwste versie van uw API-app<br>- TLS moet zijn bijgewerkt naar de nieuwste versie van uw functie-app<br>- TLS moet zijn bijgewerkt naar de nieuwste versie van uw web-app<br>- FTPS moet zijn vereist in uw API-app<br>- FTPS moet zijn vereist in uw functie-app<br>- FTPS moet zijn vereist in uw web-app                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Toegang en machtigingen beheren        | - Web-apps moeten een SSL-certificaat aanvragen voor alle inkomende aanvragen<br>- Er moet een beheerde identiteit worden gebruikt in uw API-app<br>- Er moet een beheerde identiteit worden gebruikt in uw functie-app<br>- Er moet een beheerde identiteit worden gebruikt in uw web-app                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Onbevoegde netwerktoegang beperken | - Het privé-eindpunt moet zijn ingeschakeld voor PostgreSQL-servers<br>- Het privé-eindpunt moet zijn ingeschakeld voor MariaDB-servers<br>- Het privé-eindpunt moet zijn ingeschakeld voor MySQL-servers                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| Controle en logboekregistratie inschakelen          | - Diagnostische logboeken in App Services moeten zijn ingeschakeld                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| Best practices voor beveiliging implementeren    | - Azure Backup moet zijn ingeschakeld voor virtuele machines<br>- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MariaDB<br>- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for MySQL<br>- Geografisch redundante back-up moet zijn ingeschakeld voor Azure Database for PostgreSQL<br>- PHP moet zijn bijgewerkt naar de nieuwste versie van uw API-app<br>- PHP moet zijn bijgewerkt naar de nieuwste versie van uw web-app<br>- Java moet zijn bijgewerkt naar de nieuwste versie van uw API-app<br>- Java moet zijn bijgewerkt naar de nieuwste versie van uw functie-app<br>- Java moet zijn bijgewerkt naar de nieuwste versie van uw web-app<br>- Python moet zijn bijgewerkt naar de nieuwste versie van uw API-app<br>- Python moet zijn bijgewerkt naar de nieuwste versie van uw functie-app<br>- Python moet zijn bijgewerkt naar de nieuwste versie van uw web-app<br>- Retentie voor controle voor SQL-servers moet zijn ingesteld op minstens 90 dagen |
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Gerelateerde links:

- [Meer informatie over Azure Security Benchmark](../security/benchmarks/introduction.md)
- [Meer informatie over Azure API-apps](../app-service/app-service-web-tutorial-rest-api.md)
- [Meer informatie over Azure functie-apps](../azure-functions/functions-overview.md)
- [Meer informatie over Azure web-apps](../app-service/overview.md)
- [Meer informatie over Azure Database for MariaDB](../mariadb/overview.md)
- [Meer informatie over Azure Database for MySQL](../mysql/overview.md)
- [Meer informatie over Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 is toegevoegd aan het nalevingsdashboard van Security Center

De NIST SP 800-171 R2-standaard is nu beschikbaar als ingebouwd initiatief voor gebruik met het nalevingsdashboard voor Azure Security Center. De toewijzingen voor de controles worden beschreven in [Details van het ingebouwde NIST SP 800-171 R2-nalevingsinitiatief](../governance/policy/samples/nist-sp-800-171-r2.md). 

Als u de standaard wilt toepassen op uw abonnementen en uw nalevingsstatus continu wilt monitoren, gebruikt u de instructies in [De set standaarden in uw nalevingsdasboard aanpassen](update-regulatory-compliance-packages.md).

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="De NIST SP 800 171 R2-standaard in het nalevingsdashboard van Security Center":::

Zie [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)voor meer informatie over deze nalevingsstandaard.


### <a name="recommendations-list-now-includes-filters"></a>Er zijn filters opgenomen in de lijst met aanbevelingen

U kunt de lijst met aanbevelingen voor de beveiliging nu filteren op verschillende criteria. In het volgende voorbeeld is de lijst met aanbevelingen gefilterd zodat er aanbevelingen worden weergegeven die voldoen aan de volgende criteria:

- Ze zijn **algemeen beschikbaar** (d.w.z. geen preview)
- Ze zijn bedoeld voor **opslagaccounts**
- Ze bieden ondersteuning voor **snelle oplossingen** voor herstel

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filters voor de lijst met aanbevelingen":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>De ervaring voor automatische inrichting is verbeterd en uitgebreid

Met de functie voor automatische inrichting kunt u de beheeroverhead verminderen door de vereiste extensies op nieuwe (en bestaande) Azure-VM's te installeren, zodat ze gebruik kunnen maken van de beschermingen van Security Center. 

Naarmate Azure Security Center is gegroeid, zijn er meer extensies ontwikkeld en kan Security Center een grotere lijst met resourcetypen bewaken. De hulpprogramma's voor automatische inrichting zijn nu uitgebreid, zodat aanvullende extensies en resourcetypen worden ondersteund door gebruik te maken van de mogelijkheden van Azure Policy.

U kunt nu automatische inrichting configureren voor:

- Log Analytics-agent
- (Nieuw) Azure Policy-invoegtoepassing voor Kubernetes
- (Nieuw) Microsoft Dependency Agent

Meer informatie vindt u in [Auto provisioning agents and extensions from Azure Security Center](security-center-enable-data-collection.md) (Automatische inrichting van agents en extensies van Azure Security Center).


### <a name="secure-score-is-now-available-in-continuous-export-preview"></a>Beveiligingsscore is nu beschikbaar in continue export (preview)

Met continue export van de beveiligingsscore kunt u wijzigingen in uw score in realtime streamen naar Azure Event Hubs of een Log Analytics-werkruimte. Gebruik deze mogelijkheid om:

- uw beveiligingsscore na verloop van tijd bij te houden met dynamische rapporten
- gegevens van de beveiligingsscore exporteren naar Azure Sentinel (of een andere SIEM)
- deze gegevens integreren met andere processen die u mogelijk al gebruikt om de beveiligingsscore in uw organisatie te bewaken

Meer informatie over hoe u [Security Center-gegevens continue exporteert](continuous-export.md).


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-sub-recommendations"></a>De aanbeveling 'Er moeten systeemupdates worden geïnstalleerd op uw computers' bevat nu subaanbevelingen

De aanbeveling **Er moeten systeemupdates worden geïnstalleerd op uw computers** is verbeterd. De nieuwe versie omvat subaanbevelingen voor elke ontbrekende update en biedt de volgende verbeteringen:

- Een opnieuw ontworpen ervaring op de Azure Security Center-pagina's van Azure Portal. De pagina met aanbevelingsinformatie voor **Er moeten systeemupdates worden geïnstalleerd op uw computer** bevat de lijst met resultaten zoals hieronder wordt weergegeven. Wanneer u één resultaat selecteert, wordt het deelvenster Details geopend met een koppeling naar de herstelgegevens en een lijst met betrokken resources.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Een van de subaanbevelingen in de portalervaring openen voor de bijgewerkte aanbeveling":::

- Uitgebreide gegevens voor de aanbeveling van Azure Resource Graph (ARG). ARG is een Azure-service die is ontworpen voor efficiëntere resourceverkenning. U kunt ARG gebruiken om op schaal een query uit te voeren in een bepaalde set abonnementen, zodat u uw omgeving effectief kunt beheren. 

    Voor Azure Security Center kunt u gebruikmaken van ARG en de [Kusto Query Language (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) om query's uit te voeren op een breed scala aan postuurgegevens.

    Als u in ARG eerder deze query uitvoerde op deze aanbeveling, was de enige beschikbare informatie dat de aanbeveling moet worden herstel op een machine. De volgende query van de verbeterde versie retourneert elke ontbrekende systeemupdate, gegroepeerd op machine.

    ```kusto
    securityresources
    | where type =~ "microsoft.security/assessments/subassessments"
    | where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
    | where properties.status.code == "Unhealthy"
    ```

### <a name="policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments"></a>Op de pagina Beleidsbeheer in de Azure-portal wordt nu de status van standaardbeleidstoewijzingen weergegeven

U kunt nu zien of het standaard Security Center-beleid is toegewezen aan uw abonnementen op de pagina **Beveiligingsbeleid** van het Security Center in de Azure-portal.

:::image type="content" source="media/release-notes/policy-assignment-info-per-subscription.png" alt-text="De pagina Beleidsbeheer van Azure Security Center met de standaardbeleidstoewijzingen":::

## <a name="october-2020"></a>Oktober 2020

De updates in oktober omvatten:
- [Evaluatie van beveiligingsproblemen voor on-premises en multi-cloudmachines (preview)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall-aanbeveling toegevoegd (preview)](#azure-firewall-recommendation-added-preview)
- [Aanbeveling Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services is bijgewerkt met een snelle oplossing](#authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix)
- [Het dashboard Naleving van regelgeving bevat nu een optie voor het verwijderen van standaarden](#regulatory-compliance-dashboard-now-includes-option-to-remove-standards)
- [Tabel Microsoft.Security/securityStatuses is verwijderd uit Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Evaluatie van beveiligingsproblemen voor on-premises en multi-cloudmachines (preview)

Met [Azure Defender voor servers](defender-for-servers-introduction.md) geïntegreerde evaluatie van beveiligingsproblemen (mogelijk gemaakt door Qualys) worden nu servers met Azure Arc gescand.

Wanneer u Azure Arc op uw niet-Azure-machines hebt ingeschakeld, kan de geïntegreerde scanner voor beveiligingsproblemen handmatig en op schaal worden geïmplementeerd.

Met deze update kunt u de mogelijkheden van **Azure Defender voor servers** gebruiken om uw beheerprogramma voor beveiligingsproblemen te consolideren voor al uw Azure- en niet-Azure-assets.

Belangrijkste functies:

- Bewaken van de inrichtingsstatus van de scanner voor de evaluatie van beveiligingsproblemen van Azure Arc-machines
- Inrichten van de geïntegreerde agent voor de evaluatie van beveiligingsproblemen op niet-beveiligde Azure Arc-machines onder Windows en Linux (handmatig en op schaal)
- Ontvangen en analyseren van gedetecteerde beveiligingsproblemen afkomstig van geïmplementeerde agents (handmatig en op schaal)
- Geïntegreerde ervaring voor Azure-VM's en Azure Arc-machines

[Meer informatie over het implementeren van de geïntegreerde scanner voor beveiligings problemen op uw hybride machines](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Meer informatie over servers met Azure Arc](https://docs.microsoft.com/azure/azure-arc/servers/).


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall-aanbeveling toegevoegd (preview)

Er is een nieuwe aanbeveling toegevoegd om al uw virtuele netwerken met Azure Firewall te beveiligen.

De aanbeveling, **Virtuele netwerken moeten worden beveiligd met Azure Firewall**, adviseert u om de toegang tot uw virtuele netwerken te beperken en mogelijke dreigingen te voorkomen door gebruik te maken van Azure Firewall.

Meer informatie over [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>De aanbeveling Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services is bijgewerkt met een snelle oplossing

De aanbeveling **Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services** bevat nu een snelle oplossing.

Zie [Aanbevelingen voor beveiliging: een naslaggids](recommendations-reference.md) voor meer informatie over deze aanbeveling en alle andere aanbevelingen van Security Center.

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Aanbeveling Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services met de snelle oplossing":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Het dashboard Naleving van regelgeving bevat nu een optie voor het verwijderen van standaarden

Het Dashboard Naleving van regelgeving van Security Center biedt inzicht in uw nalevingsstatus op basis van in hoeverre u aan specifieke nalevingsmechanismen en -vereisten voldoet.

Het dashboard bevat een vaste set reglementaire standaarden. Als een van de opgegeven standaarden niet relevant is voor uw organisatie, kunt u deze nu eenvoudig verwijderen uit de gebruikersinterface van een abonnement. Standaarden kunnen alleen worden verwijderd op het niveau van het *abonnement*, niet in het bereik van de beheergroep.

Meer informatie vindt u in [Removing a standard from your dashboard](update-regulatory-compliance-packages.md#removing-a-standard-from-your-dashboard) (Een standaard uit uw dashboard verwijderen).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabel Microsoft.Security/securityStatuses is verwijderd uit Azure Resource Graph (ARG)

Azure Resource Graph is een service in Azure. Het is ontworpen voor een efficiënte resourceverkenning en biedt de mogelijkheid query's op schaal uit te voeren binnen een bepaalde groep abonnementen, zodat u uw omgeving effectief kunt beheren. 

Voor Azure Security Center kunt u gebruikmaken van ARG en de [Kusto Query Language (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) om query's uit te voeren op een breed scala aan postuurgegevens. Bijvoorbeeld:

- De assetvoorraad maakt gebruik van ARG
- Er is een voorbeeld van een ARG-query gedocumenteerd voor het [identificeren van accounts zonder dat MFA (meervoudige verificatie) is ingeschakeld](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

ARG bevat tabellen met gegevens die u in uw query's kunt gebruiken.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer en de beschikbare tabellen":::

> [!TIP]
> In de ARG-documentatie vindt u een overzicht van alle beschikbare tabellen in de [Azure Resource Graph-tabel en de resourcetypeverwijzing](../governance/resource-graph/reference/supported-tables-resources.md).

Uit deze update is de tabel **Microsoft.Security/securityStatuses** verwijderd. De securityStatuses-API is nog steeds beschikbaar.

De tabel Microsoft.Security/Assessments kan gebruikmaken van gegevensvervanging.

Het belangrijkste verschil tussen Microsoft.Security/securityStatuses en Microsoft.Security/Assessments is dat de eerste een aggregatie van evaluaties toont en de tweede één record voor elke evaluatie bevat.

Zo retourneert bijvoorbeeld Microsoft.Security/securityStatuses een resultaat met een matrix met twee policyAssessments:

```
{
id: "/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet",
name: "mico-rg-vnet",
type: "Microsoft.Security/securityStatuses",
properties:  {
    policyAssessments: [
        {assessmentKey: "e3deicce-f4dd-3b34-e496-8b5381bazd7e", category: "Networking", policyName: "Azure DDOS Protection Standard should be enabled",...},
        {assessmentKey: "sefac66a-1ec5-b063-a824-eb28671dc527", category: "Compute", policyName: "",...}
    ],
    securitystateByCategory: [{category: "Networking", securityState: "None" }, {category: "Compute",...],
    name: "GenericResourceHealthProperties",
    type: "VirtualNetwork",
    securitystate: "High"
}
```
Microsoft.Security/Assessments daarentegen bevat een record voor elk van een dergelijke beleidsevaluatie. Dit werkt als volgt:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Voorbeeld van het converteren van een bestaande ARG-query met behulp van securityStatuses om nu de tabel met evaluaties te kunnen gebruiken:**

Query die verwijst naar SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Vervangingsquery voor de tabel met evaluaties:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Meer informatie vindt u via de volgende koppelingen:
- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Kusto-querytaal (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>September 2020

De updates in september zijn onder meer:
- [Security Center krijgt een nieuwe look.](#security-center-gets-a-new-look)
- [Azure Defender uitgebracht](#azure-defender-released)
- [Azure Defender voor Key Vault is algemeen verkrijgbaar](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender voor opslagbeveiliging voor bestanden en ADLS Gen2 is algemeen beschikbaar](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Hulpprogramma's voor assetinventarisatie zijn nu algemeen beschikbaar](#asset-inventory-tools-are-now-generally-available)
- [Een specifiek gevonden beveiligingsprobleem voor scans van containerregisters en virtuele machines uitschakelen](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Een resource uitsluiten van een aanbeveling](#exempt-a-resource-from-a-recommendation)
- [AWS- en GCP-connectors in Security Center bieden een multi-cloudervaring](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Bundel met aanbevelingen voor Kubernetes-workloadbeveiliging](#kubernetes-workload-protection-recommendation-bundle)
- [Resultaten van evaluatie van beveiligingsproblemen zijn nu beschikbaar in continue export](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Onjuiste beveiligingsconfiguraties voorkomen door aanbevelingen af te dwingen bij het maken van nieuwe resources](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Aanbevelingen voor netwerkbeveiligingsgroep verbeterd](#network-security-group-recommendations-improved)
- [Aanbeveling AKS-preview 'Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services' afgeschaft'](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [E-mailmeldingen van Azure Security Center verbeterd](#email-notifications-from-azure-security-center-improved)
- [Beveiligingsscore bevat geen preview-aanbevelingen](#secure-score-doesnt-include-preview-recommendations)
- [Aanbevelingen bevatten nu een ernst-indicator en vernieuwingsinterval](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center krijgt een nieuwe look.

We hebben een vernieuwde gebruikersinterface voor de portalpagina's van Security Center uitgebracht. De nieuwe pagina's bevatten een nieuwe overzichtspagina en dashboards voor beveiligingsscore, assetinventarisatie en Azure Defender.

De opnieuw ontworpen overzichtspagina heeft nu een tegel voor toegang tot de dashboards voor de beveiligingsscore, assetinventarisatie en Azure Defender. Het bevat ook een tegel met een koppeling naar het dashboard voor naleving van regelgeving.

Meer informatie over de [overzichtspagina](overview-page.md).


### <a name="azure-defender-released"></a>Azure Defender uitgebracht

**Azure Defender** is het Cloud Workload Protection Platform (CWPP) dat is geïntegreerd in Security Center voor geavanceerde, intelligente beveiliging van uw Azure- en hybride workloads. Het vervangt de standaardprijscategorie van Security Center. 

Als u Azure Defender inschakelt in het gedeelte **Prijzen en instellingen** van Azure Security Center, worden de volgende Defender-abonnementen allemaal tegelijkertijd ingeschakeld en kunt u gebruikmaken van een totaaloplossing voor de bescherming van de berekenings-, gegevens- en servicelagen in uw omgeving:

- [Azure Defender voor servers](defender-for-servers-introduction.md)
- [Azure Defender voor App Service](defender-for-app-service-introduction.md)
- [Azure Defender voor Storage](defender-for-storage-introduction.md)
- [Azure Defender voor SQL](defender-for-sql-introduction.md)
- [Azure Defender voor Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender voor containerregisters](defender-for-container-registries-introduction.md)

Elk van deze abonnementen wordt afzonderlijk beschreven in de documentatie voor Security Center.

Met zijn speciale dashboard biedt Azure Defender beveiligingswaarschuwingen en geavanceerde bescherming tegen dreigingen voor virtuele machines, SQL databases, containers, webtoepassingen, uw netwerk en meer.

[Meer informatie over Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender voor Key Vault is algemeen verkrijgbaar

Azure Key Vault is een cloudservice die versleutelingssleutels en geheimen, zoals certificaten, verbindingsreeksen en wachtwoorden, beveiligt. 

**Azure Defender voor Key Vault** biedt voor Azure systeemeigen, geavanceerde beveiliging tegen bedreigingen voor Azure Key Vault, waarmee u een extra laag beveiligingsinformatie kunt leveren. Als uitbreiding biedt Azure Defender voor Key Vault consequent bescherming aan veel van de resources die afhankelijk zijn van uw Key Vault-accounts.

Het optionele abonnement is nu algemeen beschikbaar. Deze functie was in preview als 'geavanceerde beveiliging tegen bedreigingen voor Azure Key Vault'.

Daarnaast bevatten de Key Vault-pagina's in Azure Portal nu een speciale pagina **Beveiliging** voor aanbevelingen en waarschuwingen van **Security Center**.

Meer informatie vindt u in [Azure Defender voor Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender voor opslagbeveiliging voor bestanden en ADLS Gen2 is algemeen beschikbaar 

**Azure Defender for Storage** detecteert potentieel schadelijke activiteiten in uw Azure Storage-accounts. Uw gegevens kunnen worden beschermd, ongeacht of deze zijn opgeslagen als blobcontainers, bestandsshares of data lakes.

Ondersteuning voor [Azure Files](../storage/files/storage-files-introduction.md) en [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) is nu algemeen beschikbaar.

Vanaf 1 oktober 2020 gaan we kosten in rekening brengen voor de beveiliging van resources in deze services.

Meer informatie vindt u in [Azure Defender voor Storage](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Hulpprogramma's voor assetinventarisatie zijn nu algemeen beschikbaar

De pagina voor assetinventarisatie van Azure Security Center biedt één pagina voor het weergeven van het beveiligingspostuur van de resources die u hebt verbonden met Security Center.

Security Center analyseert periodiek de beveiligingsstatus van uw Azure-resources om mogelijke beveiligingsproblemen op te sporen. Vervolgens krijgt u aanbevelingen voor het oplossen van deze beveiligingsproblemen.

Wanneer een resource openstaande aanbevelingen heeft, worden deze weergegeven in de inventarisatie.

Meer informatie vindt u in [Uw resources verkennen en beheren met hulpprogramma's voor assetinventarisatie en beheer](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Een specifiek gevonden beveiligingsprobleem voor scans van containerregisters en virtuele machines uitschakelen

Azure Defender bevat scanners voor beveiligingsproblemen om installatiekopieën in uw Azure Container Registry en uw virtuele machines te scannen.

Als u een organisatorische behoefte hebt om een resultaat te negeren in plaats van dit te herstellen, kunt u het eventueel uitschakelen. Uitgeschakelde resultaten hebben geen invloed op uw beveiligingsscore en genereren geen ongewenste ruis.

Wanneer een resultaat overeenkomt met de criteria die u hebt gedefinieerd in de regels voor uitschakelen, wordt dit niet weergegeven in de lijst met resultaten.

Deze optie is beschikbaar op de pagina met aanbevelingsdetails voor:

- **Beveiligingsproblemen met installatiekopieën in Azure Container Registry moeten worden hersteld**
- **Beveiligingsproblemen op uw virtuele machines moeten worden hersteld**

Meer informatie vindt u in [Specifieke resultaten voor uw containerinstallatiekopieën uitschakelen](defender-for-container-registries-usage.md#disable-specific-findings-preview) en [Specifieke resultaten voor uw virtuele machines uitschakelen](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Een resource uitsluiten van een aanbeveling

In sommige gevallen wordt een resource vermeld als niet in orde met betrekking tot een specifieke aanbeveling (en waardoor uw beveiligingsscore lager wordt), zelfs als u denkt dat dit niet zo zou moeten zijn. Dit kan zijn opgelost door een proces dat niet wordt bijgehouden door Security Center. Of misschien heeft uw organisatie besloten het risico voor die specifieke resource te accepteren. 

In dergelijke gevallen kunt u een uitzonderingsregel maken en ervoor zorgen dat de resource in de toekomst niet wordt vermeld als een resource die niet in orde is. Deze regels kunnen gedocumenteerde redenen bevatten, zoals hieronder wordt beschreven.

Meer informatie vindt u in [Een resource uitsluiten van aanbevelingen en de beveiligingsscore](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>AWS- en GCP-connectors in Security Center bieden een multi-cloudervaring

Veel workloads in de cloud beslaan meerdere cloudplatforms, dus moeten cloudbeveiligingsservices hetzelfde doen.

Azure Security Center biedt nu bescherming voor workloads in Azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

Met de onboarding van uw AWS- en GCP-accounts in Security Center worden AWS Security Hub, GCP Security Command en Azure Security Center geïntegreerd. 

Meer informatie vindt u in [Uw AWS-accounts verbinden met Azure Security Center](quickstart-onboard-aws.md) en [Uw GCP-accounts verbinden met Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Bundel met aanbevelingen voor Kubernetes-workloadbeveiliging

Om ervoor te zorgen dat Kubernetes-workloads standaard veilig zijn, voegt Security Center aanbevelingen voor beveiliging op Kubernetes-niveau toe, met inbegrip van afdwingingsopties met Kubernetes Admission Control.

Wanneer u de Azure Policy-invoegtoepassing voor Kubernetes op uw AKS-cluster hebt geïnstalleerd, wordt elke aanvraag voor de Kubernetes API-server gecontroleerd op basis van de vooraf gedefinieerde set aanbevolen procedures voordat deze naar het cluster gaat. Vervolgens kunt u instellingen configureren om de aanbevolen procedures af te dwingen en ze te verplichten voor toekomstige workloads.

U kunt er bijvoorbeeld voor zorgen dat containers met machtigingen niet moeten worden gemaakt, en toekomstige aanvragen hiervoor worden dan geblokkeerd.

Meer informatie vindt u in [Aanbevolen procedures voor workloadbeveiliging met behulp van Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Resultaten van evaluatie van beveiligingsproblemen zijn nu beschikbaar in continue export

Maak gebruik van continue export om uw waarschuwingen en aanbevelingen in realtime te streamen naar Azure Event Hubs, Log Analytics-werkruimten of Azure Monitor. Daar kunt u deze gegevens integreren met SIEM's (zoals Azure Sentinel, Power BI, Azure Data Explorer en meer).

De hulpprogramma's voor evaluatie van beveiligingsproblemen van Security Center retourneren informatie over uw resources als aanbevelingen voor het uitvoeren van acties in een 'hoofdaanbeveling', zoals 'beveiligingsproblemen op uw virtuele machines, moeten worden hersteld'. 

De beveiligingsresultaten zijn nu beschikbaar voor export door middel van continue export wanneer u aanbevelingen selecteert en de optie **Beveiligingsresultaten insluiten** in te schakelen.

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="De schakeloptie Beveiligingsresultaten insluiten in de configuratie voor continue export" :::

Gerelateerde pagina's:

- [De geïntegreerde oplossing van Security Center voor de evaluatie van beveiligingsproblemen met virtuele Azure-machines](deploy-vulnerability-assessment-vm.md)
- [De geïntegreerde oplossing van Security Center voor de evaluatie van beveiligingsproblemen met Azure Container Registry](defender-for-container-registries-usage.md)
- [Continue export](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Onjuiste beveiligingsconfiguraties voorkomen door aanbevelingen af te dwingen bij het maken van nieuwe resources

Onjuiste beveiligingsconfiguraties zijn een belangrijke oorzaak van beveiligingsincidenten. Security Center biedt nu de mogelijkheid om onjuiste configuratie van nieuwe resources met betrekking tot specifieke aanbevelingen te helpen *voorkomen*. 

Deze functie kan u helpen uw workloads veilig te houden en uw beveiligingsscore stabiel te houden.

Het afdwingen van een veilige configuratie op basis van een specifieke aanbeveling wordt aangeboden in twee modi:

- Met behulp van de optie **Weigeren** van Azure Policy, kunt u het maken van resources die niet in orde zijn, stoppen

- Met de optie **Afdwingen** kunt u profiteren van het effect **DeployIfNotExist** van Azure Policy en niet-compatibele resources automatisch herstellen wanneer deze worden gemaakt
 
Dit is beschikbaar voor geselecteerde beveiligingsaanbevelingen en is bovenaan de pagina met resourcedetails te vinden.

Meer informatie vindt u in [Onjuiste configuraties voorkomen met de aanbevelingen voor afdwingen/weigeren](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Aanbevelingen voor netwerkbeveiligingsgroep verbeterd

De volgende beveiligingsaanbevelingen met betrekking tot netwerkbeveiligingsgroepen zijn verbeterd om enkele gevallen van fout-positieven te verminderen.

- Alle netwerkpoorten moeten worden beperkt voor de netwerkbeveiligingsgroep die is gekoppeld aan uw VM
- Beheerpoorten moeten gesloten zijn op uw virtuele machines
- Op internet gerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen
- Subnetten moeten worden gekoppeld aan een netwerkbeveiligingsgroep


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Aanbeveling AKS-preview 'Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services' afgeschaft

De preview-aanbeveling 'Beveiligingsbeleid voor pods moet worden gedefinieerd voor Kubernetes Services' wordt afgeschaft zoals beschreven in de documentatie van [Azure Kubernetes Service](../aks/use-pod-security-policies.md).

De functie voor beveiligingsbeleid voor pods (preview) wordt afgeschaft en is na 15 oktober 2020 niet langer beschikbaar in Azure Policy voor AKS.

Nadat de functie voor beveiligingsbeleid voor pods (preview) is afgeschaft, moet u de functie op alle bestaande clusters uitschakelen met behulp van de afgeschafte functie om toekomstige clusterupgrades uit te voeren en de ondersteuning van Azure te kunnen blijven gebruiken.


### <a name="email-notifications-from-azure-security-center-improved"></a>E-mailmeldingen van Azure Security Center verbeterd

E-mailmeldingen met betrekking tot beveiligingswaarschuwingen zijn als volgt verbeterd: 

- De mogelijkheid voor het verzenden van e-mailmeldingen over waarschuwingen voor alle ernstniveaus is toegevoegd
- De mogelijkheid om gebruikers op de hoogte te stellen van verschillende Azure-rollen in het abonnement is toegevoegd
- We informeren abonnementseigenaren standaard proactief over waarschuwingen met een hoge urgentie (die een hoge waarschijnlijkheid hebben een echte inbreuk te zijn)
- Het telefoonnummerveld is verwijderd van de configuratiepagina voor e-mailmeldingen

Meer informatie vindt u in [E-mailmeldingen instellen voor beveiligingswaarschuwingen](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Beveiligingsscore bevat geen preview-aanbevelingen 

Security Center controleert uw resources, abonnementen en organisatie doorlopend op beveiligingsproblemen. Vervolgens worden alle bevindingen tot één enkele score samengevoegd, zodat u in een oogopslag uw huidige beveiligingssituatie kunt zien: hoe hoger de score, hoe lager het geïdentificeerde risiconiveau is.

Als er nieuwe bedreigingen worden gedetecteerd, wordt nieuw beveiligingsadvies beschikbaar gesteld in Security Center via nieuwe aanbevelingen. Om te voorkomen dat uw beveiligingsscore plotseling verandert, en om een respijtperiode te bieden waarin u nieuwe aanbevelingen kunt verkennen voordat ze van invloed zijn op uw scores, worden de aanbevelingen die zijn gemarkeerd als **Preview** niet meer opgenomen in de berekeningen van uw beveiligingsscore. Ze moeten, waar mogelijk, nog steeds worden hersteld. Wanneer de preview-periode is afgelopen, worden ze dus meegerekend in uw score.

Daarnaast worden in **Preview**-aanbevelingen resources niet als 'Niet in orde' weergegeven.

Een voorbeeld van een preview-aanbeveling:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Aanbeveling met de preview-markering":::

[Meer informatie over de beveiligingsscore](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Aanbevelingen bevatten nu een ernst-indicator en vernieuwingsinterval

De detailpagina voor aanbevelingen bevat nu een indicator voor het vernieuwingsinterval (indien relevant) en een duidelijke weergave van de ernst van de aanbeveling.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Aanbevelingspagina met vernieuwingsinterval en ernst":::



## <a name="august-2020"></a>Augustus 2020

De updates in augustus zijn onder meer:

- [Inventarisatie van activa - krachtige nieuwe weergave van het beveiligingspostuur van uw assets](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Ondersteuning toegevoegd voor standaardinstellingen voor beveiliging van Azure Active Directory (voor meervoudige verificatie)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Aanbeveling voor service-principals toegevoegd](#service-principals-recommendation-added)
- [Evaluatie van beveiligingsproblemen op VM's - aanbevelingen en beleidsregels geconsolideerd](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nieuw beveiligingsbeleid voor AKS toegevoegd aan ASC-standaardinitiatief: alleen voor gebruik door klanten van beperkte preview](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Inventarisatie van activa - krachtige nieuwe weergave van het beveiligingspostuur van uw assets

Assetinventarisatie van Azure Security Center (momenteel in preview) biedt een manier om het beveiligingspostuur weer te geven van de resources die u hebt verbonden met Security Center.

Security Center analyseert periodiek de beveiligingsstatus van uw Azure-resources om mogelijke beveiligingsproblemen op te sporen. Vervolgens krijgt u aanbevelingen voor het oplossen van deze beveiligingsproblemen. Wanneer een resource openstaande aanbevelingen heeft, worden deze weergegeven in de inventarisatie.

U kunt de weergave en de filters gebruiken om uw beveiligingspostuur te verkennen en verdere acties uit te voeren op basis van uw bevindingen.

Meer informatie over [assetinventarisatie](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Ondersteuning toegevoegd voor standaardinstellingen voor beveiliging van Azure Active Directory (voor meervoudige verificatie)

Security Center heeft volledige ondersteuning toegevoegd voor [standaardinstellingen voor de beveiliging](../active-directory/fundamentals/concept-fundamentals-security-defaults.md), de gratis identiteitsbeveiliging van Microsoft.

Standaardwaarden voor beveiliging bieden vooraf geconfigureerde instellingen voor identiteitsbeveiliging om uw organisatie te beschermen tegen algemene identiteitsgerelateerde aanvallen. Standaardinstellingen voor beveiliging beschermen al meer dan 5 miljoen tenants in totaal; 50.000-tenants worden ook beveiligd door Security Center.

Security Center biedt nu een beveiligingsaanbeveling wanneer een Azure-abonnement wordt geïdentificeerd waarvoor geen standaardinstellingen voor beveiliging zijn ingeschakeld. Tot nu toe gaf Security Center de aanbeveling meervoudige verificatie in te schakelen met behulp van voorwaardelijke toegang, dat onderdeel is van de Azure Active Directory (AD) Premium-licentie. Klanten die gratis Azure AD gebruiken, raden we nu aan om de standaardinstellingen voor beveiliging in te schakelen. 

Ons doel is om meer klanten te stimuleren om hun cloudomgevingen met meervoudige verificatie te beveiligen en een van de grootste risico's te beperken die ook het meest van invloed zijn op uw [beveiligingsscore](secure-score-security-controls.md).

Meer informatie over [standaardinstellingen voor beveiliging](../active-directory/fundamentals/concept-fundamentals-security-defaults.md).


### <a name="service-principals-recommendation-added"></a>Aanbeveling voor service-principals toegevoegd

Er is een nieuwe aanbeveling toegevoegd die Security Center-klanten die beheercertificaten gebruiken om hun abonnementen te beheren, aanraadt om over te schakelen naar service-principals.

De aanbeveling, **Gebruik service-principals om uw abonnementen te beschermen, geen beheercertificaten**, adviseert u service-principals of Azure Resource Manager te gebruiken om uw abonnementen veiliger te beheren. 

Meer informatie vindt u in [Toepassings- en service-principal-objecten in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Evaluatie van beveiligingsproblemen op VM's - aanbevelingen en beleidsregels geconsolideerd

Security Center inspecteert uw VM's om te detecteren of ze een oplossing voor de evaluatie van beveiligingsproblemen uitvoeren. Als er geen oplossing voor de evaluatie van beveiligingsproblemen wordt gevonden, biedt Security Center een aanbeveling om de implementatie te vereenvoudigen.

Als er beveiligingsproblemen worden gevonden, wordt in Security Center een aanbeveling gegeven waarin de resultaten worden beschreven die u zo nodig kunt onderzoeken en herstellen.

Om ervoor te zorgen dat alle gebruikers een consistente ervaring hebben, ongeacht het type scanner dat ze gebruiken, hebben we vier aanbevelingen geïntegreerd in de volgende twee:

|Uniforme aanbeveling|Beschrijving wijziging|
|----|:----|
|**A vulnerability assessment solution should be enabled on your virtual machines** (Er moet een oplossing voor de evaluatie van beveiligingsproblemen op uw virtuele machines worden ingeschakeld)|Vervangt de volgende twee aanbevelingen:<br> **•** De ingebouwde oplossing voor evaluatie van beveiligingsproblemen inschakelen op virtuele machines (mogelijk gemaakt door Qualys) (nu afgeschaft) (opgenomen in Standaard-prijscategorie)<br> **•** Er moet een oplossing voor de evaluatie van beveiligingsproblemen op uw virtuele machines moeten worden geïnstalleerd (nu afgeschaft) (opgenomen in Standaard- en gratis prijscategorie)|
|**Beveiligingsproblemen op uw virtuele machines moeten worden hersteld**|Vervangt de volgende twee aanbevelingen:<br>**•** Beveiligingsproblemen op uw virtuele machines herstellen (mogelijk gemaakt door Qualys) (nu afgeschaft)<br>**•** Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen (nu afgeschaft)|
|||

U gebruikt nu dezelfde aanbeveling om de uitbreiding voor de evaluatie van beveiligingsproblemen van Security Center of een oplossing met een eigen licentie ("BYOL") van een partner, zoals Qualys of Rapid7, te implementeren.

Wanneer er beveiligingsproblemen worden gevonden en gerapporteerd aan Security Center, wordt u door één aanbeveling gewaarschuwd voor de bevindingen, ongeacht de oplossing voor de evaluatie van beveiligingsproblemen die deze heeft gesignaleerd.

#### <a name="updating-dependencies"></a>Afhankelijkheden bijwerken

Als u scripts, query's of automatiseringen hebt die verwijzen naar de vorige aanbevelingen of beleidssleutels/-namen, gebruikt u de onderstaande tabellen om de verwijzingen bij te werken:

##### <a name="before-august-2020"></a>Vóór augustus 2020

|Aanbeveling|Bereik|
|----|:----|
|**De ingebouwde oplossing voor evaluatie van beveiligingsproblemen inschakelen op virtuele machines (mogelijk gemaakt door Qualys)**<br>Sleutel: 550e890b-e652-4d22-8274-60b3bdb24c63|Ingebouwd|
|**Beveiligingsproblemen op uw virtuele machines herstellen (mogelijk gemaakt door Qualys)**<br>Sleutel: 1195afff-c881-495e-9bc5-1486211ae03f|Ingebouwd|
|**Er moet een oplossing voor de evaluatie van beveiligingsproblemen op uw virtuele machines moeten worden geïnstalleerd**<br>Sleutel: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL (Bring Your Own License)|
|**Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen**<br>Sleutel: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL (Bring Your Own License)|
||||


|Beleid|Bereik|
|----|:----|
|**Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines**<br>Beleids-id: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Ingebouwd|
|**Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen**<br>Beleids-id: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL (Bring Your Own License)|
||||


##### <a name="from-august-2020"></a>Vanaf augustus 2020

|Aanbeveling|Bereik|
|----|:----|
|**A vulnerability assessment solution should be enabled on your virtual machines** (Er moet een oplossing voor de evaluatie van beveiligingsproblemen op uw virtuele machines worden ingeschakeld)<br>Sleutel: ffff0522-1e88-47fc-8382-2a80ba848f5d|Ingebouwd en BYOL|
|**Beveiligingsproblemen op uw virtuele machines moeten worden hersteld**<br>Sleutel: 1195afff-c881-495e-9bc5-1486211ae03f|Ingebouwd en BYOL|
||||

|Beleid|Bereik|
|----|:----|
|[**Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Beleids-id: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Ingebouwd en BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nieuw beveiligingsbeleid voor AKS toegevoegd aan ASC-standaardinitiatief: alleen voor gebruik door klanten van beperkte preview

Om ervoor te zorgen dat Kubernetes-workloads standaard veilig zijn, voegt Security Center beleid en aanbevelingen voor beveiliging op Kubernetes-niveau toe, met inbegrip van afdwingingsopties met Kubernetes Admission Control.

De vroege fase van dit project bevat een beperkte preview en het toevoegen van nieuwe beleidsregels (standaard uitgeschakeld) aan het ASC-standaardinitiatief.

U kunt dit beleid veilig negeren en dit heeft geen invloed op uw omgeving. Als u het wilt inschakelen, meldt u zich voor de preview-versie aan op https://aka.ms/SecurityPrP en selecteert u een van de volgende opties:

1. **Enkele preview-** : alleen deelnemen aan deze beperkte preview. Vermeld expliciet 'ASC doorlopend scannen' als de preview waaraan u wilt deelnemen.
1. **Doorlopend programma**: om deel te nemen aan deze en toekomstige beperkte previews. U moet een profiel en een privacyverklaring invullen.


## <a name="july-2020"></a>Juli 2020

De updates in juli zijn onder meer:
- [De evaluatie van beveiligingsproblemen voor virtuele machines is nu beschikbaar voor niet-Marketplace-installatiekopieën](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Beveiliging tegen bedreigingen voor Azure Storage is uitgebreid tot Azure Files en Azure Data Lake Storage Gen2 (preview)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Acht nieuwe aanbevelingen voor het inschakelen van beveiligingsfuncties voor bedreigingen](#eight-new-recommendations-to-enable-threat-protection-features)
- [Verbeteringen in de containerbeveiliging - sneller zoeken in het register en vernieuwde documentatie](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Adaptieve toepassingsregelaars bijgewerkt met een nieuwe aanbeveling en ondersteuning voor jokertekens in padregels](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Zes beleidsregels voor Advanced Data Security van SQL afgeschaft](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>De evaluatie van beveiligingsproblemen voor virtuele machines is nu beschikbaar voor niet-Marketplace-installatiekopieën

Bij het implementeren van een oplossing voor de evaluatie van beveiligingsproblemen voerde Security Center eerder een validatiecontrole uit voorafgaand aan implementatie. De controle was om te bevestigen dat de virtuele doelmachine een Marketplace-SKU bevatte. 

Met deze update is de controle verwijderd en kunt u nu hulpprogramma's voor de evaluatie van beveiligingsproblemen implementeren op 'aangepaste' Windows- en Linux-machines. Aangepaste installatiekopieën zijn bestanden die u hebt gewijzigd op basis van de standaardinstellingen voor Marketplace.

Hoewel u nu de geïntegreerde uitbreiding van de evaluatie van beveiligingsproblemen kunt implementeren (mogelijk gemaakt door Qualys) op veel meer computers, is ondersteuning alleen beschikbaar als u een besturingssysteem gebruikt dat wordt vermeld in [De geïntegreerde scanner voor beveiligingsproblemen implementeren op virtuele machines van de Standaard-laag](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines)

Meer informatie over de [geïntegreerde beveiligingsscanner voor virtuele machines (vereist Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Meer informatie over het gebruik van uw eigen oplossing voor de evaluatie van beveiligingsproblemen met een privélicentie van Qualys of Rapid7 vindt u in [Een oplossing voor het scannen van problemen van een partner implementeren](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Beveiliging tegen bedreigingen voor Azure Storage is uitgebreid tot Azure Files en Azure Data Lake Storage Gen2 (preview)

Beveiliging tegen bedreigingen voor Azure Storage detecteert potentieel schadelijke activiteiten in uw Azure Storage-accounts. Security Center geeft waarschuwingen weer wanneer er wordt geprobeerd toegang te krijgen tot uw opslagaccounts. 

Uw gegevens kunnen worden beschermd, ongeacht of deze zijn opgeslagen als blobcontainers, bestandsshares of data lakes.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Acht nieuwe aanbevelingen voor het inschakelen van beveiligingsfuncties voor bedreigingen

Er zijn acht nieuwe aanbevelingen toegevoegd om een eenvoudige manier te bieden om beveiligingsfuncties van Azure Security Center voor de volgende typen resources in te schakelen: virtuele machines, App Service-abonnementen, Azure SQL Database-servers, SQL-servers op computers, Azure Storage-accounts, Azure Kubernetes Service-clusters, Azure Container Registry-registers en Azure Key Vault-kluizen.

De nieuwe aanbevelingen zijn:

- **Advanced Data Security moet zijn ingeschakeld voor Azure SQL Database-servers**
- **Advanced Data Security moet zijn ingeschakeld voor SQL-servers op computers**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure App Service-plannen**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Container Registry-registers**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Key Vault-kluizen**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Kubernetes Service-clusters**
- **Advanced Threat Protection moet zijn ingeschakeld voor Azure Storage-accounts**
- **Advanced Thread Protection moet zijn ingeschakeld op virtuele machines**

Deze nieuwe aanbevelingen maken deel uit van het beveiligingsbeheer **Azure Defender inschakelen**.

De aanbevelingen bevatten ook de mogelijkheid voor snelle oplossingen. 

> [!IMPORTANT]
> Als u een van deze aanbevelingen herstelt, worden er kosten in rekening gebracht voor de beveiliging van de relevante resources. Deze kosten worden onmiddellijk van kracht als er gerelateerde resources zijn in het huidige abonnement. Of in de toekomst, als u ze op een later tijdstip toevoegt.
> 
> Als u bijvoorbeeld geen Azure Kubernetes Service-clusters in uw abonnement hebt en u de beveiliging tegen bedreigingen inschakelt, worden er geen kosten in rekening gebracht. Als u in de toekomst een cluster toevoegt aan dit abonnement, wordt dit automatisch beveiligd en worden de kosten op dat moment gestart.

Meer informatie hierover vindt u op de [pagina met naslaginformatie over beveiligingsaanbevelingen](recommendations-reference.md).

Meer informatie over [bescherming tegen bedreigingen in Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Verbeteringen in de containerbeveiliging - sneller zoeken in het register en vernieuwde documentatie

Als onderdeel van de continue investeringen in het domein van containerbeveiliging zijn we blij een aanzienlijke prestatieverbetering in de dynamische scans van Security Center-containerinstallatiekopieën die zijn opgeslagen in Azure Container Registry te kunnen delen. Scans zijn nu doorgaans in ongeveer twee minuten voltooid. In sommige gevallen kunnen ze maximaal 15 minuten duren.

Ter verbetering van de duidelijkheid en richtlijnen met betrekking tot de beveiligingsmogelijkheden van containers van Azure Security Center, hebben we ook de pagina's met documentatie over containerbeveiliging vernieuwd. 

Zie de volgende artikelen voor meer informatie over containerbeveiliging van Security Center:

- [Overzicht van beveiligingsfuncties voor containers van Security Center](container-security.md)
- [Details van de integratie met Azure Container Registry](defender-for-container-registries-introduction.md)
- [Details van de integratie met Azure Kubernetes Service](defender-for-kubernetes-introduction.md)
- [Uw registers scannen en uw Docker-hosts beveiligen](container-security.md)
- [Beveiligingswaarschuwingen van de functies voor beveiliging tegen bedreigingen voor Azure Kubernetes Service clusters](alerts-reference.md#alerts-akscluster)
- [Beveiligingswaarschuwingen van de functies voor beveiliging tegen bedreigingen voor Azure Kubernetes Service-hosts](alerts-reference.md#alerts-containerhost)
- [Beveiligingsaanbevelingen voor containers](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Adaptieve toepassingsregelaars bijgewerkt met een nieuwe aanbeveling en ondersteuning voor jokertekens in padregels

De functie voor adaptieve toepassingsregelaars heeft twee belangrijke updates ontvangen:

* Een nieuwe aanbeveling duidt mogelijk legitiem gedrag aan dat nog niet is toegestaan. De nieuwe aanbeveling, **De Allowlist-regels in uw beleid voor adaptief toepassingsbeheer moeten worden bijgewerkt**, vraagt u om nieuwe regels aan het bestaande beleid toe te voegen om het aantal fout-positieven in adaptieve toepassingsregelaars te verminderen.

* Padregels ondersteunen nu jokertekens. Vanaf deze update kunt u regels voor toegestane paden configureren met behulp van jokertekens. Er zijn twee ondersteunde scenario's:

    * Een jokerteken aan het einde van een pad gebruiken om alle uitvoerbare bestanden in deze map en submappen toe te staan

    * Een jokerteken in het midden van een pad gebruiken om het mogelijk te maken een bekende naam van een uitvoerbaar bestand te gebruiken met een veranderende mapnaam (bijvoorbeeld persoonlijke gebruikersmappen met een bekend uitvoerbaar bestand, automatisch gegenereerde mapnamen, enzovoort).


[Meer informatie over adaptieve toepassingsregelaars](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Zes beleidsregels voor Advanced Data Security van SQL afgeschaft

Er worden zes beleidsregels met betrekking tot geavanceerde gegevensbeveiliging voor SQL-machines afgeschaft:

- Advanced Threat Protection-typen moeten zijn ingesteld op 'Alles' in de Advanced Data Security-instellingen van SQL Managed Instance
- Advanced Threat Protection-typen moeten worden ingesteld op 'Alles' in de Advanced Data Security-instellingen van SQL Server
- De instellingen voor Advanced Data Security voor het beheerde SQL-exemplaar moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- De Advanced Data Security-instellingen voor SQL Server moeten een e-mailadres bevatten om beveiligingswaarschuwingen te ontvangen
- E-mailmeldingen aan beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen voor het beheerde SQL-exemplaar
- E-mailmeldingen aan beheerders en abonnementseigenaren moeten zijn ingeschakeld in de Advanced Data Security-instellingen van de SQL-server

Meer informatie over [ingebouwd beleid](./policy-reference.md).





## <a name="june-2020"></a>Juni 2020

De updates in juni zijn onder meer:
- [Beveiligingsscore-API (preview)](#secure-score-api-preview)
- [Geavanceerde gegevensbeveiliging voor SQL-machines (Azure, andere clouds en on-premises) (preview)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Twee nieuwe aanbevelingen voor het implementeren van de Log Analytics-agent op Azure Arc-machines (preview)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nieuw beleid voor het maken van configuraties voor continue export en werkstroomautomatisering op schaal](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nieuwe aanbeveling voor het gebruik van netwerkbeveiligingsgroepen om niet op internet gerichte virtuele machines te beveiligen](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nieuw beleid voor het inschakelen van beveiliging tegen bedreigingen en geavanceerde gegevensbeveiliging](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Beveiligingsscore-API (preview)

U hebt nu toegang tot uw score via de [Beveiligingsscore-API](/rest/api/securitycenter/securescores/) (momenteel als preview). De API-methoden bieden de flexibiliteit om query's uit te voeren op de gegevens en uw eigen rapportagemechanisme te bouwen van uw beveiligingsscores in de loop van de tijd. U kunt bijvoorbeeld de **Beveiligingsscore**-API gebruiken om de score voor een specifiek abonnement op te halen. Daarnaast kunt u de API voor **besturingselementen van de beveiligingsscore** gebruiken om de besturingselementen voor beveiliging en de huidige score van uw abonnementen weer te geven.

Zie [het gebied voor beveiligingsscores van onze GitHub-community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score) voor voorbeelden van externe hulpprogramma's die mogelijk zijn gemaakt met de Beveiligingsscore-API.

Meer informatie over [beveiligingsscore en besturingselementen voor beveiliging in Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Geavanceerde gegevensbeveiliging voor SQL-machines (Azure, andere clouds en on-premises) (preview)

De geavanceerde gegevensbeveiliging van Azure Security Center voor SQL-machines biedt nu beveiliging voor SQL-servers die worden gehost op Azure, in andere cloudomgevingen en zelfs op on-premises machines. Hiermee wordt de beveiliging voor uw systeemeigen SQL-servers van Azure uitgebreid om hybride omgevingen volledig te ondersteunen.

Geavanceerde gegevensbeveiliging biedt evaluatie van beveiligingsproblemen en geavanceerde beveiliging tegen bedreigingen voor uw SQL-machines, waar ze zich ook bevinden.

Het instellen bestaat uit twee stappen:

1. De Log Analytics-agent implementeren op de hostcomputer van uw SQL Server om verbinding te maken met het Azure-account.

1. Het inschakelen van de optionele bundel op de pagina met prijzen en instellingen van Security Center.

Meer informatie over [geavanceerde gegevensbeveiliging voor SQL-machines](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Twee nieuwe aanbevelingen voor het implementeren van de Log Analytics-agent op Azure Arc-machines (preview)

Er zijn twee nieuwe aanbevelingen toegevoegd om de [Log Analytics-agent](../azure-monitor/platform/log-analytics-agent.md) te implementeren op uw Azure Arc-machines en ervoor te zorgen dat ze worden beveiligd door Azure Security Center:

- **De Log Analytics-agent moet zijn geïnstalleerd op uw Windows Azure Arc-machines (preview)**
- **De Log Analytics-agent moet zijn geïnstalleerd op uw Linux Azure Arc-machines (preview)**

Deze nieuwe aanbevelingen worden weergegeven in dezelfde vier besturingselementen voor beveiliging als de bestaande (gerelateerde) aanbeveling, **De bewakingsagent moet op uw computers worden geïnstalleerd**: beveiligingsconfiguraties herstellen, adaptieve toepassingsregelaars toepassen, systeemupdates toepassen en eindpuntbeveiliging inschakelen.

De aanbevelingen omvatten ook de mogelijkheid voor snelle oplossingen om het implementatieproces te versnellen. 

Meer informatie over deze twee nieuwe aanbevelingen vindt u in de tabel [Compute- en app-aanbevelingen](recommendations-reference.md#recs-computeapp).

Meer informatie over hoe Azure Security Center de agent gebruikt, vindt u in [Wat is de Log Analytics-agent?](faq-data-collection-agents.md#what-is-the-log-analytics-agent).

Meer informatie over [extensies voor Azure Arc-machines](../azure-arc/servers/manage-vm-extensions.md).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nieuw beleid voor het maken van configuraties voor continue export en werkstroomautomatisering op schaal

Het automatiseren van de processen voor bewaking en reageren op incidenten van uw organisatie kan de tijd die nodig is om beveiligingsincidenten te onderzoeken en te verhelpen aanzienlijk verbeteren.

Als u uw automatiseringsconfiguraties in uw organisatie wilt implementeren, gebruikt u deze ingebouwde DeployIfdNotExist-beleidsregels van Azure voor het maken en configureren van procedures voor [continue export](continuous-export.md) en [werkstroomautomatisering](workflow-automation.md):

Het beleid is te vinden in Azure Policy:


|Doel  |Beleid  |Beleids-id  |
|---------|---------|---------|
|Continue export naar Event Hub|[Export implementeren in Event Hub voor Azure Security Center-waarschuwingen en -aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Continue export naar Log Analytics-werkruimte|[Export implementeren in Log Analytics-werkruimte voor Azure Security Center-waarschuwingen en -aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Werkstroomautomatisering voor beveiligingswaarschuwingen|[Werkstroomautomatisering implementeren voor Azure Security Center-waarschuwingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Werkstroomautomatisering voor beveiligingsaanbevelingen|[Werkstroomautomatisering implementeren voor Azure Security Center-aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Aan de slag met [sjablonen voor werkstroomautomatisering](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Meer informatie over het gebruik van de twee exportbeleidsregels vindt u in [Configure workflow automation at scale using the supplied policies](workflow-automation.md#configure-workflow-automation-at-scale-using-the-supplied-policies) (Werkstroomautomatisering op schaal configureren met de meegeleverde beleidsregels) en [Set up a continuous export](continuous-export.md#set-up-a-continuous-export) (Continue export instellen).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Nieuwe aanbeveling voor het gebruik van netwerkbeveiligingsgroepen om niet op internet gerichte virtuele machines te beveiligen

Het besturingselement voor beveiliging 'aanbevolen procedures voor beveiliging implementeren' bevat nu de volgende nieuwe aanbeveling:

- **Niet-internetgerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen**

In een bestaande aanbeveling, **Op internet gerichte virtuele machines moeten worden beveiligd met netwerkbeveiligingsgroepen**, werd geen onderscheid gemaakt tussen op internet gerichte en niet op internet gerichte virtuele machines. Voor beide werd een aanbeveling met een hoge urgentie gegenereerd als een virtuele machine niet aan een netwerkbeveiligingsgroep was toegewezen. In deze nieuwe aanbeveling wordt een onderscheid gemaakt met niet op internet gerichte machines om de fout-positieven te verminderen en onnodige waarschuwingen met hoge urgentie te voorkomen.

Meer informatie vindt u in de tabel [Aanbevelingen voor netwerken](recommendations-reference.md#recs-network).




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nieuw beleid voor het inschakelen van beveiliging tegen bedreigingen en geavanceerde gegevensbeveiliging

De onderstaande nieuwe beleidsregels zijn toegevoegd aan het ASC-standaardinitiatief en zijn ontworpen om te helpen bij het inschakelen van beveiliging tegen bedreigingen of geavanceerde gegevensbeveiliging voor de relevante resourcetypen.

Het beleid is te vinden in Azure Policy:


| Beleid                                                                                                                                                                                                                                                                | Beleids-id                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Advanced Data Security moet zijn ingeschakeld voor Azure SQL Database-servers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Advanced Data Security moet zijn ingeschakeld voor SQL-servers op computers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Storage-accounts](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Key Vault-kluizen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure App Service-plannen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Container Registry-registers](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Advanced Threat Protection moet zijn ingeschakeld voor Azure Kubernetes Service-clusters](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Advanced Thread Protection moet zijn ingeschakeld op virtuele machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Meer informatie over [bescherming tegen bedreigingen in Azure Security Center](azure-defender.md).
