---
title: Windows 10 implementeren op Azure met multi tenant-hosting rechten
description: Lees hoe u uw voor delen voor Windows-Software Assurance kunt maximaliseren om on-premises licenties naar Azure te brengen met multi tenant-hosting rechten.
author: mimckitt
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt
ms.openlocfilehash: bb86ba6867ad796ef0f5eeb1357a6df9e93e9f9e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555768"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Windows 10 implementeren op Azure met multi tenant-hosting rechten 
Voor klanten met Windows 10 Enter prise E3/E5 per gebruiker of Windows Virtual Desktop Access per gebruiker (licenties voor gebruikers abonnement of licenties voor gebruikers abonnementen), kunt u met multi tenant hosting rechten voor Windows 10 uw Windows 10-licenties naar de Cloud brengen en Windows 10-Virtual Machines op Azure uitvoeren zonder dat u voor een andere licentie betaalt. Multi tenant-hosting rechten zijn alleen beschikbaar voor Windows 10 (versie 1703 of hoger).

Zie [multi tenant-hosting voor Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)voor meer informatie.

> [!NOTE]
> - Zie [Windows-client in azure voor ontwikkel-en test scenario's](client-images.md) voor het gebruik van Windows 7, 8,1 en 10 installatie kopieën voor ontwikkeling of testen.
> - Raadpleeg voor delen van Windows Server-licentie verlening voor delen [van Azure Hybrid use voor Windows Server-installatie kopieën](hybrid-use-benefit-licensing.md).

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>Abonnements licenties die in aanmerking komen voor multi tenant-hosting rechten

Met het [micro soft-beheer centrum](/microsoft-365/admin/admin-overview/about-the-admin-center)kunt u controleren of aan een gebruiker een door Windows 10 ondersteund licentie is toegewezen.

> [!IMPORTANT]
> Gebruikers moeten een van de volgende abonnements licenties hebben om Windows 10-installatie kopieën in azure te kunnen gebruiken. Als u niet over een van deze abonnements licenties beschikt, kunnen ze worden aangeschaft via uw [Cloud service partner](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/) of rechtstreeks via [micro soft](https://www.microsoft.com/microsoft-365?rtc=1).

**Licenties voor in aanmerking komende abonnementen:**

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 a3/A5 
-   Windows 10 Enter prise E3/E5
-   Windows 10-onderwijs a3/A5 
-   Windows VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Windows 10-installatie kopie implementeren vanuit Azure Marketplace 
Voor implementaties met Power shell, CLI en Azure Resource Manager-sjabloon kunt u Windows 10-installatie kopieën vinden met behulp van de `PublisherName: MicrosoftWindowsDesktop` en `Offer: Windows-10` . Update van Windows 10 versie Creators (1809) of hoger wordt ondersteund voor multi tenant-hosting rechten. 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

Zie [Azure Marketplace-VM-installatie kopieën zoeken en gebruiken met Azure PowerShell](./cli-ps-findimage.md) voor meer informatie over beschik bare afbeeldingen

## <a name="uploading-windows-10-vhd-to-azure"></a>Windows 10 VHD uploaden naar Azure
Als u een gegeneraliseerde virtuele harde schijf met Windows 10 uploadt, moet u er rekening mee houden dat Windows 10 geen ingebouwd Administrator-account standaard is ingeschakeld. Als u het ingebouwde Administrator account wilt inschakelen, neemt u de volgende opdracht op als onderdeel van de aangepaste script extensie.

```powershell
Net user <username> /active:yes
```

Het volgende Power shell-fragment is het markeren van alle beheerders accounts als actief, met inbegrip van de ingebouwde Administrator. Dit voor beeld is handig als de ingebouwde gebruikers naam van de beheerder onbekend is.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Voor meer informatie: 
* [VHD uploaden naar Azure](upload-generalized-managed.md)
* [Een Windows-VHD voorbereiden om te uploaden naar Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Implementatie van Windows 10 met multi tenant-hosting rechten
Zorg ervoor dat u [de nieuwste Azure PowerShell hebt geïnstalleerd en geconfigureerd](/powershell/azure/). Nadat u uw VHD hebt voor bereid, uploadt u de VHD naar uw Azure Storage-account met de `Add-AzVhd` cmdlet als volgt:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Implementeren met behulp van Azure Resource Manager-sjabloon implementatie** In uw Resource Manager-sjablonen kunt u een extra para meter voor `licenseType` opgeven. U kunt meer lezen over het [ontwerpen van Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-syntax.md). Wanneer u uw VHD hebt geüpload naar Azure, bewerkt u de Resource Manager-sjabloon zodanig dat het licentie type wordt opgenomen als onderdeel van de compute-provider en uw sjabloon als normaal implementeren:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Implementeren via Power shell** Wanneer u uw Windows Server-VM implementeert via Power shell, hebt u een extra para meter voor `-LicenseType` . Zodra u uw VHD hebt geüpload naar Azure, maakt u een VM met `New-AzVM` en geeft u het type licentie als volgt op:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Controleren of uw virtuele machine gebruikmaakt van het voor deel van de licentie
Nadat u uw virtuele machine hebt geïmplementeerd met de implementatie methode Power shell of Resource Manager, controleert u het licentie type `Get-AzVM` als volgt:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

De uitvoer is vergelijkbaar met het volgende voor beeld voor Windows 10 met het juiste licentie type:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Deze uitvoer is in tegens telling tot de volgende VM die zonder Azure Hybrid Use Benefit-licentie verlening is geïmplementeerd, zoals een virtuele machine die rechtstreeks vanuit de Azure-galerie wordt geïmplementeerd:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Meer informatie over deelname aan Azure AD
Azure richt zich op alle Windows-Vm's met het ingebouwde Administrator account, dat niet kan worden gebruikt om lid te worden van AAD. Zo werken *instellingen > Account > toegang tot werk of School > + Connect* niet. U moet als een tweede beheerders account worden gemaakt en aangemeld om hand matig lid te worden van Azure AD. U kunt Azure AD ook configureren met behulp van een inrichtings pakket, met behulp van de koppeling in het gedeelte *volgende stappen* voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [configureren van VDA voor Windows 10](/windows/deployment/vda-subscription-activation)
- Meer informatie over [multi tenant hosting voor Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)