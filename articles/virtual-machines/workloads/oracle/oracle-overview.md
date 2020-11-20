---
title: Oracle-oplossingen op Microsoft Azure | Microsoft Docs
description: Meer informatie over opties voor het implementeren van Oracle-toepassingen en-oplossingen op Microsoft Azure, waaronder het uitvoeren van volledig op Azure-infra structuur of het gebruik van verbinding tussen de Cloud met Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: dbakevlar
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/05/2020
ms.author: kegorman
ms.openlocfilehash: d9b032bbd89f8b6d300650ac54567b07ae3307b8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94948388"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Overzicht van Oracle-toepassingen en-oplossingen op Azure

Dit artikel bevat een inleiding tot het uitvoeren van Oracle-oplossingen met behulp van Azure-infra structuur. Zie ook gedetailleerde inleiding tot beschik bare [WebLogic Server Azure-toepassingen](oracle-weblogic.md), [Oracle-VM-installatie kopieën](oracle-vm-solutions.md) in azure Marketplace en de mogelijkheid om [Azure te verbinden met Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-data bases in azure-infra structuur

Voer Oracle-data bases uit op de Azure-infra structuur met Oracle Database op Oracle Linux installatie kopieën die beschikbaar zijn in azure Marketplace:

* Oracle Database 12,1, 12,2 en 18,3 Enter prise Edition 

* Oracle Database 12,1, 12,2 en 18,3 Standard Edition

* Oracle Database 19,3

U kunt er ook voor kiezen om Oracle Database in te stellen op een niet-Oracle Linux installatie kopie die beschikbaar is in azure, een oplossing baseren op een aangepaste installatie kopie die u helemaal zelf maakt in azure of een aangepaste installatie kopie uit uw on-premises omgeving kunt uploaden.

Optioneel configureren met meerdere gekoppelde schijven en de database prestaties verbeteren door het installeren van Oracle Automated Storage Management (ASM).

## <a name="weblogic-server-with-azure-service-integrations"></a>WebLogic-Server met Azure-service-integraties

Kies uit een verscheidenheid aan WebLogic Server Azure-toepassingen om uw Cloud traject te versnellen.  Er zijn verschillende vooraf geconfigureerde Azure-service-integraties beschikbaar, waaronder data base, Azure-app gateway en Azure Active Directory.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Toepassingen op Oracle Linux en WebLogic-Server

Voer bedrijfs toepassingen uit in azure op ondersteunde Oracle Linux-installatie kopieën. De volgende installatie kopieën voor virtuele machines zijn beschikbaar in azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux met de onbreekbare Enter prise kernel (UEK) 6,8, 6,9, 6,10, 7,3 tot 7,7, 8,0, 8,1. 

## <a name="high-availability-and-disaster-recovery-options"></a>Opties voor hoge Beschik baarheid en herstel na nood gevallen

* Configureer [Oracle Data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [Active Data Guard met FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) of [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) op Azure Infrastructure in combi natie met [Beschikbaarheidszones](../../../availability-zones/az-overview.md) voor hoge Beschik baarheid in de regio. U kunt deze configuraties ook instellen in meerdere Azure-regio's voor extra Beschik baarheid en herstel na nood gevallen.

* Gebruik [Azure site Recovery](../../../site-recovery/site-recovery-overview.md) om herstel na nood gevallen voor uw Oracle Linux Vm's in Azure en uw fysieke servers te organiseren en te beheren. 

* Gebruik [Azure VMware-oplossing](../../../vmware-cloudsimple/oracle-real-application-clusters.md) of [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)om Oracle Real Application Clusters (RAC) in Azure in te scha kelen.

## <a name="backup-oracle-workloads"></a>Back-ups van Oracle-workloads

* Maak een back-up van uw Oracle-Vm's met [Azure backup](../../../backup/backup-overview.md)

* Maak een back-up van uw Oracle Database met behulp van Oracle RMAN en gebruik eventueel [Azure Blob-zekering](../../../storage/blobs/storage-how-to-mount-container-linux.md) om een [uiterst redundant Azure Blob Storage-account](../../../storage/common/storage-redundancy.md) te koppelen en uw rman-back-ups naar het te schrijven voor toegevoegde tolerantie.

## <a name="integration-of-azure-with-oci"></a>Integratie van Azure met OCI

Voer Oracle-toepassingen uit in de Azure-infra structuur, die zijn verbonden met back-end-data bases in Oracle Cloud Infrastructure (OCI). Deze oplossing maakt gebruik van de volgende mogelijkheden: 

* **Cross-Cloud netwerken** : gebruik de direct-verbinding tussen Azure ExpressRoute en Oracle FastConnect om verbindingen met hoge band breedte, privé en lage latentie tot stand te brengen tussen de toepassing en de data base-laag.
* **Geïntegreerde identiteit** : Stel federatieve identiteit in tussen Azure AD en Oracle IDCS om één identiteits bron te maken voor de oplossingen. Schakel eenmalige aanmelding in om resources te beheren in OCI en Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Oracle-toepassingen implementeren in azure

Gebruik terraform-sjablonen om een Azure-infra structuur in te stellen en Oracle-toepassingen te installeren. 

Oracle heeft deze toepassingen gecertificeerd om uit te voeren in azure bij het maken van verbinding met een Oracle-data base via de Azure/Oracle-oplossing voor verbinding met Cloud:

* E-Business Suite
* JD Edwards EnterpriseOne
* People
* Retail toepassingen van Oracle
* Financieel beheer van Oracle Hyperion

Implementeer ook aangepaste toepassingen in azure die verbinding maken met OCI en andere Azure-Services.

### <a name="set-up-oracle-databases-in-oci"></a>Oracle-data bases in OCI instellen

Gebruik Oracle Database Cloud Services (autonome data base, RAC, Exadata, DBaaS, één knoop punt) in combi natie met Oracle-software die wordt uitgevoerd in Azure. Meer informatie over [Opties voor OCI-data base](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licentieverlening

De implementatie van Oracle-toepassingen in Azure is gebaseerd op een model voor het maken van een eigen licentie. Er wordt ervan uitgegaan dat u beschikt over een juiste licentie voor het gebruik van Oracle-software en dat er een actuele ondersteunings overeenkomst is met Oracle. Oracle heeft de mobiliteit van licenties van on-premises naar Azure gegarandeerd. Raadpleeg de [Veelgestelde vragen over](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)Oracle-Azure.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [WebLogic Server Azure-toepassingen](oracle-weblogic.md) en de Azure service-integraties die worden ondersteund.

* Meer informatie over het implementeren van [Oracle-VM-installatie kopieën](oracle-vm-solutions.md) in de Azure-infra structuur.

* Meer informatie over het [verbinden van Azure met OCI](oracle-oci-overview.md).

* Bekijk de [overzichts sessie van Oracle op Azure](https://myignite.techcommunity.microsoft.com/sessions/82915) vanuit Ignite 2019. 
