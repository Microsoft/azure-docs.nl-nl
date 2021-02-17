---
title: Nieuwe functies - Language Understanding (LUIS)
description: Dit artikel wordt regelmatig bijgewerkt met nieuws over de Azure Cognitive Services Language Understanding-API.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 02/16/2021
ms.openlocfilehash: f4ac384a9b1bce944397369cb93392f179643f96
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100558757"
---
# <a name="whats-new-in-language-understanding"></a>Nieuwe functies in Language Understanding

Meer informatie over nieuwe functies in de service. Dit kunnen releaseopmerkingen, video's, blogposts en andere soorten informatie zijn. Voeg een bladwijzer toe aan deze pagina om up-to-date te blijven over de service.

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="january-2021"></a>Januari 2021

* De V3 API voor voor spellingen ondersteunt nu de [Bing-spelling-API](luis-tutorial-bing-spellcheck.md).
* De regionale portals (au.luis.ai en eu.luis.ai) zijn geconsolideerd in één portal en URL. Als u een van deze portals gebruikt, wordt u automatisch omgeleid naar luis.ai.

### <a name="december-2020"></a>December 2020

* Alle LUIS-gebruikers moeten [migreren naar een LUIS-creatieresource](luis-migration-authoring.md)
* Nieuwe [evaluatie-eind punten](luis-how-to-batch-test.md#batch-testing-using-the-rest-api) waarmee u batch tests kunt verzenden met behulp van de rest API en de nauw keurigheid van uw intentie en entiteiten krijgt. Beschikbaar vanaf het LUIS-eindpunt v3.0-preview.

### <a name="june-2020"></a>Juni 2020

* [Preview 3.0 Authoring](luis-migration-authoring-entities.md) SDK -
    * Versie 3.2.0-preview. 3 - [.NET - NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * Versie 4.0.0-preview. 3 - [JS - NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* DevOps-procedures toepassen met LUIS
    * Concepten
        * [DevOps-procedures voor LUIS](luis-concept-devops-sourcecontrol.md)
        * [Werkstromen voor continue integratie en continue levering voor LUIS DevOps](luis-concept-devops-automation.md)
        * [Testen op LUIS DevOps](luis-concept-devops-testing.md)
    * Uitleg
        * [DevOps toepassen op het ontwikkelen van LUIS-apps met GitHub-acties](luis-how-to-devops-with-github.md)
    * [Volledige GitHub-codeopslagplaats](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>Mei 2020 - //Build

* Uitgebracht als **algemeen verkrijgbaar**:
    * [Language Understanding-container](luis-container-howto.md)
    * Preview-portal gepromoveerd naar [huidige portal](https://www.luis.ai), [vorige](https://previous.luis.ai) portal nog beschikbaar
    * Nieuwe ervaring voor het maken en labelen van een machine learning-entiteit
    * [Upgradeproces](migrate-from-composite-entity.md) van samengestelde en eenvoudige entiteiten naar machine learning-entiteiten
    * Ondersteuning voor het normaliseren van woordvarianten [instellen](how-to-application-settings-portal.md)
* Wijzigingen in Authoring-API bekijken
    * App-schema 7.x voor geneste machine learning-entiteiten
    * [Migratie naar vereiste functie](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Nieuwe resources voor ontwikkelaars
    * [Hulpprogramma's voor continue integratie](developer-reference-resource.md#continuous-integration-tools)
    * Workshop: Leer de aanbevolen procedures voor [_Natural Language Understanding_ (NLU) met LUIS](developer-reference-resource.md#workshops)
* [Door de klant beheerde sleutels](luis-encryption-of-data-at-rest.md) -alle gegevens die u in LUIS gebruikt, versleutelen met uw eigen sleutel
* [AI weergeven](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (video): bekijk de nieuwe functies in LUIS



### <a name="march-2020"></a>Maart 2020

* TLS 1.2 wordt nu afgedwongen voor alle HTTP-aanvragen bij deze service. Zie [Beveiliging van Azure Cognitive Services](../cognitive-services-security.md) voor meer informatie.

### <a name="november-4-2019---ignite"></a>4 november 2019 - Ignite

* Video - [Geavanceerde Natural Language Understanding-modellen (NLU) met LUIS en Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Verbeterde productiviteit van ontwikkelaars
    * Algemene verkrijgbaarheid van ons [voorspellingseindpunt V3](luis-migration-api-v3.md).
    * De mogelijkheid om apps met de indeling `.lu` ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) te importeren en te exporteren. Hierdoor verloopt het CI/CD-proces effectiever.
* Taaluitbreiding
    * [Arabisch en Hindi](luis-language-support.md) in openbare preview.
* Vooraf gemaakte modellen
    * [Vooraf samengestelde domeinen](luis-reference-prebuilt-domains.md) zijn nu algemeen verkrijgbaar
    * Japanse [vooraf samengestelde entiteiten](luis-reference-prebuilt-entities.md#japanese-entity-support), leeftijd, valuta, getal en percentage, worden niet ondersteund in V3.
    * Italiaanse [vooraf samengestelde entiteiten](luis-reference-prebuilt-entities.md#italian-entity-support), leeftijd, valuta, dimensie, getal en percentageresolutie, gewijzigd van V2.
* Verbeterde gebruikerservaring in [preview.luis.ai-portal](https://preview.luis.ai) - labelervaring verbeterd voor het maken en opsporen van fouten in complexe modellen. Probeer de zelfstudies voor de preview-portal:
    * [Alleen intentie](tutorial-intents-only.md)
    * [Afbreekbare machine learning-entiteit](tutorial-machine-learned-entity.md)
* Language Understanding-functionaliteit verbeteren - [geavanceerde taalmodellen maken](luis-concept-entity-types.md) met minder inspanning.
* Definieer machine learning-functies op modelniveau en zorg dat modellen kunnen worden gebruikt als signalen voor andere modellen, bijvoorbeeld door entiteiten te gebruiken als functies voor intenties en andere entiteiten.
* Nieuwe, uitgebreide [limieten](luis-limits.md) -hoger maximum voor woordgroeplijsten en totaal aantal woordgroepen, nieuw model als functielimiet
* Informatie ophalen uit tekst in de indeling van een diepe hiërarchiestructuur, zodat gesprekstoepassingen krachtiger worden.

    ![afbeelding van machine learning-entiteit](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>3 september 2019

* Azure-creatieresource - [nu migreren](luis-migration-authoring.md).
    * 500 apps per Azure-resource
    * 100 versies per app
* Turkse ondersteuning voor vooraf gemaakte entiteiten
* Italiaanse ondersteuning voor datetimeV2

### <a name="july-23-2019"></a>23 juli 2019

* De [tekstherkenning](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) bijwerken naar 1.2.3
    * Herkenning van leeftijd, temperatuur, dimensie en valuta in het Italiaans.
    * Verbetering van de herkenning van feestdagen in het Engels om op Thanksgiving gebaseerde datums goed te kunnen berekenen.
    * Verbeteringen in Franse Datum/tijd om de fout-positieven van niet-datum- en niet-tijd-entiteiten te verminderen.
    * Ondersteuning voor kalender/school/boekjaar en acroniemen in het Engelse datumbereik.
    * Verbeterde herkenning van telefoonnummers in Chinees en Japans.
    * Verbeterde ondersteuning voor Getalbereik in het Engels.
    * Prestatieverbeteringen.

### <a name="june-24-2019"></a>24 juni 2019

* [Vooraf gemaakte entiteit voor OrdinalV2](luis-reference-prebuilt-ordinal-v2.md) om ordenen te ondersteunen, zoals volgende, vorige en laatste. Alleen Engelse cultuur.

### <a name="may-6-2019---build-conference"></a>6 mei 2019 - //Build Conference

De volgende functies zijn uitgebracht op de Build 2019 Conference:

* [Preview van de V3 API-migratiehandleiding](luis-migration-api-v3.md)
* [Verbeterd analysedashboard](luis-how-to-use-dashboard.md)
* [Verbeterde vooraf gemaakte domeinen](luis-reference-prebuilt-domains.md)
* [Dynamische lijstentiteiten](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Externe entiteiten](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogs

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>Video's

### <a name="2019-ignite-videos"></a>2019 Ignite-video's

[Geavanceerde Natural Language Understanding-modellen (NLU) met LUIS en Azure Cognitive Services | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 Build: video's

[Gespreks-AI van Azure gebruiken om uw bedrijf te schalen voor de volgende generatie](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Service-updates

[Meldingen van Azure-updates voor Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
