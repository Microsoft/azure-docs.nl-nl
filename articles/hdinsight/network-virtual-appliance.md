---
title: Virtueel netwerk apparaat configureren in azure HDInsight
description: Meer informatie over het configureren van een aantal extra functies voor uw virtuele netwerk apparaat in azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 7fe266c3c7b75762133fca4645e0675845c28972
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943977"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Virtueel netwerk apparaat configureren in azure HDInsight

> [!Important]
> De volgende informatie is **alleen** vereist als u een andere virtuele netwerk apparaten (NVA) dan [Azure firewall](./hdinsight-restrict-outbound-traffic.md)wilt configureren.

Azure Firewall FQDN-label wordt automatisch geconfigureerd om verkeer toe te staan voor veel van de algemene essentiële FQDN-namen. Als u een ander virtueel netwerk apparaat wilt gebruiken, moet u een aantal extra functies configureren. Houd rekening met de volgende factoren wanneer u uw virtuele netwerk apparaat configureert:

* Services die geschikt zijn voor service-eind punten kunnen worden geconfigureerd met Service-eind punten die leiden tot het overs laan van de NVA, meestal voor kosten-of prestatie overwegingen.
* Als ResourceProviderConnection is ingesteld op *outbound*, kunt u privé-eind punten gebruiken voor de opslag-en SQL-servers voor meta Stores en hoeft u deze niet toe te voegen aan de NVA.
* Afhankelijkheden van IP-adressen zijn voor niet-HTTP/S-verkeer (TCP-en UDP-verkeer).
* FQDN HTTP/HTTPS-eind punten kunnen worden goedgekeurd in uw NVA-apparaat.
* Wijs de route tabel toe die u hebt gemaakt voor uw HDInsight-subnet.

## <a name="service-endpoint-capable-dependencies"></a>Afhankelijkheden voor service-eind punten

U kunt eventueel een of meer van de volgende service-eind punten inschakelen, waardoor de NVA wordt overgeslagen. Deze optie kan handig zijn voor grote hoeveel heden gegevens overdracht om kosten te besparen en ook voor prestatie optimalisaties. 

| **Eindpunt** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Afhankelijkheden van IP-adressen

| **Eindpunt** | **Details** |
|---|---|
| [Hier](hdinsight-management-ip-addresses.md) gepubliceerde ip's | Deze IP-adressen zijn voor de HDInsight-resource provider en moeten worden opgenomen in de UDR om asymmetrische route ring te voor komen. Deze regel is alleen nodig als de ResourceProviderConnection is ingesteld op *binnenkomend*. Als de ResourceProviderConnection is ingesteld op *outbound* , zijn deze IP-adressen niet nodig in de UDR.  |
| Privé-IP-adressen van AAD-DS | Alleen vereist voor ESP-clusters als de VNETs niet zijn gekoppeld.|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-afhankelijkheden

U kunt de lijst met FQDN-afhankelijkheden ophalen (voornamelijk Azure Storage en Azure Service Bus) voor het configureren van uw NVA [in deze opslag plaats](https://github.com/Azure-Samples/hdinsight-fqdn-lists/). Zie [hier](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional)voor de regio lijst. Deze afhankelijkheden worden gebruikt door de HDInsight-resource provider (RP) om clusters te maken en te controleren en te beheren. Dit zijn onder andere telemetrie/Diagnostische logboeken, het inrichten van meta gegevens, aan het cluster gerelateerde configuraties, scripts, enzovoort. Deze FQDN-afhankelijkheids lijst kan worden gewijzigd met het vrijgeven van toekomstige HDInsight-updates.

De onderstaande lijst bevat alleen enkele FQDN-namen die nodig kunnen zijn voor besturingssysteem-en beveiligings patches of validatie van certificaten *nadat* het cluster is gemaakt en tijdens de levens duur van cluster bewerkingen:

| **Runtime-afhankelijkheden FQDN**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com/pki/mscorp/cps/default.htm:443                                      |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>Volgende stappen

* [Firewall gebruiken om uitgaand verkeer te beperken](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight Virtual Network-architectuur](hdinsight-virtual-network-architecture.md)
