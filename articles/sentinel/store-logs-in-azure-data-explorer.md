---
title: Integratie Azure Data Explorer voor langetermijnretentie van logboeken | Microsoft Docs
description: Verzend Azure Sentinel logboeken naar Azure Data Explorer voor langetermijnretentie om de kosten voor gegevensopslag te verlagen.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: bagol
ms.openlocfilehash: c7d9ef58d4bb15afe59c6734ce887c2cc3c07c26
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836174"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>Integratie Azure Data Explorer voor langetermijnretentie van logboeken

<!--Info not included:>

Script - can't xref out to a private github repo from docs
-->

Standaard worden logboeken die zijn opgenomen in Azure Sentinel opgeslagen in Azure Monitor Log Analytics. In dit artikel wordt uitgelegd hoe u de retentiekosten in Azure Sentinel verlagen door ze naar Azure Data Explorer (ADX) te verzenden voor langetermijnretentie.

Het opslaan van logboeken in ADX vermindert de kosten terwijl u de mogelijkheid behoudt om query's uit te voeren op uw gegevens en is vooral nuttig wanneer uw gegevens groeien. Hoewel beveiligingsgegevens in de loop van de tijd bijvoorbeeld waardevol kunnen worden, moet u mogelijk logboeken bewaren voor wettelijke vereisten of periodieke onderzoeken uitvoeren op oudere gegevens.

## <a name="about-azure-data-explorer"></a>Over Azure Data Explorer

ADX is een big data analytics-platform dat in hoge mate is geoptimaliseerd voor logboek- en gegevensanalyse. Omdat ADX Kusto Query Language (KQL) gebruikt als querytaal, is het een goed alternatief voor Azure Sentinel opslag van gegevens. Door ADX te gebruiken voor uw gegevensopslag kunt u platformoverschrijdende query's uitvoeren en gegevens visualiseren in ADX en Azure Sentinel.

Zie de ADX-documentatie en [-blog](/azure/data-explorer/) voor meer [informatie.](https://azure.microsoft.com/en-us/blog/tag/azure-data-explorer/)

### <a name="when-to-integrate-with-adx"></a>Wanneer integreren met ADX

Azure Sentinel biedt volledige SIEM- en SOAR-mogelijkheden, snelle implementatie en configuratie, evenals geavanceerde, ingebouwde beveiligingsfuncties voor SOC-teams. De waarde van het opslaan van beveiligingsgegevens in Azure Sentinel kan echter na een paar maanden afvallen, zodra SOC-gebruikers deze niet zo vaak nodig hebben als ze toegang hebben tot nieuwere gegevens.

Als u slechts af en toe toegang nodig hebt tot specifieke tabellen, zoals voor periodieke onderzoeken of controles, kunt u overwegen dat het bewaren van uw gegevens in Azure Sentinel niet langer rendabel is. Op dit moment raden we u aan om gegevens op te slaan in ADX, wat minder kost, maar u toch in staat stelt om te verkennen met dezelfde KQL-query's die u in uw Azure Sentinel.

U kunt de gegevens in ADX rechtstreeks vanuit uw Azure Sentinel met behulp van de [Log Analytics ADX-proxyfunctie](//azure/azure-monitor/logs/azure-monitor-data-explorer-proxy). U doet dit door clusteroverschrijdende query's te gebruiken in uw logboekzoek- of werkmappen. 

> [!IMPORTANT]
> De belangrijkste SIEM-mogelijkheden, waaronder analyseregels, UEBA en de onderzoeksgrafiek, bieden geen ondersteuning voor gegevens die zijn opgeslagen in ADX.
>

> [!NOTE]
> Door te integreren met ADX kunt u ook controle en granulariteit in uw gegevens hebben. Zie Ontwerpoverwegingen [voor meer informatie.](#design-considerations)
> 
## <a name="send-data-directly-to-azure-sentinel-and-adx-in-parallel"></a>Gegevens rechtstreeks naar Azure Sentinel ADX parallel verzenden

Mogelijk wilt u gegevens met een beveiligingswaarde *in* Azure Sentinel bewaren voor detecties, incidentonderzoeken, opsporing van bedreigingen, UEBA, en meer. Het bewaren van deze gegevens in Azure Sentinel voordelen van Security Operations Center-gebruikers (SOC), waarbij doorgaans 3-12 maanden aan opslagruimte voldoende zijn.

U kunt ook al uw gegevens configureren, ongeacht de *beveiligingswaarde,* zodat ze tegelijkertijd naar ADX worden verzonden, waar u ze langer kunt opslaan. Hoewel het tegelijkertijd verzenden van gegevens naar Azure Sentinel en ADX leidt tot duplicatie, kunnen de kostenbesparingen aanzienlijk zijn als u de retentiekosten in Azure Sentinel.

> [!TIP]
> Met deze optie kunt u ook gegevens correleren die zijn verspreid over gegevensopslag, zoals om de beveiligingsgegevens die zijn opgeslagen in Azure Sentinel te verrijken met operationele of langetermijngegevens die zijn opgeslagen in ADX. Zie Query's uitvoeren op meerdere resources voor [Azure Data Explorer met behulp van Azure Monitor.](/azure/azure-monitor/logs/azure-monitor-data-explorer-proxy)
>

In de volgende afbeelding ziet u hoe u al uw gegevens in ADX kunt bewaren, terwijl u alleen uw beveiligingsgegevens naar Azure Sentinel voor dagelijks gebruik.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Gegevens opslaan in ADX en Azure Sentinel parallel opslaan.":::

Zie voor meer informatie over het implementeren van deze architectuuroptie [Azure Data Explorer bewaking.](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer)

## <a name="export-data-from-log-analytics-into-adx"></a>Gegevens exporteren van Log Analytics naar ADX

In plaats van uw gegevens rechtstreeks naar ADX te verzenden, kunt u ervoor kiezen om uw gegevens vanuit Log Analytics te exporteren naar ADX via een ADX Event Hub of Azure Data Factory.

### <a name="data-export-architecture"></a>Architectuur voor gegevensexport

In de volgende afbeelding ziet u een voorbeeldstroom van geëxporteerde gegevens via Azure Monitor opnamepijplijn. Uw gegevens worden standaard omgeleid naar Log Analytics, maar u kunt deze ook configureren om te exporteren naar een Azure Storage-account of Event Hub.

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Gegevens exporteren uit Azure Monitor - architectuur.":::

Wanneer u de regels voor gegevensexport configureert, selecteert u de typen logboeken die u wilt exporteren. Na de configuratie worden nieuwe gegevens die bij het Log Analytics-opname-eindpunt aankomen en gericht zijn op uw werkruimte voor de geselecteerde tabellen, geëxporteerd naar uw opslagaccount of Event Hub.

Houd rekening met de volgende overwegingen bij het configureren van gegevens voor export:

|Overweging  | Details |
|---------|---------|
|**Bereik van geëxporteerde gegevens**     |  Zodra de export is geconfigureerd voor een specifieke tabel, worden alle gegevens die naar die tabel worden verzonden geëxporteerd, zonder uitzondering. Een gefilterde subset van uw gegevens geëxporteerd of de export beperken tot specifieke gebeurtenissen, wordt niet ondersteund.       |
|**Locatievereisten**     |   Zowel de Azure Monitor/Azure Sentinel als de doellocatie (een Azure Storage-account of event hub) moeten zich in dezelfde geografische regio bevinden.      |
|**Ondersteunde tabellen**     | Niet alle tabellen worden ondersteund voor export, zoals aangepaste logboektabellen, die niet worden ondersteund. <br><br>Zie Gegevensexport van [Log Analytics-werkruimten in](/azure/azure-monitor/logs/logs-data-export) Azure Monitor en de [lijst met ondersteunde tabellen voor meer informatie.](/azure/azure-monitor/logs/logs-data-export#supported-tables)         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>Methoden en procedures voor gegevensexport

Gebruik een van de volgende procedures om gegevens uit uw Azure Sentinel exporteren naar ADX:

- **Via een ADX Event Hub**. Exporteer gegevens uit Log Analytics naar een Event Hub, waar u deze kunt opnemen in ADX. Met deze methode worden sommige gegevens (de eerste X maanden) op zowel Azure Sentinel als ADX.

- **Via Azure Storage en Azure Data Factory**. Exporteer uw gegevens van Log Analytics naar Azure Blob Storage, waarna Azure Data Factory wordt gebruikt om een periodieke kopieer job uit te voeren om de gegevens verder te exporteren naar ADX. Met deze methode kunt u alleen gegevens uit Azure Data Factory kopiëren wanneer de bewaarlimiet in Azure Sentinel/Log Analytics is bereikt, om duplicatie te voorkomen.

### <a name="adx-event-hub"></a>[ADX Event Hub](#tab/adx-event-hub)

In deze sectie wordt beschreven hoe Azure Sentinel uit Log Analytics kunt exporteren naar een Event Hub, waar u deze kunt opnemen in ADX. Net als bij het parallel verzenden van gegevens naar Azure Sentinel en [ADX,](#send-data-directly-to-azure-sentinel-and-adx-in-parallel)bevat deze methode enkele gegevensduplicatie omdat de gegevens worden gestreamd naar ADX wanneer deze in Log Analytics binnenkomen.

In de volgende afbeelding ziet u een voorbeeldstroom van geëxporteerde gegevens naar een Event Hub, van waaruit deze worden opgenomen in ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="Gegevens exporteren naar ADX via een ADX Event Hub.":::

De architectuur die in de vorige afbeelding wordt weergegeven, biedt de volledige AZURE SENTINEL SIEM-ervaring, waaronder incidentbeheer, visuele onderzoeken, opsporing van bedreigingen, geavanceerde visualisaties, UEBA en meer, voor gegevens die regelmatig moeten worden geopend, elke *X* maanden. Tegelijkertijd kunt u met deze architectuur ook langetermijngegevens opvragen door ze rechtstreeks in ADX te openen of via Azure Sentinel dankzij de ADX-proxyfunctie. Query's naar langetermijngegevensopslag in ADX kunnen worden overgeplaatst zonder wijzigingen van Azure Sentinel in ADX.

> [!TIP]
> Zie Voor meer informatie over deze procedure [Zelfstudie: Bewakingsgegevens](/azure/data-explorer/ingest-data-no-code)opnemen en opvragen in Azure Data Explorer.
>

**Gegevens exporteren naar ADX via een Event Hub:**

1. **Configureer de Log Analytics-gegevensexport naar een Event Hub**. Zie Gegevensexport van [Log Analytics-werkruimte in](/azure/azure-monitor/platform/logs-data-export)Azure Monitor voor meer Azure Monitor.

1. **Maak een ADX-cluster en -database.** Zie voor meer informatie:

    - [Een cluster Azure Data Explorer database maken](/azure/data-explorer/create-cluster-database-portal)
    - [Selecteer de juiste reken-SKU voor uw Azure Data Explorer cluster](/azure/data-explorer/manage-cluster-choose-sku)

1. **Doeltabellen maken.** De onbewerkte gegevens worden eerst opgenomen in een tussenliggende tabel, waar de onbewerkte gegevens worden opgeslagen, bewerkt en uitgebreid.

    Een updatebeleid, dat vergelijkbaar is met een functie die wordt toegepast op alle nieuwe gegevens, wordt gebruikt om de uitgebreide gegevens op te nemen in de uiteindelijke tabel, die hetzelfde schema heeft als de oorspronkelijke tabel in Azure Sentinel.

    Stel de retentie voor de onbewerkte tabel in **op 0** dagen. De gegevens worden alleen opgeslagen in de correct opgemaakte tabel en verwijderd uit de onbewerkte tabel zodra deze is getransformeerd.

    Zie Bewakingsgegevens opnemen [en er query's op uitvoeren in Azure Data Explorer.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. <a name="mapping"></a>**Maak tabeltoewijzing**. Wijs de JSON-tabellen toe om te definiëren hoe records in de tabel met onbewerkte gebeurtenissen belandt wanneer ze afkomstig zijn van een Event Hub. Zie Het updatebeleid maken voor metrische gegevens en [logboekgegevens voor meer informatie.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. **Maak een updatebeleid en koppel dit aan de tabel met onbewerkte records.** In deze stap maakt u een functie, een updatebeleid genoemd, en koppelt u deze aan de doeltabel, zodat de gegevens tijdens de opname worden getransformeerd.

    > [!NOTE]
    > Deze stap is alleen vereist als u gegevenstabellen in ADX wilt hebben met hetzelfde schema en dezelfde indeling als in Azure Sentinel.
    >

    Zie Connect [an event hub to Azure Data Explorer (Een Event Hub verbinden met Azure Data Explorer) voor Azure Data Explorer.](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)

1. **Maak een gegevensverbinding tussen de Event Hub en de tabel met onbewerkte gegevens in ADX.** Configureer ADX met details over het exporteren van de gegevens naar de Event Hub.

    Volg de instructies in de [Azure Data Explorer en](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) geef de volgende details op:

    - **Doel**. Geef de specifieke tabel met de onbewerkte gegevens op.
    - **Maak op.** Geef `.json` op als tabelindeling.
    - **Toewijzing die moet worden toegepast.** Geef de toewijzingstabel op die u in [stap 4 hierboven](#mapping) hebt gemaakt.


1. **Wijzig de retentie voor de doeltabel**. Het [standaard Azure Data Explorer retentiebeleid](/azure/data-explorer/kusto/management/retentionpolicy) is mogelijk veel langer dan u nodig hebt.

    Gebruik de volgende opdracht om het retentiebeleid bij te werken naar één jaar:

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```
### <a name="azure-storage--azure-data-factory"></a>[Azure Storage/Azure Data Factory](#tab/azure-storage-azure-data-factory)

In deze sectie wordt beschreven hoe u Azure Sentinel van Log Analytics exporteert naar Azure Storage, waar Azure Data Factory een reguliere taak kunt uitvoeren om de gegevens te exporteren naar ADX.

Met Azure Storage en Azure Data Factory kunt u alleen gegevens uit Azure Storage kopiëren wanneer deze de retentielimiet in Azure Sentinel/Log Analytics bijna hebben bereikt. Er is geen gegevensduplicatie en  ADX wordt alleen gebruikt voor toegang tot gegevens die ouder zijn dan de retentielimiet in Azure Sentinel.

> [!TIP]
> Hoewel de architectuur voor het gebruik Azure Storage en Azure Data Factory voor uw verouderde gegevens complexer is, kan deze methode in het algemeen grotere kostenbesparingen opleveren.
>
In de volgende afbeelding ziet u een voorbeeldstroom van geëxporteerde gegevens naar een Azure Storage, van waaruit Azure Data Factory een reguliere taak wordt uitgevoerd om deze verder te exporteren naar ADX.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Gegevens exporteren naar ADX via Azure Storage en Azure Data Factory.":::

**Gegevens exporteren naar ADX via een Azure Storage en Azure Data Factory**:

1. **Configureer de Log Analytics-gegevensexport naar een Event Hub**. Zie Gegevensexport van [Log Analytics-werkruimte in](/azure/azure-monitor/logs/logs-data-export?tabs=portal#enable-data-export)Azure Monitor voor meer Azure Monitor.

1. **Maak een ADX-cluster en -database.** Zie voor meer informatie:

    - [Een cluster Azure Data Explorer database maken](/azure/data-explorer/create-cluster-database-portal)
    - [Selecteer de juiste reken-SKU voor uw Azure Data Explorer cluster](/azure/data-explorer/manage-cluster-choose-sku)

1. **Doeltabellen maken.** De onbewerkte gegevens worden eerst opgenomen in een tussenliggende tabel, waar de onbewerkte gegevens worden opgeslagen, bewerkt en uitgebreid.

    Een updatebeleid, dat vergelijkbaar is met een functie die wordt toegepast op alle nieuwe gegevens, wordt gebruikt om de uitgebreide gegevens op te nemen in de laatste tabel, die hetzelfde schema heeft als de oorspronkelijke tabel in Azure Sentinel.

    Stel de retentie voor de onbewerkte tabel in **op 0** dagen. De gegevens worden alleen opgeslagen in de correct opgemaakte tabel en verwijderd uit de onbewerkte tabel zodra deze is getransformeerd.

    Zie Bewakingsgegevens opnemen [en er query's op uitvoeren in Azure Data Explorer.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. <a name="mapping"></a>**Maak tabeltoewijzing**. Wijs de JSON-tabellen toe om te definiëren hoe records in de tabel met onbewerkte gebeurtenissen belandt wanneer ze afkomstig zijn van een Event Hub. Zie Het updatebeleid maken voor metrische gegevens en [logboekgegevens voor meer informatie.](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics)

1. **Maak een updatebeleid en koppel dit aan de tabel met onbewerkte records.** In deze stap maakt u een functie, een updatebeleid genoemd, en koppelt u deze aan de doeltabel, zodat de gegevens tijdens de opname worden getransformeerd.

    > [!NOTE]
    > Deze stap is alleen vereist als u gegevenstabellen in ADX wilt hebben met hetzelfde schema en dezelfde indeling als in Azure Sentinel.
    >

    Zie Connect [an event hub to Azure Data Explorer (Een Event Hub verbinden met Azure Data Explorer).](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs)

1. **Maak een gegevensverbinding tussen de Event Hub en de tabel met onbewerkte gegevens in ADX.** Configureer ADX met details over het exporteren van de gegevens naar de Event Hub.

    Volg de instructies in de [Azure Data Explorer en](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) geef de volgende details op:

    - **Doel**. Geef de specifieke tabel met de onbewerkte gegevens op.
    - **Maak op.** Geef `.json` op als tabelindeling.
    - **Toewijzing die moet worden toegepast.** Geef de toewijzingstabel op die u in [stap 4 hierboven](#mapping) hebt gemaakt.

1. **Stel de pijplijn Azure Data Factory in:**

    - Maak gekoppelde services voor Azure Storage en Azure Data Explorer. Zie voor meer informatie:

        - [Gegevens kopiëren en transformeren in Azure Blob Storage met behulp van Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
        - [Kopieer gegevens naar of van Azure Data Explorer met behulp van Azure Data Factory](/azure/data-factory/connector-azure-data-explorer).

    - Maak een gegevensset op basis van Azure Storage. Zie Gegevenssets in Azure Data Factory voor [meer Azure Data Factory.](/azure/data-factory/concepts-datasets-linked-services)

    - Maak een gegevenspijplijn met een kopieerbewerking op basis van de **eigenschappen LastModifiedDate.**

        Zie Nieuwe en gewijzigde bestanden kopiëren met [ **LastModifiedDate** met](/azure/data-factory/solution-template-copy-new-files-lastmodifieddate)Azure Data Factory voor meer Azure Data Factory.

---

## <a name="design-considerations"></a>Overwegingen bij het ontwerpen

Wanneer u uw Azure Sentinel in ADX opslaat, moet u rekening houden met de volgende elementen:

|Overweging  |Description  |
|---------|---------|
|**Clustergrootte en SKU**     | Plan zorgvuldig het aantal knooppunten en de VM-SKU in uw cluster. Deze factoren bepalen de hoeveelheid verwerkingskracht en de grootte van uw hot cache (SSD en geheugen). Hoe groter de cache, hoe meer gegevens u kunt opvragen bij hogere prestaties. <br><br>We raden u [](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)aan om de ADX-formaatcalculator te bezoeken, waar u met verschillende configuraties kunt spelen en de resulterende kosten kunt bekijken. <br><br>ADX biedt ook een mogelijkheid voor automatisch schalen die intelligente beslissingen neemt om knooppunten toe te voegen of te verwijderen op basis van de clusterbelasting. Zie Horizontaal schalen (uitschalen) van clusters beheren in Azure Data Explorer [aan veranderende vraag te voldoen voor meer informatie.](/azure/data-explorer/manage-cluster-horizontal-scaling)      |
|**Hot/cold cache**     | ADX biedt controle over de gegevenstabellen in de hot cache en retourneer sneller resultaten. Als uw ADX-cluster grote hoeveelheden gegevens heeft, wilt u tabellen mogelijk per maand opsommen, zodat u meer granulariteit hebt op de gegevens die aanwezig zijn in uw hot cache. <br><br>Zie Cachebeleid [(hot en cold cache) voor meer informatie.](/azure/data-explorer/kusto/management/cachepolicy)       |
|**Retentie**     |   In ADX kunt u configureren wanneer gegevens worden verwijderd uit een database of een afzonderlijke tabel. Dit is ook een belangrijk onderdeel van het beperken van de opslagkosten. <br><br> Zie Bewaarbeleid [voor meer informatie.](/azure/data-explorer/kusto/management/retentionpolicy)       |
|**Beveiliging**     |  Verschillende ADX-instellingen kunnen u helpen bij het beveiligen van uw gegevens, zoals identiteitsbeheer, versleuteling, en meer. Met name voor op rollen gebaseerd toegangsbeheer (RBAC) kan ADX worden geconfigureerd om de toegang tot databases, tabellen of zelfs rijen in een tabel te beperken. Zie Security in Azure Data Explorer and Row level security (Beveiliging [op rijniveau) voor](/azure/data-explorer/security) [meer informatie.](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy)|
|**Gegevens delen**     |   Met ADX kunt u gegevens beschikbaar maken voor andere partijen, zoals partners of leveranciers, en zelfs gegevens van andere partijen kopen. Zie Use Azure Data Share to share data with Azure Data Explorer (Gegevens Azure Data Share [delen met Azure Data Explorer.](/azure/data-explorer/data-share)      |
| **Andere kostenonderdelen** | Houd rekening met de andere kostenonderdelen voor de volgende methoden: <br><br>**Gegevens exporteren via een ADX Event Hub:** <br>- Kosten voor het exporteren van Log Analytics-gegevens, in rekening gebracht per geëxporteerde GBs. <br>- Event Hub-kosten, in rekening gebracht per doorvoereenheid.  <br><br>**Gegevens exporteren via Azure Storage en Azure Data Factory**: <br>- Exporteren van Log Analytics-gegevens, in rekening gebracht per geëxporteerde GBs. <br>- Azure Storage, in rekening gebracht door opgeslagen TB's. <br>- Azure Data Factory, in rekening gebracht per kopie van de activiteiten die worden uitgevoerd.
|     |         |

## <a name="next-steps"></a>Volgende stappen

Ongeacht waar u uw gegevens opgeslagen, kunt u doorgaan met de opsporing en het onderzoeken met behulp Azure Sentinel.

Zie voor meer informatie:

- [Zelfstudie: Incidenten met Azure Sentinel](tutorial-investigate-cases.md)
- [Bedreigingen zoeken met Azure Sentinel](hunting.md)
