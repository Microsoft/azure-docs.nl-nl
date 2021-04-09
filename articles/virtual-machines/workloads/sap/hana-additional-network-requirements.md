---
title: Aanvullende netwerk vereisten voor SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Aanvullende netwerk vereisten voor SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83a695792e78916bf79f4d4cf1394ae197c25fb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101671869"
---
# <a name="additional-network-requirements-for-large-instances"></a>Aanvullende netwerk vereisten voor grote instanties

Mogelijk hebt u extra netwerk vereisten als onderdeel van een implementatie van grote exemplaren van SAP HANA op Azure.

## <a name="add-more-ip-addresses-or-subnets"></a>Meer IP-adressen of subnetten toevoegen

Gebruik de Azure Portal, Power shell of de Azure CLI wanneer u meer IP-adressen of subnets toevoegt.

Voeg het nieuwe IP-adres bereik als een nieuw bereik toe aan de adres ruimte van het virtuele netwerk in plaats van een nieuw samengevoegd bereik te genereren. Deze wijziging verzenden naar micro soft. Hierdoor kunt u verbinding maken vanaf het nieuwe IP-adres bereik tot de HANA grote instantie-eenheden in uw client. U kunt een ondersteunings aanvraag voor Azure openen om de nieuwe adres ruimte van het virtuele netwerk toe te voegen. Nadat u een bevestiging hebt ontvangen, voert u de volgende stappen uit.

Zie [een virtueel netwerk maken met behulp van de Azure Portal](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)om een extra subnet te maken op basis van de Azure Portal. Zie [een virtueel netwerk maken met Power shell](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)voor informatie over het maken van een van Power shell.

## <a name="add-virtual-networks"></a>Virtuele netwerken toevoegen

Nadat u eerst een of meer virtuele Azure-netwerken hebt verbonden, wilt u mogelijk extra verbinding maken met SAP HANA op Azure (grote exemplaren). Dien eerst een ondersteunings aanvraag voor Azure in. Neem in die aanvraag de specifieke gegevens op die de specifieke Azure-implementatie identificeren. Neem ook het bereik van de IP-adres ruimte of de bereiken van de adres ruimte van het virtuele Azure-netwerk op. SAP HANA op Service beheer van micro soft biedt vervolgens de benodigde informatie die u nodig hebt om verbinding te maken met de extra virtuele netwerken en Azure ExpressRoute. Voor elk virtueel netwerk hebt u een unieke autorisatie sleutel nodig om verbinding te maken met het ExpressRoute-circuit naar HANA grote instanties.

## <a name="increase-expressroute-circuit-bandwidth"></a>Band breedte van het ExpressRoute-circuit verhogen

Neem contact op met SAP HANA op Service beheer van micro soft. Als u de band breedte van de SAP HANA op Azure (grote instanties) ExpressRoute-circuit wilt verg Roten, moet u een Azure-ondersteunings aanvraag maken. (U kunt een verhoging aanvragen voor een band breedte van één circuit tot Maxi maal 10 Gbps.) Nadat de bewerking is voltooid, ontvangt u een melding. u hoeft niets anders te doen om deze hogere snelheid in Azure in te scha kelen.

## <a name="add-an-additional-expressroute-circuit"></a>Een extra ExpressRoute-circuit toevoegen

Neem contact op met SAP HANA op Service beheer van micro soft. Als u wordt geadviseerd een extra ExpressRoute-circuit toe te voegen, maakt u een ondersteunings aanvraag voor Azure (met inbegrip van een aanvraag voor het verkrijgen van autorisatie gegevens om verbinding te maken met het nieuwe circuit). Voordat u de aanvraag doet, moet u de adres ruimte definiëren die wordt gebruikt voor de virtuele netwerken. SAP HANA in micro soft Service Management kan vervolgens autorisatie geven.

Wanneer het nieuwe circuit is gemaakt en de SAP HANA op de configuratie van micro soft Service Management is voltooid, ontvangt u een melding met de informatie die u nodig hebt om door te gaan. U kunt geen Azure Virtual Networks verbinden met dit extra circuit als ze al zijn verbonden met een andere SAP HANA op een Azure (grote instantie) ExpressRoute-circuit in dezelfde Azure-regio.

## <a name="delete-a-subnet"></a>Een subnet verwijderen

Als u een subnet van een virtueel netwerk wilt verwijderen, kunt u de Azure Portal, Power shell of de Azure CLI gebruiken. Als uw IP-adres bereik of adres ruimte van het virtuele netwerk van Azure een geaggregeerd bereik was, is er geen follow-up voor u met micro soft. (Houd er wel rekening mee dat het virtuele netwerk nog bezig is met het door geven van de BGP-route adres ruimte die het verwijderde subnet bevat.) Mogelijk hebt u het adres bereik of de adres ruimte van het virtuele netwerk van Azure gedefinieerd als meerdere IP-adresbereiken, waarvan er een is toegewezen aan het verwijderde subnet. Zorg ervoor dat u dit verwijdert uit de adres ruimte van uw virtuele netwerk. Informeer vervolgens SAP HANA over micro soft-Service beheer om het te verwijderen uit de bereiken die SAP HANA op Azure (grote instanties) kunnen communiceren met.

Zie [een subnet verwijderen](../../../virtual-network/virtual-network-manage-subnet.md#delete-a-subnet)voor meer informatie.

## <a name="delete-a-virtual-network"></a>Een virtueel netwerk verwijderen

Zie [een virtueel netwerk verwijderen](../../../virtual-network/manage-virtual-network.md#delete-a-virtual-network)voor meer informatie.

SAP HANA op Service beheer van micro soft verwijdert de bestaande autorisaties op het SAP HANA op Azure (grote instanties) ExpressRoute-circuit. Ook wordt het IP-adres bereik of de adres ruimte van het virtuele netwerk van Azure voor de communicatie met HANA grote instanties verwijderd.

Nadat u het virtuele netwerk hebt verwijderd, opent u een ondersteunings aanvraag voor Azure om het bereik of de bereiken voor de IP-adres ruimte die moeten worden verwijderd, op te geven.

Als u alles wilt verwijderen, verwijdert u de ExpressRoute-verbinding, de virtuele netwerk gateway, het open bare IP-adres van de virtuele netwerk gateway en het virtuele netwerk.

## <a name="delete-an-expressroute-circuit"></a>Een ExpressRoute-circuit verwijderen

Als u een extra SAP HANA op Azure (grote instanties) ExpressRoute-circuit wilt verwijderen, opent u een ondersteunings aanvraag voor Azure met SAP HANA in micro soft Service Management. Vraag of het circuit moet worden verwijderd. Binnen het Azure-abonnement kunt u het virtuele netwerk indien nodig verwijderen of hand haven. U moet echter wel de verbinding tussen het ExpressRoute-circuit van de HANA-grote instanties en de gekoppelde virtuele netwerk gateway verwijderen.

## <a name="next-steps"></a>Volgende stappen

- [SAP HANA (Large Instances) installeren en configureren in Azure](hana-installation.md)
