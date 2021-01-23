---
title: Een virtuele Linux-machine maken met de REST API
description: Meer informatie over het maken van een virtuele Linux-machine in azure die gebruikmaakt van Managed Disks-en SSH-verificatie met Azure REST API.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 06/05/2018
ms.author: cynthn
ms.openlocfilehash: b3eeaf5f343b6026e02d17e4d3bd90dba4b991c4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737042"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Maak een virtuele Linux-machine die gebruikmaakt van SSH-verificatie met de REST API

Een virtuele Linux-machine (VM) in azure bestaat uit verschillende bronnen, zoals schijven en netwerk interfaces en definieert para meters zoals locatie, grootte en installatie kopie van het besturings systeem en verificatie-instellingen.

U kunt een virtuele Linux-machine maken via de Azure Portal, Azure CLI 2,0, veel Azure Sdk's, Azure Resource Manager sjablonen en veel hulpprogram ma's van derden, zoals Ansible of terraform. Al deze hulpprogram ma's gebruiken uiteindelijk de REST API voor het maken van de virtuele Linux-machine.

In dit artikel wordt beschreven hoe u de REST API gebruikt om een virtuele Linux-machine met Ubuntu 18,04-LTS te maken met beheerde schijven en SSH-verificatie.

## <a name="before-you-start"></a>Voordat u begint

Voordat u de aanvraag maakt en indient, hebt u het volgende nodig:

* De `{subscription-id}` voor uw abonnement
  * Als u meerdere abonnementen hebt, raadpleegt u [werken met meerdere abonnementen](/cli/azure/manage-azure-subscriptions-azure-cli)
* Een `{resourceGroupName}` die u eerder hebt gemaakt
* Een [virtuele netwerk interface](../../virtual-network/virtual-network-network-interface.md) in dezelfde resource groep
* Een SSH-sleutel paar (u kunt [een nieuw abonnement genereren](mac-create-ssh-keys.md) als u er geen hebt)

## <a name="request-basics"></a>Basis informatie over aanvragen

Als u een virtuele machine wilt maken of bijwerken, gebruikt u de volgende *put* -bewerking:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Naast de `{subscription-id}` `{resourceGroupName}` para meters en moet u het `{vmName}` ( `api-version` optioneel) opgeven, maar dit artikel is getest met. `api-version=2017-12-01`

De volgende headers zijn vereist:

| Aanvraagheader   | Beschrijving |
|------------------|-----------------|
| *Content-Type:*  | Vereist. Ingesteld op `application/json`. |
| *Authorization:* | Vereist. Ingesteld op een geldig `Bearer` [toegangstoken](/rest/api/azure/#authorization-code-grant-interactive-clients). |

Voor algemene informatie over het werken met REST API-aanvragen raadpleegt u [onderdelen van een rest API aanvraag/antwoord](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>De aanvraag tekst maken

De volgende algemene definities worden gebruikt voor het bouwen van een aanvraag tekst:

| Name                       | Vereist | Type                                                                                | Beschrijving  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True     | tekenreeks                                                                              | Resource locatie. |
| naam                       |          | tekenreeks                                                                              | Naam voor de virtuele machine. |
| Eigenschappen. hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Hiermee geeft u de hardware-instellingen voor de virtuele machine op. |
| Eigenschappen. storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Hiermee geeft u de opslag instellingen voor de schijven van de virtuele machine. |
| Eigenschappen. osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Hiermee geeft u de instellingen van het besturings systeem voor de virtuele machine. |
| Eigenschappen. networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Hiermee geeft u de netwerk interfaces van de virtuele machine. |

Hieronder ziet u een voor beeld van een aanvraag tekst. Zorg ervoor dat u de naam van de virtuele machine opgeeft in de `{computerName}` `{name}` para meters en de naam van de netwerk interface die u hebt gemaakt onder `networkInterfaces` , uw gebruikers naam in `adminUsername` en en `path` het *open bare* gedeelte van uw SSH-sleutel paar (bijvoorbeeld `~/.ssh/id_rsa.pub` ) in `keyData` . Andere para meters die u mogelijk wilt wijzigen `location` , zijn en `vmSize` .  

```json
{
  "location": "eastus",
  "name": "{vmName}",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "{vmName}",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Zie voor een volledige lijst van de beschik bare definities in de hoofd tekst van de aanvraag de definities voor het [maken of bijwerken van aanvraag teksten voor virtuele machines](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>De aanvraag verzenden

U kunt de client van uw voor keur gebruiken voor het verzenden van deze HTTP-aanvraag. U kunt ook een [hulp programma in de browser](/rest/api/compute/virtualmachines/createorupdate) gebruiken door te klikken op de knop **Probeer het opnieuw** .

### <a name="responses"></a>Antwoorden

Er zijn twee geslaagde reacties voor de bewerking om een virtuele machine te maken of bij te werken:

| Naam        | Type                                                                              | Beschrijving |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 gemaakt | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Gemaakt     |

Een versmalded *201* -antwoord dat is gemaakt op basis van de aanvraag tekst van het vorige voor beeld waarmee een VM wordt gemaakt, ziet u dat er een *vmId* is toegewezen en dat de *provisioningState* wordt *gemaakt*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Zie voor meer informatie over REST API reacties [het antwoord bericht verwerken](/rest/api/azure/#process-the-response-message).

## <a name="next-steps"></a>Volgende stappen

Zie het volgende voor meer informatie over de Azure REST Api's of andere beheer Programma's, zoals Azure CLI of Azure PowerShell:

- [REST API Azure Compute-provider](/rest/api/compute/)
- [Aan de slag gaan met Azure REST API](/rest/api/azure/)
- [Azure-CLI](/cli/azure/)
- [Azure PowerShell-module](/powershell/azure/)
