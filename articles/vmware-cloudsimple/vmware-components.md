---
title: VMware-onderdelen van de privécloud
titleSuffix: Azure VMware Solution by CloudSimple
description: Meer informatie over hoe u met de CloudSimple-service VMware systeem eigen kunt implementeren in azure-locaties. Persoonlijke Clouds zijn geïntegreerd met de rest van de Azure-Cloud.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff9aec1904be754990958869666e9d67038e4fb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88142499"
---
# <a name="private-cloud-vmware-components"></a>VMware-onderdelen van de privécloud

Een Privécloud is een geïsoleerde VMware-stack (ESXi hosts, vCenter, vSAN en NSX) die worden beheerd door een vCenter-Server in een beheer domein.  Met de CloudSimple-service kunt u VMware systeem eigen implementeren op een Azure bare-metal infra structuur in azure-locaties.  Persoonlijke Clouds zijn geïntegreerd met de rest van de Azure-Cloud.  Een privécloud wordt geïmplementeerd met de volgende VMware stack-onderdelen:

* **VMware ESXi-** Hyper Visor op aan Azure toegewezen knoop punten
* **VMware vCenter-** Apparaat voor gecentraliseerd beheer van de vSphere-omgeving in de privécloud
* **VMware-vSAN-** Oplossing voor Hyper-geconvergeerde infra structuur
* **VMware NSX Data Center-** Netwerkvirtualisatie en beveiligings software  

## <a name="vmware-component-versions"></a>Versies van VMware-onderdelen

Een VMware-stack in de Privécloud wordt geïmplementeerd met de volgende software versie.

| Onderdeel | Versie | Versie met licentie |
|-----------|---------|------------------|
| ESXi | 6,7 U2 | Enter prise plus |
| vCenter | 6,7 U2 | vCenter-standaard |
| vSAN | 6.7 | Enterprise |
| NSX Data Center | 2.4.1 | Geavanceerd |

## <a name="esxi"></a>ESXi

VMware ESXi wordt geïnstalleerd op de ingerichte CloudSimple-knoop punten wanneer u een privécloud maakt.  ESXi biedt de Hyper Visor voor het implementeren van virtuele machines van werk belastingen (Vm's).  Knoop punten bieden een Hyper-geconvergeerde infra structuur (reken-en opslag) in uw privécloud.  De knoop punten maken deel uit van het vSphere-cluster op de privécloud.  Elk knoop punt heeft vier fysieke netwerk interfaces die zijn verbonden met het aan-netwerk.  Twee fysieke netwerk interfaces worden gebruikt voor het maken van een **vSphere-gedistribueerde switch (VDS)** in vCenter en twee worden gebruikt voor het maken van een door **NSX beheerde virtuele gedistribueerde switch (N-VDS)**.  Netwerk interfaces worden geconfigureerd in de modus actief-actief voor hoge Beschik baarheid.

Meer informatie over VMware ESXi

## <a name="vcenter-server-appliance"></a>vCenter Server-apparaat

vCenter Server-apparaat (VCSA) biedt de functies voor verificatie, beheer en Orchestration voor VMware-oplossing door CloudSimple. VCSA met de embedded platform Services controller (PSC) wordt geïmplementeerd wanneer u uw privécloud maakt.  VCSA wordt geïmplementeerd op het vSphere-cluster dat wordt gemaakt wanneer u uw privécloud implementeert.  Elke privécloud heeft zijn eigen VCSA.  Uitbrei ding van een privécloud voegt de knoop punten toe aan de VCSA op de privécloud.

### <a name="vcenter-single-sign-on"></a>eenmalige aanmelding via vCenter

De controller van de embedded platform Services op VCSA is gekoppeld aan een **VCenter Single Sign-On-domein**.  De domein naam is **cloudsimple. local**.  Er wordt een standaard gebruiker **CloudOwner@cloudsimple.com** voor toegang tot vCenter gemaakt.  U kunt uw on-premises/Azure Active Directory- [identiteits bronnen voor vCenter](set-vcenter-identity.md)toevoegen.

## <a name="vsan-storage"></a>vSAN-opslag

Persoonlijke Clouds worden gemaakt met volledig geconfigureerde alle Flash vSAN-opslag, lokaal naar het cluster.  Er zijn mini maal drie knoop punten van dezelfde SKU vereist voor het maken van een vSphere-cluster met vSAN-gegevens opslag.  De-duplicatie en compressie zijn standaard ingeschakeld op de vSAN-gegevens opslag.  Op elk knoop punt van het vSphere-cluster worden twee schijf groepen gemaakt. Elke schijf groep bevat één cache schijf en drie capaciteits schijven.

Er wordt een standaard vSAN-opslag beleid gemaakt op het vSphere-cluster en toegepast op het vSAN-gegevens archief.  Dit beleid bepaalt hoe de VM-opslag objecten worden ingericht en toegewezen in de gegevens opslag om het vereiste service niveau te garanderen.  Het opslag beleid definieert de **fouten die moeten worden toegestaan (FTT)** en de **fout tolerantie methode**.  U kunt nieuwe opslag beleidsregels maken en deze Toep assen op de Vm's. Als u SLA wilt onderhouden, moet er een capaciteit van 25% worden behouden op de vSAN-gegevens opslag.  

### <a name="default-vsan-storage-policy"></a>Standaard vSAN-opslag beleid

De volgende tabel bevat de standaard para meters voor vSAN-opslag beleid.

| Aantal knoop punten in het vSphere-cluster | FTT | Fout tolerantie methode |
|------------------------------------|-----|--------------------------|
| 3 en 4 knoop punten | 1 | RAID 1 (spie gelen): maakt 2 kopieën |
| 5 tot 16 knoop punten | 2 | RAID 1 (spie gelen): drie kopieën maken |

## <a name="nsx-data-center"></a>NSX Data Center

NSX Data Center biedt Netwerkvirtualisatie, micro segmentatie en netwerk beveiligings mogelijkheden in uw privécloud.  U kunt alle services die worden ondersteund door NSX Data Center in uw privécloud configureren via NSX.  Wanneer u een privécloud maakt, worden de volgende NSX-onderdelen geïnstalleerd en geconfigureerd.

* NSXT Manager
* Transport zones
* Uplinkpoortprofiel voor host en Edge
* Logische switch voor Edge Trans Port, Ext1 en ext2
* IP-adres groep voor ESXi-transport knooppunt
* IP-adres groep voor het knoop punt Edge-Trans Port
* Edge-knoop punten
* DRS-affiniteits regel voor controller-en Edge-Vm's
* Router op laag 0
* BGP inschakelen op de Tier0-router

## <a name="vsphere-cluster"></a>vSphere-cluster

ESXi-hosts worden geconfigureerd als een cluster om te zorgen voor een hoge Beschik baarheid van de privécloud.  Wanneer u een privécloud maakt, worden de beheer onderdelen van vSphere geïmplementeerd op het eerste cluster.  Er wordt een resource groep gemaakt voor beheer onderdelen en alle management-Vm's worden geïmplementeerd in deze resource groep. Het eerste cluster kan niet worden verwijderd om de privécloud te verkleinen.  vSphere-cluster biedt hoge Beschik baarheid voor Vm's met **VSPHERE ha**.  Te verdragen fouten zijn gebaseerd op het aantal beschik bare knoop punten in het cluster.  U kunt de formule gebruiken ```Number of nodes = 2N+1``` ```N``` om het aantal fouten te verdragen.

### <a name="vsphere-cluster-limits"></a>limieten voor vSphere-clusters

| Resource | Limiet |
|----------|-------|
| Minimum aantal knoop punten voor het maken van een privécloud (eerste vSphere-cluster) | 3 |
| Maximum aantal knoop punten in een vSphere-cluster in een privécloud | 16 |
| Maximum aantal knoop punten in een privécloud | 64 |
| Maximum aantal vSphere-clusters in een privécloud | 21 |
| Minimum aantal knoop punten op een nieuw vSphere-cluster | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware-infrastructuur onderhoud

Het is nu af en toe nood zakelijk om wijzigingen aan te brengen in de configuratie van de VMware-infra structuur. Op dit moment kunnen deze intervallen elke 1-2 maanden optreden, maar wordt de frequentie naar verwachting in de loop van de tijd geweigerd. Dit type onderhoud kan meestal worden uitgevoerd zonder het normale gebruik van de CloudSimple-services te onderbreken. Tijdens een VMware-onderhouds interval blijven de volgende services functioneren zonder enige impact:

* VMware-beheer vlak en-toepassingen
* toegang tot vCenter
* Alle netwerken en opslag
* Al het Azure-verkeer

## <a name="updates-and-upgrades"></a>Updates en upgrades

CloudSimple is verantwoordelijk voor het levenscyclus beheer van VMware-software (ESXi, vCenter, PSC en NSX) in de privécloud.

Software-updates zijn onder andere:

* **Patches**. Beveiligings patches of oplossingen voor oplossingen die worden vrijgegeven door VMware.
* **Updates**. Wijziging van de secundaire versie van een VMware-stack onderdeel.
* **Upgrades**. Wijziging van de hoofd versie van een VMware-stack onderdeel.

CloudSimple test een kritieke beveiligings patch zodra deze beschikbaar is in VMware. Per SLA implementeert CloudSimple de beveiligings patch naar privécloud binnen een week.

CloudSimple biedt updates voor het driemaandelijkse onderhoud aan VMware-software onderdelen. Wanneer er een nieuwe belang rijke versie van VMware-software beschikbaar is, werkt CloudSimple samen met klanten om een geschikt onderhouds venster voor de upgrade te coördineren.  

## <a name="next-steps"></a>Volgende stappen

* [Onderhoud en updates voor CloudSimple](cloudsimple-maintenance-updates.md)
