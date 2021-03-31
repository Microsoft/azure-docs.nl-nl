---
title: 'Veelgestelde vragen: SRE en DevOps | Microsoft Docs'
titleSuffix: Azure
description: 'Veelgestelde vragen: informatie over de relatie tussen SRE en DevOps'
author: dnblankedelman
manager: ScottCa
ms.service: site-reliability-engineering
ms.topic: article
ms.date: 09/14/2020
ms.author: dnb
ms.openlocfilehash: 62872443c386c45d9821053dc901ad8e0055b74f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674264"
---
# <a name="frequently-asked-questions-whats-the-relationship-between-sre-and-devops"></a>Veelgestelde vragen: wat is de relatie tussen SRE en DevOps?

Er is een aantal algemene vragen over de relatie tussen de site betrouwbaarheids techniek en DevOps, waaronder ' Hoe weet het? Hoe verschillen ze van elkaar? Kunnen we beide in onze organisatie hebben? '. In dit artikel wordt geprobeerd een aantal van de antwoorden te delen die zijn aangeboden door de SRE-en DevOps-community's die ons dichter bij een kennis van deze relatie kunnen krijgen.

## <a name="how-are-they-the-same"></a>Hoe zijn deze hetzelfde?

SRE en DevOps zijn zowel moderne operationele procedures die zijn gemaakt en ontwikkeld als reactie op uitdagingen die zijn opgenomen:

- een groeiende complexiteit van onze productie omgevingen en ontwikkelings processen
- een groeiende afhankelijkheid van ondernemingen van een continue werking van die omgevingen
- het onvermogen om het personeel lineair te schalen met de grootte van deze omgevingen
- de nood zaak om sneller te verplaatsen terwijl de operationele stabiliteit behouden blijft

Beide bewerkings procedures zijn belang rijk voor onderwerpen die cruciaal zijn voor het oplossen van deze uitdagingen, zoals bewaking/beheerbaarheid, automatisering, documentatie en hulpprogram ma's voor het ontwikkelen van samenwerkings software.

Er zijn belang rijke verschillen tussen SRE en DevOps. Wanneer _de site-betrouw bare werkmap_ het bestand plaatst, wordt het ' SRE-meen in dezelfde dingen als DevOps, maar om enigszins verschillende redenen. '

## <a name="three-different-ways-to-compare-the-two-operations-practices"></a>Drie verschillende manieren om de twee Operations-prak tijken te vergelijken

De overeenkomsten tussen SRE en DevOps zijn duidelijk. Waar het erg interessant is, is de manier waarop de twee verschillen of afwijkend zijn. Hier bieden we drie manieren om te denken over hun relatie als een manier om een aspect van deze vraag te brengen. U kunt niet akkoord gaan met deze antwoorden, maar ze bieden wel een goede start locatie voor discussies.

### <a name="class-sre-implements-interface-devops"></a>"class SRE implementeert interface DevOps"

_De site betrouwbaarheids werkmap_ (vermeld in de [lijst met resource boeken](../resources/books.md)) bespreekt SRE en DevOps in het eerste hoofd stuk. In dit hoofd stuk wordt gebruikgemaakt van de zin ' klasse SRE implementeert interface DevOps ' als subtitel. Dit is bedoeld om te suggereren (met behulp van een zin die gericht is op ontwikkel aars) die SRE als een specifieke implementatie van de DevOps filosofie kan worden beschouwd. Zoals beschreven in het hoofd stuk is ' DevOps is relatief stil voor het uitvoeren van bewerkingen op een gedetailleerd niveau ' terwijl SRE aanzienlijk meer in de praktijk is. Een mogelijke antwoord op de vraag van de manier waarop de twee related SRE kan worden beschouwd als een van de vele mogelijke implementaties van DevOps.

### <a name="sre-is-to-reliability-as-devops-is-to-delivery"></a>SRE is een betrouw baarheid, omdat DevOps moet worden geleverd

Deze vergelijking is een beetje muddied omdat er meerdere definities bestaan voor SRE en DevOps, maar het is nog steeds nuttig. Het begint met de vraag "als u elke bewerkings praktijk in één of twee woorden wilt distilleren, wat zou het?"

Als we deze definitie van SRE gebruiken van de [site betrouwbaarheids engineering-hub](../index.yml):

> Site Reliability Engineering is een engineeringsdiscipline die zich richt op het helpen van organisaties om duurzaam de juiste mate van betrouwbaarheid in hun systemen, services en producten te realiseren.

vervolgens zou het woord voor SRE ' betrouw baarheid ' zijn. Het is in het midden van de naam ook een uitstekende bewijs voor deze claim.

Als we deze definitie van DevOps gebruiken in het [Azure DevOps Resource Center](/azure/devops/learn/):

> DevOps is de combinatie van mensen, processen en producten om continu waarde te kunnen bieden aan onze eindgebruikers.

een vergelijk bare distillatie voor DevOps kan ' levering ' zijn.

' SRE is dus een betrouw baarheid als DevOps is voor levering '.

### <a name="direction-of-attention"></a>Attentie richting

Dit antwoord is geciteerd of enigszins paraphrased van een bijdrage door Thomas Limoncelli aan het _Zoek SRE_ -boek dat in de [lijst met resource boeken](../resources/books.md)wordt vermeld. Hij merkt dat DevOps engineers voornamelijk gericht zijn op de pipeline van de software ontwikkeling met incidentele productie-verwerkings verantwoordelijkheden, terwijl SREs zich richt op productie werkzaamheden waarbij incidentele SDLC-pijplijn verantwoordelijkheden gelden.

Maar belang rijker: hij tekent ook een diagram dat begint met het software ontwikkelings proces aan de ene kant en de productie werkzaamheden aan het andere. De twee zijn verbonden door de gebruikelijke pijp lijn die is gebouwd voor het nemen van de code van een ontwikkelaar, Shepherd het gewenste aantal tests en fase en verplaatst die code vervolgens naar productie.

Limoncelli-opmerkingen die DevOps-engineers starten in de ontwikkel omgeving en stappen voor productie automatiseren. Zodra het proces is voltooid, gaan ze terug om knel punten te optimaliseren.

SREs, aan de andere kant, zich richten op productie werkzaamheden en bereik diep in de pijp lijn als een middel om het eind resultaat te verbeteren (in principe in tegenovergestelde richting te werken).

Het is dit verschil in de richting van de SRE-en DevOps-focus, waarmee ze kunnen worden onderscheiden.

## <a name="coexistence-in-the-same-organization"></a>Samen werking in dezelfde organisatie

De laatste vraag is dat we in dezelfde organisatie ' kunnen SRE en DevOps hebben? '

Het antwoord op deze vraag is een emphatic ' ja! '.

We hopen dat de vorige antwoorden een idee hebben van de manier waarop de twee procedures elkaar overlappen en, wanneer ze niet overlappen, hoe ze kunnen worden complementair in focus. Organisaties met een tot stand gebrachte DevOps-praktijk kunnen experimenteren met SRE-prak tijken op een kleine schaal (bijvoorbeeld bij het proberen van SLIs en Slo's) zonder dat er SRE posities of teams moeten worden gemaakt. Dit is een redelijk common SRE-acceptatie patroon.

## <a name="next-steps"></a>Volgende stappen

Wilt u meer leren over de site betrouwbaarheids techniek of DevOps? Bekijk onze [site betrouwbaarheids techniek hub](../index.yml) en [Azure DevOps Resource Center](/azure/devops/learn/).