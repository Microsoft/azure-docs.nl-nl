---
title: Limieten en quota voor Azure-abonnementen
description: Biedt een lijst met algemene Azure-abonnementen en service limieten, quota's en beperkingen. Dit artikel bevat informatie over het verhogen van de limieten en maximum waarden.
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 4d56c5c3fd33d1cda05e23d5bc53e7540428671b
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547845"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure-abonnements-en service limieten, quota's en beperkingen

Dit document bevat enkele van de meest voorkomende Microsoft Azure limieten, ook wel quota's genoemd.

Zie [overzicht van Azure-prijzen](https://azure.microsoft.com/pricing/)voor meer informatie over Azure-prijzen. Daar kunt u uw kosten ramen met behulp van de [prijs calculator](https://azure.microsoft.com/pricing/calculator/). U kunt ook naar de pagina prijs informatie voor een bepaalde service gaan, bijvoorbeeld Windows- [vm's](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Zie [onverwachte kosten voor komen met Azure billing and cost management](../../cost-management-billing/cost-management-billing-overview.md)voor tips voor het beheren van uw kosten.

## <a name="managing-limits"></a>Limieten beheren

> [!NOTE]
> Sommige services hebben aanpas bare limieten.
>
> Wanneer een service geen aanpas bare limieten heeft, gebruiken de volgende tabellen de header **limiet**. In dat geval zijn de standaard-en maximum limieten hetzelfde.
>
> Wanneer de limiet kan worden aangepast, bevatten de tabellen **standaard limiet** en **maximum limieten** . De limiet kan worden verhoogd tot boven de standaard limiet, maar niet hoger dan de maximum limiet.
>
> Als u de limiet of het quotum boven de standaard limiet wilt verhogen, kunt u gratis [een online klant ondersteuning-aanvraag openen](../templates/error-resource-quota.md).
>
> De termen *zachte limiet* en *vaste limiet* worden vaak gebruikt om de huidige, aanpas bare limiet (zachte limiet) en de maximum limiet (harde limiet) te beschrijven. Als een limiet niet aanpasbaar is, is er geen zachte limiet, alleen een vaste limiet.
>

[Gratis proef abonnementen](https://azure.microsoft.com/offers/ms-azr-0044p) komen niet in aanmerking voor limiet of quotum verhoging. Als u een [gratis proef abonnement](https://azure.microsoft.com/offers/ms-azr-0044p)hebt, kunt u een upgrade uitvoeren naar een abonnement met [betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) . Zie voor meer informatie [uw abonnement op gratis proef versie van Azure bijwerken naar een betalen per gebruik-abonnement](../../cost-management-billing/manage/upgrade-azure-subscription.md) en de [Veelgestelde vragen over het gratis proef abonnement](https://azure.microsoft.com/free/free-account-faq).

Sommige limieten worden beheerd op een regionaal niveau.

We gebruiken vCPU quota's als voor beeld. Als u een quotum toename wilt aanvragen met ondersteuning voor Vcpu's, moet u bepalen hoeveel Vcpu's u wilt gebruiken in welke regio's. Vervolgens maakt u een specifieke aanvraag voor de vCPU quota's voor Azure-resource groepen voor de hoeveel heden en regio's die u wilt. Als u in Europa-west 30 Vcpu's moet gebruiken om uw toepassing daar uit te voeren, vraagt u in Europa-west specifiek 30 Vcpu's aan. Uw vCPU-quotum wordt niet verhoogd in een andere regio: alleen Europa-west heeft het vCPU-quotum van 30.

Daarom moet u bepalen wat uw Azure-resource groep-quota's voor uw werk belasting in een bepaalde regio moeten zijn. Vraag vervolgens die hoeveelheid aan in elke regio waarin u wilt implementeren. Voor hulp bij het bepalen van de huidige quota's voor specifieke regio's raadpleegt u [fouten voor resource quota oplossen](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Algemene limieten

Zie [naamgevings regels en beperkingen voor Azure-resources](resource-name-rules.md)voor limieten voor resource namen.

Zie [Resource Manager-aanvragen beperken](request-limits-and-throttling.md)voor meer informatie over de lees-en schrijf limieten van de Resource Manager-API.

### <a name="management-group-limits"></a>Limieten voor beheer groepen

De volgende limieten gelden voor [beheer groepen](../../governance/management-groups/overview.md).

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Abonnementslimieten

De volgende limieten gelden wanneer u Azure Resource Manager-en Azure-resource groepen gebruikt.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limieten voor resource groep

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Active Directory limieten

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>API Management limieten

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>App Service limieten

De volgende App Service limieten zijn limieten voor Web Apps, Mobile Apps en API Apps.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Automatiserings limieten

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-app-configuration"></a>Azure App Configuration

[!INCLUDE [app-configuration-limits](../../../includes/app-configuration-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Azure-cache voor redis-limieten

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Limieten voor Azure-Cloud Services

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Limieten voor Azure-Cognitive Search

Prijs categorieën bepalen de capaciteit en limieten van uw zoek service. Lagen zijn onder andere:

* **Gratis** multi tenant-service, gedeeld met andere Azure-abonnees, is bedoeld voor evaluatie-en kleine ontwikkelings projecten.
* **Basic** biedt specifieke computer bronnen voor productie werkbelastingen op een kleinere schaal, met Maxi maal drie replica's voor Maxi maal beschik bare query werkbelastingen.
* De **standaard**, waaronder S1, S2, S3 en S3 high density, is voor grotere productie workloads. Er zijn meerdere niveaus in de laag standaard, zodat u een resource configuratie kunt kiezen die het beste overeenkomt met uw werkbelasting profiel.

**Limieten per abonnement**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Limieten per zoek service**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Zie [service limieten in Azure Cognitive Search](../../search/search-limits-quotas-capacity.md)voor meer informatie over de limieten op een meer gedetailleerd niveau, zoals de document grootte, query's per seconde, sleutels, aanvragen en antwoorden.

## <a name="azure-cognitive-services-limits"></a>Limieten voor Azure-Cognitive Services

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB limieten

Zie [limieten in azure Cosmos DB](../../cosmos-db/concepts-limits.md)voor Azure Cosmos DB limieten.

## <a name="azure-data-explorer-limits"></a>Limieten voor Azure-Data Explorer

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Zie [beperkingen in azure database for MySQL](../../mysql/concepts-limits.md)voor Azure database for MySQL limieten.

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

Zie [beperkingen in azure database for PostgreSQL](../../postgresql/concepts-limits.md)voor Azure database for PostgreSQL limieten.

## <a name="azure-functions-limits"></a>Azure Functions limieten

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

Zie voor meer informatie [functions hosting abonnementen comparison](../../azure-functions/functions-scale.md#hosting-plans-comparison)(Engelstalig).

## <a name="azure-kubernetes-service-limits"></a>Limieten voor Azure Kubernetes-service

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Azure Machine Learning limieten

De meest recente waarden voor Azure Machine Learning Compute quota vindt u op de [Azure machine learning quotum pagina](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Azure Maps limieten

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Azure Monitor limieten

### <a name="alerts"></a>Waarschuwingen

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Actiegroepen

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="autoscale"></a>Automatisch schalen

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-autoscale.md)]

### <a name="log-queries-and-language"></a>Query's en taal in logboek registreren

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics-werkruimten

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Azure Policy limieten

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-signalr-service-limits"></a>Limieten voor Azure signalerings service

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Back-uplimieten

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Batch limieten

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Limieten voor klassiek implementatie model

Als u het klassieke implementatie model gebruikt in plaats van het Azure Resource Manager-implementatie model, zijn de volgende limieten van toepassing.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Container Instances limieten

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Container Registry limieten

De volgende tabel bevat een overzicht van de functies en limieten van de [service lagen](../../container-registry/container-registry-skus.md)Basic, Standard en Premium.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Content Delivery Network limieten

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Data Factory limieten

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Data Lake Analytics limieten

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-storage-limits"></a>Data Lake Storage limieten

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Limieten voor gegevens delen

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Database Migration Service limieten

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="digital-twins-limits"></a>Digital Apparaatdubbels-limieten

> [!NOTE]
> Sommige gebieden van deze service hebben aanpas bare limieten en andere niet. Dit wordt weer gegeven in de tabellen hieronder met de kolom *aanpasbaar?* . Wanneer de limiet kan worden aangepast, is de *aanpas bare?* -waarde *Ja*.

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>Event Grid limieten

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Event Hubs limieten

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="iot-central-limits"></a>IoT Central limieten
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>IoT Hub limieten

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service limieten

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Key Vault limieten

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="managed-identity-limits"></a>Beheerde identiteits limieten

[!INCLUDE [Managed-Identity-Limits](../../../includes/managed-identity-limits.md)]


## <a name="media-services-limits"></a>Media Services limieten

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services v2 (verouderd)

Zie [Media Services v2 (verouderd)](../../media-services/previous/media-services-quotas-and-limitations.md) voor limieten die specifiek zijn voor Media Services v2 (verouderd)

## <a name="mobile-services-limits"></a>Mobile Services limieten

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication limieten

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Netwerklimieten

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Limieten voor ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-network-gateway-limits"></a>Virtual Network gateway limieten

[!INCLUDE [virtual-network-gateway-limits](../../../includes/azure-virtual-network-gateway-limits.md)]

### <a name="nat-gateway-limits"></a>Limieten voor NAT-gateway

[!INCLUDE [nat-gateway-limits](../../../includes/azure-nat-gateway-limits.md)]

### <a name="virtual-wan-limits"></a>Virtuele WAN-limieten

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Application Gateway limieten

De volgende tabel is van toepassing op v1-, v2-, Standard-en WAF-Sku's, tenzij anders vermeld.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Network Watcher limieten

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Private Link-limieten

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

## <a name="purview-limits"></a>Limieten voor controle sfeer liggen

De meest recente waarden voor Azure controle sfeer liggen quota vindt u op de [Azure controle sfeer liggen-quotum pagina](../../purview/how-to-manage-quotas.md)

### <a name="traffic-manager-limits"></a>Traffic Manager limieten

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Limieten voor Azure Bastion

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Azure DNS limieten

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Azure Firewall limieten

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Azure front-deur-service limieten

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Notification Hubs limieten

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="azure-role-based-access-control-limits"></a>Limieten voor toegangs beheer op basis van rollen in azure

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control-limits.md)]

## <a name="service-bus-limits"></a>Service Bus limieten

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Site Recovery-limieten

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>SQL Database limieten

Zie [SQL database resource limieten voor afzonderlijke data bases](../../azure-sql/database/resource-limits-vcore-single-databases.md), [SQL database resource limieten voor elastische Pools en gepoolde data bases](../../azure-sql/database/resource-limits-vcore-elastic-pools.md)en [SQL database resource limieten voor SQL Managed instance](../../azure-sql/managed-instance/resource-limits.md)voor SQL database limieten.

## <a name="azure-synapse-analytics-limits"></a>Limieten voor Azure Synapse Analytics

Zie [Azure Synapse-resource limieten](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md)voor limieten voor Azure Synapse Analytics.

## <a name="storage-limits"></a>Opslag limieten

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Zie [schaalbaarheids doelen voor standaard opslag accounts](../../storage/common/scalability-targets-standard-account.md)voor meer informatie over limieten voor standaard opslag accounts.

### <a name="storage-resource-provider-limits"></a>Limieten van opslag Resource provider

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Limieten voor Azure Blob-opslag

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Azure Files limieten

Zie [Azure files schaal baarheid en prestatie doelen](../../storage/files/storage-files-scale-targets.md)voor meer informatie over Azure files limieten.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Azure File Sync limieten

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Opslag limieten voor Azure Queue

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Limieten voor Azure Table Storage

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Schijf limieten van virtuele machine

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Zie [grootten van virtuele machines](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)voor meer informatie.

#### <a name="disk-encryption-sets"></a>Schijf versleutelings sets

Er is een limiet van 50 schijf versleutelings sets per regio, per abonnement. Zie de documentatie over versleuteling voor virtuele [Linux](../../virtual-machines/disk-encryption.md#restrictions) -of [Windows](../../virtual-machines/disk-encryption.md#restrictions) -machines voor meer informatie. Neem contact op met de ondersteuning van Azure als u het quotum wilt verhogen.

### <a name="managed-virtual-machine-disks"></a>Beheerde schijven van virtuele machines

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Niet-beheerde schijven van virtuele machines

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>StorSimple systeem limieten

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Stream Analytics limieten

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Virtual Machines limieten

### <a name="virtual-machines-limits"></a>Virtual Machines limieten

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines limieten-Azure Resource Manager

De volgende limieten gelden wanneer u Azure Resource Manager-en Azure-resource groepen gebruikt.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Limieten voor gedeelde afbeeldingen galerie

Elk abonnement kent limieten voor het implementeren van resources met behulp van galerieën met gedeelde installatiekopieën:

- 100 galerieën met gedeelde installatiekopieën per abonnement per regio
- 1000 definities van installatiekopieën per abonnement per regio
- 10.000 versies van installatiekopieën per abonnement per regio

## <a name="virtual-machine-scale-sets-limits"></a>Limieten voor virtuele-machine schaal sets

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Zie tevens

* [Meer informatie over Azure-limieten en toename](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Grootten van virtuele machines en Cloud Services voor Azure](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Grootten voor Azure Cloud Services](../../cloud-services/cloud-services-sizes-specs.md)
* [Naamgevingsregels en -beperkingen voor Azure-resources](resource-name-rules.md)