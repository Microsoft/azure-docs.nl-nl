---
title: Kies de juiste prijs categorie voor Microsoft Azure kaarten
description: Meer informatie over Azure Maps prijs categorieën. Bekijk welke functies worden aangeboden in welke lagen en Bekijk de belangrijkste aandachtspunten voor het kiezen van een prijs categorie.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cb57e424642141e73588a61d026d1ff2f1a8b096
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905295"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>De juiste prijscategorie kiezen in Azure Maps

Azure Maps biedt twee prijs Categorieën: S0 en S1. Het doel van dit artikel is om u te helpen bij het kiezen van de juiste prijs categorie voor uw behoeften. Als u de juiste prijs categorie wilt kiezen, kunt u de volgende twee vragen stellen.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>Hoeveel gelijktijdige gebruikers wilt u ondersteunen?

De prijs categorieën S0 en S1 verwerken verschillende hoeveel heden gegevens doorvoer. De prijs categorie S0 verwerkt Maxi maal **50 query's per seconde**. Terwijl de S1-laag **meer dan 50 query's per seconde** verwerkt.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>Welke georuimtelijke mogelijkheden moet ik gebruiken?

Als de belangrijkste georuimtelijke Api's voldoen aan uw service vereisten, kiest u de prijs categorie s0. Als u meer geavanceerde mogelijkheden voor uw toepassing wilt, kunt u overwegen om de prijs categorie S1 te kiezen. Geavanceerde mogelijkheden zijn: lucht foto plus hybride afbeelding, route bereik ophalen en batch geocodering. Als u de prijs categorie wilt selecteren die het meest geschikt is voor uw toepassing, raadpleegt u de tabel met **mogelijkheden voor prijs categorie** hieronder:

### <a name="pricing-tier-capabilities"></a>Mogelijkheden voor prijs Categorieën

| Mogelijkheid                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| Kaart weergave                              | ✓                   | ✓       |
| Satelliet afbeelding                       |                     | ✓        |
| Zoeken                                  | ✓                    | ✓        |
| Zoeken in batch                            |                     | ✓        |
| Route                                   | ✓                    |✓        |
| Batch routering                            |                    | ✓        |
| Matrix routering                          |                     | ✓        |
| Route bereik (van isochronen)                |                     | ✓        |
| Verkeer                                |✓                    |✓        |
| Tijdzone                               |✓                    |✓        |
| Geolocatie (preview-versie)                    |✓                   |✓        |
| Ruimtelijke bewerkingen                        |                    |✓        |
| Geofencing                                |                    |✓        |
| Azure Maps gegevens (preview-versie)                |                     | ✓        |
| Mobiliteit (preview-versie)                       |                     | ✓        |
| Weer (preview-versie)                        |✓                    |✓        |
|  Maker (preview-versie)                         |                   |✓        |
|  Uitbrei ding (preview-versie)                        |                   |✓        |

Houd rekening met de volgende extra punten:

* Welk type onderneming hebt u?
* Hoe kritiek is uw toepassing?

### <a name="pricing-tier-targeted-customers"></a>Prijs categorie gericht op klanten

Zie de tabel met de **doel klanten van de prijs categorie** voor een beter beeld van de prijs categorieën S0 en S1. Zie [Azure Maps prijzen](https://azure.microsoft.com/pricing/details/azure-maps/)voor meer informatie. 

| Prijscategorie  |     Doel klanten                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    De prijs categorie S0 werkt voor toepassingen in alle stadia van productie: van ontwikkeling van concepten en vroege fase tests op toepassings productie en-implementatie. Deze laag is echter ontworpen voor kleinschalige ontwikkeling, of klanten met weinig gelijktijdige gebruikers, of beide. 
| S1            |    De prijs categorie S1 is voor klanten met grootschalige bedrijfs toepassingen, essentiële toepassingen of hoge volumes van gelijktijdige gebruikers. Het is ook bedoeld voor klanten die geavanceerde georuimtelijke services nodig hebben.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het weer geven en wijzigen van prijs Categorieën:

> [!div class="nextstepaction"]
> [Een prijs categorie beheren](how-to-manage-pricing-tier.md)
