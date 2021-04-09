---
title: Onboarding-vereisten voor SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Onboarding-vereisten voor SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16608facab27fc18924cf3a5c6da7e35db048f76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675612"
---
# <a name="onboarding-requirements"></a>Voorbereidingsvereisten

In deze lijst worden de vereisten voor het uitvoeren van SAP HANA op Azure geassembleerd (grotere exemplaren).

**Microsoft Azure**

- Een Azure-abonnement dat kan worden gekoppeld aan SAP HANA op Azure (grote exemplaren).
- Micro soft Premier Support-contract. Voor specifieke informatie met betrekking tot het uitvoeren van SAP in azure raadpleegt u [SAP-ondersteunings opmerking #2015553 – SAP op Microsoft Azure: vereisten voor ondersteuning](https://launchpad.support.sap.com/#/notes/2015553). Als u gebruik maakt van HANA grote instantie-eenheden met 384 en meer Cpu's, moet u ook het Premier-ondersteunings contract uitbreiden om Azure Rapid Response toe te voegen.
- U moet rekening houden met de HANA-Sku's voor grote instanties die u nodig hebt nadat u een schaal oefening met SAP hebt uitgevoerd.

**Netwerkverbinding**

- ExpressRoute tussen on-premises naar Azure: om uw on-premises Data Center te verbinden met Azure, moet u ervoor zorgen dat u Mini maal een 1 Gbps-verbinding van uw Internet provider kunt best Ellen. Connectiviteit tussen HANA grote exemplaar eenheden en Azure gebruikt ook ExpressRoute-technologie. Deze ExpressRoute-verbinding tussen de HANA grote exemplaar eenheden en Azure is inbegrepen in de prijs van de HANA grote instantie-eenheden, inclusief alle gegevens binnenkomend en uitgaand verkeer voor dit specifieke ExpressRoute-circuit. Daarom bieden u als klant geen extra kosten meer dan uw ExpressRoute-koppeling tussen on-premises en Azure.

**Besturingssysteem**

- Licenties voor SUSE Linux Enterprise Server 12 voor SAP-toepassingen.

   > [!NOTE] 
   > Het door micro soft geleverde besturings systeem is niet geregistreerd bij SUSE. Deze is niet verbonden met een exemplaar van het abonnements beheer programma.

- Het hulp programma SUSE Linux-abonnements beheer dat is geïmplementeerd in azure op een VM. Dit hulp programma biedt de mogelijkheid om SAP HANA op Azure (grote instanties) te registreren en respectievelijk te worden bijgewerkt door SUSE. (Er is geen Internet toegang in de HANA-data centrum voor grote instanties.) 
- Licenties voor Red Hat Enterprise Linux 6,7 of 7. x voor SAP HANA.

   > [!NOTE]
   > Het door micro soft geleverde besturings systeem is niet geregistreerd bij Red Hat. Het is niet verbonden met een exemplaar van Red Hat Subscription Manager.

- Red Hat Subscription Manager geïmplementeerd in azure op een virtuele machine. Red Hat Subscription Manager biedt de mogelijkheid om SAP HANA op Azure (grote instanties) te registreren en respectievelijk bijgewerkt te worden door Red Hat. (Er is geen directe toegang tot internet vanuit de Tenant die is geïmplementeerd op de stempel van het grote Azure-exemplaar.)
- Voor SAP moet u ook een ondersteunings contract met uw Linux-provider hebben. Deze vereiste wordt niet verwijderd door de oplossing van HANA grote instanties of het feit dat u Linux in azure uitvoert. In tegens telling tot een aantal installatie kopieën van de Linux Azure-galerie, zijn de service kosten *niet* opgenomen in de oplossings aanbieding van Hana grote instanties. Het is uw verantwoordelijkheid om te voldoen aan de vereisten van SAP met betrekking tot ondersteunings contracten met de Linux-distributeur. 
   - Voor SUSE Linux zoekt u de vereisten van de ondersteunings contracten in [SAP note #1984787-SuSE Linux Enterprise Server 12: installatie notities](https://launchpad.support.sap.com/#/notes/1984787) en [SAP-Opmerking #1056161-SuSE-prioriteits ondersteuning voor SAP-toepassingen](https://launchpad.support.sap.com/#/notes/1056161).
   - Voor Red Hat Linux moet u de juiste abonnements niveaus hebben die ondersteuning en service-updates bevatten voor de besturings systemen van HANA grote instanties. Red Hat adviseert het Red Hat Enterprise Linux-abonnement voor SAP-oplossing. Raadpleegt u https://access.redhat.com/solutions/3082481 . 

Zie [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)voor de ondersteunings matrix van de verschillende SAP Hana versies met de verschillende Linux-versies.

Raadpleeg voor de compatibiliteits matrix van het besturings systeem en///versie van de firmware/Stuur Programma's de [OS-upgrade voor HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Voor het type II-eenheden wordt op dit moment alleen de SLES 12 SP2-besturingssysteem versie ondersteund. 


**Database**

- Licenties en software-installatie onderdelen voor SAP HANA (platform of ENTER prise Edition).

**Toepassingen**

- Licenties en software-installatie onderdelen voor SAP-toepassingen die verbinding maken met SAP HANA en gerelateerde SAP-ondersteunings contracten.
- Licenties en software-installatie onderdelen voor niet-SAP-toepassingen die worden gebruikt met SAP HANA op Azure-omgevingen (grote exemplaren) en gerelateerde ondersteunings contracten.

**Vaardigheden**

- Ervaring met en kennis van Azure IaaS en de bijbehorende onderdelen.
- Ervaring met en kennis van het implementeren van een SAP-werk belasting in Azure.
- SAP HANA installatie Certified Personal.
- SAP architect-vaardig heden om hoge Beschik baarheid en herstel na nood gevallen te ontwerpen rond SAP HANA.

**SAP**

- Verwachting is dat u een SAP-klant bent en een ondersteunings contract met SAP hebt.
- Met name voor implementaties van de klasse type II van HANA grote instanties, raadpleegt u met SAP op versies van SAP HANA en de uiteindelijke configuraties voor grootschalige, uitbreid bare hardware.

**Volgende stappen**
- Raadpleeg de [architectuur van SAP Hana (grote instanties) op Azure](hana-architecture.md)