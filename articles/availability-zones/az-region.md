---
title: Azure-services die beschikbaarheidszones ondersteunen
description: Als u Maxi maal beschik bare en flexibele toepassingen in azure wilt maken, Beschikbaarheidszones u fysiek afzonderlijke locaties bieden die u kunt gebruiken om uw resources uit te voeren.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 2a2e4ac57eec866d9857f564d6c76ad4a775d223
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874605"
---
# <a name="azure-services-that-support-availability-zones"></a>Azure-services die beschikbaarheidszones ondersteunen

Microsoft Azure wereld wijde infra structuur is ontworpen en gebouwd op elke laag om de hoogste mate van redundantie en tolerantie voor de klanten te leveren. De Azure-infra structuur bestaat uit geografi, regio's en Beschikbaarheidszones, die de versterking van een storing beperken, waardoor de potentiële impact op klant toepassingen en-gegevens wordt beperkt. De Azure-beschikbaarheidszones-constructie is ontwikkeld om een software-en netwerk oplossing te bieden om te beschermen tegen datacenter fouten en om verhoogde hoge Beschik baarheid (HA) te bieden aan onze klanten.

Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer data centers met onafhankelijke voeding, koeling en netwerken. De fysieke schei ding van Beschikbaarheidszones binnen een regio beperkt de gevolgen voor toepassingen en gegevens uit zone fouten, zoals grootschalige Flooding, grote stormes en superstormen en andere gebeurtenissen die de toegang tot de site kunnen verstoren, veilige passeren, de uptime van uitgebreide hulpprogram ma's en de beschik baarheid van resources. Beschikbaarheidszones en hun bijbehorende data centers zijn zo ontworpen dat als er een zone wordt aangetast, de services, capaciteit en beschik baarheid worden ondersteund door de andere Beschikbaarheidszones in de regio.

Alle Azure Management-Services zijn ontworpen om te worden flexibeler dan storingen op regio niveau. In het spectrum van fouten, hebben een of meer fouten in de beschikbaarheids zone binnen een regio een kleinere fout RADIUS vergeleken met een volledige regio fout. Azure kan een storing op zone niveau van beheer services binnen een regio herstellen. Azure voert een kritieke onderhouds bewerking uit binnen een regio, om te voor komen dat er fouten optreden die van invloed zijn op klant resources die zijn geïmplementeerd op Beschikbaarheidszones binnen een regio.


![conceptuele weer gave van een Azure-regio met drie zones](./media/az-region/azure-region-availability-zones.png)


Azure-Services die Beschikbaarheidszones ondersteunen, kunnen worden onderverdeeld in drie categorieën: **zonegebonden**, **zone-redundante** en **niet-regionale** Services. Werk belastingen van klanten kunnen worden gecategoriseerd om gebruik te maken van een van deze architectuur scenario's om te voldoen aan de prestaties en duurzaamheid van toepassingen.

- **Zonegebonden Services** : een resource kan worden geïmplementeerd in een specifieke, zelfgeselecteerde beschikbaarheids zone voor meer strikte latentie of prestatie vereisten.  Tolerantie is zelf ontworpen door toepassingen en gegevens te repliceren naar een of meer zones binnen de regio.  Resources kunnen worden vastgemaakt aan een specifieke zone. Virtuele machines, Managed disks of standaard-IP-adressen kunnen bijvoorbeeld worden vastgemaakt aan een specifieke zone, waardoor de flexibiliteit kan worden verg root door een of meer exemplaren van resources over meerdere zones te verdelen.

- **Zone-redundante Services** : Azure-platform repliceert de resource en gegevens over zones.  Micro soft beheert de levering van hoge Beschik baarheid, omdat Azure automatisch exemplaren van de regio repliceert en distribueert.  ZRS bijvoorbeeld repliceert de gegevens over drie zones, zodat een zone storing geen invloed heeft op de HA van de gegevens. 

- **Niet-regionale Services** : services zijn altijd beschikbaar vanuit Azure-geografi en zijn flexibel voor de zone-brede storingen en de regionale storingen voor de hele regio. 


Als u een uitgebreide bedrijfs continuïteit wilt bereiken op Azure, bouwt u uw toepassings architectuur met de combi natie van Beschikbaarheidszones met Azure-regio paren. U kunt uw toepassingen en gegevens synchroon repliceren met Beschikbaarheidszones binnen een Azure-regio voor hoge Beschik baarheid en asynchroon repliceren in azure-regio's voor nood herstel beveiliging. Lees voor meer informatie over het [bouwen van oplossingen voor hoge Beschik baarheid met behulp van Beschikbaarheidszones](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Azure-Services die Beschikbaarheidszones ondersteunen

 - De virtuele machines van de vorige generatie worden niet weer gegeven. Zie voor meer informatie [vorige generaties virtuele machine grootten](../virtual-machines/sizes-previous-gen.md).
 - Zoals vermeld in de [regio's en Beschikbaarheidszones in azure](az-overview.md), zijn sommige services niet regionaal. Deze services hebben geen afhankelijkheid van een specifieke Azure-regio, omdat dit tot een groot aantal storingen in de zone ligt en voor regionale storingen.  De lijst met niet-regionale Services vindt u op [producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="azure-regions-with-availability-zones"></a>Azure-regio's met Beschikbaarheidszones


| Noord- en Zuid-Amerika           | Europa         | Duitsland              | Afrika              | Azië en Stille Oceaan   |
|--------------------|----------------|----------------------|---------------------|----------------|
|                    |                |                      |                     |                |
| Canada - midden     | Frankrijk - centraal | Duitsland - west-centraal | Zuid-Afrika-noord * | Japan - oost     |
| VS - centraal         | Europa - noord   |                      |                     | Azië - zuidoost |
| VS - oost            | Verenigd Koninkrijk Zuid       |                      |                     | Australië - oost |
| VS - oost 2          | Europa -west    |                      |                     |                |
| VS Zuid-Centraal |                |                      |                     |                |
| US Gov-Virginia * |                |                      |                     |                |
| VS-West 2        |                |                      |                     |                |


Voor meer informatie over de ondersteuning van Beschikbaarheidszones en beschik bare Services in deze regio's, neemt u contact op met uw micro soft-verkoop-of klant vertegenwoordiger. Zie [Azure-geografi](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)(Engelstalig) voor de aanstaande regio's die Beschikbaarheidszones ondersteunen.


## <a name="azure-services-supporting-availability-zones"></a>Azure-Services die Beschikbaarheidszones ondersteunen

- Virtuele machines van de vorige generatie worden hieronder niet weer gegeven. Zie voor meer informatie [vorige generaties virtuele machine grootten](../virtual-machines/sizes-previous-gen.md).

- Sommige services zijn niet regionaal, Zie [regio's en Beschikbaarheidszones in azure](az-overview.md) voor meer informatie. Deze services hebben geen afhankelijkheid van een specifieke Azure-regio, waardoor ze robuust zijn voor de zone-ruime onderbrekingen en de regionale storingen.  De lijst met niet-regionale Services vindt u op [producten die beschikbaar zijn per regio](https://azure.microsoft.com/global-infrastructure/services/).


### <a name="zone-resilient-services"></a>Zone flexibele services 

: globe_with_meridians: niet-regionale Services: services zijn altijd beschikbaar vanuit Azure-geografi en zijn flexibel voor de zone-brede storingen en de regionale storingen voor de hele regio.

: large_blue_diamond: robuust aan de zone-brede storingen 

**Foundational Services**

|     Producten                                                    | Flexibiliteit             |
|-----------------------------------------------------------------|:----------------------------:|
|     Opslag account                                           | : large_blue_diamond:  |
|     Application Gateway (v2)                                  | : large_blue_diamond:  |
|     Azure Backup                                                | : large_blue_diamond:  |
|     Azure Cosmos DB                                           | : large_blue_diamond:  |
|     Azure Data Lake Storage gen 2                             | : large_blue_diamond:  |
|     Azure Express-route                                       | : large_blue_diamond:  |
|     Open bare IP van Azure                                           | : large_blue_diamond:  |
|     Azure SQL Database (Algemeen laag)                 | : large_blue_diamond:  |
|     Azure SQL Database (Premium & Bedrijfskritiek-laag)     | : large_blue_diamond:  |
|     Disk Storage                                                | : large_blue_diamond:  |
|     Event Hubs                                                  | : large_blue_diamond:  |
|     Key Vault                                                   | : large_blue_diamond:  |
|     Load Balancer                                               | : large_blue_diamond:  |
|     Service Bus                                                 | : large_blue_diamond:  |
|     Service Fabric                                            | : large_blue_diamond:  |
|     Opslag: warme/koud Blob Storage lagen                      | : large_blue_diamond:  |
|     Opslag: Managed Disks                                    | : large_blue_diamond:  |
|     Virtual Machines schaal sets                               | : large_blue_diamond:  |
|     Virtual Machines                                          | : large_blue_diamond:  |
|     Virtual Machines: Av2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Bs-Series                               | : large_blue_diamond:  |
|     Virtual Machines: DSv2-Series                             | : large_blue_diamond:  |
|     Virtual Machines: DSv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Dv2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Dv3-Series                              | : large_blue_diamond:  |
|     Virtual Machines: ESv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Ev3-Series                              | : large_blue_diamond:  |
|     Virtual Network                                           | : large_blue_diamond:  |
|     VPN Gateway                                                 | : large_blue_diamond:  |


**Mainstream Services**

| Producten                                        | Flexibiliteit |
|-------------------------------------------------|:------------:|
| App Service-omgevingen                        |      : large_blue_diamond:  |
| Azure Active Directory Domain Services          |      : large_blue_diamond:  |
| Azure Bastion                                   |      : large_blue_diamond:  |
| Azure Cache voor Redis                           |      : large_blue_diamond:  |
| Azure Cognitive Services: Text Analytics        |      : large_blue_diamond:  |
| Azure Data Explorer                             |      : large_blue_diamond:  |
| Azure Database for MySQL: flexibele server      |      : large_blue_diamond:  |
| Azure Database for PostgreSQL: flexibele server |      : large_blue_diamond:  |
| Azure DDoS Protection                           |      : large_blue_diamond:  |
| Azure Firewall                                  |      : large_blue_diamond:  |
| Azure Firewall Manager                          |      : large_blue_diamond:  |
| Azure Kubernetes Service (AKS)                  |      : large_blue_diamond:  |
| Azure Private Link                              |      : large_blue_diamond:  |
| Azure Red Hat OpenShift                         |      : large_blue_diamond:  |
| Azure Site Recovery                             |      : large_blue_diamond:  |
| Container Registry                              |      : large_blue_diamond:  |
| Event Grid                                      |      : large_blue_diamond:  |
| Network Watcher                                 |      : large_blue_diamond:  |
| Power BI Embedded                               |      : large_blue_diamond:  |
| Premium-Blob Storage                            |      : large_blue_diamond:  |
| Virtual Machines: Ddsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Ddv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dsv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dv4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Edsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Edv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Esv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Ev4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Fsv2-Series                   |      : large_blue_diamond:  |
| Virtual Machines: M-serie                      |      : large_blue_diamond:  |
| Virtual WAN                                     |      : large_blue_diamond:  |


**Niet-regionale**

|     Producten                                  |     Flexibiliteit    |
|-----------------------------------------------|:-------------------:|
|     Azure DNS                                 |     : globe_with_meridians:             |
|     Azure Active Directory                  |     : globe_with_meridians:             |
|     Azure Advisor                             |     : globe_with_meridians:             |
|     Azure Bot Service                        |     : globe_with_meridians:             |
|     Azure Defender voor IoT                  |     : globe_with_meridians:             |
|     Azure Information Protection            |     : globe_with_meridians:             |
|     Azure-Lighthouse                        |     : globe_with_meridians:             |
|     Azure Managed Applications              |     : globe_with_meridians:             |
|     Azure Maps                                |     : globe_with_meridians:             |
|     Azure Policy                              |     : globe_with_meridians:             |
|     Azure-resource grafiek                    |     : globe_with_meridians:             |
|     Azure Stack                               |     : globe_with_meridians:             |
|     Azure Stack rand                        |     : globe_with_meridians:             |
|     Cloud Shell                               |     : globe_with_meridians:             |
|     Klanten-lockbox voor Microsoft Azure    |     : globe_with_meridians:             |
|     Microsoft Azure peering-service         |     : globe_with_meridians:             |
|     Microsoft Azure-portal                  |     : globe_with_meridians:             |
|     Security Center                         |     : globe_with_meridians:             |
|     Traffic Manager                         |     : globe_with_meridians:             |


## <a name="pricing-for-vms-in-availability-zones"></a>Prijzen voor Vm's in Beschikbaarheidszones

Er zijn geen extra kosten verbonden aan het implementeren van virtuele machines in een beschikbaarheids zone. Bekijk de [pagina met bandbreedte prijzen](https://azure.microsoft.com/pricing/details/bandwidth/)voor meer informatie.


## <a name="get-started-with-availability-zones"></a>Aan de slag met Beschikbaarheidszones

- [Een virtuele machine maken](../virtual-machines/windows/create-portal-availability-zone.md)
- [Een beheerde schijf toevoegen met Power shell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Een door een zone redundante schaalset voor virtuele machines maken](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Taak verdeling van virtuele machines in zones met behulp van een Standard Load Balancer met een zone-redundante front-end](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Taak verdeling van Vm's binnen een zone met behulp van een Standard Load Balancer met een zonegebonden-front-end](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Zone-redundante opslag](../storage/common/storage-redundancy.md)
- [SQL Database laag voor algemeen gebruik](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geo-noodherstel Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geo-noodherstel Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Een zone-redundante virtuele netwerkgateway maken](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Zone redundante regio voor Azure Cosmos DB toevoegen](../cosmos-db/high-availability.md#availability-zone-support)
- [Aan de slag met Azure cache voor redis Beschikbaarheidszones](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Een Azure Active Directory Domain Services-instantie maken](../active-directory-domain-services/tutorial-create-instance.md)
- [Een AKS-cluster (Azure Kubernetes service) maken dat gebruikmaakt van Beschikbaarheidszones](../aks/availability-zones.md)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Regio's en beschikbaarheidszones in Azure](az-overview.md)