---
title: Een pool met opgegeven openbare IP-adressen maken
description: Meer informatie over het maken van een Batch-pool die gebruikmaakt van uw eigen openbare IP-adressen.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: 82a37f96a91bdad37c1a7828ef0cf71b3581ca82
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768391"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Een Azure Batch groep met opgegeven openbare IP-adressen maken

Wanneer u een Azure Batch groep maakt, kunt u de [pool inrichten in](batch-virtual-network.md) een subnet van een virtueel Azure-netwerk (VNet) dat u opgeeft. Virtuele machines in de Batch-pool zijn toegankelijk via openbare IP-adressen die zijn gemaakt door Batch. Deze openbare IP-adressen kunnen gedurende de levensduur van de groep veranderen, wat betekent dat uw netwerkinstellingen verouderd kunnen raken als de IP-adressen niet worden vernieuwd.

U kunt een lijst met statische openbare IP-adressen maken voor gebruik met de virtuele machines in uw pool. Hiermee kunt u de lijst met openbare IP-adressen bepalen en ervoor zorgen dat deze niet onverwacht worden gewijzigd. Dit kan vooral nuttig zijn als u werkt met een externe service, zoals een database, die de toegang tot bepaalde IP-adressen beperkt.

Lees Create an Azure Batch pool without public IP addresses (Een groep Azure Batch zonder openbare IP-adressen) voor meer informatie over het maken van pools [zonder openbare IP-adressen.](./batch-pool-no-public-ip-address.md)

## <a name="prerequisites"></a>Vereisten

- **Verificatie**. Als u een openbaar IP-adres wilt gebruiken, moet de Batch-client-API gebruikmaken [van Azure Active Directory (AD)-verificatie.](batch-aad-auth.md)

- **Een Azure VNet.** U moet een virtueel [netwerk gebruiken uit](batch-virtual-network.md) hetzelfde Azure-abonnement waarin u uw pool en uw IP-adressen maakt. Alleen Azure Resource Manager VNets op basis van kunnen worden gebruikt. Zorg ervoor dat het VNet voldoet aan alle [algemene vereisten.](batch-virtual-network.md#vnet-requirements)

- **Ten minste één openbaar Ip-adres van Azure.** Als u een of meer openbare IP-adressen wilt maken, kunt u de [Azure Portal](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), de [Azure Command-Line Interface (CLI)](/cli/azure/network/public-ip#az_network_public_ip_create)of [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress). Zorg ervoor dat u de onderstaande vereisten volgt.

> [!NOTE]
> Batch wijst automatisch extra netwerkresources toe in de resourcegroep die de openbare IP-adressen bevat. Voor elke 100 toegewezen knooppunten wijst Batch in het algemeen één netwerkbeveiligingsgroep (NSG) en één load balancer. Deze resources worden beperkt door de resourcequota van het abonnement. Wanneer u grotere pools gebruikt, moet u mogelijk [een quotumverhoging aanvragen](batch-quota-limit.md#increase-a-quota) voor een of meer van deze resources.

## <a name="public-ip-address-requirements"></a>Vereisten voor openbare IP-adressen

Houd rekening met de volgende vereisten bij het maken van uw openbare IP-adressen:

- De openbare IP-adressen moeten zich in hetzelfde abonnement en dezelfde regio als het Batch-account dat u gebruikt om uw pool te maken.
- De **IP-adrestoewijzing** moet worden ingesteld op **Statisch.**
- **SKU** moet worden ingesteld op **Standard**.
- Er moet een DNS-naam worden opgegeven.
- De openbare IP-adressen moeten alleen worden gebruikt voor de configuratiegroepen van de virtuele machine. Geen andere resources mogen deze IP-adressen gebruiken of de pool kan toewijzingsfouten ervaren.
- Beveiligingsbeleid of resourcevergrendelingen mogen de toegang van een gebruiker tot het openbare IP-adres niet beperken.
- Het aantal openbare IP-adressen dat voor de groep is opgegeven, moet groot genoeg zijn voor het aantal VM's dat voor de groep is bedoeld. Dit moet ten minste de som zijn van de **eigenschappen targetDedicatedNodes** en    **targetLowPriorityNodes**   van de pool. Als er onvoldoende IP-adressen zijn, wijst de pool de rekenknooppunten gedeeltelijk toe en treedt er een fout op in de ize. Momenteel gebruikt Batch één openbaar IP-adres voor elke 100 VM's.
- Altijd een extra buffer van openbare IP-adressen hebben. We raden u aan ten minste één extra openbaar IP-adres toe te voegen, of ongeveer 10% van het totale aantal openbare IP-adressen dat u aan een groep toevoegt, wat groter is. Deze extra buffer helpt Batch bij de interne optimalisatie bij het omlaag schalen en het sneller omhoog schalen na een mislukte schaal omhoog of omlaag schalen.
- Zodra de pool is gemaakt, kunt u de lijst met openbare IP-adressen die door de groep worden gebruikt, niet meer toevoegen of wijzigen. Als u de lijst wilt wijzigen, moet u de pool verwijderen en deze vervolgens opnieuw maken.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Een Batch-pool met openbare IP-adressen maken

In het onderstaande voorbeeld ziet u hoe u de [Azure Batch REST API voor services](/rest/api/batchservice/pool/add) om een groep te maken die gebruikmaakt van openbare IP-adressen.

### <a name="batch-service-rest-api"></a>REST API voor Batch-service

REST API-URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Aanvraagtekst

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [Werkstroom van de batch-service en primaire resources](batch-service-workflow-features.md) als pools, knooppunten, jobs en taken.
- Meer informatie over [het maken van een pool in een subnet van een virtueel Azure-netwerk.](batch-virtual-network.md)
- Meer informatie over [het maken Azure Batch een groep zonder openbare IP-adressen.](./batch-pool-no-public-ip-address.md)
