---
title: Best practices voor het toevoegen van gebruikers aan een Face-service
titleSuffix: Azure Cognitive Services
description: Meer informatie over de Face-inschrijvingsprocedure om gebruikers te registreren bij een service voor gezichtsherkenning.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/19/2021
ms.author: pafarley
ms.openlocfilehash: 183983779c09658d8d6f609319a127b1f406452d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728424"
---
# <a name="best-practices-for-adding-users-to-a-face-service"></a>Best practices voor het toevoegen van gebruikers aan een Face-service

Als u de face Cognitive Services-API voor gezichtsverificatie of -identificatie wilt gebruiken, moet u gezichten registreren bij een **LargePersonGroup** of een vergelijkbare gegevensstructuur. Deze diepgaande best practices voor het verzamelen van zinvolle toestemming van gebruikers en voorbeeldlogica voor het maken van inschrijvingen van hoge kwaliteit die de nauwkeurigheid van de herkenning optimaliseren. 

## <a name="meaningful-consent"></a>Betekenisvolle toestemming 

Een van de voornaamste doelen van een inschrijvingstoepassing voor gezichtsherkenning is om gebruikers de mogelijkheid te bieden toestemming te geven voor het gebruik van afbeeldingen van hun gezicht voor specifieke doeleinden, zoals toegang tot een werksite. Omdat technologieën voor gezichtsherkenning kunnen worden beschouwd als het verzamelen van gevoelige persoonlijke gegevens, is het met name belangrijk om te vragen om toestemming op een manier die zowel transparant als respectvol is. Toestemming is betekenisvol vol voor gebruikers wanneer deze hen in staat stelt voor zichzelf de juiste beslissing te nemen.   

Op basis van Microsoft-gebruikersonderzoek, de verantwoordelijke AI-principes van Microsoft, en [extern onderzoek](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf), is vastgesteld dat toestemming betekenisvol is wanneer deze de gebruikers die zich inschrijven bij de technologie, het volgende biedt:

* Besef: Het mag nooit in twijfel worden getrokken in welke situaties gebruikers wordt gevraagd om hun gezichtssjabloon of inschrijvingsfoto's op te geven. 
* Begrip: Gebruikers moeten in staat zijn om in hun eigen woorden te beschrijven wat van hen wordt gevraagd, door wie, waarvoor, en met welke garanties. 
* Keuzevrijheid: Gebruikers mogen niet worden gedwongen of gemanipuleerd bij de keuze om toestemming te geven en zich in te schrijven voor gezichtsherkenning. 
* Beheer: Gebruikers moeten hun toestemming op elk gewenst moment kunnen intrekken en hun gegevens kunnen verwijderen. 

Deze sectie biedt richtlijnen voor het ontwikkelen van een inschrijvingstoepassing voor gezichtsherkenning. Deze richtlijnen zijn ontwikkeld op basis van Microsoft-gebruikersonderzoek in de context van het inschrijven van individuen voor gezichtsherkenning voor het samenstellen van een vermelding. Daarom zijn deze aanbevelingen mogelijk niet van toepassing op alle oplossingen voor gezichtsherkenning. Verantwoordelijk gebruik van de Face-API is sterk afhankelijk van de specifieke context waarin deze is geïntegreerd. De prioritering en toepassing van deze aanbevelingen moeten daarom worden aangepast aan uw scenario. 

> [!NOTE]
> Het is uw verantwoordelijkheid om uw inschrijvingstoepassing op één lijn te brengen met de toepasselijke wettelijke vereisten in uw jurisdictie, en om alle procedures voor gegevensverzameling en -verwerking nauwkeurig te weerspiegelen.

## <a name="application-development"></a>Toepassingen ontwikkelen 

Voordat u begint met een inschrijvingsstroom moet u bedenken hoe de toepassing die u bouwt, kan voldoen aan de beloften die u doet aan gebruikers over hoe hun gegevens worden beveiligd. De volgende aanbevelingen kunnen u helpen om een inschrijvingservaring te bouwen die verantwoordelijke benaderingen omvat om persoonlijke gegevens te beveiligen, de privacy van gebruikers te beheren, en ervoor te zorgen dat de toepassing toegankelijk is voor alle gebruikers.  

|Categorie | Aanbevelingen |
|---|---|
|Hardware | Houd rekening met de kwaliteit van de camera van het inschrijvingsapparaat. |
|Aanbevolen inschrijvingsfuncties | Neem een aanmeldingsstap met meervoudige verificatie op. </br></br>Koppel gebruikersgegevens, zoals een alias of id, aan de bijbehorende gezichtssjabloon-id uit de Face-API (dit heet een persoons-id). Deze toewijzing is nodig om de inschrijving van een gebruiker op te halen en te beheren. Opmerking: de persoons-id moet worden behandeld als een geheim in de toepassing.</br></br>Stel een geautomatiseerd proces op om alle inschrijvingsgegevens te verwijderen, inclusief de gezichtssjablonen en inschrijvingsfoto's van personen die geen gebruikers meer zijn van de gezichtsherkenningstechnologie, zoals voormalige werknemers. </br></br>Vermijd automatische inschrijving, omdat dit een gebruiker niet het besef en begrip, noch de keuzevrijheid of het beheer biedt die zijn aanbevolen voor het verkrijgen van toestemming. </br></br>Vraag gebruikers om toestemming om de afbeeldingen op te slaan die zijn gebruikt voor de inschrijving. Dit is handig wanneer er een modelupdate is, omdat ongeveer eens in de 10 maanden nieuwe inschrijvingsfoto's zijn vereist om opnieuw in te schrijven bij het nieuwe model. Als de originele afbeeldingen niet zijn opgeslagen, moeten gebruikers het inschrijvingsproces helemaal opnieuw doorlopen.</br></br>Geef gebruikers de optie om ervoor te kiezen hun foto's niet op te slaan in het systeem. Om de keuze duidelijker te maken kunt u een tweede scherm voor toestemmingsaanvragen toevoegen voor het opslaan van de inschrijvingsfoto's. </br></br>Als de foto's zijn opgeslagen, maakt u een geautomatiseerd proces om alle gebruikers opnieuw in te schrijven wanneer er een modelupdate plaatsvindt. Gebruikers die hun inschrijvingsfoto's hebben opgeslagen, moeten zich niet opnieuw inschrijven. </br></br>Maak een app-functie die aangewezen beheerders toestaat bepaalde kwaliteitsfilters te overschrijven als een gebruiker problemen ondervindt met inschrijven. |
|Beveiliging | Cognitive Services volgen [best practices](../cognitive-services-virtual-networks.md?tabs=portal) voor het versleutelen van data-at-rest en data-in-transit van gebruikers. Hieronder vindt u andere procedures die u kunnen helpen de beveiligingsrisico's die u aan gebruikers maakt tijdens de registratie te verbeteren. </br></br>Neem beveiligingsmaatregelen om ervoor te zorgen dat niemand op enig moment gedurende de inschrijving toegang heeft tot de persoons-id. Opmerking: De persoons-id moet worden behandeld als een geheim in het inschrijvingssysteem. </br></br>Gebruik [op rollen gebaseerd toegangsbeheer](../../role-based-access-control/overview.md) met Cognitive Services. </br></br>Gebruik tokenverificatie en/of SAS (handtekeningen voor gedeelde toegang) in plaats van sleutels en geheimen voor toegang tot resources zoals databases. Als u gebruikmaakt van aanvraag- of SAS-tokens, kunt u beperkte toegang verlenen, zonder de accountsleutels te compromitteren, en kunt u een verlooptijd opgeven voor het token. </br></br>Sla geheimen, sleutels of wachtwoorden nooit op in de app. |
|Gebruikersprivacy |Bied een bereik aan inschrijvingsopties aan om privacyproblemen op verschillende niveaus op te lossen. Verplicht personen niet om hun persoonlijke apparaten in te schrijven bij een gezichtsherkenningssysteem. </br></br>Bied gebruikers de mogelijkheid om, op elk gewenst moment en om welke reden dan ook, zich opnieuw in te schrijven, toestemming in te trekken, en gegevens te verwijderen. |
|Toegankelijkheid |Volg standaarden voor toegankelijkheid (bijvoorbeeld [ADA](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) of [W3C](https://www.w3.org/TR/WCAG21/)) om ervoor te zorgen dat de toepassing bruikbaar is voor gebruikers met verminderde mobiliteit of een visuele handicap. |

## <a name="next-steps"></a>Volgende stappen  

Volg de handleiding [Een inschrijvings-app bouwen](build-enrollment-app.md) om aan de slag te gaan met een voorbeeld van een inschrijvings-app. Pas deze vervolgens aan de behoeften van uw product aan, of schrijf een eigen app.