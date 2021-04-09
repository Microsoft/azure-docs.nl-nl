---
title: 'Quickstart: Register maken - PowerShell'
description: Leer snel hoe u een privé-Docker-register in Azure Container Registry maakt met behulp van PowerShell
ms.topic: quickstart
ms.date: 01/22/2019
ms.custom: seodec18, mvc, devx-track-azurepowershell
ms.openlocfilehash: 91d4209ccf558bf7c8038d8a753ec038428bc484
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019997"
---
# <a name="quickstart-create-a-private-container-registry-using-azure-powershell"></a>Quickstart: Een privé-containerregister maken met Azure PowerShell

Azure Container Registry is een beheerde service voor Docker-containerregisters die wordt gebruikt voor het bouwen, opslaan en faciliteren van installatiekopieën van Docker-containers. In deze snelstart leert u hoe u een Azure Container Registry maakt met behulp van PowerShell. Gebruik vervolgens Docker-opdrachten om een containerinstallatiekopie naar het register pushen, waarna u de installatiekopie ophaalt en uitvoert vanuit het register.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor deze quickstart is de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om uw geïnstalleerde versie te bepalen. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

Docker moet ook lokaal zijn geïnstalleerd. Docker biedt pakketten voor [macOS][docker-mac]-, [Windows][docker-windows]- en [Linux][docker-linux]-systemen.

Omdat Azure Cloud-Shell niet alle vereiste Docker-onderdelen bevat (de `dockerd`-daemon), kunt u de Cloud Shell niet voor deze snelstart gebruiken.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met behulp van de opdracht [Connect-AzAccount][Connect-AzAccount] en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

## <a name="create-resource-group"></a>Een resourcegroep maken

Zodra u bent geverifieerd bij Azure, maakt u een resourcegroep met [New-AzResourceGroup][New-AzResourceGroup]. Een resourcegroep is een logische container waarin u uw Azure-resources implementeert en beheert.

```powershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-container-registry"></a>Containerregister maken

Maak vervolgens een containerregister in uw nieuwe resourcegroep met de opdracht [New-AzContainerRegistry][New-AzContainerRegistry].

De registernaam moet uniek zijn binnen Azure en mag 5 tot 50 alfanumerieke tekens bevatten. In het volgende voorbeeld wordt een register met de naam 'myContainerRegistry007' gemaakt. Vervang *myContainerRegistry007* in de volgende opdracht en voer de opdracht vervolgens uit om het register te maken:

```powershell
$registry = New-AzContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

In deze quickstart maakt u een *Basic*-register. Dit is een voor kosten geoptimaliseerde optie voor ontwikkelaars die meer willen leren over Azure Container Registry. Zie [Servicelagen voor Container Registry][container-registry-skus] voor meer informatie over de beschikbare servicelagen.

## <a name="log-in-to-registry"></a>Aanmelden bij register

Voordat u installatiekopieën van containers gaat pushen en ophalen, moet u zich aanmelden bij uw register. In productiescenario's moet u een afzonderlijke identiteit of service-principal gebruiken voor toegang tot het containerregister, maar om deze quickstart niet onnodig lang te maken, gebruikt u de opdracht [Get-AzContainerRegistryCredential][Get-AzContainerRegistryCredential] om de gebruiker met beheerdersrechten in te schakelen voor het register:

```powershell
$creds = Get-AzContainerRegistryCredential -Registry $registry
```

Voer vervolgens de [docker-aanmelding][docker-login] uit om u aan te melden:

```powershell
$creds.Password | docker login $registry.LoginServer -u $creds.Username --password-stdin
```

De opdracht retourneert `Login Succeeded` nadat deze is voltooid.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met de resources die u hebt gemaakt in deze quickstart, gebruikt u de opdracht [Remove-AzResourceGroup][Remove-AzResourceGroup] om de resourcegroep, het containerregister en de daarin opgeslagen containerinstallatiekopieën te verwijderen:

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure Container Registry gemaakt met Azure PowerShell. U hebt een containerinstallatiekopie gepusht en de installatiekopie uit het register opgehaald en uitgevoerd. Ga verder met de zelfstudies voor Azure Container Registry om meer te leren over ACR.

> [!div class="nextstepaction"]
> [Azure Container Registry-zelfstudies][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Zelfstudies over Azure Container Registry-taken][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Get-AzContainerRegistryCredential]: /powershell/module/az.containerregistry/get-azcontainerregistrycredential
[Get-Module]: /powershell/module/microsoft.powershell.core/get-module
[New-AzContainerRegistry]: /powershell/module/az.containerregistry/New-AzContainerRegistry
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[Remove-AzResourceGroup]: /powershell/module/az.resources/remove-azresourcegroup
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
