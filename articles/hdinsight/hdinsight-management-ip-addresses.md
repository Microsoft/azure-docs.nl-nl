---
title: IP-adressen van Azure HDInsight-beheer
description: Meer informatie over welke IP-adressen u binnenkomend verkeer moet toestaan, om netwerk beveiligings groepen en door de gebruiker gedefinieerde routes correct te configureren voor virtuele netwerken met Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 9fa38e045bbe29e5d45587adf0d277c1414fee4c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549035"
---
# <a name="hdinsight-management-ip-addresses"></a>IP-adressen beheren met HDInsight

In dit artikel vindt u de IP-adressen die worden gebruikt door Azure HDInsight Health and Management Services. Als u netwerk beveiligings groepen (Nsg's) of door de gebruiker gedefinieerde routes (Udr's) gebruikt, moet u mogelijk enkele van deze IP-adressen toevoegen aan de acceptatie lijst voor binnenkomend netwerk verkeer.

## <a name="introduction"></a>Inleiding
 
> [!Important]
> In de meeste gevallen kunt u nu [service Tags](hdinsight-service-tags.md) gebruiken voor netwerk beveiligings groepen, in plaats van het hand matig toevoegen van IP-adressen. IP-adressen worden niet gepubliceerd voor nieuwe Azure-regio's en ze hebben alleen gepubliceerde service tags. De vaste IP-adressen voor beheer-IP-adressen zullen uiteindelijk worden afgeschaft.

Als u netwerk beveiligings groepen (Nsg's) of door de gebruiker gedefinieerde routes (Udr's) gebruikt om inkomend verkeer naar uw HDInsight-cluster te beheren, moet u ervoor zorgen dat uw cluster kan communiceren met de essentiële Azure-status-en beheer Services.  Sommige IP-adressen voor deze services zijn regio-specifiek en sommige van deze zijn van toepassing op alle Azure-regio's. Mogelijk moet u ook verkeer van de Azure DNS-service toestaan als u geen aangepaste DNS gebruikt.

Als u IP-adressen nodig hebt voor een regio die hier niet wordt vermeld, kunt u de [service Tags detectie-API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) gebruiken om IP-adressen voor uw regio te vinden. Als u de API niet kunt gebruiken, downloadt u het [JSON-bestand van de service label](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) en zoekt u naar de gewenste regio.

HDInsight valideert deze regels met het maken en schalen van clusters, om verdere fouten te voor komen. Als de validatie niet is geslaagd, wordt maken en schalen mislukt.

In de volgende secties worden de specifieke IP-adressen besproken die moeten worden toegestaan.

## <a name="azure-dns-service"></a>Azure DNS-service

Als u de door Azure verschafte DNS-service gebruikt, verleent u toegang tot __168.63.129.16__ op poort 53 voor TCP-en UDP-verbindingen. Zie voor meer informatie de [naam omzetting voor vm's en rollen instanties](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) document. Als u aangepaste DNS gebruikt, kunt u deze stap overs Laan.

## <a name="health-and-management-services-all-regions"></a>Status-en beheer Services: alle regio's

Verkeer toestaan van de volgende IP-adressen voor Azure HDInsight-status-en beheer Services, die van toepassing zijn op alle Azure-regio's:

| IP-adres van bron | Doel  | Richting |
| ---- | ----- | ----- |
| 168.61.49.99 | \*: 443 | Inkomend |
| 23.99.5.239 | \*: 443 | Inkomend |
| 168.61.48.131 | \*: 443 | Inkomend |
| 138.91.141.162 | \*: 443 | Inkomend |

## <a name="health-and-management-services-specific-regions"></a>Status-en beheer Services: specifieke regio's

Verkeer toestaan van de IP-adressen die worden vermeld voor de Azure HDInsight-status-en beheer Services in de specifieke Azure-regio waar uw resources zich bevinden:

> [!IMPORTANT]  
> Als de Azure-regio die u gebruikt niet wordt weer gegeven, gebruikt u [de functie servicetag](hdinsight-service-tags.md) voor netwerk beveiligings groepen.

| Land | Regio | Toegestane IP-adressen van bron | Toegestaan doel | Richting |
| ---- | ---- | ---- | ---- | ----- |
| Azië | Azië - oost | 23.102.235.122</br>52.175.38.134 | \*: 443 | Inkomend |
| &nbsp; | Azië - zuidoost | 13.76.245.160</br>13.76.136.249 | \*: 443 | Inkomend |
| Australië | Australië - oost | 104.210.84.115</br>13.75.152.195 | \*: 443 | Inkomend |
| &nbsp; | Australia Southeast | 13.77.2.56</br>13.77.2.94 | \*: 443 | Inkomend |
| Brazilië | Brazil South | 191.235.84.104</br>191.235.87.113 | \*: 443 | Inkomend |
| Canada | Canada - oost | 52.229.127.96</br>52.229.123.172 | \*: 443 | Inkomend |
| &nbsp; | Canada - midden | 52.228.37.66</br>52.228.45.222 |\*: 443 | Inkomend |
| China | China - noord | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*: 443 | Inkomend |
| &nbsp; | China East | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*: 443 | Inkomend |
| &nbsp; | China - noord 2 | 40.73.37.141</br>40.73.38.172 | \*: 443 | Inkomend |
| &nbsp; | China - oost 2 | 139.217.227.106</br>139.217.228.187 | \*: 443 | Inkomend |
| Europa | Europa - noord | 52.164.210.96</br>13.74.153.132 | \*: 443 | Inkomend |
| &nbsp; | Europa -west| 52.166.243.90</br>52.174.36.244 | \*: 443 | Inkomend |
| Frankrijk | Frankrijk - centraal| 20.188.39.64</br>40.89.157.135 | \*: 443 | Inkomend |
| Duitsland | Duitsland - centraal | 51.4.146.68</br>51.4.146.80 | \*: 443 | Inkomend |
| &nbsp; | Duitsland - noordoost | 51.5.150.132</br>51.5.144.101 | \*: 443 | Inkomend |
| India | India - centraal | 52.172.153.209</br>52.172.152.49 | \*: 443 | Inkomend |
| &nbsp; | India - zuid | 104.211.223.67<br/>104.211.216.210 | \*: 443 | Inkomend |
| Japan | Japan - oost | 13.78.125.90</br>13.78.89.60 | \*: 443 | Inkomend |
| &nbsp; | Japan - west | 40.74.125.69</br>138.91.29.150 | \*: 443 | Inkomend |
| Korea | Korea - centraal | 52.231.39.142</br>52.231.36.209 | \*: 443 | Inkomend |
| &nbsp; | Korea - zuid | 52.231.203.16</br>52.231.205.214 | \*: 443 | Inkomend
| Verenigd Koninkrijk | Verenigd Koninkrijk West | 51.141.13.110</br>51.141.7.20 | \*: 443 | Inkomend |
| &nbsp; | Verenigd Koninkrijk Zuid | 51.140.47.39</br>51.140.52.16 | \*: 443 | Inkomend |
| Verenigde Staten | Central US | 13.89.171.122</br>13.89.171.124 | \*: 443 | Inkomend |
| &nbsp; | VS - oost | 13.82.225.233</br>40.71.175.99 | \*: 443 | Inkomend |
| &nbsp; | VS - noord-centraal | 157.56.8.38</br>157.55.213.99 | \*: 443 | Inkomend |
| &nbsp; | VS - west-centraal | 52.161.23.15</br>52.161.10.167 | \*: 443 | Inkomend |
| &nbsp; | VS - west | 13.64.254.98</br>23.101.196.19 | \*: 443 | Inkomend |
| &nbsp; | West US 2 | 52.175.211.210</br>52.175.222.222 | \*: 443 | Inkomend |
| &nbsp; | VAE - noord | 65.52.252.96</br>65.52.252.97 | \*: 443 | Inkomend |
| &nbsp; | UAE - centraal | 20.37.76.96</br>20.37.76.99 | \*: 443 | Inkomend |

Voor informatie over de IP-adressen die voor Azure Government moeten worden gebruikt, raadpleegt u het document [Azure Government Intelligence en Analytics](../azure-government/compare-azure-government-global-azure.md) .

Zie [netwerk verkeer beheren](./control-network-traffic.md)voor meer informatie.

Als u door de gebruiker gedefinieerde routes (Udr's) gebruikt, moet u een route opgeven en uitgaand verkeer van het virtuele netwerk naar de bovenstaande IP-adressen toestaan met de volgende hop ingesteld op ' Internet '.

## <a name="next-steps"></a>Volgende stappen

* [Virtuele netwerken voor Azure HDInsight-clusters maken](hdinsight-create-virtual-network.md)
* [NSG-service tags (netwerk beveiligings groep) voor Azure HDInsight](hdinsight-service-tags.md)
