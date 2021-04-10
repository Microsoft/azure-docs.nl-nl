---
title: Maak Sdk's met aangepaste taal met auto rest
titleSuffix: Azure Digital Twins
description: Meer informatie over het gebruik van autorest voor het genereren van Sdk's voor aangepaste talen, voor het schrijven van Azure Digital Apparaatdubbels-code in andere talen waarvoor geen Sdk's zijn uitgegeven.
author: baanders
ms.author: baanders
ms.date: 3/9/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom:
- devx-track-js
- contperf-fy21q3
ms.openlocfilehash: 35cf54199f8f2c187ad397c21fb941111f07c4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561837"
---
# <a name="create-custom-language-sdks-for-azure-digital-twins-using-autorest"></a>Aangepaste Sdk's voor Azure Digital Apparaatdubbels maken met auto rest

Als u met Azure Digital Apparaatdubbels moet werken met een taal die geen [gepubliceerde Azure Digital APPARAATDUBBELS SDK](how-to-use-apis-sdks.md)heeft, wordt in dit artikel uitgelegd hoe u auto rest kunt gebruiken om uw eigen SDK te genereren in de taal van uw keuze. 

In de voor beelden in dit artikel wordt het maken van een [Data-SDK](how-to-use-apis-sdks.md#overview-data-plane-apis)weer gegeven, maar dit proces werkt ook voor het genereren van een  [beheer vlak-SDK](how-to-use-apis-sdks.md#overview-control-plane-apis) .

## <a name="prerequisites"></a>Vereisten

Als u een SDK wilt genereren, moet u eerst de volgende installatie uitvoeren op uw lokale computer:
* Auto [**rest**](https://github.com/Azure/autorest)installeren, versie 2.0.4413 (versie 3 wordt momenteel niet ondersteund)
* Installeer [**Node.js**](https://nodejs.org), een vereiste voor het gebruik van auto rest
* [ **Visual Studio** installeren](https://visualstudio.microsoft.com/downloads/)
* Down load het nieuwste Azure Digital Apparaatdubbels **Data vlak Swagger** (OpenAPI)-bestand van de [Data vlak Swagger-map](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins), samen met de bijbehorende map met voor beelden. Het Swagger-bestand bevindt zich in de naam *digitaltwins.jsop*.

>[!TIP]
> Als u in plaats daarvan een **Control-SDK** wilt maken, voert u de stappen in dit artikel uit met behulp van het meest recente OpenAPI-bestand ( **Control best Swagger** ) van de [Control vlak Swagger-map](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/) in plaats van het gegevens vlak één.

Zodra de computer is uitgerust met alles uit de bovenstaande lijst, kunt u auto rest gebruiken om een SDK te maken.

## <a name="create-the-sdk-using-autorest"></a>De SDK maken met auto rest 

Zodra u Node.js hebt geïnstalleerd, kunt u deze opdracht uitvoeren om ervoor te zorgen dat de vereiste versie van auto rest is geïnstalleerd:
```cmd/sh
npm install -g autorest@2.0.4413
```

Voer de volgende stappen uit om auto rest te gebruiken voor het Swagger-bestand van de Azure Digital Apparaatdubbels:
1. Kopieer het Azure Digital Apparaatdubbels Swagger-bestand en de bijbehorende map met voor beelden naar een werkmap.
2. Gebruik een opdracht prompt venster om over te scha kelen naar die werkmap.
3. Voer auto rest uit met de volgende opdracht. Vervang de `<language>` tijdelijke aanduiding door de taal van uw keuze: `python` ,, `java` , enzovoort `go` . (U vindt de volledige lijst met opties in het [Leesmij-bestand](https://github.com/Azure/autorest)voor auto rest.)

```cmd/sh
autorest --input-file=digitaltwins.json --<language> --output-folder=DigitalTwinsApi --add-credentials --azure-arm --namespace=DigitalTwinsApi
```

Als gevolg hiervan ziet u een nieuwe map met de naam *DigitalTwinsApi* in uw werkmap. De gegenereerde SDK-bestanden hebben de naam ruimte *DigitalTwinsApi*. U kunt die naam ruimte blijven gebruiken via de rest van de voor beelden van het gebruik in dit artikel.

Auto rest ondersteunt een breed scala aan taal code generators.

## <a name="make-the-sdk-into-a-class-library"></a>De SDK in een klassen bibliotheek maken

U kunt de bestanden die worden gegenereerd door autorest rechtstreeks toevoegen aan een .NET-oplossing. Het is echter waarschijnlijk dat u de Azure Digital Apparaatdubbels SDK wilt gebruiken in verschillende afzonderlijke projecten (uw client-apps, Azure Functions apps en meer). Daarom kan het handig zijn om een afzonderlijk project (een .NET-klassen bibliotheek) te bouwen op basis van de gegenereerde bestanden. Vervolgens kunt u dit klassen bibliotheek project in meerdere oplossingen insluiten als een project verwijzing.

In deze sectie vindt u instructies voor het bouwen van de SDK als een klassen bibliotheek. Dit is een eigen project en kan in andere projecten worden opgenomen. Deze stappen zijn afhankelijk van **Visual Studio**.

Dit zijn de stappen:

1. Een nieuwe Visual Studio-oplossing maken voor een klassen bibliotheek
2. *DigitalTwinsApi* gebruiken als de project naam
3. Klik in Solution Explorer met de rechter muisknop op het *DigitalTwinsApi* -project van de gegenereerde oplossing en kies *> bestaand item toevoegen...*
4. Zoek de map waar u de SDK hebt gegenereerd en selecteer de bestanden op het hoofd niveau
5. Klik op OK
6. Voeg een map toe aan het project (Klik met de rechter muisknop op het project in Solution Explorer en kies *> nieuwe map toevoegen*)
7. De mappen *modellen* een naam
8. Klik met de rechter muisknop op de map *modellen* in Solutions Explorer en selecteer *> bestaand item toevoegen...*
9. Selecteer de bestanden in de map *modellen* van de gegenereerde SDK en druk op OK

Als u de SDK wilt bouwen, moet uw project over de volgende verwijzingen beschikken:
* `Microsoft.Rest.ClientRuntime`
* `Microsoft.Rest.ClientRuntime.Azure`

Als u deze wilt toevoegen, opent u *Hulpprogram ma's > NuGet Package Manager > NuGet-pakketten beheren voor oplossing...*.

1. Controleer in het deel venster of het tabblad *Bladeren* is geselecteerd
2. Zoek naar *micro soft. rest*
3. Selecteer de `ClientRuntime` pakketten en en `ClientRuntime.Azure` Voeg deze toe aan uw oplossing

U kunt nu het project bouwen en dit toevoegen als een project verwijzing in een Azure Digital Apparaatdubbels-toepassing die u schrijft.

## <a name="tips-for-using-the-sdk"></a>Tips voor het gebruik van de SDK

Deze sectie bevat algemene informatie en richt lijnen voor het gebruik van de gegenereerde SDK.

### <a name="synchronous-and-asynchronous-calls"></a>Synchrone en asynchrone aanroepen

Alle SDK-functies zijn beschikbaar in synchrone en asynchrone versies.

### <a name="typed-and-untyped-data"></a>Getypte en niet-getypte gegevens

REST API-oproepen retour neren doorgaans sterk getypte objecten. Maar omdat gebruikers met Azure Digital Apparaatdubbels hun eigen aangepaste typen kunnen definiëren voor apparaatdubbels, is het niet mogelijk om statische retour gegevens te definiëren voor veel Azure Digital Apparaatdubbels-aanroepen. In plaats daarvan retour neren de Api's sterk getypeerde wrapper types, indien van toepassing, en de aangepaste, getypte dubbele gegevens bevindt zich in Json.NET-objecten (gebruikt wanneer het gegevens type ' object ' wordt weer gegeven in de API-hand tekeningen). U kunt deze objecten op de juiste wijze casten in uw code.

### <a name="error-handling"></a>Foutafhandeling

Wanneer er een fout optreedt in de SDK (inclusief HTTP-fouten zoals 404), wordt er een uitzonde ring gegenereerd door de SDK. Daarom is het belang rijk om alle API-aanroepen te integreren binnen try/catch-blokken.

Hier volgt een code fragment dat probeert een dubbele fout op te tellen en eventuele fouten in dit proces te ondervangen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_errorHandling":::

### <a name="paging"></a>Zoekresultaten oproepen

Auto rest genereert twee typen paginerings patronen voor de SDK:
* Een voor alle Api's, met uitzonde ring van de query-API
* Een voor de query-API

In de niet-query paginerings patroon ziet u hier een voor beeld van het ophalen van een lijst met uitgaande relaties van Azure Digital Apparaatdubbels:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="FindOutgoingRelationshipsMethod":::

Het tweede patroon wordt alleen gegenereerd voor de query-API. Er wordt een `continuationToken` expliciete methode gebruikt.

>[!TIP]
> Een belangrijkste reden voor het ophalen van pagina's is het berekenen van de kosten voor de [query-eenheid](concepts-query-units.md) voor een query-API-aanroep.

Hier volgt een voor beeld met dit patroon:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="PagedQuery":::

## <a name="next-steps"></a>Volgende stappen

Door loop de stappen voor het maken van een client-app waarin u uw SDK kunt gebruiken:
* [*Zelfstudie: Een client-app coderen*](tutorial-code.md)
