---
title: Azure Firewall actieve FTP-ondersteuning
description: Actieve FTP is standaard uitgeschakeld op Azure Firewall. U kunt deze inschakelen met behulp van PowerShell, CLI en ARM-sjabloon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: victorh
ms.openlocfilehash: 18b3680e47fe808413998144259e033a4cbcaa27
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864465"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure Firewall actieve FTP-ondersteuning

Met Actieve FTP initieert de FTP-server de gegevensverbinding met de aangewezen FTP-clientgegevenspoort. Firewalls op het netwerk aan de clientzijde blokkeren normaal gesproken een externe verbindingsaanvraag naar een interne clientpoort. Zie Actieve FTP versus [passieve FTP, een definitieve uitleg voor meer informatie.](https://slacksite.com/other/ftp.html)

Actieve FTP-ondersteuning is standaard uitgeschakeld op Azure Firewall ftp-bounce-aanvallen te beveiligen met behulp van de `PORT` FTP-opdracht. U kunt actieve FTP echter inschakelen wanneer u implementeert met behulp van Azure PowerShell, de Azure CLI of een Azure ARM-sjabloon.

Als u FTP in de actieve modus wilt ondersteunen, moeten de volgende TCP-poorten worden geopend:

- Poort 21 van de FTP-server vanaf elke locatie (client initieert verbinding)
- Poort 21 van ftp-server naar poorten > 1023 (server reageert op de controlepoort van de client)
- Poort 20 van FTP-server naar poorten > 1023 op clients (server initieert gegevensverbinding met de gegevenspoort van de client)
- Poort 20 van de FTP-server vanaf poorten > 1023 op clients (client verzendt ACL's naar de gegevenspoort van de server)

## <a name="azure-powershell"></a>Azure PowerShell

Als u wilt implementeren Azure PowerShell, gebruikt u de `AllowActiveFTP` parameter . Zie Create [a Firewall with Allow Active FTP (Een firewall maken met Actieve FTP toestaan) voor meer informatie.](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-)

## <a name="azure-cli"></a>Azure CLI

Als u wilt implementeren met behulp van de Azure CLI, gebruikt u de `--allow-active-ftp` parameter . Zie az [network firewall create voor meer informatie.](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters) 

## <a name="azure-resource-manager-arm-template"></a>Azure Resource Manager (ARM)-sjabloon

Als u wilt implementeren met behulp van een ARM-sjabloon, gebruikt u het `AdditionalProperties` veld:

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
Zie [Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie Deploy and configure Azure Firewall using Azure Firewall Azure PowerShell (Een Azure Firewall implementeren en configureren met behulp van [Azure PowerShell.](deploy-ps.md)