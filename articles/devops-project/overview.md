---
title: Overzicht van DevOps Starter for Azure| Microsoft Docs
description: Ontdek de waarde van DevOps Starter
services: devops-project
documentationcenter: ''
author: mlearned
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: mlearned
ms.openlocfilehash: 7c36539ef5be503b2cb7e14047e596522ef8e962
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330645"
---
# <a name="overview-of-devops-starter"></a>Overzicht van DevOps Starter

 Met Azure DevOps Starter kunt u eenvoudig aan de slag met Azure met behulp van GitHub-acties of Azure DevOps. U kunt met enkele eenvoudige stappen vanuit de Azure-portal uw favoriete type app starten in de Azure-service van uw keuze. 

 DevOps Starter stelt alles in wat u nodig hebt voor het ontwikkelen, implementeren en controleren van uw toepassing. Met het DevOps Starter-dashboard kunt u het doorvoeren, compileren en implementeren van code controleren, allemaal vanuit één weergave in de Azure-portal.

## <a name="advantages-of-using-devops-starter"></a>Voordelen van het gebruik van DevOps Starter

  DevOps Starter ondersteunt de volgende twee CI/CD-providers om uw implementaties te automatiseren
  * [GitHub Actions](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  DevOps Starter automatiseert de configuratie van een volledige pijplijn voor continue integratie (CI) en continue levering (CD) van uw toepassing naar Azure.  U kunt beginnen met bestaande code of een van de gegeven voorbeeldtoepassingen gebruiken. Vervolgens kunt u die toepassing snel implementeren in verschillende Azure-services, zoals Virtual Machines, App Service, Azure Kubernetes Service (AKS), Azure SQL Database en Azure Service Fabric.  

  DevOps Starter verricht alle taken voor de eerste configuratie van een DevOps-pijplijn, met inbegrip van het instellen van de initiële Git-opslagplaats, het configureren van de CI/CD-pijplijn, het maken van een Application Insights-resource voor controledoeleinden en het genereren van één enkele weergave van de complete oplossing via een DevOps Starter-dashboard in Azure Portal.

U kunt DevOps Starter gebruiken om:

* Snel een toepassing implementeren in Azure
* De installatie van een VSTS CI/CD-werkstroom of -pijplijn te automatiseren
* Te bekijken en begrijpen hoe u een CI/CD- werkstroom of -pijplijn correct instelt
* De release-pijplijnen verder aan te passen op basis van uw specifieke scenario's

## <a name="how-to-use-devops-starter"></a>Hoe kunt u DevOps Starter gebruiken?

  DevOps Starter is beschikbaar via de Azure-portal. U maakt een DevOps Starter-resource in de portal net zoals elke andere Azure-resource. DevOps Projects werkt zoals een wizard, wat inhoudt dat de verschillende configuratieopties achtereenvolgens worden aangeboden.  

U kiest een aantal configuratieopties om de eerste installatie uit te voeren. Het gaat om deze opties:

* Uw favoriete CI/CD-provider selecteren
* De bijgeleverde voorbeeld-app of uw eigen code gebruiken (alleen voor Azure DevOps)
* Een taal voor de app selecteren
* Een app-framework kiezen op basis van de taal
* Een Azure-service (implementatiedoel) selecteren
* Uw GitHub- of Azure DevOps-organisatie selecteren
* Uw Azure-abonnement kiezen
* De locatie van Azure-services kiezen
* Kiezen uit verschillende prijscategorieën voor Azure-services

Nadat het DevOps Starter-project is gemaakt, kunt u het volgende doen:

* Uw GitHub-werkstroom of Azure DevOps Pipeline aanpassen
* Met behulp van pull-aanvragen de codestroom beheren en een hoge kwaliteit garanderen
* Elke doorvoer (commit) testen en compileren voordat u de code samenvoegt om de kwaliteit nog verder te verbeteren

Nadat u DevOps Starter hebt gebruikt, kunt u alle resources ook vanaf één locatie verwijderen, namelijk vanuit het DevOps Starter-dashboard in de Azure-portal.

## <a name="devops-starter-and-github-integration"></a>Integratie van DevOps Starter en GitHub

DevOps Starter biedt nu ondersteuning voor GitHub-acties als een CI/CD-provider. Het automatiseert al het werk dat nodig is in GitHub om een CI/CD-werkstroom met GitHub Actions in te stellen. Er wordt een GitHub-opslagplaats gemaakt in een bestaande GitHub-organisatie, en vervolgens wordt een voorbeeldtoepassing doorgevoerd in de nieuwe GitHub-opslagplaats.  

Met de automatisering wordt ook een trigger voor de werkstroom gemaakt, zodat elke nieuwe codedoorvoer een build- en implementatietaak binnen de werkstroom start. De toepassing wordt geïmplementeerd naar de Azure-service die u kiest. De GitHub-werkstroom kan worden aangepast voor aanvullende scenario's. 

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter en Azure DevOps integreren

DevOps Starter automatiseert met behulp van Azure DevOps al het werk dat in Azure Pipelines nodig is voor het instellen van een CI/CD-pijplijn. Het maakt een Git-repository in een nieuwe of bestaande Azure DevOps-organisatie en voert vervolgens een voorbeeldtoepassing of uw bestaande code door in een nieuwe Git-repository.  

Met de automatisering wordt ook een CI-trigger voor de build gemaakt, zodat elke nieuwe codedoorvoer een build tot gevolg heeft. DevOps Starter maakt ook een CD-trigger en implementeert elke nieuwe geslaagde build in de Azure-service van uw keuze.  

De build- en release-pijplijnen kunnen worden aangepast voor aanvullende scenario's. U kunt de build- en release-pijplijnen ook klonen voor gebruik in andere projecten.

## <a name="getting-started-with-devops-starter"></a>Aan de slag met DevOps Starter

* [Aan de slag met DevOps Starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>Video's over DevOps Starter

* [Create CI/CD with Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8) (Engelstalig)
