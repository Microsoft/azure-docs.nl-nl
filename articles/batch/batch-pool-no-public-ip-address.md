---
title: Een Azure Batch-pool zonder openbare IP-adressen maken
description: Meer informatie over het maken van een groep zonder open bare IP-adressen
author: pkshultz
ms.topic: how-to
ms.date: 12/9/2020
ms.author: peshultz
ms.custom: references_regions
ms.openlocfilehash: 806e85fca0a509d56e248fc7779fba0f0a59a61d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007667"
---
# <a name="create-an-azure-batch-pool-without-public-ip-addresses"></a>Een Azure Batch-pool zonder openbare IP-adressen maken

Wanneer u een Azure Batch groep maakt, kunt u de configuratie groep van de virtuele machine inrichten zonder een openbaar IP-adres. In dit artikel wordt uitgelegd hoe u een batch-pool kunt instellen zonder open bare IP-adressen.

## <a name="why-use-a-pool-without-public-ip-addresses"></a>Waarom een pool zonder open bare IP-adressen gebruiken?

Standaard wordt aan alle reken knooppunten in een Azure Batch configuratie groep voor virtuele machines een openbaar IP-adres toegewezen. Dit adres wordt gebruikt door de batch-service voor het plannen van taken en voor communicatie met reken knooppunten, inclusief uitgaande toegang tot internet.

Als u de toegang tot deze knoop punten wilt beperken en de detectie van deze knoop punten van het Internet wilt verkorten, kunt u de groep inrichten zonder open bare IP-adressen.

> [!IMPORTANT]
> Ondersteuning voor Pools zonder open bare IP-adressen in Azure Batch is momenteel beschikbaar als open bare Preview voor de volgende regio's: Frankrijk-centraal, Azië-oost, VS-West-Centraal, Zuid-Centraal VS, VS-West 2, Verenigde Staten, Europa-noord, VS-Oost 2, VS-midden, Europa-west, Noord-Centraal VS, VS-West, Australië-oost, Japan-Oost, Japan-West.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- **Verificatie**. Als u een groep zonder open bare IP-adressen binnen een [virtueel netwerk](./batch-virtual-network.md)wilt gebruiken, moet de batch-client-API gebruikmaken van Azure Active Directory (AD)-verificatie. Azure Batch-ondersteuning voor Azure AD wordt beschreven in [Oplossingen van Batch-service verifiëren met Active Directory](batch-aad-auth.md). Als u de groep niet binnen een virtueel netwerk maakt, kunt u Azure AD-verificatie of verificatie op basis van een sleutel gebruiken.

- **Een Azure-VNet**. Als u uw pool in een [virtueel netwerk](batch-virtual-network.md)maakt, volgt u deze vereisten en configuraties. Als u een VNet met een of meer subnetten vooraf wilt voorbereiden, kunt u de Azure Portal, Azure PowerShell, de Azure Command-Line-interface (CLI) of een andere methode gebruiken.
  - Het VNet moet in hetzelfde abonnement en dezelfde regio voorkomen als het Batch-account dat u gebruikt om de pool te maken.
  - Het subnet dat is opgegeven voor de pool moet voldoende vrije IP-adressen hebben voor het aantal virtuele machines voor de pool, ofwel de som van de `targetDedicatedNodes`- en `targetLowPriorityNodes`-eigenschappen van de pool. Als het subnet onvoldoende vrije IP-adressen heeft, wijst de pool de rekenknooppunten gedeeltelijk toe en wordt een fout weergegeven voor het aanpassen van de grootte.
  - U moet de persoonlijke-koppelings service en het eindpunt netwerk beleid uitschakelen. Dit kan worden gedaan met behulp van Azure CLI: ```az network vnet subnet update --vnet-name <vnetname> -n <subnetname> --resouce-group <resourcegroup> --disable-private-endpoint-network-policies --disable-private-link-service-network-policies```

> [!IMPORTANT]
> Voor elke knoop punt met 100 toegewezen of lage prioriteit worden met batch één persoonlijke-koppelings service en één load balancer toegewezen. De beperkingen die voor deze resources gelden, worden bepaald door de [resourcequota](../azure-resource-manager/management/azure-subscription-service-limits.md) van het abonnement. Voor grote Pools moet u mogelijk [een quotum verhoging aanvragen](batch-quota-limit.md#increase-a-quota) voor een of meer van deze resources. Daarnaast moeten er geen resource vergrendelingen worden toegepast op resources die zijn gemaakt door batch, omdat hierdoor het opruimen van resources als gevolg van door de gebruiker geïnitieerde acties wordt voor komen, zoals het verwijderen van een pool of het wijzigen van de grootte in nul.

## <a name="current-limitations"></a>Huidige beperkingen

1. Pools zonder open bare IP-adressen moeten de configuratie van de virtuele machine gebruiken en niet Cloud Services configuratie.
1. [Aangepaste eindpunt configuratie](pool-endpoint-configuration.md) voor batch Compute-knoop punten werkt niet met Pools zonder open bare IP-adressen.
1. Omdat er geen open bare IP-adressen zijn, kunt u [uw eigen opgegeven open bare IP-adressen niet gebruiken](create-pool-public-ip.md) met dit type groep.

## <a name="create-a-pool-without-public-ip-addresses-in-the-azure-portal"></a>Een pool maken zonder open bare IP-adressen in de Azure Portal

1. Ga in Azure Portal naar uw Batch-account.
1. Selecteer **Pools** in het venster **instellingen** aan de linkerkant.
1. Selecteer in het venster **groepen** de optie **toevoegen**.
1. Selecteer in het venster **groep toevoegen** de optie die u wilt gebruiken in de vervolg keuzelijst **afbeeldings type** .
1. Selecteer de juiste **Uitgever/aanbieding/SKU** van uw installatie kopie.
1. Geef de resterende vereiste instellingen op, zoals de **knooppunt grootte**, het **doel toegewezen knoop punten** en **knoop punten met een lage prioriteit**, evenals de gewenste optionele instellingen.
1. Selecteer desgewenst een virtueel netwerk en een subnet dat u wilt gebruiken. Dit virtuele netwerk moet zich in dezelfde resource groep bevinden als de groep die u maakt.
1. Selecteer **NoPublicIPAddresses** in het **inrichtings type voor IP-adressen**.

![Scherm opname van het NoPublicIPAddresses voor het toevoegen van een groep, waarbij de selectie is ingeschakeld.](./media/batch-pool-no-public-ip-address/create-pool-without-public-ip-address.png)

## <a name="use-the-batch-rest-api-to-create-a-pool-without-public-ip-addresses"></a>De batch-REST API gebruiken om een pool te maken zonder open bare IP-adressen

In het onderstaande voor beeld ziet u hoe u de [Azure Batch rest API](/rest/api/batchservice/pool/add) gebruikt om een groep te maken die gebruikmaakt van open bare IP-adressen.

### <a name="rest-api-uri"></a>REST API-URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

### <a name="request-body"></a>Aanvraagbody

```json
"pool": {
     "id": "pool2",
     "vmSize": "standard_a1",
     "virtualMachineConfiguration": {
          "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.040-LTS"
          },
     "nodeAgentSKUId": "batch.node.ubuntu 16.04"
     }
     "networkConfiguration": {
          "subnetId": "/subscriptions/<your_subscription_id>/resourceGroups/<your_resource_group>/providers/Microsoft.Network/virtualNetworks/<your_vnet_name>/subnets/<your_subnet_name>",
          "publicIPAddressConfiguration": {
               "provision": "NoPublicIPAddresses"
          }
     },
     "resizeTimeout": "PT15M",
     "targetDedicatedNodes": 5,
     "targetLowPriorityNodes": 0,
     "taskSlotsPerNode": 3,
     "taskSchedulingPolicy": {
          "nodeFillType": "spread"
     },
     "enableAutoScale": false,
     "enableInterNodeCommunication": true,
     "metadata": [
    {
      "name": "myproperty",
      "value": "myvalue"
    }
       ]
}
```

## <a name="outbound-access-to-the-internet"></a>Uitgaande toegang tot Internet

In een groep zonder open bare IP-adressen, hebben uw virtuele machines geen toegang tot het open bare internet tenzij u de netwerk instellingen op de juiste wijze configureert, bijvoorbeeld via NAT van het [virtuele netwerk](../virtual-network/nat-overview.md). Houd er rekening mee dat NAT alleen uitgaande toegang tot Internet van de virtuele machines in het virtuele netwerk toestaat. Batch-gemaakte reken knooppunten zijn niet openbaar toegankelijk, omdat er geen open bare IP-adressen aan zijn gekoppeld.

Een andere manier om uitgaande connectiviteit te bieden is door gebruik te maken van een door de gebruiker gedefinieerde route (UDR). Zo kunt u verkeer door sturen naar een proxy computer die open bare Internet toegang heeft.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het maken van Pools in een virtueel netwerk](batch-virtual-network.md).
- Meer informatie over het [gebruik van privé-eind punten met batch-accounts](private-connectivity.md).
