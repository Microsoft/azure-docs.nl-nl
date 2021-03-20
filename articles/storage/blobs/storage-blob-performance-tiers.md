---
title: Prestatie lagen voor Blob Storage blok keren, Azure Storage
description: In dit artikel wordt het verschil beschreven tussen de Premium-en Standard-prestatie lagen voor Azure Block Blob Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: cf0b5d29e0dc375a07fe024ef0763c8200275055
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880705"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Prestatielagen voor blok-blobopslag

Als ondernemingen prestaties gevoelige Cloud toepassingen implementeren, is het belang rijk dat u beschikt over opties voor rendabele gegevens opslag op verschillende prestatie niveaus.

Azure Block Blob Storage biedt twee verschillende prestatie lagen:

- **Premium**: geoptimaliseerd voor hoge transactie tarieven en een consistente opslag latentie van één cijfer
- **Standaard**: geoptimaliseerd voor hoge capaciteit en hoge door Voer

De volgende overwegingen zijn van toepassing op de verschillende prestatie lagen:

| Gebied |Standaard prestaties  |Premium-prestaties  |
|---------|---------|---------|
|Beschikbaarheid in regio’s     |   Alle regio's      | In [regio's selecteren](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Ondersteunde [typen opslag accounts](../common/storage-account-overview.md#types-of-storage-accounts)     |     Algemeen gebruik v2, BlobStorage, algemeen gebruik v1    |    BlockBlobStorage     |
|Ondersteunt [blok-blobs met hoge door Voer](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    Ja, met meer dan 4 MiB-PutBlock of PutBlob-grootten     |    Ja, groter dan 256 KiB PutBlock of PutBlob grootten    |
|Redundantie     |     [Typen opslag accounts](../common/storage-account-overview.md#types-of-storage-accounts) weer geven   |  Ondersteunt momenteel alleen lokaal redundante opslag (LRS) en zone-redudant Storage (ZRS)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> Zone-redundante opslag (ZRS) is beschikbaar in Selecteer regio's voor de accounts voor Premium-prestatie blok-Blob-opslag.</div>

Met betrekking tot kosten biedt Premium-prestaties geoptimaliseerde prijzen voor toepassingen met een hoge transactie snelheid om de [totale opslag kosten](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) voor deze werk belastingen te verlagen.

## <a name="premium-performance"></a>Premium-prestaties

Met de Premium-opslag voor blok-blobs kunt u gegevens beschikbaar maken via high performance-hardware. Gegevens worden opgeslagen op Ssd's (Solid-state drives) die zijn geoptimaliseerd voor een lage latentie. Ssd's bieden een hogere door Voer vergeleken met traditionele harde schijven.

Premium-prestatie opslag is ideaal voor werk belastingen waarvoor snelle en consistente reactie tijden zijn vereist. Het is het beste voor werk belastingen die veel kleine trans acties uitvoeren. Voor beelden van werk belastingen zijn:

- **Interactieve werk belastingen**. Voor deze werk belastingen zijn directe updates en gebruikers feedback vereist, zoals e-commerce en het toewijzen van toepassingen. Zo worden in een e-commerce-toepassing minder vaak bekeken items waarschijnlijk niet in de cache opgeslagen. Ze moeten echter direct worden weer gegeven aan de klant op aanvraag.

- **Analyse**. In een IoT-scenario kunnen veel kleinere schrijf bewerkingen elke seconde worden gepusht naar de Cloud. Grote hoeveel heden gegevens kunnen worden opgenomen in, geaggregeerd voor analyse doeleinden en bijna onmiddellijk worden verwijderd. De hoge opname mogelijkheden van de Premium-blok-blobopslag maken het efficiënt voor dit type werk belasting.

- **Kunst matige intelligentie/machine learning (AI/ml)**. AI/ML behandelt het gebruik en de verwerking van verschillende gegevens typen, zoals visuele elementen, spraak en tekst. Dit type werk belasting met hoge prestaties wordt behandeld met grote hoeveel heden gegevens waarvoor snelle reacties en efficiënte opname tijden voor gegevens analyse zijn vereist.

- **Gegevens transformatie**. Voor processen waarvoor het bewerken, aanpassen en converteren van gegevens constant is, zijn directe updates vereist. Voor een nauw keurige weer gave van gegevens moeten gebruikers van deze gegevens deze wijzigingen onmiddellijk weer geven.

## <a name="standard-performance"></a>Standaard prestaties

Standaard prestaties bieden ondersteuning voor verschillende [toegangs lagen](storage-blob-storage-tiers.md) om gegevens op de voordeligste manier op te slaan. Het is geoptimaliseerd voor hoge capaciteit en hoge door Voer voor grote gegevens sets.

- **Gegevens sets voor back-ups en herstel na nood gevallen**. De Standard-prestatie opslag biedt rendabele lagen, waardoor het een perfecte use-case is voor gegevens sets op de korte en lange termijn, secundaire back-ups en het archiveren van de compatibiliteit.

- **Media-inhoud**. Afbeeldingen en Video's worden vaak gebruikt wanneer u voor het eerst wordt gemaakt en opgeslagen, maar dit inhouds type wordt minder vaak toegepast wanneer het ouder is. Opslag voor standaard prestaties biedt geschikte lagen voor de behoeften van media-inhoud. 

- **Bulk gegevens verwerking**. Deze soorten workloads zijn geschikt voor standaard opslag, omdat ze rendabele opslag met hoge door Voer vereisen in plaats van een consistente lage latentie. Grote, onbewerkte gegevens sets worden klaargezet voor verwerking en uiteindelijk naar koele lagen gemigreerd.

## <a name="migrate-from-standard-to-premium"></a>Migreren van Standard naar Premium

U kunt een bestaand standaard prestatie opslag account niet converteren naar een blok-Blob-opslag account met Premium-prestaties. Als u wilt migreren naar een Premium-prestatie opslag account, moet u een BlockBlobStorage-account maken en de gegevens naar het nieuwe account migreren. Zie [een BlockBlobStorage-account maken](storage-blob-create-account-block-blob.md)voor meer informatie.

U kunt de meest recente versie van het [AzCopy](../common/storage-use-azcopy-v10.md#transfer-data) -opdracht regel programma gebruiken om blobs tussen opslag accounts te kopiëren. Andere hulpprogram ma's, zoals Azure Data Factory, zijn ook beschikbaar voor gegevens verplaatsing en-trans formatie.

## <a name="blob-lifecycle-management"></a>Beheer van de BLOB levenscyclus

Levenscyclus beheer van Blob-opslag biedt een uitgebreid beleid op basis van regels:

- **Premium**: verlopen gegevens aan het einde van de levens cyclus.
- **Standard**: overgangs gegevens naar de beste Access-laag en verlopen gegevens aan het einde van de levens cyclus.

Zie [de Azure Blob-opslag levenscyclus beheren](storage-lifecycle-management-concepts.md)voor meer informatie.

Het is niet mogelijk om gegevens die zijn opgeslagen in een Premium Block Blob-opslag account te verplaatsen tussen de lagen hot, cool en Archive. U kunt echter blobs kopiëren van een blok-Blob-opslag account naar de laag Hot Access in een *ander* account. Als u gegevens wilt kopiëren naar een ander account, gebruikt u het [put-blok van URL](/rest/api/storageservices/put-block-from-url) API of [AzCopy V10 toevoegen](../common/storage-use-azcopy-v10.md). **Met de put-blok kering van URL** -API worden gegevens synchroon gekopieerd op de server. De oproep wordt pas voltooid nadat alle gegevens zijn verplaatst van de oorspronkelijke server locatie naar de doel locatie.

## <a name="next-steps"></a>Volgende stappen

Evalueer warme, cool en Archive in GPv2-en Blob Storage-accounts.

- [Meer informatie over reactiveren BLOB-gegevens uit de laag archief](storage-blob-rehydration.md)
- [Gebruik van de huidige opslagaccounts evalueren door metrische gegevens voor Azure Storage in te schakelen](./monitor-blob-storage.md)
- [Controleer de prijzen voor warme, koude en archief in Blob Storage en GPv2-accounts per regio](https://azure.microsoft.com/pricing/details/storage/)
- [Prijzen voor gegevensoverdracht controleren](https://azure.microsoft.com/pricing/details/data-transfers/)
