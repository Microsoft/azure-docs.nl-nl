---
title: Een CI/CD-pijp lijn maken met Azure-pijp lijnen-team data Science process
description: Maak een continue integratie en doorlopende leverings pijplijn voor toepassingen met kunst matige intelligentie (AI) met behulp van docker en Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: af7900faff18d526686b80f23b9de3b3bce5ec5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93309616"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>CI/CD-pijp lijnen maken voor AI-apps met behulp van Azure-pijp lijnen, docker en Kubernetes

Een AI-toepassing (kunst matige intelligentie) is toepassings code die is inge sloten met een model dat is getraind machine learning (ML). Er zijn altijd twee werk stromen voor een AI-toepassing: gegevens wetenschappers bouwen het ML-model en app-ontwikkel aars bouwen de app en geven deze weer voor gebruik door eind gebruikers. In dit artikel wordt beschreven hoe u een pijp lijn voor continue integratie en continue levering (CI/CD) implementeert voor een AI-toepassing waarmee het ML-model in de app-bron code wordt Inge sloten. De voorbeeld code en zelf studie gebruiken een python-fles webtoepassing en halen een vooraf getraind model op uit een persoonlijk Azure Blob-opslag account. U kunt ook een AWS S3-opslag account gebruiken.

> [!NOTE]
> Het volgende proces is een van de verschillende manieren om CI/CD uit te voeren. Er zijn alternatieven voor dit hulp programma en de vereisten.

## <a name="source-code-tutorial-and-prerequisites"></a>Bron code, zelf studie en vereisten

U kunt de [bron code](https://github.com/Azure/DevOps-For-AI-Apps) en een [gedetailleerde zelf studie](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) downloaden van github. Volg de stappen in de zelf studie om een CI/CD-pijp lijn voor uw eigen toepassing te implementeren.

Als u de gedownloade bron code en zelf studie wilt gebruiken, hebt u de volgende vereisten nodig: 

- De [bron code opslagplaats](https://github.com/Azure/DevOps-For-AI-Apps) is gevorkt naar uw github-account
- Een [Azure DevOps-organisatie](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Azure-CLI](/cli/azure/install-azure-cli)
- Een [Azure container service voor een Kubernetes-cluster (AKS)](/previous-versions/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) opdrachten uitvoeren en configuratie ophalen uit het AKS-cluster 
- Een [Azure container Registry-account (ACR)](../../container-registry/container-registry-get-started-portal.md)

## <a name="cicd-pipeline-summary"></a>Overzicht van CI/CD-pijp lijn

Elke nieuwe Git-commiting begint de build-pijp lijn. De build haalt veilig het meest recente ML-model op uit een Blob Storage-account en verpakt het met de app-code in één container. Door deze ontkoppeling van de ontwikkeling van toepassingen en data wetenschappen workstreams zorgt u ervoor dat de nieuwste code van de productie-app altijd wordt uitgevoerd met het laatste ML-model. Als de app wordt getest, slaat de pijp lijn de build-installatie kopie veilig op in een docker-container in ACR. De release pijplijn implementeert vervolgens de container met behulp van AKS. 

## <a name="cicd-pipeline-steps"></a>Stappen voor CI/CD-pijp lijn

In het volgende diagram en de stappen wordt de architectuur van de CI/CD-pijp lijn beschreven:

![Architectuur van CI/CD-pijp lijn](./media/ci-cd-flask/architecture.png)

1. Ontwikkel aars werken aan de code van de toepassing in de IDE van hun keuze.
2. De ontwikkel aars voeren de code door naar Azure opslag plaatsen, GitHub of een andere Git-broncode beheer provider. 
3. Gegevens wetenschappers werken afzonderlijk bij het ontwikkelen van het ML-model.
4. De gegevens wetenschappers publiceren het voltooide model naar een model opslagplaats, in dit geval een Blob Storage-account. 
5. Azure-pijp lijnen worden uitgevoerd op basis van het door voeren van Git.
6. De build pijp lijn haalt het meest recente ML-model op uit Blob Storage en maakt een container.
7. De pijp lijn duwt de installatie kopie naar de opslag plaats voor persoonlijke installatie kopieën in ACR.
8. De release pijplijn start op basis van de succes volle build.
9. De pijp lijn haalt de nieuwste afbeelding van ACR en implementeert deze in het Kubernetes-cluster op AKS.
10. Gebruikers aanvragen voor de app gaan via de DNS-server.
11. De DNS-server geeft de aanvragen door aan een load balancer en stuurt antwoorden terug naar de gebruikers.

## <a name="see-also"></a>Zie ook

- [Team data Science process (TDSP)](./index.yml)
- [Azure Machine Learning (AML)](../index.yml)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Azure Kubernetes Services (AKS)](../../aks/intro-kubernetes.md)