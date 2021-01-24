---
title: Een demo-app maken in Azure Portal
titleSuffix: Azure Cognitive Search
description: Voer de wizard Demo-app maken (preview) uit om HTML-pagina's en een script te genereren voor een operationele web-app. De pagina bevat een zoekbalk, resultaatgebied, zijbalk en typeahead-ondersteuning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/23/2021
ms.openlocfilehash: 590afe4c396942c5179826cd831908e37f48c3e4
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2021
ms.locfileid: "98745747"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>Quickstart: Een demo-app maken in de portal (Azure Cognitive Search)

Gebruik de wizard **Demo-app maken** van Azure Portal om een te downloaden web-app in localhost-stijl te genereren die in een browser wordt uitgevoerd. Afhankelijk van de configuratie is de gegenereerde app operationeel bij het eerste gebruik en bevat deze een live alleen-lezenverbinding met een externe index. Een standaard-app kan een zoekbalk, resultaatgebied, zijbalkfilters en typeahead-ondersteuning bevatten.

De demo-app kan u helpen bij het visualiseren van de werking van een index in een client-app, maar is niet bedoeld voor productiescenario's. Client-apps moeten logica voor beveiliging, foutafhandeling en hosting bevatten die niet beschikbaar is op de gegenereerde HTML-pagina. Wanneer u klaar bent om een client-app te maken, raadpleegt u [Uw eerste Zoek-app maken met behulp van de .NET-SDK-](tutorial-csharp-create-first-app.md) voor de volgende stappen.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/)

+ Een Azure Cognitive Search-service. [Maak een service](search-create-service-portal.md) of [zoek een bestaande service](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in uw huidige abonnement. U kunt een gratis service voor deze quickstart gebruiken. 

+ [Microsoft Edge (nieuwste versie)](https://www.microsoft.com/edge) of Google Chrome.

+ Een [zoekindex](search-what-is-an-index.md) om te gebruiken als basis van uw gegenereerde toepassing. 

  In deze quickstart wordt gebruikgemaakt van de ingebouwde vastgoedsamplegegevens en -index omdat deze miniatuurafbeeldingen bevat (de wizard ondersteunt het toevoegen van afbeeldingen aan de resultaatpagina). U maakt de index die in deze oefening wordt gebruikt door de wizard **Gegevens importeren** uit te voeren en de gegevensbron *realestate-us-sample* te kiezen.

  :::image type="content" source="media/search-create-app-portal/import-data-realestate.png" alt-text="gegevensbronpagina voor samplegegevens" border="false":::

Wanneer de index klaar is voor gebruik, gaat u verder met de volgende stap.

## <a name="start-the-wizard"></a>De wizard starten

1. Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com/).

1. [Zoek uw zoekservice](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) en selecteer op de pagina Overzicht in de koppelingen midden op de pagina **Indexen**. 

1. Kies *realestate-us-sample-index* in de lijst met bestaande indexen.

1. Selecteer bovenaan de indexpagina **Demo-app maken (preview)** om de wizard te starten.

1. Selecteer op de eerste pagina van de wizard **Cross Origin Resource Sharing (CORS)** om CORS-ondersteuning toe te voegen aan uw indexdefinitie. Deze stap is optioneel, maar zonder kan uw lokale web-app kan geen verbinding maken met de externe index.

## <a name="configure-search-results"></a>Zoekresultaten configureren

De wizard biedt een basisindeling voor weergegeven zoekresultaten die ruimte bevat voor een miniatuurafbeelding, titel en beschrijving. Ter ondersteuning van elk van deze elementen is er een veld in uw index dat de gegevens levert. 

1. Kies bij Miniatuur het veld *miniatuur* in de index *realestate-us-sample*. Deze sample bevat afbeeldingsminiaturen in de vorm van afbeeldingen met URL-adres die zijn opgeslagen in het veld *miniatuur*. Als uw index geen afbeeldingen bevat, laat u dit veld leeg.

1. Kies bij Titel een veld dat de uniciteit van elk document kenbaar maakt. In deze sample is de vermeldings-id een zinnige selectie.

1. Kies bij Beschrijving een veld dat details biedt waarmee iemand kan bepalen of er moet worden geklikt op het desbetreffende document.

   :::image type="content" source="media/search-create-app-portal/configure-results.png" alt-text="resultaten voor voorbeeldgegevens configureren" border="false":::

## <a name="add-a-sidebar"></a>Een zijbalk toevoegen

De zoekservice ondersteunt facetnavigatie, vaak weergegeven als zijbalk. Facetten zijn gebaseerd op velden die filterbaar en facetbaar zijn, zoals uitgedrukt in het indexschema.

In Azure Cognitive Search is facetnavigatie een cumulatieve filterervaring. Als u binnen een categorie meerdere filters selecteert, bijvoorbeeld Seattle en Bellevue binnen Plaats, breidt u de resultaten uit. Als u in meerdere categorieën meerdere filters selecteert, beperkt u de resultaten.

> [!TIP]
> U kunt het volledige indexschema bekijken in de portal. Zoek naar de koppeling **Indexdefinitie (JSON)** op de overzichtspagina van elke index. Velden die in aanmerking komen voor facetnavigatie hebben de kenmerken filterbaar:waar en facetbaar:waar.

1. Selecteer in de wizard het tabblad **zijbalk** boven aan de pagina. Er wordt een lijst weer geven met alle velden die zijn toegeschreven als filterbaar en bruikbaar in de index.

1. Accepteer de huidige selectie van facet velden en ga door naar de volgende pagina.

## <a name="add-typeahead"></a>Typeahead toevoegen

De typeahead-functionaliteit is beschikbaar in de vorm van automatisch aanvullen en querysuggesties. De wizard ondersteunt querysuggesties. Op basis van toetsinvoer van de gebruiker retourneert de zoekservice een lijst voltooide querytekenreeksen die als invoer kunnen worden geselecteerd.

Suggesties worden ingeschakeld voor specifieke velddefinities. De wizard biedt opties voor het configureren van de hoeveelheid informatie die wordt opgenomen in een suggestie. 

In de volgende schermopname ziet u de opties in de wizard met daarnaast een weergegeven pagina in de app. U kunt zien hoe veldselecties worden gebruikt en hoe Veldnaam weergeven wordt gebruikt voor het opnemen of weglaten van labels in de suggestie.

:::image type="content" source="media/search-create-app-portal/suggestions.png" alt-text="Configuratie van querysuggesties":::

## <a name="add-suggestions"></a>Suggesties toevoegen

Suggesties verwijzen naar Automatische query prompts die zijn gekoppeld aan het zoekvak. Cognitive Search ondersteunt twee: automatisch *aanvullen* van een gedeeltelijk ingevoerde zoek term en *suggesties* voor een vervolg keuzelijst met mogelijke overeenkomende documenten op basis van.

De wizard ondersteunt suggesties en de velden die voorgestelde resultaten kunnen bieden, zijn afgeleid van een [`Suggesters`](index-add-suggesters.md) construct in de index:

```JSON
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
        "number",
        "street",
        "city",
        "region",
        "postCode",
        "tags"
      ]
```

1. Selecteer in de wizard het tabblad **suggesties** boven aan de pagina. Er wordt een lijst weer geven met alle velden die zijn opgegeven in het index schema als suggesties providers.

1. De huidige selectie accepteren en door gaan naar de volgende pagina.

## <a name="create-download-and-execute"></a>Maken, downloaden en uitvoeren

1. Selecteer **demo-app maken** onder aan de pagina om het HTML-bestand te genereren.

1. Selecteer desgevraagd **Uw app downloaden** om het bestand te downloaden.

1. Open het bestand en klik op de zoek knop. Met deze actie wordt een query uitgevoerd. Dit kan een lege query ( `*` ) zijn die een wille keurige resultatenset retourneert. De pagina moet er ongeveer uitzien als in de volgende scherm afbeelding. Voer een term in en gebruik filters om de resultaten te beperken. 

De onderliggende index bestaat uit fictieve gegenereerde gegevens die zijn gedupliceerd in andere documenten en beschrijvingen die soms niet overeenkomen met de afbeelding. U kunt een samenhangende ervaring verwachten als u een app maakt op basis van uw eigen indexen.

:::image type="content" source="media/search-create-app-portal/run-app.png" alt-text="De app uitvoeren":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

De demo-app is handig voor het prototypen, omdat u een eindgebruikers ervaring kunt simuleren zonder Java script-of front-end-code te hoeven schrijven. Begin met facet navigatie voor meer informatie over front-end-functies:

> [!div class="nextstepaction"]
> [Een facet filter maken](search-filters-facets.md)