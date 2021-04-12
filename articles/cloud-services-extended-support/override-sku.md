---
title: SKU-gegevens via CSCFG/CSDEF voor Azure Cloud Services overschrijven (uitgebreide ondersteuning)
description: SKU-gegevens via CSCFG/CSDEF voor Azure Cloud Services overschrijven (uitgebreide ondersteuning)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: 17e47b562c52ffce631a01cf03004d77053ea647
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387317"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>SKU-gegevens via CSCFG/CSDEF in Cloud Services overschrijven (uitgebreide ondersteuning) 

Met deze functie kan de gebruiker de grootte van de rol en het aantal instanties in de Cloud service bijwerken met behulp van de eigenschap **allowModelOverride** zonder dat de service configuratie en de service definitie bestanden hoeven te worden bijgewerkt, waardoor de Cloud service omhoog/omlaag/omlaag kan worden geschaald zonder dat er opnieuw een pakket wordt gemaakt en opnieuw wordt geïmplementeerd.

## <a name="set-allowmodeloverride-property"></a>Eigenschap allowModelOverride instellen
De eigenschap allowModelOverride kan op de volgende manieren worden ingesteld:
* Als allowModelOverride = True is, wordt met de API-aanroep de rolgrootte en het aantal exemplaren voor de Cloud service bijgewerkt zonder dat de waarden worden gevalideerd met de csdef-en cscfg-bestanden. 
> [!Note]
> Het cscfg-nummer wordt bijgewerkt op basis van het aantal rolinstantie, maar het csdef (binnen de cspkg) behoudt de oude waarden
* Als allowModelOverride = False, wordt er door de API-aanroep een fout gegenereerd wanneer de waarden voor de rolgrootte en het aantal instanties niet overeenkomen met de waarde van de csdef-en cscfg-bestanden

De standaard waarde is ingesteld op false. Als de eigenschap opnieuw wordt ingesteld op ONWAAR, worden de csdef-en cscfg-bestanden opnieuw gecontroleerd op validatie.

Bekijk de onderstaande voor beelden om de eigenschap in Power shell, sjabloon en SDK toe te passen

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon
Als u de eigenschap ' allowModelOverride ' = True ' instelt, wordt de Cloud service bijgewerkt met de functie-eigenschappen die zijn gedefinieerd in de sectie roleProfile
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “**allowModelOverride**” : true,
        "roleProfile": {
          "roles": [
            {
              "name": "WebRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            },
            {
              "name": "WorkerRole1",
              "sku": {
                "name": "Standard_D1_v2",
                "capacity": "1"
              }
            }
          ]
        },

```
### <a name="powershell"></a>PowerShell
Als u de schakel optie ' AllowModelOverride ' instelt op de nieuwe New-AzCloudService cmdlet, wordt de Cloud service bijgewerkt met de SKU-eigenschappen die zijn gedefinieerd in de RoleProfile
```powershell
New-AzCloudService ` 
-Name “ContosoCS” ` 
-ResourceGroupName “ContosOrg” ` 
-Location “East US” `
-AllowModelOverride  ` 
-PackageUrl $cspkgUrl ` 
-ConfigurationUrl $cscfgUrl ` 
-UpgradeMode 'Auto' ` 
-RoleProfile $roleProfile ` 
-NetworkProfile $networkProfile  ` 
-ExtensionProfile $extensionProfile ` 
-OSProfile $osProfile `
-Tag $tag
```
### <a name="sdk"></a>SDK
Als de variabele AllowModelOverride = True wordt ingesteld, wordt de Cloud service bijgewerkt met de SKU-eigenschappen die zijn gedefinieerd in de RoleProfile

```csharp
CloudService cloudService = new CloudService
    {
        Properties = new CloudServiceProperties
            {
                RoleProfile = cloudServiceRoleProfile
                Configuration = < Add Cscfg xml content here>
                PackageUrl = <Add cspkg SAS url here>,
                ExtensionProfile = cloudServiceExtensionProfile,
                OsProfile= cloudServiceOsProfile,
                NetworkProfile = cloudServiceNetworkProfile,
                UpgradeMode = 'Auto',
                AllowModelOverride = true
            },
                Location = m_location
            };
CloudService createOrUpdateResponse = m_CrpClient.CloudServices.CreateOrUpdate(“ContosOrg”, “ContosoCS”, cloudService);
```
### <a name="azure-portal"></a>Azure Portal
De bovenstaande eigenschap kan niet worden overschreven door de portal om de grootte van de rol en het aantal instanties in de csdef en cscfg te overschrijven. 


## <a name="next-steps"></a>Volgende stappen 
- Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning).
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
