---
title: Wat is er nieuw in azure Sentinel
description: In dit artikel worden nieuwe functies in azure Sentinel van de afgelopen maanden beschreven.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 31ba96e0f8772877d7b4881c6bab0561cbe7956e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604250"
---
# <a name="whats-new-in-azure-sentinel"></a>Wat is er nieuw in azure Sentinel

Dit artikel bevat een lijst met recente functies die zijn toegevoegd voor Azure Sentinel, en nieuwe functies in gerelateerde services die een verbeterde gebruikers ervaring bieden in azure Sentinel.

Zie onze [community-blogs voor technische](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)informatie over eerdere functies die worden geleverd.

Genoteerde functies zijn momenteel beschikbaar als PREVIEW-versie. De [Aanvullende voorwaarden voor Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.


> [!TIP]
> Onze Threat jacht-teams over micro soft bijdragen query's, playbooks, werkmappen en notebooks naar de [Azure Sentinel Community](https://github.com/Azure/Azure-Sentinel), met inbegrip van specifieke [jacht-query's](https://github.com/Azure/Azure-Sentinel) die uw teams kunnen aanpassen en gebruiken. 
>
> U kunt ook bijdragen leveren. Doe mee aan de [Azure-omgeving voor Sentinel-dreigingen github-Community](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="march-2021"></a>2021 maart

- [Automatiserings regels en door incidenten geactiveerde playbooks](#automation-rules-and-incident-triggered-playbooks) (inclusief alle-nieuwe Playbook-documentatie)
- [Nieuwe waarschuwings verrijkingen: uitgebreide entiteits toewijzing en aangepaste Details](#new-alert-enrichments-enhanced-entity-mapping-and-custom-details)
- [Uw Azure Sentinel-werkmappen afdrukken of opslaan als PDF-bestand](#print-your-azure-sentinel-workbooks-or-save-as-pdf)
- [Incident filters en sorteer voorkeuren die nu zijn opgeslagen in uw sessie (open bare preview)](#incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview)
- [Microsoft 365 Defender-incident integratie (open bare preview)](#microsoft-365-defender-incident-integration-public-preview)
- [Nieuwe micro soft-service connectors met behulp van Azure Policy](#new-microsoft-service-connectors-using-azure-policy)
 
### <a name="automation-rules-and-incident-triggered-playbooks"></a>Automatiserings regels en door incidenten geactiveerde playbooks

Automatiserings regels zijn een nieuw concept in azure Sentinel, waarmee u de automatisering van incident verwerking centraal kunt beheren. Naast de mogelijkheid om playbooks toe te wijzen aan incidenten (niet alleen op waarschuwingen zoals voorheen), kunt u met automatiserings regels ook de reacties voor meerdere analyse regels tegelijk automatiseren, automatisch tags toewijzen, toekennen of sluiten van incidenten zonder playbooks, en de volg orde bepalen van de acties die worden uitgevoerd. Met Automation-regels worden automatiserings gebruik in azure Sentinel gestroomlijnd en kunt u complexe werk stromen vereenvoudigen voor de indelings processen van uw incident.

Meer informatie vindt u in deze [volledige uitleg over Automation-regels](automate-incident-handling-with-automation-rules.md).

Zoals hierboven vermeld, kan playbooks nu worden geactiveerd met de trigger voor incidenten naast de waarschuwing. De incident trigger biedt uw playbooks een grotere set ingangen waarmee u kunt werken (aangezien het incident ook alle waarschuwingen en entiteits gegevens bevat), waardoor u nog meer kracht en flexibiliteit hebt in uw antwoord werk stromen. Door incidenten geactiveerde playbooks worden geactiveerd door te worden aangeroepen vanuit Automation-regels.

Meer informatie over [playbooks ' Enhanced mogelijkheden](automate-responses-with-playbooks.md), en hoe u [een antwoord werk stroom](tutorial-respond-threats-playbook.md) maakt met behulp van playbooks in combi natie met automatiserings regels.

### <a name="new-alert-enrichments-enhanced-entity-mapping-and-custom-details"></a>Nieuwe waarschuwings verrijkingen: uitgebreide entiteits toewijzing en aangepaste Details

Verrijk uw waarschuwingen op twee nieuwe manieren om ze bruikbaarder en meer informatie te maken.

Neem eerst uw entiteits toewijzing naar het volgende niveau. U kunt nu bijna 20 soorten entiteiten, van gebruikers, hosts en IP-adressen, aan bestanden en processen toewijzen aan post vakken, Azure-resources en IoT-apparaten. U kunt ook meerdere id's voor elke entiteit gebruiken om hun unieke identificatie te versterken. Dit biedt u een veel rijkere gegevensset in uw incidenten, die voorziet in een bredere correlatie en krachtiger onderzoek. [Meer informatie over de nieuwe manier om entiteiten](map-data-fields-to-entities.md) in uw waarschuwingen toe te wijzen.

[Lees meer over entiteiten](entities-in-azure-sentinel.md) en Bekijk de [volledige lijst met beschik bare entiteiten en hun id's](entities-reference.md).

Geef uw onderzoeken en reacties een nog betere verbetering door uw waarschuwingen aan te passen aan de details van het Opper vlak van uw onbewerkte gebeurtenissen. Breng inzicht in de inhoud van gebeurtenissen in uw incidenten, waardoor u meer kracht en flexibiliteit hebt bij het reageren op beveiligings dreigingen. [Meer informatie over het oppervlak aanpassen van aangepaste Details](surface-custom-details-in-alerts.md) in uw waarschuwingen.



### <a name="print-your-azure-sentinel-workbooks-or-save-as-pdf"></a>Uw Azure Sentinel-werkmappen afdrukken of opslaan als PDF-bestand

U kunt nu Azure-Sentinel-werkmappen afdrukken, zodat u deze ook kunt exporteren naar Pdf's en lokaal kunt opslaan of delen.

Selecteer in uw werkmap het menu opties > :::image type="icon" source="media/whats-new/print-icon.png" border="false"::: **inhoud afdrukken**. Selecteer vervolgens uw printer of selecteer **Opslaan als PDF-bestand** als dat nodig is.

:::image type="content" source="media/whats-new/print-workbook.png" alt-text="Uw werkmap afdrukken of opslaan als PDF-bestand.":::

Zie [zelf studie: uw gegevens visualiseren en bewaken](tutorial-monitor-your-data.md)voor meer informatie.

### <a name="incident-filters-and-sort-preferences-now-saved-in-your-session-public-preview"></a>Incident filters en sorteer voorkeuren die nu zijn opgeslagen in uw sessie (open bare preview)

Uw incident filters en sorteren worden nu opgeslagen in de Azure-Sentinel-sessie, zelfs wanneer u naar andere gebieden van het product navigeert.
Als u zich nog steeds in dezelfde sessie bevindt, gaat u terug naar het gebied [incidenten](tutorial-investigate-cases.md) in azure Sentinel, worden uw filters en sorteren op dezelfde manier als u hebt achtergelaten.

> [!NOTE]
> Incident filters en-sorteringen worden niet opgeslagen na het verlaten van Azure-Sentinel of het vernieuwen van uw browser.

### <a name="microsoft-365-defender-incident-integration-public-preview"></a>Microsoft 365 Defender-incident integratie (open bare preview)

Met de integratie van Azure Sentinel [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) kunt u alle M365D-incidenten streamen naar Azure Sentinel en ze gesynchroniseerd houden tussen beide portals. Incidenten van M365D (voorheen bekend als micro soft Threat Protection of MTP) bevatten alle gerelateerde waarschuwingen, entiteiten en relevante informatie, zodat u over voldoende context beschikt voor het uitvoeren van sorteren en voorlopig onderzoek in azure Sentinel. Eenmaal in Sentinel blijven incidenten bidirectionele gesynchroniseerd met M365D, zodat u kunt profiteren van de voor delen van beide portals in uw incident onderzoek.

Als u zowel Azure Sentinel als Microsoft 365 Defender samen gebruikt, hebt u het beste van beide werelden. U krijgt inzicht in de mate van inzichten die een SIEM geeft aan het hele bereik van informatie bronnen van uw organisatie, en tevens de diepte van de aangepaste en aanpas bare voedings kracht die een XDR biedt om uw Microsoft 365-bronnen te beschermen, beide gecoördineerd en gesynchroniseerd voor naadloze SOC-trans actie.

Zie [Microsoft 365 Defender-integratie met Azure Sentinel](microsoft-365-defender-sentinel-integration.md)voor meer informatie.

### <a name="new-microsoft-service-connectors-using-azure-policy"></a>Nieuwe micro soft-service connectors met behulp van Azure Policy

[Azure Policy](../governance/policy/overview.md) is een Azure-service waarmee u beleid kunt gebruiken om de eigenschappen van een resource af te dwingen en te beheren. Het gebruik van beleid zorgt ervoor dat de resources blijven voldoen aan de standaarden van uw IT-governance.

Onder de eigenschappen van bronnen die door het beleid kunnen worden beheerd, worden diagnostische gegevens en controle logboeken gemaakt en verwerkt. Azure Sentinel gebruikt nu Azure Policy zodat u een gemeen schappelijke set diagnostische logboek instellingen kunt Toep assen op alle (huidige en toekomstige) resources van een bepaald type waarvan de logboeken die u wilt opnemen in azure Sentinel. Bedankt voor het Azure Policy u niet langer de instellingen resource voor Diagnostische logboeken per resource hoeft in te stellen.

Op Azure Policy gebaseerde connectors zijn nu beschikbaar voor de volgende Azure-Services:
- [Azure Key Vault](connect-azure-key-vault.md) (open bare preview)
- [Azure Kubernetes-service](connect-azure-kubernetes-service.md) (open bare preview)
- Azure SQL-data bases/-servers (GA)

Klanten kunnen de logboeken nog steeds hand matig verzenden voor specifieke exemplaren en hoeven de beleids engine niet te gebruiken.

## <a name="february-2021"></a>Februari 2021

- [CMMC-werkmap (Cyber beveiliging vervaldag model Certification)](#cybersecurity-maturity-model-certification-cmmc-workbook)
- [Gegevens connectors van derden](#third-party-data-connectors)
- [UEBA Insights op de entiteits pagina (open bare preview)](#ueba-insights-in-the-entity-page-public-preview)
- [Verbeterd zoeken naar incidenten (open bare preview)](#improved-incident-search-public-preview)

### <a name="cybersecurity-maturity-model-certification-cmmc-workbook"></a>CMMC-werkmap (Cyber beveiliging vervaldag model Certification)

De Azure Sentinel CMMC-werkmap biedt een mechanisme voor het weer geven van logboek query's die zijn afgestemd op CMMC-besturings elementen op de micro soft-Port Folio, waaronder micro soft-beveiligings aanbiedingen, Office 365, teams, intune, Windows virtueel bureau blad en nog veel meer.

Met de CMMC-werkmap kunnen beveiligings architecten, technici, beveiligings analisten, managers en IT-professionals op het gebied van de beveiliging postuur van Cloud werkbelastingen. Er zijn ook aanbevelingen voor het selecteren, ontwerpen, implementeren en configureren van micro soft-aanbiedingen voor uitlijning met de respectieve CMMC-vereisten en-procedures.

Zelfs als u niet verplicht bent om te voldoen aan CMMC, is de CMMC-werkmap handig bij het bouwen van beveiligings bewerkings centra, het ontwikkelen van waarschuwingen, het visualiseren van bedreigingen en het geven van belang bewuste werk belastingen.

Open de CMMC-werkmap in het gebied voor de Azure-Sentinel- **werkmappen** . Selecteer **sjabloon** en zoek naar **CMMC**.

:::image type="content" source="media/whats-new/cmmc-guide-toggle.gif" alt-text="Scha kelen tussen de CMMC-werkmap gids in-en uitschakelen" lightbox="media/whats-new/cmmc-guide-toggle.gif":::


Zie voor meer informatie:

- [Azure Sentinel Cyber beveiliging vervaldag model Certification (CMMC)-werkmap](https://techcommunity.microsoft.com/t5/public-sector-blog/azure-sentinel-cybersecurity-maturity-model-certification-cmmc/ba-p/2110524)
- [Zelfstudie: Uw gegevens visualiseren en bewaken](tutorial-monitor-your-data.md)


### <a name="third-party-data-connectors"></a>Gegevens connectors van derden

Onze verzameling integraties van derden blijft groeien, met dertig Connect oren die in de afgelopen twee maanden worden toegevoegd. Hier volgt een lijst:

- [Agari phishing-verdediging en merk beveiliging](connect-agari-phishing-defense.md)
- [Akamai-beveiligings gebeurtenissen](connect-akamai-security-events.md)
- [Alsid voor Active Directory](connect-alsid-active-directory.md)
- [Apache HTTP-server](connect-apache-http-server.md)
- [Aruba ClearPass](connect-aruba-clearpass.md)
- [BlackBerry CylancePROTECT](connect-data-sources.md)
- [DLP van Broadcom Symantec](connect-broadcom-symantec-dlp.md)
- [Cisco Firepower eStreamer](connect-data-sources.md)
- [Cisco Meraki](connect-cisco-meraki.md)
- [Cisco Umbrella](connect-cisco-umbrella.md)
- [Cisco Unified computing System (UCS)](connect-cisco-ucs.md)
- [ESET Enter prise Inspector](connect-data-sources.md)
- [ESET Security Management Center](connect-data-sources.md)
- [Google-werk ruimte (voorheen G suite)](connect-google-workspace.md)
- [Imperva WAF-gateway](connect-imperva-waf-gateway.md)
- [Juniper SRX](connect-juniper-srx.md)
- [Netskope](connect-data-sources.md)
- [NXLog DNS-logboeken](connect-nxlog-dns.md)
- [NXLog Linux-audit](connect-nxlog-linuxaudit.md)
- [Onapsis-platform](connect-data-sources.md)
- [Proofpoint on demand-e-mail beveiliging (POD)](connect-proofpoint-pod.md)
- [Knowledge Base voor beveiligings beheer van Qualys](connect-data-sources.md)
- [Salesforce Service Cloud](connect-salesforce-service-cloud.md)
- [SonicWall-firewall](connect-data-sources.md)
- [Sophos Cloud Optix](connect-sophos-cloud-optix.md)
- [Squid Proxy](connect-squid-proxy.md)
- [Symantec Endpoint Protection](connect-data-sources.md)
- [Thycotic Secret Server](connect-thycotic-secret-server.md)
- [Trend Micro XDR](connect-data-sources.md)
- [VMware ESXi](connect-vmware-esxi.md)

### <a name="ueba-insights-in-the-entity-page-public-preview"></a>UEBA Insights op de entiteits pagina (open bare preview)

De pagina's Details van de Azure-Sentinel-entiteit bieden een [inzichten venster](identify-threats-with-entity-behavior-analytics.md#entity-insights)waarin gedrags inzichten op de entiteit worden weer gegeven en snel afwijkingen en beveiligings Risico's kunnen worden geïdentificeerd.

Als u [UEBA hebt ingeschakeld](ueba-enrichments.md)en een periode van ten minste vier dagen hebt geselecteerd, bevat dit Insights-deel venster nu ook de volgende nieuwe secties voor UEBA Insights:

|Sectie  |Beschrijving  |
|---------|---------|
|**UEBA Insights**     | Een overzicht van afwijkende gebruikers activiteiten: <br>-Over geografische locaties, apparaten en omgevingen<br>-In periode en frequentie horizon taal, vergeleken met de eigen geschiedenis van de gebruiker <br>-Vergeleken met gedrag van peers <br>-Vergeleken met het gedrag van de organisatie     |
|**Gebruikers peers op basis van lidmaatschap van een beveiligings groep**     |   Geeft een lijst van de peers van de gebruiker op basis van het lidmaatschap van een Azure AD-beveiligings groep, waarmee beveiligings bewerkingen teams kunnen maken van andere gebruikers die vergelijk bare machtigingen delen.  |
|**Gebruikers toegangs machtigingen voor het Azure-abonnement**     |     Toont de toegangs machtigingen van de gebruiker voor de Azure-abonnementen die rechtstreeks toegankelijk zijn, of via Azure AD-groepen/service-principals.   |
|**Bedreigings indicatoren met betrekking tot de gebruiker**     |  Een lijst met bekende bedreigingen met betrekking tot IP-adressen die worden weer gegeven in de activiteiten van de gebruiker. Bedreigingen worden weer gegeven op het type bedreiging en familie, en worden verrijkt door de Threat Intelligence-Service van micro soft.       |
|     |         |

### <a name="improved-incident-search-public-preview"></a>Verbeterd zoeken naar incidenten (open bare preview)

We hebben de Azure-Zoek ervaring voor Sentinel-incidenten verbeterd, waardoor u sneller kunt navigeren door incidenten wanneer u een specifieke bedreiging onderzoekt.

Bij het zoeken naar incidenten in azure Sentinel kunt u nu zoeken op de volgende incident Details:

- Id
- Titel
- Product
- Eigenaar
- Tag

## <a name="january-2021"></a>Januari 2021

- [Wizard analyse regel: verbeterde mogelijkheden voor het bewerken van query's (open bare preview)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [AZ. SecurityInsights Power shell-module (open bare preview)](#azsecurityinsights-powershell-module-public-preview)
- [SQL database-connector](#sql-database-connector)
- [Dynamics 365-connector (open bare preview)](#dynamics-365-connector-public-preview)
- [Verbeterde reacties op incidenten](#improved-incident-comments)
- [Toegewezen Log Analytics clusters](#dedicated-log-analytics-clusters)
- [Beheerde identiteiten van Logic apps](#logic-apps-managed-identities)
- [Verbeterde regel afstemming met de voorbeeld grafieken van Analytics-regels](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Wizard analyse regel: verbeterde mogelijkheden voor het bewerken van query's (open bare preview)

De Azure Sentinel Scheduled Analytics regel wizard biedt nu de volgende verbeteringen voor het schrijven en bewerken van query's:

-   Een uitbreidbaar bewerkings venster dat u meer scherm ruimte biedt om uw query te bekijken.
-   Het markeren van belang rijke woorden in uw query code.
-   Uitgebreide ondersteuning voor automatisch aanvullen.
-   Validatie van query's in realtime. Fouten in uw query worden nu weer gegeven als een rood blok in de schuif balk en als een rode stip op de naam van het tabblad **regel logica instellen** . Daarnaast kan een query met fouten niet worden opgeslagen.

Zie [zelf studie: aangepaste analyse regels maken voor het detecteren van bedreigingen](tutorial-detect-threats-custom.md)voor meer informatie.
### <a name="azsecurityinsights-powershell-module-public-preview"></a>AZ. SecurityInsights Power shell-module (open bare preview)

Azure Sentinel ondersteunt nu de nieuwe [AZ. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) Power shell-module.

De **AZ. SecurityInsights** -module ondersteunt algemene Azure Sentinel use-cases, zoals interactie met incidenten voor het wijzigen van Statues, Ernst, eigenaar, enzovoort, het toevoegen van opmerkingen en labels aan incidenten en het maken van blad wijzers.

We raden u aan om [Azure Resource Manager-sjablonen (arm)](../azure-resource-manager/templates/index.yml) te gebruiken voor uw CI/cd-pijp lijn, de module **AZ. SecurityInsights** is handig voor taken na de implementatie en is bedoeld voor Soc Automation.  Uw SOC-automatisering kan bijvoorbeeld stappen bevatten voor het configureren van gegevens connectors, het maken van analyse regels of het toevoegen van automatiserings acties aan Analytics-regels.

Zie de [documentatie van AZ. SecurityInsights Power shell](/powershell/module/az.securityinsights/)(Engelstalig) voor meer informatie, inclusief een volledige lijst en een beschrijving van de beschik bare cmdlets, parameter beschrijvingen en voor beelden.

### <a name="sql-database-connector"></a>SQL database-connector

Azure Sentinel biedt nu een Azure SQL database-connector, waarmee u de controle-en Diagnostische logboeken van uw data bases kunt streamen naar Azure Sentinel en doorlopend monitor activiteiten in al uw exemplaren.

Azure SQL is een volledig beheerde PaaS-data base-engine (platform-as-a-Service) die de meeste database beheer functies, zoals upgrades, patches, back-ups en bewaking, afhandelt, zonder tussen komst van de gebruiker.

Zie voor meer informatie [verbinding maken tussen Azure SQL database-diagnose en controle logboeken](connect-azure-sql-logs.md).

### <a name="dynamics-365-connector-public-preview"></a>Dynamics 365-connector (open bare preview)

Azure Sentinel biedt nu een connector voor micro soft Dynamics 365, waarmee u uw Dynamics 365-toepassingen van de gebruiker, de beheerder en de activiteiten logboeken voor ondersteuning kunt verzamelen in azure Sentinel. U kunt deze gegevens gebruiken om u te helpen bij het controleren van de volledige gegevens verwerkings acties en om deze te analyseren op mogelijke beveiligings schendingen.

Zie [verbinding maken met Dynamics 365-activiteiten logboeken met Azure Sentinel](connect-dynamics-365.md)voor meer informatie.

### <a name="improved-incident-comments"></a>Verbeterde reacties op incidenten

Analisten gebruiken reacties op incidenten om samen te werken aan incidenten, processen en stappen hand matig of als onderdeel van een Playbook te documenteren. 

Dankzij de verbeterde ervaring voor het maken van reacties op incidenten kunt u uw opmerkingen opmaken en bestaande opmerkingen bewerken of verwijderen.

Zie voor meer informatie [automatisch incidenten maken op basis van beveiligings waarschuwingen van micro soft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Toegewezen Log Analytics clusters

Azure Sentinel ondersteunt nu toegewezen Log Analytics clusters als een implementatie optie. U kunt het beste een toegewezen cluster overwegen als u:

- **Opname van meer dan 1 TB per dag** in uw Azure Sentinel-werk ruimte
- **Meerdere Azure Sentinel-werk ruimten** in uw Azure-inschrijving hebben

Met toegewezen clusters kunt u functies gebruiken zoals door de klant beheerde sleutels, lockbox, dubbele versleuteling en snellere query's in meerdere werk ruimten wanneer u meerdere werk ruimten op hetzelfde cluster hebt.

Zie [Azure monitor specifieke clusters vastleggen in Logboeken](../azure-monitor/logs/logs-dedicated-clusters.md)voor meer informatie.

### <a name="logic-apps-managed-identities"></a>Beheerde identiteiten van Logic apps

Azure Sentinel ondersteunt nu beheerde identiteiten voor de Azure Sentinel Logic Apps-Connector, zodat u machtigingen rechtstreeks kunt toewijzen aan een specifieke Playbook om te kunnen omgaan met Azure Sentinel in plaats van extra identiteiten te maken.

- **Zonder een beheerde identiteit** vereist de Logic Apps Connector een afzonderlijke identiteit met een Azure Sentinel RBAC-rol om te kunnen worden uitgevoerd op Azure Sentinel. De afzonderlijke identiteit kan een Azure AD-gebruiker of een Service-Principal zijn, zoals een geregistreerde Azure AD-toepassing.

- Door **beheerde identiteits ondersteuning in uw logische app in te scha kelen** , wordt de logische app met Azure AD geregistreerd en wordt een object-id geboden. Gebruik de object-ID in de Azure-Sentinel om de logische app toe te wijzen aan een Azure RBAC-rol in uw Azure Sentinel-werk ruimte. 

Zie voor meer informatie:

- [Verifiëren met beheerde identiteit in Azure Logic Apps](../logic-apps/create-managed-service-identity.md)
- [Documentatie voor Azure Sentinel Logic Apps-Connector](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Verbeterde regel afstemming met de voorbeeld grafieken van Analytics-regels (open bare preview)

Azure Sentinel helpt u nu bij het afstemmen van uw analyse regels, waardoor u de nauw keurigheid kunt verhogen en de ruis moet verminderen.

Nadat u een analyse regel hebt bewerkt op het tabblad **regel logica instellen** , gaat u naar het **simulatie gebied resultaten** aan de rechter kant. 

Selecteer **testen met huidige gegevens** om Azure Sentinel een simulatie van de laatste 50 uitvoeringen van uw analyse regel uit te voeren. Er wordt een grafiek gegenereerd om het gemiddelde aantal waarschuwingen weer te geven dat de regel had gegenereerd, op basis van de geëvalueerde gebeurtenis gegevens. 

Zie [de regel query logica definiëren en instellingen configureren](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings)voor meer informatie.

## <a name="december-2020"></a>December 2020

- [80 nieuwe ingebouwde jacht-query's](#80-new-built-in-hunting-queries)
- [Verbeteringen in Log Analytics-agent](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 nieuwe ingebouwde jacht-query's
 
Met de ingebouwde jacht query's van Azure Sentinel kunnen SOC-analisten de huidige detectie dekking verminderen en Ignite nieuwe jacht-leads.

Deze update voor Azure Sentinel bevat nieuwe jacht-query's die dekking bieden in de MITRE ATT&verzonken Framework-matrix:

- **Verzameling**
- **Opdracht en controle**
- **Referenties toegang**
- **Discovery** (Detectie)
- **Uitvoering**
- **Exfiltration** (Exfiltratie)
- **Impact**
- **Initiële toegang**
- **Persistentie**
- **Escalatie van bevoegdheden**

De toegevoegde jacht-query's zijn ontworpen om u te helpen verdachte activiteiten in uw omgeving te vinden. Hoewel ze geldige activiteiten en mogelijk schadelijke activiteiten kunnen retour neren, kunnen ze nuttig zijn om uw jacht te verrijken. 

Als u na het uitvoeren van deze query's zeker weet wat de resultaten zijn, wilt u deze wellicht converteren naar Analytics-regels of kunt u de zoek resultaten toevoegen aan bestaande of nieuwe incidenten.

Alle toegevoegde query's zijn beschikbaar via de Azure Sentinel jacht-pagina. Zie voor meer informatie zoeken [naar bedreigingen met Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Verbeteringen in Log Analytics-agent

Azure Sentinel-gebruikers profiteren van de volgende Log Analytics-agent verbeteringen:

- **Ondersteuning voor meer besturings systemen**, waaronder CentOS 8, Redhat 8 en SUSE Linux 15.
- **Ondersteuning voor python 3** naast python 2

Azure Sentinel gebruikt de Log Analytics-agent voor het verzenden van gebeurtenissen naar uw werk ruimte, met inbegrip van Windows-beveiligings gebeurtenissen, syslog-gebeurtenissen, CEF-logboeken en meer.

> [!NOTE]
> De Log Analytics-agent wordt soms ook wel de OMS-agent of de micro soft Monitoring Agent (MMA) genoemd. 
> 

Zie de [documentatie van log Analytics](../azure-monitor/agents/log-analytics-agent.md) en de [release opmerkingen voor log Analytics agent](https://github.com/microsoft/OMS-Agent-for-Linux/releases)voor meer informatie.
## <a name="november-2020"></a>November 2020

- [De status van uw Playbooks in azure-Sentinel controleren](#monitor-your-playbooks-health-in-azure-sentinel)
- [Microsoft 365 Defender-connector (open bare preview)](#microsoft-365-defender-connector-public-preview)

### <a name="monitor-your-playbooks-health-in-azure-sentinel"></a>De status van uw Playbooks in azure-Sentinel controleren

Azure Sentinel playbooks zijn gebaseerd op werk stromen die zijn ingebouwd in [Azure-logboek-apps](../logic-apps/index.yml), een Cloud service die u helpt bij het plannen, automatiseren en organiseren van taken, bedrijfs processen en werk stromen. Playbooks kan automatisch worden aangeroepen wanneer een incident wordt gemaakt, of wanneer uitsorteren en werken met incidenten. 

Als u inzicht wilt krijgen in de status, prestaties en het gebruik van uw playbooks, hebt u een [werkmap](../azure-monitor/visualize/workbooks-overview.md) met de naam **playbooks Health Monitoring** toegevoegd. 

Gebruik de **Playbooks-status controle** werkmap om de status van uw Playbooks te controleren of om afwijkingen op te sporen in de hoeveelheid geslaagde of mislukte uitvoeringen. 

De **Playbooks Health Monitoring** -werkmap is nu beschikbaar in de Azure Sentinel-sjablonen galerie:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Voor beeld van een werkmap voor de Playbooks-status controle":::

Zie voor meer informatie:

- [Documentatie over Logic Apps](../logic-apps/monitor-logic-apps-log-analytics.md#set-up-azure-monitor-logs)

- [Azure Monitor-documentatie](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender-connector (open bare preview)
 
Met de Microsoft 365 Defender connector voor Azure Sentinel kunt u geavanceerde jacht-Logboeken (een type onbewerkte gebeurtenis gegevens) streamen van Microsoft 365 Defender naar Azure Sentinel. 

Met de integratie van [micro soft Defender voor eind punt (MDATP)](/windows/security/threat-protection/) in het beveiligings paraplu van [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) kunt u nu uw micro soft Defender voor geavanceerde jacht-gebeurtenissen verzamelen met behulp van de Microsoft 365 Defender connector, en deze direct in een nieuwe, door het doel gemaakte tabellen in uw Azure Sentinel-werk ruimte. 

De Azure-Sentinel-tabellen zijn gebaseerd op hetzelfde schema dat wordt gebruikt in de Microsoft 365 Defender-Portal en bieden u volledige toegang tot de volledige set geavanceerde jacht-Logboeken. 

Zie [verbinding maken tussen gegevens van Microsoft 365 Defender en Azure Sentinel](connect-microsoft-365-defender.md)voor meer informatie.

> [!NOTE]
> Microsoft 365 Defender was voorheen bekend als micro soft Threat Protection of MTP. Micro soft Defender voor eind punt was voorheen bekend als micro soft Defender Advanced Threat Protection of MDATP.
> 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
>[Azure-Sentinel op de trein](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Inzicht krijgen in waarschuwingen](quickstart-get-visibility.md)
