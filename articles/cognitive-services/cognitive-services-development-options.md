---
title: Ontwikkel opties voor Azure Cognitive Services
description: Meer informatie over het gebruik van Azure Cognitive Services met verschillende ontwikkel-en implementatie opties, zoals client Bibliotheken, REST Api's, Logic Apps, Power Automatiseer, Azure Functions, Azure App Service, Azure Databricks en nog veel meer.
services: cognitive-services
manager: nitinme
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: a952bfa45515b9d35549d03d18fd94103679344d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349466"
---
# <a name="cognitive-services-development-options"></a>Cognitive Services-ontwikkelingsopties

Dit document bevat een overzicht van de ontwikkelings-en implementatie opties waarmee u aan de slag kunt met Azure Cognitive Services.

Azure Cognitive Services zijn op de cloud gebaseerde AI-services waarmee ontwikkel aars intelligentie kunnen bouwen in hun toepassingen en producten zonder enige kennis van machine learning. Met Cognitive Services hebt u toegang tot de AI-mogelijkheden of-modellen die door micro soft zijn gebouwd, getraind en bijgewerkt, zodat u deze kunt gebruiken in uw toepassingen. In veel gevallen hebt u ook de mogelijkheid om de modellen aan te passen aan uw bedrijfs behoeften. 

Cognitive Services zijn onderverdeeld in vier categorieën: beslissing, taal, spraak en visie. Normaal gesp roken hebt u toegang tot deze services via REST Api's, client bibliotheken en aangepaste hulpprogram ma's (zoals opdracht regel interfaces) van micro soft. Dit is echter maar één pad naar geslaagd. Via Azure hebt u ook toegang tot verschillende ontwikkel opties, zoals:

* Automatiserings- en integratiehulpprogramma's, zoals Logic Apps en Power Automate.
* Implementatie-opties zoals Azure Functions en de App Service. 
* Cognitive Services Docker-containers voor veilige toegang.
* Hulpprogramma's als Apache Spark, Azure Databricks, Azure Synapse Analytics en Azure Kubernetes Service voor big data-scenario's. 

Voordat we aan de slag gaan, is het belang rijk om te weten dat de Cognitive Services hoofd zakelijk wordt gebruikt voor twee verschillende taken. Op basis van de taak die u wilt uitvoeren, hebt u verschillende ontwikkelings-en implementatie opties waaruit u kunt kiezen. 

* [Ontwikkel opties voor voor spelling en analyse](#development-options-for-prediction-and-analysis)
* [Hulp middelen voor het aanpassen en configureren van modellen](#tools-to-customize-and-configure-models)

## <a name="development-options-for-prediction-and-analysis"></a>Ontwikkel opties voor voor spelling en analyse 

De hulpprogram ma's die u gebruikt om modellen aan te passen en te configureren, zijn anders dan degene die u gebruikt om de Cognitive Services aan te roepen. Met de meeste Cognitive Services kunt u gegevens verzenden en inzichten ontvangen zonder dat u deze hoeft aan te passen. Bijvoorbeeld: 

* U kunt een installatie kopie verzenden naar de Computer Vision-service om woorden en zinsdelen te detecteren of het aantal personen in het frame te tellen
* U kunt een audio bestand naar de speech-service verzenden en transcripties ophalen en de spraak tekst op hetzelfde moment vertalen
* U kunt een PDF naar de formulier Recognizer-service verzenden en tabellen, cellen en tekst in die cellen detecteren, en u krijgt een JSON-uitvoer met coördinaten en Details

Azure biedt een breed scala aan hulpprogram ma's die zijn ontworpen voor verschillende soorten gebruikers, die veel kunnen worden gebruikt met Cognitive Services. Ontwerp-hulpprogram ma's zijn het eenvoudigst te gebruiken en zijn snel te installeren en te automatiseren, maar kunnen beperkingen oplopen wanneer het gaat om aanpassingen. Onze REST Api's en client bibliotheken bieden gebruikers meer controle en flexibiliteit, maar vereisen meer inspanning, tijd en expertise om een oplossing te bouwen. Als u REST-Api's en client bibliotheken gebruikt, is er een verwachting dat u vertrouwd bent met moderne programmeer talen zoals C#, Java, Python, java script of een andere populaire programmeer taal. 

Laten we eens kijken naar de verschillende manieren waarop u met de Cognitive Services kunt werken.

### <a name="client-libraries-and-rest-apis"></a>Clientbibliotheken en REST API's

Cognitive Services-client bibliotheken en REST-Api's bieden u rechtstreekse toegang tot uw service. Deze hulpprogram ma's bieden programmatische toegang tot de Cognitive Services, hun basislijn modellen en in veel gevallen kunt u uw modellen en oplossingen programmatisch aanpassen. 

* **Doel gebruiker (s)**: ontwikkel aars en gegevens wetenschappers
* **Voor delen**: biedt de grootste flexibiliteit voor het aanroepen van de Services vanuit elke taal en omgeving. 
* **Gebruikers interface**: N/A-code
* **Abonnement/abonnementen**: Azure-account + Cognitive services resources

Als u meer wilt weten over de beschik bare client bibliotheken en REST Api's, gebruikt u onze [Cognitive Services-overzicht](index.yml) om te kiezen en te onderhouden en aan de slag te gaan met een van onze Quick starts voor Vision, beslissing, taal en spraak.

### <a name="cognitive-services-for-big-data"></a>Cognitive Services voor Big Data

Met Cognitive Services voor Big data kunt u intelligente modellen rechtstreeks in Apache Spark&trade; en SQL-berekeningen opnemen. Dankzij hulpprogramma's moeten ontwikkelaars zich niet meer bezighouden met netwerkdetails op laag niveau, zodat ze zich kunnen richten op het maken van slimme, gedistribueerde toepassingen. Cognitive Services voor Big Data ondersteunt de volgende platforms en connectors: Azure Databricks, Azure Synapse, Azure Kubernetes service en data connectors.

* **Doel gebruiker (s)**: gegevens wetenschappers en gegevens technici
* **Voor delen**: met Azure Cognitive Services voor Big data kunnen gebruikers met behulp van Apache Spark Data Channel terabytes aan gegevens via Cognitive Services &trade; . Het is eenvoudig om grootschalige intelligente toepassingen te maken met elke gegevens opslag.
* **Gebruikers interface**: N/A-code
* **Abonnement/abonnementen**: Azure-account + Cognitive services resources

Als u meer wilt weten over Big data voor Cognitive Services, kunt u het beste beginnen met het [overzicht](./big-data/cognitive-services-for-big-data.md). Als u klaar bent om te beginnen met het bouwen, probeert u onze [python](./big-data/samples-python.md) -of [scala](./big-data/samples-scala.md) -voor beelden.

### <a name="azure-functions-and-azure-service-web-jobs"></a>Azure Functions-en Azure-service-webjobs

[Azure functions](../azure-functions/index.yml) -en [Azure app service webjobs](../app-service/index.yml) bieden beide code-eerste integratie services die zijn ontworpen voor ontwikkel aars en zijn gebouwd op [Azure-app Services](../app-service/index.yml). Deze producten bieden serverloze infra structuur voor het schrijven van code. Binnen die code kunt u aanroepen naar onze services met behulp van onze client bibliotheken en REST Api's. 

* **Doel gebruiker (s)**: ontwikkel aars en gegevens wetenschappers
* **Voor delen**: serverloze compute-service waarmee u gebeurtenis-geactiveerde code kunt uitvoeren. 
* **UI**: Ja
* **Abonnement/abonnementen**: Azure-account + Cognitive Services resource + Azure functions abonnement

### <a name="azure-logic-apps"></a>Azure Logic Apps 

[Azure Logic apps](../logic-apps/index.yml) dezelfde werk stroom ontwerper en connectors als automatische automatisering delen, maar biedt meer geavanceerde en controle mogelijkheden, inclusief integraties met Visual Studio en DevOps. Met energie automatisering kunt u gemakkelijk integreren met uw cognitieve services-resources via servicespecifieke connectors die een proxy of wrapper rond de Api's bieden. Dit zijn dezelfde connectors als die beschikbaar zijn in automatische energie automatisering. 

* **Doel gebruiker (s)**: ontwikkel aars, integrators, IT-professionals, DevOps
* **Voor delen**: Designer-First (declaratief) ontwikkelings model dat geavanceerde opties en integratie biedt in een oplossing met weinig code
* **UI**: Ja
* **Abonnement (en)**: Azure-account + Cognitive Services resource + Logic apps implementatie

### <a name="power-automate"></a>Power Automate 

Energie automatisering is een service in het [Power platform](/power-platform/) waarmee u geautomatiseerde werk stromen tussen apps en services kunt maken zonder code te hoeven schrijven. We bieden verschillende connectors waarmee u eenvoudig kunt communiceren met uw Cognitive Services-resource in een oplossing voor het automatiseren van de stroom. Power Automate is gebaseerd op Logic Apps. 

* **Doel gebruiker (s)**: zakelijke gebruikers (analisten) en share point-beheerders
* **Voor delen**: Automatiseer Herhaalde hand matige taken door muis klikken te registreren, toetsaanslagen en kopieer plak stappen van uw bureau blad.
* **UI-hulpprogram ma's**: Ja, alleen gebruikers interface
* **Abonnement (en)**: Azure account + Cognitive Services resource + abonnement voor automatische abonnementen + Office 365-abonnement

### <a name="ai-builder"></a>AI Builder 

[AI Builder](/ai-builder/overview) is een micro soft power platform-mogelijkheid die u kunt gebruiken om de bedrijfs prestaties te verbeteren door processen te automatiseren en resultaten te voors pellen. AI Builder biedt de kracht van AI naar uw oplossingen door middel van een point-and-click-ervaring. Veel cognitieve Services, zoals formulier herkenning, Text Analytics en Computer Vision, zijn rechtstreeks geïntegreerd en u hoeft geen eigen Cognitive Services te maken. 

* **Doel gebruiker (s)**: zakelijke gebruikers (analisten) en share point-beheerders
* **Voor delen**: een kant-en-klare oplossing die de kracht van AI biedt met behulp van een point-and-click-ervaring. Geen code ring of vaardig heden van data Science vereist.
* **UI-hulpprogram ma's**: Ja, alleen gebruikers interface
* **Abonnement (en)**: AI-opbouw functie

### <a name="continuous-integration-and-deployment"></a>Continue integratie en implementatie

U kunt de acties van Azure DevOps en GitHub gebruiken voor het beheren van uw implementaties. In de [sectie hieronder vindt](#continuous-integration-and-delivery-with-devops-and-github-actions) u twee voor beelden van CI/cd-integraties voor het trainen en implementeren van aangepaste modellen voor spraak en de Language Understanding-service (Luis). 

* **Doel gebruiker (s)**: ontwikkel aars, gegevens wetenschappers en gegevens technici
* **Voor delen**: Hiermee kunt u toepassingen en modellen voortdurend programmatisch aanpassen, bijwerken en implementeren. Er is een aanzienlijk voor deel wanneer u uw gegevens regel matig gebruikt om modellen voor spraak, visie, taal en beslissing te verbeteren en bij te werken. 
* **UI-hulpprogram ma's**: alleen N/A-code 
* **Abonnement (en)**: Azure-account + Cognitive Services resource + github-account

## <a name="tools-to-customize-and-configure-models"></a>Hulp middelen voor het aanpassen en configureren van modellen

Wanneer u de voortgang van uw traject bouwt voor het maken van een toepassing of werk stroom met de Cognitive Services, moet u mogelijk het model aanpassen om de gewenste prestaties te verkrijgen. Veel van onze services bieden u de mogelijkheid om de vooraf gemaakte modellen te bouwen om te voldoen aan uw specifieke bedrijfs behoeften. Voor alle aanpas bare services bieden we zowel een door de gebruikers interface gestuurde ervaring voor het door lopen van het proces als Api's voor op code gerichte training. Bijvoorbeeld:

* U wilt een Custom Speech model trainen om medische voor waarden correct te herkennen met een woord fout frequentie (WER) van minder dan 3%
* U wilt een afbeeldings classificatie bouwen met Custom Vision die het verschil tussen coniferous en loof bomen kan aangeven
* U wilt een aangepaste Neural-stem bouwen met uw persoonlijke spraak gegevens voor een verbeterde automatische gebruikers ervaring

De hulpprogram ma's die u gebruikt om modellen te trainen en te configureren, zijn anders dan degene die u gebruikt om de Cognitive Services aan te roepen. In veel gevallen biedt Cognitive Services die ondersteuning bieden voor portals en hulpprogram ma's voor de gebruikers interface die zijn ontworpen om u te helpen bij het trainen, evalueren en implementeren van modellen. Laten we snel een aantal opties bekijken:<br><br>

| Pijler | Service | UI voor aanpassing | Snelstart |
|--------|---------|------------------|------------|
| Vision | Custom Vision | https://www.customvision.ai/ | [Snelstartgids](./custom-vision-service/quickstarts/image-classification.md?pivots=programming-language-csharp) | 
| Vision | Form Recognizer | Voorbeeldhulpprogramma voor labelen | [Snelstartgids](./form-recognizer/quickstarts/label-tool.md?tabs=v2-0) |
| Besluit | Content Moderator | https://contentmoderator.cognitive.microsoft.com/dashboard | [Snelstartgids](./content-moderator/review-tool-user-guide/human-in-the-loop.md) |
| Besluit | Metrics Advisor | https://metricsadvisor.azurewebsites.net/  | [Snelstartgids](./metrics-advisor/quickstarts/web-portal.md) |
| Besluit | Personalizer | De gebruikers interface is beschikbaar in het Azure Portal onder uw persoonlijke resource. | [Snelstartgids](./personalizer/quickstart-personalizer-sdk.md) |
| Taal | Language Understanding (LUIS) | https://www.luis.ai/ | |
| Taal | QnA Maker | https://www.qnamaker.ai/ | [Snelstartgids](./qnamaker/quickstarts/create-publish-knowledge-base.md) |
| Taal | Translator/aangepaste vertaler | https://portal.customtranslator.azure.ai/ | [Snelstartgids](./translator/custom-translator/quickstart-build-deploy-custom-model.md) |
| Spraak | Aangepaste opdrachten | https://speech.microsoft.com/ | [Snelstartgids](./speech-service/custom-commands.md) |
| Spraak | Aangepaste spraak | https://speech.microsoft.com/ | [Snelstartgids](./speech-service/custom-speech-overview.md) |
| Spraak | Aangepaste stem | https://speech.microsoft.com/ | [Snelstartgids](./speech-service/how-to-custom-voice.md) |  

### <a name="continuous-integration-and-delivery-with-devops-and-github-actions"></a>Continue integratie en levering met DevOps-en GitHub-acties

Language Understanding en de speech-service bieden continue integratie en continue implementatie oplossingen die worden ondersteund door Azure DevOps-en GitHub-acties. Deze hulpprogram ma's worden gebruikt voor het automatisch trainen, testen en vrijgeven van het beheer van aangepaste modellen. 

* [CI/CD voor Custom Speech](./speech-service/how-to-custom-speech-continuous-integration-continuous-deployment.md)
* [CI/CD voor LUIS](./luis/luis-concept-devops-automation.md)

## <a name="on-prem-containers"></a>On-premises containers 

Veel van de Cognitive Services kunnen worden geïmplementeerd in containers voor on-premises toegang en gebruik. Met deze containers hebt u de flexibiliteit om Cognitive Services dichter bij uw gegevens te brengen voor naleving, beveiliging of andere operationele redenen. Zie [on-premises containers voor Cognitive Services](./cognitive-services-container-support.md)voor een volledige lijst met Cognitive Services containers.

## <a name="next-steps"></a>Volgende stappen
<!--
* Learn more about low code development options for Cognitive Services -->
* [Een Cognitive Services resource maken en beginnen met bouwen](./cognitive-services-apis-create-account.md?tabs=multiservice%252clinux)