---
title: Azure HDInsight-versleuteling in transit
description: Meer informatie over beveiligings functies voor het leveren van versleuteling voor uw Azure HDInsight-cluster.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: fb3761ce7839cb4450997da094646b6604aeb895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98946860"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>IPSec-versleuteling in transit voor Azure HDInsight

In dit artikel wordt de implementatie van versleuteling in transit beschreven voor communicatie tussen Azure HDInsight-cluster knooppunten.

## <a name="background"></a>Achtergrond

Azure HDInsight biedt diverse beveiligings functies voor het beveiligen van uw bedrijfs gegevens. Deze oplossingen worden gegroepeerd op de pijlers van de beveiliging, verificatie, autorisatie, controle, versleuteling en naleving van de rand. Versleuteling kan worden toegepast op gegevens, zowel op rest als tijdens de overdracht.

Versleuteling op rest wordt gedekt door versleuteling aan de server zijde van Azure Storage-accounts, evenals schijf versleuteling op de Azure-Vm's die deel uitmaken van uw HDInsight-cluster.

Versleuteling van gegevens in transit op HDInsight wordt bereikt met [Transport Layer Security (TLS)](../transport-layer-security.md) voor toegang tot de cluster gateways en [Internet Protocol Security (IPSec)](https://wikipedia.org/wiki/IPsec) tussen cluster knooppunten. IPSec kan eventueel worden ingeschakeld tussen alle hoofd knooppunten, Worker-knoop punten, Edge-knoop punten, Zookeeper-knoop punten en gateway [-en id Broker-](./identity-broker.md) knoop punten.

## <a name="enable-encryption-in-transit"></a>Versleuteling in transit inschakelen

### <a name="azure-portal"></a>Azure Portal

Voer de volgende stappen uit om een nieuw cluster te maken met versleuteling in transit dat is ingeschakeld met behulp van de Azure Portal:

1. Begin met het normale proces voor het maken van het cluster. Zie op [Linux gebaseerde clusters maken in HDInsight met behulp van de Azure portal voor de](../hdinsight-hadoop-create-linux-clusters-portal.md) eerste stappen voor het maken van het cluster.
1. Voltooi de tabbladen **basis beginselen** en **opslag** . Ga naar het tabblad **beveiliging en netwerk** .

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Het tabblad Beveiliging en netwerk voor het cluster maken.":::

1. Schakel op het tabblad **beveiliging en netwerk** het selectie vakje **versleuteling in transit inschakelen in** .

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Cluster maken: Schakel versleuteling in door Voer in.":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Een cluster maken met versleuteling in transit dat is ingeschakeld via de Azure CLI

Versleuteling in transit is ingeschakeld met behulp van de `isEncryptionInTransitEnabled` eigenschap.

U kunt [een voorbeeld sjabloon en een parameter bestand downloaden](https://github.com/Azure-Samples/hdinsight-enterprise-security). Voordat u de sjabloon en het Azure CLI-code fragment hieronder gebruikt, vervangt u de volgende tijdelijke aanduidingen door de juiste waarden:

| Tijdelijke aanduiding | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | De ID van uw Azure-abonnement |
| `<RESOURCE_GROUP>` | De resource groep waar u het nieuwe cluster en opslag account wilt maken. |
| `<STORAGEACCOUNTNAME>` | Het bestaande opslag account dat moet worden gebruikt met het cluster. De naam moet de vorm hebben `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | De naam van uw HDInsight-cluster. |
| `<PASSWORD>` | Het wacht woord dat u hebt gekozen voor het aanmelden bij het cluster met behulp van SSH en het Ambari-dash board. |
| `<VNET_NAME>` | Het virtuele netwerk waar het cluster wordt geïmplementeerd. |

In het onderstaande code fragment worden de volgende eerste stappen uitgevoerd:

1. Meld u aan bij uw Azure-account.
1. Hiermee stelt u het actieve abonnement in waarin de bewerkingen worden gemaakt.
1. Hiermee maakt u een nieuwe resource groep voor de nieuwe implementatie activiteiten.
1. Implementeer de sjabloon voor het maken van een nieuw cluster.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az deployment group create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van bedrijfsbeveiliging in Azure HDInsight](hdinsight-security-overview.md)
* [Azure Active Directory gebruikers synchroniseren met een HDInsight-cluster](../disk-encryption.md).
