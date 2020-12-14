---
title: IP-verbindingsfilters van Azure IoT DPS | Microsoft Docs
description: IP-filtering gebruiken om verbindingen van specifieke IP-adressen naar uw Azure IoT DPS-exemplaar te blokkeren. U kunt verbindingen van afzonderlijke IP-adressen of reeksen IP-adressen blokkeren.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 6ff5731e7d346c954253ec4186357595461b7678
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400250"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>IP-verbindingsfilters van Azure IoT DPS gebruiken

Beveiliging is een belangrijk aspect van elke IoT-oplossing. Soms moet u expliciet de IP-adressen opgeven waarvan apparaten verbinding kunnen maken als onderdeel van uw beveiligingsconfiguratie. Met de functie *IP-filter* voor een Azure IoT Hub Device Provisioning Service (DPS) kunt u regels configureren voor het afwijzen of accepteren van verkeer van specifieke IPv4-adressen.

## <a name="when-to-use"></a>Wanneer gebruikt u dit?

Er zijn twee specifieke situaties waarin het nuttig is om verbindingen met een DPS-eindpunt van bepaalde IP-adressen te blokkeren:

* Uw DPS mag alleen verkeer ontvangen van een opgegeven bereik van IP-adressen en moet alle andere gegevens weigeren. U gebruikt bijvoorbeeld uw DPS met [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) om privéverbindingen te maken tussen een DPS en uw apparaten.

* U moet het verkeer afwijzen van IP-adressen die als verdacht zijn geïdentificeerd door de DPS-beheerder.

## <a name="how-filter-rules-are-applied"></a>Hoe filterregels worden toegepast

De IP-filterregels worden toegepast op het niveau van het DPS-exemplaar. De IP-filterregels gelden daarom voor alle verbindingen van apparaten en back-endapps met behulp van elk ondersteund protocol.

Elke verbindingspoging van een IP-adres dat overeenkomt met een afwijzings-IP-regel in uw DPS-exemplaar ontvangt een niet-geautoriseerde 401-statuscode en een beschrijving. De IP-regel wordt niet vermeld in het antwoordbericht.

## <a name="default-setting"></a>Standaardinstelling

Het raster van het **IP-filter** is standaard leeg in de portal voor DPS. Deze standaardinstelling betekent dat uw DPS verbindingen van elk IP-adres accepteert. Deze standaardinstelling komt overeen met een regel die het IP-adresbereik 0.0.0.0/0 accepteert.

![Standaard IP-filterinstellingen voor IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Een IP-filterregel toevoegen of bewerken

Als u een IP-filterregel wilt toevoegen, selecteert u **+ IP-filterregel toevoegen**.

![Een IP-filterregel toevoegen aan een IoT DPS](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Vul de velden in nadat u **IP-filterregel toevoegen** hebt geselecteerd.

![Nadat u IP-filterregel toevoegen hebt geselecteerd](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Geef een **naam** op voor de IP-filterregel. Dit moet een unieke, niet-hoofdlettergevoelige tekenreeks van maximaal 128 tekens zijn. Alleen de ASCII 7-bits alfanumerieke tekens plus `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` worden geaccepteerd.

* Geef één IPv4-adres op of een blok met IP-adressen in CIDR-notatie. Bijvoorbeeld in CIDR-notatie staat 192.168.100.0/22 voor de 1024 IPv4-adressen van 192.168.100.0 tot en met 192.168.103.255.

* Selecteer **Toestaan** of **Blokkeren** als de **actie** voor de IP-filterregel.

Nadat u de velden hebt ingevuld, selecteert u **Opslaan** om de regel op te slaan. U ziet een waarschuwing dat de update wordt uitgevoerd.

![Melding over het opslaan van een IP-filterregel](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

De optie **toevoegen** is uitgeschakeld wanneer u het maximum van 100 IP-filter regels bereikt.

Als u een bestaande regel wilt bewerken, selecteert u de gegevens die u wilt wijzigen, brengt u de wijziging aan en selecteert u **Opslaan** om uw bewerking op te slaan.

> [!NOTE]
> Het afwijzen van IP-adressen kan verhinderen dat andere Azure-services communiceren met het DPS-exemplaar.

## <a name="delete-an-ip-filter-rule"></a>Een IP-filterregel verwijderen

Als u een IP-filterregel wilt verwijderen, selecteert u het prullenbakpictogram op die rij en selecteert u **Opslaan**. De regel wordt verwijderd en de wijziging wordt opgeslagen.

![Een IP-filterregel van een IoT DPS verwijderen](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>IP-filterregels in code bijwerken

U kunt het DPS-IP-filter ophalen en wijzigen met behulp van het REST-eindpunt van de Azure-resourceprovider. Zie `properties.ipFilterRules` in de [createorupdate-methode](/rest/api/iot-dps/iotdpsresource/createorupdate).

Het bijwerken van de IP-filterregels van DPS wordt momenteel niet ondersteund met Azure CLI of Azure PowerShell, maar kan worden uitgevoerd met Azure Resource Manager-sjablonen. Zie [Azure Resource Manager-sjablonen](../azure-resource-manager/templates/overview.md) voor hulp bij het gebruik van Resource Manager-sjablonen. De volgende sjabloonvoorbeelden laten zien hoe u DPS-IP-filterregels kunt maken, bewerken en verwijderen.

### <a name="add-an-ip-filter-rule"></a>Een IP-filterregel toevoegen

In het volgende sjabloonvoorbeeld wordt een nieuwe IP-filterregel gemaakt met de naam 'AllowAll', waarmee al het verkeer wordt geaccepteerd.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Werk de kenmerken van de IP-filterregel van de sjabloon bij op basis van uw vereisten.

| Kenmerk                | Beschrijving |
| ------------------------ | ----------- |
| **FilterName**           | Geef een naam op voor de IP-filterregel. Dit moet een unieke, niet-hoofdlettergevoelige tekenreeks van maximaal 128 tekens zijn. Alleen de ASCII 7-bits alfanumerieke tekens plus  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`   worden geaccepteerd. |
| **Actie**               | Geaccepteerde waarden zijn **Accepteren** of  **Afwijzen** als actie voor de IP-filterregel. |
| **ipMask**               | Geef één IPv4-adres op of een blok met IP-adressen in CIDR-notatie. Bijvoorbeeld in CIDR-notatie staat 192.168.100.0/22 voor de 1024 IPv4-adressen van 192.168.100.0 tot en met 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Een IP-filterregel bewerken

In het volgende sjabloonvoorbeeld wordt de IP-filterregel met de naam 'AllowAll', die eerder werd weergegeven, bijgewerkt om al het verkeer af te wijzen.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Een IP-filterregel verwijderen

In het volgende sjabloonvoorbeeld worden alle IP-filterregels voor het DPS-exemplaar verwijderd.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Evaluatie van IP-filterregel

IP-filterregels worden op volgorde toegepast en de eerste regel die overeenkomt met het IP-adres bepaalt de actie accepteren of afwijzen.

Als u bijvoorbeeld adressen in het bereik 192.168.100.0/22 wilt accepteren en de rest wilt afwijzen, moet de eerste regel in het raster het adresbereik 192.168.100.0/22 accepteren. De volgende regel moet alle adressen afwijzen met behulp van het bereik 0.0.0.0/0.

U kunt de volgorde van de IP-filterregels in het raster wijzigen door te klikken op de drie verticale puntjes aan het begin van een rij en met slepen en neerzetten.

Als u de nieuwe IP-filterregel wilt opslaan, klikt u op **Opslaan**.

![Wijzig de volgorde van de IP-filterregels van uw DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het beheren van DPS:

* [Inzicht in IP-adressen van IoT DPS](iot-dps-understand-ip-address.md)
* [DPS configureren met behulp van Azure CLI](how-to-manage-dps-with-cli.md)
* [Toegang tot DPS beheren](how-to-control-access.md)
