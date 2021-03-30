---
title: Integratie van Azure DevTest Labs en DevOps | Microsoft Docs
description: Meer informatie over het gebruik van Labs van Azure DevTest Labs binnen een pijp lijn (continue Integration)/continue levering (CD) in een bedrijfs omgeving.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 8a5d35a541e079b7d39cae2ec43da608274533f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481065"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integratie van Azure DevTest Labs en Azure DevOps
DevOps is een methodologie voor software ontwikkeling waarmee software ontwikkeling (dev) wordt geïntegreerd met bewerkingen (OPS) voor een systeem. Dit systeem kan nieuwe functies, updates en oplossingen leveren op basis van de bedrijfs doelen. Deze methodologie omvat alles uit het ontwerpen van nieuwe functies op basis van doel stellingen, gebruiks patronen en feedback van klanten; om het systeem te corrigeren, te herstellen en te verhelpen wanneer er problemen optreden. Een eenvoudig geïdentificeerd onderdeel van deze methodologie is de pijp lijn doorlopende integratie (CI)/continue levering (CD). Een CI/CD-pijp lijn neemt informatie, code en resources van een door Voer via een reeks stappen die bouwen, testen en implementeren bevatten, om het systeem te maken. Dit artikel richt zich op verschillende manieren om Labs effectief te gebruiken in een pijp lijn in een bedrijfs omgeving. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Voor delen van het gebruik van Labs in de DevOps-werk stroom 

### <a name="focused-access"></a>Toegang met focus 
Het gebruik van een Lab als onderdeel maakt het mogelijk een specifiek ecosysteem te koppelen aan een beperkte groep personen. Normaal gesp roken wordt een team of groep die werkt in een gemeen schappelijk gebied of een specifieke functie een Lab toegewezen.   

### <a name="infrastructure-replication-in-the-cloud"></a>Infrastructuur replicatie in de Cloud 
Een ontwikkelaar kan snel een ontwikkelings ecosysteem instellen dat een dev box bevat met bron code en hulpprogram ma's. De ontwikkelaar kan ook een omgeving maken die bijna identiek is aan de productie configuratie. Het helpt bij het sneller ontwikkelen van de binnenste lus. 

### <a name="pre-production"></a>Pre-productie 
Als u een Lab hebt in de CI/CD-pijp lijn, kunt u eenvoudiger meerdere verschillende pre-productie omgevingen of machines tegelijk uitvoeren voor asynchrone tests. Verschillende ondersteunings infrastructuren, zoals build-agents, kunnen binnen een Lab worden geïmplementeerd en beheerd. 

## <a name="devops-with-devtest-labs"></a>DevOps met DevTest Labs 

### <a name="development--operation"></a>Ontwikkeling/bewerking 
Een lab moet worden gericht op een team dat werkt in een functie gebied. Deze algemene focus maakt het delen van systeemspecifieke resources, zoals hulpprogram ma's, scripts of Resource Manager-sjablonen. Dit maakt snellere wijzigingen mogelijk en beperkt de negatieve effecten tot een kleinere groep. Met deze gedeelde bronnen kan de ontwikkelaar ontwikkel-Vm's maken met alle benodigde code, hulpprogram ma's en configuratie. Ze kunnen dynamisch worden gemaakt of een systeem hebben dat basis installatie kopieën maakt met de aanpassingen. Het is niet alleen mogelijk om Vm's te maken, maar ze kunnen ook DevTest Labs-omgevingen maken op basis van de benodigde sjablonen om de juiste Azure-resources in het lab te maken. Wijzigingen of destructieve werk kunnen worden uitgevoerd op de test omgeving zonder dat dit van invloed is op iemand anders. Denk na over het scenario waarin het product een zelfstandig systeem is dat is geïnstalleerd op de computer van de klant. In dit scenario heeft DevTest Labs het maken van de VM verbeterd, waaronder het installeren van extra software met behulp van artefacten en het vooraf bouwen van klant configuraties voor een snellere interne loop-test van de code. 
  
## <a name="cicd-pipeline"></a>CI/CD-pijplijn 
De CI/CD-pijp lijn is een van de essentiële onderdelen in DevOps die code verplaatsen van een pull-aanvraag van de ontwikkelaar, integreert met de bestaande code en implementeert deze in het productie-ecosysteem. Alle resources hoeven niet binnen een lab te zijn. Een Jenkins-host kan bijvoorbeeld buiten Lab worden ingesteld als een meer persistente resource. Hier volgen enkele specifieke voor beelden van het integreren van Labs in de pijp lijn. 

### <a name="build"></a>Build 
De build-pijp lijn is gericht op het maken van een pakket met onderdelen dat samen wordt getest om aan de release pijplijn te worden door gegeven. Labs kan deel uitmaken van de build-pijp lijn als de locatie voor Build-agents en andere ondersteunings bronnen. De mogelijkheid om de infra structuur dynamisch te bouwen, biedt meer controle. Met de mogelijkheid om meerdere omgevingen in een lab te hebben, kan elke build asynchroon worden uitgevoerd terwijl de build-ID als onderdeel van de omgevings gegevens wordt gebruikt om de resources op unieke wijze te identificeren voor de specifieke build.   

Voor Build-agents is het mogelijk om de toegang te beperken tot de beveiliging en de kans op onbedoelde beschadigingen te reduceren.  

### <a name="test"></a>Testen 
Met DevTest Labs kan een CI/CD-pijp lijn het maken van Azure-resource (Vm's, omgevingen) automatiseren die kan worden gebruikt voor geautomatiseerde en hand matige tests. De Vm's worden gemaakt met behulp van artefacten of formules die informatie uit het bouw proces gebruiken om de verschillende aangepaste configuraties te maken die nodig zijn voor het testen.   

### <a name="release"></a>Release 
DevTest Labs wordt doorgaans gebruikt voor verificatie in het gedeelte release voordat de code wordt geïmplementeerd. Het is vergelijkbaar met testen in de sectie build. Productie resources mogen niet worden geïmplementeerd in DevTest Labs. 

### <a name="customization"></a>Aanpassing 
In azure DevOps zijn er bestaande taken die het manipuleren van Vm's en omgevingen binnen specifieke Labs mogelijk maken. Azure DevOps Services is een manier om de CI/CD-pijp lijn te beheren, maar u kunt het lab integreren in elk systeem dat de mogelijkheid biedt om REST Api's aan te roepen, Power shell-scripts uit te voeren of Azure CLI te gebruiken. 

Sommige CI/CD-pipeline-beheerders hebben bestaande open-source-invoeg toepassingen die resources kunnen beheren in Azure en DevTest Labs. Mogelijk moet u een aangepast script gebruiken om te voldoen aan de specifieke behoeften van de pijp lijn.  Bij het uitvoeren van een taak wordt een Service-Principal gebruikt met de juiste rol om toegang te krijgen tot het lab. Voor de Service-Principal is doorgaans toegang tot het lab vereist voor de rol Inzender. Zie [Azure DevTests Labs integreren in uw Azure DevOpsing-pijp lijn voor continue integratie en levering](devtest-lab-integrate-ci-cd.md)voor meer informatie. 
 