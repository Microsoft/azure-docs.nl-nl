---
title: Doorlopende werk stromen voor LUIS-apps
description: CI/CD-werk stromen implementeren voor DevOps voor Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/5/2020
ms.openlocfilehash: 215399e4b131162097e54c15b84cb6fa7dac72e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932545"
---
# <a name="continuous-integration-and-continuous-delivery-workflows-for-luis-devops"></a>Werkstromen voor continue integratie en continue levering voor LUIS DevOps

Software-engineers die een Language Understanding-app (LUIS) ontwikkelen, kunnen DevOps-procedures rond [broncode beheer](luis-concept-devops-sourcecontrol.md), [geautomatiseerde builds](luis-concept-devops-automation.md), [testen](luis-concept-devops-testing.md)en [release beheer](luis-concept-devops-automation.md#release-management)Toep assen. In dit artikel worden de concepten beschreven voor het implementeren van geautomatiseerde builds voor LUIS.

## <a name="build-automation-workflows-for-luis"></a>Automation-werk stromen maken voor LUIS

![CI-werk stromen](./media/luis-concept-devops-automation/luis-automation.png)

Configureer in het systeem voor broncode beheer (SCM) automatische build pijp lijnen om uit te voeren met de volgende gebeurtenissen:

1. **PR-werk stroom** wordt geactiveerd wanneer een [pull-aanvraag](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) wordt gegenereerd. Met deze werk stroom wordt de inhoud van de PR gevalideerd *voordat* de updates worden samengevoegd in de hoofd vertakking.
1. **CI/cd-werk stroom** die wordt geactiveerd wanneer updates worden gepusht naar de hoofd vertakking, bijvoorbeeld bij het samen voegen van de wijzigingen van een PR. Met deze werk stroom wordt de kwaliteit van alle updates voor de hoofd vertakking gegarandeerd.

De **CI/cd-werk stroom** combineert twee complementaire ontwikkelings processen:

* [Continue integratie](/azure/devops/learn/what-is-continuous-integration) (CI) is de technische praktijk van het veelvuldig door voeren van code in een gedeelde opslag plaats en het uitvoeren van een geautomatiseerde build. Met een geautomatiseerd [test](luis-concept-devops-testing.md) proces kunt u voor elke update controleren of de LUDown-bron nog geldig is en kan worden geïmporteerd in een Luis-app, maar ook een groep tests waarmee wordt gecontroleerd of de getrainde app de intenties en entiteiten kan herkennen die nodig zijn voor uw oplossing.

* [Continue levering](/azure/devops/learn/what-is-continuous-delivery) (cd) zorgt voor een continue integratie concept om de toepassing automatisch te implementeren in een omgeving waarin u meer gedetailleerde tests kunt uitvoeren. Met de CD kunnen we tijdig ontdekken wat er onvoorziene problemen zijn die zich voordoen als gevolg van onze wijzigingen, en ook om meer te weten te komen over hiaten in onze test dekking.

Het doel van continue integratie en continue levering is om ervoor te zorgen dat ' hoofd is altijd shippable, '. Voor een LUIS-app betekent dit dat we, indien nodig, een wille keurige versie van de hoofd vertakkings-LUIS-app kunnen nemen en deze op productie kan leveren.

### <a name="tools-for-building-automation-workflows-for-luis"></a>Hulpprogram ma's voor het maken van Automation-werk stromen voor LUIS

Er zijn verschillende technologieën voor het bouwen van Automatisering beschikbaar om werk stromen voor het bouwen van Automation te maken. Voor alle gebruikers moet u scripts uitvoeren met een opdracht regel interface (CLI) of REST-aanroepen, zodat ze kunnen worden uitgevoerd op een build-server.

Gebruik de volgende hulpprogram ma's voor het maken van Automation-werk stromen voor LUIS:

* [Bot Framework tools Luis cli](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) voor het werken met Luis-apps en-versies, Train, test en publiceer ze in de Luis-service.

* [Azure cli](/cli/azure/) voor het opvragen van Azure-abonnementen, ophalen van Luis-ontwerp-en Voorspellings sleutels en het maken van een Azure- [Service-Principal](/cli/azure/ad/sp) die wordt gebruikt voor Automation-verificatie.

* [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) -hulp programma voor [het testen van een Luis-app](luis-concept-devops-testing.md) en het analyseren van test resultaten.

### <a name="the-pr-workflow"></a>De PR-werk stroom

Zoals gezegd, kunt u deze werk stroom zo configureren dat deze wordt uitgevoerd wanneer een ontwikkelaar een PR krijgt om wijzigingen voor te stellen die worden samengevoegd van een functie vertakking in de hoofd vertakking. Het doel is om de kwaliteit van de wijzigingen in de PR te controleren voordat ze worden samengevoegd met de hoofd vertakking.

Deze werk stroom moet:

* Maak een tijdelijke LUIS-app door de `.lu` bron te importeren in de PR.
* Train en publiceer de versie van de LUIS-app.
* Voer alle [eenheids tests](luis-concept-devops-testing.md) uit.
* Geef de werk stroom door als alle tests zijn geslaagd, anders niet.
* De tijdelijke app opschonen en verwijderen.

Configureer regels voor vertakkings beveiliging als deze door uw SCM worden ondersteund, zodat deze werk stroom moet worden voltooid voordat de pull-bewerking kan worden voltooid.

### <a name="the-main-branch-cicd-workflow"></a>De hoofd vertakking CI/CD-werk stroom

Deze werk stroom zo configureren dat deze wordt uitgevoerd nadat de updates in de PR zijn samengevoegd in de hoofd vertakking. Het doel is om de kwaliteits balk voor uw hoofd vertakking hoog te hand haven door de updates te testen. Als de updates voldoen aan de kwaliteits balk, implementeert deze werk stroom de nieuwe LUIS-App-versie naar een omgeving waarin u meer gedetailleerde tests kunt uitvoeren.

Deze werk stroom moet:

* Bouw een nieuwe versie in uw primaire LUIS-app (de app die u voor de hoofd vertakking onderhoudt) met behulp van de bijgewerkte bron code.

* Train en publiceer de versie van de LUIS-app.

  > [!NOTE]
  > Zoals in het geval van [tests in een geautomatiseerde build-werk stroom wordt](luis-concept-devops-testing.md#running-tests-in-an-automated-build-workflow) uitgelegd, moet u de Luis-App-versie onder test publiceren, zodat de hulpprogram MA'S zoals NLU. DevOps heeft toegang tot het bestand. LUIS biedt alleen ondersteuning voor twee benoemde publicatie sleuven, *fase ring* en *productie* voor een Luis-app, maar u kunt ook [rechtstreeks een versie publiceren](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) en [query's uitvoeren op versie](./luis-migration-api-v3.md#changes-by-slot-name-and-version-name). Gebruik direct versie publiceren in uw Automation-werk stromen om te voor komen dat u de benoemde publicatie sleuven beperkt.

* Voer alle [eenheids tests](luis-concept-devops-testing.md)uit.

* Voer eventueel [batch tests](luis-concept-devops-testing.md#how-to-do-unit-testing-and-batch-testing) uit om de kwaliteit en nauw keurigheid van de Luis-App-versie te meten en te vergelijken met een bepaalde basis lijn.

* Als de tests zijn voltooid:
  * Label de bron in de opslag plaats.
  * Voer de taak continue levering (CD) uit om de LUIS-App-versie te implementeren in omgevingen voor verdere tests.

### <a name="continuous-delivery-cd"></a>Continue levering (CD)

De CD-taak in een CI/CD-werk stroom wordt voorwaardelijk uitgevoerd op het slagen van het build-en automatische eenheids testen. Het is de taak om de LUIS-toepassing automatisch te implementeren in een omgeving waarin u meer tests kunt uitvoeren.

Er is geen aanbevolen oplossing voor het implementeren van uw LUIS-app en u moet het proces implementeren dat geschikt is voor uw project. De [Luis DevOps-sjabloon](https://github.com/Azure-Samples/LUIS-DevOps-Template) opslag plaats implementeert een eenvoudige oplossing voor het publiceren van [de nieuwe Luis-App-versie](./luis-how-to-publish-app.md) naar de *productie* publicatie sleuf. Dit is prima voor een eenvoudige installatie. Als u echter een aantal verschillende productie omgevingen tegelijk wilt ondersteunen, zoals *ontwikkeling*, *staging* en *bedoeld*, zal de limiet van twee benoemde publicatie sleuven per app ontoereikend zijn.

Andere opties voor het implementeren van een app-versie zijn onder andere:

* Laat de App-versie gepubliceerd op het directe versie-eind punt en implementeer een proces om downstream productie omgevingen met het direct versie-eind punt zo nodig te configureren.
* Onderhoud verschillende LUIS-apps voor elke productie omgeving en schrijf automatiserings stappen om het `.lu` in een nieuwe versie in de Luis-app voor de doel productie omgeving te importeren, om deze te trainen en te publiceren.
* Exporteer de geteste LUIS-App-versie naar een [Luis docker-container](./luis-container-howto.md?tabs=v3) en implementeer de Luis-container in azure [container instances](../../container-instances/index.yml).

## <a name="release-management"></a>Releasebeheer

Over het algemeen raden we u aan continue levering alleen uit te voeren naar uw niet-productie omgevingen, zoals ontwikkeling en fase ring. De meeste teams vereisen een hand matig controle-en goedkeurings proces voor implementatie naar een productie omgeving. Voor een productie-implementatie wilt u er wellicht zeker van zijn dat deze zich voordoet wanneer er belang rijke personen voor het ontwikkelings team beschikbaar zijn voor ondersteuning of tijdens een periode van minder verkeer.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [implementeren van DevOps voor Luis met github](luis-how-to-devops-with-github.md)
* Meer informatie over het schrijven [van een github actions-werk stroom met NLU. DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template/blob/master/docs/4-pipeline.md)
