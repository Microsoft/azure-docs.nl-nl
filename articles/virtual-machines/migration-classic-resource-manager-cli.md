---
title: Vm's migreren naar Resource Manager met behulp van Azure CLI
description: In dit artikel wordt de door het platform ondersteunde migratie van resources van klassiek naar Azure Resource Manager door lopen met behulp van Azure CLI.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: a6f6783819d97ec97b93f86b687cf3093d6f4209
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94904706"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>IaaS-resources migreren van klassiek naar Azure Resource Manager met behulp van Azure CLI

> [!IMPORTANT]
> Nu gebruiken we op ongeveer 90% IaaS Vm's [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Vanaf 28 februari 2020 zijn klassieke Vm's afgeschaft en worden ze volledig buiten gebruik gesteld op 1 maart 2023. Meer [informatie]( https://aka.ms/classicvmretirement) over deze afschaffing en [hoe dit van invloed is op u](classic-vm-deprecation.md#how-does-this-affect-me).

In deze stappen ziet u hoe u de opdracht regel interface (CLI) van Azure kunt gebruiken om IaaS-resources (Infrastructure as a Service) te migreren van het klassieke implementatie model naar het Azure Resource Manager-implementatie model. Voor het artikel is de [klassieke Azure-cli](/cli/azure/install-classic-cli)vereist. Omdat Azure CLI alleen van toepassing is op Azure Resource Manager-resources, kan deze niet worden gebruikt voor deze migratie.

> [!NOTE]
> Alle bewerkingen die hier worden beschreven, zijn idempotent. Als er een ander probleem is dan een niet-ondersteunde functie of een configuratie fout, raden wij u aan de bewerking voor voorbereiden, afbreken of door voeren opnieuw uit. Het platform zal vervolgens de actie opnieuw proberen.
> 
> 

<br>
Hier volgt een stroom diagram om de volg orde te bepalen waarin de stappen moeten worden uitgevoerd tijdens een migratie proces

![Schermafbeelding van de migratiestappen](./media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>Stap 1: voorbereiden op migratie
Hier volgen enkele aanbevolen procedures voor het evalueren van de migratie van IaaS-resources van klassiek naar Resource Manager:

* Lees de [lijst met niet-ondersteunde configuraties of functies](migration-classic-resource-manager-overview.md). Als u virtuele machines hebt die niet-ondersteunde configuraties of functies gebruiken, raden wij u aan te wachten tot de ondersteuning van de functie/configuratie wordt aangekondigd. U kunt deze functie ook verwijderen of van die configuratie verplaatsen om migratie mogelijk te maken als deze aan uw behoeften voldoet.
* Als u geautomatiseerde scripts hebt waarmee u vandaag nog uw infra structuur en toepassingen implementeert, kunt u een vergelijk bare test configuratie maken met behulp van deze scripts voor migratie. U kunt ook voorbeeld omgevingen instellen met behulp van de Azure Portal.

> [!IMPORTANT]
> Toepassings gateways worden momenteel niet ondersteund voor migratie van klassiek naar Resource Manager. Als u een klassiek virtueel netwerk met een toepassings gateway wilt migreren, verwijdert u de gateway voordat u een voorbereidings bewerking uitvoert om het netwerk te verplaatsen. Nadat u de migratie hebt voltooid, maakt u opnieuw verbinding met de gateway in Azure Resource Manager. 
>
>ExpressRoute gateways die verbinding maken met ExpressRoute-circuits in een ander abonnement, kunnen niet automatisch worden gemigreerd. In dergelijke gevallen verwijdert u de ExpressRoute-gateway, migreert u het virtuele netwerk en maakt u de gateway opnieuw. Zie [ExpressRoute-circuits en gekoppelde virtuele netwerken van het klassieke naar het Resource Manager-implementatie model migreren](../expressroute/expressroute-migration-classic-resource-manager.md) voor meer informatie.
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>Stap 2: uw abonnement instellen en de provider registreren
Voor migratie scenario's moet u uw omgeving instellen voor klassieke en Resource Manager. [Installeer Azure cli](/cli/azure/install-classic-cli) en [Selecteer uw abonnement](/cli/azure/authenticate-azure-cli).

Meld u aan bij uw account.

```azurecli
azure login
```

Selecteer het Azure-abonnement met behulp van de volgende opdracht.

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> Registratie is een eenmalige stap, maar deze moet eenmaal worden uitgevoerd voordat u de migratie uitvoert. Zonder registratie wordt het volgende fout bericht weer gegeven 
> 
> *Onjuiste aanvraag: het abonnement is niet geregistreerd voor migratie.* 
> 
> 

Meld u bij de resource provider voor migratie aan met de volgende opdracht. Houd er rekening mee dat in sommige gevallen een time-out optreedt voor deze opdracht. De registratie slaagt echter wel.

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

Wacht vijf minuten totdat de registratie is voltooid. U kunt de status van de goed keuring controleren met behulp van de volgende opdracht. Zorg ervoor dat RegistrationState is `Registered` voordat u doorgaat.

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

Schakel de CLI nu over naar de `asm` modus.

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Stap 3: Zorg ervoor dat u voldoende Azure Resource Manager virtuele-Vcpu's in de Azure-regio van uw huidige implementatie of VNET hebt
Voor deze stap moet u overschakelen naar de `arm` modus. Doe dit met de volgende opdracht.

```azurecli
azure config mode arm
```

U kunt de volgende CLI-opdracht gebruiken om het huidige aantal Vcpu's te controleren dat zich in Azure Resource Manager bevindt. Zie [limieten en de Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)voor meer informatie over vCPU-quota's.

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

Zodra u klaar bent met het controleren van deze stap, kunt u terugschakelen naar de `asm` modus.

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>Stap 4: optie 1-virtuele machines in een Cloud service migreren
Bekijk de lijst met Cloud Services met behulp van de volgende opdracht en kies vervolgens de Cloud service die u wilt migreren. Als de virtuele machines in de Cloud service zich in een virtueel netwerk bevinden of als ze beschikken over web-en werk rollen, wordt er een fout bericht weer gegeven.

```azurecli
azure service list
```

Voer de volgende opdracht uit om de implementatie naam voor de Cloud service op te halen uit de uitgebreide uitvoer. In de meeste gevallen is de naam van de implementatie hetzelfde als de naam van de Cloud service.

```azurecli
azure service show <serviceName> -vv
```

Controleer eerst of u de Cloud service kunt migreren met behulp van de volgende opdrachten:

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

Bereid de virtuele machines in de Cloud service voor op migratie. U kunt kiezen uit twee opties.

Als u de Vm's wilt migreren naar een virtueel netwerk dat door een platform is gemaakt, gebruikt u de volgende opdracht.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

Als u wilt migreren naar een bestaand virtueel netwerk in het Resource Manager-implementatie model, gebruikt u de volgende opdracht.

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

Nadat de voor bereiding is voltooid, kunt u de uitgebreide uitvoer bekijken om de migratie status van de Vm's op te halen en ervoor te zorgen dat deze zich in de staat bevinden `Prepared` .

```azurecli
azure vm show <vmName> -vv
```

Controleer de configuratie voor de voor bereide bronnen met behulp van CLI of de Azure Portal. Als u niet gereed bent voor migratie en u wilt terugkeren naar de oude status, gebruikt u de volgende opdracht.

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

Als de voor bereide configuratie goed lijkt, kunt u de resources door lopen en door voeren met de volgende opdracht.

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>Stap 4: optie 2-virtuele machines in een virtueel netwerk migreren
Kies het virtuele netwerk dat u wilt migreren. Houd er rekening mee dat als het virtuele netwerk web-of werk rollen of Vm's met niet-ondersteunde configuraties bevat, een validatie fout bericht wordt weer gegeven.

Gebruik de volgende opdracht om alle virtuele netwerken in het abonnement op te halen.

```azurecli
azure network vnet list
```

De uitvoer ziet er ongeveer uit zoals in dit voorbeeld:

![Scherm opname van de opdracht regel met de volledige naam van het virtuele netwerk gemarkeerd.](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

In het bovenstaande voor beeld is **virtualNetworkName** de volledige naam **"groep classicubuntu16 classicubuntu16"**.

Controleer eerst of u het virtuele netwerk kunt migreren met behulp van de volgende opdracht:

```shell
azure network vnet validate-migration <virtualNetworkName>
```

Bereid het virtuele netwerk van uw keuze voor op de migratie met behulp van de volgende opdracht.

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

Controleer de configuratie voor de voor bereide virtuele machines met behulp van CLI of de Azure Portal. Als u niet gereed bent voor migratie en u wilt terugkeren naar de oude status, gebruikt u de volgende opdracht.

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

Als de voor bereide configuratie goed lijkt, kunt u de resources door lopen en door voeren met de volgende opdracht.

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>Stap 5: een opslag account migreren
Wanneer u klaar bent met het migreren van de virtuele machines, raden we u aan om het opslag account te migreren.

Bereid het opslag account voor op de migratie met behulp van de volgende opdracht

```azurecli
azure storage account prepare-migration <storageAccountName>
```

Controleer de configuratie voor het voor bereide opslag account met behulp van CLI of de Azure Portal. Als u niet gereed bent voor migratie en u wilt terugkeren naar de oude status, gebruikt u de volgende opdracht.

```azurecli
azure storage account abort-migration <storageAccountName>
```

Als de voor bereide configuratie goed lijkt, kunt u de resources door lopen en door voeren met de volgende opdracht.

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Power shell gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Community tools voor hulp bij de migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md)
* [Bekijk de veelgestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md)
