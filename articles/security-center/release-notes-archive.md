---
title: Archief van wat er nieuw is in Azure Security Center
description: Een beschrijving van wat er nieuw en gewijzigd is in Azure Security Center van zes maanden geleden en eerder.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/04/2021
ms.author: memildin
ms.openlocfilehash: 9d376a374d1934f55b6a6fb15f1642c81b30b2fc
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718661"
---
# <a name="archive-for-whats-new-in-azure-security-center"></a>Archiveren voor wat er nieuw is in Azure Security Center?

De primaire [pagina Wat is er nieuw in Azure Security Center?](release-notes.md) bevat updates voor de afgelopen zes maanden, terwijl deze pagina oudere items bevat.

Op deze pagina vindt u informatie over:

- Nieuwe functies
- Opgeloste fouten
- Afgeschafte functionaliteit


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

[Meer informatie over servers met Azure Arc](../azure-arc/servers/index.yml).


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall-aanbeveling toegevoegd (preview)

Er is een nieuwe aanbeveling toegevoegd om al uw virtuele netwerken met Azure Firewall te beveiligen.

De aanbeveling, **Virtuele netwerken moeten worden beveiligd met Azure Firewall**, adviseert u om de toegang tot uw virtuele netwerken te beperken en mogelijke dreigingen te voorkomen door gebruik te maken van Azure Firewall.

Meer informatie over [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/).


### <a name="authorized-ip-ranges-should-be-defined-on-kubernetes-services-recommendation-updated-with-quick-fix"></a>De aanbeveling Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services is bijgewerkt met een snelle oplossing

De aanbeveling **Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services** bevat nu een snelle oplossing.

Raadpleeg [Aanbevelingen voor beveiliging: een naslaggids](recommendations-reference.md) voor meer informatie over deze aanbeveling en alle andere aanbevelingen van Security Center.

:::image type="content" source="./media/release-notes/authorized-ip-ranges-recommendation.png" alt-text="Aanbeveling Geautoriseerde IP-bereiken moeten worden gedefinieerd voor Kubernetes Services met de snelle oplossing":::


### <a name="regulatory-compliance-dashboard-now-includes-option-to-remove-standards"></a>Het dashboard Naleving van regelgeving bevat nu een optie voor het verwijderen van standaarden

Het Dashboard Naleving van regelgeving van Security Center biedt inzicht in uw nalevingsstatus op basis van in hoeverre u aan specifieke nalevingsmechanismen en -vereisten voldoet.

Het dashboard bevat een vaste set reglementaire standaarden. Als een van de opgegeven standaarden niet relevant is voor uw organisatie, is het nu een eenvoudig proces om ze te verwijderen uit de gebruikersinterface voor een abonnement. Standaarden kunnen alleen worden verwijderd op het niveau van het *abonnement*, niet in het bereik van de beheergroep.

Meer informatie in [Een standaard verwijderen uit uw dashboard.](update-regulatory-compliance-packages.md#remove-a-standard-from-your-dashboard)


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Tabel Microsoft.Security/securityStatuses is verwijderd uit Azure Resource Graph (ARG)

Azure Resource Graph is een service in Azure. Het is ontworpen voor een efficiënte resourceverkenning en biedt de mogelijkheid query's op schaal uit te voeren binnen een bepaalde groep abonnementen, zodat u uw omgeving effectief kunt beheren. 

Voor Azure Security Center kunt u gebruikmaken van ARG en de [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/) om query's uit te voeren op een breed scala aan postuurgegevens. Bijvoorbeeld:

- De assetvoorraad maakt gebruik van ARG
- Er is een voorbeeld van een ARG-query gedocumenteerd voor het [identificeren van accounts zonder dat MFA (meervoudige verificatie) is ingeschakeld](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

In ARG zijn er tabellen met gegevens die u kunt gebruiken in uw query's.

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
- [Kusto-querytaal (KQL)](/azure/data-explorer/kusto/query/)


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

We hebben een vernieuwde gebruikersinterface voor de portalpagina's van Security Center uitgebracht. De nieuwe pagina's bevatten een nieuwe overzichtspagina en dashboards voor de beveiligde score, assetinventaris en Azure Defender.

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

Vanaf 1 oktober 2020 gaan we kosten in rekening brengen voor het beveiligen van resources voor deze services.

Meer informatie vindt u in [Azure Defender voor Storage](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Hulpprogramma's voor assetinventarisatie zijn nu algemeen beschikbaar

De pagina voor assetinventarisatie van Azure Security Center biedt één pagina voor het weergeven van het beveiligingspostuur van de resources die u hebt verbonden met Security Center.

Security Center analyseert periodiek de beveiligingsstatus van uw Azure-resources om mogelijke beveiligingsproblemen op te sporen. Vervolgens krijgt u aanbevelingen voor het oplossen van deze beveiligingsproblemen.

Wanneer een resource openstaande aanbevelingen heeft, worden deze weergegeven in de inventarisatie.

Voor meer informatie raadpleegt u [Uw resources verkennen en beheren met assetvoorraad](asset-inventory.md).



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

De functie beveiligingsbeleid voor pods (preview) is ingesteld voor afschaffing en is na 15 oktober 2020 niet meer beschikbaar in plaats van Azure Policy voor AKS.

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

Security Center heeft volledige ondersteuning toegevoegd voor [standaardinstellingen](../active-directory/fundamentals/concept-fundamentals-security-defaults.md)voor beveiliging, de gratis beveiliging voor identiteiten van Microsoft.

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
|**A vulnerability assessment solution should be enabled on your virtual machines** (Er moet een oplossing voor de evaluatie van beveiligingsproblemen op uw virtuele machines worden ingeschakeld)|Vervangt de volgende twee aanbevelingen:<br> De ingebouwde oplossing voor evaluatie van beveiligingsleed inschakelen op virtuele machines (powered by Qualys (nu afgeschaft) (opgenomen in de Standard-laag)<br> De oplossing voor de evaluatie van beveiligingsleeds moet worden geïnstalleerd op uw virtuele machines (nu afgeschaft) (Standard en gratis lagen)|
|**Beveiligingsproblemen op uw virtuele machines moeten worden hersteld**|Vervangt de volgende twee aanbevelingen:<br>Beveiligingsproblemen op uw virtuele machines herstellen (powered by Qualys) (nu afgeschaft)<br>Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen (nu afgeschaft)|
|||

U gebruikt nu dezelfde aanbeveling om de uitbreiding voor de evaluatie van beveiligingsproblemen van Security Center of een oplossing met een eigen licentie ("BYOL") van een partner, zoals Qualys of Rapid7, te implementeren.

Wanneer er beveiligingsproblemen worden gevonden en gerapporteerd aan Security Center, wordt u door één aanbeveling gewaarschuwd voor de bevindingen, ongeacht de oplossing voor de evaluatie van beveiligingsproblemen die deze heeft gesignaleerd.

#### <a name="updating-dependencies"></a>Afhankelijkheden bijwerken

Als u scripts, query's of automatiseringen hebt die verwijzen naar de vorige aanbevelingen of beleidssleutels/-namen, gebruikt u de onderstaande tabellen om de verwijzingen bij te werken:

##### <a name="before-august-2020"></a>Vóór augustus 2020

| Aanbeveling|Bereik|
|----|:----|
|**De ingebouwde oplossing voor evaluatie van beveiligingsproblemen inschakelen op virtuele machines (mogelijk gemaakt door Qualys)**<br>Sleutel: 550e890b-e652-4d22-8274-60b3bdb24c63|Ingebouwd|
|**Beveiligingsproblemen op uw virtuele machines herstellen (mogelijk gemaakt door Qualys)**<br>Sleutel: 1195afff-c881-495e-9bc5-1486211ae03f|Ingebouwd|
|**Er moet een oplossing voor de evaluatie van beveiligingsproblemen op uw virtuele machines moeten worden geïnstalleerd**<br>Sleutel: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL (Bring Your Own License)|
|**Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen**<br>Sleutel: 71992a2a-d168-42e0-b10e-6b45fa2ecddb|BYOL (Bring Your Own License)|
|||


|Beleid|Bereik|
|----|:----|
|**Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines**<br>Beleids-id: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Ingebouwd|
|**Beveiligingsproblemen moeten worden opgelost met een oplossing voor evaluatie van beveiligingsproblemen**<br>Beleids-id: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL (Bring Your Own License)|
|||


##### <a name="from-august-2020"></a>Vanaf augustus 2020

|Aanbeveling|Bereik|
|----|:----|
|**A vulnerability assessment solution should be enabled on your virtual machines** (Er moet een oplossing voor de evaluatie van beveiligingsproblemen op uw virtuele machines worden ingeschakeld)<br>Sleutel: ffff0522-1e88-47fc-8382-2a80ba848f5d|Ingebouwd en BYOL|
|**Beveiligingsproblemen op uw virtuele machines moeten worden hersteld**<br>Sleutel: 1195afff-c881-495e-9bc5-1486211ae03f|Ingebouwd en BYOL|
|||

|Beleid|Bereik|
|----|:----|
|[**Evaluatie van beveiligingsproblemen moet zijn ingeschakeld op virtuele machines**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Beleids-id: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Ingebouwd en BYOL|
|||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nieuw beveiligingsbeleid voor AKS toegevoegd aan ASC-standaardinitiatief: alleen voor gebruik door klanten van beperkte preview

Om ervoor te zorgen dat Kubernetes-workloads standaard veilig zijn, voegt Security Center beleid en aanbevelingen voor beveiliging op Kubernetes-niveau toe, met inbegrip van afdwingingsopties met Kubernetes Admission Control.

De vroege fase van dit project bevat een beperkte preview en het toevoegen van nieuwe beleidsregels (standaard uitgeschakeld) aan het ASC-standaardinitiatief.

U kunt dit beleid veilig negeren en dit heeft geen invloed op uw omgeving. Als u het wilt inschakelen, meldt u zich voor de preview-versie aan op https://aka.ms/SecurityPrP en selecteert u een van de volgende opties:

1. **Enkele preview-** : alleen deelnemen aan deze beperkte preview. Vermeld expliciet 'ASC Continuous Scan' als de preview-versie die u wilt deelnemen.
1. **Doorlopend programma**: om deel te nemen aan deze en toekomstige beperkte previews. U moet een profiel en privacyovereenkomst voltooien.


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
- [Beveiligingsaanbevelingen voor containers](recommendations-reference.md#recs-compute)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Adaptieve toepassingsregelaars bijgewerkt met een nieuwe aanbeveling en ondersteuning voor jokertekens in padregels

De functie voor adaptieve toepassingsregelaars heeft twee belangrijke updates ontvangen:

* Een nieuwe aanbeveling duidt mogelijk legitiem gedrag aan dat nog niet is toegestaan. De nieuwe aanbeveling, **De Allowlist-regels in uw beleid voor adaptief toepassingsbeheer moeten worden bijgewerkt**, vraagt u om nieuwe regels aan het bestaande beleid toe te voegen om het aantal fout-positieven in adaptieve toepassingsregelaars te verminderen.

* Padregels ondersteunen nu jokertekens. Vanaf deze update kunt u regels voor toegestane paden configureren met behulp van jokertekens. Er zijn twee ondersteunde scenario's:

    * Een jokerteken aan het einde van een pad gebruiken om alle uitvoerbare bestanden in deze map en submappen toe te staan

    * Een jokerteken in het midden van een pad gebruiken om het mogelijk te maken een bekende naam van een uitvoerbaar bestand te gebruiken met een veranderende mapnaam (bijvoorbeeld persoonlijke gebruikersmappen met een bekend uitvoerbaar bestand, automatisch gegenereerde mapnamen, etc.).


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
- [Geavanceerde gegevensbeveiliging voor SQL-machines (Azure, andere clouds en on-premises) (preview)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview)
- [Twee nieuwe aanbevelingen voor het implementeren van de Log Analytics-agent op Azure Arc-machines (preview)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nieuw beleid voor het maken van configuraties voor continue export en werkstroomautomatisering op schaal](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Nieuwe aanbeveling voor het gebruik van netwerkbeveiligingsgroepen om niet op internet gerichte virtuele machines te beveiligen](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nieuw beleid voor het inschakelen van beveiliging tegen bedreigingen en geavanceerde gegevensbeveiliging](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Beveiligingsscore-API (preview)

U hebt nu toegang tot uw score via de [Beveiligingsscore-API](/rest/api/securitycenter/securescores/) (momenteel als preview). De API-methoden bieden de flexibiliteit om query's uit te voeren op de gegevens en uw eigen rapportagemechanisme te bouwen van uw beveiligingsscores in de loop van de tijd. U kunt bijvoorbeeld de **Beveiligingsscore**-API gebruiken om de score voor een specifiek abonnement op te halen. Daarnaast kunt u de API voor **besturingselementen van de beveiligingsscore** gebruiken om de besturingselementen voor beveiliging en de huidige score van uw abonnementen weer te geven.

Zie [het gebied voor beveiligingsscores van onze GitHub-community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score) voor voorbeelden van externe hulpprogramma's die mogelijk zijn gemaakt met de Beveiligingsscore-API.

Meer informatie over [beveiligingsscore en besturingselementen voor beveiliging in Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-premises-preview"></a>Geavanceerde gegevensbeveiliging voor SQL-machines (Azure, andere clouds en on-premises) (preview)

De geavanceerde gegevensbeveiliging van Azure Security Center voor SQL-machines biedt nu beveiliging voor SQL-servers die worden gehost op Azure, in andere cloudomgevingen en zelfs op on-premises machines. Hiermee wordt de beveiliging voor uw systeemeigen SQL-servers van Azure uitgebreid om hybride omgevingen volledig te ondersteunen.

Geavanceerde gegevensbeveiliging biedt evaluatie van beveiligingsproblemen en geavanceerde beveiliging tegen bedreigingen voor uw SQL-machines, waar ze zich ook bevinden.

Het instellen bestaat uit twee stappen:

1. De Log Analytics-agent implementeren op de hostcomputer van uw SQL Server om verbinding te maken met het Azure-account.

1. Het inschakelen van de optionele bundel op de pagina met prijzen en instellingen van Security Center.

Meer informatie over [geavanceerde gegevensbeveiliging voor SQL-machines](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Twee nieuwe aanbevelingen voor het implementeren van de Log Analytics-agent op Azure Arc-machines (preview)

Er zijn twee nieuwe aanbevelingen toegevoegd om de [Log Analytics-agent](../azure-monitor/agents/log-analytics-agent.md) te implementeren op uw Azure Arc-machines en ervoor te zorgen dat ze worden beveiligd door Azure Security Center:

- **De Log Analytics-agent moet zijn geïnstalleerd op uw Windows Azure Arc-machines (preview)**
- **De Log Analytics-agent moet zijn geïnstalleerd op uw Linux Azure Arc-machines (preview)**

Deze nieuwe aanbevelingen worden weergegeven in dezelfde vier besturingselementen voor beveiliging als de bestaande (gerelateerde) aanbeveling, **De bewakingsagent moet op uw computers worden geïnstalleerd**: beveiligingsconfiguraties herstellen, adaptieve toepassingsregelaars toepassen, systeemupdates toepassen en eindpuntbeveiliging inschakelen.

De aanbevelingen omvatten ook de mogelijkheid voor snelle oplossingen om het implementatieproces te versnellen. 

Meer informatie over deze twee nieuwe aanbevelingen vindt u in de tabel [Compute- en app-aanbevelingen](recommendations-reference.md#recs-compute).

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

Meer informatie vindt u in de tabel [Aanbevelingen voor netwerken](recommendations-reference.md#recs-networking).




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


## <a name="may-2020"></a>Mei 2020

De updates in mei zijn onder meer:
- [Regels voor waarschuwingsonderdrukking (preview)](#alert-suppression-rules-preview)
- [Evaluatie van beveiligingsproblemen van virtuele machines is nu algemeen beschikbaar](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Wijzigingen in de Just-In-Time-toegang van virtuele machines (VM)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Aangepaste aanbevelingen zijn verplaatst naar een afzonderlijk besturingselement voor beveiliging](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Schakeloptie toegevoegd om aanbevelingen weer te geven in besturingselementen of als een platte lijst](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Besturingselement voor beveiliging 'Aanbevolen procedures voor beveiliging implementeren' uitgebreid](#expanded-security-control-implement-security-best-practices)
- [Aangepaste beleidsregels met aangepaste metagegevens zijn nu algemeen beschikbaar](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Mogelijkheden voor crashdumpanalyse migreren naar detectie van bestandsloze aanvallen](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Regels voor waarschuwingsonderdrukking (preview)

Met deze nieuwe functie (momenteel in preview) kunt u overbodige waarschuwingen verminderen. Gebruik regels om waarschuwingen waarvan bekend is dat ze onschuldig of gerelateerd zijn aan normale activiteiten in uw organisatie automatisch te verbergen. Zo kunt u zich richten op de meest relevante bedreigingen. 

Waarschuwingen die overeenkomen met uw ingeschakelde onderdrukkingsregels worden nog steeds gegenereerd, maar de status wordt ingesteld op 'Genegeerd'. U kunt de status bekijken in Azure Portal of de manier waarop u uw Security Center beveiligingswaarschuwingen bekijkt.

Met onderdrukkingsregels worden de criteria gedefinieerd waarvoor waarschuwingen automatisch moeten worden genegeerd. Normaal gesproken gebruikt u een onderdrukkingsregel voor het volgende:

- waarschuwingen onderdrukken die u als fout-positieven hebt geïdentificeerd

- waarschuwingen onderdrukken die te vaak worden geactiveerd om nuttig te zijn

Meer informatie over [het onderdrukken van waarschuwingen voor beveiliging tegen bedreigingen van Azure Security Center](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Evaluatie van beveiligingsproblemen van virtuele machines is nu algemeen beschikbaar

De Standaard-laag van Security Center bevat nu een geïntegreerde evaluatie van beveiligingsproblemen voor virtuele machines zonder extra kosten. Deze uitbreiding wordt mogelijk gemaakt door Qualys, maar rapporteert de resultaten direct terug naar Security Center. U hebt geen Qualys-licentie of Qualys-account nodig. De scans worden naadloos uitgevoerd in Security Center.

De nieuwe oplossing kan voortdurend uw virtuele machines scannen om beveiligingsproblemen op te sporen en de bevindingen in Security Center weergeven. 

Als u de oplossing wilt implementeren, gebruikt u de nieuwe beveiligingsaanbeveling:

'De ingebouwde oplossing voor evaluatie van beveiligingsproblemen inschakelen op virtuele machines (mogelijk gemaakt door Qualys)'

Meer informatie over [De geïntegreerde evaluatie van beveiligingsproblemen voor virtuele machines van Security Center](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Wijzigingen in de Just-In-Time-toegang van virtuele machines (VM)

Security Center bevat een optionele functie waarmee u de beheerpoorten van uw virtuele machines kunt beveiligen. Dit biedt een verdediging tegen de meest voorkomende vorm van beveiligingsaanvallen.

Met deze update worden de volgende wijzigingen doorgevoerd in deze functie:

- De naam van de aanbeveling waarmee u wordt geadviseerd om JIT in te schakelen, is gewijzigd. Eerder was de naam 'Just-In-Time-netwerktoegangsbeheer moet worden toegepast op virtuele machines' en nu is de naam: 'Beheerpoorten van virtuele machines moeten worden beveiligd met Just-In-Time-netwerktoegangsbeheer'.

- De aanbeveling wordt alleen geactiveerd als er open beheerpoorten zijn.

Meer informatie over [de JIT-toegangsfunctie](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Aangepaste aanbevelingen zijn verplaatst naar een afzonderlijk besturingselement voor beveiliging

Een besturingselement voor beveiliging dat is geïntroduceerd in de verbeterde beveiligingsscore is 'Aanbevolen procedures voor beveiliging implementeren'. Aangepaste aanbevelingen die zijn gemaakt voor uw abonnementen, worden automatisch in dat besturingselement geplaatst. 

Om u te helpen uw aangepaste aanbevelingen gemakkelijker te vinden, zijn deze naar een speciaal besturingselement voor beveiliging, 'Aangepaste aanbevelingen ', verplaatst. Dit besturingselement heeft geen invloed op uw beveiligingsscore.

Meer informatie over besturingselementen voor beveiliging vindt u in [Verbeterde beveiligingsscore (preview) in Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Schakeloptie toegevoegd om aanbevelingen weer te geven in besturingselementen of als een platte lijst

Besturingselementen voor beveiliging zijn logische groepen van gerelateerde beveiligingsaanbevelingen. Ze zijn gebaseerd op gebieden waar u kwetsbaar bent voor aanvallen. Een besturingselement is een reeks beveiligingsaanbevelingen met instructies die u helpen bij het implementeren van deze aanbevelingen.

Als u onmiddellijk wilt zien hoe goed uw organisatie een afzonderlijke kwetsbaarheid beveiligt, controleert u de scores voor elk besturingselement voor beveiliging.

Uw aanbevelingen worden standaard weergegeven in de besturingselementen voor beveiliging. Vanaf deze update kunt u ze ook als een lijst weergeven. Als u ze wilt weergeven als een eenvoudige lijst, gesorteerd op de status van de betrokken resources, gebruikt u de nieuwe schakeloptie 'Groeperen op besturingselementen'. De schakeloptie bevindt zich boven de lijst in de portal.

De besturingselementen voor beveiliging - en deze schakeloptie - maken deel uit van de vernieuwde beveiligingsscore. Vergeet niet om ons uw feedback te sturen vanuit de portal.

Meer informatie over besturingselementen voor beveiliging vindt u in [Verbeterde beveiligingsscore (preview) in Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="De schakeloptie Groeperen op besturingselementen voor aanbevelingen":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Besturingselement voor beveiliging 'Aanbevolen procedures voor beveiliging implementeren' uitgebreid 

Een besturingselement voor beveiliging dat is geïntroduceerd in de verbeterde beveiligingsscore is 'Aanbevolen procedures voor beveiliging implementeren'. Wanneer een aanbeveling zich in dit besturingselement bevindt, heeft dit geen invloed op de beveiligingsscore. 

Met deze update zijn drie aanbevelingen uit de besturingselementen waarin deze oorspronkelijk zijn geplaatst naar dit besturingselement voor aanbevolen procedures verplaatst. We hebben deze stap doorgevoerd omdat er is vastgesteld dat het risico van deze drie aanbevelingen lager is dan oorspronkelijk werd aangenomen.

Daarnaast zijn er twee nieuwe aanbevelingen geïntroduceerd en toegevoegd aan dit besturingselement.

De drie aanbevelingen die zijn verplaatst, zijn:

- **MFA moet zijn ingeschakeld voor accounts met leesmachtigingen voor uw abonnement** (oorspronkelijk in het besturingselement 'MFA inschakelen')
- **Externe accounts met leesmachtigingen moeten worden verwijderd uit uw abonnement** (oorspronkelijk in het besturingselement 'Toegang en machtigingen beheren')
- **Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement** (oorspronkelijk in het besturingselement 'Toegang en machtigingen beheren')

De twee nieuwe aanbevelingen die aan het besturingselement zijn toegevoegd, zijn:

- **De Gastconfiguratie-extensie moet op virtuele Windows-machines zijn geïnstalleerd (preview)** : het gebruik van [Azure Policy-gastconfiguratie](../governance/policy/concepts/guest-configuration.md) biedt inzicht in de instellingen van de virtuele machines op de server en toepassing (alleen Windows).

- **Windows Defender Exploit Guard moet zijn ingeschakeld op uw computers (preview)** : Windows Defender Exploit Guard maakt gebruik van de Azure Policy-gastconfiguratieagent. Exploit Guard bestaat uit vier onderdelen die zijn ontworpen om apparaten te vergrendelen tegen diverse aanvalsvectoren en blokkeergedrag die in malware-aanvallen worden gebruikt en die bedrijven de mogelijkheid bieden om een goede balans te vinden tussen hun vereisten op het gebied van beveiligingsrisico's en productiviteit (alleen Windows).

Meer informatie over Windows Defender Exploit Guard vindt u in [Een Exploit Guard-beleid maken en implementeren](/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Meer informatie over besturingselementen voor beveiliging vindt u in [Verbeterde beveiligingsscore (preview)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Aangepaste beleidsregels met aangepaste metagegevens zijn nu algemeen beschikbaar

Aangepaste beleidsregels maken nu deel uit van de Security Center-aanbevelingen, de beveiligingsscore en het dashboard voor naleving van regelgeving. Deze functie is nu algemeen beschikbaar en biedt u de mogelijkheid om de dekking van de evaluatie van beveiligingsproblemen van uw organisatie in Security Center uit te breiden. 

Maak een aangepaste initiatief in Azure Policy, voeg er beleidsregels aan toe en onboard dit naar Azure Security Center, en visualiseer het vervolgens als aanbevelingen.

We hebben nu ook de optie toegevoegd voor het bewerken van de aangepaste metagegevens voor aanbevelingen. Opties voor metagegevens zijn ernst, herstelstappen, informatie over bedreigingen en meer.  

Meer informatie over [uw aangepaste aanbevelingen verbeteren met gedetailleerde informatie](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Mogelijkheden voor crashdumpanalyse migreren naar detectie van bestandsloze aanvallen 

De detectiemogelijkheden van Windows Crash Dump Analysis (CDA) worden geïntegreerd in [detectie van bestandsloze aanvallen](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). Analyse van detectie van bestandsloze aanvallen biedt verbeterde versies van de volgende beveiligingswaarschuwingen voor Windows-computers: 'Code-injectie gedetecteerd', 'Verdachte Windows-module gedetecteerd', 'Shellcode gedetecteerd' en 'Verdacht codesegment gedetecteerd'.

Enkele voordelen hiervan:

- **Proactieve en tijdige detectie van malware**: bij de CDA-benadering werd gewacht op een crash, en werden vervolgens analyses uitgevoerd om schadelijke artefacten te vinden. Bij het gebruik van detectie van bestandsloze aanvallen worden bedreigingen in het geheugen proactief geïdentificeerd terwijl ze worden uitgevoerd. 

- **Verrijkte waarschuwingen**: de beveiligingswaarschuwingen van detectie van bestandsloze aanvallen bevatten verrijkingen die niet beschikbaar zijn via CDA, zoals informatie over actieve netwerkverbindingen. 

- **Waarschuwingsaggregatie**: wanneer CDA meerdere aanvalspatronen in één crashdump heeft gedetecteerd, worden er meerdere beveiligingswaarschuwingen geactiveerd. Bij detectie van bestandsloze aanvallen worden alle geïdentificeerde aanvalspatronen uit hetzelfde proces gecombineerd tot één waarschuwing, waardoor de noodzaak om meerdere waarschuwingen met elkaar in verband te brengen, wordt weggenomen.

- **Minder vereisten voor uw Log Analytics-werkruimte**: crashdumps met mogelijk gevoelige gegevens worden niet meer geüpload naar uw Log Analytics-werkruimte.






## <a name="april-2020"></a>April 2020

De updates in april zijn onder meer:
- [Dynamische nalevingspakketten zijn nu algemeen beschikbaar](#dynamic-compliance-packages-are-now-generally-available)
- [Identiteitsaanbevelingen nu opgenomen in de gratis laag van Azure Security Center](#identity-recommendations-now-included-in-azure-security-center-free-tier)


### <a name="dynamic-compliance-packages-are-now-generally-available"></a>Dynamische nalevingspakketten zijn nu algemeen beschikbaar

Het dashboard voor naleving van regelgeving van Azure Security Center bevat nu **dynamische nalevingspakketten** (nu algemeen beschikbaar) om aanvullende regelgevende en bedrijfstakstandaarden bij te houden.

Dynamische nalevingspakketten kunnen worden toegevoegd aan uw abonnement of beheergroep via de pagina met beveiligingsbeleid van Security Center. Wanneer u een standaard of benchmark hebt geïntroduceerd, wordt de standaard weergegeven in het dashboard voor naleving van regelgeving met alle gekoppelde nalevingsgegevens die zijn gekoppeld als evaluaties. Er kan een overzichtsrapport voor alle geïntroduceerde standaarden worden gedownload.

U kunt nu standaarden toevoegen zoals:

- **NIST SP 800-53 R4**
- **SWIFT CSP CSCF-v2020**
- **UK OFFICIAL en UK NHS**
- **Canada Federal PBMM**
- **Azure CIS 1.1.0 (nieuw)** (een meer volledige weergave van Azure CIS 1.1.0)

Daarnaast hebben we onlangs de [Azure Security-benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction) toegevoegd, de door Microsoft ontworpen, voor Azure specifieke richtlijnen voor aanbevolen procedures voor beveiliging en naleving op basis van algemene nalevingskaders. Er worden extra standaarden ondersteund in het dashboard zodra deze beschikbaar komen.  
 
Meer informatie over het [aanpassen van de set standaarden in uw dashboard voor naleving van regelgeving](update-regulatory-compliance-packages.md).


### <a name="identity-recommendations-now-included-in-azure-security-center-free-tier"></a>Identiteitsaanbevelingen nu opgenomen in de gratis laag van Azure Security Center

Beveiligingsaanbevelingen voor identiteit en toegang in de gratis laag van Azure Security Center zijn nu algemeen beschikbaar. Dit maakt deel uit van de inspanningen om de CSPM-functies (Cloud Security Posture Management) gratis te maken. Tot nu toe zijn deze aanbevelingen alleen beschikbaar in de prijscategorie Standaard.

Voorbeelden van aanbevelingen voor identiteit en toegang zijn onder meer:

- 'Meervoudige verificatie moet zijn ingeschakeld voor accounts met eigenaarsmachtigingen voor uw abonnement.'
- 'Er moeten maximaal drie eigenaren worden aangewezen voor uw abonnement.'
- 'Afgeschafte accounts moeten worden verwijderd uit uw abonnement.'

Als u abonnementen hebt in de gratis laag, worden de beveiligingsscores ervan beïnvloed door deze wijziging omdat ze nooit zijn geëvalueerd op hun identiteits- en toegangsbeveiliging.

Meer informatie over [aanbevelingen voor identiteit en toegang](recommendations-reference.md#recs-identityandaccess).

Meer informatie over [het beheren van meervoudige verificatie (MFA) afdwingen voor uw abonnementen.](security-center-identity-access.md)



## <a name="march-2020"></a>Maart 2020

Updates in maart zijn onder andere:

- [Werkstroomautomatisering is nu algemeen beschikbaar](#workflow-automation-is-now-generally-available)
- [Integratie van Azure Security Center met Windows Admin Center](#integration-of-azure-security-center-with-windows-admin-center)
- [Beveiliging voor Azure Kubernetes Service](#protection-for-azure-kubernetes-service)
- [Verbeterde Just-In-Time-ervaring](#improved-just-in-time-experience)
- [Twee beveiligingsaanbevelingen voor webtoepassingen afgeschaft](#two-security-recommendations-for-web-applications-deprecated)


### <a name="workflow-automation-is-now-generally-available"></a>Werkstroomautomatisering is nu algemeen beschikbaar

De functie werkstroomautomatisering van Azure Security Center is nu algemeen beschikbaar. Gebruik dit om automatisch beveiligingswaarschuwingen en Logic Apps te activeren. Bovendien zijn handmatige triggers beschikbaar voor waarschuwingen en alle aanbevelingen die de optie snelle oplossing beschikbaar hebben.

Elk beveiligingsprogramma bevat meerdere werkstromen voor incidentrespons. Deze processen kunnen bestaan uit het melden van relevante belanghebbenden, het starten van een wijzigingsbeheerproces en het toepassen van specifieke herstelstappen. Beveiligingsexperts raden u aan om zoveel mogelijk stappen van deze procedures te automatiseren. Automatisering vermindert de overhead en kan uw beveiliging verbeteren door ervoor te zorgen dat de processtappen snel, consistent en volgens uw vooraf gedefinieerde vereisten worden uitgevoerd.

Zie werkstroomautomatisering voor meer Security Center en handmatige mogelijkheden voor het uitvoeren [van uw werkstromen.](workflow-automation.md)

Meer informatie over het [maken van Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="integration-of-azure-security-center-with-windows-admin-center"></a>Integratie van Azure Security Center met Windows Admin Center

Het is nu mogelijk om uw on-premises Windows-servers rechtstreeks van de Windows Admin Center naar de Azure Security Center. Security Center uw enige venster om beveiligingsinformatie weer te geven voor al uw Windows Admin Center-resources, waaronder on-premises servers, virtuele machines en extra PaaS-workloads.

Nadat u een server van Windows Admin Center naar Azure Security Center verplaatst, kunt u het volgende doen:

- Bekijk beveiligingswaarschuwingen en aanbevelingen in de Security Center van de Windows Admin Center.
- Bekijk de beveiligingsstatus en haal aanvullende gedetailleerde informatie op van uw Windows Admin Center beheerde servers in de Security Center binnen de Azure Portal (of via een API).

Meer informatie over [het integreren van Azure Security Center met Windows Admin Center](windows-admin-center-integration.md).


### <a name="protection-for-azure-kubernetes-service"></a>Beveiliging voor Azure Kubernetes Service

Azure Security Center breidt de beveiligingsfuncties voor containers uit om AKS (Azure Kubernetes Service) te beveiligen.

Het populaire opensource-platform Kubernetes is zo algemeen gebruikt dat het nu een industriestandaard is voor container orchestration. Ondanks deze wijdverbreide implementatie is er nog steeds geen begrip over het beveiligen van een Kubernetes-omgeving. Het beschermen van de aanvalsoppervlakken van een containertoepassing vereist expertise om ervoor te zorgen dat de infrastructuur veilig wordt geconfigureerd en voortdurend wordt bewaakt op mogelijke bedreigingen.

De Security Center-verdediging omvat:

- **Detectie en zichtbaarheid:** continue detectie van beheerde AKS-exemplaren binnen de abonnementen die zijn geregistreerd voor Security Center.
- **Aanbevelingen voor beveiliging:** aanbevelingen die kunnen worden gedaan om u te helpen voldoen aan de aanbevolen beveiligingsprocedures voor AKS. Deze aanbevelingen zijn opgenomen in uw beveiligingsscore om ervoor te zorgen dat ze worden gezien als onderdeel van de beveiligingsstatus van uw organisatie. Een voorbeeld van een aanbeveling met betrekking tot AKS die u mogelijk ziet, is 'Op rollen gebaseerd toegangsbeheer moet worden gebruikt om de toegang tot een Kubernetes-servicecluster te beperken'.
- **Bedreigingsbeveiliging:** door continue analyse van uw AKS-implementatie waarschuwt Security Center u op bedreigingen en schadelijke activiteiten die zijn gedetecteerd op host- en AKS-clusterniveau.

Meer informatie over de integratie [van Azure Kubernetes Services met Security Center](defender-for-kubernetes-introduction.md).

Meer informatie over [de beveiligingsfuncties voor containers in Security Center](container-security.md).


### <a name="improved-just-in-time-experience"></a>Verbeterde Just-In-Time-ervaring

De functies, werking en gebruikersinterface voor Azure Security Center Just-In-Time-hulpprogramma's voor het beveiligen van uw beheerpoorten zijn als volgt verbeterd: 

- **Redenveld:** wanneer u toegang tot een virtuele machine (VM) aanvraagt via de Just-In-Time-pagina van de Azure Portal, is er een nieuw optioneel veld beschikbaar om een reden voor de aanvraag in te voeren. Gegevens die in dit veld zijn ingevoerd, kunnen worden bij gehouden in het activiteitenlogboek. 
- **Automatisch opschonen van redundante JIT-regels (Just-In-Time)** : wanneer u een JIT-beleid bij werkt, wordt er automatisch een hulpprogramma voor opschonen uitgevoerd om de geldigheid van uw hele regelset te controleren. Het hulpprogramma zoekt naar niet-overeenkomende regels in uw beleid en regels in de NSG. Als het opschoningshulpprogramma een niet-overeenkomende waarde vindt, wordt de oorzaak bepaald en worden ingebouwde regels verwijderd die niet meer nodig zijn wanneer het veilig is om dit te doen. De schoonmaker verwijdert nooit regels die u hebt gemaakt. 

Meer informatie over [de JIT-toegangsfunctie](security-center-just-in-time.md).


### <a name="two-security-recommendations-for-web-applications-deprecated"></a>Twee beveiligingsaanbevelingen voor webtoepassingen afgeschaft

Twee beveiligingsaanbevelingen met betrekking tot webtoepassingen worden afgeschaft: 

- De regels voor webtoepassingen op IaaS-NSG's moeten worden gehard.
    (Gerelateerd beleid: De regels voor NSG's ten aanzien van webtoepassingen op IaaS moeten strenger worden)

- Toegang tot App Services moet worden beperkt.
    (Gerelateerd beleid: Toegang tot App Services moet worden beperkt [preview])

Deze aanbevelingen worden niet meer weergegeven in de Security Center lijst met aanbevelingen. Het gerelateerde beleid wordt niet meer opgenomen in het initiatief met de naam Security Center Standaard.

Meer informatie over [beveiligingsaanbevelingen.](recommendations-reference.md)




## <a name="february-2020"></a>Februari 2020

### <a name="fileless-attack-detection-for-linux-preview"></a>Detectie van bestandsloze aanvallen voor Linux (preview)

Naarmate aanvallers die toenemen verborgen methoden gebruiken om detectie te voorkomen, Azure Security Center uitbreiding van detectie van bestandsloze aanvallen voor Linux, naast Windows. Bestandsloze aanvallen maken gebruik van beveiligingsproblemen met software, injecteren schadelijke nettoladingen in goedaardige systeemprocessen en verbergen zich in het geheugen. Deze technieken:

- traceringen van malware op schijf minimaliseren of elimineren
- de kans op detectie aanzienlijk verminderen door op schijven gebaseerde oplossingen voor het scannen van malware

Om deze bedreiging tegen te gaan, Azure Security Center in oktober 2018 bestandsloze aanvalsdetectie voor Windows uitgebracht en is nu ook de detectie van bestandsloze aanvallen op Linux uitgebreid. 



## <a name="january-2020"></a>Januari 2020

### <a name="enhanced-secure-score-preview"></a>Verbeterde beveiligde score (preview)

Een verbeterde versie van de functie voor beveiligde score van Azure Security Center is nu beschikbaar in de preview-versie. In deze versie worden meerdere aanbevelingen gegroepeerd in Besturingselementen voor beveiliging die uw kwetsbare aanvalsoppervlakken beter weerspiegelen (bijvoorbeeld de toegang tot beheerpoorten beperken).

Vertrouwd raken met de wijzigingen in de beveiligde score tijdens de preview-fase en andere herstels bepalen die u helpen uw omgeving verder te beveiligen.

Meer informatie over [verbeterde beveiligde score (preview)](secure-score-security-controls.md).



## <a name="november-2019"></a>November 2019

Updates in november omvatten:
 - [Bedreigingsbeveiliging voor Azure Key Vault in Noord-Amerika regio's (preview)](#threat-protection-for-azure-key-vault-in-north-america-regions-preview)
 - [Bedreigingsbeveiliging voor Azure Storage omvat malwarereputatiescreening](#threat-protection-for-azure-storage-includes-malware-reputation-screening)
 - [Werkstroomautomatisering met Logic Apps (preview)](#workflow-automation-with-logic-apps-preview)
 - [snelle oplossing voor bulkresources die algemeen beschikbaar zijn](#quick-fix-for-bulk-resources-generally-available)
 - [Containerafbeeldingen scannen op beveiligingsproblemen (preview)](#scan-container-images-for-vulnerabilities-preview)
 - [Aanvullende nalevingsstandaarden voor regelgeving (preview)](#additional-regulatory-compliance-standards-preview)
 - [Threat Protection voor Azure Kubernetes Service (preview)](#threat-protection-for-azure-kubernetes-service-preview)
 - [Evaluatie van beveiligingsleed van virtuele machines (preview)](#virtual-machine-vulnerability-assessment-preview)
 - [Geavanceerde gegevensbeveiliging voor SQL-servers op Azure Virtual Machines (preview)](#advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview)
 - [Ondersteuning voor aangepast beleid (preview)](#support-for-custom-policies-preview)
 - [Uitbreiding Azure Security Center dekking met platform voor community's en partners](#extending-azure-security-center-coverage-with-platform-for-community-and-partners)
 - [Geavanceerde integraties met het exporteren van aanbevelingen en waarschuwingen (preview)](#advanced-integrations-with-export-of-recommendations-and-alerts-preview)
 - [Onboarding van on-Security Center servers vanuit Windows Admin Center (preview)](#onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview)

### <a name="threat-protection-for-azure-key-vault-in-north-america-regions-preview"></a>Bedreigingsbeveiliging voor Azure Key Vault in Noord-Amerika-regio's (preview)

Azure Key Vault is een essentiële service voor het beveiligen van gegevens en het verbeteren van de prestaties van cloudtoepassingen door de mogelijkheid te bieden sleutels, geheimen, cryptografische sleutels en beleidsregels centraal te beheren in de cloud. Aangezien Azure Key Vault gevoelige en bedrijfskritieke gegevens opgeslagen, is maximale beveiliging vereist voor de sleutelkluizen en de gegevens die in deze kluizen zijn opgeslagen.

Azure Security Center ondersteuning voor bedreigingsbeveiliging voor Azure Key Vault biedt een extra laag beveiligingsinformatie die ongebruikelijke en mogelijk schadelijke pogingen detecteert om toegang te krijgen tot of misbruik te maken van sleutelkluizen. Met deze nieuwe beveiligingslaag kunnen klanten bedreigingen voor hun sleutelkluizen aanpakken zonder een beveiligingsexpert te zijn of beveiligingsbewakingssystemen te beheren. De functie is in openbare preview in Noord-Amerika regio's.


### <a name="threat-protection-for-azure-storage-includes-malware-reputation-screening"></a>Bedreigingsbeveiliging voor Azure Storage omvat malwarereputatiescreening

Bedreigingsbeveiliging voor Azure Storage biedt nieuwe detecties powered by Microsoft Threat Intelligence voor het detecteren van uploads van malware naar Azure Storage met behulp van hashreputatieanalyse en verdachte toegang vanaf een actief Tor-exit-knooppunt (een anonieme proxy). U kunt nu gedetecteerde malware in opslagaccounts weergeven met behulp Azure Security Center.


### <a name="workflow-automation-with-logic-apps-preview"></a>Werkstroomautomatisering met Logic Apps (preview)

Organisaties met centraal beheerde beveiliging en IT/operationele activiteiten implementeren interne werkstroomprocessen om de vereiste actie binnen de organisatie uit te voeren wanneer discrepanties worden ontdekt in hun omgevingen. In veel gevallen zijn deze werkstromen herhaalbare processen en kan automatisering processen binnen de organisatie sterk stroomlijnen.

Vandaag introduceren we een nieuwe mogelijkheid in Security Center waarmee klanten automatiseringsconfiguraties kunnen maken met Azure Logic Apps en beleidsregels kunnen maken waarmee ze automatisch worden triggers op basis van specifieke ASC-bevindingen, zoals aanbevelingen of waarschuwingen. Azure Logic App kan worden geconfigureerd voor het uitvoeren van aangepaste acties die worden ondersteund door de grote community van Logic App-connectors, of een van de sjablonen van Security Center gebruiken, zoals het verzenden van een e-mail of het openen van een &trade; ServiceNow-ticket.

Zie Werkstroomautomatisering voor meer informatie over de automatische en handmatige Security Center voor het uitvoeren [van uw werkstromen.](workflow-automation.md)

Zie voor meer informatie Logic Apps maken [van Azure Logic Apps](../logic-apps/logic-apps-overview.md).


### <a name="quick-fix-for-bulk-resources-generally-available"></a>snelle oplossing voor bulkresources die algemeen beschikbaar zijn

Met de vele taken die een gebruiker als onderdeel van de secure score krijgt, kan de mogelijkheid om problemen in een grote vloot effectief op te lossen lastig worden.

Als u het herstel van onjuiste beveiligingsconfiguraties wilt vereenvoudigen en snel aanbevelingen voor een groot aantal resources kunt herstellen en uw beveiligingsscore wilt verbeteren, gebruikt u snelle oplossing herstel.

Met deze bewerking kunt u de resources selecteren waarin u het herstel wilt toepassen en een herstelactie starten waarmee de instelling namens u wordt geconfigureerd.

Snelle oplossing is tegenwoordig algemeen beschikbaar voor klanten als onderdeel van Security Center pagina met aanbevelingen.

Zie voor welke aanbevelingen snelle oplossing is ingeschakeld in de [referentiehandleiding voor beveiligingsaanbevelingen.](recommendations-reference.md)


### <a name="scan-container-images-for-vulnerabilities-preview"></a>Containerafbeeldingen scannen op beveiligingsproblemen (preview)

Azure Security Center kunt nu containerafbeeldingen scannen in Azure Container Registry op beveiligingsproblemen.

Het scannen van afbeeldingen werkt door het containerafbeeldingsbestand te parseren en vervolgens te controleren of er bekende beveiligingsproblemen zijn (powered by Qualys).

De scan zelf wordt automatisch geactiveerd bij het pushen van nieuwe containerafbeeldingen naar Azure Container Registry. Gevonden beveiligingsproblemen worden aan het Security Center en opgenomen in de Azure-beveiligingsscore, samen met informatie over hoe u deze kunt patchen om de kwetsbaarheid voor aanvallen te verminderen die ze hebben toegestaan.


### <a name="additional-regulatory-compliance-standards-preview"></a>Aanvullende nalevingsstandaarden voor regelgeving (preview)

Het dashboard Naleving van regelgeving biedt inzicht in uw nalevingsstatus op basis Security Center evaluaties. Het dashboard laat zien hoe uw omgeving voldoet aan de controles en vereisten die zijn bepaald door specifieke regelgevingsstandaarden en branchebenchmarks en bevat prescriptieve aanbevelingen voor het aanpakken van deze vereisten.

Het dashboard voor naleving van regelgeving ondersteunt tot nu toe vier ingebouwde standaarden: Azure CIS 1.1.0, PCI-DSS, ISO 27001 en SOC-TSP. We kondigen nu de openbare preview-versie aan van aanvullende ondersteunde standaarden: NIST SP 800-53 R4, SWIFT CSP CSCF v2020, Canada Federal PBMM en UK Official samen met UK NHS. We brengen ook een bijgewerkte versie van Azure CIS 1.1.0 uit, die meer besturingselementen van de standaard beslaat en de uitbreidingsmogelijkheden verbetert.

[Meer informatie over het aanpassen van de set standaarden in uw dashboard voor naleving van regelgeving.](update-regulatory-compliance-packages.md)


### <a name="threat-protection-for-azure-kubernetes-service-preview"></a>Bedreigingsbeveiliging voor Azure Kubernetes Service (preview)

Kubernetes wordt snel de nieuwe standaard voor het implementeren en beheren van software in de cloud. Weinig mensen hebben uitgebreide ervaring met Kubernetes en veel mensen richten zich alleen op algemene engineering en beheer en kijken naar het beveiligingsaspect. De Kubernetes-omgeving moet zorgvuldig worden geconfigureerd om veilig te zijn. Zo zorgt u ervoor dat er geen containergerichte aanvalsoppervlakdeuren openstaan voor aanvallers. Security Center breidt de ondersteuning in de containerruimte uit naar een van de snelst groeiende services in Azure: Azure Kubernetes Service (AKS).

De nieuwe mogelijkheden in deze openbare preview-versie zijn onder andere:

- **Zichtbaarheid & detectie:** continue detectie van beheerde AKS-exemplaren binnen Security Center geregistreerde abonnementen van uw bedrijf.
- **Aanbevelingen voor beveiligingsscore: actie-items** om klanten te helpen voldoen aan de aanbevolen beveiligingsprocedures voor AKS en hun beveiligingsscore te verhogen. Aanbevelingen omvatten items zoals 'Op rollen gebaseerd toegangsbeheer moet worden gebruikt om de toegang tot een Kubernetes Service-cluster te beperken'.
- **Detectie van** bedreigingen: analyse op basis van host en cluster, zoals 'Een geprivilegieerde container gedetecteerd'.


### <a name="virtual-machine-vulnerability-assessment-preview"></a>Evaluatie van beveiligingsleed van virtuele machines (preview)

Toepassingen die in virtuele machines zijn geïnstalleerd, kunnen vaak beveiligingsproblemen hebben die kunnen leiden tot een inbreuk op de virtuele machine. We kondigen aan dat de Security Center standard-laag een ingebouwde evaluatie van beveiligingsleedsleed bevat voor virtuele machines, zonder extra kosten. Met de evaluatie van beveiligingsleeds, powered by Qualys in de openbare preview, kunt u continu alle geïnstalleerde toepassingen op een virtuele machine scannen om kwetsbare toepassingen te vinden en de bevindingen in de ervaring van de Security Center-portal presenteren. Security Center zorgt voor alle implementatiebewerkingen, zodat de gebruiker geen extra werk hoeft te doen. We zijn van plan om in de toekomst opties voor evaluatie van beveiligingsleed te bieden ter ondersteuning van de unieke bedrijfsbehoeften van onze klanten.

[Meer informatie over evaluaties van beveiligingsleeds voor uw Azure Virtual Machines](deploy-vulnerability-assessment-vm.md).


### <a name="advanced-data-security-for-sql-servers-on-azure-virtual-machines-preview"></a>Geavanceerde gegevensbeveiliging voor SQL-servers op Azure Virtual Machines (preview)

Azure Security Center ondersteuning voor bedreigingsbeveiliging en evaluatie van beveiligingslekken voor SQL-DATABASE's die worden uitgevoerd op IaaS-VM's is nu beschikbaar als preview-versie.

[Evaluatie van beveiligingsproblemen](../azure-sql/database/sql-vulnerability-assessment.md) is een eenvoudig te configureren service waarmee u potentiële zwakke plekken in de beveiliging van de database kunt detecteren, volgen en verhelpen. Het biedt inzicht in uw beveiligingsstatus als onderdeel van de Azure-beveiligingsscore en bevat de stappen voor het oplossen van beveiligingsproblemen en het verbeteren van uw databasebeveiliging.

[Advanced Threat Protection](../azure-sql/database/threat-detection-overview.md) detecteert afwijkende activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot of misbruik te maken van uw SQL-server. Uw database wordt continu bewaakt op verdachte activiteiten en er worden actiegerichte beveiligingswaarschuwingen weergegeven voor afwijkende patronen voor databasetoegang. Deze waarschuwingen bieden de details van verdachte activiteiten en aanbevolen acties om de bedreiging te onderzoeken en te beperken.


### <a name="support-for-custom-policies-preview"></a>Ondersteuning voor aangepast beleid (preview)

Azure Security Center ondersteunt nu aangepast beleid (in preview).

Onze klanten willen hun huidige dekking voor beveiligingsevaluaties in Security Center uitbreiden met hun eigen beveiligingsevaluaties op basis van beleidsregels die ze in Azure Policy. Met ondersteuning voor aangepast beleid is dit nu mogelijk.

Deze nieuwe beleidsregels maken deel uit van de Security Center aanbevelingen, de secure score en het dashboard voor naleving van regelgeving. Met de ondersteuning voor aangepaste beleidsregels kunt u nu een aangepast initiatief maken in Azure Policy, dit vervolgens als een beleid toevoegen in Security Center en visualiseren als een aanbeveling.


### <a name="extending-azure-security-center-coverage-with-platform-for-community-and-partners"></a>Uitbreiding Azure Security Center dekking met platform voor community's en partners

Gebruik Security Center om niet alleen aanbevelingen van Microsoft te ontvangen, maar ook van bestaande oplossingen van partners zoals Check Point, Tenable en CyberArk. Er komen nog veel meer integraties aan.  Met de eenvoudige onboardingstroom van Security Center kunt u uw bestaande oplossingen verbinden met Security Center, zodat u uw aanbevelingen voor beveiligingsstatus op één plek kunt bekijken, uniforme rapporten kunt uitvoeren en alle mogelijkheden van Security Center kunt gebruiken ten opzichte van zowel ingebouwde als partneraanbevelingen. U kunt ook Security Center exporteren naar partnerproducten.

[Meer informatie over Microsoft Intelligent Security Association.](https://www.microsoft.com/security/partnerships/intelligent-security-association)



### <a name="advanced-integrations-with-export-of-recommendations-and-alerts-preview"></a>Geavanceerde integraties met het exporteren van aanbevelingen en waarschuwingen (preview)

Als u scenario's op ondernemingsniveau wilt inschakelen boven op Security Center, is het nu mogelijk om Security Center-waarschuwingen en -aanbevelingen op andere plaatsen te gebruiken, met uitzondering van de Azure Portal of API. Deze kunnen rechtstreeks worden geëxporteerd naar een Event Hub en naar Log Analytics-werkruimten. Hier zijn enkele werkstromen die u kunt maken rond deze nieuwe mogelijkheden:

- Met exporteren naar Log Analytics-werkruimte kunt u aangepaste dashboards maken met Power BI.
- Met exporteren naar Event Hub kunt u Security Center-waarschuwingen en -aanbevelingen in realtime exporteren naar uw SIEM's van derden, naar een oplossing van derden of Azure Data Explorer.


### <a name="onboard-on-prem-servers-to-security-center-from-windows-admin-center-preview"></a>Onboarding van on-Security Center servers vanuit Windows Admin Center (preview)

Windows Admin Center is een beheerportal voor Windows-servers die niet zijn geïmplementeerd in Azure. Deze portal biedt verschillende Azure-beheermogelijkheden, zoals back-up- en systeemupdates. We hebben onlangs de mogelijkheid toegevoegd om deze niet-Azure-servers rechtstreeks vanuit de Windows Admin Center onboarding-ervaring te onboarden.

Met deze nieuwe ervaring kunnen gebruikers een WAC-server onboarden voor Azure Security Center en de beveiligingswaarschuwingen en aanbevelingen rechtstreeks in de Windows Admin Center weergeven.


## <a name="september-2019"></a>September 2019

De updates in september zijn onder meer:

 - [Regels beheren met verbeteringen in adaptieve toepassingsbesturingselementen](#managing-rules-with-adaptive-application-controls-improvements)
 - [Aanbevelingen voor containerbeveiliging controleren met behulp van Azure Policy](#control-container-security-recommendation-using-azure-policy)

### <a name="managing-rules-with-adaptive-application-controls-improvements"></a>Regels beheren met verbeteringen in adaptieve toepassingsbesturingselementen

De ervaring met het beheren van regels voor virtuele machines met adaptieve toepassingsregelaars is verbeterd. Azure Security Center adaptieve toepassingsregelaars van Azure Security Center kunt u bepalen welke toepassingen op uw virtuele machines kunnen worden uitgevoerd. Naast een algemene verbetering van regelbeheer kunt u met een nieuw voordeel bepalen welke bestandstypen worden beveiligd wanneer u een nieuwe regel toevoegt.

[Meer informatie over adaptieve toepassingsregelaars](security-center-adaptive-application.md).


### <a name="control-container-security-recommendation-using-azure-policy"></a>Aanbevelingen voor containerbeveiliging controleren met behulp van Azure Policy

Azure Security Center aanbeveling om beveiligingsproblemen in containerbeveiliging op te verhelpen, kan nu worden ingeschakeld of uitgeschakeld via Azure Policy.

Als u uw ingeschakelde beveiligingsbeleid wilt weergeven, Security Center u de pagina Beveiligingsbeleid openen.


## <a name="august-2019"></a>Augustus 2019

De updates in augustus zijn onder meer:

 - [Jit-VM-toegang (Just-In-Time) voor Azure Firewall](#just-in-time-jit-vm-access-for-azure-firewall)
 - [Herstel met één klik om uw beveiligingsstatus te verbeteren (preview)](#single-click-remediation-to-boost-your-security-posture-preview)
 - [Beheer in meerdere tenants](#cross-tenant-management)

### <a name="just-in-time-jit-vm-access-for-azure-firewall"></a>Jit-VM-toegang (Just-In-Time) voor Azure Firewall 

Jit-VM-toegang (Just-In-Time) voor Azure Firewall is nu algemeen beschikbaar. Gebruik deze om uw Azure Firewall beveiligde omgevingen te beveiligen naast uw met NSG beveiligde omgevingen.

Jit VM-toegang vermindert de blootstelling aan netwerk volumetrische aanvallen door beheerde toegang tot VM's alleen wanneer dat nodig is, met behulp van uw NSG en Azure Firewall regels.

Wanneer u JIT voor uw VM's inschakelen, maakt u een beleid dat bepaalt welke poorten moeten worden beveiligd, hoe lang de poorten open moeten blijven en goedgekeurde IP-adressen van waaraf deze poorten toegankelijk zijn. Met dit beleid kunt u de controle houden over wat gebruikers kunnen doen wanneer ze toegang aanvragen.

Aanvragen worden vastgelegd in het Azure-activiteitenlogboek, zodat u de toegang eenvoudig kunt controleren en controleren. De Pagina Just-In-Time helpt u ook om snel bestaande VM's te identificeren waarop JIT is ingeschakeld en VM's waarop JIT wordt aanbevolen.

Meer informatie over [Azure Firewall](../firewall/overview.md).


### <a name="single-click-remediation-to-boost-your-security-posture-preview"></a>Herstel met één klik om uw beveiligingsstatus te verbeteren (preview)

Beveiligingsscore is een hulpprogramma waarmee u de beveiligingsstatus van uw workload kunt beoordelen. Het beoordeelt uw beveiligingsaanbevelingen en geeft deze prioriteit, zodat u weet welke aanbevelingen u het eerst moet uitvoeren. Dit helpt u bij het vinden van de meest ernstige beveiligingsproblemen om onderzoek te prioriteren.

Om het herstel van onjuiste beveiligingsconfiguraties te vereenvoudigen en u te helpen uw beveiligingsscore snel te verbeteren, hebben we een nieuwe mogelijkheid toegevoegd waarmee u een aanbeveling voor een groot aantal resources in één klik kunt herstellen.

Met deze bewerking kunt u de resources selecteren waarin u het herstel wilt toepassen en een herstelactie starten waarmee de instelling namens u wordt geconfigureerd.

Zie voor welke aanbevelingen een snelle oplossing is ingeschakeld in de [referentiehandleiding voor beveiligingsaanbevelingen.](recommendations-reference.md)


### <a name="cross-tenant-management"></a>Beheer in meerdere tenants

Security Center ondersteunt nu beheerscenario's voor meerdere tenants als onderdeel van Azure Lighthouse. Hierdoor kunt u meer inzicht krijgen en de beveiligingsstatus van meerdere tenants in Security Center. 

[Meer informatie over beheerervaringen voor meerdere tenants.](security-center-cross-tenant-management.md)


## <a name="july-2019"></a>Juli 2019

### <a name="updates-to-network-recommendations"></a>Updates voor netwerkaanbevelingen

Azure Security Center (ASC) heeft nieuwe netwerkaanbevelingen geïntroduceerd en een aantal bestaande aanbevelingen verbeterd. Het gebruik van Security Center zorgt nu voor nog betere netwerkbeveiliging voor uw resources. 

[Meer informatie over netwerkaanbevelingen.](recommendations-reference.md#recs-networking)


## <a name="june-2019"></a>Juni 2019

### <a name="adaptive-network-hardening---generally-available"></a>Adaptieve netwerkharding - algemeen beschikbaar

Een van de grootste aanvalsoppervlakken voor workloads die in de openbare cloud worden uitgevoerd, zijn verbindingen van en naar het openbare internet. Onze klanten vinden het moeilijk om te weten welke NSG-regels (Netwerkbeveiligingsgroep) moeten worden gebruikt om ervoor te zorgen dat Azure-workloads alleen beschikbaar zijn voor de vereiste bronbereiken. Met deze functie leert Security Center het netwerkverkeer en de connectiviteitspatronen van Azure-workloads en worden aanbevelingen voor NSG-regels gegeven voor virtuele machines die zijn gericht op internet. Dit helpt onze klant om zijn netwerktoegangsbeleid beter te configureren en de blootstelling aan aanvallen te beperken. 

[Meer informatie over adaptieve netwerkverharding.](security-center-adaptive-network-hardening.md)