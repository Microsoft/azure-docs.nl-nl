---
title: Stoppen met het gebruik van een beheerde identiteit VM-extensie-Azure AD
description: Stapsgewijze instructies om het gebruik van de VM-extensie te stoppen en de Azure Instance Metadata Service (IMDS) te gebruiken voor verificatie.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2020
ms.author: barclayn
ROBOTS: NOINDEX
ms.openlocfilehash: dca5f9ed2911ae3042fb9871f849212ec18b1b58
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539380"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Stoppen met het gebruik van de extensie voor beheerde identiteiten van de virtuele machine en het gebruik van Azure Instance Metadata Service

## <a name="virtual-machine-extension-for-managed-identities"></a>Extensie van de virtuele machine voor beheerde identiteiten

De extensie van de virtuele machine voor beheerde identiteiten wordt gebruikt om tokens aan te vragen voor een beheerde identiteit in de virtuele machine. De werk stroom bestaat uit de volgende stappen:

1. Ten eerste roept de werk belasting binnen de resource het lokale eind punt `http://localhost/oauth2/token` aan om een toegangs token aan te vragen.
2. De extensie van de virtuele machine gebruikt vervolgens de referenties voor de beheerde identiteit om een toegangs token aan te vragen bij Azure AD. 
3. Het toegangs token wordt geretourneerd naar de aanroeper en kan worden gebruikt om te verifiëren bij services die ondersteuning bieden voor Azure AD-verificatie, zoals Azure Key Vault of Azure Storage.

Als gevolg van verschillende beperkingen die in de volgende sectie worden beschreven, is de beheerde identiteits-VM-extensie vervangen door het gebruik van het equivalente eind punt in azure Instance Metadata Service (IMDS)

### <a name="provision-the-extension"></a>De uitbrei ding inrichten 

Wanneer u een schaalset voor een virtuele machine of virtuele machine configureert voor een beheerde identiteit, kunt u eventueel de beheerde identiteiten voor de VM-extensie van Azure-resources inrichten met behulp `-Type` van de para meter voor de cmdlet [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) . U kunt ofwel `ManagedIdentityExtensionForWindows` of `ManagedIdentityExtensionForLinux` , afhankelijk van het type van de virtuele machine, een naam geven met behulp van de `-Name` para meter. De `-Settings` para meter geeft de poort die wordt gebruikt door het OAuth-token-eind punt voor het ophalen van tokens:

```azurepowershell-interactive
$settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

U kunt ook de Azure Resource Manager-implementatie sjabloon gebruiken om de VM-extensie in te richten door de volgende JSON toe te voegen aan de `resources` sectie aan de sjabloon (gebruik `ManagedIdentityExtensionForLinux` de naam en het type van de elementen voor de Linux-versie).

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
    "apiVersion": "2018-06-01",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.ManagedIdentity",
        "type": "ManagedIdentityExtensionForWindows",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "port": 50342
        }
    }
}
```
    
    
Als u werkt met schaal sets voor virtuele machines, kunt u ook de beheerde identiteiten voor de virtuele-machine Scale set-extensie van Azure-resources inrichten met de cmdlet [add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) . U kunt ofwel bepalen `ManagedIdentityExtensionForWindows` of `ManagedIdentityExtensionForLinux` , afhankelijk van het type schaalset voor virtuele machines, en deze een naam geven met behulp van de `-Name` para meter. De `-Settings` para meter geeft de poort die wordt gebruikt door het OAuth-token-eind punt voor het ophalen van tokens:

   ```azurepowershell-interactive
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Als u de extensie voor virtuele-machine schaal sets wilt inrichten met de sjabloon voor de Azure Resource Manager-implementatie, voegt u de volgende JSON toe aan de `extensionpProfile` sectie aan de sjabloon (gebruik `ManagedIdentityExtensionForLinux` de naam en het type van de elementen voor de Linux-versie).

```json
"extensionProfile": {
    "extensions": [
        {
            "name": "ManagedIdentityWindowsExtension",
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                },
                "protectedSettings": {}
            }
        }
```

Het inrichten van de extensie van de virtuele machine kan mislukken vanwege fouten in de DNS-zoek actie. Als dit het geval is, start u de virtuele machine opnieuw op en probeert u het opnieuw. 

### <a name="remove-the-extension"></a>De extensie verwijderen 
Als u de uitbrei ding wilt verwijderen, gebruikt `-n ManagedIdentityExtensionForWindows` of `-n ManagedIdentityExtensionForLinux` schakelt u (afhankelijk van het type virtuele machine) met [AZ VM extension delete](/cli/azure/vm/)of [AZ vmss extension delete](/cli/azure/vmss) voor Virtual Machine Scale sets met behulp van Azure CLI of `Remove-AzVMExtension` voor Power shell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```azurepowershell-interactive
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Een token verkrijgen met de extensie van de virtuele machine

Hier volgt een voor beeld van een aanvraag met behulp van het eind punt beheerde identiteiten voor Azure-resources VM Extension:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beschrijving |
| ------- | ----------- |
| `GET` | De HTTP-term waarmee wordt aangegeven dat u gegevens wilt ophalen uit het eind punt. In dit geval een OAuth-toegangs token. | 
| `http://localhost:50342/oauth2/token` | Het eind punt Managed Identities voor Azure resources, waarbij 50342 de standaard poort is en kan worden geconfigureerd. |
| `resource` | Een query teken reeks parameter, waarmee de App-ID-URI van de doel resource wordt aangegeven. Het wordt ook weer gegeven in de `aud` claim (publiek) van het uitgegeven token. In dit voor beeld wordt een token aangevraagd voor toegang tot Azure Resource Manager, dat een app-ID-URI van heeft `https://management.azure.com/` . |
| `Metadata` | Een veld met een HTTP-aanvraag header, dat door beheerde identiteiten voor Azure-resources wordt vereist als risico op aanvallen op server zijde vervalsing (SSRF). Deze waarde moet in alle kleine letters worden ingesteld op ' True '.|
| `object_id` | Beschrijving Een query reeks parameter, waarmee de object_id van de beheerde identiteit wordt aangegeven waarvoor u het token wilt voor. Vereist als uw virtuele machine meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|
| `client_id` | Beschrijving Een query reeks parameter, waarmee de client_id van de beheerde identiteit wordt aangegeven waarvoor u het token wilt voor. Vereist als uw virtuele machine meerdere door de gebruiker toegewezen beheerde identiteiten heeft.|


Voorbeeld antwoord:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Beschrijving |
| ------- | ----------- |
| `access_token` | Het aangevraagde toegangs token. Wanneer u een beveiligd REST API aanroept, wordt het token in het `Authorization` veld aanvraag header Inge sloten als een Bearer-token, waardoor de API de aanroeper kan verifiëren. | 
| `refresh_token` | Wordt niet gebruikt door beheerde identiteiten voor Azure-resources. |
| `expires_in` | Het aantal seconden dat het toegangs token geldig blijft, vóór verloop tijd, vanaf tijdstip van uitgifte. De tijd van de uitgifte vindt u in de claim van het token `iat` . |
| `expires_on` | De time span op het moment dat het toegangs token verloopt. De datum wordt weer gegeven als het aantal seconden van ' 1970-01-01T0:0: 0Z UTC ' (komt overeen met de claim van het token `exp` ). |
| `not_before` | De time span wanneer het toegangs token van kracht is en kan worden geaccepteerd. De datum wordt weer gegeven als het aantal seconden van ' 1970-01-01T0:0: 0Z UTC ' (komt overeen met de claim van het token `nbf` ). |
| `resource` | De bron waarvoor het toegangs token is aangevraagd, dat overeenkomt met de `resource` query teken reeks parameter van de aanvraag. |
| `token_type` | Het type token, een ' Bearer ' toegangs token, wat betekent dat de bron toegang kan verlenen aan de Bearer van dit token. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Problemen met de extensie van de virtuele machine oplossen 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>De extensie van de virtuele machine opnieuw starten na een fout

In Windows en bepaalde versies van Linux, als de extensie stopt, kan de volgende cmdlet worden gebruikt om deze hand matig opnieuw te starten:

```azurepowershell-interactive
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Waar: 
- De naam en het type van de extensie voor Windows is: `ManagedIdentityExtensionForWindows`
- De naam en het type van de extensie voor Linux is: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Automatiserings script mislukt bij het proberen van schema export voor beheerde identiteiten voor Azure-bronnen uitbreiding

Wanneer beheerde identiteiten voor Azure-resources zijn ingeschakeld op een virtuele machine, wordt de volgende fout weer gegeven wanneer u probeert de functie Automation script te gebruiken voor de virtuele machine of de bijbehorende resource groep:

![Export fout van beheerde identiteiten voor het automatiserings script van Azure-resources](./media/howto-migrate-vm-extension/automation-script-export-error.png)

De beheerde identiteiten voor de extensie van de virtuele machine van Azure-resources ondersteunen momenteel niet de mogelijkheid om het schema te exporteren naar een sjabloon voor een resource groep. Als gevolg hiervan worden door de gegenereerde sjabloon geen configuratie parameters weer gegeven voor het inschakelen van beheerde identiteiten voor Azure-resources op de resource. Deze secties kunnen hand matig worden toegevoegd met behulp van de voor beelden in [beheerde identiteiten voor Azure-resources configureren op een virtuele Azure-machine met een sjabloon](qs-configure-template-windows-vm.md).

Wanneer de functie voor het exporteren van het schema beschikbaar wordt voor de extensie van de virtuele machine van Azure-resources (gepland voor afschaffing in januari 2019), wordt deze weer gegeven bij het [exporteren van resource groepen die VM-extensies bevatten](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Beperkingen van de extensie van de virtuele machine 

Er zijn enkele belang rijke beperkingen bij het gebruik van de extensie van de virtuele machine. 

 * De meest ernstige beperking is het feit dat de referenties die worden gebruikt voor het aanvragen van tokens, worden opgeslagen op de virtuele machine. Een aanvaller die een inbreuk op de virtuele machine heeft, kan de referenties exfiltreren. 
 * Bovendien wordt de extensie van de virtuele machine nog steeds niet ondersteund door verschillende Linux-distributies, met een enorme ontwikkelings kosten voor het wijzigen, bouwen en testen van de uitbrei ding voor elk van deze distributies. Op dit moment worden alleen de volgende Linux-distributies ondersteund: 
    * CoreOS Stable
    * CentOS 7.1 
    * Red Hat 7,2 
    * Ubuntu 15,04 
    * Ubuntu 16.04
 * Het implementeren van virtuele machines met beheerde identiteiten heeft invloed op de prestaties, omdat de extensie van de virtuele machine ook moet worden ingericht. 
 * Ten slotte kan de extensie van de virtuele machine alleen ondersteuning 32 bieden voor door de gebruiker toegewezen beheerde identiteiten per virtuele machine. 

## <a name="azure-instance-metadata-service"></a>Azire Instance Metadata Service

[Azure instance metadata service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) is een rest-eind punt dat informatie bevat over het uitvoeren van virtuele machine-instanties die kunnen worden gebruikt voor het beheren en configureren van uw virtuele machines. Het eind punt is beschikbaar op een bekende niet-Routeer bare IP-adres ( `169.254.169.254` ) die alleen vanuit de virtuele machine kan worden geopend.

Er zijn verschillende voor delen van het gebruik van Azure IMDS voor het aanvragen van tokens. 

1. De service is extern voor de virtuele machine. Daarom zijn de referenties die worden gebruikt door beheerde identiteiten niet meer aanwezig op de virtuele machine. In plaats daarvan worden ze gehost en beveiligd op de hostcomputer van de virtuele Azure-machine.   
2. Alle Windows-en Linux-besturings systemen die worden ondersteund op Azure IaaS, kunnen beheerde identiteiten gebruiken.
3. De implementatie is sneller en eenvoudiger, omdat de VM-extensie niet meer hoeft te worden ingericht.
4. Met het IMDS-eind punt kunnen Maxi maal 1000 door de gebruiker toegewezen beheerde identiteiten worden toegewezen aan één virtuele machine.
5. Er is geen belang rijke wijziging in de aanvragen met behulp van IMDS, in tegens telling tot die van de extensie van de virtuele machine. Daarom is het redelijk eenvoudig om de bestaande implementaties te gebruiken die momenteel gebruikmaken van de extensie van de virtuele machine.

Daarom is de Azure IMDS-service de enige manier om tokens aan te vragen, zodra de extensie van de virtuele machine is afgeschaft. 


## <a name="next-steps"></a>Volgende stappen

* [Beheerde identiteiten voor Azure-resources gebruiken op een virtuele machine van Azure om een toegangs token te verkrijgen](how-to-use-vm-token.md)
* [Azire Instance Metadata Service](../../virtual-machines/windows/instance-metadata-service.md)
