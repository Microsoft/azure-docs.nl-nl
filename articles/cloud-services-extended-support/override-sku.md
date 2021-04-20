---
title: SKU-informatie over CSCFG/CSDEF overschrijven voor Azure Cloud Services (uitgebreide ondersteuning)
description: SKU-informatie over CSCFG/CSDEF overschrijven voor Azure Cloud Services (uitgebreide ondersteuning)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/05/2021
ms.custom: ''
ms.openlocfilehash: d5dfae4b5cfee8f61e11e418a05e86017d119410
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739256"
---
# <a name="override-sku-information-over-cscfgcsdef-in-cloud-services-extended-support"></a>SKU-informatie over CSCFG/CSDEF in Cloud Services overschrijven (uitgebreide ondersteuning) 

Met deze functie kan de gebruiker de rolgrootte en het aantal exemplaren in de cloudservice bijwerken met behulp van de eigenschap **allowModelOverride** zonder dat de serviceconfiguratie- en servicedefinitiebestanden moeten worden bijgewerkt, waardoor de cloudservice omhoog/omlaag/in/uit kan schalen zonder dat er een herpakket wordt uitgevoerd en opnieuw wordt uitgevoerd.

## <a name="set-allowmodeloverride-property"></a>Eigenschap allowModelOverride instellen
De eigenschap allowModelOverride kan op de volgende manieren worden ingesteld:
* Wanneer allowModelOverride = true wordt gebruikt, worden met de API-aanroep de rolgrootte en het aantal exemplaren voor de cloudservice bijgewerkt zonder de waarden te valideren met de csdef- en cscfg-bestanden. 
> [!Note]
> De cscfg wordt bijgewerkt met het aantal rol-exemplaren, maar de csdef (binnen de cspkg) behoudt de oude waarden
* Wanneer allowModelOverride = false wordt gebruikt, zou de API-aanroep een foutmelding geven wanneer de waarden voor de rolgrootte en het aantal exemplaren niet overeenkomen met respectievelijk de csdef- en cscfg-bestanden

De standaardwaarde is ingesteld op onwaar. Als de eigenschap wordt teruggezet naar false vanuit true, worden de csdef- en cscfg-bestanden opnieuw gecontroleerd voor validatie.

Neem de onderstaande voorbeelden door om de eigenschap toe te passen in PowerShell, de sjabloon en de SDK

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon
Als u hier de eigenschap allowModelOverride = true instelt, wordt de cloudservice bijgewerkt met de roleigenschappen die zijn gedefinieerd in de sectie roleProfile
```json
"properties": {
        "packageUrl": "[parameters('packageSasUri')]",
        "configurationUrl": "[parameters('configurationSasUri')]",
        "upgradeMode": "[parameters('upgradeMode')]",
        “allowModelOverride” : true,
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
Als u de switch AllowModelOverride in de nieuwe cmdlet New-AzCloudService inschakelt, wordt de cloudservice bijgewerkt met de SKU-eigenschappen die zijn gedefinieerd in het RoleProfile
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
Als u de variabele AllowModelOverride= true instelt, wordt de cloudservice bijgewerkt met de SKU-eigenschappen die zijn gedefinieerd in het RoleProfile

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
De portal staat niet toe dat de bovenstaande eigenschap de rolgrootte en het aantal exemplaren in csdef en cscfg overschrijven. 


## <a name="next-steps"></a>Volgende stappen 
- Controleer de [implementatievoorwaarden voor](deploy-prerequisite.md) Cloud Services (uitgebreide ondersteuning).
- Bekijk [veelgestelde vragen over](faq.md) Cloud Services (uitgebreide ondersteuning).
