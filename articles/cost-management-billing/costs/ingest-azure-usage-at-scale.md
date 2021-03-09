---
title: Grote kosten gegevens sets terughalen met de export bewerkingen
description: Dit artikel helpt u regel matig grote hoeveel heden gegevens te exporteren met uitvoer van Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 03/08/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 465225341bdffc984ac6cbc82ba94eb656ad60df
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509679"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Grote kosten gegevens sets terughalen met de export bewerkingen

Dit artikel helpt u regel matig grote hoeveel heden gegevens te exporteren met uitvoer van Azure Cost Management. Exporteren is de aanbevolen manier om niet-samengevoegde kostengegevens op te halen, vooral wanneer gebruiksbestanden te groot zijn om ze betrouwbaar te kunnen aanroepen en downloaden met de API voor gedetailleerde gebruiksgegevens. Geëxporteerde gegevens worden in het Azure Storage-account van uw keuze geplaatst. Van daaruit kunt u ze in uw eigen systemen laden en naar behoefte analyseren. Zie [Gegevens exporteren](tutorial-export-acm-data.md) om exports in Azure Portal te configureren.

Als u exports met verschillende bereiken wilt automatiseren, is het voorbeeld van een API-aanvraag in de volgende sectie een goed uitgangspunt. U kunt de Exports-API gebruiken om automatische exports te maken als onderdeel van uw algemene omgevingsconfiguratie. Automatische exports helpen te verzekeren dat u over de gegevens beschikt die u nodig hebt. U kunt ze gebruiken in de systemen van uw eigen organisatie naarmate u uw Azure-gebruik uitbreidt.

## <a name="common-export-configurations"></a>Algemene exportconfiguraties

Voordat u uw eerste export maakt, overweegt u uw scenario en de configuratieopties die u nodig hebt om het mogelijk te maken. Overweeg de volgende exportopties:

- **Terugkeerpatroon**: Bepaalt hoe vaak de exporttaak wordt uitgevoerd en wanneer een bestand in uw Azure Storage-account wordt geplaatst. Kies tussen Dagelijks, Wekelijks en Maandelijks. Probeer uw terugkeerpatroon zodanig te configureren dat het overeenkomt met de gegevensimporttaken die worden gebruikt door het interne systeem van uw organisatie.
- **Terugkeerperiode**: Bepaalt hoelang de export geldig blijft. Bestanden worden alleen geëxporteerd tijdens de terugkeerperiode.
- **Tijdsbestek**: Bepaalt hoeveel gegevens er door de export worden gegenereerd bij een bepaalde uitvoering. Algemene opties zijn MonthToDate en WeekToDate.
- **StartDate**: Hiermee configureert u wanneer u wilt dat het exportschema begint. Een export wordt gemaakt op de StartDate en wordt daarna gebaseerd op uw Terugkeerpatroon.
- **Type**: Er zijn drie exporttypen:
  - ActualCost: Toont het totale gebruik en de totale kosten voor de opgegeven periode naarmate ze worden gemaakt en weergegeven op uw factuur.
  - AmortizedCost: Toont het totale gebruik en de totale kosten voor de opgegeven periode, waarbij afschrijving wordt toegepast op de toepasselijke aankoopkosten van reserveringen.
  - Gebruik: Alle exports die vóór 20 juli 2020 zijn gemaakt, zijn van het type Gebruik. Werk al uw geplande exports bij als ActualCost of AmortizedCost.
- **Kolommen**: Definieert de gegevensvelden die u in uw exportbestand wilt opnemen. Ze komen overeen met de velden die beschikbaar zijn in de API voor gedetailleerde gebruiksgegevens. Zie [API voor gedetailleerde gebruiksgegevens](/rest/api/consumption/usagedetails/list) voor meer informatie.

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Een dagelijkse export van maand tot heden maken voor een abonnement

Aanvraag-URL: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>Grote Azure Storage-blobs kopiëren

U kunt Cost Management gebruiken om de export van uw Azure-gebruiks gegevens te plannen in uw Azure Storage-accounts als blobs. De resulterende BLOB-grootten kunnen groter zijn dan gigabytes. Het Azure Cost Management-team heeft met het Azure Storage team gewerkt om het kopiëren van grote Azure Storage-blobs te testen. De resultaten worden beschreven in de volgende secties. U kunt een vergelijk bare resultaten verwachten wanneer u opslag-blobs van de ene Azure-regio naar de andere kopieert.

Om de prestaties te testen, heeft het team de blobs overgedragen van opslag accounts in de regio vs West naar dezelfde en andere regio's. Het team heeft een snelheid gemeten van meer dan 2 GB per seconde in dezelfde regio tot 150 MB per seconde voor opslag accounts in de regio Zuid-Azië-oost.

### <a name="test-configuration"></a>Configuratie testen

Om de snelheid van BLOB-overdracht te meten, heeft het team een eenvoudige .NET-console toepassing gemaakt die verwijst naar de meest recente versie (v 2.0.1) van de Azure data verplaatsings bibliotheek (DLM) via NuGet. DLM is een SDK van het Azure Storage-team die programmatische toegang tot hun overdrachts Services vereenvoudigt. Vervolgens hebben ze standaard v2-opslag accounts in meerdere regio's gemaakt en de VS West als bron regio gebruikt. De opslag accounts zijn gevuld met containers, waarbij elke 10 2-GB blok-blobs worden bewaard. Ze hebben de containers naar andere opslag accounts gekopieerd met behulp van de methode _TransferManager. CopyDirectoryAsync ()_ van DLM met de optie _CopyMethod. ServiceSideSyncCopy_ . Er zijn tests uitgevoerd op een computer met Windows 10 met 12 kernen en 1 GbE-netwerk.

Gebruikte toepassings instellingen:

- _TransferManager.Configurations. ParallelOperations_  =  _environment. ProcessorCount \* 32_. Het team heeft gevonden dat de instelling het meest effect heeft op de algemene door voer. Een waarde van 32 maal het aantal kernen dat de beste door Voer voor de test client biedt.
- _ServicePointManager. DefaultConnectionLimit = int. MaxValue_. Als u deze instelt op een maximum waarde, wordt de volledige controle over de parallelle overdracht door gegeven aan de _ParallelOperations_ -instelling hierboven.
- _TransferManager.Configurations. Blok grootte = 4.194.304_. Het had een invloed op de overdrachts tarieven met 4 MB, om het beste te kunnen testen.

Zie koppelingen in de sectie [volgende stappen](#next-steps) voor meer informatie en voorbeeld code.

### <a name="test-results"></a>Testresultaten

| **Test nummer** | **Naar regio** | **Blobs** | **Tijd (sec.)** | **MB/s** | **Opmerkingen** |
| --- | --- | --- | --- | --- | --- |
| 1 | WestUS | 2 GB x 10 | 10 | 2.000 |   |
| 2 | WestUS2 | 2 GB x 10 | 33 | 600 |   |
| 3 | EastUS | 2 GB x 10 | 67 | 300 |   |
| 4 | EastUS | 2 GB x 10 x 4 | 99 | 200 | 4 parallelle overdrachten met acht opslag accounts: 4 tot 4 Oost gemiddeld per overdracht |
| 6 | EastUS | 2 GB x 10 x 4 | 92 | 870 | 4 parallelle overdracht van 1 opslag account naar een ander |
| 5 | EastUS | 2G x 10 x 8 | 148 | 135 | 8 parallelle overdrachten met acht opslag accounts: 4-West-naar-4x2 Oost gemiddeld per overdracht |
| 7 | Zuidoost-Azië | 2 GB x 10 | 133 | 150 |   |
| 8 | Zuidoost-Azië | 2 GB x 10 x 4 | 444 | 180 | 4 parallelle overdracht van 1 opslag account naar een ander |

### <a name="sync-transfer-characteristics"></a>Kenmerken van synchronisatie overdracht

Hier volgen enkele kenmerken van de synchronisatie overdracht aan de service zijde die wordt gebruikt met DML die relevant zijn voor het gebruik ervan:

- DML kan één BLOB of een map overdragen. Voor Directory overdracht kunt u een zoek patroon gebruiken dat overeenkomt met het voor voegsel van de blob.
- Blok-BLOB-overdrachten worden parallel uitgevoerd. Alle volledig aan het einde van het overdrachts proces. Afzonderlijke BLOB-blokken worden parallel overgedragen.
- De overdracht wordt asynchroon uitgevoerd op de client. De overdrachts status is periodiek beschikbaar via een call back naar een methode die kan worden gedefinieerd in een _TransferContext_ -object.
- De overdracht maakt controle punten tijdens de voortgang en stelt een _TransferCheckpoint_ -object beschikbaar. Het object vertegenwoordigt het laatste controle punt via het _TransferContext_ -object. Als de _TransferCheckpoint_ wordt opgeslagen voordat een overdracht wordt geannuleerd/afgebroken, kan de overdracht van Maxi maal zeven dagen worden hervat vanaf het controle punt. De overdracht kan worden hervat vanuit elk controle punt, niet alleen de laatste.
- Als het client proces van de overdracht wordt afgebroken en opnieuw wordt gestart zonder de functie voor het controle punt te implementeren.
  - Voordat een BLOB-overdracht is voltooid, wordt de overdracht opnieuw gestart.
  - Nadat enkele blobs zijn voltooid, wordt de overdracht opnieuw gestart voor alleen de niet-voltooide blobs.
- Wanneer de uitvoering van de client wordt onderbroken, worden de overdrachten onderbroken.
- De functie voor het overdragen van blobs abstractt de client van tijdelijke fouten. Het beperken van een opslag account leidt er doorgaans niet toe dat een overdracht mislukt, maar de overdracht wordt vertraagd.
- Overdrachten aan service zijde hebben weinig client gebruik voor de CPU en het geheugen, een aantal netwerk bandbreedte en verbindingen.

### <a name="async-transfer-characteristics"></a>Kenmerken van async-overdracht

U kunt de methode _TransferManager. CopyDirectoryAsync ()_ aanroepen met de optie _CopyMethod. ServiceSideAsyncCopy_ . Het werkt op dezelfde manier als het mechanisme voor synchronisatie overdracht van het client perspectief, maar met de volgende verschillen in bewerking:

- De overdrachts tarieven zijn veel langzamer dan de equivalente synchronisatie overdracht (meestal 10 MB/s of minder).
- De overdracht wordt voortgezet, zelfs als het client proces wordt beëindigd.
- Hoewel controle punten worden ondersteund, kan het hervatten van een overdracht met een _TransferCheckpoint_ niet worden hervat op het tijd controlepunt, maar bij de huidige status van de overdracht.

### <a name="test-summary"></a>Test samenvatting

Azure Blob-opslag ondersteunt hoge wereld wijde overdrachts snelheden met de functie synchronisatie overdracht aan de service zijde. Het gebruik van de functie in .NET-toepassingen is eenvoudig met behulp van de bibliotheek voor gegevens verplaatsing. Het is mogelijk dat Cost Management exporteert naar een betrouw bare kopie van honderden gigabytes aan gegevens naar een opslag account in minder dan een uur.

## <a name="next-steps"></a>Volgende stappen

- Zie de [Microsoft Azure Storage](https://github.com/Azure/azure-storage-net-data-movement) bron voor het verplaatsen van gegevens.
- [Gegevens overdragen met de bibliotheek voor gegevens verplaatsing](../../storage/common/storage-use-data-movement-library.md).
- Zie het voor beeld van een [AzureDmlBackup-voorbeeld toepassing](https://github.com/markjbrown/AzureDmlBackup) .
- Lees [met hoge door Voer met Azure Blob Storage](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage).