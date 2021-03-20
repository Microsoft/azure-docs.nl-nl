---
title: Azure Stack Edge januari 2021 impact van update | Microsoft Docs
description: In dit artikel wordt beschreven wat de impact is van IoT Edge-rollenbeheer op Azure Stack Edge-apparaten nadat u de update van januari 2021 installeert.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: f16f33e9aadcc01427602a1bd81f81cb0710e4dd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94578736"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge-device"></a>Wijzigingen in IoT Edge-rollenbeheer voor uw Azure Stack Edge-apparaat

Voor IoT Edge-rollenbeheer voor uw Azure Stack Edge-apparaat gebruikt u de nieuwste versie van de API, de SDK en Azure PowerShell die voor de release van januari 2021 staat gepland. 

In dit artikel worden de wijzigingen beschreven die u moet aanbrengen wanneer u deze nieuwste versie gebruikt.

De update van januari 2021 is alleen beschikbaar voor apparaten van Azure Stack Edge Pro - GPU, Azure Stack Edge Pro R-en Azure Stack Edge Mini R. De informatie in dit artikel is alleen van toepassing op deze apparaten.

> [!NOTE]
> U bent niet verplicht om te upgraden naar de versie van januari 2021. Als u ervoor kiest om door te gaan met de huidige versie, heeft dit geen invloed op IoT Edge-rollenbeheer. Als u echter wilt profiteren van de nieuwe functies en eventuele beveiligingsrisico's wilt beperken, raden we u aan om de nieuwere versie te installeren. 

## <a name="iot-edge-role-management-changes"></a>Wijzigingen in IoT Edge-rollenbeheer

Nadat u de optionele update van januari 2021 hebt geïnstalleerd op uw Azure Stack Edge-apparaat, moet u de nieuwste versie van de API-, SDK-en PowerShell-cmdlets voor IoT Edge-rollenbeheer gebruiken.

De volgende wijzigingen zijn alleen vereist als u de update van januari 2021 toepast:

- Als u momenteel de versie van &nbsp;2019-08-01 van de API voor rollenbeheer gebruikt, moet u te zijner tijd upgraden naar de API-versie die in januari 2021 wordt uitgebracht. 
- Als u momenteel rollenbeheer gebruikt via SDK-versie&nbsp;1.0.0 van de API voor rollenbeheer, moet u te zijner tijd upgraden naar de versie die in januari 2021 wordt uitgebracht.
- Als u rollenbeheer met Azure PowerShell-cmdlets (preview) gebruikt, zoals `Get-AzStackEdgeRole`, `New-AzStackEdgeRole`, `Set-AzStackEdgeRole` of `Remove-AzStackEdgeRole`, moet u wachten op de nieuwe cmdlets die worden uitgebracht in februari 2021.

## <a name="api-usage"></a>API-gebruik

Als u momenteel rollenbeheer van IoT Edge via de API uitvoert, moet u de nieuwe API-versie 2020-12-01 gebruiken die later wordt gepubliceerd. Als u de huidige rol-API gebruikt nadat u de aankomende versie van de apparaatsoftware installeert, moet u overstappen op de Kubernetes-rol PLAATSEN, OPHALEN of VERWIJDEREN gevolgd door API IoT-invoegtoepassing PLAATSEN.

### <a name="for-the-put-method"></a>Voor de methode PLAATSEN

#### <a name="the-current-http-request"></a>De huidige HTTP-aanvraag 

- De API-aanroepen worden gemaakt op deze URI: https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01

- De hoofdtekst van de aanvraag ziet er als volgt uit:

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="the-upcoming-http-request"></a>De aankomende HTTP-aanvraag 

- De API-aanroepen voor de Kubernetes-rol worden gemaakt op de volgende URI: 

  'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01'

    De hoofdtekst van de aanvraag ziet er als volgt uit:

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- De API-aanroepen voor de IoT Edge-invoegtoepassing worden gemaakt op de volgende URI: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

    De hoofdtekst van de aanvraag ziet er als volgt uit:

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-the-get-method"></a>Voor de methode OPHALEN

#### <a name="the-current-http-response"></a>Het huidige HTTP-antwoord

- De API-aanroepen worden gemaakt op de volgende URI: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

- De antwoordtekst ziet er als volgt uit:

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="the-upcoming-http-response"></a>Het aankomend HTTP-antwoord

- De API-aanroepen worden gemaakt op de volgende URI:  

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

- De antwoordtekst ziet er als volgt uit: 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-the-delete-method"></a>Voor de methode VERWIJDEREN

### <a name="the-current-api-calls"></a>De huidige API-aanroepen

De API-aanroepen worden gemaakt op de volgende URI:  

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

### <a name="the-upcoming-api-calls"></a>De aankomende API-aanroepen

De API-aanroepen worden gemaakt op de volgende URI:  

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

## <a name="sdk-usage"></a>SDK-gebruik

Als u de SDK gebruikt en de update van januari 2021 hebt geïnstalleerd, moet u de manier wijzigen waarop u de rollen van IoT Edge instelt, zoals wordt weergegeven in het volgende voorbeeld. Vervolgens downloadt en installeert u het aankomende NuGet-pakket om over te stappen op de nieuwe SDK, zoals hier wordt weergegeven.

### <a name="the-current-sdk-sample"></a>Voorbeeld van huidige SDK

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="the-new-sdk-sample"></a>Voorbeeld van nieuwe SDK

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>Cmdlet-gebruik

Als u momenteel de cmdlet `Get-AzStackEdgeRole`, `New-AzStackEdgeRole`, `Set-AzStackEdgeRole` of `Remove-AzStackEdgeRole` gebruikt, moet u wachten op de nieuwe versie die voor februari 2021 staat gepland.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Ik gebruik Azure Stack Edge Pro - FPGA. Heeft de update van januari 2021 invloed op het FPGA-model?**

Nee. De update van januari 2021 is alleen van toepassing op Azure Stack Edge Pro - FPGA-, Azure Stack Edge Pro R- en Azure Stack Edge Mini R-apparaten. Azure Stack Edge Pro - FPGA wordt niet beïnvloed door deze update en vereist geen enkele wijziging in het rollenbeheer van IoT Edge.

**Als ik Azure Stack Edge Pro - GPU in januari 2021 heb bijgewerkt naar de nieuwe apparaatsoftware, heeft dit dan invloed op de bestaande services?**

Nee. Uw geconfigureerde services worden niet beïnvloed nadat u de apparaatsoftware van januari 2021 hebt geïnstalleerd.

**Wat zijn de wijzigingen op hoog niveau voor IoT Edge beheer-API, -SDK of -cmdlet?**

IoT Edge is een invoegtoepassing onder de Kubernetes-rol, wat impliceert dat u eerst moet zorgen dat Kubernetes is geconfigureerd en dat u vervolgens de IoT Edge-configuratie uitvoert.


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over het toepassen van updates](azure-stack-edge-gpu-install-update.md)

