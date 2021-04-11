---
title: Lokalisatie van Azure Media Player
description: Ondersteuning voor meerdere talen voor gebruikers van niet-Engelse land instellingen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: e78ff237fb488a995d9161814fe61590fb1c6d5b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449834"
---
# <a name="azure-media-player-localization"></a>Lokalisatie van Azure Media Player #

Dankzij ondersteuning voor meerdere talen kunnen gebruikers van niet-Engelse land instellingen systeem eigen communiceren met de speler. Azure Media Player wordt geïnstantieerd met een algemene woorden lijst met talen, waarmee de fout berichten worden gelokaliseerd op basis van de pagina taal. Een ontwikkelaar kan ook een Player-exemplaar maken met een geforceerd ingestelde taal. De standaard taal is Engels (en).

> [!NOTE]
> Deze functie is nog steeds bezig met testen en is afhankelijk van fouten.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player ondersteunt momenteel de volgende talen met de bijbehorende taal codes:

| Taal            | Code | Taal                | Code   | Taal                | Code         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Engels {standaard}   | en   | Kroatisch                | uur     | Roemeens                | ro           |
| Arabisch              | ar   | Hongaars               | hu     | Slowaaks                  | sk           |
| Bulgaars           | bg   | Indonesisch              | id     | Slovene                 | sl           |
| Catalaans             | ca   | IJslands               | is     | Servisch - Cyrillisch      | SR-Cyrl-CS   |
| Tsjechisch               | cs   | Italiaans                 | it     | Servisch - Latijn         | sr-latn-rs   |
| Deens              | da   | Japans                | ja     | Russisch                 | ru           |
| Duits              | de   | Kazachs                  | kk     | Zweeds                 | sv           |
| Grieks               | el   | Koreaans                  | ko     | Thai                    | th           |
| Spaans             | es   | Litouws              | lt     | Tagalog                 | 't           |
| Ests            | et   | Lets                 | lv     | Turks                 | tr           |
| Baskisch              | eu   | Maleisische               | ms     | Oekraïens               | uk           |
| Iraans               | fa   | Noors-BokmÃ ¥ l     | nb     | Urdu                    | ur           |
| Fins             | fi   | Nederlands                   | nl     | Vietnamees              | vi           |
| Frans              | fr   | Noors-Nynorsk     | nn     | Chinees-vereenvoudigd    | zh-Hans      |
| Galicisch            | gl   | Pools                  | pl     | Chinees-traditioneel   | zh-hant      |
| Hebreeuws              | he   | Portuguese - Brazil     | pt-br  |                         |              |
| Hindi               | hi   | Portugees - Portugal   | pt-pt  |                         |              |


> [!NOTE]
> Als u niet wilt dat een lokalisatie plaatsvindt, moet u de taal afdwingen voor Engels

## <a name="next-steps"></a>Volgende stappen ##

- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)
