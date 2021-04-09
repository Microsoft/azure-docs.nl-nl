---
title: Trans port Layer Security in azure HDInsight
description: Trans port Layer Security (TLS) en Secure Sockets Layer (SSL) zijn cryptografische protocollen die communicatie beveiliging bieden via een computer netwerk.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 648f24e87e7d8a49c9c495a78e56afb8ae9e8aae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944699"
---
# <a name="transport-layer-security-in-azure-hdinsight"></a>Trans port Layer Security in azure HDInsight

Verbindingen met het HDInsight-cluster via het open bare cluster-eind punt `https://CLUSTERNAME.azurehdinsight.net` worden via cluster gateway knooppunten via een proxy verzonden. Deze verbindingen worden beveiligd met een protocol dat TLS wordt genoemd. Het afdwingen van hogere versies van TLS op gateways verbetert de beveiliging voor deze verbindingen. Zie [het probleem met het TLS 1,0 oplossen](/security/solving-tls1-problem)voor meer informatie over de reden waarom u nieuwere versies van TLS moet gebruiken.

Standaard accepteren Azure HDInsight-clusters TLS 1,2-verbindingen op open bare HTTPS-eind punten en oudere versies voor achterwaartse compatibiliteit. U kunt de minimale TLS-versie die wordt ondersteund op de gateway knooppunten tijdens het maken van het cluster beheren met behulp van de Azure Portal of een resource manager-sjabloon. Selecteer voor de Portal de TLS-versie op het tabblad **beveiliging en netwerk** tijdens het maken van het cluster. Gebruik de eigenschap **minSupportedTlsVersion** voor een resource manager-sjabloon op het moment van implementatie. Zie de Quick Start- [sjabloon voor mini maal TLS 1,2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls)voor een voorbeeld sjabloon. Deze eigenschap ondersteunt drie waarden: "1,0", "1,1" en "1,2", die overeenkomen met respectievelijk TLS 1.0 +, TLS 1.1 + en TLS 1.2 +.


## <a name="next-steps"></a>Volgende stappen

* [Een virtueel netwerk plannen voor Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md)
* [Virtuele netwerken maken voor Azure HDInsight-clusters](hdinsight-create-virtual-network.md).
* [Netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md).