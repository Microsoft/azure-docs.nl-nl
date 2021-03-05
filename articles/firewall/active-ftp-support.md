---
title: Ondersteuning voor Active FTP Azure Firewall
description: Actieve FTP is standaard uitgeschakeld op Azure Firewall. U kunt deze functie inschakelen met Power shell, CLI en ARM-sjabloon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/22/2021
ms.author: victorh
ms.openlocfilehash: 47bced3c3c4f01212af6919d51adc5dfc2e6e1b5
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200055"
---
# <a name="azure-firewall-active-ftp-support"></a>Ondersteuning voor Active FTP Azure Firewall

Met Active FTP initieert de FTP-server de gegevens verbinding met de toegewezen FTP-client gegevens poort. Firewalls in het netwerk van de client blok keren normaal gesp roken een externe verbindings aanvraag naar een interne client poort. Zie voor meer informatie [Active FTP versus passieve FTP, een definitieve uitleg](https://slacksite.com/other/ftp.html).

Active FTP-ondersteuning is standaard uitgeschakeld op Azure Firewall voor beveiliging tegen FTP-aanvallen met de FTP- `PORT` opdracht. U kunt Active FTP echter inschakelen wanneer u implementeert met behulp van Azure PowerShell, de Azure CLI of een Azure ARM-sjabloon.

> [!NOTE]
> Actieve FTP wordt momenteel alleen ondersteund voor firewalls die zijn geïmplementeerd in een virtueel netwerk. Virtuele WAN-ondersteuning wordt later toegevoegd.

## <a name="azure-powershell"></a>Azure PowerShell

Als u wilt implementeren met behulp van Azure PowerShell, gebruikt u de `AllowActiveFTP` para meter. Zie [een firewall maken met actieve FTP toestaan](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-)voor meer informatie.

## <a name="azure-cli"></a>Azure CLI

Als u wilt implementeren met behulp van de Azure CLI, gebruikt u de `--allow-active-ftp` para meter. Zie [AZ Network Firewall Create](/cli/azure/ext/azure-firewall/network/firewall#ext_azure_firewall_az_network_firewall_create-optional-parameters)(Engelstalig) voor meer informatie. 

## <a name="azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM)-sjabloon

Als u wilt implementeren met een ARM-sjabloon, gebruikt u het `AdditionalProperties` veld:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Zie [micro soft. Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure firewall implementeren en configureren met behulp van Azure PowerShell](deploy-ps.md)voor meer informatie over het implementeren van een Azure firewall.