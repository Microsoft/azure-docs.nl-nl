---
title: 'Zelfstudie: Canary-implementaties configureren voor virtuele Linux-machines van Azure'
description: In deze zelfstudie leert u hoe u een pijplijn voor continue implementatie (CD) kunt instellen. Met deze pijplijn wordt een groep virtuele Linux-machines van Azure bijgewerkt met behulp van de blauw-groene implementatiestrategie.
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
ms.openlocfilehash: 4545891cce926f049673cd2c2380a8309f2e71a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552584"
---
# <a name="tutorial---configure-the-blue-green-deployment-strategy-for-azure-linux-virtual-machines"></a>Zelfstudie: de blauw-groene implementatiestrategie configureren voor virtuele Linux-machines van Azure

## <a name="infrastructure-as-a-service-iaas---configure-cicd"></a>Infrastructure as a Service (IaaS): CI/CD configureren

Azure Pipelines biedt een set hulpprogramma's voor het automatiseren van CI/CD voor implementaties op virtuele machines. U kunt vanuit de Azure-portal een pijplijn voor continue levering configureren voor een Azure-VM.

In dit artikel wordt beschreven hoe u een CI/CD-pijplijn instelt die gebruikmaakt van de blauw-groene strategie voor implementatie op meerdere machines. De Azure-portal biedt ook ondersteuning voor andere strategieën, zoals [rolling](./tutorial-devops-azure-pipelines-classic.md) en [canary](./tutorial-azure-devops-canary-strategy.md).

### <a name="configure-cicd-on-virtual-machines"></a>CI/CD configureren op virtuele machines

U kunt virtuele machines als doelen toevoegen aan een [implementatiegroep](/azure/devops/pipelines/release/deployment-groups). U kunt deze machines vervolgens selecteren voor gerichte updates. Nadat u een implementatie hebt uitgevoerd op machines, kunt u de **implementatiegeschiedenis** voor een implementatiegroep weergeven. In deze weergave kunt u precies zien wat er is gebeurd, van de VM naar de pijplijn en vervolgens naar de doorvoerfase (commit).

### <a name="blue-green-deployments"></a>Blauw-groene implementaties

Een blauw-groene implementatie vermindert downtime door een identieke stand-by omgeving te hebben. Er is op elk moment slechts één omgeving actief.

Tijdens de voorbereiding voor een nieuwe release voltooit u de laatste testfase in de groene omgeving. Nadat de software in de groene omgeving werkt, schakelt u het verkeer om zodat alle inkomende aanvragen naar de groene omgeving gaan. De blauwe omgeving is inactief.

Met behulp van de optie voor continue levering kunt u vanuit de Azure-portal blauw- groene implementaties configureren naar uw virtuele machines. Dit zijn kort de te volgen stappen:

1. Meld u aan bij de Azure-portal en ga naar een virtuele machine.
1. Selecteer in het meest linkse deelvenster van de VM-instellingen de optie **Continue levering**. Selecteer vervolgens **Configureren**.

   ![Het deelvenster Continue levering met de knop Configureren](media/tutorial-devops-azure-pipelines-classic/azure-devops-configure.png)

1. Geef in het configuratiescherm bij **Azure DevOps-organisatie** aan of u een bestaand account wilt gebruiken of een nieuw account wilt maken. Selecteer vervolgens het project waaronder u de pijplijn wilt configureren.  

   ![Het deelvenster Continue levering](media/tutorial-devops-azure-pipelines-classic/azure-devops-rolling.png)

1. Een implementatiegroep is een logische set doelmachines voor een implementatie die de fysieke omgevingen voorstellen. Dev, Test, UAT en Productie zijn voorbeelden. U kunt een nieuwe implementatiegroep maken of een bestaande groep selecteren.
1. Selecteer de build-pipeline waarmee het pakket dat moet worden geïmplementeerd, op de virtuele machine wordt gepubliceerd. Het gepubliceerde pakket moet een implementatiescript met de naam deploy.ps1 of deploy.sh bevatten in de map deployscripts in de hoofdmap van het pakket. Dit implementatiescript wordt uitgevoerd door de pijplijn.
1. Selecteer **Blauw-groen** bij **Implementatiestrategie**.
1. Voeg een 'blauw' of 'groen' tag toe aan VM's die onderdeel gaan uitmaken van blauw-groene implementaties. Als een VM bestemd is voor een stand-by rol, labelt u deze als 'groen', en anders als 'blauw'.

   ![Het deelvenster Continue levering, met blauw-groen als de gekozen implementatiestrategie](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-configure.png)

1. Selecteer **OK** om de pijplijn voor continue levering te configureren voor implementatie op de virtuele machine.

   ![De blauw-groen-pijplijn](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-pipeline.png)

1. De implementatiedetails voor de virtuele machine worden weergegeven. U kunt de koppeling selecteren om naar de release-pijplijn in Azure DevOps te gaan. Selecteer in de release-pijplijn **Bewerken** om de pijplijnconfiguratie weer te geven. De pijplijn bestaat uit deze drie fasen:

   1. Deze fase is een fase voor de implementatiegroep. Toepassingen worden geïmplementeerd op stand-by VM's, die worden gelabeld als 'groen'.
   1. In deze fase wordt de pijplijn onderbroken en wordt er gewacht op handmatige interventie om de uitvoering te hervatten. Gebruikers kunnen de uitvoering van de pijplijn hervatten zodra ze handmatig voor de stabiliteit van de implementatie naar VM's met de tag 'groen' hebben gezorgd.
   1. In deze fase worden de tags 'blauw' en 'groen' in de VM's verwisseld. Dit zorgt ervoor dat VM's met oudere toepassingsversies nu als 'groen' worden gelabeld. Tijdens de volgende pijplijnuitvoering worden toepassingen geïmplementeerd op deze VM's.

      ![Het deelvenster Implementatiegroep voor de taak blauw-groen implementeren](media/tutorial-devops-azure-pipelines-classic/azure-devops-blue-green-tasks.png)

1. Met de taak Implementatiescript uitvoeren wordt standaard het implementatiescript deploy.ps1 of deploy.sh uitgevoerd. Het script staat in de map deployscripts in de hoofdmap van het gepubliceerde pakket. Zorg ervoor dat de geselecteerde build-pipeline de implementatie publiceert in de hoofdmap van het pakket.

   ![Het deelvenster Artefacten met deploy.sh in de map deployscripts](media/tutorial-deployment-strategy/package.png)

## <a name="other-deployment-strategies"></a>Andere implementatiestrategieën

- [Strategie voor Rolling-implementatie configureren](./tutorial-devops-azure-pipelines-classic.md)
- [Strategie voor Canary-implementatie configureren](./tutorial-azure-devops-canary-strategy.md)

## <a name="azure-devops-projects"></a>Azure DevOps Projects

U kunt eenvoudig aan de slag met Azure. Met Azure DevOps Projects kunt u uw toepassing in slechts drie stappen uitvoeren in elke Azure-service door het volgende te selecteren:

- Een toepassingstaal
- Een runtime
- Een Azure-service

[Meer informatie](https://azure.microsoft.com/features/devops-projects/).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Implementeren naar virtuele machines van Azure met Azure DevOps Projects](../../devops-project/azure-devops-project-vms.md)
- [Continue implementatie van uw app naar een virtuele-machineschaalset van Azure](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset)