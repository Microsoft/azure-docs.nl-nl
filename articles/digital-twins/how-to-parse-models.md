---
title: Modellen parseren en valideren
titleSuffix: Azure Digital Twins
description: Meer informatie over het gebruik van de parser-bibliotheek voor het parseren van DTDL-modellen.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q3
ms.openlocfilehash: 155566a125485fda326f9f5e02d4aead0ffe30e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100560741"
---
# <a name="parse-and-validate-models-with-the-dtdl-parser-library"></a>Modellen parseren en valideren met de DTDL parser-bibliotheek

[Modellen](concepts-models.md) in azure Digital apparaatdubbels worden gedefinieerd met behulp van de op JSON-LD gebaseerde Digital apparaatdubbels Definition Language (DTDL). **Het is raadzaam om uw modellen offline te valideren voordat u ze uploadt naar uw Azure Digital Apparaatdubbels-exemplaar.**

U kunt dit doen door een .NET-bibliotheek voor DTDL-parsering aan de client zijde te maken op NuGet: [**micro soft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/). 

U kunt de parser-bibliotheek rechtstreeks in uw C#-code gebruiken of het voorbeeld project language-neutraal code gebruiken dat is gebaseerd op de parser-bibliotheek: [**DTDL validator**](/samples/azure-samples/dtdl-validator/dtdl-validator)-voor beeld.

## <a name="use-the-dtdl-validator-sample"></a>Het voor beeld van de DTDL-validator gebruiken

De [**validatie functie DTDL**](/samples/azure-samples/dtdl-validator/dtdl-validator) is een voorbeeld project dat model documenten kan valideren om er zeker van te zijn dat de DTDL geldig is. Het is gebaseerd op de .NET parser-bibliotheek en is neutraal. U kunt het downloaden met behulp van de knop voor het laden van een *zip* -voorbeeld koppeling.

De bron code bevat voor beelden voor het gebruik van de parser-bibliotheek. U kunt het voor beeld validator als een opdracht regel programma gebruiken om een mapstructuur van DTDL-bestanden te valideren. Het biedt ook een interactieve modus.

In de map voor het voor beeld van de DTDL-validator raadpleegt u het *README.MD* -bestand voor instructies over hoe u het voor beeld kunt inpakken in een zelfstandig uitvoerbaar bestand.

Nadat u een zelfstandig pakket hebt gemaakt en het uitvoer bare bestand aan uw pad hebt toegevoegd, kunt u de validatie functie met deze opdracht uitvoeren in een-console op uw computer:

```cmd/sh
DTDLValidator
```

Met de standaard opties zoekt het voor beeld naar `*.json` bestanden in de huidige map en alle submappen. U kunt ook de volgende optie toevoegen om het voor beeld te zoeken in de aangegeven map en alle submappen voor bestanden met de extensie *. dtdl*:

```cmd/sh
DTDLValidator -d C:\Work\DTDL -e dtdl 
```

U kunt de `-i` optie voor het voor beeld toevoegen om de interactieve modus in te voeren:

```cmd/sh
DTDLValidator -i
```

Zie voor meer informatie over dit voor beeld de bron code of de uitvoering `DTDLValidator --help` .

## <a name="use-the-net-parser-library"></a>De .NET parser-bibliotheek gebruiken 

De bibliotheek [**micro soft. Azure. DigitalTwins. parser**](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) biedt model toegang tot de DTDL definities, die in wezen optreden als het equivalent van C# reflectie voor DTDL. Deze tape wisselaar kan onafhankelijk van een [Azure Digital APPARAATDUBBELS SDK](how-to-use-apis-sdks.md)worden gebruikt, met name voor DTDL-validatie in een visuele of tekst editor. Het is handig om te controleren of uw model definitie bestanden geldig zijn voordat u deze uploadt naar de service.

Als u de parser-bibliotheek wilt gebruiken, geeft u deze een set DTDL-documenten. Normaal gesp roken haalt u deze model documenten op uit de-service, maar u kunt ze ook lokaal beschikbaar stellen als uw client verantwoordelijk is voor het uploaden van ze naar de service in de eerste plaats. 

Hier volgt de algemene werk stroom voor het gebruik van de parser:
1. Haal sommige of alle DTDL-documenten op uit de service.
2. Geef de geretourneerde, in-Memory DTDL-documenten door aan de parser.
3. De parser valideert de set van documenten die hieraan worden door gegeven en retourneert gedetailleerde fout gegevens. Deze mogelijkheid is handig in scenario's met de editor.
4. Gebruik de parser-Api's om door te gaan met het analyseren van de modellen die zijn opgenomen in de documentenset. 

De mogelijkheden van de parser zijn onder andere:
* Alle geïmplementeerde model interfaces ophalen (de inhoud van de sectie van de interface `extends` ).
* Alle eigenschappen, telemetrie, opdrachten, onderdelen en relaties die in het model zijn gedeclareerd, ophalen. Met deze opdracht worden ook alle meta gegevens in deze definities opgehaald en worden de overname ( `extends` secties) in rekening gebracht.
* Alle complexe model definities ophalen.
* Bepaal of een model kan worden toegewezen vanuit een ander model.

> [!NOTE]
> [IoT Plug en Play-apparaten (PnP)](../iot-pnp/overview-iot-plug-and-play.md) gebruiken een kleine syntaxis variant om hun functionaliteit te beschrijven. Deze variant van de syntaxis is een semantisch compatibele subset van de DTDL die wordt gebruikt in azure Digital Apparaatdubbels. Wanneer u de parser-bibliotheek gebruikt, hoeft u niet te weten welke syntaxis variant is gebruikt voor het maken van de DTDL voor uw digitale twee. De parser retourneert altijd standaard hetzelfde model voor zowel de syntaxis van PnP-als Azure Digital Apparaatdubbels.

### <a name="code-with-the-parser-library"></a>Code met de parser-bibliotheek

U kunt de parser-bibliotheek rechtstreeks gebruiken voor zaken als het valideren van modellen in uw eigen toepassing of voor het genereren van dynamische, op modellen gebaseerde gebruikers interface, Dash boards en rapporten.

Voor de ondersteuning van het volgende voor beeld van de parsers code moet u rekening houden met verschillende modellen die zijn gedefinieerd in een Azure Digital Apparaatdubbels-exemplaar:

:::code language="json" source="~/digital-twins-docs-samples/models/coffeeMaker-coffeeMakerInterface-coffeeBar.json":::

De volgende code toont een voor beeld van het gebruik van de parser-bibliotheek om deze definities in C# weer te geven:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/parseModels.cs":::

## <a name="next-steps"></a>Volgende stappen

Wanneer u klaar bent met het schrijven van uw modellen, raadpleegt u de DigitalTwinsModels-Api's om ze te uploaden (en andere beheer bewerkingen uit te voeren):
* [*Instructies: DTDL-modellen beheren*](how-to-manage-model.md)