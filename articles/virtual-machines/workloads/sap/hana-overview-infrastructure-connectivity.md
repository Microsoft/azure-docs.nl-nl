---
title: Infra structuur en connectiviteit voor het SAP HANA van Azure (grote exemplaren) | Microsoft Docs
description: Configureer de vereiste connectiviteits infrastructuur om SAP HANA te gebruiken op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4f6f8a057651ce56731c180a2ccbb05e35d8fefc
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967547"
---
# <a name="sap-hana-large-instances-deployment"></a>Implementatie van SAP HANA (grote instanties) 

In dit artikel wordt ervan uitgegaan dat u uw aankoop van SAP HANA op Azure (grote exemplaren) van micro soft hebt voltooid. Zie voor algemene achtergrond de [reguliere voor waarden van Hana-exemplaren](hana-know-terms.md) en [Hana grote instanties sku's](hana-available-skus.md)voordat u dit artikel leest.


Micro soft vereist de volgende informatie voor de implementatie van HANA grote instantie-eenheden:

- De naam van de klant.
- Zakelijke contact gegevens (inclusief e-mail adres en telefoon nummer).
- Technische contact gegevens (inclusief e-mail adres en telefoon nummer).
- Contact gegevens van technische netwerken (inclusief e-mail adres en telefoon nummer).
- Azure-implementatie regio (bijvoorbeeld VS-West, Australië-oost of Europa-noord).
- SAP HANA op Azure (grote exemplaren) SKU (configuratie).
- Voor elke Azure-implementatie regio:
    - Een/29 IP-adres bereik voor er-P2P-verbindingen waarmee virtuele Azure-netwerken worden verbonden met HANA grote instanties.
    - Een/24 CIDR-blok dat wordt gebruikt voor de IP-adres groep van de HANA-server voor grote instanties.
    - Optioneel wanneer [ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) gebruikt om directe route ring van on-premises naar Hana grote instantie-eenheden of route ring tussen Hana grote instantie-eenheden in verschillende Azure-regio's in te scha kelen, moet u een ander/29 IP-adres bereik reserveren. Dit specifieke bereik mag niet overlappen met een van de andere IP-adresbereiken die u eerder hebt gedefinieerd.
- De IP-adresbereiken die worden gebruikt in het adres ruimte kenmerk van het virtuele netwerk van elk virtueel netwerk van Azure dat verbinding maakt met de HANA grote instanties.
- Gegevens voor elk HANA-systeem met grote instanties:
  - Gewenste hostnaam, idea liter met een Fully Qualified Domain Name.
  - Gewenst IP-adres voor de HANA-eenheid voor grote instanties buiten het adres bereik van de server-IP-pool. (De eerste 30 IP-adressen in het adres bereik van de server-IP-groep zijn gereserveerd voor intern gebruik binnen HANA grote instanties.)
  - SAP HANA SID-naam voor de SAP HANA-instantie (vereist om de benodigde SAP HANA-gerelateerde schijf volumes te maken). Micro soft heeft de HANA-SID nodig voor het maken van de machtigingen voor sidadm op de NFS-volumes. Deze volumes worden gekoppeld aan de HANA-eenheid voor grote exemplaren. De HANA SID wordt ook gebruikt als een van de naam onderdelen van de schijf volumes die aan de gekoppeld zijn. Als u meer dan één HANA-exemplaar op de eenheid wilt uitvoeren, moet u meerdere HANA-Sid's vermelden. Elk hiervan krijgt een afzonderlijke set toegewezen volumes.
  - In het Linux-besturings systeem heeft de gebruiker sidadm een groeps-ID. Deze ID is vereist om de benodigde SAP HANA-gerelateerde schijf volumes te maken. De SAP HANA-installatie maakt doorgaans de groep sapsys, met een groeps-ID van 1001. De sidadm-gebruiker maakt deel uit van deze groep.
  - In het Linux-besturings systeem heeft de sidadm-gebruiker een gebruikers-ID. Deze ID is vereist om de benodigde SAP HANA-gerelateerde schijf volumes te maken. Als u verschillende HANA-exemplaren op de eenheid uitvoert, worden alle sidadm-gebruikers weer geven. 
- De Azure-abonnements-ID voor het Azure-abonnement waaraan SAP HANA op Azure HANA grote instanties worden gekoppeld, wordt rechtstreeks verbonden. Deze abonnements-ID verwijst naar het Azure-abonnement, dat wordt gefactureerd met de HANA-eenheid voor grote instanties of eenheden.

Nadat u de voor gaande informatie hebt verstrekt, kunnen micro soft-bepalingen SAP HANA op Azure (grote exemplaren). Micro soft stuurt u informatie om uw virtuele Azure-netwerken te koppelen aan HANA grote instanties. U kunt ook toegang krijgen tot de HANA grote instantie-eenheden.

Gebruik de volgende reeks om verbinding te maken met de HANA-grote instanties nadat micro soft deze heeft geïmplementeerd:

1. [Virtuele Azure-machines verbinden met HANA grote instanties](hana-connect-azure-vm-large-instances.md)
2. [Een VNet verbinden met HANA grote instanties ExpressRoute](hana-connect-vnet-express-route.md)
3. [Aanvullende netwerk vereisten (optioneel)](hana-additional-network-requirements.md)
