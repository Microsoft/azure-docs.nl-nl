---
title: SQL Server VM uitvoeren op een toegewezen Azure-host
description: Meer informatie over het uitvoeren van een SQL Server-VM op een toegewezen Azure-host.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 957e458bf21eb4a95b1faba1daf0459271648310
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358687"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>SQL Server VM uitvoeren op een toegewezen Azure-host 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel vindt u meer informatie over het gebruik van een SQL Server virtuele machine (VM) met een [toegewezen Azure-host](../../../virtual-machines/dedicated-hosts.md). Meer informatie over de specifieke Azure-host vindt u in het blog bericht Inleiding tot de [toegewezen Azure-host](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Overzicht
De [toegewezen Azure-host](../../../virtual-machines/dedicated-hosts.md) is een service die fysieke servers biedt en een of meer virtuele machines kan hosten die zijn toegewezen aan één Azure-abonnement. Toegewezen hosts zijn dezelfde fysieke servers die worden gebruikt in Data Centers van micro soft, als een resource. U kunt toegewezen hosts inrichten binnen een regio, een beschikbaarheids zone en een fout domein. Vervolgens kunt u Vm's rechtstreeks op uw ingerichte hosts plaatsen, in welke configuratie het beste voldoet aan uw behoeften.

## <a name="limitations"></a>Beperkingen

- Niet alle VM-reeksen worden ondersteund op toegewezen hosts en de beschik baarheid van de VM-reeks varieert per regio. Zie [overzicht van met Azure toegewezen hosts](../../../virtual-machines/dedicated-hosts.md)voor meer informatie.

## <a name="licensing"></a>Licentieverlening

U kunt kiezen tussen twee verschillende licentie opties wanneer u uw SQL Server virtuele machine in een door Azure toegewezen host plaatst. 

  - **SQL VM-licentie verlening**: dit is de bestaande licentie optie, waar u voor elke SQL Server VM-licentie afzonderlijk betaalt. 
  - **Dedicated host Licensing**: het nieuwe licentie model dat beschikbaar is voor de toegewezen Azure-host, waarbij SQL Server licenties worden gebundeld en betaald op hostniveau. 


Opties op hostniveau voor het gebruik van bestaande SQL Server licenties: 
  - SQL Server Enterprise Edition Azure Hybrid Benefit (AHB)
    - Beschikbaar voor klanten met SA of een abonnement.
    - Licentie voor alle beschik bare fysieke kernen en geniet van onbeperkte virtualisatie (Maxi maal het maximum aantal Vcpu's dat door de host wordt ondersteund).
        - Zie [Azure Hybrid Benefit FAQ (Engelstalig](https://azure.microsoft.com/pricing/hybrid-benefit/faq/)) voor meer informatie over het Toep assen van de Ahb op een toegewezen Azure-host. 
  - SQL Server licenties die zijn verkregen vóór 1 oktober
      - SQL Server Enterprise Edition heeft zowel de licentie opties host-niveau als de virtuele machine. 
      - SQL Server Standard Edition heeft alleen een licentie optie per VM beschikbaar. 
          - Zie [product voorwaarden van micro soft](https://www.microsoft.com/licensing/product-licensing/products)voor meer informatie. 
  - Als er geen SQL Server specifieke optie op hostniveau is geselecteerd, kunt u SQL Server AHB selecteren op het niveau van afzonderlijke Vm's, net zoals u dat met Vm's met meerdere tenants zou doen.



## <a name="provisioning"></a>Inrichten  
Het inrichten van een SQL Server-VM naar de toegewezen host is niet anders dan andere virtuele Azure-machines. U kunt dit doen met behulp van [Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md), de [Azure Portal](../../../virtual-machines/dedicated-hosts-portal.md)en de [Azure cli](../../../virtual-machines/linux/dedicated-hosts-cli.md).

Het proces voor het toevoegen van een bestaande SQL Server virtuele machine aan de toegewezen host heeft downtime nodig, maar heeft geen invloed op gegevens en heeft geen gegevens verlies. Voor alle data bases, inclusief systeem databases, moet eerst een back-up worden gemaakt voordat deze kan worden verplaatst.

## <a name="virtualization"></a>Virtualisatie 

Een van de voor delen van een speciale host is een onbeperkte virtualisatie. U kunt bijvoorbeeld licenties hebben voor 64 vCores, maar u kunt de host zo configureren dat deze 128 vCores heeft, zodat u een dubbele vCores krijgt, maar alleen de helft van wat u zou doen voor de SQL Server-licenties. 

Omdat het de host is, komt u in aanmerking voor het instellen van virtualisatie met een verhouding van 1:2. 

## <a name="faq"></a>Veelgestelde vragen

**V: Hoe werkt de Azure Hybrid Benefit voor Windows Server/SQL Server-licenties op een toegewezen Azure-host?**

A: klanten kunnen de waarde van hun bestaande Windows Server-en SQL Server-licenties met Software Assurance gebruiken, of abonnements licenties kwalificeren, om een gereduceerd tarief te betalen op een toegewezen Azure-host met behulp van Azure Hybrid Benefit. Klanten met Windows Server Data Center en SQL Server Enterprise Edition krijgen een onbeperkte virtualisatie (zo veel mogelijk virtuele Windows Server-machines kunnen op de host worden geïmplementeerd op basis van de fysieke capaciteit van de onderliggende server) wanneer ze een licentie voor de hele host hebben en Azure Hybrid Benefit gebruiken.  Alle Windows Server-en SQL Server-workloads in de toegewezen Azure-host komen ook in aanmerking voor uitgebreide beveiligings updates voor Windows Server en SQL Server 2008/R2 zonder extra kosten. 

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](frequently-asked-questions-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](doc-changes-updates-release-notes.md)