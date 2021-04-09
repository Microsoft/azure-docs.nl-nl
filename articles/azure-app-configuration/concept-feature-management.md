---
title: Onderdeel beheer met Azure-app configuratie
description: Functies in-en uitschakelen met behulp van Azure-app configuratie
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.custom: devx-track-dotnet
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: cfd676092bcaede58909a3ec1eefeabb4c80f86b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930225"
---
# <a name="feature-management-overview"></a>Overzicht van functie beheer

Normaal gesp roken moet u voor het verzenden van een nieuwe toepassings functie een volledige herimplementatie van de toepassing zelf uitvoeren. Voor het testen van een functie zijn vaak meerdere implementaties van de toepassing vereist.  Elke implementatie kan de functie wijzigen of de functie beschikbaar maken voor verschillende klanten om te testen.  

Feature Management is een moderne software-ontwikkelings praktijk die de functie release van code-implementatie loskoppelt en de beschik baarheid van functies op aanvraag snel kunt wijzigen. Er wordt gebruikgemaakt van een techniek die *functie vlaggen* (ook wel *functie* *Schakel opties, functie switches*, enzovoort) gebruikt om de levens cyclus van een functie dynamisch te beheren.

Met functie beheer kunnen ontwikkel aars de volgende problemen oplossen:

* **Code Branch Management**: gebruik functie vlaggen om nieuwe toepassings functionaliteit te verpakken die momenteel wordt ontwikkeld. Deze functionaliteit is standaard verborgen. U kunt de functie veilig verzenden, zelfs als deze nog niet is voltooid, en deze blijft in productie. Met deze methode, een *donkere implementatie* genoemd, kunt u al uw code aan het einde van elke ontwikkel cyclus vrijgeven. Het is niet meer nodig om code vertakkingen in meerdere ontwikkel cycli te onderhouden, omdat voor een bepaalde functie meer dan één cyclus vereist is.
* **Test in productie**: gebruik functie vlaggen om vroegtijdige toegang te verlenen tot nieuwe functionaliteit in de productie. U kunt bijvoorbeeld de toegang tot team leden of interne bèta testers beperken. Deze gebruikers hebben de volledige betrouw baarheid van de productie in plaats van een gesimuleerde of gedeeltelijke ervaring in een test omgeving.
* **Flighting**: gebruik functie vlaggen om een incrementele nieuwe functionaliteit voor eind gebruikers te implementeren. U kunt eerst een klein percentage van uw gebruikers populatie instellen en dit percentage geleidelijk verhogen.
* **Instant Kill-Switch**: functie vlaggen bieden een inherenend veiligheids netwerk voor het vrijgeven van nieuwe functionaliteit. U kunt toepassings functies in-en uitschakelen zonder dat u code hoeft te implementeren. Als dat nodig is, kunt u een functie snel uitschakelen zonder uw toepassing opnieuw te bouwen en opnieuw te implementeren.
* **Selectieve activering**: gebruik functie vlaggen om uw gebruikers te segmenteren en een specifieke set functies aan elke groep te leveren. Mogelijk hebt u een functie die alleen werkt in een bepaalde webbrowser. U kunt een functie vlag definiëren zodat alleen gebruikers van die browser de functie kunnen zien en gebruiken. Met deze methode kunt u de ondersteunde browser lijst later eenvoudig uitbreiden zonder dat u code wijzigingen hoeft aan te brengen.

## <a name="basic-concepts"></a>Basisbegrippen

Hier volgen enkele nieuwe voor waarden met betrekking tot het beheer van functies:

* **Functie vlag**: een functie vlag is een variabele met de binaire status *in* of *uit*. De functie vlag heeft ook een bijbehorend code blok. De status van de functie vlag geeft aan of het code blok wordt uitgevoerd.
* **Functie beheer**: een functie beheerder is een toepassings pakket dat de levens cyclus van alle functie vlaggen in een toepassing verwerkt. Functie beheer biedt ook aanvullende functionaliteit, waaronder de functie vlaggen voor caching en het bijwerken van de statussen.
* **Filter**: een filter is een regel voor het evalueren van de status van een functie vlag. Mogelijke filters zijn gebruikers groepen, apparaat-of browser typen, geografische locaties en tijd Vensters.

Een efficiënte implementatie van feature Management bestaat uit ten minste twee onderdelen die in overleg werken:

* Een toepassing die gebruikmaakt van functie vlaggen.
* Een afzonderlijke opslag plaats waarin de functie vlaggen en de huidige statussen worden opgeslagen.

## <a name="using-feature-flags-in-your-code"></a>Functie vlaggen gebruiken in uw code

Het basis patroon voor het implementeren van functie vlaggen in een toepassing is eenvoudig. Een functie vlag is een Booleaanse status variabele die een voorwaardelijke instructie in uw code beheert:

```csharp
if (featureFlag) {
    // Run the following code
}
```

U kunt de waarde van `featureFlag` statisch instellen.

```csharp
bool featureFlag = true;
```

U kunt de status van de vlag evalueren op basis van bepaalde regels:

```csharp
bool featureFlag = isBetaUser();
```

U kunt de voorwaardelijke actie voor het instellen van het toepassings gedrag voor een van beide statussen uitbreiden:

```csharp
if (featureFlag) {
    // This following code will run if the featureFlag value is true
} else {
    // This following code will run if the featureFlag value is false
}
```

## <a name="feature-flag-repository"></a>Opslag plaats voor functie vlaggen

Als u functie vlaggen effectief wilt gebruiken, moet u alle functie vlaggen Externalize die in een toepassing worden gebruikt. Hierdoor kunt u de status van de functie vlaggen wijzigen zonder de toepassing zelf te wijzigen en opnieuw te implementeren.

Azure-app configuratie biedt een centrale opslag plaats voor functie vlaggen. U kunt dit gebruiken om verschillende soorten functie vlaggen te definiëren en hun status snel en met vertrouwen te manipuleren. Vervolgens kunt u de app-configuratie bibliotheken voor verschillende programmeer taal raamwerken gebruiken om gemakkelijk toegang te krijgen tot deze functie vlaggen vanuit uw toepassing.

[Gebruik functie vlaggen in een ASP.net core-app](./use-feature-flags-dotnet-core.md) om te zien hoe de .net core-app configuratie provider en onderdelen beheer bibliotheken samen worden gebruikt om functie vlaggen voor uw ASP.NET-webtoepassing te implementeren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Functie vlaggen toevoegen aan een ASP.NET Core web-app](./quickstart-feature-flag-aspnet-core.md)  
