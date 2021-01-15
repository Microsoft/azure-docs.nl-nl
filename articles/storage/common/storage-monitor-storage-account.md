---
title: Een Azure Storage-account bewaken in de Azure Portal | Microsoft Docs
description: Meer informatie over het bewaken van een opslag account in azure met behulp van de Azure Portal en Azure Opslaganalyse.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: e5495b466bf9b16319b788ec32c7b3a03100f505
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218139"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Een Storage-account bewaken in de Azure-portal

[Azure Opslaganalyse](storage-analytics.md) biedt metrische gegevens voor alle opslag Services, en logboeken voor blobs, wacht rijen en tabellen. U kunt de [Azure Portal](https://portal.azure.com) gebruiken om te configureren welke metrische gegevens en logboeken voor uw account worden vastgelegd en om grafieken te configureren die visuele voors tellingen hebben van uw metriekgegevens. 

U wordt aangeraden [Azure monitor voor opslag](../../azure-monitor/insights/storage-insights-overview.md) te bekijken (preview). Het is een functie van Azure Monitor die uitgebreide controle biedt over uw Azure Storage-accounts door een uniforme weer gave te bieden van de prestaties, capaciteit en beschik baarheid van uw Azure Storage services. U hoeft niets in te scha kelen of te configureren, en u kunt deze metrische gegevens direct weer geven vanuit de vooraf gedefinieerde interactieve diagrammen en andere visualisaties die zijn opgenomen.

> [!NOTE]
> Er zijn kosten verbonden aan het onderzoeken van bewakings gegevens in de Azure Portal. Zie [Opslaganalyse](storage-analytics.md)voor meer informatie.
>
> Azure Files ondersteunt momenteel Opslaganalyse metrische gegevens, maar biedt nog geen ondersteuning voor logboek registratie.
>
> Opslag accounts voor Premium-prestaties blok-blobs bieden geen ondersteuning voor metrische gegevens over opslag analyse, maar wel ondersteuning voor logboek registratie. U kunt logboek registratie via een programma inschakelen via de REST API of de client bibliotheek. Als u metrische gegevens wilt weer geven met Premium-Blob-Blob Storage-accounts, kunt u overwegen [Azure Storage metrische gegevens in azure monitor](../blobs/monitor-blob-storage.md)te gebruiken.
>
> Zie [Microsoft Azure Storage controleren, diagnosticeren en problemen oplossen](storage-monitoring-diagnosing-troubleshooting.md)voor meer informatie over het gebruik van Opslaganalyse en andere hulpprogram ma's voor het identificeren, vaststellen en oplossen van problemen met Azure Storage.
>

<a id="modify-retention-policy"></a>

## <a name="configure-monitoring-for-a-storage-account"></a>Bewaking configureren voor een opslag account

1. Selecteer in de [Azure Portal](https://portal.azure.com) **opslag accounts** en vervolgens de naam van het opslag account om het account dashboard te openen.
1. Selecteer **Diagnostische gegevens** in het gedeelte **bewaking** van de menu-Blade.

    ![Scherm opname van de optie Diagnostische instellingen (klassiek) in de sectie bewaking (klassiek).](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Selecteer het **type** metrische gegevens voor elke **service** die u wilt bewaken en het **Bewaar beleid** voor de gegevens. U kunt de bewaking ook uitschakelen door de **status** in te stellen op **uit**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Als u het Bewaar beleid voor gegevens wilt instellen, verschuift u de schuif regelaar **bewaren (dagen)** of voert u het aantal dagen aan gegevens in dat u wilt behouden, van 1 tot 365. De standaard waarde voor nieuwe opslag accounts is zeven dagen. Als u geen Bewaar beleid wilt instellen, voert u nul in. Als er geen Bewaar beleid is, kunt u de bewakings gegevens verwijderen.

   > [!WARNING]
   > Er worden kosten in rekening gebracht wanneer u de metrische gegevens hand matig verwijdert. Verlopen analyse gegevens (gegevens die ouder zijn dan uw Bewaar beleid) worden kosteloos verwijderd door het systeem. We raden u aan een Bewaar beleid in te stellen op basis van hoe lang u de opslag analyse gegevens voor uw account wilt behouden. Zie [facturering op metrische](storage-analytics-metrics.md#billing-on-storage-metrics) gegevens van de opslag voor meer informatie.
   >

1. Wanneer u de bewakings configuratie hebt voltooid, selecteert u **Opslaan**.

Een standaardset metrische gegevens wordt weer gegeven in grafieken op de Blade van het opslag account, evenals de afzonderlijke service-Blades (BLOB, wachtrij, tabel en bestand). Zodra u de metrische gegevens voor een service hebt ingeschakeld, kan het tot een uur duren voordat het bestand in de grafieken wordt weer gegeven. U kunt **bewerken** selecteren in een metrische grafiek om te configureren welke metrische gegevens worden weer gegeven in de grafiek.

U kunt metrische gegevens verzamelen en logboek registratie uitschakelen door de **status** in te stellen op **uit**.

> [!NOTE]
> Azure Storage gebruikt [tabel opslag](storage-introduction.md#table-storage) om de metrische gegevens voor uw opslag account op te slaan, en slaat de metrische gegevens op in tabellen in uw account. Zie voor meer informatie [Hoe metrische gegevens worden opgeslagen](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Grafieken met metrische gegevens aanpassen

Gebruik de volgende procedure om te kiezen welke metrische opslag gegevens in een grafiek met metrische gegevens moeten worden weer gegeven.

1. Begin met het weer geven van een metrische opslag grafiek in het Azure Portal. U kunt grafieken vinden op de **Blade opslag account** en op de Blade **metrische gegevens** voor een afzonderlijke service (BLOB, wachtrij, tabel, bestand).

   In dit voor beeld wordt het volgende diagram gebruikt dat wordt weer gegeven op de **Blade opslag account**:

   ![Grafiek selectie in Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Klik ergens in het diagram om de grafiek te bewerken.

1. Selecteer vervolgens het **tijds bereik** van de metrische gegevens die u wilt weer geven in de grafiek en de **service** (BLOB, wachtrij, tabel, bestand) waarvan u de metrische gegevens wilt weer geven. Hier worden de metrische gegevens van de afgelopen week geselecteerd om weer te geven voor de BLOB-service:

   ![Tijds bereik en service selectie in de Blade grafiek bewerken](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Selecteer de afzonderlijke **metrische gegevens** die u wilt weer geven in de grafiek en klik vervolgens op **OK**.

   ![Afzonderlijke metrieke selectie in Blade grafiek bewerken](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

De instellingen van de grafiek zijn niet van invloed op de verzameling, aggregatie of opslag van bewakings gegevens in het opslag account.

### <a name="metrics-availability-in-charts"></a>Beschik baarheid van metrische gegevens in grafieken

De lijst met beschik bare metrische gegevens wordt gewijzigd op basis van de service die u hebt gekozen in de vervolg keuzelijst en het eenheids type van de grafiek die u wilt bewerken. U kunt bijvoorbeeld percentage metrische gegevens, zoals *PercentNetworkError* en *percentthrottlingerror aan* , alleen selecteren als u een grafiek bewerkt waarin eenheden worden weer gegeven in een percentage:

![Diagram voor fout percentage voor aanvraag in de Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Resolutie van metrische gegevens

De metrische gegevens die u in **Diagnostische gegevens** hebt geselecteerd, bepalen de resolutie van de metrische gegevens die beschikbaar zijn voor uw account:

* **Aggregatie** bewaking biedt metrische gegevens, zoals inkomend/uitgaand verkeer, Beschik baarheid, latentie en succes percentages. Deze metrische gegevens worden geaggregeerd vanuit de blob-, tabel-, bestands-en wachtrij Services.
* **Per API** biedt een nauw keurige oplossing, met metrische gegevens die beschikbaar zijn voor afzonderlijke opslag bewerkingen, naast de aggregatie op service niveau.

## <a name="configure-metrics-alerts"></a>Waarschuwingen voor metrische gegevens configureren

U kunt waarschuwingen maken om u te waarschuwen wanneer de drempel waarden voor de metrische gegevens van de opslag resource zijn bereikt.

1. Als u de **Blade waarschuwings regels** wilt openen, schuift u omlaag naar de sectie **bewaking** van de **menu-Blade** en selecteert u **waarschuwingen (klassiek)**.
2. Selecteer **waarschuwing voor metrische gegevens toevoegen (klassiek)** om de Blade **een waarschuwings regel toevoegen** te openen
3. Voer een **naam** en **Beschrijving** in voor de nieuwe waarschuwings regel.
4. Selecteer de **metriek** waarvoor u een waarschuwing, een waarschuwings **voorwaarde** en een **drempel waarde** wilt toevoegen. Het type drempel-eenheid verandert afhankelijk van de metriek die u hebt gekozen. Bijvoorbeeld: ' count ' is het eenheids type voor *ContainerCount*, terwijl de eenheid voor de metrische waarde van *PercentNetworkError* een percentage is.
5. Selecteer de **periode**. Metrische gegevens die de drempel binnen de periode bereiken of overschrijden, activeren een waarschuwing.
6. Beschrijving E-mail en **webhook** **-** meldingen configureren. Zie [een webhook configureren voor een Azure metric-waarschuwing](../../azure-monitor/platform/alerts-webhooks.md)voor meer informatie over webhooks. Als u geen e-mail of webhook-meldingen configureert, worden waarschuwingen alleen weer gegeven in de Azure Portal.

![De Blade een waarschuwings regel toevoegen in de Azure Portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Metrische grafieken toevoegen aan het portal-dash board

U kunt Azure Storage metrische grafieken voor uw opslag accounts toevoegen aan uw portal-dash board.

1. Selecteer in het [Azure Portal](https://portal.azure.com)op **dash board bewerken** terwijl u uw dash board weergeeft.
1. Selecteer in de **Galerie tegels** de optie **tegels zoeken op**  >  **type**.
1. Selecteer   >  **opslag accounts** opgeven.
1. Selecteer in **resources** het opslag account waarvan u de metrische gegevens aan het dash board wilt toevoegen.
1. Selecteer **categorie**  >  **bewaking**.
1. Slepen en neerzetten van de grafiek tegel naar het dash board voor de metrische gegevens die u wilt weer geven. Herhaal deze stap voor alle metrische gegevens die u op het dash board wilt weer geven. In de volgende afbeelding is de grafiek ' blobs-totaal aantal aanvragen ' gemarkeerd als voor beeld, maar alle grafieken zijn beschikbaar voor plaatsing op uw dash board.

   ![Tegel galerie in Azure Portal](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Selecteer **klaar met aanpassen** aan de bovenkant van het dash board wanneer u klaar bent met het toevoegen van grafieken.

Zodra u grafieken aan het dash board hebt toegevoegd, kunt u ze verder aanpassen zoals beschreven in metrische grafieken aanpassen.

## <a name="configure-logging"></a>Logboek registratie configureren

U kunt Azure Storage voor het opslaan van Diagnostische logboeken voor lees-, schrijf-en verwijderings aanvragen voor de blob-, Table-en Queue-Services. Het beleid voor gegevens retentie dat u instelt, is ook van toepassing op deze logboeken.

> [!NOTE]
> Azure Files ondersteunt momenteel Opslaganalyse metrische gegevens, maar biedt nog geen ondersteuning voor logboek registratie.
>

1. Selecteer in de [Azure Portal](https://portal.azure.com) **opslag accounts** en vervolgens de naam van het opslag account om de Blade opslag account te openen.
1. Selecteer **instellingen voor diagnostische gegevens (klassiek)** in het gedeelte **bewaking (klassiek)** van de menu Blade.

    ![Menu-item diagnostische gegevens onder bewaking in het Azure Portal.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Controleer of de **status** is ingesteld **op aan** en selecteer de **Services** waarvoor u logboek registratie wilt inschakelen.

    ![Configureer logboek registratie in de Azure Portal.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Klik op **Opslaan**.

De diagnostische logboeken worden opgeslagen in een BLOB-container met de naam *$logs* in uw opslag account. U kunt de logboek gegevens weer geven met behulp van een opslag Verkenner, zoals de [Microsoft Azure Storage Explorer](https://storageexplorer.com), of programmatisch met behulp van de Storage-client bibliotheek of Power shell.

Zie [logboek registratie van Storage Analytics](storage-analytics-logging.md)voor informatie over het openen van de $logs-container.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [metrische gegevens, logboek registratie en facturering](storage-analytics.md) voor Opslaganalyse.