---
title: Release opmerkingen bij de SDK voor insluitende lezers
titleSuffix: Azure Cognitive Services
description: Meer informatie over wat er nieuw is in de Inge sloten Reader java script SDK.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93133531"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>Release opmerkingen bij insluitende Reader java script SDK

## <a name="version-110"></a>Versie 1.1.0

Deze release bevat nieuwe functies, fixes voor beveiligings problemen, oplossingen voor oplossingen, updates voor code voorbeelden en configuratie opties.

#### <a name="new-features"></a>Nieuwe functies

* Opslaan en laden van gebruikers voorkeuren in verschillende browsers en apparaten inschakelen
* Configureren van standaard weergave opties inschakelen
* Optie toevoegen om de taal van de vertaling in te stellen, woord omzetting in te scha kelen en document vertalingen in te scha kelen bij het starten van insluitende lezer
* Ondersteuning toevoegen voor het configureren van hardop voor lezen via opties
* Mogelijkheid voor het uitschakelen van de eerste uitvoerings ervaring toevoegen
* ImmersiveReaderView toevoegen voor UWP

#### <a name="improvements"></a>Verbeteringen

* De voor beeld-HTML van de Android-code bijwerken zodat deze werkt met de nieuwste SDK
* Start reactie bijwerken om het aantal verwerkte tekens te retour neren
* Code voorbeelden bijwerken om v 1.1.0 te gebruiken
* Niet toestaan dat launchAsync wordt aangeroepen wanneer al wordt geladen
* Controleren op ongeldige inhoud door berichten te negeren waarbij de gegevens geen teken reeks zijn
* Inbellen naar een venster in een if-component om de browser ondersteuning van Promise te controleren

#### <a name="fixes"></a>Oplossingen

* Dependabot corrigeren door garens te verwijderen uit gitignore
* Beveiligings probleem oplossen door pug naar v 3.0.0 te upgraden in Quick Start-nodejs code sample
* Meerdere beveiligings problemen oplossen door de jest-en type script-pakketten bij te werken
* Een beveiligings probleem oplossen door micro soft. Identity model. clients. ActiveDirectory te upgraden naar v 5.2.0

<br>

## <a name="version-100"></a>Versie 1.0.0

Deze release bevat belang rijke wijzigingen, nieuwe functies, code voorbeeld verbeteringen en oplossingen voor problemen.

#### <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

* Azure AD-token en-subdomein vereisen en de tokens die in vorige versies worden gebruikt
* Stel CookiePolicy in op disabled. Het bewaren van gebruikers voorkeuren is standaard uitgeschakeld. De lezer start elke keer met de standaard instellingen, tenzij CookiePolicy is ingesteld op ingeschakeld.

#### <a name="new-features"></a>Nieuwe functies

* Ondersteuning toevoegen om cookies in of uit te scha kelen
* Voor beeld van Quick Start-code voor Android-Kotlin toevoegen
* Voor beeld van een Quick Start-code voor Android-Java toevoegen
* Node.js voor beeld van snelle start code toevoegen

#### <a name="improvements"></a>Verbeteringen

* Node.js geavanceerde README.md bijwerken
* Voor beeld van python-code wijzigen van Geavanceerd in snel starten
* Voor beeld van iOS SWIFT-code verplaatsen naar JS/samples
* Code voorbeelden bijwerken om v 1.0.0 te gebruiken

#### <a name="fixes"></a>Oplossingen

* Correctie voor het voor beeld van Node.js geavanceerde code
* Ontbrekende bestanden toevoegen voor Geavanceerd-csharp-meerdere resources
* En-US verwijderen uit hyper links

<br>

## <a name="version-003"></a>Versie 0.0.3

Deze release bevat nieuwe functies, verbeteringen in code voorbeelden, oplossingen voor beveiligings problemen en oplossingen voor oplossingen.

#### <a name="new-features"></a>Nieuwe functies

* Voor beeld van iOS SWIFT-code toevoegen
* Voorbeeld code van C# toevoegen die het gebruik van meerdere resources demonstreert 
* Ondersteuning toevoegen om de wissel knop volledig scherm uit te scha kelen
* Ondersteuning toevoegen om de knop afsluiten van de invoeg toepassing voor sluiten te verbergen
* Een call back-functie toevoegen die door de hosttoepassing kan worden gebruikt bij het afsluiten van de insluitende lezer
* Code voorbeelden bijwerken om Azure Active Directory verificatie te gebruiken

#### <a name="improvements"></a>Verbeteringen

* Een voor beeld van C#-code bijwerken om een Word-document op te nemen
* Code voorbeelden bijwerken om v 0.0.3 te gebruiken

#### <a name="fixes"></a>Oplossingen

* Upgrade lodash naar versie 4.17.14 om beveiligings problemen op te lossen
* De C# MSAL-bibliotheek bijwerken om beveiligings problemen op te lossen
* Upgrade Mixin-diep naar versie 1.3.2 om beveiligings problemen op te lossen
* Upgrade jest, webpack en webpack-cli die kwets bare versies van set-value en Mixin-diep gebruiken om beveiligings problemen op te lossen

<br>

## <a name="version-002"></a>Versie 0.0.2

Deze release bevat nieuwe functies, verbeteringen in code voorbeelden, oplossingen voor beveiligings problemen en oplossingen voor oplossingen.

#### <a name="new-features"></a>Nieuwe functies

* Voor beeld van python Advanced-code toevoegen
* Voor beeld van Java-code voor snelle start toevoegen
* Eenvoudig code voorbeeld toevoegen

#### <a name="improvements"></a>Verbeteringen

* Wijzig de naam van ResourceName in cogSvcsSubdomain
* Geheimen uit code verplaatsen en omgevings variabelen gebruiken
* Code voorbeelden bijwerken om v 0.0.2 te gebruiken

#### <a name="fixes"></a>Oplossingen

* Knop voor toegankelijkheids fouten van insluitende lezer herstellen
* Defect schuiven herstellen
* Handvatten-pakket bijwerken naar versie 4.1.2 om beveiligings problemen op te lossen
* Herstelt fouten in SDK-eenheids tests
* Lost java script Internet Explorer 11-compatibiliteits fouten op
* Updates SDK-url's

<br>

## <a name="version-001"></a>Versie 0.0.1

De eerste versie van de Java script-SDK voor insluitende Reader.

* Invoeg toepassing voor Inge sloten lezer java script-SDK
* Ondersteuning toevoegen om de taal van de gebruikers interface op te geven
* Een time-out toevoegen om te bepalen wanneer de functie launchAsync zou moeten mislukken met een time-outfout
* Ondersteuning toevoegen om de z-index van de insluitende Reader iframe op te geven
* Ondersteuning toevoegen om een webweergave-tag te gebruiken in plaats van een IFRAME, voor compatibiliteit met Chrome-apps
* SDK-eenheids tests toevoegen
* Node.js geavanceerde code voorbeeld toevoegen
* Voor beeld van C#-geavanceerde code toevoegen
* Voor beeld van C# Quick Start-code toevoegen
* Pakket configuratie, garens en andere build-bestanden toevoegen
* Git-configuratie bestanden toevoegen
* README.md-bestanden toevoegen aan code voorbeelden en SDK
* MIT-licentie toevoegen
* Inzender instructies toevoegen
* SVG-assets voor een statisch pictogram toevoegen

## <a name="next-steps"></a>Volgende stappen

Aan de slag met Insluitende lezer:

* Lees de [naslaginformatie over de clientbibliotheek van Insluitende lezer](./reference.md)
* Maak kennis met de [clientbibliotheek voor Insluitende lezer op GitHub](https://github.com/microsoft/immersive-reader-sdk)
