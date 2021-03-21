---
title: Resource beschikbaarheid per regio
description: Beschik baarheid van reken-en geheugen resources voor de Azure Container Instances-service in verschillende Azure-regio's.
ms.topic: article
ms.date: 04/27/2020
ms.custom: references_regions
ms.openlocfilehash: 4decf29be23c2f1ed51f422052869e99abe4a511
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573605"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Beschik baarheid van resources voor Azure Container Instances in azure-regio's

In dit artikel vindt u informatie over de beschik baarheid van Azure Container Instances compute, geheugen en opslag resources in azure-regio's en op het doel besturingssysteem. Zie [beschik bare regio's](https://azure.microsoft.com/regions/services/)voor een algemene lijst met beschik bare regio's voor Azure container instances.

De weer gegeven waarden zijn de Maxi maal beschik bare bronnen per implementatie van een [container groep](container-instances-container-groups.md). Waarden zijn actueel op het moment van publicatie.

> [!NOTE]
> Container groepen die zijn gemaakt binnen deze resource limieten, zijn afhankelijk van de beschik baarheid binnen de implementatie regio. Wanneer een regio zwaar wordt belast, kan er een fout optreden bij het implementeren van instanties. Als u een dergelijke implementatie fout wilt verhelpen, implementeert u instanties met lagere bron instellingen of probeert u de implementatie op een later tijdstip of in een andere regio met beschik bare resources.

Zie [quota's en limieten voor Azure container instances](container-instances-quotas.md)voor meer informatie over quota's en andere limieten in uw implementaties.

## <a name="linux-container-groups"></a>Linux-container groepen

De volgende regio's en maximum resources zijn beschikbaar voor container groepen met Linux-containers in algemene implementaties, Azure-implementaties van [virtuele netwerken](container-instances-vnet.md) en implementaties met [GPU-bronnen](container-instances-gpu.md) (preview).

> [!IMPORTANT]
> De maximale bronnen in een regio verschillen afhankelijk van uw implementatie. Een regio kan bijvoorbeeld een andere maximale CPU-en geheugen grootte hebben in een implementatie van een virtueel Azure-netwerk dan voor een algemene implementatie. Dezelfde regio kan ook een andere set maximum waarden hebben voor een implementatie met GPU-resources. Controleer uw implementatie type voordat u de onderstaande tabellen controleert op de maximum waarden in uw regio.

| Region | Maximaal CPU-gebruik | Maxi maal geheugen (GB) | Maximale CPU voor VNET | Max. VNET-geheugen (GB) | Opslag (GB) | GPU-Sku's (preview-versie) |
| -------- | :---: | :---: | :----: | :-----: | :-------: | :----: |
| Australië - oost | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| Brazilië - zuid | 4 | 16 | 2 | 8 | 50 | N.v.t. |
| Canada - midden | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| India - centraal | 4 | 16 | 4 | 4 | 50 | V100 |
| VS - centraal | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| Azië - oost | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| VS - oost | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| VS - oost 2 | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| Frankrijk - centraal | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| Duitsland - west-centraal | 3 | 16 | N.v.t. | N.v.t. | 50 | N.v.t. |
| Japan - oost | 2 | 8 | 4 | 16 | 50 | N.v.t. |
| Korea - centraal | 4 | 16 | N.v.t. | N.v.t. | 50 | N.v.t. |
| VS - noord-centraal | 2 | 3,5 | 4 | 16 | 50 | K80, P100, V100 |
| Europa - noord | 4 | 16 | 4 | 16 | 50 | K80 |
| VS - zuid-centraal | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| Azië - zuidoost | 4 | 16 | 4 | 16 | 50 | P100, V100 |
| India - zuid | 4 | 16 | N.v.t. | N.v.t. | 50 | N.v.t. |
| Zwitserland - noord | 3 | 16 | N.v.t. | N.v.t. | 50 | N.v.t. |
| Verenigd Koninkrijk Zuid | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| VAE - noord | 3 | 16 | N.v.t. | N.v.t. | 50 | N.v.t. |
| VS - west-centraal| 4 | 16 | 4 | 16 | 50 | N.v.t. |
| Europa -west | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |
| VS - west | 4 | 16 | 4 | 16 | 50 | N.v.t. |
| VS - west 2 | 4 | 16 | 4 | 16 | 50 | K80, P100, V100 |

De volgende maximum bronnen zijn beschikbaar voor een container groep die is geïmplementeerd met [GPU-bronnen](container-instances-gpu.md) (preview-versie).

> [!IMPORTANT]
> Op dit moment worden implementaties met GPU-bronnen niet ondersteund in een implementatie van een virtueel Azure-netwerk en zijn deze alleen beschikbaar in Linux-container groepen.

| GPU-Sku's | Aantal GPU | Maximaal CPU-gebruik | Maxi maal geheugen (GB) | Opslag (GB) |
| --- | --- | --- | --- | --- |
| K80 | 1 | 6 | 56 | 50 |
| K80 | 2 | 12 | 112 | 50 |
| K80 | 4 | 24 | 224 | 50 |
| P100, V100 | 1 | 6 | 112 | 50 |
| P100, V100 | 2 | 12 | 224 | 50 |
| P100, V100 | 4 | 24 | 448 | 50 |

## <a name="windows-container-groups"></a>Windows-container groepen

De volgende regio's en maximum resources zijn beschikbaar voor container groepen met [ondersteunde en preview](container-instances-faq.md#what-windows-base-os-images-are-supported) Windows Server-containers.

> [!IMPORTANT]
> Op dit moment worden implementaties met Windows-container groepen niet ondersteund in een implementatie van een virtueel Azure-netwerk.

###  <a name="windows-server-2016"></a>Windows Server 2016

> [!NOTE]
> Zie [host-en container versie compatibiliteit](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) voor meer informatie over de hosts van 1B, 2b en 3b.

| Region | 1B/2B maximum CPU | 1B/2B Max. geheugen (GB) |3B maximum CPU | 3B Max. geheugen (GB) | Opslag (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Australië - oost | 2 | 8 | 2 | 8 | 20 |
| Brazilië - zuid | 4 | 16 | 4 | 16 | 20 |
| Canada - midden | 2 | 8 | 2 | 3,5 | 20 |
| India - centraal | 2 | 3,5 | 2 | 3,5 | 20 |
| VS - centraal | 2 | 3,5 | 2 | 3,5 | 20 |
| Azië - oost | 2 | 3,5 | 2 | 3,5 | 20 |
| VS - oost | 4 | 16 | 2 | 8 | 20 |
| VS - oost 2 | 2 | 3,5 | 4 | 16 | 20 |
| Japan - oost | 4 | 16 | 4 | 16 | 20 |
| Korea - centraal | 4 | 16 | 4 | 16 | 20 |
| VS - noord-centraal | 4 | 16 | 4 | 16 | 20 |
| Europa - noord | 2 | 8 | 2 | 8 | 20 |
| VS - zuid-centraal | 2 | 3,5 | 2 | 8 | 20 |
| Azië - zuidoost | N.v.t. | N.v.t. | 2 | 3,5 | 20 |
| India - zuid | 2 | 3,5 | 2 | 3,5 | 20 |
| Verenigd Koninkrijk Zuid | 2 | 8 | 2 | 3,5 | 20 |
| VS - west-centraal | 4 | 16 | 2 | 8 | 20 |
| Europa -west | 4 | 16 | 4 | 16 | 20 |
| VS - west | 4 | 16 | 2 | 8 | 20 |
| VS - west 2 | 2 | 8 | 2 | 3,5 | 20 |


### <a name="windows-server-2019-ltsc"></a>Windows Server 2019 LTSC

> [!NOTE]
> Zie [host-en container versie compatibiliteit](/virtualization/windowscontainers/deploy-containers/update-containers#host-and-container-version-compatibility) voor meer informatie over de hosts van 1B, 2b en 3b.

| Region | 1B/2B maximum CPU | 1B/2B Max. geheugen (GB) |3B maximum CPU | 3B Max. geheugen (GB) | Opslag (GB) |
| -------- | :---: | :---: | :----: | :-----: | :-------: |
| Australië - oost | 4 | 16 | 4 | 16 | 20 |
| Brazilië - zuid | 4 | 16 | 4 | 16 | 20 |
| Canada - midden | 4 | 16 | 4 | 16 | 20 |
| India - centraal | 4 | 16 | 4 | 16 | 20 |
| VS - centraal | 4 | 16 | 4 | 16 | 20 |
| Azië - oost | 4 | 16 | 4 | 16 | 20 |
| VS - oost | 4 | 16 | 4 | 16 | 20 |
| VS - oost 2 | 2 | 3,5 | 2 | 3,5 | 20 |
| Frankrijk - centraal | 4 | 16 | 4 | 16 | 20 |
| Japan - oost | N.v.t. | N.v.t. | 4 | 16 | 20 |
| Korea - centraal | 4 | 16 | 4 | 16 | 20 |
| VS - noord-centraal | 4 | 16 | 4 | 16 | 20 |
| Europa - noord | 4 | 16 | 4 | 16 | 20 |
| VS - zuid-centraal | 4 | 16 | 4 | 16 | 20 |
| Azië - zuidoost | 4 | 16 | 4 | 16 | 20 |
| India - zuid | 4 | 16 | 4 | 16 | 20 |
| Verenigd Koninkrijk Zuid | 4 | 16 | 4 | 16 | 20 |
| VS - west-centraal | 4 | 16 | 4 | 16 | 20 |
| Europa -west | 4 | 16 | 4 | 16 | 20 |
| VS - west | 4 | 16 | 4 | 16 | 20 |
| VS - west 2 | 2 | 8 | 4 | 16 | 20 |

## <a name="next-steps"></a>Volgende stappen

Laat het team weten of u extra regio's of meer Beschik baarheid van resources wilt zien op [aka.MS/ACI/feedback](https://aka.ms/aci/feedback).

Zie [problemen met implementaties oplossen met Azure container instances](container-instances-troubleshooting.md)voor meer informatie over het oplossen van problemen met de implementatie van container instanties.


[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
