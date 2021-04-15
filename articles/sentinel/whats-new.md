---
title: Wat is er nieuw in Azure Sentinel
description: In dit artikel worden nieuwe functies in Azure Sentinel de afgelopen maanden beschreven.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 7f9a8cb54458999d8f20a258bc36241dfdbd0de8
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376032"
---
# <a name="whats-new-in-azure-sentinel"></a>Wat is er nieuw in Azure Sentinel

Dit artikel bevat recente functies die zijn toegevoegd Azure Sentinel en nieuwe functies in gerelateerde services die een verbeterde gebruikerservaring bieden in Azure Sentinel.

Zie onze Tech Community-blogs voor meer informatie over eerdere [geleverde functies.](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)

Genoteerde functies zijn momenteel beschikbaar als preview-versie. De [Aanvullende voorwaarden voor Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.


> [!TIP]
> Onze threat hunting-teams van Microsoft dragen query's, playbooks, werkmappen en notebooks bij aan de [Azure Sentinel Community,](https://github.com/Azure/Azure-Sentinel)inclusief specifieke hunting-query's die uw teams kunnen aanpassen en gebruiken. [](https://github.com/Azure/Azure-Sentinel)
>
> U kunt ook bijdragen! Neem deel aan de [github-community Azure Sentinel Threat GitHub.](https://github.com/Azure/Azure-Sentinel/wiki)
>

## <a name="april-2021"></a>April 2021

- [Tijdlijn voor incidenten (openbare preview)](#incident-timeline-public-preview)

### <a name="incident-timeline-public-preview"></a>Tijdlijn voor incidenten (openbare preview)

Het eerste tabblad op een pagina met incidentdetails is nu de **tijdlijn,** waarin een tijdlijn met waarschuwingen en bladwijzers in het incident wordt weergegeven. De tijdlijn van een incident kan u helpen het incident beter te begrijpen en de tijdlijn van de aanvalsactiviteiten te reconstrueren in de gerelateerde waarschuwingen en bladwijzers.

- Selecteer een item in de tijdlijn om de details ervan te bekijken, zonder de incidentcontext te verlaten
- Filter de inhoud van de tijdlijn om alleen waarschuwingen of bladwijzers, of items met een specifieke ernst of MITRE-tactiek weer te geven.
- U kunt de koppeling **Systeemwaarschuwings-id** selecteren om de volledige record of de koppeling **Gebeurtenissen** weer te geven om de gerelateerde gebeurtenissen in het gebied **Logboeken te** bekijken.

Bijvoorbeeld:

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="Tabblad Tijdlijn voor incidenten":::

Zie Zelfstudie: Incidenten onderzoeken met [Azure Sentinel.](tutorial-investigate-cases.md)

## <a name="march-2021"></a>Maart 2021

- [Werkmappen zo instellen dat ze automatisch worden vernieuwd in de weergavemodus](#set-workbooks-to-automatically-refresh-while-in-view-mode)
- [Nieuwe detecties voor Azure Firewall](#new-detections-for-azure-firewall)
- [Automatiseringsregels en door incidenten geactiveerde playbooks](#automation-rules-and-incident-triggered-playbooks) (inclusief alle nieuwe playbook-documentatie)
- [Nieuwe verrijkingen van waarschuwingen: verbeterde entiteitstoewijzing en aangepaste details](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Uw werkmappen Azure Sentinel opslaan of opslaan als PDF](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Incidentfilters en sorteervoorkeuren worden nu opgeslagen in uw sessie (openbare preview)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Microsoft 365 Defender-incidentintegratie (openbare preview)](#microsoft-365-defender-incident-integration-public-preview)
- [Nieuwe Connectors voor Microsoft-service met Azure Policy](#new-microsoft-service-connectors-using-azure-policy)

### <a name="set-workbooks-to-automatically-refresh-while-in-view-mode"></a>Werkmappen zo instellen dat ze automatisch worden vernieuwd in de weergavemodus

Azure Sentinel kunnen nu de nieuwe Azure Monitor [werkmapgegevens](https://techcommunity.microsoft.com/t5/azure-monitor/azure-workbooks-set-it-to-auto-refresh/ba-p/2228555) automatisch vernieuwen tijdens een weergavesessie.

Selecteer in elke werkmap of werkmapsjabloon :::image type="icon" source="media/whats-new/auto-refresh-workbook.png" border="false"::: **Automatisch vernieuwen om** uw intervalopties weer te geven. Selecteer de optie die u wilt gebruiken voor de huidige weergavesessie en selecteer **Toepassen.**

- Ondersteunde vernieuwingsintervallen variëren **van 5 minuten** **tot 1 dag.**
- Automatisch vernieuwen is standaard uitgeschakeld. Om de prestaties te optimaliseren, wordt automatisch vernieuwen ook uitgeschakeld telkens wanneer u een werkmap sluit en wordt deze niet op de achtergrond uitgevoerd. Schakel automatisch vernieuwen weer in wanneer dat nodig is wanneer u de werkmap de volgende keer opent.
- Automatisch vernieuwen wordt onderbroken tijdens het bewerken van een werkmap en intervallen voor automatisch vernieuwen worden opnieuw gestart telkens wanneer u vanuit de bewerkingsmodus terugschakelt naar de weergavemodus.

    Intervallen worden ook opnieuw gestart als u de werkmap handmatig vernieuwt door de knop :::image type="icon" source="media/whats-new/manual-refresh-button.png" border="false"::: **Vernieuwen te** selecteren.

Zie Zelfstudie: Uw gegevens visualiseren [en bewaken en de](tutorial-monitor-your-data.md) documentatie Azure Monitor meer [informatie.](../azure-monitor/visualize/workbooks-overview.md)

### <a name="new-detections-for-azure-firewall"></a>Nieuwe detecties voor Azure Firewall

Verschillende out-of-the-box-detecties voor Azure Firewall zijn toegevoegd aan het [gebied Analyse](import-threat-intelligence.md#analytics-puts-your-threat-indicators-to-work-detecting-potential-threats) in Azure Sentinel. Met deze nieuwe detecties kunnen beveiligingsteams waarschuwingen ontvangen als computers in het interne netwerk proberen internetdomeinnamen of IP-adressen op te vragen of er verbinding mee te maken die zijn gekoppeld aan bekende IOC's, zoals gedefinieerd in de detectieregelquery.

De nieuwe detecties zijn onder andere:

- [Solorigate Network Baken](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
- [Bekende DOMEINEN EN hashes](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
- [Bekend IP-adres van IRIDIUM](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
- [Bekende phosphorusgroepsdomeinen/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
- [THALLIUM-domeinen die zijn opgenomen in de DCU-takedown](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
- [Bekende aan DEFS gerelateerde maldoc-hash](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)
- [Bekende DOMEINEN VAN DEEEDIUM-groep](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
- [VERORDENINGIUM - Domein- en IP-IOC's - maart 2021](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)


Detecties voor Azure Firewalls worden continu toegevoegd aan de ingebouwde galerie met sjablonen. Als u de meest recente detecties voor Azure Firewall wilt, filtert u onder **Regelsjablonen** de gegevensbronnen **op** Azure Firewall :

:::image type="content" source="media/whats-new/new-detections-analytics-efficiency-workbook.jpg" alt-text="Nieuwe detecties in de werkmap Analyse-efficiëntie":::

Zie Nieuwe detecties voor nieuwe [Azure Firewall in Azure Sentinel.](https://techcommunity.microsoft.com/t5/azure-network-security/new-detections-for-azure-firewall-in-azure-sentinel/ba-p/2244958)

### <a name="automation-rules-and-incident-triggered-playbooks"></a>Automatiseringsregels en door incidenten geactiveerde playbooks

Automatiseringsregels zijn een nieuw concept in Azure Sentinel, zodat u de automatisering van incidentafhandeling centraal kunt beheren. Naast het toewijzen van playbooks aan incidenten (niet alleen aan waarschuwingen zoals voorheen), kunt u met automatiseringsregels ook reacties voor meerdere analyseregels tegelijk automatiseren, automatisch incidenten taggen, toewijzen of sluiten zonder dat playbooks nodig zijn, en de volgorde bepalen van acties die worden uitgevoerd. Automatiseringsregels stroomlijnen het automatiseringsgebruik in Azure Sentinel en stellen u in staat om complexe werkstromen voor uw incident orchestration-processen te vereenvoudigen.

Meer informatie met deze [volledige uitleg van automatiseringsregels](automate-incident-handling-with-automation-rules.md).

Zoals hierboven vermeld, kunnen playbooks nu worden geactiveerd met de incidenttrigger naast de waarschuwingstrigger. De incidenttrigger biedt uw playbooks een grotere set invoer om mee te werken (omdat het incident ook alle waarschuwings- en entiteitsgegevens bevat), waardoor u nog meer kracht en flexibiliteit hebt in uw reactiewerkstromen. Door incidenten geactiveerde playbooks worden geactiveerd door aangeroepen te worden vanuit automatiseringsregels.

Meer informatie over de verbeterde mogelijkheden van [](tutorial-respond-threats-playbook.md) [playbooks](automate-responses-with-playbooks.md)en het maken van een antwoordwerkstroom met behulp van playbooks in samenwerking met automatiseringsregels.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Nieuwe verrijkingen van waarschuwingen: verbeterde entiteitstoewijzing en aangepaste details

Verrijk uw waarschuwingen op twee nieuwe manieren om ze bruikbaarder en meer informatief te maken.

Begin door uw entiteitstoewijzing naar het volgende niveau te brengen. U kunt nu bijna 20 soorten entiteiten, van gebruikers, hosts en IP-adressen, aan bestanden en processen, aan postvakken, Azure-resources en IoT-apparaten. U kunt ook meerdere id's voor elke entiteit gebruiken om hun unieke identificatie te versterken. Dit biedt u een veel uitgebreidere gegevensset in uw incidenten, wat zorgt voor een bredere correlatie en krachtiger onderzoek. [Meer informatie over de nieuwe manier om entiteiten](map-data-fields-to-entities.md) in uw waarschuwingen toe te wijst.

[Lees meer over entiteiten en](entities-in-azure-sentinel.md) bekijk de volledige lijst met beschikbare entiteiten en hun [id's.](entities-reference.md)

Geef uw onderzoeks- en reactiemogelijkheden een nog grotere boost door uw waarschuwingen aan te passen om details van uw onbewerkte gebeurtenissen boven water te brengen. Breng gebeurtenisinhoud inzicht in uw incidenten, waardoor u steeds meer kracht en flexibiliteit hebt bij het reageren op en onderzoeken van beveiligingsrisico's. [Meer informatie over het weergeven van aangepaste details](surface-custom-details-in-alerts.md) in uw waarschuwingen.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Uw werkmappen Azure Sentinel of opslaan als PDF

U kunt nu Azure Sentinel werkmappen afdrukken, zodat u ze ook kunt exporteren naar PDF's en lokaal kunt opslaan of delen.

Selecteer in uw werkmap het menu Opties > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **Inhoud afdrukken.** Selecteer vervolgens uw printer of selecteer **opslaan als PDF naar** behoefte.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Druk uw werkmap af of sla deze op als PDF.":::

Zie Zelfstudie: Uw gegevens [visualiseren en bewaken voor meer informatie.](tutorial-monitor-your-data.md)

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Incidentfilters en sorteervoorkeuren zijn nu opgeslagen in uw sessie (openbare preview)

Uw incidentfilters en -sortering worden nu opgeslagen tijdens Azure Sentinel sessie, zelfs tijdens het navigeren naar andere gebieden van het product.
Als u zich nog steeds in dezelfde sessie hebt, kunt u in het gebied Incidenten in Azure Sentinel filters en sorteren op dezelfde manier als u deze hebt gelaten. [](tutorial-investigate-cases.md)

> [!NOTE]
> Incidentfilters en -sortering worden niet opgeslagen nadat u Azure Sentinel browser hebt verlaten of vernieuwd.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Microsoft 365 Defender-incidentintegratie (openbare preview)

Azure Sentinel de integratie [van Microsoft 365 Defender-incidenten (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) kunt u alle M365D-incidenten streamen naar Azure Sentinel en ze gesynchroniseerd houden tussen beide portals. Incidenten van M365D (voorheen bekend als Microsoft Threat Protection of MTP) omvatten alle bijbehorende waarschuwingen, entiteiten en relevante informatie, zodat u voldoende context hebt om een triage en voorlopig onderzoek uit te voeren in Azure Sentinel. Eenmaal in Sentinel blijven incidenten in twee richtingen gesynchroniseerd met M365D, zodat u kunt profiteren van de voordelen van beide portals in uw incidentonderzoek.

Door zowel Azure Sentinel als Microsoft 365 Defender samen te gebruiken, krijgt u het beste van beide werelden. U krijgt een breed inzicht dat een SIEM u biedt in het volledige bereik van informatiebronnen van uw organisatie, en ook de uitgebreide onderzoekskracht die een XDR biedt om uw Microsoft 365-resources te beveiligen, beide gecoördineerd en gesynchroniseerd voor naadloze SOC-werking.

Zie Defender-integratie Microsoft 365 met Azure Sentinel voor [meer Azure Sentinel.](microsoft-365-defender-sentinel-integration.md)

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nieuwe Microsoft-serviceconnectoren met Azure Policy

[Azure Policy](../governance/policy/overview.md) is een Azure-service waarmee u beleidsregels kunt gebruiken om de eigenschappen van een resource af te dwingen en te controleren. Het gebruik van beleid zorgt ervoor dat resources voldoen aan uw IT-governancestandaarden.

Een van de eigenschappen van resources die kunnen worden beheerd door beleidsregels zijn het maken en verwerken van diagnostische gegevens en controlelogboeken. Azure Sentinel maakt nu gebruik van Azure Policy zodat u een algemene set instellingen voor diagnostische logboeken kunt toepassen op alle (huidige en toekomstige) resources van een bepaald type waarvan u de logboeken wilt opnemen in Azure Sentinel. Dankzij Azure Policy hoeft u de instellingen voor diagnostische logboeken niet langer resource per resource in te stellen.

Azure Policy zijn nu beschikbaar voor de volgende Azure-services:
- [Azure Key Vault](connect-azure-key-vault.md) (openbare preview)
- [Azure Kubernetes Service](connect-azure-kubernetes-service.md) (openbare preview)
- Azure SQL databases/servers (GA)

Klanten kunnen de logboeken nog steeds handmatig verzenden voor specifieke exemplaren en hoeven de beleidsen engine niet te gebruiken.

## <a name="february-2021"></a>Februari 2021

- [CMMC-werkmap (Cybersecurity Maturity Model Certification)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Gegevensconnectoren van derden](#third-party-data-connectors)
- [UEBA-inzichten op de entiteitspagina (openbare preview)](#ueba-insights-in-the-entity-page-public-preview)
- [Verbeterde zoekopdrachten naar incidenten (openbare preview)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>CMMC-werkmap (Cybersecurity Maturity Model Certification)

De Azure Sentinel CMMC-werkmap biedt een mechanisme voor het weergeven van logboekquery's die zijn afgestemd op CMMC-besturingselementen in het Microsoft-portfolio, waaronder Microsoft-beveiligingsaanbiedingen, Office 365, Teams, Intune, Windows Virtual Desktop en nog veel meer.

Met de CMMC-werkmap kunnen beveiligingsarchitecten, technici, beveiligingsanalisten, managers en IT-professionals situationeel inzicht krijgen in de beveiligingsstatus van cloudworkloads. Er zijn ook aanbevelingen voor het selecteren, ontwerpen, implementeren en configureren van Microsoft-aanbiedingen voor afstemming met de respectieve CMMC-vereisten en -procedures.

Zelfs als u niet aan CMMC hoeft te voldoen, is de CMMC-werkmap handig bij het bouwen van Security Operations Centers, het ontwikkelen van waarschuwingen, het visualiseren van bedreigingen en het bieden van situationele kennis van workloads.

Toegang tot de CMMC-werkmap in het Azure Sentinel **Workbooks.** Selecteer **Sjabloon** en zoek vervolgens naar **CMMC.**

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="De CMMC-werkmap in- en uitschakelen" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Zie voor meer informatie:

- [Azure Sentinel workbook voor certificering van het cyberbeveiligingsmodel (CMMC)](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Zelfstudie: Uw gegevens visualiseren en bewaken](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Gegevensconnectoren van derden

Onze verzameling integraties van derden blijft groeien, met dertig connectors die in de afgelopen twee maanden zijn toegevoegd. Hier is een lijst:

- [Agari Phishing Defense and Brand Protection](connect-agari-phishing-defense.md)
- [Akamai-beveiligingsgebeurtenissen](connect-akamai-security-events.md)
- [Alsid voor Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP Server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [Blackberry CylancePROTECT](connect-data-sources.md)
- [Broadcom Symantec DLP](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified Computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enterprise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google Workspace (voorheen G Suite)](connect-google-workspace.md)
- [Imperva WAF-gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS-logboeken](connect-nxlog-dns.md)
- [NXLog Linux Audit](connect-nxlog-linuxaudit.md)
- [Onapsis Platform](connect-data-sources.md)
- [Proofpoint On Demand Email Security (POD)](connect-proofpoint-pod.md)
- [Qualys Vulnerability Management Knowledge Base](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall Firewall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>UEBA-inzichten op de entiteitspagina (openbare preview)

De Azure Sentinel entiteitsdetails bieden een deelvenster [Inzichten,](identify-threats-with-entity-behavior-analytics.md#entity-insights)waarin gedragsinzichten worden weergegeven in de entiteit en waarmee u snel afwijkingen en beveiligingsrisico's kunt identificeren.

Als u [UEBA](ueba-enrichments.md)hebt ingeschakeld en een tijdsbestek van ten minste vier dagen hebt geselecteerd, bevat dit deelvenster Inzichten nu ook de volgende nieuwe secties voor UEBA-inzichten:

|Sectie  |Beschrijving  |
|---------|---------|
|**UEBA Insights**     | Geeft een overzicht van afwijkende gebruikersactiviteiten: <br>- Over geografische locaties, apparaten en omgevingen<br>- Tijds- en frequentieperioden, vergeleken met de eigen geschiedenis van de gebruiker <br>- Vergeleken met het gedrag van collega's <br>- Vergeleken met het gedrag van de organisatie     |
|**Gebruikers-peers op basis van lidmaatschap van beveiligingsgroep**     |   Geeft de peers van de gebruiker weer op basis van het lidmaatschap van Azure AD-beveiligingsgroepen, zodat beveiligingsteams een lijst krijgen met andere gebruikers die vergelijkbare machtigingen delen.  |
|**Gebruikerstoegangmachtigingen voor Azure-abonnement**     |     Toont de toegangsmachtigingen van de gebruiker voor de Azure-abonnementen die rechtstreeks toegankelijk zijn, of via Azure AD-groepen/service-principals.   |
|**Bedreigingsindicatoren met betrekking tot de gebruiker**     |  Geeft een verzameling bekende bedreigingen weer met betrekking tot IP-adressen die worden weergegeven in de activiteiten van de gebruiker. Bedreigingen worden vermeld op bedreigingstype en -familie en worden verrijkt met de service voor bedreigingsinformatie van Microsoft.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Verbeterd zoeken naar incidenten (openbare preview)

We hebben de ervaring voor Azure Sentinel incident zoeken verbeterd, zodat u sneller door incidenten kunt navigeren terwijl u een specifieke bedreiging onderzoekt.

Wanneer u zoekt naar incidenten in Azure Sentinel, kunt u nu zoeken op de volgende incidentdetails:

- Id
- Titel
- Product
- Eigenaar
- Tag

## <a name="january-2021"></a>Januari 2021

- [Wizard Analyseregels: Verbeterde ervaring voor het bewerken van query's (openbare preview)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [PowerShell-module Az.SecurityInsights (openbare preview)](#azsecurityinsights-powershell-module-public-preview)
- [SQL database-connector](#sql-database-connector)
- [Dynamics 365-connector (openbare preview)](#dynamics-365-connector-public-preview)
- [Verbeterde incident opmerkingen](#improved-incident-comments)
- [Toegewezen Log Analytics-clusters](#dedicated-log-analytics-clusters)
- [Beheerde identiteiten van Logische apps](#logic-apps-managed-identities)
- [Verbeterde afstemming van regels met voorbeeldgrafieken voor analyseregel](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Wizard Analyseregels: Verbeterde ervaring voor het bewerken van query's (openbare preview)

De Azure Sentinel wizard Geplande analyseregel biedt nu de volgende verbeteringen voor het schrijven en bewerken van query's:

-   Een uitbreidbaar bewerkingsvenster, zodat u meer schermruimte hebt om uw query weer te geven.
-   Belangrijke woord markeren in uw querycode.
-   Uitgebreide ondersteuning voor automatisch aanvullen.
-   Queryvalidaties in realtime. Fouten in uw query worden nu weergegeven als een rood blok in de schuifbalk en als een rode stip op de **naam** van het tabblad Regel instellen. Bovendien kan een query met fouten niet worden opgeslagen.

Zie Zelfstudie: Aangepaste analyseregels maken om bedreigingen [te detecteren voor meer informatie.](tutorial-detect-threats-custom.md)
### <a name="azsecurityinsights-powershell-module-public-preview"></a>PowerShell-module Az.SecurityInsights (openbare preview)

Azure Sentinel ondersteunt nu de nieuwe PowerShell-module [Az.SecurityInsights.](https://www.powershellgallery.com/packages/Az.SecurityInsights/)

De **Az.SecurityInsights-module** ondersteunt veelvoorkomende Azure Sentinel-use-cases, zoals interactie met incidenten om de ernst, de ernst, de eigenaar, en meer te wijzigen, opmerkingen en labels toe te voegen aan incidenten en bladwijzers te maken.

Hoewel het raadzaam is [Azure Resource Manager-sjablonen (ARM)](../azure-resource-manager/templates/index.yml) te gebruiken voor uw CI/CD-pijplijn, is de **Module Az.SecurityInsights** handig voor taken na de implementatie en is gericht op SOC-automatisering.  Uw SOC-automatisering kan bijvoorbeeld stappen bevatten voor het configureren van gegevensconnectoren, het maken van analyseregels of het toevoegen van automatiseringsacties aan analyseregels.

Zie de PowerShell-documentatie voor [Az.SecurityInsights](/powershell/module/az.securityinsights/)voor meer informatie, waaronder een volledige lijst en beschrijving van de beschikbare cmdlets, parameterbeschrijvingen en voorbeelden.

### <a name="sql-database-connector"></a>SQL database-connector

Azure Sentinel biedt nu een Azure SQL-databaseconnector, waarmee u de auditlogboeken en diagnostische logboeken van uw databases kunt streamen naar Azure Sentinel en de activiteit in al uw instanties continu kunt bewaken.

Azure SQL is een volledig beheerde PaaS-database-engine (Platform-as-a-Service) die de meeste databasebeheerfuncties verwerkt, zoals upgraden, patchen, back-ups en bewaking, zonder tussenkomst van de gebruiker.

Zie Connect [Azure SQL database diagnostics and auditing logs (Diagnostische](connect-azure-sql-logs.md)gegevens en auditlogboeken van de database verbinden) voor meer informatie.

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365-connector (openbare preview)

Azure Sentinel biedt nu een connector voor Microsoft Dynamics 365, waarmee u de gebruikers-, beheerders- en ondersteuningslogboeken van uw Dynamics 365-toepassingen kunt verzamelen in Azure Sentinel. U kunt deze gegevens gebruiken om u te helpen bij het controleren van alle gegevensverwerkingsacties die worden uitgevoerd en om deze te analyseren op mogelijke beveiligingsschending.

Zie Connect [Dynamics 365 activity logs to Azure Sentinel (Dynamics 365-activiteitenlogboeken verbinden](connect-dynamics-365.md)met Azure Sentinel).

### <a name="improved-incident-comments"></a>Verbeterde incident opmerkingen

Analisten gebruiken incidentin opmerkingen om samen te werken aan incidenten, processen en stappen handmatig of als onderdeel van een playbook te documenteren. 

Dankzij onze verbeterde ervaring voor het plaatsen van opmerkingen bij incidenten kunt u uw opmerkingen opmaken en bestaande opmerkingen bewerken of verwijderen.

Zie Automatisch incidenten maken van [Microsoft-beveiligingswaarschuwingen voor meer informatie.](create-incidents-from-alerts.md)
### <a name="dedicated-log-analytics-clusters"></a>Toegewezen Log Analytics-clusters

Azure Sentinel ondersteunt nu toegewezen Log Analytics-clusters als implementatieoptie. We raden u aan om een toegewezen cluster te overwegen als u:

- **Meer dan 1 Tb per dag opnemen** in uw Azure Sentinel werkruimte
- **Meerdere werkruimten Azure Sentinel in** uw Azure-inschrijving

Met toegewezen clusters kunt u functies gebruiken zoals door de klant beheerde sleutels, lockbox, dubbele versleuteling en snellere query's voor meerdere werkruimten wanneer u meerdere werkruimten in hetzelfde cluster hebt.

Zie toegewezen clusters Azure Monitor [logboeken voor meer informatie.](../azure-monitor/logs/logs-dedicated-clusters.md)

### <a name="logic-apps-managed-identities"></a>Beheerde identiteiten van Logische apps

Azure Sentinel ondersteunt nu beheerde identiteiten voor de Azure Sentinel Logic Apps-connector, zodat u rechtstreeks machtigingen kunt verlenen aan een specifieke playbook om te werken met Azure Sentinel in plaats van extra identiteiten te maken.

- **Zonder een beheerde identiteit** vereist de Logic Apps-connector een afzonderlijke identiteit met een Azure Sentinel RBAC-rol om te kunnen worden uitgevoerd op Azure Sentinel. De afzonderlijke identiteit kan een Azure AD-gebruiker of een service-principal zijn, zoals een bij Azure AD geregistreerde toepassing.

- **Als u ondersteuning voor beheerde identiteiten in uw logische app in** te zetten, wordt de logische app geregistreerd bij Azure AD en wordt een object-id verstrekt. Gebruik de object-id in Azure Sentinel de logische app toe te wijzen met een Azure RBAC-rol in Azure Sentinel werkruimte. 

Zie voor meer informatie:

- [Verifiëren met beheerde identiteit in Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
- [documentatie Azure Sentinel Logic Apps connector](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Verbeterde afstemming van regels met de voorbeeldgrafieken voor analyseregel (openbare preview)

Azure Sentinel helpt u nu uw analyseregels beter af te stemmen, zodat u de nauwkeurigheid kunt vergroten en ruis kunt verminderen.

Nadat u een analyseregel hebt bewerkt op **het tabblad Regellogica** instellen, gaat u naar het gebied **Resultatensimulatie** aan de rechterkant. 

Selecteer **Testen met huidige gegevens om** een simulatie Azure Sentinel van de laatste 50 runs van uw analyseregel uit te voeren. Er wordt een grafiek gegenereerd om het gemiddelde aantal waarschuwingen weer te geven dat de regel zou hebben gegenereerd, op basis van de geëvalueerde onbewerkte gebeurtenisgegevens. 

Zie De logica van de regelquery [definiëren en instellingen configureren voor meer informatie.](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings)

## <a name="december-2020"></a>December 2020

- [80 nieuwe ingebouwde zoekquery's](#80-new-built-in-hunting-queries)
- [Verbeteringen aan de Log Analytics-agent](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nieuwe ingebouwde zoekquery's
 
Azure Sentinel ingebouwde opsporingsquery's van Azure Sentinel SOC-analisten in staat stellen om hiaten in de huidige detectiedekking te verminderen en nieuwe opsporings leads aan te vallen.

Deze update voor Azure Sentinel bevat nieuwe zoekquery's die dekking bieden in de MITRE ATT-&CK-frameworkmatrix:

- **Verzameling**
- **Opdracht en beheer**
- **Toegang tot referenties**
- **Discovery** (Detectie)
- **Uitvoering**
- **Exfiltration** (Exfiltratie)
- **Impact**
- **Initiële toegang**
- **Persistentie**
- **Escalatie van bevoegdheden**

De toegevoegde zoekquery's zijn ontworpen om u te helpen verdachte activiteiten in uw omgeving te vinden. Hoewel ze legitieme activiteiten en mogelijk schadelijke activiteiten kunnen retourneren, kunnen ze nuttig zijn bij het begeleiden van uw hunting. 

Als u na het uitvoeren van deze query's zeker bent van de resultaten, kunt u ze converteren naar analyseregels of de resultaten voor de hunting toevoegen aan bestaande of nieuwe incidenten.

Alle toegevoegde query's zijn beschikbaar via de pagina Azure Sentinel Hunting. Zie Voor meer informatie [Zoeken naar bedreigingen met Azure Sentinel.](hunting.md)

### <a name="log-analytics-agent-improvements"></a>Verbeteringen aan de Log Analytics-agent

Azure Sentinel gebruikers profiteren van de volgende log analytics-agentverbeteringen:

- **Ondersteuning voor meer besturingssystemen,** waaronder CentOS 8, RedHat 8 en SUSE Linux 15.
- **Ondersteuning voor Python 3** naast Python 2

Azure Sentinel gebruikt de Log Analytics-agent om gebeurtenissen naar uw werkruimte te sturen, waaronder Windows-beveiliging-gebeurtenissen, Syslog-gebeurtenissen, CEF-logboeken en meer.

> [!NOTE]
> De Log Analytics-agent wordt soms aangeduid als de OMS-agent of de Microsoft Monitoring Agent (MMA). 
> 

Zie de Log [Analytics-documentatie en](../azure-monitor/agents/log-analytics-agent.md) de releasenotities van de [Log Analytics-agent voor meer informatie.](https://github.com/microsoft/OMS-Agent-for-Linux/releases)
## <a name="november-2020"></a>November 2020

- [De status van uw Playbooks in Azure Sentinel](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender-connector (openbare preview)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>De status van uw Playbooks in Azure Sentinel

Azure Sentinel playbooks zijn gebaseerd op werkstromen die zijn gebouwd in [Azure Log Apps,](../logic-apps/index.yml)een cloudservice waarmee u taken, bedrijfsprocessen en werkstromen kunt plannen, automatiseren en ins orchestraeren. Playbooks kunnen automatisch worden aangeroepen wanneer een incident wordt gemaakt of wanneer er wordt geseed en met incidenten wordt gewerkt. 

Om inzicht te krijgen in de status, prestaties en het gebruik [](../azure-monitor/visualize/workbooks-overview.md) van uw playbooks, hebben we een werkmap met de naam **Playbooks-statusbewaking toegevoegd.** 

Gebruik de **werkmap Voor statuscontrole** van Playbooks om de status van uw playbooks te controleren of om te zoeken naar afwijkingen in het aantal geslaagd of mislukte runs. 

De **werkmap Playbooks-statuscontrole** is nu beschikbaar in de Azure Sentinel Sjablonen:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Voorbeeld van werkmap voor statuscontrole van Playbooks":::

Zie voor meer informatie:

- [Logic Apps documentatie](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Azure Monitor-documentatie](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender-connector (openbare preview)
 
Met Microsoft 365 Defender-connector voor Azure Sentinel kunt u geavanceerde hunting-logboeken (een type onbewerkte gebeurtenisgegevens) van Microsoft 365 Defender naar Azure Sentinel. 

Met de integratie van [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) in de beveiligingsparaplu van [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) kunt u nu geavanceerde zoekgebeurtenissen van Microsoft Defender for Endpoint verzamelen met behulp van de Microsoft 365 Defender-connector en deze rechtstreeks streamen naar nieuwe, speciaal gebouwde tabellen in uw Azure Sentinel-werkruimte. 

De Azure Sentinel-tabellen zijn gebaseerd op hetzelfde schema dat wordt gebruikt in de Microsoft 365 Defender-portal en bieden u volledige toegang tot de volledige set geavanceerde logboeken voor de hunting. 

Zie Connect [data from Microsoft 365 Defender to Azure Sentinel (Gegevens](connect-microsoft-365-defender.md)van Microsoft 365 Defender verbinden met Azure Sentinel).

> [!NOTE]
> Microsoft 365 Defender werd voorheen bekend als Microsoft Threat Protection of MTP. Microsoft Defender for Endpoint werd voorheen bekend als Microsoft Defender Advanced Threat Protection of MDATP.
> 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[On-board Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Inzicht krijgen in waarschuwingen](quickstart-get-visibility.md)
