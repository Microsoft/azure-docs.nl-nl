---
title: Azure HDInsight-clusters beveiligen en isoleren met een persoonlijke koppeling (preview-versie)
description: Meer informatie over het isoleren van Azure HDInsight-clusters in een virtueel netwerk met behulp van een persoonlijke Azure-koppeling.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 66c9a3afb91aaff448d6eadc86175d8515be766c
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94889079"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>Azure HDInsight-clusters beveiligen en isoleren met een persoonlijke koppeling (preview-versie)

In de [standaard virtuele-netwerk architectuur](./hdinsight-virtual-network-architecture.md)van Azure hdinsight communiceert de HDInsight-resource provider (RP) met het cluster met behulp van open bare IP-adressen. Voor sommige scenario's is volledige netwerk isolatie vereist zonder gebruik van open bare IP-adressen. In dit artikel vindt u informatie over de geavanceerde besturings elementen die u kunt gebruiken om een persoonlijk HDInsight-cluster te maken. Zie [netwerk verkeer beheren in azure HDInsight](./control-network-traffic.md)voor meer informatie over het beperken van het verkeer van en naar uw cluster zonder volledige netwerk isolatie.

U kunt persoonlijke HDInsight-clusters maken door specifieke netwerk eigenschappen te configureren in een Azure Resource Manager ARM-sjabloon. Er zijn twee eigenschappen die u kunt gebruiken om persoonlijke HDInsight-clusters te maken:

* Verwijder open bare IP-adressen door `resourceProviderConnection` in te stellen op uitgaand.
* Persoonlijke Azure-koppeling inschakelen en [privé-eind punten](../private-link/private-endpoint-overview.md) gebruiken door `privateLink` in te stellen op ingeschakeld.

## <a name="remove-public-ip-addresses"></a>Open bare IP-adressen verwijderen

De HDInsight RP maakt standaard gebruik van een *inkomende* verbinding met het cluster met behulp van open bare ip's. Wanneer de `resourceProviderConnection` eigenschap netwerk is ingesteld op *uitgaand*, worden de verbindingen met de HDInsight RP omgekeerd zodat de verbindingen altijd vanuit het cluster naar de RP worden geïnitieerd. Zonder een binnenkomende verbinding hoeven de inkomende service tags of open bare IP-adressen niet te worden gemaakt.

De Basic load balancers die worden gebruikt in de standaard virtuele-netwerk architectuur, bieden automatisch een open bare NAT (Network Address Translation) voor toegang tot de vereiste uitgaande afhankelijkheden, zoals de HDInsight RP. Als u de uitgaande verbinding met het open bare Internet wilt beperken, kunt u [een firewall configureren](./hdinsight-restrict-outbound-traffic.md), maar dit is geen vereiste.

Als `resourceProviderConnection` u configureert voor uitgaand verkeer, kunt u ook toegang krijgen tot specifieke cluster bronnen, zoals Azure data Lake Storage Gen2 of externe meta-archieven, met behulp van persoonlijke eind punten. Het gebruik van privé-eind punten voor deze resources is niet mandetory, maar als u van plan bent om persoonlijke eind punten voor deze resources te gebruiken, moet u de persoonlijke eind punten en DNS-vermeldingen configureren `before` die het HDInsight-cluster maken. We raden u aan alle externe SQL-data bases die u nodig hebt, zoals Apache zwerver, Ambari, Oozie en Hive-meta Stores, te maken en op te geven tijdens het maken van het cluster. De vereiste is dat al deze bronnen toegankelijk moeten zijn vanuit het subnet van het cluster, hetzij via hun eigen privé-eind punt, hetzij anderszins.

Het gebruik van privé-eind punten voor Azure Key Vault wordt niet ondersteund. Als u Azure Key Vault gebruikt voor CMK-versleuteling in rust, moet het Azure Key Vault-eind punt toegankelijk zijn vanuit het HDInsight-subnet zonder persoonlijk eind punt.

In het volgende diagram ziet u hoe een mogelijke virtuele HDInsight-netwerk architectuur eruit kan zien wanneer deze `resourceProviderConnection` is ingesteld op uitgaand:

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagram van HDInsight-architectuur met behulp van een verbinding met een uitgaande resource provider":::

Nadat u het cluster hebt gemaakt, moet u de juiste DNS-omzetting instellen. De volgende canonieke naam DNS-record (CNAME) wordt gemaakt op de door Azure beheerde open bare DNS-zone: `azurehdinsight.net` .

```dns
<clustername>    CNAME    <clustername>-int
```

Als u toegang wilt krijgen tot het cluster met behulp van cluster-FQDN-namen, kunt u de interne load balancer persoonlijke Ip's rechtstreeks gebruiken of uw eigen Privé-DNS zone gebruiken om de cluster eindpunten te overschrijven, afhankelijk van uw behoeften. U kunt bijvoorbeeld een Privé-DNS zone hebben `azurehdinsight.net` . en voeg indien nodig uw persoonlijke Ip's toe:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>Persoonlijke koppeling inschakelen

Een persoonlijke koppeling, die standaard is uitgeschakeld, vereist uitgebreide netwerk kennis voor het instellen van door de gebruiker gedefinieerde routes (UDR) en firewall regels op de juiste manier voordat u een cluster maakt. Het gebruik van deze instelling is optioneel, maar is alleen beschikbaar wanneer de `resourceProviderConnection` eigenschap netwerk is ingesteld op *uitgaand* , zoals wordt beschreven in de vorige sectie.

Wanneer `privateLink` is ingesteld op *inschakelen*, worden interne [standaard load balancers](../load-balancer/load-balancer-overview.md) (SLB) gemaakt en wordt een Azure Private Link-service voor elke SLB ingericht. Met de persoonlijke koppelings service kunt u toegang krijgen tot het HDInsight-cluster vanuit privé-eind punten.

Standaard load balancers bieden niet automatisch de [open bare uitgaande NAT](../load-balancer/load-balancer-outbound-connections.md) , zoals Basic load balancers. U moet uw eigen NAT-oplossing opgeven, zoals [Virtual Network NAT](../virtual-network/nat-overview.md) of een [firewall](./hdinsight-restrict-outbound-traffic.md), voor uitgaande afhankelijkheden. Uw HDInsight-cluster moet nog steeds toegang hebben tot de uitgaande afhankelijkheden. Als deze uitgaande afhankelijkheden niet zijn toegestaan, kan het maken van het cluster mislukken.

### <a name="prepare-your-environment"></a>Uw omgeving voorbereiden

Voor het maken van persoonlijke koppelings Services moet u [netwerk beleid voor de persoonlijke koppelings service expliciet uitschakelen](../private-link/disable-private-link-service-network-policy.md).

In het volgende diagram ziet u een voor beeld van de netwerk configuratie die is vereist voordat u een cluster maakt. In dit voor beeld wordt al het uitgaande verkeer [geforceerd](../firewall/forced-tunneling.md) Azure Firewall met behulp van UDR en moeten de vereiste uitgaande afhankelijkheden "allowed" zijn op de firewall voordat een cluster wordt gemaakt. Voor Enterprise Security Package clusters kan de netwerk verbinding met Azure Active Directory Domain Services worden verschaft door VNet-peering.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagram van een persoonlijke koppelings omgeving vóór het maken van het cluster":::

Zodra u het netwerk hebt ingesteld, kunt u een cluster maken waarvoor een uitgaande resource provider verbinding en een persoonlijke koppeling is ingeschakeld, zoals wordt weer gegeven in de volgende afbeelding. In deze configuratie zijn er geen open bare Ip's en een persoonlijke koppelings service voor elke standaard load balancer ingericht.

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="Diagram van een persoonlijke koppelings omgeving na het maken van het cluster":::

### <a name="access-a-private-cluster"></a>Toegang tot een persoonlijk cluster

Als u toegang wilt krijgen tot persoonlijke clusters, kunt u de interne load balancer privé-Ip's rechtstreeks gebruiken, of u kunt gebruikmaken van DNS-extensies voor persoonlijke koppelingen en privé-eind punten. Als de `privateLink` instelling is ingesteld op ingeschakeld, kunt u uw eigen privé-eind punten maken en DNS-omzetting configureren via particuliere DNS-zones.

De vermeldingen voor privé koppelingen die zijn gemaakt in de open bare DNS-zone die door Azure `azurehdinsight.net` worden beheerd, zijn als volgt:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

In de volgende afbeelding ziet u een voor beeld van de privé-DNS-vermeldingen die zijn vereist om toegang te krijgen tot het cluster vanuit een virtueel netwerk dat niet is gekoppeld aan of geen rechtstreekse regel van het gezichts vermogen heeft voor de cluster load balancers. U kunt Azure private zone gebruiken om `*.privatelink.azurehdinsight.net` FQDN-namen te overschrijven en om te zetten in uw eigen persoonlijke eind punt-IP-adressen.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagram van de architectuur van een persoonlijke koppeling":::

## <a name="how-to-create-clusters"></a>Hoe kan ik clusters maken?
### <a name="use-arm-template-properties"></a>Eigenschappen van ARM-sjabloon gebruiken

Het volgende JSON-code fragment bevat de twee netwerk eigenschappen die u moet configureren in uw ARM-sjabloon om een privé HDInsight-cluster te maken.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

Voor een volledige sjabloon met veel van de beveiligings functies van HDInsight-ondernemingen, waaronder persoonlijke koppeling, Zie [HDInsight Enter prise Security-sjabloon](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

### <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

Zie het [voor beeld voor](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster?view=azps-5.1.0#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature)het gebruik van Power shell.

### <a name="use-azure-cli"></a>Azure CLI gebruiken
Zie het [voor beeld als](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az_hdinsight_create-examples)u Azure cli wilt gebruiken.

## <a name="next-steps"></a>Volgende stappen

* [Enterprise Security Package voor Azure HDInsight](enterprise-security-package.md)
* [Algemene informatie over Enter prise Security en richt lijnen in azure HDInsight](./domain-joined/general-guidelines.md)
