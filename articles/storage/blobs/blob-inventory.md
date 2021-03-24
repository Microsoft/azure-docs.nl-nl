---
title: Azure Storage-inventaris gebruiken voor het beheren van BLOB-gegevens (preview)
description: Azure Storage-inventaris is een hulp programma waarmee u een overzicht kunt krijgen van alle BLOB-gegevens binnen een opslag account.
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 8310de465a6416102a7ce4e614ead7029e6be87a
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950923"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Azure Storage BLOB-inventaris gebruiken voor het beheren van BLOB-gegevens (preview)

De functie voor het Azure Storage BLOB-inventaris biedt een overzicht van de BLOB-gegevens in een opslag account. Gebruik het inventaris rapport om inzicht te krijgen in de totale gegevens grootte, leeftijd, versleutelings status, enzovoort. Het rapport bevat een overzicht van uw gegevens voor bedrijfs-en nalevings vereisten. Wanneer deze functie is ingeschakeld, wordt automatisch dagelijks een inventaris rapport gemaakt.

## <a name="availability"></a>Beschikbaarheid

BLOB-inventarisatie wordt ondersteund voor zowel GPv2 (General version 2) als Premium Block Blob-opslag accounts. Deze functie wordt ondersteund met of zonder de functie voor [hiërarchische naam ruimte](data-lake-storage-namespace.md) ingeschakeld.

> [!IMPORTANT]
> De BLOB-inventarisatie is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of nog niet beschikbaar zijn.

### <a name="preview-regions"></a>Preview-regio's

De voor beeld van de BLOB-inventaris is beschikbaar voor opslag accounts in de volgende regio's:

- Frankrijk - centraal
- Canada - midden
- Canada - oost
- VS - oost
- VS - oost 2

### <a name="pricing-and-billing"></a>Prijzen en facturering

De kosten voor voorraad rapporten worden niet in rekening gebracht tijdens de preview-periode. De prijs wordt bepaald wanneer deze functie algemeen beschikbaar is.

## <a name="enable-inventory-reports"></a>Inventarisatie rapporten inschakelen

Schakel de BLOB-inventaris rapporten in door een beleid toe te voegen aan uw opslag account. Een beleid toevoegen, bewerken of verwijderen met behulp van de [Azure Portal](https://portal.azure.com/).

1. Ga naar [Azure Portal](https://portal.azure.com/)
1. Selecteer een van uw opslag accounts
1. Selecteer onder **BLOB service** **BLOB-inventaris**
1. Zorg ervoor dat **BLOB-inventaris ingeschakeld** is geselecteerd
1. Selecteer **een regel toevoegen**
1. Geef een naam op voor de nieuwe regel
1. De **BLOB-typen** voor uw inventaris rapport selecteren
1. Een voor voegsel toevoegen die overeenkomt met het filteren van uw inventaris rapport
1. Selecteer of u **BLOB-versies wilt toevoegen** en **moment opnamen** in uw inventaris rapport wilt toevoegen. Versies en moment opnamen moeten zijn ingeschakeld voor het account om een nieuwe regel op te slaan met de bijbehorende optie ingeschakeld.
1. Selecteer **Opslaan**

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Scherm afbeelding die laat zien hoe u een BLOB-inventaris regel toevoegt met behulp van de Azure Portal":::

Inventarisatie beleidsregels worden volledig gelezen of geschreven. Gedeeltelijke updates worden niet ondersteund.

> [!IMPORTANT]
> Als u firewallregels voor uw opslagaccount inschakelt, worden voorraadaanvragen mogelijk geblokkeerd. U kunt deze aanvragen deblokkeren door uitzonderingen op te geven voor vertrouwde Microsoft-services. Zie de sectie uitzonde ringen in [firewalls en virtuele netwerken configureren](../common/storage-network-security.md#exceptions)voor meer informatie.

De uitvoering van een BLOB-inventaris wordt automatisch elke dag gepland. Het kan tot 24 uur duren voordat een inventarisatie is voltooid. Een inventaris rapport wordt geconfigureerd door een inventaris beleid toe te voegen met een of meer regels.

## <a name="inventory-policy"></a>Inventaris beleid

Een inventarisatie beleid is een verzameling regels in een JSON-document.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

Bekijk de JSON voor een inventaris beleid door het tabblad **code weergave** te selecteren in het gedeelte **BLOB-inventaris** van het Azure Portal.

| Parameternaam | Parametertype        | Notities | Vereist? |
|----------------|-----------------------|-------|-----------|
| doel    | Tekenreeks                | De doel container waar alle inventarisatie bestanden worden gegenereerd. De doel container moet al bestaan. | Ja |
| enabled        | Booleaans               | Wordt gebruikt om het hele beleid uit te scha kelen. Als deze eigenschap is ingesteld op **True**, wordt deze para meter overschreven door het veld op regel niveau ingeschakeld. Wanneer dit is uitgeschakeld, wordt de inventarisatie voor alle regels uitgeschakeld. | Ja |
| regels          | Matrix van regel objecten | Er is ten minste één regel vereist in een beleid. Maxi maal 10 regels worden ondersteund. | Ja |

## <a name="inventory-rules"></a>Inventarisatie regels

Met een regel worden de filter voorwaarden en uitvoer parameters voor het genereren van een inventaris rapport vastgelegd. Met elke regel wordt een inventaris rapport gemaakt. Regels kunnen overlappende voor voegsels hebben. Een BLOB kan in meer dan één inventaris worden weer gegeven, afhankelijk van de regel definities.

Elke regel in het beleid heeft verschillende para meters:

| Parameternaam | Parametertype                 | Notities | Vereist? |
|----------------|--------------------------------|-------|-----------|
| naam           | Tekenreeks                         | Een regel naam kan Maxi maal 256 hoofdletter gevoelige alfanumerieke tekens bevatten. De naam moet uniek zijn binnen een beleid. | Ja |
| enabled        | Booleaans                        | Een vlag waarmee een regel kan worden ingeschakeld of uitgeschakeld. De standaard waarde is **True**. | Ja |
| definitie     | Definitie van JSON-inventarisatie regel | Elke definitie bestaat uit een ingestelde regel filter. | Ja |

De vlag globale **BLOB-inventaris ingeschakeld** heeft voor rang op de *ingeschakelde* para meter in een regel.

### <a name="rule-filters"></a>Regel filters

Er zijn verschillende filters beschikbaar voor het aanpassen van een BLOB-inventaris rapport:

| Bestandsnaam         | Filtertype                     | Notities | Vereist? |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | Matrix van vooraf gedefinieerde Enum-waarden | Geldige waarden zijn `blockBlob` en `appendBlob` voor hiërarchische,, en `blockBlob` , `appendBlob` , en `pageBlob` voor andere accounts geschikte accounts. | Ja |
| prefixMatch         | Matrix van Maxi maal 10 teken reeksen voor voor voegsels die moeten worden vergeleken. Een voor voegsel moet beginnen met een container naam, bijvoorbeeld "container1/foo" | Als u geen *prefixMatch* definieert of een leeg voor voegsel opgeeft, is de regel van toepassing op alle blobs in het opslag account. | Nee |
| includeSnapshots    | Booleaans                         | Hiermee geeft u op of de inventaris moment opnamen moet bevatten. De standaard waarde is **False**. | Nee |
| includeBlobVersions | Booleaans                         | Hiermee geeft u op of de inventaris BLOB-versies moet bevatten. De standaard waarde is **False**. | Nee |

Bekijk de JSON voor inventarisatie regels door het tabblad **code weergave** te selecteren in het gedeelte **BLOB-inventaris** van het Azure Portal. Filters worden opgegeven in de definitie van een regel.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>Inventarisatie-uitvoer

Elke inventarisatie-uitvoering genereert een set met CSV-bestanden in de opgegeven inventarisatie doel container. De inventarisatie-uitvoer wordt gegenereerd onder het volgende pad: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` waar:

- *AccountName* is de naam van uw Azure Blob Storage-account
- *inventaris-doel-container* is de doel container die u hebt opgegeven in het inventarisatie beleid
- *Jjjj/mm/dd/uu-mm-SS* is de tijd waarop de inventarisatie is gestart

### <a name="inventory-files"></a>Inventarisatie bestanden

Bij elke inventarisatie worden de volgende bestanden gegenereerd:

- **Inventarisatie CSV-bestand**: een bestand met door komma's gescheiden waarden (CSV) voor elke inventaris regel. Elk bestand bevat overeenkomende objecten en de bijbehorende meta gegevens. De eerste rij in elk CSV-bestand is altijd de schemarij. In de volgende afbeelding ziet u een CSV-bestand van de inventaris geopend in micro soft Excel.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Scherm afbeelding van een inventarisatie CSV-bestand dat in micro soft Excel is geopend":::

- **Manifest bestand**: een manifest.jsin het bestand met de details van de inventarisatie bestanden die worden gegenereerd voor elke regel in die uitvoering. In het manifest bestand wordt ook de regel definitie vastgelegd die door de gebruiker is gemaakt en het pad naar de inventaris voor die regel.

- **Controlesom bestand**: een bestand manifest. checksum met de MD5-controlesom van de inhoud van manifest.jsin het bestand. Het genereren van het manifest. controlesom bestand markeert de voltooiing van een inventarisatie-uitvoering.

## <a name="inventory-completed-event"></a>Gebeurtenis inventarisatie voltooid

Abonneer u op de voltooide inventarisatie gebeurtenis om een melding te ontvangen wanneer de inventarisatie is voltooid. Deze gebeurtenis wordt gegenereerd wanneer het bestand met de manifest controlesom wordt gemaakt. De gebeurtenis inventarisatie voltooid vindt ook plaats als er een fout optreedt bij de inventarisatie van de gebruiker voordat deze wordt uitgevoerd. Als er bijvoorbeeld een fout is opgetreden in een ongeldig beleid of als de doel container niet aanwezig is, wordt de gebeurtenis geactiveerd. De gebeurtenis wordt gepubliceerd naar het onderwerp BLOB Inventory.

Voorbeeld gebeurtenis:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="next-steps"></a>Volgende stappen

- [Het aantal en de totale grootte van blobs per container berekenen](calculate-blob-count-size.md)
- [Azure Blob Storage levenscyclus beheren](storage-lifecycle-management-concepts.md)
