---
title: Een pool met opgegeven open bare IP-adressen maken
description: Meer informatie over het maken van een batch-pool die gebruikmaakt van uw eigen open bare IP-adressen.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: e822311718847e173763847d503335f71457308b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91849325"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Een Azure Batch groep met opgegeven open bare IP-adressen maken

Wanneer u een Azure Batch groep maakt, kunt u [de groep inrichten in een subnet van een virtueel Azure-netwerk](batch-virtual-network.md) (VNet) dat u opgeeft. Virtuele machines in de batch-pool zijn toegankelijk via open bare IP-adressen die door batch worden gemaakt. Deze open bare IP-adressen kunnen worden gewijzigd gedurende de levens duur van de groep. Dit betekent dat uw netwerk instellingen kunnen worden verouderd als de IP-adressen niet worden vernieuwd.

U kunt een lijst met statische open bare IP-adressen maken voor gebruik met de virtuele machines in uw pool. Hiermee kunt u de lijst met open bare IP-adressen beheren en ervoor zorgen dat ze onverwacht niet worden gewijzigd. Dit kan vooral nuttig zijn als u werkt met een externe service, zoals een Data Base, waardoor de toegang tot bepaalde IP-adressen wordt beperkt.

Voor informatie over het maken van Pools zonder open bare IP-adressen, Lees [een Azure batch groep maken zonder open bare IP-adressen](./batch-pool-no-public-ip-address.md).

## <a name="prerequisites"></a>Vereisten

- **Verificatie**. Als u een openbaar IP-adres wilt gebruiken, moet de batch-client-API gebruikmaken van [Azure Active Directory (AD)-verificatie](batch-aad-auth.md).

- **Een Azure-VNet**. U moet een [virtueel netwerk](batch-virtual-network.md) gebruiken van hetzelfde Azure-abonnement waarin u uw pool en uw IP-adressen maakt. Alleen op Azure Resource Manager gebaseerde VNets kunnen worden gebruikt. Zorg ervoor dat het VNet voldoet aan alle [algemene vereisten](batch-virtual-network.md#vnet-requirements).

- **Ten minste één openbaar IP-adres van Azure**. Als u een of meer open bare IP-adressen wilt maken, kunt u de [Azure Portal](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address), de [Azure Command-Line interface (CLI)](/cli/azure/network/public-ip#az-network-public-ip-create)of [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress)gebruiken. Zorg ervoor dat u voldoet aan de vereisten die hieronder worden weer gegeven.

> [!NOTE]
> Batch wijst automatisch extra netwerk bronnen toe aan de resource groep met de open bare IP-adressen. Voor elke 100 toegewezen knoop punten wijst batch in het algemeen één netwerk beveiligings groep (NSG) en één load balancer toe. Deze resources worden beperkt door de resource quota van het abonnement. Wanneer u grotere groepen gebruikt, moet u mogelijk [een quotum toename aanvragen](batch-quota-limit.md#increase-a-quota) voor een of meer van deze resources.

## <a name="public-ip-address-requirements"></a>Vereisten voor openbaar IP-adres

Houd bij het maken van uw open bare IP-adressen de volgende vereisten in acht:

- De open bare IP-adressen moeten zich in hetzelfde abonnement en dezelfde regio bevinden als het batch-account dat u gebruikt om uw groep te maken.
- De **toewijzing van het IP-adres** moet worden ingesteld op **statisch**.
- **SKU** moet worden ingesteld op **Standard**.
- U moet een DNS-naam opgeven.
- De open bare IP-adressen moeten alleen worden gebruikt voor de configuratie groepen van de virtuele machine. Andere resources mogen deze IP-adressen niet gebruiken of de pool kan toewijzings fouten ondervinden.
- Geen beveiligings beleid of resource vergrendeling moet de toegang van een gebruiker tot het open bare IP-adres beperken.
- Het aantal open bare IP-adressen dat is opgegeven voor de groep, moet groot genoeg zijn voor het aantal Vm's dat is gericht op de groep. Dit moet ten minste de som zijn van de eigenschappen **targetDedicatedNodes**   en **targetLowPriorityNodes**   van de groep. Als er niet voldoende IP-adressen zijn, wijst de groep gedeeltelijk de reken knooppunten toe en wordt er een fout bij het wijzigen van de grootte weer gegeven. Op dit moment gebruikt batch één openbaar IP-adres voor elke virtuele machine van 100.
- Altijd een extra buffer van open bare IP-adressen. We raden u aan ten minste één extra openbaar IP-adres toe te voegen of ongeveer 10% van de totale open bare IP-adressen die u toevoegt aan een groep, afhankelijk van wat groter is. Deze extra buffer helpt batch met de interne Optima Lise ring bij het schalen, en biedt de mogelijkheid om sneller te schalen nadat een uitbrei ding is mislukt of omlaag kan worden geschaald.
- Zodra de groep is gemaakt, kunt u de lijst met open bare IP-adressen die worden gebruikt door de groep, niet meer toevoegen of wijzigen. Als u de lijst moet wijzigen, moet u de groep verwijderen en vervolgens opnieuw maken.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Een batch-pool met open bare IP-adressen maken

In het onderstaande voor beeld ziet u hoe u de [Azure batch-Service rest API](/rest/api/batchservice/pool/add) kunt gebruiken om een groep te maken die gebruikmaakt van open bare IP-adressen.

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
- Meer informatie over het [maken van een pool in een subnet van een virtueel Azure-netwerk](batch-virtual-network.md).
- Meer informatie over [het maken van een Azure batch groep zonder open bare IP-adressen](./batch-pool-no-public-ip-address.md).

