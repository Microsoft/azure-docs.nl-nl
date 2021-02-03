---
title: Wat is er nieuw in azure Sentinel
description: In dit artikel worden nieuwe functies in azure Sentinel van de afgelopen maanden beschreven.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 8154e1adff8d8c2bdfe7fedc9309f95e5c5880bd
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500719"
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

## <a name="january-2021"></a>Januari 2021

- [AZ. SecurityInsights Power shell-module (open bare preview)](#azsecurityinsights-powershell-module-public-preview)
- [SQL database-connector](#sql-database-connector)
- [Verbeterde reacties op incidenten](#improved-incident-comments)
- [Toegewezen Log Analytics clusters](#dedicated-log-analytics-clusters)
- [Beheerde identiteiten van Logic apps](#logic-apps-managed-identities)
- [Verbeterde regel afstemming met de voorbeeld grafieken van Analytics-regels](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="azsecurityinsights-powershell-module-public-preview"></a>AZ. SecurityInsights Power shell-module (open bare preview)

Azure Sentinel ondersteunt nu de nieuwe [AZ. SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/) Power shell-module.

De **AZ. SecurityInsights** -module ondersteunt algemene Azure Sentinel use-cases, zoals interactie met incidenten voor het wijzigen van Statues, Ernst, eigenaar, enzovoort, het toevoegen van opmerkingen en labels aan incidenten en het maken van blad wijzers.

We raden u aan om [Azure Resource Manager-sjablonen (arm)](/azure/azure-resource-manager/templates/) te gebruiken voor uw CI/cd-pijp lijn, de module **AZ. SecurityInsights** is handig voor taken na de implementatie en is specifiek gericht op Soc Automation.  Uw SOC-automatisering kan bijvoorbeeld stappen bevatten voor het configureren van gegevens connectors, het maken van analyse regels of het toevoegen van automatiserings acties aan Analytics-regels.

Zie de [documentatie van AZ. SecurityInsights Power shell](/powershell/module/az.securityinsights/)(Engelstalig) voor meer informatie, inclusief een volledige lijst en een beschrijving van de beschik bare cmdlets, parameter beschrijvingen en voor beelden.

### <a name="sql-database-connector"></a>SQL database-connector

Azure Sentinel biedt nu een Azure SQL database-connector, waarmee u de controle-en Diagnostische logboeken van uw data bases kunt streamen naar Azure Sentinel en doorlopend monitor activiteiten in al uw exemplaren.

Azure SQL is een volledig beheerde PaaS-data base-engine (platform-as-a-Service) die de meeste database beheer functies, zoals upgrades, patches, back-ups en bewaking, afhandelt, zonder tussen komst van de gebruiker.

Zie voor meer informatie [verbinding maken tussen Azure SQL database-diagnose en controle logboeken](connect-azure-sql-logs.md).

### <a name="improved-incident-comments"></a>Verbeterde reacties op incidenten

Analisten gebruiken reacties op incidenten om samen te werken aan incidenten, processen en stappen hand matig of als onderdeel van een Playbook te documenteren. 

Dankzij de verbeterde ervaring voor het maken van reacties op incidenten kunt u uw opmerkingen opmaken en bestaande opmerkingen bewerken of verwijderen.

Zie voor meer informatie [automatisch incidenten maken op basis van beveiligings waarschuwingen van micro soft](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Toegewezen Log Analytics clusters

Azure Sentinel ondersteunt nu toegewezen Log Analytics clusters als een implementatie optie. U kunt het beste een toegewezen cluster overwegen als u:

- **Opname van meer dan 1 TB per dag** in uw Azure Sentinel-werk ruimte
- **Meerdere Azure Sentinel-werk ruimten** in uw Azure-inschrijving hebben

Met toegewezen clusters kunt u functies gebruiken zoals door de klant beheerde sleutels, lockbox, dubbele versleuteling en snellere query's in meerdere werk ruimten wanneer u meerdere werk ruimten op hetzelfde cluster hebt.

Zie [Azure monitor specifieke clusters vastleggen in Logboeken](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters)voor meer informatie.

### <a name="logic-apps-managed-identities"></a>Beheerde identiteiten van Logic apps

Azure Sentinel ondersteunt nu beheerde identiteiten voor de Azure Sentinel Logic Apps-Connector, zodat u machtigingen kunt verlenen aan een rechtstreeks aan een specifieke Playbook, in plaats van extra identiteiten te maken.

- **Zonder een beheerde identiteit** vereist de Logic Apps Connector een afzonderlijke identiteit met een Azure Sentinel RBAC-rol om te kunnen worden uitgevoerd op Azure Sentinel. De afzonderlijke identiteit kan een Azure AD-gebruiker of een Service-Principal zijn, zoals een geregistreerde Azure AD-toepassing.

- Door **beheerde identiteits ondersteuning in uw logische app in te scha kelen** , wordt de logische app met Azure AD geregistreerd en wordt een object-id geboden. Gebruik de object-ID in de Azure-Sentinel om de logische app toe te wijzen aan een Azure RBAC-rol in uw Azure Sentinel-werk ruimte. 

Zie voor meer informatie:

- [Verifiëren met beheerde identiteit in Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
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

Als u na het uitvoeren van deze query's zeker weet dat de resultaten worden gevonden, kunt u deze converteren naar Analytics-regels of de zoek resultaten toevoegen aan bestaande of nieuwe incidenten.

Alle toegevoegde query's zijn beschikbaar via de Azure Sentinel jacht-pagina. Zie voor meer informatie zoeken [naar bedreigingen met Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Verbeteringen in Log Analytics-agent

Azure Sentinel-gebruikers profiteren van de volgende Log Analytics-agent verbeteringen:

- **Ondersteuning voor meer besturings systemen**, waaronder CentOS 8, Redhat 8 en SUSE Linux 15.
- **Ondersteuning voor python 3** naast python 2

Azure Sentinel gebruikt de Log Analytics-agent voor het verzenden van gebeurtenissen naar uw werk ruimte, met inbegrip van Windows-beveiligings gebeurtenissen, syslog-gebeurtenissen, CEF-logboeken en meer.

> [!NOTE]
> De Log Analytics-agent wordt soms ook wel de OMS-agent of de micro soft Monitoring Agent (MMA) genoemd. 
> 

Zie de [documentatie van log Analytics](/azure/azure-monitor/platform/log-analytics-agent) en de [release opmerkingen voor log Analytics agent](https://github.com/microsoft/OMS-Agent-for-Linux/releases)voor meer informatie.
## <a name="november-2020"></a>November 2020

- [Uw Logic Apps Playbooks in azure-Sentinel controleren](#monitor-your-logic-apps-playbooks-in-azure-sentinel)
- [Microsoft 365 Defender-connector (open bare preview)](#microsoft-365-defender-connector-public-preview)
### <a name="monitor-your-logic-apps-playbooks-in-azure-sentinel"></a>Uw Logic Apps Playbooks in azure-Sentinel controleren

Azure Sentinel kan nu worden geïntegreerd met [Azure-logboek-apps](/azure/logic-apps/), een Cloud service die u helpt bij het plannen, automatiseren en organiseren van taken, bedrijfs processen en werk stromen.

Gebruik een Azure Logic-app in azure Sentinel als een Playbook, die automatisch kan worden aangeroepen wanneer een incident wordt gemaakt, of wanneer uitsorteren en werken met incidenten. 

Om inzicht te krijgen in de status, prestaties en het gebruik van uw playbooks, inclusief alle die u toevoegt met Azure Logic Apps, hebben we een [Azure-werkmap](/azure/azure-monitor/platform/workbooks-overview) met de naam **playbooks Health Monitoring** toegevoegd. 

Gebruik de **Playbooks-status controle** werkmap om de status van uw Playbooks te controleren of om afwijkingen op te sporen in de hoeveelheid geslaagde of mislukte uitvoeringen. 

De **Playbooks Health Monitoring** -werkmap is nu beschikbaar in de Azure Sentinel-sjablonen galerie:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Voor beeld van een werkmap voor de Playbooks-status controle":::

Zie voor meer informatie:

- [Documentatie over Logic Apps](/azure/logic-apps/monitor-logic-apps-log-analytics#set-up-azure-monitor-logs)

- [Azure Monitor-documentatie](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

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
