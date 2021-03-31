---
title: Functie-evaluatie-persoonlijker
titleSuffix: Azure Cognitive Services
description: Wanneer u een evaluatie uitvoert in uw Personaler-resource vanuit het Azure Portal, bevat Personaler informatie over de functies van context en acties die van invloed zijn op het model.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: c0e47a2943cf8c934d201f76aefc41868adf0b25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "87127720"
---
# <a name="feature-evaluation"></a>Functie-evaluatie

Wanneer u een evaluatie uitvoert in uw Personaler-resource vanuit het [Azure Portal](https://portal.azure.com), bevat personaler informatie over de functies van context en acties die van invloed zijn op het model. 

Dit is handig om het volgende te doen:

* Stel dat er aanvullende functies zijn die u kunt gebruiken, en krijg inspiratie van welke functies belang rijker zijn in het model.
* Bekijk welke functies niet belang rijk zijn en of u deze kunt verwijderen of verder wilt analyseren wat het gebruik kan beïnvloeden.
* Geef richt lijnen op voor redactionele of curator-teams over nieuwe inhoud of producten die in de catalogus worden gebracht.
* Veelvoorkomende problemen en fouten oplossen die zich voordoen bij het verzenden van functies naar persoonlijkere.

De belangrijkste functies hebben betere gewichten in het model. Omdat deze functies een sterker gewicht hebben, zijn ze vaak aanwezig als persoonlijker betere beloningen verkrijgt.

## <a name="getting-feature-importance-evaluation"></a>Evaluatie van functie urgentie ophalen

Als u de belang rijke resultaten van de functie wilt bekijken, moet u een evaluatie uitvoeren. De evaluatie maakt labels voor menselijke Lees bare onderdelen op basis van de functie namen die tijdens de evaluatie periode zijn waargenomen.

De resulterende informatie over de functie urgentie is het huidige online model van Personaler. De evaluatie analyseert het belang van de functie van het model dat is opgeslagen op de eind datum van de evaluatie periode, nadat alle training tijdens de evaluatie is uitgevoerd, met het huidige online learning-beleid. 

De belang rijke resultaten van de functie vertegenwoordigen geen andere beleids regels en modellen die tijdens de evaluatie zijn getest of gemaakt.  De evaluatie bevat geen functies die na het einde van de evaluatie periode naar Personaler worden verzonden.

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>De evaluatie van de functie urgentie interpreteren

Personaler evalueert functies door ' groepen ' te maken van functies met een vergelijk bare prioriteit. Een groep kan worden beschouwd als een algemeen sterker belang dan andere, maar binnen de groep is de volg orde van de functies alfabetisch.

Informatie over elke functie omvat:

* Hiermee wordt aangegeven of de functie afkomstig is van context of acties.
* Functie sleutel en waarde.

Een voor beeld van een bestel-app met Ice room kan bijvoorbeeld ' context. weer: Hot ' zien als een belang rijk onderdeel.

Persoonlijker toont de correlaties van functies die, wanneer ze samen in aanmerking worden genomen, hogere beloningen produceren.

Zo ziet u mogelijk ' context. weer: Hot *with* action. menu item: ijs ' en ' context. weer: koud *met* action. menu item: WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>Acties die u kunt uitvoeren op basis van de functie-evaluatie

### <a name="imagine-additional-features-you-could-use"></a>Stel dat u aanvullende functies kunt gebruiken

Vind inspiratie van de belang rijke functies in het model. Als u bijvoorbeeld ' context. MobileBattery: low ' ziet in een video mobiele app, kunt u denken dat het verbindings type ook kan zorgen dat klanten een video clip over een andere weer geven en vervolgens functies toevoegen over het verbindings type en de band breedte in uw app.

### <a name="see-what-features-are-not-important"></a>Bekijk welke functies niet belang rijk zijn

Mogelijk onbelangrijke functies te verwijderen of om te analyseren wat van invloed kan zijn op het gebruik. Functies kunnen om verschillende redenen laag worden gerangschikt. Een van de mogelijke oorzaken is dat de functie niet van invloed is op het gedrag van de gebruiker. Maar het kan ook betekenen dat de functie niet zichtbaar is voor de gebruiker. 

Een video site zou bijvoorbeeld kunnen zien dat "Action. VideoResolution = 4.000" een functie met lage urgentie is, wat in strijd is met gebruikers onderzoek. De oorzaak kan zijn dat de toepassing de video resolutie niet vermeldt of weergeeft, zodat gebruikers hun gedrag op basis daarvan niet kunnen wijzigen.

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>Richt lijnen bieden voor redactionele of curator-teams

Geef richt lijnen op voor nieuwe inhoud of producten die in de catalogus worden gebracht. Personaler is ontworpen om een hulp programma te zijn waarmee mensen inzicht en teams worden uitgebreid. Een manier om dit te doen, is door informatie op te geven over producten, artikelen of inhoud die het gedrag verstuurt. In het scenario van de video-toepassing kan bijvoorbeeld worden weer gegeven dat er een belang rijke functie met de naam action. VideoEntities. cat: True is, waarin het redactionele team wordt gevraagd om meer kat Video's te nemen.

### <a name="troubleshoot-common-problems-and-mistakes"></a>Veelvoorkomende problemen en fouten oplossen

Veelvoorkomende problemen en fouten kunnen worden opgelost door de code van uw toepassing te wijzigen, zodat er geen ongewenste of onjuist opgemaakte functies naar Personaler worden verzonden. 

Veelvoorkomende fouten bij het verzenden van functies zijn onder andere:

* Het verzenden van persoons gegevens (PII). Persoonlijke gegevens die specifiek zijn voor één persoon (zoals naam, telefoon nummer, creditcard nummers en IP-adressen) mogen niet worden gebruikt met Personaler. Als uw toepassing gebruikers moet bijhouden, gebruikt u een niet-identificerende UUID of een ander nummer van de gebruikers-id. In de meeste scenario's is dit ook problematisch.
* Met een groot aantal gebruikers is het niet waarschijnlijk dat de interactie van elke gebruiker meer overschrijdt dan alle interactie van de populatie, waardoor het verzenden van gebruikers-Id's (zelfs als niet-PII) waarschijnlijk meer ruis zal toevoegen dan de waarde voor het model.
* Het verzenden van datum-en tijd velden als exacte tijds tempels in plaats van featurized tijd waarden. Bevat functies zoals context. time stamp. Day = maandag of ' context. time stamp. Hour ' = "13" is nuttiger. Er zijn Maxi maal 7 of 24 functie waarden voor elk. Maar ' context. time stamp ': ' 1985-04-12T23:20:50.52 Z ' is zo nauw keurig dat er geen andere manier is om deze te leren, omdat het nooit meer gebeurt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [schaal baarheid en prestaties](concepts-scalability-performance.md) met persoonlijker.

