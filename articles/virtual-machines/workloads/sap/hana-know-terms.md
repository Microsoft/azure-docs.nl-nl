---
title: De voorwaarden van SAP HANA in Azure (Large Instances) | Microsoft Docs
description: De voorwaarden van SAP HANA in Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a243b348c01e6d1297a6a1fe016e3b6bc8d98d47
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719075"
---
# <a name="know-the-terms"></a>Inzicht in de voorwaarden

In de handleiding voor architectuur en technische implementatie worden veel algemene definities gebruikt. Let op de volgende termen en hun betekenis:

- **IaaS:** Infrastructuur als een dienst.
- **PaaS:** Platform as a Service.
- **SaaS:** Software as a Service.
- **SAP-onderdeel:** een afzonderlijke SAP-toepassing, zoals ECC (ERP Central Component), Business Warehouse (BW), Solution Manager of Enterprise Portal (EP). SAP-onderdelen kunnen worden gebaseerd op traditionele ABAP- of Java-technologieën of op een niet-NetWeaver-toepassing, zoals bedrijfsobjecten.
- **SAP-omgeving:** een of meer SAP-onderdelen die logisch zijn gegroepeerd om een bedrijfsfunctie uit te voeren, zoals ontwikkeling, kwaliteitscontrole, training, herstel na noodherstel of productie.
- **SAP-landschap:** verwijst naar de volledige SAP-assets in uw IT-landschap. Het SAP-landschap omvat alle productie- en niet-productieomgevingen.
- **SAP-systeem:** de combinatie van DBMS-laag en toepassingslaag van, bijvoorbeeld een SAP ERP-ontwikkelsysteem, een SAP BW-testsysteem en een SAP CRM-productiesysteem. Azure-implementaties bieden geen ondersteuning voor het delen van deze twee lagen tussen on-premises en Azure. Een SAP-systeem wordt on-premises geïmplementeerd of geïmplementeerd in Azure. U kunt de verschillende systemen van een SAP-landschap implementeren in Azure of on-premises. U kunt bijvoorbeeld de SAP CRM-ontwikkel- en testsystemen implementeren in Azure terwijl u het SAP CRM-productiesysteem on-premises implementeert. Voor SAP HANA in Azure (Large Instances) is het bedoeld dat u de SAP-toepassingslaag van SAP-systemen in VM's host en het bijbehorende SAP HANA-exemplaar op een eenheid in de zegel SAP HANA on Azure (Large Instances).
- **Large Instance-zegel:** een hardware-infrastructuurstack die is SAP HANA door TDI gecertificeerd en toegewezen voor het uitvoeren van SAP HANA-exemplaren in Azure.
- **SAP HANA in Azure (Large Instances):** Officiële naam voor de aanbieding in Azure voor het uitvoeren van HANA-exemplaren in op SAP HANA met TDI gecertificeerde hardware die is geïmplementeerd in groot-exemplaarstempels in verschillende Azure-regio's. De gerelateerde term *HANA Large Instance* staat voor *SAP HANA on Azure (Large Instances)* en wordt veel gebruikt in deze technische implementatiehandleiding.
- **Cross-premises:** beschrijft een scenario waarin VM's worden geïmplementeerd in een Azure-abonnement met site-naar-site-, multi-site- of Azure ExpressRoute-connectiviteit tussen on-premises datacenters en Azure. In algemene Azure-documentatie worden dit soort implementaties ook beschreven als cross-premises scenario's. De reden voor de verbinding is om on-premises domeinen, on-premises Azure Active Directory/OpenLDAP en on-premises DNS uit te breiden naar Azure. Het on-premises landschap wordt uitgebreid naar de Azure-assets van de Azure-abonnementen. Met deze extensie kunnen de VM's deel uitmaken van het on-premises domein. 

   Domeingebruikers van het on-premises domein hebben toegang tot de servers en kunnen services uitvoeren op deze VM's (zoals DBMS-services). Communicatie en naamom oplossing tussen VM's die on-premises en door Azure geïmplementeerde VM's zijn geïmplementeerd, is mogelijk. Dit scenario is een typisch scenario voor de manier waarop de meeste SAP-assets worden geïmplementeerd. Zie Azure VPN Gateway en Create a virtual network with a site-to-site connection by using the Azure Portal [(Een](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) virtueel netwerk met een [site-naar-site-verbinding](../../../vpn-gateway/tutorial-site-to-site-portal.md)maken met behulp van de Azure Portal).
- **Tenant:** een klant die is geïmplementeerd in de HANA Large Instance-zegel wordt geïsoleerd in een *tenant.* Een tenant is geïsoleerd in de netwerk-, opslag- en rekenlaag van andere tenants. Opslag- en rekeneenheden die zijn toegewezen aan de verschillende tenants, kunnen elkaar niet zien of met elkaar communiceren op het ZEGELniveau van het grote HANA-exemplaar. Een klant kan ervoor kiezen om implementaties in verschillende tenants te hebben. Zelfs dan is er geen communicatie tussen tenants op het zegelniveau HANA Large Instance.
- **SKU-categorie:** voor grote HANA-exemplaren worden de volgende twee categorieën SKU's aangeboden:
    - **Type** I-klasse: S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 en S224m
    - **Type** II-klasse: S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm en S960m
- **Zegel:** hiermee definieert u de interne implementatiegrootte van HANA Large Instances van Microsoft. Voordat HANA Large Instance-eenheden kunnen worden geïmplementeerd, moet een HANA Large Instance-stempel die bestaat uit reken-, netwerk- en opslagrekken, worden geïmplementeerd in een datacenterlocatie. Een dergelijke implementatie wordt een HANA Large Instance-zegel genoemd of van Revisie 4 (zie hieronder) op gebruiken we het alternatief van de term **Rij met grote exemplaren**
- **Revisie:** er zijn twee verschillende zegelrevisies voor HANA Large Instance-zegels. Deze verschillen in architectuur en de nabijheid van hosts voor virtuele Azure-machines.
    - Revisie 3 (Rev 3) is het oorspronkelijke ontwerp dat vanaf het midden van 2016 is geïmplementeerd.
    - Revisie 4.2 (Rev 4.2) is een nieuw ontwerp dat de nabijheid van virtuele-machinehosts van Azure dichter bij elkaar brengt. Rev 4.2 biedt een ultra lage netwerklatentie tussen azure-VM's en HANA Large Instance-eenheden. Resources in de Azure Portal worden aangeduid als BareMetal Infrastructure. Klanten hebben toegang tot hun resources als BareMetal-exemplaren vanuit de Azure Portal. 

Er zijn diverse aanvullende resources beschikbaar voor het implementeren van een SAP-workload in de cloud. Als u van plan bent om een implementatie van SAP HANA in Azure uit te voeren, moet u ervaring hebben met de principes van Azure IaaS en de implementatie van SAP-workloads op Azure IaaS. Zie SAP-oplossingen op [virtuele Azure-machines gebruiken](get-started.md) voor meer informatie voordat u verder gaat. 

## <a name="next-steps"></a>Volgende stappen
- Raadpleeg [HLI-certificering.](hana-certification.md)