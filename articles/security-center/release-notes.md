---
title: Releaseopmerkingen voor Azure Security Center
description: Een beschrijving van wat er nieuw is en gewijzigd is in Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 04/11/2021
ms.author: memildin
ms.openlocfilehash: bb79bbe918bb1a68b982ae4d44739c2c77a11434
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719147"
---
# <a name="whats-new-in-azure-security-center"></a>Wat is er nieuw in Azure Security Center?

Azure Security Center is in actieve ontwikkeling en wordt voortdurend verbeterd. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt deze pagina u informatie over nieuwe functies, opgeloste fouten en afgeschafte functionaliteit.

Deze pagina wordt regelmatig bijgewerkt. Kom hier daarom regelmatig terug. 

Zie [Belangrijke aanstaande wijzigingen aan Azure Security Center](upcoming-changes.md) voor meer informatie over *geplande* wijzigingen die binnenkort in Security Center worden doorgevoerd. 

> [!TIP]
> Als u op zoek bent naar items die ouder zijn dan zes maanden, vindt u deze in het [Archief voor nieuwe functies in Azure AD in Azure Security Center](release-notes-archive.md).

## <a name="april-2021"></a>April 2021

De updates in april zijn onder meer:
- [Onlangs opgehaalde containerregister-afbeeldingen worden nu wekelijks opnieuw gescand (algemene beschikbaarheid)](#recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability)
- [Kubernetes Azure Defender voor Kubernetes en Kubernetes-implementaties in meerdere cloudomgevingen beveiligen (preview)](#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview)
- [Vier nieuwe aanbevelingen met betrekking tot gastconfiguratie (preview)](#four-new-recommendations-related-to-guest-configuration-preview)
- [CMK-aanbevelingen verplaatst naar aanbevolen procedures voor beveiligingsbeheer](#cmk-recommendations-moved-to-best-practices-security-control)
- [11 Azure Defender waarschuwingen afgeschaft](#11-azure-defender-alerts-deprecated)
- [Twee aanbevelingen van het beveiligingsbeheer Systeemupdates toepassen zijn afgeschaft](#two-recommendations-from-apply-system-updates-security-control-were-deprecated)

### <a name="recently-pulled-container-registry-images-are-now-rescanned-weekly-general-availability"></a>Onlangs opgehaalde containerregister-afbeeldingen worden nu wekelijks opnieuw gescand (algemene beschikbaarheid)

Azure Defender voor containerregisters bevat een ingebouwde scanner voor beveiligingsprobleem. Met deze scanner worden alle afbeeldingen die u naar het register pusht en alle binnen de afgelopen 30 dagen opgehaalde afbeeldingen direct gescand.

Er worden elke dag nieuwe beveiligingsproblemen ontdekt. Met deze update worden containerafbeeldingen die in de afgelopen 30 dagen uit uw registers zijn gehaald, elke week **opnieuw** gescand. Dit zorgt ervoor dat nieuw ontdekte beveiligingsproblemen worden geïdentificeerd in uw afbeeldingen.

Scannen wordt per afbeelding in rekening gebracht, dus er worden geen extra kosten in rekening gebracht voor deze scans.

Meer informatie over deze scanner in [Azure Defender voor containerregisters](defender-for-container-registries-usage.md)gebruiken om uw afbeeldingen te scannen op beveiligingsproblemen.


### <a name="use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-preview"></a>Kubernetes Azure Defender voor Kubernetes en Kubernetes-implementaties in meerdere cloudomgevingen beveiligen (preview)

Azure Defender voor Kubernetes breidt de mogelijkheden voor beveiliging tegen bedreigingen uit om uw clusters te beschermen waar ze ook worden geïmplementeerd. Dit is mogelijk gemaakt door te integreren met [kubernetes Azure Arc kubernetes](../azure-arc/kubernetes/overview.md) en de nieuwe [uitbreidingsmogelijkheden](../azure-arc/kubernetes/extensions.md). 

Wanneer u Azure Arc hebt ingeschakeld voor uw niet-Azure Kubernetes-clusters, biedt een nieuwe aanbeveling van Azure Security Center aan om de Azure Defender-extensie met slechts een paar klikken te implementeren.

Gebruik de aanbeveling (**Azure Arc kubernetes-clusters** moeten de extensie van Azure Defender hebben geïnstalleerd) en de extensie om Kubernetes-clusters te beveiligen die zijn geïmplementeerd in andere cloudproviders, hoewel niet op hun beheerde Kubernetes-services.

Deze integratie tussen Azure Security Center, Azure Defender kubernetes Azure Arc Kubernetes biedt:

- Eenvoudig inrichten van de Azure Defender-extensie voor niet-beveiligde Azure Arc Kubernetes-clusters (handmatig en op schaal)
- Bewaking van de Azure Defender-extensie en de inrichtingstoestand ervan vanuit de Azure Arc-portal
- Beveiligingsaanbevelingen van Security Center worden gerapporteerd op de nieuwe pagina Beveiliging van Azure Arc Portal
- Geïdentificeerde beveiligingsrisico's van Azure Defender worden gerapporteerd op de nieuwe pagina Beveiliging van Azure Arc Portal
- Azure Arc Kubernetes-clusters zijn geïntegreerd in het Azure Security Center platform en de ervaring

Meer informatie vindt [u in Azure Defender voor Kubernetes gebruiken met uw on-premises Kubernetes-clusters en Kubernetes-clusters in meerdere cloudomgevingen.](defender-for-kubernetes-azure-arc.md)

:::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center aanbeveling voor het implementeren van de extensie Azure Defender kubernetes Azure Arc Kubernetes-clusters." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

### <a name="four-new-recommendations-related-to-guest-configuration-preview"></a>Vier nieuwe aanbevelingen met betrekking tot gastconfiguratie (preview)

De [gastconfiguratie-extensierapporten van](../governance/policy/concepts/guest-configuration.md) Azure Security Center om ervoor te zorgen dat de in-guest-instellingen van uw virtuele machines worden gehard. De extensie is niet vereist voor Arc-servers omdat deze is opgenomen in de Arc Connected Machine-agent. Voor de extensie is een door het systeem beheerde identiteit op de computer vereist.

We hebben vier nieuwe aanbevelingen toegevoegd aan Security Center om het meeste uit deze extensie te kunnen gebruiken.

- In twee aanbevelingen wordt u gevraagd de extensie en de vereiste door het systeem beheerde identiteit te installeren:
    - **Gastconfiguratie-extensie moet worden geïnstalleerd op uw computers**
    - **De extensie voor gastconfiguratie van virtuele machines moet worden geïmplementeerd met een door het systeem toegewezen beheerde identiteit**

- Wanneer de extensie is geïnstalleerd en wordt uitgevoerd, wordt begonnen met het controleren van uw computers en wordt u gevraagd instellingen te beperken, zoals de configuratie van het besturingssysteem en de omgevingsinstellingen. Deze twee aanbevelingen vragen u om uw Windows- en Linux-machines te harden zoals beschreven:
    - **Windows Defender Exploit Guard moet zijn ingeschakeld op uw computers**
    - **Voor verificatie met Linux-machines moeten SSH-sleutels zijn vereist**

Meer informatie in [Inzicht Azure Policy gastconfiguratie van de Azure Policy.](../governance/policy/concepts/guest-configuration.md)

### <a name="cmk-recommendations-moved-to-best-practices-security-control"></a>CMK-aanbevelingen verplaatst naar aanbevolen procedures voor beveiligingsbeheer

Het beveiligingsprogramma van elke organisatie bevat vereisten voor gegevensversleuteling. Standaard worden de gegevens van Azure-klanten 'at rest' versleuteld met door de service beheerde sleutels. Door de klant beheerde sleutels (CMK) zijn echter vaak vereist om te voldoen aan regelgevingsnormen. Met CMK's kunt u uw gegevens versleutelen met [Azure Key Vault](../key-vault/general/overview.md) sleutel die door u is gemaakt en eigendom is. Dit geeft u volledige controle en verantwoordelijkheid voor de levenscyclus van de sleutel, inclusief roulatie en beheer.

Azure Security Center beveiligingscontroles zijn logische groepen gerelateerde beveiligingsaanbevelingen en weerspiegelen uw kwetsbare aanvalsoppervlakken. Elk besturingselement heeft een maximum aantal punten dat u aan uw veilige score kunt toevoegen als u alle aanbevelingen in het besturingselement herstelt voor al uw resources. Het **beveiligingsbeheer voor best practices voor beveiliging** implementeren is nul punten waard. Aanbevelingen in dit besturingselement hebben dus geen invloed op uw beveiligde score.

De onderstaande aanbevelingen worden verplaatst  naar beveiligingsbesturingselement aanbevolen procedures voor beveiliging implementeren om de optionele aard ervan beter weer te geven. Deze overstap zorgt ervoor dat deze aanbevelingen de meest geschikte controle hebben om te voldoen aan hun doelstelling.

- Voor Azure Cosmos DB-accounts moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest
- Azure Machine Learning-werkruimten moeten worden versleuteld met een door de klant beheerde sleutel (CMK)
- Cognitive Services-accounts moeten gegevensversleuteling inschakelen met een door de klant beheerde sleutel (CMK)
- Containerregisters moeten worden versleuteld met een door de klant beheerde sleutel (CMK)
- Voor met SQL beheerde exemplaren moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest
- Voor SQL-servers moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest
- Voor opslagaccounts moet een CMK (door de klant beheerde sleutel) worden gebruikt voor versleuteling

Voor meer informatie over welke aanbevelingen zich in elk beveiligingsbeheer bevinden, raadpleegt u [Beveiligingscontroles en de bijbehorende aanbevelingen](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="11-azure-defender-alerts-deprecated"></a>11 Azure Defender waarschuwingen afgeschaft

De Azure Defender waarschuwingen die hieronder worden vermeld, zijn afgeschaft.

- Nieuwe waarschuwingen vervangen deze twee waarschuwingen en bieden een betere dekking:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | PREVIEW - MicroBurst toolkit "Get-AzureDomainInfo" function run detected (Preview: functie-run 'Get-AzureDomainInfo' van MicroBurst-toolkit gedetecteerd) |
    | ARM_MicroBurstRunbook    | PREVIEW - Functie-run 'Get-AzurePasswords' van MicroBurst-toolkit gedetecteerd  |
    |                          |                                                                          |

- Deze negen waarschuwingen hebben betrekking op een Azure Active Directory Identity Protection-connector (IPC) die al is afgeschaft:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Onbekende aanmeldingseigenschappen |
    | AnonymousLogin      | Anoniem IP-adres          |
    | InfectedDeviceLogin | Aan malware gekoppeld IP-adres     |
    | ImpossibleTravel    | Ongewoon traject               |
    | MaliciousIP         | Schadelijk IP-adres          |
    | LeakedCredentials   | Gelekte referenties            |
    | PasswordSpray       | Wachtwoordspray                |
    | LeakedCredentials   | Azure AD-bedreigingsinformatie  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 
    > [!TIP]
    > Deze negen IPC-waarschuwingen zijn nooit Security Center waarschuwingen. Ze maken deel uit van de Azure Active Directory (AAD) Identity Protection-connector (IPC) die ze naar de Security Center. De afgelopen twee jaar zijn de enige klanten die deze waarschuwingen hebben gezien organisaties die de export (van de connector naar ASC) in 2019 of eerder hebben geconfigureerd. AAD IPC blijft deze weergeven in eigen waarschuwingssystemen en ze zijn nog steeds beschikbaar in Azure Sentinel. De enige wijziging is dat ze niet meer worden weergegeven in Security Center.

### <a name="two-recommendations-from-apply-system-updates-security-control-were-deprecated"></a>Twee aanbevelingen van het beveiligingsbeheer Systeemupdates toepassen zijn afgeschaft 

De volgende twee aanbevelingen zijn afgeschaft en de wijzigingen kunnen leiden tot een lichte invloed op uw secure score:

- **Uw machines moeten opnieuw worden opgestart om systeemupdates toe te passen**
- **De bewakingsagent moet op uw computers zijn geïnstalleerd.** Deze aanbeveling heeft alleen betrekking op on-premises machines en een deel van de logica wordt overgedragen naar een andere aanbeveling. Statusproblemen met **Log Analytics-agent** moeten worden opgelost op uw computers

We raden u aan om uw configuraties voor continue export en werkstroomautomatisering te controleren om te zien of deze aanbevelingen in deze configuraties zijn opgenomen. Bovendien moeten dashboards of andere bewakingshulpprogramma's die deze kunnen gebruiken, dienovereenkomstig worden bijgewerkt.

Meer informatie over deze aanbevelingen kunt u vinden op [de referentiepagina voor beveiligingsaanbevelingen.](recommendations-reference.md)


## <a name="march-2021"></a>Maart 2021

Updates in maart zijn onder andere:

- [Azure Firewall management geïntegreerd in Security Center](#azure-firewall-management-integrated-into-security-center)
- [Evaluatie van SQL-beveiligingsle beveiligingsles bevat nu de ervaring Regel uitschakelen (preview)](#sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview)
- [Azure Monitor Workbooks geïntegreerd in Security Center en drie meegeleverde sjablonen](#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)
- [Het dashboard voor naleving van regelgeving bevat nu Azure Audit-rapporten (preview)](#regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview)
- [Aanbevelingsgegevens kunnen worden bekeken in Azure Resource Graph met 'Verkennen in ARG'](#recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg)
- [Updates voor de beleidsregels voor het implementeren van werkstroomautomatisering](#updates-to-the-policies-for-deploying-workflow-automation)
- [Twee verouderde aanbevelingen schrijven geen gegevens meer rechtstreeks naar het Azure-activiteitenlogboek](#two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log)
- [Verbeteringen op de pagina Aanbevelingen](#recommendations-page-enhancements)


### <a name="azure-firewall-management-integrated-into-security-center"></a>Azure Firewall management geïntegreerd in Security Center

Wanneer u Azure Security Center opent, wordt als eerste de overzichtspagina weergegeven. 

Dit interactieve dashboard biedt een uniforme weergave van de beveiligingsstatus van uw hybride cloudworkloads. Bovendien worden er naast andere informatie beveiligingswaarschuwingen en informatie over de dekking weergegeven.

Als onderdeel van het bekijken van uw beveiligingsstatus vanuit een centrale ervaring, hebben we de Azure Firewall Manager in dit dashboard geïntegreerd. U kunt nu de firewalldekkingsstatus in alle netwerken controleren en de beleidsregels centraal beheren Azure Firewall vanaf Security Center.

Meer informatie over dit dashboard [op Azure Security Center overzichtspagina van .](overview-page.md)

:::image type="content" source="media/release-notes/overview-dashboard-firewall-manager.png" alt-text="Security Center dashboard met een tegel voor Azure Firewall":::


### <a name="sql-vulnerability-assessment-now-includes-the-disable-rule-experience-preview"></a>Evaluatie van SQL-beveiligingsles bevat nu de ervaring Regel uitschakelen (preview)

Security Center bevat een ingebouwde scanner voor beveiligingsproblemen waarmee u potentiële beveiligingsproblemen in de database kunt ontdekken, volgen en herstellen. De resultaten van uw evaluatiescans bieden een overzicht van de beveiligingstoestand van uw SQL-machines en details van eventuele beveiligingsresultaten.

Als u een organisatorische behoefte hebt om een resultaat te negeren in plaats van dit te herstellen, kunt u het eventueel uitschakelen. Uitgeschakelde resultaten hebben geen invloed op uw beveiligingsscore en genereren geen ongewenste ruis.

Meer informatie vindt u in [Specifieke bevindingen uitschakelen.](defender-for-sql-on-machines-vulnerability-assessment.md#disable-specific-findings-preview)



### <a name="azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided"></a>Azure Monitor Workbooks geïntegreerd in Security Center en drie meegeleverde sjablonen

Als onderdeel van Ignite Spring 2021 hebben we een geïntegreerde Azure Monitor Workbooks-ervaring in Security Center.

U kunt de nieuwe integratie gebruiken om gebruik te maken van de out-of-the-box-sjablonen uit Security Center van uw galerie. Met behulp van werkmapsjablonen kunt u dynamische en visuele rapporten openen en bouwen om de beveiligingsstatus van uw organisatie bij te houden. Daarnaast kunt u nieuwe werkmappen maken op basis van Security Center-gegevens of andere ondersteunde gegevenstypen en snel community-werkmappen implementeren vanuit de GitHub-community van Security Center.

Er worden drie sjablonenrapporten aangeboden:

- **Beveiligde score in de tijd:** de scores en wijzigingen in uw abonnementen bijhouden in aanbevelingen voor uw resources
- **Systeemupdates:** ontbrekende systeemupdates weergeven op resources, besturingssysteem, ernst en meer
- **Resultaten van evaluatie van beveiligingslesources:** bekijk de resultaten van scans van beveiligingsprobleem van uw Azure-resources

Meer informatie over het gebruik van deze rapporten of het bouwen van uw eigen [uitgebreide, interactieve rapporten van Security Center maken.](custom-dashboards-azure-workbooks.md)

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Rapport Over een veilige score in de tijd":::


### <a name="regulatory-compliance-dashboard-now-includes-azure-audit-reports-preview"></a>Het dashboard voor naleving van regelgeving bevat nu Azure Audit-rapporten (preview)

Op de werkbalk van het dashboard voor naleving van regelgeving kunt u nu Azure- en Dynamics-certificeringsrapporten downloaden. 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="Werkbalk van het dashboard voor naleving van regelgeving":::

U kunt het tabblad voor de relevante rapportentypen (PCI, SOC, ISO en andere) selecteren en filters gebruiken om de specifieke rapporten te vinden die u nodig hebt.

Meer informatie over het [beheren van de standaarden in uw dashboard voor naleving van regelgeving.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard.png" alt-text="De lijst met beschikbare Azure Audit-rapporten filteren":::



### <a name="recommendation-data-can-be-viewed-in-azure-resource-graph-with-explore-in-arg"></a>Aanbevelingsgegevens kunnen worden bekeken in Azure Resource Graph met 'Verkennen in ARG'

De pagina's met aanbevelingsdetails bevatten nu de werkbalkknop Verkennen in ARG. Gebruik deze knop om een query Azure Resource Graph te openen en de gegevens van de aanbeveling te verkennen, exporteren en delen.

Azure Resource Graph (ARG) biedt directe toegang tot resourcegegevens in uw cloudomgevingen met robuuste mogelijkheden voor filteren, groeperen en sorteren. Het is een snelle en efficiënte manier om via een programma of vanuit de Azure Portal.

Meer informatie over [Azure Resource Graph (ARG).](../governance/resource-graph/index.yml)

:::image type="content" source="media/release-notes/explore-in-resource-graph.png" alt-text="Verken aanbevelingsgegevens in Azure Resource Graph.":::


### <a name="updates-to-the-policies-for-deploying-workflow-automation"></a>Updates voor de beleidsregels voor het implementeren van werkstroomautomatisering

Het automatiseren van de processen voor bewaking en reageren op incidenten van uw organisatie kan de tijd die nodig is om beveiligingsincidenten te onderzoeken en te verhelpen aanzienlijk verbeteren.

We bieden drie Azure Policy'DeployIfNotExist'-beleid voor het maken en configureren van procedures voor werkstroomautomatisering, zodat u uw automatiseringen in uw organisatie kunt implementeren:

|Doel  |Beleid  |Beleids-id  |
|---------|---------|---------|
|Werkstroomautomatisering voor beveiligingswaarschuwingen|[Werkstroomautomatisering implementeren voor Azure Security Center-waarschuwingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Werkstroomautomatisering voor beveiligingsaanbevelingen|[Werkstroomautomatisering implementeren voor Azure Security Center-aanbevelingen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
|Werkstroomautomatisering voor wijzigingen in de naleving van regelgeving|[Werkstroomautomatisering implementeren Azure Security Center naleving van regelgeving](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-509122b9-ddd9-47ba-a5f1-d0dac20be63c)|509122b9-ddd9-47ba-a5f1-d0dac20be63c|
||||

Er zijn twee updates voor de functies van dit beleid:

- Wanneer deze worden toegewezen, blijven ze ingeschakeld door afdwinging.
- U kunt deze beleidsregels nu aanpassen en parameters bijwerken, zelfs nadat ze al zijn geïmplementeerd. Als een gebruiker bijvoorbeeld een andere evaluatiesleutel wil toevoegen of een bestaande evaluatiesleutel wil bewerken, kan deze sleutel worden gebruikt.

Aan de slag met [sjablonen voor werkstroomautomatisering](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Meer informatie over het [automatiseren van antwoorden op Security Center triggers](workflow-automation.md).


### <a name="two-legacy-recommendations-no-longer-write-data-directly-to-azure-activity-log"></a>Twee verouderde aanbevelingen schrijven geen gegevens meer rechtstreeks naar het Azure-activiteitenlogboek 

Security Center geeft de gegevens voor bijna alle beveiligingsaanbevelingen door aan Azure Advisor die deze op zijn beurt naar het [Azure-activiteitenlogboek schrijft.](../azure-monitor/essentials/activity-log.md)

Voor twee aanbevelingen worden de gegevens tegelijkertijd rechtstreeks naar het Azure-activiteitenlogboek geschreven. Met deze wijziging stopt Security Center schrijven van gegevens voor deze verouderde beveiligingsaanbevelingen rechtstreeks naar het activiteitenlogboek. In plaats daarvan exporteren we de gegevens naar Azure Advisor zoals we doen voor alle andere aanbevelingen.

De twee verouderde aanbevelingen zijn:
- Statusproblemen met eindpuntbescherming moeten worden opgelost voor uw machines
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld

Als u toegang hebt tot informatie voor deze twee aanbevelingen in de categorie 'Aanbeveling van het type TaskDiscovery' van het activiteitenlogboek, is dit niet meer beschikbaar.


### <a name="recommendations-page-enhancements"></a>Verbeteringen op de pagina Aanbevelingen 

We hebben een verbeterde versie van de lijst met aanbevelingen uitgebracht om in één oogopslag meer informatie weer te geven.

Op de pagina ziet u nu het volgende:

1. De maximale score en huidige score voor elk besturingselement voor beveiliging.
1. Pictogrammen die tags vervangen, zoals **Snelle oplossing** en **Preview.**
1. Een nieuwe kolom met het [beleidsinitiatief dat is](security-policy-concept.md) gerelateerd aan elke aanbeveling. Deze kolom wordt weergegeven wanneer Groepeer op besturingselementen is uitgeschakeld.

:::image type="content" source="media/release-notes/recommendations-grid-enhancements.png" alt-text="Verbeteringen aan Azure Security Center pagina met aanbevelingen - maart 2021" lightbox="media/release-notes/recommendations-grid-enhancements.png":::

:::image type="content" source="media/release-notes/recommendations-grid-enhancements-initiatives.png" alt-text="Verbeteringen aan Azure Security Center 'platte' lijst met aanbevelingen - maart 2021" lightbox="media/release-notes/recommendations-grid-enhancements-initiatives.png":::

Meer informatie in [Beveiligingsaanbevelingen in Azure Security Center](security-center-recommendations.md).


## <a name="february-2021"></a>Februari 2021

Updates in februari zijn onder andere:

- [Nieuwe pagina met beveiligingswaarschuwingen in de Azure Portal voor algemene beschikbaarheid (GA)](#new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga)
- [Aanbevelingen voor beveiliging van Kubernetes-workloads uitgebracht voor algemene beschikbaarheid](#kubernetes-workload-protection-recommendations-released-for-general-availability-ga)
- [Microsoft Defender voor eindpuntintegratie met Azure Defender ondersteunt nu Windows Server 2019 en Windows 10 Virtual Desktop (WVD) (in preview)](#microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview)
- [Directe koppeling naar beleid vanaf de pagina met aanbevelingsdetails](#direct-link-to-policy-from-recommendation-details-page)
- [Aanbeveling voor SQL-gegevensclassificatie heeft geen invloed meer op uw beveiligingsscore](#sql-data-classification-recommendation-no-longer-affects-your-secure-score)
- [Werkstroomautomatiseringen kunnen worden geactiveerd door wijzigingen in evaluaties van naleving van regelgeving (in preview)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)
- [Verbeteringen van de pagina Assetinventarisatie](#asset-inventory-page-enhancements)


### <a name="new-security-alerts-page-in-the-azure-portal-released-for-general-availability-ga"></a>Nieuwe pagina met beveiligingswaarschuwingen in de Azure Portal voor algemene beschikbaarheid (GA)

De pagina Beveiligingswaarschuwingen van Azure Security Center is opnieuw ontworpen en biedt nu het volgende:

- **Verbeterde triage-ervaring voor** waarschuwingen: de lijst bevat aanpasbare filters en groeperingsopties om waarschuwingen te beperken en de aandacht te richten op de meest relevante bedreigingen.
- **Meer informatie in de lijst met waarschuwingen,** zoals MITRE ATT&ACK-tactieken.
- **Knop voor het maken van voorbeeldwaarschuwingen:** om de mogelijkheden Azure Defender evalueren en uw waarschuwingen te testen. configuratie (voor SIEM-integratie, e-mailmeldingen en werkstroomautomatiseringen), kunt u voorbeeldwaarschuwingen maken van alle Azure Defender plannen.
- **Afstemming op de incidentervaring** van Azure Sentinel: voor klanten die beide producten gebruiken, is het schakelen tussen deze producten nu een eenvoudigere ervaring en is het eenvoudig om van elkaar te leren.
- **Betere prestaties voor** grote waarschuwingenlijsten.
- **Toetsenbordnavigatie** door de lijst met waarschuwingen.
- **Waarschuwingen van Azure Resource Graph**: u kunt query's uitvoeren op waarschuwingen in Azure Resource Graph, de Kusto-achtige API voor al uw resources. Dit is ook handig als u uw eigen waarschuwingendashboards bouwt. [Meer informatie over Azure Resource Graph](../governance/resource-graph/index.yml).
- **Voorbeeldwaarschuwingen maken:** zie Voorbeeldwaarschuwingen genereren om voorbeeldwaarschuwingen te maken op Azure Defender [nieuwe ervaring met waarschuwingen.](security-center-alert-validation.md#generate-sample-azure-defender-alerts)

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center lijst met beveiligingswaarschuwingen van de Azure Security Center":::


### <a name="kubernetes-workload-protection-recommendations-released-for-general-availability-ga"></a>Aanbevelingen voor beveiliging van Kubernetes-workloads uitgebracht voor algemene beschikbaarheid

Met trots kondigen we de algemene beschikbaarheid (GA) aan van de reeks aanbevelingen voor kubernetes-workloadbeveiligingen.

Om ervoor te zorgen dat Kubernetes-workloads standaard zijn beveiligd, heeft Security Center aanbevelingen voor beveiliging op Kubernetes-niveau toegevoegd, waaronder afdwingingsopties met Kubernetes-toegangsbeheer.

Wanneer de Azure Policy-invoegsel voor Kubernetes is geïnstalleerd op uw Azure Kubernetes Service-cluster (AKS), wordt elke aanvraag voor de Kubernetes API-server gecontroleerd op basis van de vooraf gedefinieerde set aanbevolen procedures ( weergegeven als 13 beveiligingsaanbevelingen) voordat deze in het cluster worden opgeslagen. Vervolgens kunt u instellingen configureren om de aanbevolen procedures af te dwingen en ze te verplichten voor toekomstige workloads.

U kunt er bijvoorbeeld voor zorgen dat containers met machtigingen niet moeten worden gemaakt, en toekomstige aanvragen hiervoor worden dan geblokkeerd.

Meer informatie vindt u in [Aanbevolen procedures voor workloadbeveiliging met behulp van Kubernetes Admission Control](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).

> [!NOTE]
> Hoewel de aanbevelingen in preview waren, hebben ze een AKS-clusterresource niet in orde gemaakt en zijn ze niet opgenomen in de berekeningen van uw beveiligde score. met deze GA-aankondiging worden deze opgenomen in de berekening van de score. Als u ze nog niet hebt herstelt, kan dit leiden tot een lichte invloed op uw secure score. Herstel deze waar mogelijk, zoals beschreven in [Aanbevelingen herstellen in Azure Security Center](security-center-remediate-recommendations.md).


### <a name="microsoft-defender-for-endpoint-integration-with-azure-defender-now-supports-windows-server-2019-and-windows-10-virtual-desktop-wvd-in-preview"></a>Microsoft Defender voor eindpuntintegratie met Azure Defender ondersteunt nu Windows Server 2019 en Windows 10 Virtual Desktop (WVD) (in preview)

Microsoft Defender for Endpoint is een holistische, cloudoplossing voor eindpuntbeveiliging. Het biedt op risico'vulnerability management en evaluatie, evenals eindpuntdetectie en -respons (EDR). Zie Protect [your endpoints with Security Center's integrated EDR solution: Microsoft Defender for Endpoint](security-center-wdatp.md)(Uw eindpunten beveiligen met de geïntegreerde EDR-oplossing van Security Center: Microsoft Defender for Endpoint) voor een volledige lijst met de voordelen van het gebruik van Defender for Endpoint in Azure Security Center.

Wanneer u een Azure Defender voor servers op een Windows-server inschakelen, wordt een licentie voor Defender for Endpoint opgenomen in het abonnement. Als u al een Azure Defender voor servers hebt ingeschakeld en u Windows 2019-servers in uw abonnement hebt, ontvangen deze automatisch Defender for Endpoint met deze update. Er is geen handmatige actie vereist. 

Ondersteuning is nu uitgebreid met Windows Server 2019 en [Windows Virtual Desktop (WVD).](../virtual-desktop/overview.md)

> [!NOTE]
> Als u Defender for Endpoint inschakelen op een Windows Server 2019-computer, zorg er dan voor dat deze voldoet aan de vereisten die worden beschreven in Enable [the Microsoft Defender for Endpoint integration (Microsoft Defender voor eindpuntintegratie inschakelen).](security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration)

### <a name="direct-link-to-policy-from-recommendation-details-page"></a>Directe koppeling naar beleid vanaf de pagina met aanbevelingsdetails

Wanneer u de details van een aanbeveling bekijkt, is het vaak handig om het onderliggende beleid te kunnen zien. Voor elke aanbeveling die door een beleid wordt ondersteund, is er een nieuwe koppeling op de pagina met aanbevelingsdetails:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Koppeling naar Azure Policy pagina voor het specifieke beleid dat een aanbeveling ondersteunt":::

Gebruik deze koppeling om de beleidsdefinitie weer te geven en de evaluatielogica te controleren. 

Als u de lijst met aanbevelingen in [](recommendations-reference.md)onze referentiehandleiding voor beveiligingsaanbevelingen bekijkt, ziet u ook koppelingen naar de pagina's met beleidsdefinitie:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="De pagina Azure Policy voor een specifiek beleid rechtstreeks openen vanaf de Azure Security Center aanbevelingenpagina" lightbox="media/release-notes/view-policy-definition-from-documentation.png":::


### <a name="sql-data-classification-recommendation-no-longer-affects-your-secure-score"></a>Aanbeveling voor SQL-gegevensclassificatie heeft geen invloed meer op uw beveiligingsscore
De aanbeveling **Gevoelige gegevens in uw SQL-databases moeten worden geclassificeerd,** zijn niet langer van invloed op uw beveiligingsscore. Dit is de enige aanbeveling in het **besturingselement** Beveiliging voor gegevensclassificatie toepassen, zodat het besturingselement nu een beveiligingsscorewaarde van 0 heeft.

Zie Beveiligingsmaatregelen en hun aanbevelingen voor een volledige lijst met alle beveiligingscontroles in Security Center, samen met hun scores en een lijst met de aanbevelingen in [elk besturingselement.](secure-score-security-controls.md#security-controls-and-their-recommendations)

### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview"></a>Werkstroomautomatiseringen kunnen worden geactiveerd door wijzigingen in evaluaties van naleving van regelgeving (in preview)
We hebben een derde gegevenstype toegevoegd aan de triggeropties voor uw werkstroomautomatiseringen: wijzigingen in evaluaties van naleving van regelgeving.

Meer informatie over het gebruik van de hulpprogramma's voor werkstroomautomatisering in [Antwoorden op Security Center triggers automatiseren.](workflow-automation.md)

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="Wijzigingen in evaluaties van naleving van regelgeving gebruiken om een werkstroomautomatisering te activeren" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::


### <a name="asset-inventory-page-enhancements"></a>Verbeteringen op de pagina Assetinventarisatie
Security Center van de assetinventarispagina is op de volgende manieren verbeterd:

- Samenvattingen boven aan de pagina bevatten nu Niet-geregistreerde **abonnementen,** met het aantal abonnementen zonder Security Center ingeschakeld.

    :::image type="content" source="media/release-notes/unregistered-subscriptions.png" alt-text="Aantal niet-geregistreerde abonnementen in de samenvattingen boven aan de pagina assetinventarisatie":::

- Filters zijn uitgebreid en uitgebreid met:
    - **Tellingen:** elk filter geeft het aantal resources weer dat voldoet aan de criteria van elke categorie

        :::image type="content" source="media/release-notes/counts-in-inventory-filters.png" alt-text="Tellingen in de filters op de assetinventarispagina van Azure Security Center":::

    - **Bevat een uitzonderingsfilter** (optioneel) - verklein de resultaten tot resources die wel/geen uitzonderingen hebben. Dit filter wordt niet standaard weergegeven, maar is toegankelijk via de **knop Filter** toevoegen.

        :::image type="content" source="media/release-notes/adding-contains-exemption-filter.gif" alt-text="Het filter 'contains exemption' toevoegen aan Azure Security Center de pagina assetinventarisatie":::

Meer informatie over het verkennen [en beheren van uw resources met assetinventarisatie.](asset-inventory.md)

## <a name="january-2021"></a>Januari 2021

Updates in januari zijn onder andere:

- [Azure Security Benchmark is nu het standaardbeleidsinitiatief voor Azure Security Center](#azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center)
- [Evaluatie van beveiligingsleed voor on-premises machines en computers met meerdere cloudomgevingen wordt uitgebracht voor algemene beschikbaarheid](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga)
- [De beveiligde score voor beheergroepen is nu beschikbaar in de preview-versie](#secure-score-for-management-groups-is-now-available-in-preview)
- [Beveiligde score-API wordt uitgebracht voor algemene beschikbaarheid (GA)](#secure-score-api-is-released-for-general-availability-ga)
- [Daning van DNS-beveiligingen toegevoegd aan Azure Defender voor App Service](#dangling-dns-protections-added-to-azure-defender-for-app-service)
- [Multi-cloudconnectoren worden uitgebracht voor algemene beschikbaarheid (GA)](#multi-cloud-connectors-are-released-for-general-availability-ga)
- [Volledige aanbevelingen van uw beveiligde score voor abonnementen en beheergroepen vrijstellen](#exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups)
- [Gebruikers kunnen nu tenantbrede zichtbaarheid aanvragen bij hun globale beheerder](#users-can-now-request-tenant-wide-visibility-from-their-global-administrator)
- [Er zijn 35 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen](#35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [CSV-export van een gefilterde lijst met aanbevelingen](#csv-export-of-filtered-list-of-recommendations)
- [Resources die niet van toepassing zijn, worden nu gerapporteerd als 'Compatibel' in Azure Policy evaluaties](#not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments)
- [Wekelijkse momentopnamen van gegevens over de veilige score en naleving van regelgeving exporteren met continue export (preview)](#export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview)


### <a name="azure-security-benchmark-is-now-the-default-policy-initiative-for-azure-security-center"></a>Azure Security Benchmark is nu het standaardbeleidsinitiatief voor Azure Security Center

Azure Security Benchmark is de door Microsoft ontworpen, Azure-specifieke set richtlijnen voor best practices voor beveiliging en naleving op basis van algemene nalevingsframeworks. Deze algemeen gerespecteerde benchmark bouwt voort op de controles van het [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) met een focus op cloudgerichte beveiliging.

In de afgelopen maanden is Security Center lijst met ingebouwde beveiligingsaanbevelingen aanzienlijk toegenomen om onze dekking van deze benchmark uit te breiden.

In deze release is de benchmark de basis voor Security Center van de Security Center volledig geïntegreerd als het standaardbeleidsinitiatief. 

Alle Azure-services hebben een pagina beveiligingsbasislijn in hun documentatie. Dit is [bijvoorbeeld Security Center basislijn van .](security-baseline.md) Deze basislijnen zijn gebaseerd op Azure Security Benchmark.

Als u het dashboard voor Security Center naleving van regelgeving gebruikt, ziet u twee exemplaren van de benchmark tijdens een periode:

:::image type="content" source="media/release-notes/regulatory-compliance-with-azure-security-benchmark.png" alt-text="Azure Security Center dashboard voor naleving van regelgeving met de Azure Security-benchmark":::

Bestaande aanbevelingen worden niet beïnvloed en naarmate de benchmark groeit, worden wijzigingen automatisch doorgevoerd in Security Center. 

Zie de volgende pagina's voor meer informatie:

- [Meer informatie over Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [De set standaarden in uw dashboard voor naleving van regelgeving aanpassen](update-regulatory-compliance-packages.md)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-is-released-for-general-availability-ga"></a>Evaluatie van beveiligingsprobleem voor on-premises machines en computers met meerdere cloudservices wordt vrijgegeven voor algemene beschikbaarheid

In oktober hebben we een preview voor het scannen van servers met Azure Arc met [Azure Defender voor servers](defender-for-servers-introduction.md) geïntegreerde evaluatie van beveiligingsproblemen (mogelijk gemaakt door Qualys).

Deze is nu uitgebracht voor algemene beschikbaarheid.

Wanneer u Azure Arc op uw niet-Azure-machines hebt ingeschakeld, kan de geïntegreerde scanner voor beveiligingsproblemen handmatig en op schaal worden geïmplementeerd.

Met deze update kunt u de mogelijkheden van **Azure Defender voor servers** gebruiken om uw beheerprogramma voor beveiligingsproblemen te consolideren voor al uw Azure- en niet-Azure-assets.

Belangrijkste functies:

- Bewaken van de inrichtingsstatus van de scanner voor de evaluatie van beveiligingsproblemen van Azure Arc-machines
- Inrichten van de geïntegreerde agent voor de evaluatie van beveiligingsproblemen op niet-beveiligde Azure Arc-machines onder Windows en Linux (handmatig en op schaal)
- Ontvangen en analyseren van gedetecteerde beveiligingsproblemen afkomstig van geïmplementeerde agents (handmatig en op schaal)
- Geïntegreerde ervaring voor Azure-VM's en Azure Arc-machines

[Meer informatie over het implementeren van de geïntegreerde scanner voor beveiligings problemen op uw hybride machines](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Meer informatie over servers met Azure Arc](../azure-arc/servers/index.yml).


### <a name="secure-score-for-management-groups-is-now-available-in-preview"></a>De beveiligde score voor beheergroepen is nu beschikbaar in de preview-versie

Op de pagina met de beveiligde score worden nu de geaggregeerde beveiligde scores voor uw beheergroepen weergegeven, naast het abonnementsniveau. U kunt nu dus de lijst met beheergroepen in uw organisatie en de score voor elke beheergroep bekijken.

:::image type="content" source="media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Bekijk de beveiligde scores voor uw beheergroepen.":::

Meer informatie over [beveiligingsscore en besturingselementen voor beveiliging in Azure Security Center](secure-score-security-controls.md).

### <a name="secure-score-api-is-released-for-general-availability-ga"></a>Beveiligde score-API wordt uitgebracht voor algemene beschikbaarheid (GA)

U hebt nu toegang tot uw score via de [API voor de secure score.](/rest/api/securitycenter/securescores/) De API-methoden bieden de flexibiliteit om query's uit te voeren op de gegevens en uw eigen rapportagemechanisme te bouwen van uw beveiligingsscores in de loop van de tijd. Bijvoorbeeld:

- de **API voor beveiligde scores** gebruiken om de score voor een specifiek abonnement op te halen
- de API **voor besturingselementen voor beveiligingsscores** gebruiken om de beveiligingscontroles en de huidige score van uw abonnementen weer te bieden

Meer informatie over externe hulpprogramma's die mogelijk worden gemaakt met de API voor de secure score in het gebied voor de beveiligde [score van onze GitHub-community.](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)

Meer informatie over [beveiligingsscore en besturingselementen voor beveiliging in Azure Security Center](secure-score-security-controls.md).


### <a name="dangling-dns-protections-added-to-azure-defender-for-app-service"></a>Daning van DNS-beveiligingen toegevoegd aan Azure Defender voor App Service

Overnames van subdomeinen vormen een veelvoorkomende bedreiging met hoge urgentie voor organisaties. Een overname van een subdomein kan plaatsvinden wanneer u een DNS-record hebt die naar een website met deprovisioning wijst. Dergelijke DNS-records worden ook wel 'dansende DNS'-vermeldingen genoemd. CNAME-records zijn met name kwetsbaar voor deze bedreiging. 

Met subdomeinovernames kunnen bedreigingsac actors verkeer dat is bedoeld voor het domein van een organisatie, omleiden naar een site die schadelijke activiteiten uitvoeren.

Azure Defender voor App Service detecteert nu aaneengebaarde DNS-vermeldingen wanneer een App Service website buiten gebruik wordt gesteld. Dit is het moment waarop de DNS-vermelding verwijst naar een niet-bestaande resource en uw website kwetsbaar is voor een overname van een subdomein. Deze beveiligingen zijn beschikbaar, ongeacht of uw domeinen worden beheerd met Azure DNS of een externe domeinregistrar en van toepassing zijn op zowel App Service in Windows als App Service op Linux.

Meer informatie:

- [App Service waarschuwingsverwijzingstabel](alerts-reference.md#alerts-azureappserv) : bevat twee nieuwe Azure Defender waarschuwingen die worden activeren wanneer een bovenende DNS-vermelding wordt gedetecteerd
- [Blokkerende DNS-vermeldingen voorkomen en overname van subdomein](../security/fundamentals/subdomain-takeover.md) voorkomen : meer informatie over de bedreiging van overname van subdomeinen en het dansbare DNS-aspect
- [Inleiding tot Azure Defender voor App Service](defender-for-app-service-introduction.md)


### <a name="multi-cloud-connectors-are-released-for-general-availability-ga"></a>Multi-cloudconnectoren worden uitgebracht voor algemene beschikbaarheid (GA)

Veel workloads in de cloud beslaan meerdere cloudplatforms, dus moeten cloudbeveiligingsservices hetzelfde doen.

Azure Security Center biedt bescherming voor workloads in Azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

Door uw AWS- of GCP-accounts te verbinden, worden hun systeemeigen beveiligingshulpprogramma's zoals AWS Security Hub en GCP Security Command Center geïntegreerd in Azure Security Center.

Deze mogelijkheid betekent dat Security Center zichtbaarheid en beveiliging biedt in alle belangrijke cloudomgevingen. Enkele voordelen van deze integratie:

- Automatische inrichting van agent: Security Center gebruikt Azure Arc om de Log Analytics-agent te implementeren in uw AWS-exemplaren
- Beleidsbeheer
- Beheer van beveiligingsproblemen
- Geïntegreerde eindpuntdetectie en -respons (EDR)
- Detectie van onjuiste beveiligingsconfiguraties
- Eén weergave met beveiligingsaanbevelingen van alle cloudproviders
- Al uw resources opnemen in Security Center van de secure score-berekeningen van uw bedrijf
- Evaluaties van naleving van regelgeving van uw AWS- en GCP-resources

Selecteer in Security Center menu de optie **Multi-cloudconnectoren** en u ziet de opties voor het maken van nieuwe connectors:

:::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Knop 'AWS-account toevoegen' op de pagina voor meerdere cloudconnectors van Security Center":::

Meer informatie in:
- [Uw AWS-accounts verbinden met Azure Security Center](quickstart-onboard-aws.md)
- [Uw GCP-accounts verbinden met Azure Security Center](quickstart-onboard-gcp.md)


### <a name="exempt-entire-recommendations-from-your-secure-score-for-subscriptions-and-management-groups"></a>Volledige aanbevelingen van uw beveiligde score voor abonnementen en beheergroepen vrijstellen

We breiden de mogelijkheid tot uitzondering uit met volledige aanbevelingen. Het bieden van extra opties voor het afstemmen van de beveiligingsaanbevelingen die Security Center doen voor uw abonnementen, beheergroep of resources.

Af en toe wordt een resource vermeld als beschadigd wanneer u weet dat het probleem is opgelost door een hulpprogramma van derden dat Security Center niet is gedetecteerd. Of een aanbeveling wordt in een bereik weer gegeven waar u denkt dat deze er niet bij hoort. De aanbeveling is mogelijk niet geschikt voor een specifiek abonnement. Of misschien heeft uw organisatie besloten om de risico's met betrekking tot de specifieke resource of aanbeveling te accepteren.

Met deze preview-functie kunt u nu een uitzondering maken voor een aanbeveling voor:

- **Een resource vrijstellen** om ervoor te zorgen dat deze in de toekomst niet wordt vermeld met de resources die niet in orde zijn en geen invloed heeft op uw beveiligde score. De resource wordt weergegeven als niet van toepassing en de reden wordt weergegeven als 'uitgesloten' met de specifieke reden die u selecteert.

- **Een abonnement of beheergroep** vrijstellen om ervoor te zorgen dat de aanbeveling geen invloed heeft op uw beveiligde score en in de toekomst niet wordt weergegeven voor het abonnement of de beheergroep. Dit heeft betrekking op bestaande resources en alle resources die u in de toekomst maakt. De aanbeveling wordt gemarkeerd met de specifieke reden die u selecteert voor het bereik dat u hebt geselecteerd.

Meer informatie vindt u in [Resources en aanbevelingen uit uw beveiligde score vrijstellen.](exempt-resource.md)



### <a name="users-can-now-request-tenant-wide-visibility-from-their-global-administrator"></a>Gebruikers kunnen nu tenantbrede zichtbaarheid aanvragen bij hun globale beheerder

Als een gebruiker geen machtigingen heeft om Security Center zien, ziet hij of zij nu een koppeling om machtigingen aan te vragen bij de globale beheerder van de organisatie. De aanvraag bevat de gevraagde rol en de reden waarom deze nodig is.

:::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banner die een gebruiker informeert dat deze machtigingen voor de hele tenant kan aanvragen.":::

Meer informatie in [Tenantbrede machtigingen aanvragen wanneer u onvoldoende machtigingen hebt.](tenant-wide-permissions-management.md#request-tenant-wide-permissions-when-yours-are-insufficient)


### <a name="35-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Er zijn 35 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen

[Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction) is het standaardbeleidsinitiatief in Azure Security Center. 

Om de dekking van deze benchmark te vergroten, zijn de volgende 35 preview-aanbevelingen toegevoegd aan Security Center.

> [!TIP]
> Preview-aanbevelingen zorgen er niet voor dat een resource als beschadigd wordt weergegeven en ze worden niet opgenomen in de berekeningen van uw beveiligde score. Herstel ze waar mogelijk, zodat zij wanneer de preview-periode afloopt zullen bijdragen aan uw score. Zie [Aanbevelingen oplossen in Azure Security Center](security-center-remediate-recommendations.md) voor meer informatie over hoe u kunt reageren op deze aanbevelingen.

| Beveiligingsmaatregelen                     | Nieuwe aanbevelingen                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Versleuteling at-rest inschakelen            | - Voor Azure Cosmos DB-accounts moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest<br>- Azure Machine Learning-werkruimten moeten worden versleuteld met een door de klant beheerde sleutel (CMK)<br>- BYOK-gegevensversleuteling moet zijn ingeschakeld voor MySQL-servers<br>- BYOK-gegevensversleuteling moet zijn ingeschakeld voor PostgreSQL-servers<br>- Voor Cognitive Services-accounts moet gegevensversleuteling zijn ingeschakeld met een door de klant beheerde sleutel (CMK)<br>- Containerregisters moeten worden versleuteld met een door de klant beheerde sleutel (CMK)<br>- Voor SQL Managed Instance moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest<br>- Voor SQL Server moeten door de klant beheerde sleutels worden gebruikt voor het versleutelen van data-at-rest<br>- Voor opslagaccounts moet een door de klant beheerde sleutel (CMK) worden gebruikt voor versleuteling                                                                                                                                                              |
| Best practices voor beveiliging implementeren    | - Abonnementen moeten een e-mailadres van contactpersonen voor beveiligingsproblemen bevatten<br> - Automatisch inrichten van de Log Analytics-agent moet zijn ingeschakeld voor uw abonnement<br> - E-mailmelding voor waarschuwingen met hoge urgentie moet zijn ingeschakeld<br> - E-mailmelding aan abonnementseigenaar voor waarschuwingen met hoge urgentie moet zijn ingeschakeld<br> - Beveiliging tegen leegmaken moet zijn ingeschakeld voor sleutelkluizen<br> - Voorlopig verwijderen moet zijn ingeschakeld voor sleutelkluizen |
| Toegang en machtigingen beheren        | - Clientcertificaten (binnenkomende clientcertificaten) moeten voor functie-apps zijn ingeschakeld |
| Toepassingen beschermen tegen DDoS-aanvallen | - WAF (Web Application Firewall) moet zijn ingeschakeld voor Application Gateway<br> - WAF (Web Application Firewall) moet zijn ingeschakeld voor de Azure Front Door-service |
| Onbevoegde netwerktoegang beperken | - De firewall moet zijn ingeschakeld in Key Vault<br> - Er moet een privé-eindpunt worden geconfigureerd voor Key Vault<br> - Voor App Configuration moeten privékoppelingen worden gebruikt<br> - Azure Cache voor Redis moet zich in een virtueel netwerk bevinden<br> - Voor Azure Event Grid-domeinen moet Private Link worden gebruikt<br> - Voor Azure Event Grid-onderwerpen moete Private Link worden gebruikt<br> - Voor Azure Machine Learning-werkruimten moet Private Link worden gebruikt<br> - Voor Azure SignalR Service moet Private Link worden gebruikt<br> - Azure Spring Cloud moet netwerkinjectie gebruiken<br> - Containerregisters mogen geen onbeperkte netwerktoegang toestaan<br> - Voor containerregisters moet Private Link worden gebruikt<br> - Openbare netwerktoegang moet worden uitgeschakeld voor MariaDB-servers<br> - Openbare netwerktoegang moet worden uitgeschakeld voor MySQL-servers<br> - Openbare netwerktoegang moet worden uitgeschakeld voor PostgreSQL-servers<br> - Voor een opslagaccount moet een Private Link-verbinding worden gebruikt<br> - Opslagaccounts moeten netwerktoegang beperken met behulp van regels voor virtuele netwerken<br> - Voor VM Image Builder-sjablonen moet Private Link worden gebruikt|
|                                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

Gerelateerde links:

- [Meer informatie over Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Meer informatie over Azure Database for MariaDB](../mariadb/overview.md)
- [Meer informatie over Azure Database for MySQL](../mysql/overview.md)
- [Meer informatie over Azure Database for PostgreSQL](../postgresql/overview.md)




### <a name="csv-export-of-filtered-list-of-recommendations"></a>CSV-export van een gefilterde lijst met aanbevelingen 

In november 2020 hebben we filters toegevoegd aan de aanbevelingspagina ([Lijst met aanbevelingen bevat nu filters](#recommendations-list-now-includes-filters)). In december hebben we die filters uitgebreid ([De aanbevelingspagina bevat nieuwe filters voor omgeving, ernst en beschikbare reacties](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)). 

Met deze aankondiging veranderen we het gedrag van de knop **Downloaden naar CSV**, zodat de CSV-export alleen de aanbevelingen omvat die momenteel worden weergegeven in de gefilterde lijst. 

In de onderstaande afbeeldingen ziet u bijvoorbeeld dat de lijst is gefilterd in twee aanbevelingen. Het gegenereerde CSV-bestand omvat de statusgegevens voor elke resource die door deze twee aanbevelingen wordt beïnvloed.   

:::image type="content" source="media/security-center-managing-and-responding-alerts/export-to-csv-with-filters.png" alt-text="Gefilterde aanbevelingen exporteren naar een CSV-bestand":::

Meer informatie in [Beveiligingsaanbevelingen in Azure Security Center](security-center-recommendations.md).


### <a name="not-applicable-resources-now-reported-as-compliant-in-azure-policy-assessments"></a>Resources die niet van toepassing zijn, worden nu gerapporteerd als 'Compatibel' in Azure Policy evaluaties

Voorheen werden resources die werden geëvalueerd  voor een aanbeveling en die niet van toepassing waren, in Azure Policy als 'Niet-compatibel'. Gebruikersacties kunnen hun status niet wijzigen in 'Compatibel'. Met deze wijziging worden ze gerapporteerd als 'Compatibel' voor betere duidelijkheid.

Dit is alleen van invloed op Azure Policy, waar het aantal compatibele resources zal toenemen. Het is niet van invloed om uw beveiligingsscore in Azure Security Center.


### <a name="export-weekly-snapshots-of-secure-score-and-regulatory-compliance-data-with-continuous-export-preview"></a>Wekelijkse momentopnamen van beveiligde score- en nalevingsgegevens van regelgeving exporteren met continue export (preview)

We hebben een nieuwe preview-functie toegevoegd aan de hulpprogramma's voor continue [export](continuous-export.md) voor het exporteren van wekelijkse momentopnamen van gegevens over de veilige score en naleving van regelgeving.

Wanneer u een continue export definieert, stelt u de exportfrequentie in:

:::image type="content" source="media/release-notes/export-frequency.png" alt-text="De frequentie van uw continue export kiezen":::

- **Streaming:** evaluaties worden in realtime verzonden wanneer de status van een resource wordt bijgewerkt (als er geen updates worden uitgevoerd, worden er geen gegevens verzonden).
- **Momentopnamen:** er wordt elke week een momentopname van de huidige status van alle evaluaties van naleving van regelgeving verzonden (dit is een preview-functie voor wekelijkse momentopnamen van beveiligde scores en nalevingsgegevens van regelgeving).

Meer informatie over de volledige mogelijkheden van deze functie in [Continu exporteren Security Center gegevens.](continuous-export.md)

## <a name="december-2020"></a>December 2020

Updates in december omvatten:

- [Azure Defender voor SQL-servers op computers is algemeen beschikbaar](#azure-defender-for-sql-servers-on-machines-is-generally-available)
- [Azure Defender voor SQL-ondersteuning voor een toegewezen SQL-pool in Azure Synapse Analytics is algemeen beschikbaar](#azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available)
- [Globale beheerders kunnen nu machtigingen op tenantniveau aan zichzelf verlenen](#global-administrators-can-now-grant-themselves-tenant-level-permissions)
- [Twee nieuwe Azure Defender-abonnementen: Azure Defender voor DNS en Azure Defender voor Resource Manager (in preview)](#two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview)
- [Pagina Nieuwe beveiligingswaarschuwingen in Azure Portal (preview)](#new-security-alerts-page-in-the-azure-portal-preview)
- [Revitalized Security Center-ervaring in Azure SQL Database & SQL Managed Instance](#revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance)
- [Tools en filters voor inventarisatie van activa bijgewerkt](#asset-inventory-tools-and-filters-updated)
- [Aanbeveling over web-apps die SSL-certificaten aanvragen die geen deel meer uitmaken van beveiligde score](#recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score)
- [De aanbevelingspagina bevat nieuwe filters voor omgeving, ernst en beschikbare reacties](#recommendations-page-has-new-filters-for-environment-severity-and-available-responses)
- [Continue export haalt nieuwe gegevenstypen en verbeterde deployifnotexist-beleidsregels op](#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)


### <a name="azure-defender-for-sql-servers-on-machines-is-generally-available"></a>Azure Defender voor SQL-servers op computers is algemeen beschikbaar

Azure Security Center biedt twee Azure Defender-abonnementen voor SQL-servers:

- **Azure Defender voor Azure SQL-databaseservers**: hiermee beveiligt u uw systeemeigen SQL-servers in Azure 
- **Azure Defender sql-servers op computers:** breidt dezelfde beveiligingen uit naar uw SQL-servers in hybride, multi-cloud- en on-premises omgevingen

Voortaan beveiligt **Azure Defender voor SQL** uw databases en de bijbehorende gegevens, waar ze zich ook bevinden.

Azure Defender voor SQL bevat functies voor de evaluatie van beveiligingsproblemen. Het hulpprogramma voor de evaluatie van beveiligingsproblemen biedt de volgende geavanceerde functies:

- **Basislijnconfiguratie** (nieuw!) om de resultaten van het scannen naar beveiligingsproblemen op intelligente wijze te verfijnen, zodat alleen de problemen overblijven die een daadwerkelijk beveiligingsrisico vormen. Nadat u de beveiligingsstatus van uw basislijn hebt ingesteld, worden door het hulpprogramma voor de evaluatie van beveiligingsproblemen alleen afwijkingen van die basislijnstatus gerapporteerd. Resultaten die overeenkomen met de basislijn, worden niet meer meegenomen in vervolgscans. Zo kunnen u en uw analisten zich volledig richten op de belangrijkste problemen.
- **Gedetailleerde benchmarkgegevens** om u *meer inzicht te geven* in de gedetecteerde problemen en in hoe deze gerelateerd zijn aan uw resources.
- **Herstelscripts** om u te helpen bij het oplossen van geïdentificeerde risico's.

Lees meer informatie over [Azure Defender voor SQL](defender-for-sql-introduction.md).


### <a name="azure-defender-for-sql-support-for-azure-synapse-analytics-dedicated-sql-pool-is-generally-available"></a>Azure Defender voor SQL-ondersteuning voor een toegewezen SQL-pool in Azure Synapse Analytics is algemeen beschikbaar

Azure Synapse Analytics (voorheen SQL DW) is een analyseservice die datawarehousing voor ondernemingen en big data-analyses combineert. In Azure Synapse bieden toegewezen SQL-pools de functionaliteit voor datawarehousing voor ondernemingen. Meer informatie leest u in [Wat is Azure Synapse Analytics (voorheen SQL DW)?](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Azure Defender voor SQL beveiligt uw toegewezen SQL-pools met:

- **Geavanceerde bescherming tegen bedreigingen** om bedreigingen en aanvallen te detecteren 
- **Functionaliteit voor de evaluatie van beveiligingsproblemen** om onjuiste beveiligingsconfiguraties te identificeren en te herstellen

Azure Defender voor SQL-ondersteuning voor SQL-pools in Azure Synapse Analytics wordt automatisch toegevoegd aan de bundel Azure SQL-databases in Azure Security Center. Op de pagina met de Synapse-werkruimte in Azure Portal ziet u een nieuw tabblad Azure Defender voor SQL.

Lees meer informatie over [Azure Defender voor SQL](defender-for-sql-introduction.md).


### <a name="global-administrators-can-now-grant-themselves-tenant-level-permissions"></a>Globale beheerders kunnen nu machtigingen op tenantniveau aan zichzelf verlenen

Een gebruiker met de Azure Active Directory-rol **Globale beheerder** kan verantwoordelijkheden hebben voor de hele tenant, maar geen Azure-machtigingen om deze organisatiebrede informatie te bekijken in Azure Security Center. 

Volg de instructies in [Tenantbrede machtigingen toewijzen aan uzelf](tenant-wide-permissions-management.md#grant-tenant-wide-permissions-to-yourself) om machtigingen op tenantniveau toe te wijzen aan uzelf.


### <a name="two-new-azure-defender-plans-azure-defender-for-dns-and-azure-defender-for-resource-manager-in-preview"></a>Twee nieuwe Azure Defender-abonnementen: Azure Defender voor DNS en Azure Defender voor Resource Manager (in preview)

We hebben twee nieuwe, cloudeigen beschermingsmogelijkheden tegen bedreigingen toegevoegd voor uw Azure-omgeving.

Deze nieuwe beschermingen verbeteren uw tolerantie tegen aanvullen van bedreigende actoren en vergroten het aantal Azure-resources dat wordt beschermd door Azure Defender.

- **Azure Defender voor Resource Manager** - bewaakt automatisch alle resourcebeheerbewerkingen die in uw organisatie worden uitgevoerd. Zie voor meer informatie:
    - [Inleiding op Azure Defender voor Resource Manager](defender-for-resource-manager-introduction.md)
    - [Reageren op Azure Defender voor Resource Manager-waarschuwingen](defender-for-resource-manager-usage.md)
    - [Lijst met waarschuwingen door Azure Defender voor Resource Manager](alerts-reference.md#alerts-resourcemanager)

- **Azure Defender voor DNS** - bewaakt continu alle DNS-query's van uw Azure-resources. Zie voor meer informatie:
    - [Inleiding tot Azure Defender voor DNS](defender-for-dns-introduction.md)
    - [Reageren op Azure Defender voor DNS-waarschuwingen](defender-for-dns-usage.md)
    - [Lijst met waarschuwingen door Azure Defender voor DNS](alerts-reference.md#alerts-dns)


### <a name="new-security-alerts-page-in-the-azure-portal-preview"></a>Pagina Nieuwe beveiligingswaarschuwingen in Azure Portal (preview)

De pagina Beveiligingswaarschuwingen van Azure Security Center is opnieuw ontworpen en biedt nu het volgende:

- **Verbeterde sorteerervaring voor waarschuwingen**: hiermee wordt de alertheid op waarschuwingen verbeterd en de focus meer gericht op de meest relevante bedreigingen; de lijst bevat aanpasbare opties voor filters en groeperingen
- **Meer informatie in de lijst met waarschuwingen**, bijvoorbeeld MITRE ATT&ACK-tactieken
- **Knop om voorbeeldwaarschuwingen te maken**: voor het evalueren van de mogelijkheden van Azure Defender en het testen van de configuratie van uw waarschuwingen (voor SIEM-integratie, e-mailmeldingen en werkstroomautomatisering), kunt u voorbeeldwaarschuwingen maken in alle Azure Defender-abonnementen
- **Overeenstemming met de incidentervaring van Azure Sentinel**: klanten die beide producten gebruiken, kunnen deze nu eenvoudiger afwisselen en het ene leren op basis van het andere
- **Betere prestaties** voor lange lijsten met waarschuwingen
- **Toetsenbordnavigatie** via de lijst met waarschuwingen
- **Waarschuwingen van Azure Resource Graph**: u kunt query's uitvoeren op waarschuwingen in Azure Resource Graph, de Kusto-achtige API voor al uw resources. Dit is ook handig als u uw eigen waarschuwingendashboards bouwt. [Meer informatie over Azure Resource Graph](../governance/resource-graph/index.yml).

Als u toegang wilt krijgen tot de nieuwe ervaring, gebruikt u de koppeling Nu proberen in de banner boven aan de pagina met beveiligingswaarschuwingen.

:::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Banner met koppeling naar het nieuwe waarschuwingenproces (preview)":::

Zie [Azure Defender-voorbeeldwaarschuwingen genereren](security-center-alert-validation.md#generate-sample-azure-defender-alerts) als u voorbeeldwaarschuwingen wilt maken vanuit het nieuwe waarschuwingenproces.


### <a name="revitalized-security-center-experience-in-azure-sql-database--sql-managed-instance"></a>Revitalized Security Center-ervaring in Azure SQL Database & SQL Managed Instance 

De Security Center-ervaring in SQL biedt toegang tot de volgende Security Center-functies en Azure Defender voor SQL-functies:

- **Beveiligingsaanbevelingen**: Security Center analyseert periodiek de beveiligingsstatus van alle Azure-resources om mogelijke beveiligingsproblemen op te sporen. Vervolgens worden aanbevelingen gedaan om deze beveiligingsproblemen op te lossen en de beveiligingspostuur van de organisatie te verbeteren.
- **Beveiligingswaarschuwingen**: een detectieservice die Azure SQL-activiteiten continu bewaakt als het gaat om bedreigingen (zoals SQL-injectie, brute-force-aanvallen en misbruik van bevoegdheden). Deze service activeert gedetailleerde en actiegerichte beveiligingswaarschuwingen in Security Center en biedt opties voor verder onderzoek met Azure Sentinel, de Azure-native SIEM-oplossing van Microsoft.
- **Bevindingen**: een service voor evaluatie van beveiligingsproblemen die continu Azure SQL-configuraties bewaakt en beveiligingsproblemen helpt op te lossen. Evaluatiescans bieden een overzicht van de Azure SQL-beveiligingsstatussen met gedetailleerde beveiligingsresultaten.     

:::image type="content" source="media/release-notes/azure-security-center-experience-in-sql.png" alt-text="De beveiligingsfuncties van Azure Security Center voor SQL zijn beschikbaar vanuit Azure SQL":::


### <a name="asset-inventory-tools-and-filters-updated"></a>Tools en filters voor inventarisatie van activa bijgewerkt

De voorraadpagina in Azure Security Center is vernieuwd met de volgende wijzigingen:

- **Hulplijnen en feedback** toegevoegd aan de werkbalk. Hiermee opent u een deelvenster met koppelingen naar gerelateerde informatie en hulpprogramma's. 
- **Het filter abonnementen** is toegevoegd aan de standaardfilters die beschikbaar zijn voor uw resources.
- **Open query**-koppeling om de huidige filteropties te openen als een Azure Resource Graph-query (voorheen 'Weergeven in Resource Graph Explorer' genoemd).
- **Operatoropties** voor elke filter. U kunt nu kiezen uit meer logische operators dan '='. U kunt bijvoorbeeld alle resources zoeken met actieve aanbevelingen waarvan de titels de tekenreeks 'versleutelen' bevatten. 

    :::image type="content" source="media/release-notes/inventory-filter-operators.png" alt-text="Besturingselementen voor de operatoroptie in de filters van assetinventarisatie":::

Voor meer informatie over voorraad, zie [Uw resources verkennen en beheren met assetvoorraad](asset-inventory.md).


### <a name="recommendation-about-web-apps-requesting-ssl-certificates-no-longer-part-of-secure-score"></a>Aanbeveling over web-apps die SSL-certificaten aanvragen, maakt geen deel meer uit van beveiligde score

De aanbeveling "Web-apps moeten een SSL-certificaat aanvragen voor alle binnenkomende aanvragen" is verplaatst van het beveiligingsbeheer **Toegang en machtigingen beheren** (maximaal 4 punten waard) naar **De aanbevolen procedures voor beveiliging implementeren** (geen punten waard). 

Door ervoor te zorgen dat een web-app een certificaat aanvraagt, wordt het zeker veiliger. Voor openbare web-apps is het echter irrelevant. Als u toegang tot uw site hebt via HTTP en niet HTTPS, ontvangt u geen clientcertificaat. Als voor uw toepassing clientcertificaten zijn vereist, moet u dus geen aanvragen voor uw toepassing via HTTP toestaan. Voor meer informatie raadpleegt u [Wederzijdse TLS-verificatie voor Azure App Service configureren](../app-service/app-service-web-configure-tls-mutual-auth.md).

Met deze wijziging is de aanbeveling nu een aanbevolen best practice die geen invloed heeft op uw score. 

Voor meer informatie over welke aanbevelingen zich in elk beveiligingsbeheer bevinden, raadpleegt u [Beveiligingscontroles en de bijbehorende aanbevelingen](secure-score-security-controls.md#security-controls-and-their-recommendations).


### <a name="recommendations-page-has-new-filters-for-environment-severity-and-available-responses"></a>De aanbevelingspagina bevat nieuwe filters voor omgeving, ernst en beschikbare reacties

Azure Security Center bewaakt alle verbonden bronnen en genereert beveiligingsaanbevelingen. Gebruik deze aanbevelingen om uw hybride cloudpostuur te versterken en naleving te volgen van de beleidsregels en standaarden die relevant zijn voor uw organisatie, branche en land.

Naarmate Security Center de dekking en functies blijft uitbreiden, neemt de lijst van beveiligingsaanbevelingen elke maand toe. Zie, bijvoorbeeld, [Er zijn 29 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark).

Met de groeiende lijst moeten de aanbevelingen worden gefilterd om de belangrijkste te vinden. In november hebben we filters toegevoegd aan de aanbevelingspagina (zie [Lijst met aanbevelingen bevat nu filters](#recommendations-list-now-includes-filters)).

De filters die deze maand zijn toegevoegd bieden opties om de lijst met aanbevelingen te verfijnen op basis van:

- **Omgeving**: aanbevelingen weergeven voor uw AWS, GCP of Azure-resources (of een combinatie hiervan)
- **Ernst**: aanbevelingen weergeven op basis van de ernstclassificatie die is ingesteld door Security Center
- **Antwoordacties**: aanbevelingen weergeven op basis van de beschikbaarheid van Security Center-antwoordopties: Snel oplossen, Weigeren en Afdwingen

    > [!TIP]
    > Het filter voor antwoordacties vervangt het filter **Snelle oplossing beschikbaar (Ja/Nee)** . 
    > 
    > Meer informatie over elk van deze antwoordopties:
    > - [Snelle oplossingen voor herstel](security-center-remediate-recommendations.md#quick-fix-remediation)
    > - [Onjuiste configuraties voorkomen met afdwingen/weigeren](prevent-misconfigurations.md)

:::image type="content" source="./media/release-notes/added-recommendations-filters.png" alt-text="Aanbevelingen gegroepeerd op beveiligingsbeheer" lightbox="./media/release-notes/added-recommendations-filters.png":::

### <a name="continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies"></a>Continue export haalt nieuwe gegevenstypen en verbeterde deployifnotexist-beleidsregels op

Met de hulpprogramma's van Azure Security Center voor continue export kunt u de aanbevelingen en waarschuwingen van Security Center exporteren voor gebruik met andere controle hulpprogramma's in uw omgeving.

Met continue export kunt u volledig aanpassen wat waarheen wordt geëxporteerd. Voor meer informatie, zie [Security Center-gegevens continu exporteren](continuous-export.md).

Deze hulpprogramma's zijn verbeterd en uitgebreid op de volgende manieren:

- **Het deployifnotexist-beleid van continue export is verbeterd**. Het beleid is nu:

    - **Controleer of de configuratie is ingeschakeld.** Als dat niet het geval is, wordt het beleid weergegeven als niet-compatibel en wordt er een compatibele resource gemaakt. Meer informatie over de geleverde Azure Policy sjablonen op het tabblad 'Op schaal implementeren met Azure Policy' in [Een continue export instellen.](continuous-export.md#set-up-a-continuous-export)

    - **Ondersteuning voor het exporteren van beveiligingsresultaten.** Wanneer u de Azure Policy sjablonen gebruikt, kunt u uw continue export configureren om bevindingen op te stellen. Dit is relevant bij het exporteren van aanbevelingen met 'subaanbevelingen' (zoals bevindingen van scanners voor beveiligingsevaluatie) of specifieke systeemupdates voor de 'hoofdaanbeveling': "Systeemupdates moeten op uw computers worden geïnstalleerd".
    
    - **Ondersteuning voor het exporteren van beveiligingsscoregegevens.**

- **Gegevens over reglementaire nalevingsbeoordeling toegevoegd (in preview).** U kunt nu voortdurend updates exporteren naar compliance-evaluaties voor regelgeving, met inbegrip van aangepaste initiatieven, naar een Log Analytics-werkruimte of Event Hub. Deze functie is niet beschikbaar in nationale/onafhankelijke clouds.

    :::image type="content" source="media/release-notes/continuous-export-regulatory-compliance-option.png" alt-text="De opties voor het opnemen van informatie van compliance-evaluaties voor regelgeving met uw continue exportgegevens.":::


## <a name="november-2020"></a>November 2020

Updates in november omvatten:

- [Er zijn 29 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen](#29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark)
- [NIST SP 800 171 R2 is toegevoegd aan het nalevingsdashboard van de Security Center](#nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard)
- [Er zijn filters opgenomen in de lijst met aanbevelingen](#recommendations-list-now-includes-filters)
- [De ervaring voor automatische inrichting is verbeterd en uitgebreid](#auto-provisioning-experience-improved-and-expanded)
- [Beveiligingsscore is nu beschikbaar in continue export (preview)](#secure-score-is-now-available-in-continuous-export-preview)
- [Aanbeveling 'Systeemupdates moeten worden geïnstalleerd op uw computers' bevat nu subrecommendations](#system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations)
- [Op de pagina Beleidsbeheer in de Azure-portal wordt nu de status van standaardbeleidstoewijzingen weergegeven](#policy-management-page-in-the-azure-portal-now-shows-status-of-default-policy-assignments)

### <a name="29-preview-recommendations-added-to-increase-coverage-of-azure-security-benchmark"></a>Er zijn 29 preview-aanbevelingen toegevoegd om de dekking van Azure Security Benchmark te verhogen

Azure Security Benchmark is de door Microsoft opgestelde, azure-specifieke set richtlijnen voor best practices voor beveiliging en naleving op basis van algemene nalevingskaders. [Meer informatie over Azure Security-benchmark](../security/benchmarks/introduction.md).

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

- [Meer informatie over Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)
- [Meer informatie over Azure API-apps](../app-service/app-service-web-tutorial-rest-api.md)
- [Meer informatie over Azure functie-apps](../azure-functions/functions-overview.md)
- [Meer informatie over Azure web-apps](../app-service/overview.md)
- [Meer informatie over Azure Database for MariaDB](../mariadb/overview.md)
- [Meer informatie over Azure Database for MySQL](../mysql/overview.md)
- [Meer informatie over Azure Database for PostgreSQL](../postgresql/overview.md)


### <a name="nist-sp-800-171-r2-added-to-security-centers-regulatory-compliance-dashboard"></a>NIST SP 800 171 R2 is toegevoegd aan het nalevingsdashboard van Security Center

De NIST SP 800-171 R2-standaard is nu beschikbaar als ingebouwd initiatief voor gebruik met het nalevingsdashboard voor Azure Security Center. De toewijzingen voor de controles worden beschreven in [Details van het ingebouwde NIST SP 800-171 R2-nalevingsinitiatief](../governance/policy/samples/nist-sp-800-171-r2.md). 

Als u de standaard wilt toepassen op uw abonnementen en uw nalevingsstatus continu wilt bewaken, gebruikt u de instructies in De set standaarden aanpassen in uw [dashboard voor naleving van regelgeving.](update-regulatory-compliance-packages.md)

:::image type="content" source="media/release-notes/nist-sp-800-171-r2-standard.png" alt-text="De NIST SP 800 171 R2-standaard in het nalevingsdashboard van Security Center":::

Zie [NIST SP 800-171 R2](https://csrc.nist.gov/publications/detail/sp/800-171/rev-2/final)voor meer informatie over deze nalevingsstandaard.


### <a name="recommendations-list-now-includes-filters"></a>Er zijn filters opgenomen in de lijst met aanbevelingen

U kunt de lijst met aanbevelingen voor de beveiliging nu filteren op verschillende criteria. In het volgende voorbeeld is de lijst met aanbevelingen gefilterd zodat er aanbevelingen worden weergegeven die voldoen aan de volgende criteria:

- zijn **algemeen beschikbaar** (niet in preview)
- Ze zijn bedoeld voor **opslagaccounts**
- Ze bieden ondersteuning voor **snelle oplossingen** voor herstel

:::image type="content" source="media/release-notes/recommendations-filters.png" alt-text="Filters voor de lijst met aanbevelingen":::


### <a name="auto-provisioning-experience-improved-and-expanded"></a>De ervaring voor automatische inrichting is verbeterd en uitgebreid

Met de functie voor automatische inrichting kunt u de beheeroverhead verminderen door de vereiste extensies op nieuwe (en bestaande) Azure-VM's te installeren, zodat ze gebruik kunnen maken van de beschermingen van Security Center. 

Naarmate Azure Security Center is gegroeid, zijn er meer extensies ontwikkeld en kan Security Center een grotere lijst met resourcetypen bewaken. De hulpprogramma's voor automatische inrichting zijn nu uitgebreid ter ondersteuning van andere extensies en resourcetypen door gebruik te maken van de mogelijkheden van Azure Policy.

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


### <a name="system-updates-should-be-installed-on-your-machines-recommendation-now-includes-subrecommendations"></a>Aanbeveling 'Systeemupdates moeten worden geïnstalleerd op uw computers' bevat nu subrecommendations

De aanbeveling **Er moeten systeemupdates worden geïnstalleerd op uw computers** is verbeterd. De nieuwe versie bevat subrecommendations voor elke ontbrekende update en biedt de volgende verbeteringen:

- Een opnieuw ontworpen ervaring op de Azure Security Center-pagina's van Azure Portal. De pagina met aanbevelingsinformatie voor **Er moeten systeemupdates worden geïnstalleerd op uw computer** bevat de lijst met resultaten zoals hieronder wordt weergegeven. Wanneer u één resultaat selecteert, wordt het deelvenster Details geopend met een koppeling naar de herstelgegevens en een lijst met betrokken resources.

    :::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="Een van de subrecommendations openen in de portal-ervaring voor de bijgewerkte aanbeveling":::

- Uitgebreide gegevens voor de aanbeveling van Azure Resource Graph (ARG). ARG is een Azure-service die is ontworpen voor efficiëntere resourceverkenning. U kunt ARG gebruiken om op schaal een query uit te voeren in een bepaalde set abonnementen, zodat u uw omgeving effectief kunt beheren. 

    Voor Azure Security Center kunt u gebruikmaken van ARG en de [Kusto Query Language (KQL)](/azure/data-explorer/kusto/query/) om query's uit te voeren op een breed scala aan postuurgegevens.

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