---
title: Aangepaste SDK's maken met AutoRest
titleSuffix: Azure Digital Twins
description: Meer informatie over het gebruik van AutoRest voor het genereren van SDK's in aangepaste talen voor het schrijven van Azure Digital Twins-code in andere talen waarop geen SDK's zijn gepubliceerd.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 4e91bf5acc5290229afa8dc7a849e8953257bcfd
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751108"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Aangepaste SDK's voor Azure Digital Twins maken met AutoRest

Als u met Azure Digital Twins wilt werken met een taal die geen [gepubliceerde Azure Digital Twins SDK](how-to-use-apis-sdks.md)heeft, wordt in dit artikel beschreven hoe u AutoRest gebruikt om uw eigen SDK te genereren in de taal van uw keuze. 

In de voorbeelden in dit artikel wordt het maken van een [gegevensvlak-SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)beschreven, maar dit proces werkt ook voor het genereren van een [besturingsvlak-SDK.](how-to-use-apis-sdks.md#overview-control-plane-apis)

## <a name="prerequisites"></a>Vereisten

Als u een SDK wilt genereren, moet u eerst de volgende installatie op uw lokale computer voltooien:
* Installeer [**AutoRest,**](https://github.com/Azure/autorest)versie 2.0.4413 (versie 3 wordt momenteel niet ondersteund)
* Installeer [**Node.js.**](https://nodejs.org)Dit is een vereiste voor het gebruik van AutoRest
* Installatie [ **Visual Studio**](https://visualstudio.microsoft.com/downloads/)
* Download het meest recente Azure Digital Twins **Swagger-bestand** (OpenAPI) van de [Swagger-map](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)van het gegevensvlak, samen met de bijbehorende map met voorbeelden. Het Swagger-bestand heeft de naam *digitaltwins.jsop*.

>[!TIP]
> Als u in plaats daarvan een **besturingsvlak-SDK** wilt maken, voltooit u de stappen in dit artikel met behulp van het meest recente **Swagger-bestand** (OpenAPI) voor het besturingsvlak vanuit de [Swagger-map](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) van het besturingsvlak in plaats van het gegevensvlak.

Zodra uw computer is uitgerust met alles uit de bovenstaande lijst, kunt u AutoRest gebruiken om een SDK te maken.

## <a name="create-the-sdk-using-autorest"></a>De SDK maken met AutoRest 

Nadat u Node.js geïnstalleerd, kunt u deze opdracht uitvoeren om ervoor te zorgen dat de vereiste versie van AutoRest is geïnstalleerd:
```cmd/sh
npm install -g autorest@2.0.4413
```

Voer de volgende stappen uit om AutoRest Azure Digital Twins Swagger-bestand uit te voeren:
1. Kopieer het Azure Digital Twins Swagger-bestand en de bijbehorende map met voorbeelden naar een werkmap.
2. Gebruik een opdrachtpromptvenster om over te schakelen naar die werkmap.
3. Voer AutoRest uit met de volgende opdracht. Vervang de `<language>` tijdelijke aanduiding door de taal van uw `python` `java` keuze: , , , , en `go` meer. (U vindt de volledige lijst met opties in [de AutoRest README](https://github.com/Azure/autorest).)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

Als gevolg hiervan ziet u een nieuwe map met de *naam DigitalTwinsApi* in uw werkmap. De gegenereerde SDK-bestanden hebben de naamruimte *DigitalTwinsApi.* U blijft die naamruimte gebruiken via de rest van de gebruiksvoorbeelden in dit artikel.

AutoRest ondersteunt een breed scala aan taalcodegeneratoren.

## <a name="make-the-sdk-into-a-class-library"></a>De SDK in een klassebibliotheek maken

U kunt de bestanden die door AutoRest worden gegenereerd, rechtstreeks opnemen in een .NET-oplossing. Het is echter waarschijnlijk dat u de Azure Digital Twins SDK wilt opnemen in verschillende afzonderlijke projecten (uw client-apps, Azure Functions-apps en meer). Daarom kan het handig zijn om een afzonderlijk project (een .NET-klassebibliotheek) te bouwen op basis van de gegenereerde bestanden. Vervolgens kunt u dit klassebibliotheekproject als projectverwijzing opnemen in verschillende oplossingen.

In deze sectie vindt u instructies voor het bouwen van de SDK als een klassebibliotheek. Dit is een eigen project en kan worden opgenomen in andere projecten. Deze stappen zijn afhankelijk **van Visual Studio**.

Dit zijn de stappen:

1. Een nieuwe Visual Studio voor een klassebibliotheek maken
2. *DigitalTwinsApi gebruiken* als projectnaam
3. Selecteer Solution Explorer het *DigitalTwinsApi-project* van de gegenereerde oplossing en kies *Toevoegen > bestaand item...*
4. Zoek de map waar u de SDK hebt gegenereerd en selecteer de bestanden op hoofdniveau
5. Druk op OK
6. Voeg een map toe aan het project (selecteer het project met de Solution Explorer en kies *Toevoegen > nieuwe map*)
7. De map Modellen een naam *geven*
8. Selecteer met de rechterkant *de map* Modellen in Solution Explorer selecteer Toevoegen > *bestaand item...*
9. Selecteer de bestanden in de *map Modellen* van de gegenereerde SDK en druk op OK

Als u de SDK wilt bouwen, hebt u voor uw project de volgende verwijzingen nodig:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Open Tools > *NuGet Pakketbeheer > Manage NuGet Packages for Solution...* om deze toe te voegen.

1. Zorg ervoor dat in het deelvenster het *tabblad Bladeren* is geselecteerd
2. Zoeken naar *Microsoft.Rest*
3. Selecteer de `ClientRuntime` pakketten en en voeg deze toe aan uw `ClientRuntime.Azure` oplossing

U kunt het project nu bouwen en als een projectverwijzing opnemen in elke toepassing Azure Digital Twins u schrijft.

## <a name="tips-for-using-the-sdk"></a>Tips voor het gebruik van de SDK

Deze sectie bevat algemene informatie en richtlijnen voor het gebruik van de gegenereerde SDK.

### <a name="synchronous-and-asynchronous-calls"></a>Synchrone en asynchrone aanroepen

Alle SDK-functies zijn beschikbaar in synchrone en asynchrone versies.

### <a name="typed-and-untyped-data"></a>Getypte en niet-getypte gegevens

REST API-aanroepen retourneren doorgaans sterk getypeerd objecten. Omdat gebruikers Azure Digital Twins hun eigen aangepaste typen voor tweelingen kunnen definiëren, is er echter geen manier om vooraf statische retourgegevens te definiëren voor veel Azure Digital Twins aanroepen. In plaats daarvan retourneren de API's indien van toepassing sterk getypeerd wrapper-typen en staan de gegevens van de aangepaste tweelingen in Json.NET-objecten (overal gebruikt waar het gegevenstype 'object' wordt weergegeven in de API-handtekeningen). U kunt deze objecten op de juiste manier casten in uw code.

### <a name="error-handling"></a>Foutafhandeling

Wanneer er een fout optreedt in de SDK (inclusief HTTP-fouten zoals 404), zal de SDK een uitzondering veroorzaken. Daarom is het belangrijk om alle API-aanroepen binnen try/catch-blokken in te kapselen.

Hier ziet u een codefragment dat probeert een tweeling toe te voegen en eventuele fouten in dit proces vangt:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Zoekresultaten oproepen

AutoRest genereert twee typen pagineringspatronen voor de SDK:
* Een voor alle API's, met uitzondering van de Query-API
* Een voor de Query-API

In het pagineringspatroon zonder query is hier een voorbeeldmethode die laat zien hoe u een lijst met uitgaande relaties kunt ophalen uit Azure Digital Twins:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Het tweede patroon wordt alleen gegenereerd voor de Query-API. Er wordt expliciet `continuationToken` een gebruikt.

>[!TIP]
> Een belangrijke reden voor het opvragen van pagina's is het berekenen van de kosten voor de [query-eenheid](concepts-query-units.md) voor een query-API-aanroep.

Hier is een voorbeeld met dit patroon:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Volgende stappen

Doorloop de stappen voor het maken van een client-app waarin u uw SDK kunt gebruiken:
* [*Zelfstudie: Een client-app coderen*](tutorial-code.md)
