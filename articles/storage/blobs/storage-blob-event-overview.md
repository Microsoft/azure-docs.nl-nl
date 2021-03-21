---
title: Reageren op Azure Blob Storage-gebeurtenissen | Microsoft Docs
description: Gebruik Azure Event Grid om u te abonneren op Blob Storage-gebeurtenissen en hierop te reageren. Meer informatie over het gebeurtenis model, het filteren van gebeurtenissen en procedures voor het gebruiken van gebeurtenissen.
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: dineshm
ms.openlocfilehash: f07c249e3b7cb54283959df410d51ca18998f2cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181513"
---
# <a name="reacting-to-blob-storage-events"></a>Reageren op gebeurtenissen van Blob Storage

Met Azure Storage gebeurtenissen kunnen toepassingen reageren op gebeurtenissen, zoals het maken en verwijderen van blobs. Dit gebeurt zonder dat er complexe code of dure en inefficiënte polling services nodig zijn. Het beste deel is dat u alleen betaalt voor wat u gebruikt.

Blob Storage-gebeurtenissen worden gepusht met behulp van [Azure Event grid](https://azure.microsoft.com/services/event-grid/) naar abonnees, zoals Azure Functions, Azure Logic apps of zelfs naar uw eigen HTTP-listener. Event Grid biedt betrouw bare levering van gebeurtenissen aan uw toepassingen via een uitgebreid beleid voor opnieuw proberen en onbestelbare berichten.

Zie het artikel over het [schema voor Blob Storage-gebeurtenissen](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) om de volledige lijst weer te geven van de gebeurtenissen die door Blob Storage worden ondersteund.

Veelvoorkomende gebeurtenissen in Blob Storage zijn onder andere afbeeldings-of video verwerking, zoek indexering of een op bestanden gebaseerde werk stroom. Het asynchroon uploaden van bestanden is een uitstekend geschikt voor gebeurtenissen. Als de wijzigingen niet worden doorgevoerd, maar voor uw scenario een onmiddellijke reactie nodig is, kan de architectuur op basis van gebeurtenissen bijzonder efficiënt zijn.

Als u Blob Storage-gebeurtenissen wilt proberen, raadpleegt u een van de volgende Quick Start-artikelen:

|Als u dit hulp programma wilt gebruiken:    |Zie dit artikel: |
|--|-|
|Azure Portal    |[Quickstart: Blob Storage-gebeurtenissen routeren naar een webeindpunt met Azure Portal](../../event-grid/blob-event-quickstart-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Snelstartgids: opslag gebeurtenissen naar een webeindpunt door sturen met Power shell](./storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Snelstartgids: opslag gebeurtenissen naar een webeindpunt door sturen met Azure CLI](./storage-blob-event-quickstart.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Zie deze artikelen voor meer gedetailleerde voor beelden van het reageren op Blob Storage-gebeurtenissen met behulp van Azure functions:

- [Zelf studie: een Databricks Delta tabel bijwerken met behulp van Azure data Lake Storage Gen2-gebeurtenissen](data-lake-storage-events.md).
- [Zelfstudie: Formaat van geüploade afbeeldingen automatisch wijzigen met Event Grid](../../event-grid/resize-images-on-storage-blob-upload-event.md?tabs=dotnet)

>[!NOTE]
> Alleen opslag accounts van het type **StorageV2 (algemeen gebruik v2)**, **BlockBlobStorage** en **BlobStorage** ondersteunings gebeurtenis integratie. **Storage (algemeen gebruik v1)** biedt *geen* ondersteuning voor integratie met Event grid.

## <a name="the-event-model"></a>Het gebeurtenis model

Event Grid gebruikt [gebeurtenis abonnementen](../../event-grid/concepts.md#event-subscriptions) om gebeurtenis berichten te routeren naar abonnees. In deze afbeelding ziet u de relatie tussen gebeurtenis uitgevers, gebeurtenis abonnementen en gebeurtenis-handlers.

![Event Grid model](./media/storage-blob-event-overview/event-grid-functional-model.png)

Abonneer u eerst op een eind punt voor een gebeurtenis. Wanneer een gebeurtenis vervolgens wordt geactiveerd, verzendt de Event Grid-Service gegevens over die gebeurtenis naar het eind punt.

Zie het artikel over het [schema voor Blob Storage-gebeurtenissen](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor het weer geven van:

> [!div class="checklist"]
> * Een volledige lijst met Blob Storage-gebeurtenissen en hoe elke gebeurtenis wordt geactiveerd.
> * Een voor beeld van de gegevens die het Event Grid verzendt voor elk van deze gebeurtenissen.
> * Het doel van elk sleutel waarde-paar dat wordt weer gegeven in de gegevens.

## <a name="filtering-events"></a>Gebeurtenissen filteren

BLOB- [gebeurtenissen kunnen worden gefilterd](/cli/azure/eventgrid/event-subscription) op basis van het gebeurtenis type, de naam van de container of de naam van het object dat is gemaakt/verwijderd. Filters in Event Grid komen overeen met het begin of het einde van het onderwerp, zodat gebeurtenissen met een overeenkomend onderwerp naar de abonnee gaan.

Zie [gebeurtenissen filteren voor Event grid voor](../../event-grid/how-to-filter-events.md)meer informatie over het Toep assen van filters.

Het onderwerp van Blob Storage-gebeurtenissen maakt gebruik van de volgende indeling:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Als u alle gebeurtenissen voor een opslag account wilt vergelijken, kunt u de onderwerps filters leeg laten.

Als u gebeurtenissen wilt vergelijken van blobs die zijn gemaakt in een set containers die een voor voegsel delen, gebruikt u een `subjectBeginsWith` filter zoals:

```
/blobServices/default/containers/containerprefix
```

Als u gebeurtenissen wilt vergelijken van blobs die zijn gemaakt in een specifieke container, gebruikt u een `subjectBeginsWith` filter zoals:

```
/blobServices/default/containers/containername/
```

Als u gebeurtenissen wilt vergelijken van blobs die zijn gemaakt in een specifieke container voor het delen van een BLOB-naam, gebruikt u een `subjectBeginsWith` filter zoals:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Als u gebeurtenissen wilt vergelijken van blobs die zijn gemaakt in een specifieke container met een BLOB-achtervoegsel, gebruikt u een `subjectEndsWith` filter zoals ". log" of ". jpg". Zie [Event grid-concepten](../../event-grid/concepts.md#event-subscriptions)voor meer informatie.

## <a name="practices-for-consuming-events"></a>Procedures voor het gebruiken van gebeurtenissen

Toepassingen die Blob Storage-gebeurtenissen verwerken, moeten een aantal aanbevolen procedures volgen:
> [!div class="checklist"]
> * Omdat meerdere abonnementen kunnen worden geconfigureerd voor het routeren van gebeurtenissen naar dezelfde gebeurtenis-handler, is het belang rijk om te voor komen dat gebeurtenissen afkomstig zijn uit een bepaalde bron, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van het opslag account dat u verwacht.
> * Controleer ook of de Event type is ingesteld als een voor bereiding op het proces en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de verwachte typen zijn.
> * Wanneer berichten na enige vertraging kunnen arriveren, gebruikt u de ETAG-velden om te begrijpen of uw informatie over objecten nog steeds actueel is. Zie voor meer informatie over het gebruik van het ETAG-veld [gelijktijdigheid beheren in Blob Storage](./concurrency-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage).
> * Wanneer berichten niet in de juiste volg orde arriveren, kunt u de sequencer-velden gebruiken om de volg orde van gebeurtenissen op een bepaald object te begrijpen. Het veld sequencer is een teken reeks waarde die de logische volg orde van gebeurtenissen voor een bepaalde BLOB-naam vertegenwoordigt. U kunt standaard teken reeks vergelijking gebruiken om inzicht te krijgen in de relatieve volg orde van twee gebeurtenissen op dezelfde blobnaam.
> * Opslag gebeurtenissen garanderen mini maal één keer aan abonnees, wat ervoor zorgt dat alle berichten worden gegenereerd. Als gevolg van nieuwe pogingen tussen back-end-knoop punten en-services of Beschik baarheid van abonnementen, kunnen er echter dubbele berichten optreden. Zie [Event grid aflevering van berichten en probeer](../../event-grid/delivery-and-retry.md)het opnieuw.
> * Gebruik het veld blobType om te begrijpen welk type bewerkingen op de BLOB zijn toegestaan en welke client bibliotheek typen u moet gebruiken voor toegang tot de blob. Geldige waarden zijn ofwel `BlockBlob` of `PageBlob` . 
> * Gebruik het URL-veld met `CloudBlockBlob` de `CloudAppendBlob` Constructors om toegang te krijgen tot de blob.
> * Velden negeren die u niet begrijpt. Met deze procedure kunt u de nieuwe functies die in de toekomst kunnen worden toegevoegd, flexibeler maken.
> * Als u ervoor wilt zorgen dat de gebeurtenis **micro soft. storage. BlobCreated** alleen wordt geactiveerd als een blok-BLOB volledig is doorgevoerd, filtert u de gebeurtenis voor de `CopyBlob` -, `PutBlob` - `PutBlockList` of `FlushWithClose` rest API-aanroepen. Met deze API-aanroepen wordt de gebeurtenis **micro soft. storage. BlobCreated** alleen geactiveerd wanneer gegevens volledig zijn doorgevoerd in een blok-blob. Zie [gebeurtenissen filteren voor Event grid voor](../../event-grid/how-to-filter-events.md)meer informatie over het maken van een filter.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over Event Grid en het geven van Blob Storage-gebeurtenissen een try:

- [Over Event Grid](../../event-grid/overview.md)
- [Schema voor Blob Storage-gebeurtenissen](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Blob Storage-gebeurtenissen naar een aangepast eind punt van een web routeren](storage-blob-event-quickstart.md)
