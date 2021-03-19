---
title: Upgrade uitvoeren naar .NET SDK versie 10
titleSuffix: Azure Cognitive Search
description: Code migreren naar Azure Cognitive Search .NET SDK versie 10 van oudere versies. Meer informatie over wat er nieuw is en welke code wijzigingen vereist zijn.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: bfe24ff38446fa0d0ccea96799e6f42b561713bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89002807"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Upgrade uitvoeren naar Azure Cognitive Search .NET SDK versie 10

Als u versie 9,0 of ouder van de [.NET SDK](/dotnet/api/overview/azure/search)gebruikt, helpt dit artikel bij het upgraden van uw toepassing tot het gebruik van versie 10.

De naam van Azure Search is gewijzigd in azure Cognitive Search in versie 10, maar naam ruimten en pakket namen blijven onveranderd. Eerdere versies van de SDK (9,0 en eerder) blijven de oude naam gebruiken. Zie [Azure Cognitive Search gebruiken vanuit een .NET-toepassing](search-howto-dotnet-sdk.md)voor meer informatie over het gebruik van de SDK, met inbegrip van voor beelden.

Versie 10 voegt diverse functies en probleem oplossingen toe en brengt deze naar hetzelfde functionaliteits niveau als de REST API versie `2019-05-06` . In gevallen waarin een wijziging bestaande code verbreekt, wordt u begeleid bij de [stappen die nodig zijn om het probleem op te lossen](#UpgradeSteps).

> [!NOTE]
> Als u versie 8,0-Preview of ouder gebruikt, moet u eerst een upgrade uitvoeren naar versie 9 en vervolgens een upgrade uitvoeren naar versie 10. Zie [upgraden naar de Azure Search .NET SDK versie 9](search-dotnet-sdk-migration-version-9.md) voor instructies.
>
> Het exemplaar van de zoek service ondersteunt verschillende REST API versies, met inbegrip van de meest recente versie. U kunt een versie blijven gebruiken wanneer deze niet langer het meest recent is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u in elke aanvraag de API-versie opgeven via de para meter API-Version. Wanneer u de .NET SDK gebruikt, bepaalt de versie van de SDK die u gebruikt, de corresponderende versie van de REST API. Als u een oudere SDK gebruikt, kunt u deze code zonder wijzigingen blijven uitvoeren, zelfs niet als de service wordt bijgewerkt voor ondersteuning van een nieuwere API-versie.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Wat is er nieuw in versie 10
Versie 10 van de Azure Cognitive Search .NET SDK-doelen REST API `2019-05-06` met deze updates:

* Introductie van twee nieuwe vaardig heden- [voorwaardelijke](cognitive-search-skill-conditional.md) vaardig heden en [tekst vertalingen](cognitive-search-skill-text-translation.md).
* [Shaper-vaardigheids](cognitive-search-skill-shaper.md) invoer is herstructureeerd om consolidatie van geneste contexten mogelijk te houden. Zie voor meer informatie dit [voor beeld van JSON-definitie](./cognitive-search-skill-shaper.md#scenario-3-input-consolidation-from-nested-contexts).
* Toevoeging van twee nieuwe [veld toewijzings functies](search-indexer-field-mappings.md):
    - [urlEncode](./search-indexer-field-mappings.md#urlencode-function)
    - [urlDecode](./search-indexer-field-mappings.md#urldecode-function)
* In bepaalde gevallen kunnen fouten en waarschuwingen die worden weer gegeven in de [uitvoerings status van de Indexeer functie](/rest/api/searchservice/get-indexer-status) extra informatie bevatten over fout opsporing. `IndexerExecutionResult` is bijgewerkt om dit gedrag weer te geven.
* Afzonderlijke vaardig heden die zijn gedefinieerd binnen een [vakkennisset](cognitive-search-defining-skillset.md) kunnen eventueel worden geïdentificeerd door een eigenschap op te geven `name` .
* `ServiceLimits` toont de limieten voor [complexe typen](./search-howto-complex-data-types.md) en `IndexerExecutionInfo` toont de relevante limieten/quota's voor de Indexeer functie.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen voor het uitvoeren van een upgrade

1. Werk uw NuGet-verwijzing bij voor `Microsoft.Azure.Search` het gebruik van de NuGet Package Manager-console of klik met de rechter muisknop op uw project verwijzingen en selecteer vervolgens NuGet-pakketten beheren... in Visual Studio.

2. Nadat NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u het project opnieuw op. 

3. Als uw build is mislukt, moet u elke compilatie fout herstellen. Zie belang rijke [wijzigingen in versie 10](#ListOfChanges) voor meer informatie over het oplossen van elke mogelijke build-fout.

4. Wanneer u eventuele build-fouten of-waarschuwingen hebt opgelost, kunt u wijzigingen aanbrengen in uw toepassing om te profiteren van de nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Wijzigingen in versie 10 verbreken

Er zijn verschillende belang rijke wijzigingen in versie 10 waarvoor code wijzigingen moeten worden aangebracht naast het opnieuw samen stellen van uw toepassing.

> [!NOTE]
> De onderstaande lijst met wijzigingen is niet limitatief. Sommige wijzigingen leiden waarschijnlijk niet tot fouten in de build, maar zijn technisch gebreken omdat ze binaire compatibiliteit met assembly's die afhankelijk zijn van eerdere versies van de Azure Cognitive Search .NET SDK-assembly's, afgebroken. Belang rijke wijzigingen die onder deze categorie vallen, worden ook vermeld samen met aanbevelingen. Bouw uw toepassing opnieuw op wanneer u een upgrade uitvoert naar versie 10 om problemen met binaire compatibiliteit te voor komen.

### <a name="custom-web-api-skill-definition"></a>Aangepaste web API-vaardigheids definitie

De definitie van de [aangepaste web API-vaardigheid](cognitive-search-custom-skill-web-api.md) is onjuist opgegeven in versie 9 en ouder. 

Het model `WebApiSkill` dat is opgegeven `HttpHeaders` als een object eigenschap die een woorden boek _bevat_ . Als u een vaardig heden maakt die `WebApiSkill` op deze manier zijn gebouwd, wordt een uitzonde ring veroorzaakt, omdat het rest API de aanvraag zou beschouwen als een ongeldige indeling. Dit probleem is opgelost door `HttpHeaders` **een woordenboek eigenschap op het hoogste niveau** in het model zelf te maken, `WebApiSkill` die als een geldige aanvraag van de rest API wordt beschouwd.

Als u bijvoorbeeld eerder hebt geprobeerd een exemplaar van een te maken, doet u het volgende `WebApiSkill` :

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

Wijzig deze in het volgende om te voor komen dat de validatie fout van de REST API:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Shaper-vaardigheid staat geneste context consolidatie toe

Shaper-vaardigheid kan nu invoer samenvoeging van geneste contexten toestaan. Om deze wijziging in te scha kelen, zijn we gewijzigd `InputFieldMappingEntry` zodat deze kan worden geïnstantieerd door alleen een `Source` eigenschap op te geven, of zowel de `SourceContext` als- `Inputs` Eigenschappen.

Waarschijnlijk hoeft u geen code wijzigingen aan te brengen. Houd er echter rekening mee dat slechts één van deze twee combi Naties is toegestaan. Dit betekent:

- Het `InputFieldMappingEntry` `Source` is niet toegestaan om een geïnitialiseerde te maken.
- Het maken van een `InputFieldMappingEntry` waar alleen `SourceContext` en `Inputs` is geïnitialiseerd, is geldig.
- Alle andere Combi Naties waarbij deze drie eigenschappen zijn betrokken, zijn ongeldig.

Als u besluit het gebruik van deze nieuwe mogelijkheid te maken, moet u ervoor zorgen dat al uw clients zijn bijgewerkt met versie 10 eerst voordat u die wijziging uitvoerde. Anders is het mogelijk dat een update door een client (met behulp van een oudere versie van de SDK) naar de shaper-vaardigheid kan leiden tot validatie fouten.

> [!NOTE]
> Hoewel het onderliggende `InputFieldMappingEntry` model is gewijzigd om consolidatie van geneste contexten mogelijk te maken, is het gebruik alleen geldig binnen de definitie van een shaper-vaardigheid. Het gebruik van deze mogelijkheid in andere vaardig heden, terwijl de tijd tijdens het compileren geldig is, resulteert in een validatie fout tijdens runtime.

## <a name="skills-can-be-identified-by-a-name"></a>Vaardig heden kunnen worden geïdentificeerd aan de hand van een naam

Elke vaardigheid binnen een vaardig heden heeft nu een nieuwe eigenschap `Name` , die in uw code kan worden geïnitialiseerd om de vaardigheid te identificeren. Dit is optioneel: als u niets opgeeft (dit is de standaard instelling, als er geen expliciete code wijziging is aangebracht), wordt er een standaard naam toegewezen met de op 1 gebaseerde index van de vaardigheid in de vaardig heden, voorafgegaan door het teken ' # '. In de volgende definitie van de vaardig heden (de meeste initialisaties worden overgeslagen voor de boog):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill` wordt toegewezen aan een naam `#1` , wordt toegewezen, wordt toegewezen `WebApiSkill` , enzovoort `#2` `ShaperSkill` `#3` .

Als u ervoor kiest om de vaardig heden te identificeren met een aangepaste naam, moet u ervoor zorgen dat alle exemplaren van uw clients eerst worden bijgewerkt naar versie 10 van de SDK. Anders is het mogelijk dat een client die gebruikmaakt van een oudere versie van de SDK `null` `Name` , de eigenschap van een vaardigheid zou kunnen gebruiken, waardoor de client terugvalt op het standaard naamgevings schema.

## <a name="details-about-errors-and-warnings"></a>Details over fouten en waarschuwingen

`ItemError` en `ItemWarning` modellen waarmee details van fouten en waarschuwingen (respectievelijk) die zich voordoen tijdens een uitvoering van de Indexeer functie zijn gewijzigd, zodat er drie nieuwe eigenschappen zijn met het doel om te helpen bij het opsporen van fouten in de Indexeer functie. Deze eigenschappen zijn:

- `Name`: De naam van de bron waarvan de fout afkomstig is. Het kan bijvoorbeeld verwijzen naar een bepaalde vaardigheid in de gekoppelde vaardig heden.
- `Details`: Meer uitgebreide informatie over de fout of waarschuwing.
- `DocumentationLink`: Een koppeling naar een probleemoplossings gids voor de specifieke fout of waarschuwing.

> [!NOTE]
> We zijn begonnen met het structureren van onze fouten en waarschuwingen om deze nuttige informatie waar mogelijk op te nemen. Er wordt aan gewerkt om ervoor te zorgen dat voor alle fouten en waarschuwingen deze Details aanwezig zijn, maar dat er een onderhanden werk wordt uitgevoerd en deze aanvullende details mogelijk niet altijd worden ingevuld.

## <a name="next-steps"></a>Volgende stappen

- Wijzigingen in de shaper-vaardigheid hebben de meest mogelijke gevolgen voor nieuwe of bestaande code. Als volgende stap moet u het volgende voor beeld bekijken waarin de invoer structuur wordt geïllustreerd: [voor beeld van een shaper skill JSON-definitie](cognitive-search-skill-shaper.md)
- Ga naar het [overzicht van AI-verrijking](cognitive-search-concept-intro.md).
- We stellen uw feedback op de SDK. Als u problemen ondervindt, kunt u ons vragen om hulp over [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Als u een bug vindt, kunt u een probleem in de [Azure .NET SDK github-opslag plaats](https://github.com/Azure/azure-sdk-for-net/issues)opslaan. Zorg ervoor dat u de titel van het probleem voordoet met ' [Azure Cognitive Search] '.