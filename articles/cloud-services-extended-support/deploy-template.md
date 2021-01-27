---
title: Een Azure-Cloud service implementeren (uitgebreide ondersteuning)-Sjablonen
description: Een Azure-Cloud service (uitgebreide ondersteuning) implementeren met behulp van ARM-sjablonen
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: f86b2a50040704aac2827c463a362a04f78ba34f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881819"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>Een Cloud service (uitgebreide ondersteuning) implementeren met ARM-sjablonen

In deze zelf studie wordt uitgelegd hoe u een implementatie van een Cloud service (uitgebreide ondersteuning) maakt met [arm-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview). 

> [!IMPORTANT]
> Cloud Services (uitgebreide ondersteuning) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="before-you-begin"></a>Voordat u begint
1. Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning) en maak de bijbehorende resources. 

2. Maak een nieuwe resource groep met behulp van de [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) of [Power shell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell). Deze stap is optioneel als u een bestaande resource groep gebruikt. 
 
3. Maak een nieuw opslag account met behulp van de [Azure Portal](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) of [Power shell](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-powershell). Deze stap is optioneel als u een bestaand opslag account gebruikt. 

4. Upload uw service definition (csdef) en service configuratie bestanden (. cscfg) naar het opslag account met behulp van de [Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#upload-a-block-blob), [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs-upload?toc=/azure/storage/blobs/toc.json) of [Power shell](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-powershell#upload-blobs-to-the-container). Verkrijg de SAS-Uri's van beide bestanden die u later in deze zelf studie moet toevoegen aan de ARM-sjabloon. 

5. Beschrijving Maak een sleutel kluis en upload de certificaten. 
    -  Certificaten kunnen worden gekoppeld aan Cloud Services om beveiligde communicatie van en naar de service mogelijk te maken. Om certificaten te kunnen gebruiken, moeten hun vinger afdrukken worden opgegeven in het service configuratie bestand (. cscfg) en worden geüpload naar een sleutel kluis. Een Key Vault kan worden gemaakt via de [Azure Portal](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) of [Power shell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell). 
    - De gekoppelde Key Vault moeten zich in dezelfde regio en hetzelfde abonnement bevinden als de Cloud service.   
    - Voor de bijbehorende Key Vault voor moeten de juiste machtigingen zijn ingeschakeld, zodat Cloud Services (uitgebreide ondersteunings resource) het certificaat kan ophalen van Key Vault. Zie [certificaten en Key Vault](certificates-and-key-vault.md) voor meer informatie.
    - Er moet worden verwezen naar de sleutel kluis in de sectie OsProfile van de ARM-sjabloon die in de onderstaande stappen wordt weer gegeven.

## <a name="deploy-a-cloud-service-extended-support"></a>Een Cloud service implementeren (uitgebreide ondersteuning) 
1. Virtueel netwerk maken. De naam van het virtuele netwerk moet overeenkomen met de verwijzingen in het service configuratie bestand (. cscfg). Als u een bestaand virtueel netwerk gebruikt, laat u deze sectie uit de ARM-sjabloon weg.

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     Als u een nieuw virtueel netwerk maakt, voegt u het volgende toe aan de `dependsOn` sectie om ervoor te zorgen dat het platform het virtuele netwerk maakt voordat de Cloud service wordt gemaakt. 

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. Maak een openbaar IP-adres en (optioneel) Stel de DNS-label eigenschap van het open bare IP-adres in. Als u een statisch IP-adres gebruikt, moet u ernaar verwijzen als een Gereserveerd IP in het bestand met de service configuratie (. cscfg). Als u een bestaand IP-adres gebruikt, slaat u deze stap over en voegt u de IP-adres gegevens rechtstreeks toe aan de load balancer configuratie-instellingen van uw ARM-sjabloon.
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     Als u een nieuw IP-adres maakt, voegt u het volgende toe aan de `dependsOn` sectie om ervoor te zorgen dat het platform het IP-adres maakt voordat de Cloud service wordt gemaakt. 
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. Maak een object voor een netwerk profiel en koppel het open bare IP-adres aan de front-end van de load balancer. Er wordt automatisch een Load Balancer gemaakt door het platform.  

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4. Voeg uw sleutel kluis referentie toe in het  `OsProfile`   gedeelte van de arm-sjabloon. Key Vault wordt gebruikt om certificaten op te slaan die zijn gekoppeld aan Cloud Services (uitgebreide ondersteuning). Voeg de certificaten toe aan Key Vault en verwijs vervolgens naar de certificaat vingerafdrukken in het service configuratie bestand (. cscfg). U moet ook Key Vault inschakelen voor de juiste machtigingen, zodat Cloud Services (uitgebreide ondersteunings resource) het certificaat kan ophalen dat is opgeslagen als geheimen van Key Vault. Zie [certificaten gebruiken met Cloud Services (uitgebreide ondersteuning)](certificates-and-key-vault.md)voor meer informatie.
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault is de ARM-Resource-ID voor uw Key Vault. U kunt deze informatie vinden door de resource-ID te zoeken in de sectie eigenschappen van uw Key Vault. 
    > - u kunt certificateUrl vinden door te navigeren naar het certificaat in de sleutel kluis die als **geheime id** is gemarkeerd.  
   >  - certificateUrl moet de notatie https://{endpoin}/geheimen/{geheimnaam}/{Secret-id} hebben.

5. Een rollen profiel maken. Zorg ervoor dat het aantal rollen, rolnaams, het aantal instanties in elke rol en groottes hetzelfde zijn in de sectie Service configuratie (. cscfg), service definitie (. csdef) en roltype in de ARM-sjabloon. 
    
    ```json
    "roleProfile": { 
          "roles": { 
          "value": [ 
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
        }
    }   
    ```

6. Beschrijving Maak een extensie profiel om uitbrei dingen toe te voegen aan uw Cloud service. In dit voor beeld voegen we de extern bureau blad-en Windows Azure Diagnostics-extensie toe. 
    
    ```json
        "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
              ]
            }
          }
        ]
      }

  
    ```    

7. Bekijk de volledige sjabloon. 

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
         },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
         }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2020-10-01-preview",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
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
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
        "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
      }
    }
    ```
 
8. De sjabloon implementeren en de implementatie van de Cloud service (uitgebreide ondersteuning) maken. 

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName “ContosOrg -TemplateFile "file path to your template file”  
    ```
 
## <a name="next-steps"></a>Volgende stappen 
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).
- Ga naar de [opslag plaats voor beelden van Cloud Services (uitgebreide ondersteuning)](https://github.com/Azure-Samples/cloud-services-extended-support)