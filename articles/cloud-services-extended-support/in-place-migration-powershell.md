---
title: Migreren naar Azure Cloud Services (uitgebreide ondersteuning) met behulp van Power shell
description: Migreren van Azure Cloud Services (klassiek) naar Azure Cloud Services (uitgebreide ondersteuning) met behulp van Power shell
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: eea49a41e81e7e580becce815ff91aff6aa430d6
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286917"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>Migreren naar Azure Cloud Services (uitgebreide ondersteuning) met behulp van Power shell

Deze stappen laten zien hoe u Azure PowerShell opdrachten kunt gebruiken om te migreren van [Cloud Services (klassiek)](../cloud-services/cloud-services-choose-me.md) naar [Cloud Services (uitgebreide ondersteuning)](overview.md).

> [!IMPORTANT]
> Migratie van Cloud Services (klassiek) naar Cloud Services (uitgebreide ondersteuning) met behulp van het migratie programma is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="1-plan-for-migration"></a>1) migratie plannen
Planning is de belangrijkste stap voor een geslaagde migratie-ervaring. Bekijk het [overzicht van de Cloud Services (uitgebreide ondersteuning)](overview.md) en [plan de migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-plan.md) voordat u een migratie procedure start. 

## <a name="2-install-the-latest-version-of-powershell"></a>2) de nieuwste versie van Power Shell installeren
Er zijn twee belang rijke opties voor het installeren van Azure PowerShell: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) of [Web platform Installer (WebPI)](https://aka.ms/webpi-azps). WebPI ontvangt maandelijkse updates. PowerShell Gallery updates doorlopend worden ontvangen. Dit artikel is gebaseerd op Azure PowerShell versie 2.1.0.

Zie [Azure PowerShell installeren en configureren](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0&preserve-view=true) voor de installatie-instructies.

## <a name="3-ensure-admin-permissions"></a>3) Zorg ervoor dat beheerders machtigingen
Als u deze migratie wilt uitvoeren, moet u worden toegevoegd als een cobeheerder voor het abonnement in de [Azure Portal](https://portal.azure.com).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu **hub** de optie **abonnement**. Als u deze niet ziet, selecteert u **alle services**.
3. Zoek de juiste abonnements vermelding en Bekijk het veld **mijn rol** . Voor een cobeheerder moet de waarde *account beheerder* zijn.

Als u geen mede beheerder kunt toevoegen, neemt u contact op met een service beheerder of mede beheerder voor het abonnement om aan de slag te gaan.

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4) de klassieke provider en de functie Microsoftcompute registreren
Start eerst een Power shell-prompt. Stel uw omgeving in voor de klassieke en Resource Manager voor migratie.

Meld u aan bij uw account voor het Resource Manager-model.

```powershell
Connect-AzAccount
```

Haal de beschik bare abonnementen op met behulp van de volgende opdracht:

```powershell
Get-AzSubscription | Sort Name | Select Name
```

Stel uw Azure-abonnement in voor de huidige sessie. In dit voor beeld wordt de standaard naam van het abonnement ingesteld op **mijn Azure-abonnement**. Vervang de naam van het voor beeld-abonnement door uw eigen.

```powershell
Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

Meld u bij de resource provider voor migratie aan met de volgende opdracht:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> Registratie is een eenmalige stap, maar u moet het eenmaal doen voordat u de migratie uitvoert. Zonder te registreren, wordt het volgende fout bericht weer gegeven:
>
> *Onjuiste aanvraag: het abonnement is niet geregistreerd voor migratie.*

Registreer de CloudServices-functie voor uw abonnement. Het kan enkele minuten duren voordat de registraties zijn voltooid. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Wacht vijf minuten totdat de registratie is voltooid. 

Controleer de status van de goed keuring van de klassieke provider met behulp van de volgende opdracht:

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Controleer de status van de registratie met behulp van het volgende:  
```powershell
Get-AzProviderFeature -FeatureName CloudServices
```

Zorg ervoor dat RegistrationState is `Registered` voor beide voordat u verdergaat.

Voordat u overschakelt naar het klassieke implementatie model, moet u ervoor zorgen dat u voldoende Azure Resource Manager vCPU quota hebt in de Azure-regio van uw huidige implementatie of virtueel netwerk. U kunt de volgende Power shell-opdracht gebruiken om het huidige aantal Vcpu's te controleren dat zich in Azure Resource Manager bevindt. Zie [limieten en de Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)voor meer informatie over vCPU-quota's.

In dit voor beeld wordt de beschik baarheid in de regio **VS-West** gecontroleerd. Vervang de naam van het voorbeeld gebied door eigen.

```powershell
Get-AzVMUsage -Location "West US"
```

Meld u nu aan bij uw account voor het klassieke implementatie model.

```powershell
Add-AzureAccount
```

Haal de beschik bare abonnementen op met behulp van de volgende opdracht:

```powershell
Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Stel uw Azure-abonnement in voor de huidige sessie. In dit voor beeld wordt het standaard abonnement ingesteld op **mijn Azure-abonnement**. Vervang de naam van het voor beeld-abonnement door uw eigen.

```powershell
Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="5-migrate-your-cloud-services"></a>5) uw Cloud Services migreren 
* [Een Cloud service migreren die zich niet in een virtueel netwerk bewaart](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [Een Cloud service in een virtueel netwerk migreren](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> Alle bewerkingen die hier worden beschreven, zijn idempotent. Als er een ander probleem is dan een niet-ondersteunde functie of een configuratie fout, raden wij u aan de bewerking voor voorbereiden, afbreken of door voeren opnieuw uit. Het platform voert vervolgens de actie opnieuw uit.


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5,1) optie 1: een Cloud service migreren die zich niet in een virtueel netwerk bedoet
De lijst met Cloud Services ophalen met behulp van de volgende opdracht. Kies vervolgens de Cloud service die u wilt migreren.

```powershell
Get-AzureService | ft Servicename
```

Haal de implementatie naam op voor de Cloud service. In dit voor beeld is de service naam **mijn service**. Vervang de naam van de voorbeeld service door uw eigen service naam.

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

Controleer eerst of u de Cloud service kunt migreren met behulp van de volgende opdrachten:

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

Met de volgende opdracht worden eventuele waarschuwingen en fouten die de migratie blok keren weer gegeven. Als de validatie is geslaagd, kunt u door gaan naar de stap voorbereiden.

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5,1) optie 2: een Cloud service in een virtueel netwerk migreren

Als u een Cloud service in een virtueel netwerk wilt migreren, migreert u het virtuele netwerk. De Cloud service wordt automatisch gemigreerd met het virtuele netwerk.

> [!NOTE]
> De naam van het virtuele netwerk kan afwijken van wat er in de nieuwe portal wordt weer gegeven. De nieuwe Azure Portal geeft de naam weer als `[vnet-name]` , maar de werkelijke naam van het virtuele netwerk is van het type `Group [resource-group-name] [vnet-name]` . Voordat u met de migratie begint, zoekt u de werkelijke naam van het virtuele netwerk op met behulp van de opdracht `Get-AzureVnetSite | Select -Property Name` of bekijkt u het in de oude Azure Portal. 

In dit voor beeld wordt de naam van het virtuele netwerk ingesteld op **myVnet**. Vervang het voor beeld van een virtueel netwerk door uw eigen naam.

```powershell
$vnetName = "myVnet"
```

Controleer eerst of u het virtuele netwerk kunt migreren met behulp van de volgende opdracht:

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Met de volgende opdracht worden eventuele waarschuwingen en fouten die de migratie blok keren weer gegeven. Als de validatie is geslaagd, kunt u door gaan met de volgende stap voor bereiding:

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Controleer de configuratie voor de voor bereide Cloud service met behulp van Azure PowerShell of de Azure Portal. Als u niet gereed bent voor migratie en u wilt terugkeren naar de oude status, gebruikt u de volgende opdracht:

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Als de voor bereide configuratie goed lijkt, kunt u de resources door lopen en door voeren met de volgende opdracht:

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de sectie wijzigingen in de configuratie van de [migratie](in-place-migration-overview.md#post-migration-changes) voor een overzicht van de wijzigingen in de implementatie bestanden, Automation en andere kenmerken van de nieuwe implementatie van Cloud Services (uitgebreide ondersteuning). 
