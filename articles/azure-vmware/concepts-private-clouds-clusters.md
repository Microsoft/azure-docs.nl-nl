---
title: Concepten-persoonlijke Clouds en clusters
description: Meer informatie over de belangrijkste mogelijkheden van de Azure VMware-oplossing software-gedefinieerde data centers en vSphere-clusters.
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 6a2bb0f41bb1809d0f43b08c71766acc914c0689
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509262"
---
#  <a name="azure-vmware-solution-private-cloud-and-cluster-concepts"></a>Azure VMware-oplossing persoonlijke Cloud-en cluster concepten

De Azure VMware-oplossing levert persoonlijke Clouds op basis van VMware in Azure. Persoonlijke Clouds bevatten clusters die zijn gebouwd met speciale, Bare-Metal Azure-hosts. Ze worden geïmplementeerd en beheerd via de Azure Portal, CLI of Power shell.  Clusters die zijn ingericht in private clouds zijn VMware vSphere-, vCenter-, vSAN-en NSX-software. Azure VMware-oplossing, hardware-en software-implementaties in de privécloud zijn volledig geïntegreerd en geautomatiseerd in Azure.

Er is een logische relatie tussen Azure-abonnementen, persoonlijke Clouds van Azure VMware, vSAN-clusters en hosts. Het diagram toont één Azure-abonnement met twee persoonlijke Clouds die de ontwikkelings-en productie omgeving vertegenwoordigen.  In elk van deze persoonlijke Clouds zijn twee clusters. 

In dit artikel worden al deze concepten beschreven.

![Afbeelding van twee persoonlijke Clouds in een klant abonnement](./media/hosts-clusters-private-clouds-final.png)

>[!NOTE]
>Vanwege de lagere mogelijke behoeften van een ontwikkel omgeving gebruikt u kleinere clusters met hosts met een lagere capaciteit. 

## <a name="private-clouds"></a>Privéclouds

Persoonlijke Clouds bevatten vSAN-clusters die zijn gebouwd met speciale, Bare-Metal Azure-hosts. Elke privécloud kan meerdere clusters hebben die worden beheerd door dezelfde vCenter-Server en NSX-T-beheer. U kunt persoonlijke Clouds implementeren en beheren in de portal, CLI of Power shell. 

Net als bij andere resources worden persoonlijke Clouds geïnstalleerd en beheerd vanuit een Azure-abonnement. Het aantal privé-Clouds in een abonnement is schaalbaar. In eerste instantie is er een limiet van één privécloud per abonnement.

## <a name="clusters"></a>Clusters
Voor elke privécloud die is gemaakt, is er standaard één vSAN-cluster. U kunt clusters toevoegen, verwijderen en schalen met behulp van de Azure Portal of via de API.  Alle clusters hebben een standaard grootte van drie hosts en kunnen tot 16 hosts worden geschaald.  De hosts die in een cluster worden gebruikt, moeten hetzelfde type host zijn.

Proef clusters kunnen worden geëvalueerd en beperkt tot drie hosts. Er is één proef cluster per privécloud. U kunt een proef cluster op één host schalen tijdens de evaluatie periode.

U gebruikt vSphere en NSX-T-beheer om de meeste andere aspecten van de cluster configuratie of-bewerking te beheren. Alle lokale opslag van elke host in een cluster wordt beheerd door vSAN.

## <a name="hosts"></a>Hosts

Azure VMware-oplossing Private Cloud clusters gebruiken Hyper-geconvergeerde, Bare-Metal Infrastructure-hosts. In de volgende tabel ziet u het RAM-geheugen, de CPU en de schijf capaciteit van de host. 

| Hosttype              |             CPU             |   RAM (GB)   |  vSAN NVMe-cache-laag (TB, RAW)  |  vSAN SSD-capaciteits tier (TB, RAW)  |
| :---                   |            :---:            |    :---:     |               :---:              |                :---:               |
| High-End (HE)          |  Dual Intel 18 Core 2,3 GHz  |     576      |                3.2               |                15,20               |

Hosts die worden gebruikt voor het bouwen of schalen van clusters, zijn afkomstig uit een geïsoleerde groep hosts. Deze hosts hebben hardware-tests door lopen en alle gegevens zijn veilig verwijderd. 

## <a name="vmware-software-versions"></a>VMware-software versies

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="host-maintenance-and-lifecycle-management"></a>Host onderhoud en levenscyclus beheer

Host onderhoud en levenscyclus beheer hebben geen invloed op de capaciteit of prestaties van de particuliere cloud clusters.  Voor beelden van automatische host onderhoud zijn onder andere firmware-upgrades en reparaties of vervangingen van hardware.

Micro soft is verantwoordelijk voor het levenscyclus beheer van NSX-T-apparaten, zoals NSX-T-beheer en NSX-T EDGE. Ze zijn ook verantwoordelijk voor het Boots trappen van de netwerk configuratie, zoals het maken van de laag-0-gateway en het inschakelen van North-South route ring. U bent zelf verantwoordelijk voor de configuratie van NSX-T SDN. Bijvoorbeeld netwerk segmenten, gedistribueerde firewall regels, laag 1-gateways en load balancers.

> [!IMPORTANT]
> Wijzig de configuratie van de gateway NSX-T-rand of laag-0 niet, omdat dit kan leiden tot een verlies van de service.

## <a name="backup-and-restoration"></a>Back-ups maken en herstellen

De persoonlijke Cloud-vCenter-en NSX-T-configuraties worden gepland volgens een back-upschema per uur.  Back-ups worden drie dagen bewaard. Als u vanuit een back-up wilt herstellen, opent u een [ondersteunings aanvraag](https://rc.portal.azure.com/#create/Microsoft.Support) in de Azure Portal om de herstel bewerking aan te vragen.

## <a name="next-steps"></a>Volgende stappen

Nu u Azure VMware-oplossingen hebt ondergebracht, kunt u het volgende weten: 

- [Azure VMware-oplossingen voor netwerk-en interconnectiviteit-concepten](concepts-networking.md).
- [Concepten van Azure VMware-oplossingen opslag](concepts-storage.md).
- [Informatie over het inschakelen van de Azure VMware Solution-resource](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VCSA versions]: https://kb.vmware.com/s/article/2143838
[ESXi versions]: https://kb.vmware.com/s/article/2143832
[vSAN versions]: https://kb.vmware.com/s/article/2150753

