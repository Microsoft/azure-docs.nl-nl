---
title: Hoe Cloud Foundry integreert met Azure | Microsoft Docs
description: Hierin wordt beschreven hoe Cloud Foundry Azure-Services kunt gebruiken om de bedrijfs ervaring te verbeteren
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: ff2a6618b60ff2cfa5faa74c905e140466a14359
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563316"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Cloud Foundry integreren met Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) is een Paas-platform dat wordt uitgevoerd boven op het IaaS-platform van cloud providers. Het biedt consistente toepassings implementaties voor alle cloud providers. Het kan ook worden geïntegreerd met verschillende Azure-Services, met hoogwaardige HA, schaal baarheid en kosten besparingen van de onderneming.
Er zijn [zes subsystemen van Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), die flexibel kunnen worden geschaald, waaronder: route ring, authenticatie, beheer van de levens cyclus van toepassingen, Service beheer, berichten en bewaking. Voor elk van de subsystemen kunt u Cloud Foundry configureren voor het gebruik van de correspondent Azure-service. 

![Cloud Foundry op integratie architectuur van Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. hoge Beschik baarheid en schaal baarheid
### <a name="managed-disk"></a>Managed Disk
Bosh maakt gebruik van Azure CPI (Cloud Provider Interface) voor het maken en verwijderen van routines. Standaard worden niet-beheerde schijven gebruikt. Hiervoor moet de klant hand matig opslag accounts maken en vervolgens de accounts in CF-manifest bestanden configureren. Dit komt door de beperking van het aantal schijven per opslag account.
Nu [Managed Disk](https://azure.microsoft.com/services/managed-disks/) beschikbaar is, biedt beheerde beveiligde en betrouw bare schijf opslag voor virtuele machines. De klant hoeft niet meer te omgaan met het opslag account voor schaal en HA. In Azure worden schijven automatisch gerangschikt. Of het nu gaat om een nieuwe of bestaande implementatie, de Azure CPI zorgt voor het maken of migreren van de beheerde schijf tijdens een CF-implementatie. Het wordt ondersteund door PCF 1,11. U kunt ook de richt lijnen voor de Open Source Cloud Foundry [Managed Disk](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) verkennen ter referentie. 
### <a name="availability-zone-"></a>Beschikbaarheids zone *
Als een Cloud-systeem eigen toepassings platform is Cloud Foundry ontworpen met een [hoge mate van Beschik baarheid](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Hoewel de eerste drie niveaus van software fouten kunnen worden verwerkt door het CF-systeem zelf, wordt de fout tolerantie van het platform verschaft door cloud providers. De Key CF-onderdelen moeten worden beveiligd met een platform HA-oplossing van een Cloud provider. Dit omvat GoRouters, Diego hersenen, CF data base en service-tegels. Standaard wordt de [Azure-beschikbaarheidsset](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) gebruikt voor fout tolerantie tussen clusters in een Data Center.
De nieuwe [Azure-beschikbaarheids zone](../availability-zones/az-overview.md) wordt nu vrijgegeven, waardoor de fout tolerantie naar het volgende niveau wordt geduurd, met een lage latentie redundantie op data centers.
De Azure-beschikbaarheids zone behaalt HA door een set Vm's in twee of meer data centers te plaatsen, waarbij elke set Vm's overbodig is voor andere sets. Als de ene zone niet beschikbaar is, zijn de andere sets nog steeds Live, geïsoleerd van de nood geval.
> [!NOTE] 
> De Azure-beschikbaarheids zone wordt nog niet aan alle regio's aangeboden. Controleer de meest recente [aankondiging voor de lijst met ondersteunde regio's](../availability-zones/az-overview.md). Voor Open Source Cloud Foundry raadpleegt u de [Azure-beschikbaarheids zone voor de richt lijnen voor Open Source-Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone).

## <a name="2-network-routing"></a>2. netwerk routering
Standaard wordt Azure Basic load balancer gebruikt voor binnenkomende CF API/apps-aanvragen, door ze door te sturen naar de Gorouters. CF-onderdelen, zoals Diego hersenen, MySQL, ERT kunnen ook gebruikmaken van de load balancer om het verkeer voor HA te verdelen. Azure biedt ook een set volledig beheerde oplossingen voor taak verdeling. Als u op zoek bent naar TLS/SSL-beëindiging ("SSL-offload") of per HTTP/HTTPS-aanvraag verwerking, kunt u Application Gateway. Voor hoge Beschik baarheid en schaal baarheid taak verdeling op laag 4, kunt u het beste de standaard load balancer.
### <a name="azure-application-gateway-"></a>Azure-toepassing gateway *
[Azure-toepassing gateway](../application-gateway/overview.md) biedt verschillende mogelijkheden voor de taak verdeling van laag 7, zoals SSL-offloading, end-to-end TLS, Web Application firewall, sessie affiniteit op basis van cookies en meer. U kunt [Application Gateway configureren in Open Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Voor PCF raadpleegt u de  [release opmerkingen voor PCF 2,1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) voor haalbaarheids test.

### <a name="azure-standard-load-balancer-"></a>Azure Standard Load Balancer *
Azure Load Balancer is een laag 4-load balancer. Het wordt gebruikt om het verkeer te verdelen over instanties van services in een set met gelijke taak verdeling. De standaard versie biedt [geavanceerde functies](../load-balancer/load-balancer-overview.md) boven op de basis versie. Bijvoorbeeld 1. De maximum limiet voor de back-end-groep is verhoogd van 100 naar 1000 Vm's.  2. De eind punten ondersteunen nu meerdere beschikbaarheids sets in plaats van één beschikbaarheidsset.  3. Extra functies, zoals HA-poorten, uitgebreide bewakings gegevens, enzovoort. Als u overstapt naar de beschikbaarheids zone van Azure, is standaard load balancer vereist. Voor een nieuwe implementatie raden we u aan om te beginnen met Azure Standard Load Balancer. 

## <a name="3-authentication"></a>3. verificatie 
[Cloud Foundry gebruikers account en-verificatie](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) is de centrale identiteits beheer service voor CF en de verschillende onderdelen. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) is de multi tenant, cloud-gebaseerde Directory-en identiteits beheer service van micro soft. UAA wordt standaard gebruikt voor Cloud Foundry-verificatie. Als een geavanceerde optie ondersteunt UAA ook Azure AD als externe gebruikers opslag. Azure AD-gebruikers hebben toegang tot Cloud Foundry met behulp van hun LDAP-identiteit, zonder een Cloud Foundry-account. Volg deze stappen om [Azure ad te configureren voor UAA in PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. gegevens opslag voor Cloud Foundry runtime-systeem
Cloud Foundry biedt een geweldige uitbreid baarheid voor het gebruik van Azure blobarchief of Azure MySQL/PostgreSQL-Services voor toepassings runtime-systeem opslag.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Azure Blobarchief voor Cloud Foundry-Cloud controller blobarchief
De blobarchief van de Cloud controller is een essentiële gegevens opslag voor buildpacks, drup pels, pakketten en resource groepen. Standaard wordt de NFS-server gebruikt voor de blobarchief van de Cloud controller. Als u Single Point of Failure wilt voor komen, gebruikt u Azure Blob Storage als externe opslag. Bekijk de [Cloud Foundry documentatie](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) voor achtergrond en [opties in de Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL als Cloud Foundry Elastic run time-data base *
Voor Elastic elastische runtime zijn twee belang rijke systeem databases vereist:
#### <a name="ccdb"></a>CCDB 
De data base van de Cloud controller.  Cloud controller biedt REST API-eind punten voor clients toegang tot het systeem. CCDB slaat tabellen op voor organisaties, Spaces, services, gebruikers rollen en meer voor de Cloud controller.
#### <a name="uaadb"></a>UAADB 
De Data Base voor gebruikers account en-verificatie. De gegevens van de gebruikers verificatie worden opgeslagen, zoals versleutelde gebruikers namen en wacht woorden.

Standaard kan een lokale systeem database (MySQL) worden gebruikt. Voor HA en om te schalen, maakt gebruik van Azure beheerde MySQL-of PostgreSQL-Services. Hier volgt de instructie voor het [inschakelen van Azure MySQL/PostgreSQL voor ccdb, UAADB en andere systeem databases met Open-Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Open Service Broker
Azure Service Broker biedt een consistente interface voor het beheren van de toegang tot Azure-Services van de toepassing. Het nieuwe [Open Service Broker voor Azure-project](https://github.com/Azure/open-service-broker-azure) biedt een enkele en eenvoudige manier om services te leveren aan toepassingen in Cloud Foundry, open Shift en Kubernetes. Zie de [tegel Azure Open Service Broker voor PCF voor](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) implementatie-instructies op pcf.

## <a name="6-metrics-and-logging"></a>6. metrische gegevens en logboek registratie
De Azure Log Analytics-spuit stuk is een Cloud Foundry onderdeel dat metrische gegevens van de [Cloud Foundry loggregator-firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) doorstuurt naar [Azure monitor-logboeken](https://azure.microsoft.com/services/log-analytics/). Met de spuit stuk kunt u uw CF-systeem status-en prestatie gegevens over meerdere implementaties verzamelen, bekijken en analyseren.
Klik [hier](./cloudfoundry-oms-nozzle.md) voor meer informatie over het implementeren van de Azure log Analytics-spuit op open source-en pivot-Cloud Foundry omgeving en vervolgens toegang tot de gegevens vanuit de console van Azure monitor Logboeken. 
> [!NOTE]
> Van PCF 2,0 worden standaard metrische gegevens van BOSH-status voor virtuele machines doorgestuurd naar de Loggregator firehose en geïntegreerd in Azure Monitor logs console.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. kosten besparen
### <a name="cost-saving-for-devtest-environments"></a>Kosten besparen voor ontwikkel-en test omgevingen
#### <a name="b-series-"></a>B-serie: *
Hoewel de serie F en D VM meestal wordt aanbevolen voor de productie omgeving van de draai Cloud Foundry, worden in de nieuwe ' bevolle ' [B-serie](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) nieuwe opties geleverd. De beproefde virtuele machines uit de B-serie zijn ideaal voor werk belastingen die de volledige prestaties van de CPU niet continu nodig hebben, zoals webservers, kleine data bases en ontwikkel-en test omgevingen. Deze werk belastingen hebben doorgaans een burstieve prestatie vereisten. De waarde is $0.012/Hour (B1) in vergelijking tot $0,05/Hour (F1), zie de volledige lijst met [VM-grootten](../virtual-machines/sizes-general.md) en- [prijzen](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) voor meer informatie. 
#### <a name="managed-standard-disk"></a>Beheerde standaard schijf: 
Premium-schijven werden aanbevolen voor betrouw bare prestaties in productie.  Met [beheerde schijf](https://azure.microsoft.com/services/managed-disks/)kan standaard opslag een vergelijk bare betrouw baarheid bieden, met verschillende prestaties. Voor werk belastingen die geen prestatie gevoelig zijn, zoals ontwikkel-en test-of niet-kritieke omgevingen, bieden beheerde standaard schijven een alternatieve optie met lagere kosten.  
### <a name="cost-saving-in-general"></a>Kosten besparing in het algemeen 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Aanzienlijke kosten voor het opslaan van virtuele machines met Azure-reserve ringen: 
Op dit moment worden alle CF-Vm's gefactureerd op basis van prijzen op aanvraag, ook al blijven de omgevingen voor onbepaalde tijd in de praktijk. U kunt nu de VM-capaciteit reserveren op een termijn van 1 of 3 jaar en korting van 45-65% krijgen. Kortingen worden toegepast in het facturerings systeem, zonder wijzigingen in uw omgeving. Zie [hoe Azure-reserve ringen werkt](https://azure.microsoft.com/pricing/reserved-vm-instances/)voor meer informatie. 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Beheerde Premium-schijf met kleinere grootten: 
Managed disks ondersteunen kleinere schijf grootten, bijvoorbeeld P4 (32 GB) en P6 (64 GB) voor de Premium-en Standard-schijven. Als u kleine werk belastingen hebt, kunt u kosten besparen bij het migreren van Standard Premium-schijven naar beheerde Premium-schijven.
#### <a name="use-azure-first-party-services"></a>Services van Azure First Party gebruiken: 
Door gebruik te maken van de eerste partij service van Azure worden de beheer kosten voor de lange termijn verlaagd, naast HA en betrouw baarheid vermeld in de bovenstaande secties. 

Pivotal heeft een [klein footprintd ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) voor PCF-klanten gelanceerd, de onderdelen worden in slechts vier virtuele machines geplaatst, met maxi maal 2500 toepassings exemplaren. De evaluatie versie is nu beschikbaar via de [Azure-markt plaats](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Volgende stappen
Azure-integratie functies zijn eerst beschikbaar met [Open-Source Cloud Foundry](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), voordat deze beschikbaar zijn in de draai Cloud Foundry. Functies die zijn gemarkeerd met * zijn nog steeds niet beschikbaar via PCF. Cloud Foundry integratie met Azure Stack niet in dit document opgenomen.
Voor PCF-ondersteuning voor de functies die zijn gemarkeerd met *, of Cloud Foundry integratie met Azure Stack, neemt u contact op met uw Pivot-en Microsoft-account manager voor de meest recente status.