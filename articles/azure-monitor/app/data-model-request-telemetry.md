---
title: Gegevens model voor aanvraag-telemetrie-Azure-toepassing Insights
description: Application Insights gegevens model voor aanvraag-telemetrie
ms.topic: conceptual
ms.date: 01/07/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 7a352f4ce3528d395599a91b53031c74b0873152
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87320558"
---
# <a name="request-telemetry-application-insights-data-model"></a>Telemetrie aanvragen: Application Insights gegevens model

Een telemetrie van aanvragen (in [Application Insights](./app-insights-overview.md)) vertegenwoordigt de logische volg orde van uitvoering die door een externe aanvraag naar uw toepassing wordt geactiveerd. Elke aanvraag uitvoering wordt geïdentificeerd met unieke `ID` en `url` bevat alle uitvoerings parameters. U kunt aanvragen groeperen op logische `name` en de `source` van deze aanvraag definiëren. Uitvoering van code kan resulteren in `success` of `fail` en heeft een bepaalde `duration` . Geslaagde en mislukte uitvoeringen kunnen verder worden gegroepeerd door `resultCode` . Begin tijd voor de telemetrie van de aanvraag die is gedefinieerd op het envelop niveau.

Telemetrie aanvragen ondersteunt het model voor standaard uitbreid baarheid met behulp van aangepast `properties` en `measurements` .

## <a name="name"></a>Name

De naam van de aanvraag vertegenwoordigt het codepad dat is gemaakt om de aanvraag te verwerken. Lage kardinaliteit waarde om een betere groepering van aanvragen mogelijk te maken. Voor HTTP-aanvragen vertegenwoordigt het de sjabloon HTTP-methode en URL-pad, zoals `GET /values/{id}` zonder de werkelijke `id` waarde.

Application Insights Web-SDK verzendt de naam van de aanvraag ' in de staat ' met betrekking tot letters. Groeperen op de gebruikers interface is hoofdletter gevoelig, dus `GET /Home/Index` wordt afzonderlijk geteld, `GET /home/INDEX` zelfs als ze vaak resulteren in dezelfde controller en uitgevoerde actie. De reden hiervoor is dat url's in het algemeen [hoofdletter gevoelig](https://www.w3.org/TR/WD-html40-970708/htmlweb.html)zijn. U kunt bijvoorbeeld zien of alle `404` url's zijn getypt in hoofd letters. Meer informatie over de aanvraag naam verzameling door ASP.NET Web SDK vindt u in het [blog bericht](https://apmtips.com/posts/2015-02-23-request-name-and-url/).

Maximale lengte: 1024 tekens

## <a name="id"></a>Id

Id van een exemplaar van een aanvraag aanroep. Wordt gebruikt voor correlatie tussen aanvraag en andere telemetrie-items. De ID moet globaal uniek zijn. Zie [correlatie](./correlation.md) pagina voor meer informatie.

Maximale lengte: 128 tekens

## <a name="url"></a>URL

Aanvraag-URL met alle query reeks parameters.

Maximale lengte: 2048 tekens

## <a name="source"></a>Bron

De bron van de aanvraag. Voor beelden zijn de instrumentatie sleutel van de aanroeper of het IP-adres van de aanroeper. Zie [correlatie](./correlation.md) pagina voor meer informatie.

Maximale lengte: 1024 tekens

## <a name="duration"></a>Duur

De duur van aanvragen in de indeling: `DD.HH:MM:SS.MMMMMM` . Moet positief en kleiner dan `1000` dagen zijn. Dit veld is vereist omdat de aanvraag-telemetrie de bewerking met het begin en het einde vertegenwoordigt.

## <a name="response-code"></a>Antwoord code

Resultaat van het uitvoeren van een aanvraag. HTTP-status code voor HTTP-aanvragen. Dit kan een `HRESULT` waarde zijn of een uitzonderings type voor andere aanvraag typen.

Maximale lengte: 1024 tekens

## <a name="success"></a>Geslaagd

Indicatie van een geslaagde of mislukte aanroep. Dit veld is vereist. Wanneer niet expliciet wordt ingesteld op `false` -een aanvraag, wordt beschouwd als geslaagd. Stel deze waarde in op `false` als de bewerking is onderbroken door een uitzonde ring of geretourneerde fout resultaat code.

Voor de webtoepassingen Application Insights een aanvraag definiëren als geslaagd wanneer de respons code kleiner is dan `400` of gelijk is aan `401` . Er zijn echter gevallen waarin deze standaard toewijzing niet overeenkomt met de semantiek van de toepassing. De antwoord code `404` kan duiden op "no records", die deel kunnen uitmaken van de normale stroom. Dit kan ook duiden op een verbroken koppeling. Voor de verbroken koppelingen kunt u zelfs meer geavanceerde logica implementeren. U kunt verbroken koppelingen alleen als fouten markeren als deze koppelingen zich op dezelfde site bevinden door het analyseren van URL-verwijzings bronnen. Of markeer ze als storingen wanneer ze worden geopend vanuit de mobiele toepassing van het bedrijf. `301`En `302` duidt op fout bij het openen van de client die geen ondersteuning biedt voor omleiden.

Gedeeltelijk geaccepteerde inhoud `206` kan duiden op een fout in een algemene aanvraag. Application Insights eind punt ontvangt bijvoorbeeld een batch met telemetrie-items als één aanvraag. Er wordt een resultaat weer gegeven `206` wanneer sommige items in de batch niet zijn verwerkt. Een hogere frequentie `206` geeft aan dat er een probleem is dat moet worden onderzocht. Een soort gelijke logica is van toepassing op `207` meerdere statussen, waarbij het resultaat mogelijk het ergste aantal afzonderlijke respons codes kan zijn.

Meer informatie over de aanvraag resultaat code en status code vindt u in het [blog bericht](https://apmtips.com/posts/2016-12-03-request-success-and-response-code/).

## <a name="custom-properties"></a>Aangepaste eigenschappen

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Aangepaste metingen

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Volgende stappen

- [Telemetrie van aangepaste aanvraag schrijven](./api-custom-events-metrics.md#trackrequest)
- Zie [gegevens model](data-model.md) voor Application Insights typen en gegevens model.
- Meer informatie over het [configureren van ASP.net core](./asp-net.md) toepassing met Application Insights.
- Bekijk de [platforms](./platforms.md) die door Application Insights worden ondersteund.

