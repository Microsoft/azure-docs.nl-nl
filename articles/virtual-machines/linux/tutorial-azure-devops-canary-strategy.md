---
title: 'Zelfstudie: Canary-distributies configureren voor virtuele Linux-machines van Azure'
description: In deze zelfstudie leert u hoe u een pijplijn voor continue implementatie (CD) kunt instellen. Met deze pijplijn wordt een groep virtuele Linux-machines van Azure bijgewerkt met behulp van de implementatiestrategie Canary.
author: moala
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
ms.openlocfilehash: bbfe6571cf075b2ce4930eea91bfd1e239470c5a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552504"
---
# <a name="tutorial---configure-the-canary-deployment-strategy-for-azure-linux-virtual-machines"></a>Zelfstudie: De implementatiestrategie Canary configureren voor virtuele Linux-machines van Azure

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastructure as a Service (IaaS): CI/CD configureren

Azure Pipelines biedt een set hulpprogramma's voor het automatiseren van CI/CD voor implementaties op virtuele machines. U kunt vanuit de Azure-portal een pijplijn voor continue levering configureren voor een Azure-VM.

In dit artikel wordt beschreven hoe u een CI/CD-pijplijn instelt die gebruikmaakt van de Canary-strategie voor implementatie op meerdere machines. De Azure-portal biedt ook ondersteuning voor andere strategieën, zoals [Rolling](./tutorial-devops-azure-pipelines-classic.md) en [Blue-Green](./tutorial-azure-devops-blue-green-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>CI/CD configureren op virtuele machines

U kunt virtuele machines als doelen toevoegen aan een [implementatiegroep](/azure/devops/pipelines/release/deployment-groups). U kunt deze machines vervolgens selecteren voor gerichte updates. Nadat u een implementatie hebt uitgevoerd op machines, kunt u de **implementatiegeschiedenis** voor een implementatiegroep weergeven. In deze weergave kunt u precies zien wat er is gebeurd, van de VM naar de pijplijn en vervolgens naar de doorvoerfase (commit).

### <a name="canary-deployments"></a>Canary-implementaties

Een Canary-implementatie vermindert het risico door wijzigingen langzaam uit te rollen naar een kleine subset van gebruikers. Als u voldoende vertrouwen hebt in de nieuwe versie, kunt u deze vrijgeven aan meer servers in uw infrastructuur en er meer gebruikers naartoe leiden.

Met behulp van de optie voor continue levering kunt u vanuit de Azure-portal Canary-implementaties configureren naar uw virtuele machines. Dit zijn kort de te volgen stappen:

1. Meld u aan bij de Azure-portal en ga naar een virtuele machine.
1. Selecteer in het meest linkse deelvenster van de VM-instellingen de optie **Continue levering**. Selecteer vervolgens **Configureren**.

   ![Het deelvenster Continue levering met de knop Configureren](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Geef in het configuratiescherm bij **Azure DevOps-organisatie** aan of u een bestaand account wilt gebruiken of een nieuw account wilt maken. Selecteer vervolgens het project waaronder u de pijplijn wilt configureren.  

   ![Het deelvenster Continue levering](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Een implementatiegroep is een logische set doelmachines voor een implementatie die de fysieke omgevingen voorstellen. Dev, Test, UAT en Productie zijn voorbeelden. U kunt een nieuwe implementatiegroep maken of een bestaande groep selecteren.
1. Selecteer de build-pipeline waarmee het pakket dat moet worden geïmplementeerd, op de virtuele machine wordt gepubliceerd. Het gepubliceerde pakket moet een implementatiescript met de naam deploy.ps1 of deploy.sh bevatten in de map deployscripts in de hoofdmap van het pakket. Dit implementatiescript wordt uitgevoerd door de pijplijn.
1. Selecteer **Canary** bij **Implementatiestrategie**.
1. Voeg de tag 'canary' toe aan VM's die deel moeten uitmaken van Canary-implementaties. Voeg de tag 'prod' toe aan VM's die deel uitmaken van implementaties die moeten worden uitgevoerd nadat de Canary-implementatie is voltooid. Tags helpen om implementaties alleen uit te voeren op VM's met een specifieke rol.

   ![Het deelvenster Continue levering, met Canary als de gekozen implementatiestrategie](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure-canary.png)

1. Selecteer **OK** om de pijplijn voor continue levering te configureren voor implementatie op de virtuele machine.

   ![De Canary-pijplijn](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-pipeline.png)

1. De implementatiedetails voor de virtuele machine worden weergegeven. U kunt de koppeling selecteren om naar de release-pijplijn in Azure DevOps te gaan. Selecteer in de release-pijplijn **Bewerken** om de pijplijnconfiguratie weer te geven. De pijplijn bestaat uit deze drie fasen:

   1. Deze fase is een fase voor de implementatiegroep. Toepassingen worden geïmplementeerd op VM's die de tag 'canary' hebben.
   1. In deze fase wordt de pijplijn onderbroken en wordt er gewacht op handmatige interventie om de uitvoering te hervatten.
   1. Dit is ook een fase voor de implementatiegroep. De update wordt nu geïmplementeerd op VM's met de tag 'prod'.

      ![Het deelvenster Implementatiegroep voor de taak Canary implementeren](media/tutorial-devops-azure-pipelines-classic/azure-devops-canary-task.png)

1. Zorg ervoor dat ten minste één VM de tag 'prod' heeft voordat u de pijplijn verder uitvoert. In de derde fase van de pijplijn worden toepassingen alleen geïmplementeerd op VM's die de tag 'prod' hebben.

1. Met de taak Implementatiescript uitvoeren wordt standaard het implementatiescript deploy.ps1 of deploy.sh uitgevoerd. Het script staat in de map deployscripts in de hoofdmap van het gepubliceerde pakket. Zorg ervoor dat de geselecteerde build-pipeline de implementatie publiceert in de hoofdmap van het pakket.

   ![Het deelvenster Artefacten met deploy.sh in de map deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Andere implementatiestrategieën
- [Strategie voor Rolling-implementatie configureren](./tutorial-devops-azure-pipelines-classic.md)
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