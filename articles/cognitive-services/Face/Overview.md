---
title: Wat is de Azure Face-service?
titleSuffix: Azure Cognitive Services
description: De Azure Face-service biedt AI-algoritmen voor het detecteren, herkennen en analyseren van menselijke gezichten in afbeeldingen.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: gezichtsherkenning, gezichtsherkenningssoftware, gezichtsanalyse, gezichtsvergelijking, gezichtsherkennings-app, gezichten zoeken binnen een afbeelding, zoekfunctie voor gezichtsherkenning
ms.openlocfilehash: a999b2100dc57d7b623cebbbe261f821a80835e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505119"
---
# <a name="what-is-the-azure-face-service"></a>Wat is de Azure Face-service?

> [!WARNING]
> Op 11 juni 2020 kondigde Microsoft aan dat het geen gezichtsherkenningssoftware verkoopt aan politieafdelingen in de Verenigde Staten totdat er solide wetgeving op basis van mensenrechten in werking is getreden. Daardoor is het mogelijk dan klanten gezichtsherkenningsfuncties of een functionaliteit in Azure Services, zoals Face of Video Indexer, niet kunnen gebruiken als de klant deze services gebruikt of laat gebruiken voor of door een politieafdeling in de Verenigde Staten.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

De Azure Face-service biedt AI-algoritmen voor het detecteren, herkennen en analyseren van menselijke gezichten in afbeeldingen. Gezichtsherkenningssoftware is belangrijk in veel verschillende scenario's, zoals beveiliging, natuurlijke gebruikersinterfaces, analyse en beheer van afbeeldingsinhoud, mobiele apps en robotica.

De Face-service biedt verschillende functies voor gezichtsanalyse die in de volgende secties worden beschreven.

Deze documentatie bevat de volgende typen artikelen:
* De [quickstarts](./Quickstarts/client-libraries.md) zijn stapsgewijs instructies voor het aanroepen van de service en het in korte tijd krijgen van resultaten. 
* De [instructiegidsen bevatten](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md) instructies voor het gebruik van de service op specifiekere of aangepaste manieren.
* De [conceptuele artikelen](./concepts/face-detection.md) bevatten uitgebreide uitleg over de functionaliteit en functies van de service.
* De [zelfstudies zijn](./enrollment-overview.md) langere handleidingen die laten zien hoe u deze service kunt gebruiken als onderdeel van bredere zakelijke oplossingen.

## <a name="face-detection"></a>Gezichtsdetectie

De Detect-API detecteert menselijke gezichten in een afbeelding en retourneert de rechthoekcoördinaten van hun locaties. Gezichtsdetectie kan desgewenst een reeks gezichtsgerelateerde kenmerken extraheren, zoals de houding van het hoofd, geslacht, leeftijd, emotie, gezichtsbeharing en of er een bril gedragen wordt. Deze kenmerken zijn algemene voorspellingen, geen werkelijke classificaties. 

> [!NOTE]
> De functie voor gezichtsdetectie is ook beschikbaar via de [Computer Vision-service](../computer-vision/overview.md). Als u echter meer Face-bewerkingen wilt uitvoeren, zoals Identificeren, Verifiëren, Vergelijkbaar zoeken of Groeperen, moet u in plaats daarvan deze Face-service gebruiken.

![Een afbeelding van een vrouw en een man met rechthoeken getekend rond de gezichten, en de leeftijd en het geslacht weergegeven](./Images/Face.detection.jpg)

Zie het artikel over concepten van [gezichtsdetectie](concepts/face-detection.md) voor meer informatie over gezichtsdetectie. Zie ook de referentiedocumentatie over de [Detectie-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="face-verification"></a>Gezichtsverificatie

De Verify-API bouwt voort op Detectie en beantwoordt de vraag: 'Zijn deze twee afbeeldingen dezelfde persoon?'. Verificatie wordt ook wel 'een-op-een' genoemd, omdat de testafbeelding wordt vergeleken met slechts één ingeschreven sjabloon. Verificatie kan worden gebruikt in scenario's voor identiteitsverificatie of toegangsbeheer om te controleren of een afbeelding overeenkomt met een eerder vastgelegde afbeelding (zoals van een foto van een door de overheid uitgegeven id-kaart). Zie voor meer informatie de handleiding over concepten van [Gezichtsherkenning](concepts/face-recognition.md) of de referentiedocumentatie over de [Verificatie API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="face-identification"></a>Gezichtsidentificatie

De Identify-API begint ook met Detectie en beantwoordt de vraag 'Kan dit gedetecteerde gezicht worden gematcht met een geregistreerd gezicht in een database?' Omdat het lijkt op zoeken in gezichtsherkenning, wordt ook wel 'een-op-veel'-matching genoemd. Mogelijke overeenkomsten worden geretourneerd op basis van hoe nauwkeurig de testsjabloon met het gedetecteerde gezicht overeenkomt met elk van de ingeschreven sjablonen.

In de volgende afbeelding ziet u een voorbeeld van een database met de naam `"myfriends"`. Elke groep kan maximaal 1 miljoen verschillende persoonsobjecten bevatten. Voor elk persoonsobject kunnen maximaal 248 gezichten zijn geregistreerd.

![Een raster met drie kolommen voor verschillende personen, elk met drie rijen van afbeeldingen van gezichten](./Images/person.group.clare.jpg)

Nadat u een database hebt gemaakt en getraind, kunt u een identificatie uitvoeren op basis van de groep en een nieuw gedetecteerd gezicht. Als het gezicht wordt geïdentificeerd als een persoon in de groep, wordt het persoonsobject geretourneerd.

Zie de handleiding over concepten van [Gezichtsherkenning](concepts/face-recognition.md) of de referentiedocumentatie over de [Identify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) voor meer informatie over persoonsidentificatie.

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

De Find Similar API vergelijkt de gezichten van het doel en van een set kandidaten en maakt vervolgens een nieuwe, kleinere set op basis van welke gezichten het meest op het doel lijken. Dit is handig voor het zoeken van een gezicht binnen een afbeelding. 

Er worden twee werkmodi ondersteund: **matchPerson** en **matchFace**. De modus **matchPerson** retourneert vergelijkbare gezichten nadat er een filter is toegepast op basis van een persoon. Hiervoor wordt de [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) gebruikt. In de modus **matchFace** wordt het filter voor dezelfde persoon genegeerd. Er wordt een lijst met vergelijkbare gezichten geretourneerd die al dan niet tot dezelfde persoon behoren.

In het onderstaande voorbeeld ziet u het doelgezicht:

![Een glimlachende vrouw](./Images/FaceFindSimilar.QueryFace.jpg)

En deze afbeeldingen zijn de kandidaatgezichten:

![Vijf afbeeldingen van glimlachende mensen Afbeelding a en b zijn van dezelfde persoon.](./Images/FaceFindSimilar.Candidates.jpg)

Om vier vergelijkbare gezichten te vinden, retourneert de modus **matchPerson** a en b, die dezelfde persoon aanduiden als het doelgezicht. De modus **matchFace** retourneert a, b, c en d&mdash;. Precies vier kandidaten, zelfs als sommige gezichten niet dezelfde persoon zijn als het doel of er nauwelijks op lijken. Zie de handleiding over concepten van [Gezichtsherkenning](concepts/face-recognition.md) of de referentiedocumentatie over de [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) voor meer informatie.

## <a name="face-grouping"></a>Gezichtsgroepering

De Groeperings-API verdeelt een set onbekende gezichten in groepen op basis van gelijkenis. Elke groep is een niet-aaneengesloten juiste subset van de oorspronkelijke set van gezichten. Alle gezichten in een groep behoren waarschijnlijk tot dezelfde persoon. Er kunnen verschillende groepen voor één persoon zijn. De groepen worden onderscheiden door een andere factor, bijvoorbeeld de gezichtsuitdrukking. Zie de handleiding over concepten van [Gezichtsherkenning](concepts/face-recognition.md) of de referentiedocumentatie over de [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) voor meer informatie.


## <a name="sample-apps"></a>Voorbeeld-apps

In de volgende voorbeeldtoepassingen ziet u een aantal manieren om de Face-service te gebruiken:

- [Face-API: Windows-clientbibliotheek en voorbeeld](https://github.com/Microsoft/Cognitive-Face-Windows) is een WPF-app in verschillende scenario's voor gezichtsdetectie, -analyse en -identificatie.
- [FamilyNotes UWP-app](https://github.com/Microsoft/Windows-appsample-familynotes) is een UWP-app (Universal Windows Platform) die gebruikmaakt van gezichtsidentificatie gecombineerd met spraak, Cortana, inkt en camera in een familiescenario waarin notities worden gedeeld.

## <a name="data-privacy-and-security"></a>Gegevensprivacy en -beveiliging

Zoals geldt voor alle resources van Cognitive Services, dienen ontwikkelaars die de Face-service gebruiken op de hoogte te zijn van het beleid van Microsoft inzake klantgegevens. Zie de [pagina Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) (Engelstalig) in het Microsoft Trust Center voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Volg een quickstart over het coderen van de basisonderdelen van een gezichtsherkennings-app in de taal van uw keuze.

- [Quickstart voor de clientbibliotheek](quickstarts/client-libraries.md).
