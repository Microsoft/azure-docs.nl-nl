---
title: Een taal model aanpassen met Video Indexer-API
titlesuffix: Azure Media Services
description: Meer informatie over het aanpassen van een taal model met de Video Indexer-API.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: kumud
ms.openlocfilehash: f373afae03357ffb65eb459f806fe441e29b21b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047089"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Een taal model aanpassen met de Video Indexer-API

Met Video Indexer kunt u aangepaste taal modellen maken om spraak herkenning aan te passen door de aanpassings tekst te uploaden, namelijk de tekst van het domein waarvan u wilt dat de engine aan de woorden lijst voldoet. Zodra u het model hebt getraind, worden nieuwe woorden in de aanpassings tekst herkend.

Zie [een taal model aanpassen met video indexer](customize-language-model-overview.md)voor een gedetailleerd overzicht en aanbevolen procedures voor aangepaste taal modellen.

U kunt de Video Indexer-Api's gebruiken om aangepaste taal modellen in uw account te maken en te bewerken, zoals wordt beschreven in dit onderwerp. U kunt ook de website gebruiken, zoals beschreven in [taal model aanpassen met behulp van de video indexer-website](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Een taal model maken

Met de API [Create a language model](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) maakt u een nieuw aangepast taal model in het opgegeven account. U kunt bestanden voor het taal model uploaden in deze aanroep. U kunt ook het taal model hier maken en de bestanden voor het model later uploaden door het taal model bij te werken.

> [!NOTE]
> U moet het model nog steeds trainen met de ingeschakelde bestanden voor het model om de inhoud van de bestanden te kunnen zien. Instructies voor het trainen van een taal vindt u in de volgende sectie.

Als u bestanden wilt uploaden die moeten worden toegevoegd aan het taal model, moet u bestanden in de hoofd tekst uploaden met behulp van FormData, naast de waarden voor de vereiste para meters. Er zijn twee manieren om deze taak uit te voeren:

* De sleutel wordt de bestands naam en de waarde is het txt-bestand.
* De sleutel wordt de bestands naam en de waarde is een URL naar een txt-bestand.

### <a name="response"></a>Antwoord

Het antwoord geeft de meta gegevens van het zojuist gemaakte taal model samen met de meta gegevens van elk van de bestanden van het model volgens de indeling van dit voor beeld JSON-uitvoer:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Een taal model trainen

De [trein een taal model-API traint](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) een aangepast taal model in het opgegeven account met de inhoud van de bestanden die zijn geüpload naar en ingeschakeld in het taal model.

> [!NOTE]
> U moet eerst het taal model maken en de bestanden uploaden. U kunt bestanden uploaden bij het maken van het taal model of door het taal model bij te werken.

### <a name="response"></a>Antwoord

Het antwoord geeft de meta gegevens van het zojuist getrainde taal model samen met de meta gegevens van elk van de bestanden van het model volgens de indeling van dit voor beeld JSON-uitvoer:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

De geretourneerde `id` is een unieke id die wordt gebruikt om onderscheid te maken tussen taal modellen en `languageModelId` wordt gebruikt voor het [uploaden van een video om](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) een video-api's te indexeren en opnieuw te [indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) (ook wel bekend als `linguisticModelId` in video indexer-api's voor uploaden/opnieuw indexeren).

## <a name="delete-a-language-model"></a>Een taal model verwijderen

Met de API voor het [verwijderen van een taal model](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) wordt een aangepast taal model uit het opgegeven account verwijderd. Alle Video's die het verwijderde taal model gebruiken, blijven dezelfde index totdat u de video opnieuw indexeert. Als u de video opnieuw indexeert, kunt u een nieuw taal model toewijzen aan de video. Anders wordt het standaard model van Video Indexer gebruikt voor het opnieuw indexeren van de video.

### <a name="response"></a>Antwoord

Er is geen inhoud geretourneerd wanneer het taal model is verwijderd.

## <a name="update-a-language-model"></a>Een taal model bijwerken

Met de [Update een taal model](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) -API wordt een aangepast persoonlijk taal model in het opgegeven account bijgewerkt.

> [!NOTE]
> U moet het taal model al hebben gemaakt. U kunt deze aanroep gebruiken om alle bestanden onder het model in of uit te scha kelen, de naam van het taal model bij te werken en bestanden te uploaden die moeten worden toegevoegd aan het taal model.

Als u bestanden wilt uploaden die moeten worden toegevoegd aan het taal model, moet u bestanden in de hoofd tekst uploaden met behulp van FormData, naast de waarden voor de vereiste para meters. Er zijn twee manieren om deze taak uit te voeren:

* De sleutel wordt de bestands naam en de waarde is het txt-bestand.
* De sleutel wordt de bestands naam en de waarde is een URL naar een txt-bestand.

### <a name="response"></a>Antwoord

Het antwoord geeft de meta gegevens van het zojuist getrainde taal model samen met de meta gegevens van elk van de bestanden van het model volgens de indeling van dit voor beeld JSON-uitvoer:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Gebruik de `id` van de bestanden die zijn geretourneerd in het antwoord op het downloaden van de inhoud van het bestand.

## <a name="update-a-file-from-a-language-model"></a>Een bestand bijwerken vanuit een taal model

Met de [Update van een bestand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) kunt u de naam en `enable` status van een bestand in een aangepast taal model in het opgegeven account bijwerken.

### <a name="response"></a>Antwoord

Het antwoord bevat meta gegevens voor het bestand dat u hebt bijgewerkt, volgens de indeling van de voor beeld-JSON-uitvoer hieronder.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Gebruik het `id` van het bestand dat is geretourneerd in het antwoord op het downloaden van de inhoud van het bestand.

## <a name="get-a-specific-language-model"></a>Een specifiek taal model ophalen

De [Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) -API retourneert informatie over het opgegeven taal model in het opgegeven account, zoals taal en de bestanden in het taal model.

### <a name="response"></a>Antwoord

Het antwoord geeft de meta gegevens van het opgegeven taal model samen met de meta gegevens van elk van de bestanden van het model volgens de indeling van dit voor beeld JSON-uitvoer:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Gebruik het `id` van het bestand dat is geretourneerd in het antwoord op het downloaden van de inhoud van het bestand.

## <a name="get-all-the-language-models"></a>Alle taal modellen ophalen

De [Alles ophalen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) -API retourneert alle aangepaste taal modellen in het opgegeven account in een lijst.

### <a name="response"></a>Antwoord

Het antwoord bevat een lijst met alle taal modellen in uw account en elk van de meta gegevens en bestanden volgens de indeling van dit voor beeld JSON-uitvoer:

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Een bestand verwijderen uit een taal model

Met de API [verwijderen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) wordt het opgegeven bestand uit het opgegeven taal model in het opgegeven account verwijderd.

### <a name="response"></a>Antwoord

Er is geen inhoud die wordt geretourneerd wanneer het bestand wordt verwijderd uit het taal model.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Meta gegevens ophalen voor een bestand van een taal model

De [meta gegevens van een bestands](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) -API ophalen retourneert de inhoud van en meta gegevens van het opgegeven bestand uit het gekozen taal model in uw account.

### <a name="response"></a>Antwoord

Het antwoord bevat de inhoud en meta gegevens van het bestand in de JSON-indeling, zoals in dit voor beeld:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> De inhoud van dit voorbeeld bestand is de woorden ' Hello ' en ' World ' in twee afzonderlijke regels.

## <a name="download-a-file-from-a-language-model"></a>Een bestand downloaden van een taal model

Het [downloaden van een bestand](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) -API downloadt een tekst bestand met de inhoud van het opgegeven bestand uit het opgegeven taal model in het opgegeven account. Dit tekst bestand moet overeenkomen met de inhoud van het tekst bestand dat oorspronkelijk is geüpload.

### <a name="response"></a>Antwoord

Het antwoord is het downloaden van een tekst bestand met de inhoud van het bestand in de JSON-indeling.

## <a name="next-steps"></a>Volgende stappen

[Taal model aanpassen met website](customize-language-model-with-website.md)
