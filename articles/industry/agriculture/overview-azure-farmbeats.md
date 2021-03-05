---
title: Wat is Azure FarmBeats?
description: Biedt een overzicht van Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: f173dfb055f3d9c75ee63570cd413276ffb6ba37
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102182822"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Overzicht van Azure Notebooks (preview)

Azure FarmBeats is een Business-to-business-aanbieding die beschikbaar is in Azure Marketplace. Hiermee kunnen agrarische gegevenssets vanuit verschillende providers worden verzameld. Met Azure FarmBeats kunt u AI-modellen (AI: kunstmatige intelligentie) of ML-modellen (ML: machine learning) maken op basis van samengevoegde gegevenssets. Door gebruik te maken van Azure FarmBeats kunnen de landbouwbedrijven zich richten op de kernzaken die van toegevoegde waarde zijn in plaats van de niet-gedifferentieerde zware inzet op data engineering.

> [!NOTE]
> Azure FarmBeats is momenteel beschikbaar als openbare preview-versie. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. Azure FarmBeats wordt geleverd zonder Service Level Agreement. Gebruik het [ondersteuningsforum voor Azure FarmBeats](/answers/topics/azure-farmbeats.html) voor ondersteuning.

![Een Farm Beats-project](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Met de preview-versie van Azure FarmBeats kunt u het volgende doen:

- De status van het landbouwbedrijf beoordelen met behulp van de vegetatie-index en de waterindex op basis van satellietbeelden.
- Aanbevelingen krijgen over het aantal sensoren voor bodemvochtigheid dat moet worden gebruikt en waar ze moeten worden geplaatst.
- De situatie op het landbouwbedrijf bijhouden door bodemgegevens te visualiseren die door sensoren van verschillende leveranciers zijn verzameld.
- Een kaart voor bodemvochtigheid verkrijgen op basis van het samenvoegen van satelliet- en sensorgegevens.
- Praktische inzichten verkrijgen door AI/ML-modellen boven op geaggregeerde gegevenssets te compileren.
- Uw digitale landbouwoplossing compileren of uitbreiden door adviezen over de status van het landbouwbedrijf te bieden.

## <a name="datahub"></a>Datahub

De Azure FarmBeats Datahub is een API-laag waarmee u diverse landbouwgegevenssets van verschillende providers kunt verzamelen, normaliseren en contextualiseren. U kunt Azure FarmBeats gebruiken om het volgende te krijgen:
- **Sensorgegevens** van drie sensorproviders [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl Instruments](https://metos.at/)
- **Satellietbeelden** van de satellietmissie [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) van het Europees Ruimteagentschap
- **Dronebeelden** van drie dronebeeldenproviders [senseFly](https://www.sensefly.com/), [SlantRange](https://slantrange.com/), [DJI](https://dji.com/)

Datahub is ontworpen als een uitbreidbaar API-platform. We werken met veel meer providers die we willen integreren met Azure FarmBeats, zodat u meer keuze hebt tijdens het compileren van uw oplossing.

## <a name="accelerator"></a>Accelerator

De Azure FarmBeats Accelerator is een voorbeeldwebtoepassing, die is gecompileerd boven op Datahub. Met de Accelerator kunt u snel aan de slag met uw gebruikersinterface en de ontwikkeling van modellen. De Azure FarmBeats Accelerator maakt gebruik van Azure FarmBeats-API's. Hiermee worden opgenomen sensorgegevens gevisualiseerd als grafieken en modelresultaten als kaarten. U kunt met de Accelerator bijvoorbeeld snel een landbouwbedrijf maken en eenvoudig een kaart met de vegetatie-index of een kaart met sensorlocaties voor dit landbouwbedrijf verkrijgen.

## <a name="azure-role-based-access-control-azure-rbac"></a>Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)

Een beheerder kan toegangsregels voor Azure FarmBeats definiëren met behulp van een van de vooraf gedefinieerde rollen. Rollen bepalen tot welke gebieden van de toepassing gebruikers toegang hebben en welke acties ze kunnen uitvoeren. Er zijn twee soorten rollen in Azure FarmBeats: voor gebruikers en voor partners.

### <a name="user-roles"></a>Gebruikersrollen

Een [beheerder kan gebruikers toevoegen en beheren](manage-users-in-azure-farmbeats.md) en hun toegangsniveaus definiëren op basis van twee gebruikersrollen: Beheerder en Alleen-lezen.

### <a name="partner-roles"></a>Partnerrollen

Een beheerder kan meerdere partners als gegevensprovider aan Azure FarmBeats toevoegen. Hieronder vindt u een overzicht van de beschikbare partnerrollen in FarmBeats en de bijbehorende machtigingen:

| Partnertype    |   Acties  | Bereik |
| ---- | -------- | -------- |
| Sensorpartner  |   Maken, Lezen, Bijwerken <br/> <br/> Lezen, Bijwerken | DeviceModel, Device, SensorModel, Sensor <br/> <br/> ExtendedType |
| Partner voor beeldmateriaal  |   Maken, Lezen, Bijwerken <br/> <br/> Lezen, Bijwerken <br/> <br/> Lezen | Scene, SceneFile <br/> <br/> ExtendedType <br/> <br/> Landbouwbedrijf |
| Partner voor weerberichten* <br/> <br/>  (* binnenkort beschikbaar) |   Maken, Lezen, Bijwerken <br/> <br/> Lezen, Bijwerken <br/> <br/> Lezen | WeatherDataModel, WeatherDataLocation, JobType <br/> <br/> ExtendedType <br/> <br/> Landbouwbedrijf |

## <a name="resources"></a>Resources

Azure FarmBeats wordt zonder extra kosten aangeboden. U betaalt alleen voor de Azure-resources die u gebruikt. U kunt de onderstaande resources gebruiken voor meer informatie over de aanbieding:

- Blijf op de hoogte van het laatste nieuws over Azure FarmBeats door onze [Azure FarmBeats-blog](https://aka.ms/farmbeatsblog) te bezoeken.
- U kunt om assistentie vragen door een vraag te plaatsen op het [ondersteuningsforum voor Azure FarmBeats](/answers/topics/azure-farmbeats.html).
- U kunt feedback geven door een bericht te plaatsen over of te stemmen op een idee voor een functie op het [Azure FarmBeats-feedbackforum](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure FarmBeats installeren](install-azure-farmbeats.md)