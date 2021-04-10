---
title: 'Zelfstudie: Rolling-distributies configureren voor virtuele Linux-machines van Azure'
description: In deze zelfstudie leert u hoe u een pijplijn voor continue implementatie (CD) kunt instellen. Met deze pijplijn wordt een groep virtuele Linux-machines van Azure incrementeel bijgewerkt met behulp van de implementatiestrategie Rolling.
author: moala
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 4/10/2020
ms.author: moala
ms.custom: devops
ms.openlocfilehash: dd47250989be5c31d5f0ade2b602b9d6af535d83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563996"
---
# <a name="tutorial---configure-the-rolling-deployment-strategy-for-azure-linux-virtual-machines"></a>Zelfstudie: De implementatiestrategie Rolling configureren voor virtuele Linux-machines van Azure

Azure DevOps is een ingebouwde Azure-service waarmee elk deel van het DevOps-proces wordt geautomatiseerd voor elke Azure-resource. Ongeacht of uw app gebruikmaakt van virtuele machines, web-apps, Kubernetes of een andere resource, kunt u infrastructuur als code (IaC) implementeren, maar ook continue integratie, continue tests, continue levering en continue bewaking met Azure en Azure DevOps.

![De Azure-portal met Azure DevOps geselecteerd onder Services](media/tutorial-devops-azure-pipelines-classic/azdevops-view.png)

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastructure as a Service (IaaS): CI/CD configureren

Azure Pipelines biedt een set hulpprogramma's voor het automatiseren van CI/CD voor implementaties op virtuele machines. U kunt vanuit de Azure-portal een pijplijn voor continue levering configureren voor een Azure-VM.

In dit artikel wordt beschreven hoe u een CI/CD-pijplijn instelt die gebruikmaakt van de Rolling-strategie voor implementatie op meerdere machines vanuit de Azure-portal. De Azure-portal biedt ook ondersteuning voor andere strategieën, zoals [Canary](./tutorial-azure-devops-canary-strategy.md) en [Blue-Green](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>CI/CD configureren op virtuele machines

U kunt virtuele machines als doelen toevoegen aan een [implementatiegroep](/azure/devops/pipelines/release/deployment-groups). U kunt deze machines vervolgens selecteren voor gerichte updates. Nadat u een implementatie hebt uitgevoerd op machines, kunt u de **implementatiegeschiedenis** voor een implementatiegroep weergeven. In deze weergave kunt u precies zien wat er is gebeurd, van de VM naar de pijplijn en vervolgens naar de doorvoerfase (commit).

### <a name="rolling-deployments"></a>Rolling-implementaties

Bij elke iteratie vervangt een rolling-implementatie exemplaren van de vorige versie van de toepassing. Ze worden vervangen door exemplaren van de nieuwe versie op een vaste set machines (rolling-set). De volgende procedure laat zien hoe u een rolling-update configureert voor virtuele machines.

Met behulp van de optie voor continue levering kunt u vanuit de Azure-portal rolling-updates configureren naar uw virtuele machines. Dit zijn kort de te volgen stappen:

1. Meld u aan bij de Azure-portal en ga naar een virtuele machine.
1. Selecteer in het meest linkse deelvenster van de VM-instellingen de optie **Continue levering**. Selecteer vervolgens **Configureren**.

   ![Het deelvenster Continue levering met de knop Configureren](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Geef in het configuratiescherm bij **Azure DevOps-organisatie** aan of u een bestaand account wilt gebruiken of een nieuw account wilt maken. Selecteer vervolgens het project waaronder u de pijplijn wilt configureren.  

   ![Het deelvenster Continue levering](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Een implementatiegroep is een logische set doelmachines voor een implementatie die de fysieke omgevingen voorstellen. Dev, Test, UAT en Productie zijn voorbeelden. U kunt een nieuwe implementatiegroep maken of een bestaande groep selecteren.
1. Selecteer de build-pipeline waarmee het pakket dat moet worden geïmplementeerd, op de virtuele machine wordt gepubliceerd. Het gepubliceerde pakket moet een implementatiescript met de naam deploy.ps1 of deploy.sh bevatten in de map deployscripts in de hoofdmap van het pakket. Dit implementatiescript wordt uitgevoerd door de pijplijn.
1. Selecteer **Rolling** bij **Implementatiestrategie**.
1. U kunt desgewenst elke machine taggen met de bijbehorende rol. De tags 'web' en 'db' zijn voorbeelden. Deze tags helpen om implementaties alleen uit te voeren op VM's met een specifieke rol.
1. Selecteer **OK** om de pijplijn voor continue levering te configureren.
1. Nadat de configuratie is voltooid, hebt u een doorlopende pijplijn voor continue levering geconfigureerd om te implementeren op de virtuele machine.  

   ![Het deelvenster Continue levering met de implementatiegeschiedenis](media/tutorial-devops-azure-pipelines-classic/azure-devops-deployment-history.png)

1. De implementatiedetails voor de virtuele machine worden weergegeven. U kunt de koppeling selecteren om naar de pijplijn te gaan, **Release-1**, om de implementatie weer te geven. Selecteer **Bewerken** om de definitie van de release-pijplijn te wijzigen.

1. Als u meerdere VM's configureert, herhaalt u stap 2 tot en met 4 om andere VM's toe te voegen aan de implementatiegroep. Als u een implementatiegroep selecteert die al een pijplijnuitvoering heeft, worden de VM's alleen aan de implementatiegroep toegevoegd. Er worden dan geen nieuwe pijplijnen gemaakt.
1. Nadat de configuratie is voltooid, selecteert u de pijplijndefinitie, gaat u naar de Azure DevOps-organisatie en selecteert u **Bewerken** voor de release-pijplijn.

   ![De rolling-pijplijn bewerken](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline.png)

1. Selecteer **1 job, 1 taak** in de fase **dev**. Selecteer de fase **Implementeren**.

   ![Taken voor een rolling-pijplijn met de taak Implementeren geselecteerd](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling-pipeline-tasks.png)

1. In het rechterdeelvenster van het configuratiescherm kunt u het aantal machines opgeven dat u parallel wilt implementeren in elke iteratie. Als u op meerdere machines tegelijk wilt implementeren, kunt u het aantal machines opgeven als een percentage met behulp van de schuifregelaar.  

1. Met de taak Implementatiescript uitvoeren wordt standaard het implementatiescript deploy.ps1 of deploy.sh uitgevoerd. Het script staat in de map deployscripts in de hoofdmap van het gepubliceerde pakket.

   ![Het deelvenster Artefacten met deploy.sh in de map deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Andere implementatiestrategieën

- [Strategie voor Canary-implementatie configureren](./tutorial-azure-devops-canary-strategy.md)
- [Strategie voor Blue-Green-implementatie configureren](./tutorial-azure-devops-blue-green-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

U kunt eenvoudig aan de slag met Azure. Met Azure DevOps Projects kunt u uw toepassing in slechts drie stappen uitvoeren in elke Azure-service door het volgende te selecteren:

- Een toepassingstaal
- Een runtime
- Een Azure-service
 
[Meer informatie](https://azure.microsoft.com/features/devops-projects/).
 
## <a name="additional-resources"></a>Aanvullende bronnen

- [Implementeren naar virtuele machines van Azure met Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Continue implementatie van uw app naar een virtuele-machineschaalset van Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)