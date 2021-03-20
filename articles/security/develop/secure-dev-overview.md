---
title: Best practices voor veilige ontwikkeling op Microsoft Azure
description: Aanbevolen procedures voor het ontwikkelen van veiligere code en het implementeren van een veiligere toepassing in de Cloud.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: baaa311620f5c10948aa3494002ce359cc5dab28
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517169"
---
# <a name="secure-development-best-practices-on-azure"></a>Best practices voor veilige ontwikkeling op Azure
Deze reeks artikelen bevat beveiligings activiteiten en-besturings elementen waarmee u rekening moet houden bij het ontwikkelen van toepassingen voor de Cloud. De fasen van de micro soft Security Development Lifecycle (SDL) en beveiligings vragen en concepten die u tijdens elke fase van de levens cyclus kunt overwegen, worden behandeld. Het doel is om u te helpen bij het definiëren van activiteiten en Azure-Services die u in elke fase van de levens cyclus kunt gebruiken om een veiligere toepassing te ontwerpen, ontwikkelen en implementeren.

De aanbevelingen in de artikelen zijn afkomstig van onze ervaring met Azure-beveiliging en van de ervaringen van onze klanten. U kunt deze artikelen gebruiken als referentie voor wat u moet overwegen tijdens een specifieke fase van het ontwikkelings project, maar we raden u ook aan alle artikelen van het begin tot ten minste één keer te lezen. Als u alle artikelen leest, introduceert u concepten die u mogelijk hebt gemist in eerdere fasen van uw project. Als u deze concepten implementeert voordat u uw product uitgeeft, kan u helpen bij het bouwen van beveiligde software, vereisten voor de naleving van het beveiligings beleid en de ontwikkelings kosten verminderen.

Deze artikelen zijn bedoeld als een resource voor software ontwerpers, ontwikkel aars en testers op alle niveaus die beveiligde Azure-toepassingen bouwen en implementeren.

## <a name="overview"></a>Overzicht

Beveiliging is een van de belangrijkste aspecten van elke toepassing, en het is niet eenvoudig om meteen aan de slag te gaan. Gelukkig biedt Azure veel services die u kunnen helpen bij het beveiligen van uw toepassing in de Cloud. Met deze artikelen kunt u activiteiten en Azure-Services implementeren in elke fase van de levens cyclus van de software ontwikkeling om u te helpen bij het ontwikkelen van veiligere code en het implementeren van een veiligere toepassing in de Cloud.

## <a name="security-development-lifecycle"></a>Security Development Lifecycle

De volgende aanbevolen procedures voor het ontwikkelen van beveiligde software vereisen het integreren van de beveiliging in elke fase van de levens cyclus van software ontwikkeling, van analyse van vereisten tot onderhoud, ongeacht de project methodologie ([waterval](https://en.wikipedia.org/wiki/Waterfall_model), [Agile](https://en.wikipedia.org/wiki/Agile_software_development)of [DevOps](https://en.wikipedia.org/wiki/DevOps)). Bij het ontwaken van gegevens schendingen met een hoog profiel en de exploitatie van operationele beveiligings fouten, zijn meer ontwikkel aars beter te weten dat de beveiliging moet worden aangepakt tijdens het ontwikkelings proces.

Hoe later u een probleem in uw ontwikkelings levenscyclus verhelpt, hoe meer deze oplossing u kunt kosten. Beveiligings problemen zijn geen uitzonde ring. Als u geen beveiligings problemen hebt in de vroege fasen van uw software ontwikkeling, kan elke fase die volgt, de beveiligings problemen van de voor gaande fase overnemen. Het uiteindelijke product heeft meerdere beveiligings problemen opgebouwd en de mogelijkheid van schending. Door beveiliging in elke fase van de ontwikkelings levenscyclus te maken, kunt u tijdig problemen ondervangen en kunt u de ontwikkelings kosten verlagen.

We volgen de fasen van de micro soft [Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) om activiteiten en Azure-Services uit te voeren die u kunt gebruiken om in elke fase van de levens cyclus te voldoen aan de veilige procedures voor het ontwikkelen van software.

De SDL-fasen zijn:

  - Training
  - Vereisten
  - Ontwerp
  - Implementatie
  - Verificatie
  - Release
  - Antwoord

![Security Development Lifecycle](./media/secure-dev-overview/01-sdl-phase.png)

In deze artikelen groeperen we de SDL-fasen in ontwerpen, ontwikkelen en implementeren.

## <a name="engage-your-organizations-security-team"></a>Het beveiligings team van uw organisatie benaderen

Uw organisatie heeft mogelijk een formeel toepassings beveiligings programma dat u helpt bij het uitvoeren van beveiligings activiteiten van begin tot eind tijdens de ontwikkelings cyclus. Als uw organisatie beveiligings-en nalevings teams heeft, zorg er dan voor dat u deze aanneemt voordat u begint met het ontwikkelen van uw toepassing. Vraag hen in elke fase van de SDL of er taken zijn die u hebt gemist.

We begrijpen dat veel lezers mogelijk geen beveiligings-of nalevings team hebben om deel te nemen. Deze artikelen kunnen u helpen bij de beveiligings vragen en-beslissingen die u moet overwegen bij elke fase van SDL.

## <a name="resources"></a>Resources

Gebruik de volgende bronnen voor meer informatie over het ontwikkelen van beveiligde toepassingen en voor het beveiligen van uw toepassingen in Azure:

[Micro soft Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)) : SDL is een software ontwikkelings proces van micro soft waarmee ontwikkel aars beter beveiligde software kunnen bouwen. Het helpt u bij het oplossen van vereisten voor beveiligings naleving terwijl de ontwikkelings kosten worden verminderd.

[Open Web Application Security project (OWASP)](https://www.owasp.org/index.php/Main_Page) : OWASP is een online community die vrij beschik bare artikelen, methodologieën, Documentatie, hulpprogram ma's en technologieën produceert in het veld van de beveiliging van webtoepassingen.

[Naar links pushen, zoals een chef,](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca) een reeks online artikelen die verschillende soorten toepassings beveiligings activiteiten bevat die ontwikkel aars moeten volt ooien om veiligere code te maken.

[Micro soft Identity platform](../../active-directory/develop/index.yml) : het micro soft Identity-platform is een evolutie van de Azure AD Identity-service en het ontwikkelaars platform. Het is een volledig uitgeruste platform dat bestaat uit een verificatie service, open source-bibliotheken, registratie en configuratie van toepassingen, volledige documentatie van ontwikkel aars, code voorbeelden en andere inhoud voor ontwikkel aars. Het micro soft-identiteits platform ondersteunt industrie-standaard protocollen zoals OAuth 2,0 en OpenID Connect Connect.

[Aanbevolen beveiligings procedures voor Azure-oplossingen](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/) : een verzameling aanbevolen procedures voor beveiliging bij het ontwerpen, implementeren en beheren van cloud oplossingen met behulp van Azure. Dit artikel is bedoeld als een resource voor IT-professionals. Dit kan ontwerpers, architecten, ontwikkel aars en testers zijn die beveiligde Azure-oplossingen bouwen en implementeren.

[Blauw drukken voor beveiliging en naleving op Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview) : Azure Security en nalevings schema's zijn bronnen die u kunnen helpen bij het bouwen en starten van toepassingen in de cloud die voldoen aan strenge voor schriften en standaarden.

## <a name="next-steps"></a>Volgende stappen
In de volgende artikelen raden wij aan beveiligings maatregelen en activiteiten te gebruiken die u kunnen helpen bij het ontwerpen, ontwikkelen en implementeren van beveiligde toepassingen.

- [Veilige toepassingen ontwerpen](secure-design.md)
- [Veilige toepassingen ontwikkelen](secure-develop.md)
- [Veilige toepassingen implementeren](secure-deploy.md)