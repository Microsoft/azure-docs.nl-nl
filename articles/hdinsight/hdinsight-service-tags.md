---
title: NSG-service tags (netwerk beveiligings groep) voor Azure HDInsight
description: Gebruik HDInsight-service tags om binnenkomend verkeer naar uw cluster toe te staan vanuit de knoop punten status-en beheer Services, zonder IP-adressen aan uw Nsg's toe te voegen.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 1a90bc6636dcb3aa81f09b0489850c1a95b3256d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022748"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>NSG-service tags voor Azure HDInsight

De labels van de Azure HDInsight-service voor netwerk beveiligings groepen (Nsg's) zijn groepen IP-adressen voor status-en beheer Services. Deze groepen helpen de complexiteit te minimaliseren voor het maken van de beveiligings regel. [Service Tags](../virtual-network/network-security-groups-overview.md#service-tags) staan inkomend verkeer van specifieke ip's toe zonder elk van de [beheer-IP-adressen](hdinsight-management-ip-addresses.md) in uw nsg's in te voeren.

De HDInsight-service beheert deze service tags. U kunt geen eigen servicetag maken of een bestaande tag wijzigen. Micro soft beheert de adres voorvoegsels die overeenkomen met het servicetag en werkt de servicetag automatisch bij met gewijzigde adressen.

Als u een bepaalde regio wilt gebruiken en de servicetag nog niet is gedocumenteerd op deze pagina, kunt u de service [Tags detectie-API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) gebruiken om uw servicetag te vinden. U kunt ook het JSON-bestand van de [service label](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) downloaden en naar uw gewenste regio zoeken.

## <a name="get-started-with-service-tags"></a>Aan de slag met Service Tags

U hebt twee opties voor het gebruik van service tags in uw netwerk beveiligings groepen:

- **Eén algemeen HDInsight-service label gebruiken**: met deze optie wordt het virtuele netwerk geopend op alle IP-adressen die de HDInsight-service gebruikt om clusters in alle regio's te bewaken. Deze optie is de eenvoudigste methode, maar is mogelijk niet geschikt als u strenge beveiligings vereisten hebt.

- **Meerdere regionale service tags gebruiken: met** deze optie wordt het virtuele netwerk geopend op alleen de IP-adressen die door HDInsight worden gebruikt in die specifieke regio. Als u echter meerdere regio's gebruikt, moet u meerdere service Tags toevoegen aan het virtuele netwerk.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Eén algemeen HDInsight-service label gebruiken

De eenvoudigste manier om te beginnen met het gebruik van service tags met uw HDInsight-cluster is het toevoegen van de algemene tag `HDInsight` aan een NSG-regel.

1. Selecteer uw netwerk beveiligings groep in het [Azure Portal](https://portal.azure.com/).

1. Selecteer bij **instellingen** de optie **regels voor binnenkomende beveiliging** en selecteer vervolgens **+ toevoegen**.

1. Selecteer in de vervolg keuzelijst **bron** de optie **service label**.

1. Selecteer **HDInsight** in de vervolg keuzelijst **bron service label** .

    ![Een servicetag van de Azure Portal toevoegen](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Dit label bevat de IP-adressen van de status-en beheer Services voor alle regio's waar HDInsight beschikbaar is. Met het label zorgt u ervoor dat uw cluster kan communiceren met de benodigde status-en beheer Services, ongeacht waar deze zijn gemaakt.

## <a name="use-regional-hdinsight-service-tags"></a>Regionale HDInsight-service tags gebruiken

Als de optie globale code niet werkt omdat u meer beperkende machtigingen nodig hebt, kunt u alleen de service Tags toestaan die van toepassing zijn op uw regio. Er kunnen meerdere service tags zijn, afhankelijk van de regio waarin het cluster is gemaakt.

Lees de volgende secties van het artikel als u wilt weten welke service tags u voor uw regio wilt toevoegen.

### <a name="use-a-single-regional-service-tag"></a>Eén regionale servicetag gebruiken

Als uw cluster zich bevindt in een regio die in deze tabel wordt vermeld, hoeft u slechts één regionale servicetag aan uw NSG toe te voegen.

| Land | Regio | Servicetag |
| ---- | ---- | ---- |
| Australië | Australië - oost | HDInsight. AustraliaEast |
| &nbsp; | Australië - zuidoost | HDInsight. AustraliaSoutheast |
| &nbsp; | Australië - centraal | HDInsight. AustraliaCentral |
| China | China - oost 2 | HDInsight. ChinaEast2 |
| &nbsp; | China - noord 2 | HDInsight. ChinaNorth2 |
| Verenigde Staten | VS - noord-centraal | HDInsight. NorthCentralUS |
| &nbsp; | VS - west 2 | HDInsight. WestUS2 |
| &nbsp; | VS - west-centraal | HDInsight. WestCentralUS |
| Canada | Canada - oost | HDInsight. CanadaEast |
| Brazilië | Brazil South | HDInsight. BrazilSouth |
| Korea | Korea - centraal | HDInsight. KoreaCentral |
| &nbsp; | Korea - zuid | HDInsight. KoreaSouth |
| India | India - centraal | HDInsight. CentralIndia |
| &nbsp; | India - zuid | HDInsight. SouthIndia |
| Japan | Japan - west | HDInsight. JapanWest |
| Frankrijk | Frankrijk - centraal| HDInsight. FranceCentral |
| VK | Verenigd Koninkrijk Zuid | HDInsight. UKSouth |
| Azure Government | USDoD-centraal | HDInsight. USDoDCentral |
| &nbsp; | USGov Texas | HDInsight. USGovTexas |
| &nbsp; | UsDoD-Oost | HDInsight. USDoDEast |
| &nbsp; | USGov Arizona | HDInsight. USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Meerdere regionale service tags gebruiken

Als de regio waar uw cluster is gemaakt, niet in de voor gaande tabel wordt vermeld, moet u meerdere regionale service Tags toestaan. De nood zaak om meer dan één te gebruiken, is vanwege verschillen in de rang schikking van resource providers voor de verschillende regio's.

De overige regio's worden onderverdeeld in groepen op basis van de regionale service tags die ze gebruiken.

#### <a name="group-1"></a>Groep 1

Als uw cluster wordt gemaakt in een van de regio's in de volgende tabel, kunt u de service tags `HDInsight.WestUS` en toestaan `HDInsight.EastUS` . Ook de regionale servicetag wordt vermeld. Voor regio's in deze sectie zijn drie service Tags vereist.

Als uw cluster bijvoorbeeld in de regio wordt gemaakt `East US 2` , moet u de volgende service Tags toevoegen aan de netwerk beveiligings groep:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Land | Regio | Servicetag |
| ---- | ---- | ---- |
| Verenigde Staten | VS - oost 2 | HDInsight. EastUS2 |
| &nbsp; | Central US | HDInsight. Centraalus |
| &nbsp; | NorthCentral | HDInsight. NorthCentralUS |
| &nbsp; | VS - zuid-centraal | HDInsight. SouthCentralUS |
| &nbsp; | VS - oost | HDInsight. Oostelijkeus |
| &nbsp; | VS - west | HDInsight. Westelijkeus |
| Japan | Japan - oost | HDInsight. JapanEast |
| Europa | Europa - noord | HDInsight. NorthEurope |
| &nbsp; | Europa -west| HDInsight. Europa West |
| Azië | Azië - oost | HDInsight. EastAsia |
| &nbsp; | Azië - zuidoost | HDInsight. SoutheastAsia |
| Australië | Australië - oost | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Groep 2

Clusters in de regio's van *China-Noord* en *China-Oost* twee service Tags toestaan: `HDInsight.ChinaNorth` en `HDInsight.ChinaEast` .

#### <a name="group-3"></a>Groep 3

Clusters in de regio's van *US gov-Iowa* en *US gov-Virginia* twee service Tags toestaan: `HDInsight.USGovIowa` en `HDInsight.USGovVirginia` .

#### <a name="group-4"></a>Groep 4

Clusters in de regio's van *Duitsland-centraal* en *Duitsland-Noordoost* twee service Tags toestaan: `HDInsight.GermanyCentral` en `HDInsight.GermanyNortheast` .

## <a name="next-steps"></a>Volgende stappen

- [Netwerk beveiligings groepen: Service Tags](../virtual-network/network-security-groups-overview.md#security-rules)
- [Virtuele netwerken voor Azure HDInsight-clusters maken](hdinsight-create-virtual-network.md)