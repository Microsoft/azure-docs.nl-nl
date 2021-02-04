---
title: Wat is subnet delegering in het virtuele Azure-netwerk?
description: Meer informatie over subnet delegering in het virtuele Azure-netwerk
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2020
ms.author: kumud
ms.openlocfilehash: 2801aa6b2e2b72df815b170200587c49ec0bae14
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538989"
---
# <a name="what-is-subnet-delegation"></a>Wat is subnet delegering?

Met subnet delegering kunt u een specifiek subnet aanwijzen voor een Azure PaaS-service van uw keuze die moet worden toegevoegd aan uw virtuele netwerk. Subnet delegering biedt de klant volledige controle over het beheer van de integratie van Azure-Services in hun virtuele netwerken.

Wanneer u een subnet delegeert naar een Azure-service, kunt u met die service enkele basis regels voor het netwerk configureren voor dat subnet, waarmee de Azure-service de instanties op een stabiele manier kan uitvoeren. Als gevolg hiervan kan de Azure-service een of meer voor waarden voor de implementatie tot stand brengen, zoals:
- Implementeer de service in een gedeeld versus toegewezen subnet.
- Voeg aan de service een verzameling beleids regels toe die zijn vereist voor een goede werking van de service.

##  <a name="advantages-of-subnet-delegation"></a>Voor delen van subnet delegering

Het overdragen van een subnet naar specifieke services biedt de volgende voor delen:

- helpt bij het aanwijzen van een subnet voor een of meer Azure-Services en het beheer van de instanties in het subnet conform de vereisten. De eigenaar van het virtuele netwerk kan bijvoorbeeld het volgende definiëren voor een gedelegeerd subnet om beter bronnen en toegang te beheren:
    - netwerk filtering Traffic-beleids regels met netwerk beveiligings groepen.
    - routerings beleid met door de gebruiker gedefinieerde routes.
    - Services worden geïntegreerd met configuraties van service-eind punten.
- helpt bij het injecteren van services voor een betere integratie met het virtuele netwerk door hun vooraf-voor waarden van implementaties te definiëren in de vorm van beleids regels voor netwerk intentie. Dit zorgt ervoor dat acties die van invloed kunnen zijn op de werking van de injected service, kunnen worden geblokkeerd op het tijdstip van de PUT.


## <a name="who-can-delegate"></a>Wie kan delegeren?
Subnet delegering is een oefening dat de virtuele-netwerk eigenaren moeten worden uitgevoerd om een van de subnetten voor een specifieke Azure-service aan te duiden. Met de Azure-service worden de instanties in dit subnet geïmplementeerd voor gebruik door de workloads van de klant.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Gevolgen van het delegeren van subnetten in uw subnet
Elke Azure-service definieert hun eigen implementatie model, waar ze kunnen bepalen welke eigenschappen ze wel of niet ondersteunen in een gedelegeerd subnet voor injectie doeleinden, zoals volgt:
- gedeeld subnet met andere Azure-Services of VM/virtuele-machine schaal sets in hetzelfde subnet, of ondersteunt alleen een toegewezen subnet met alleen instanties van deze service.
- ondersteunt de NSG-koppeling met het overgedragen subnet.
- biedt ondersteuning voor NSG die zijn gekoppeld aan het overgedragen subnet, kunnen ook worden gekoppeld aan een ander subnet.
- Hiermee staat u de koppeling route tabel met het overgedragen subnet toe.
- Hiermee staat u toe dat de route tabel die is gekoppeld aan het gedelegeerde subnet aan een ander subnet is gekoppeld.
- Hiermee wordt het minimum aantal IP-adressen in het gedelegeerde subnet bepaald.
- bepaalt de IP-adres ruimte in het gedelegeerde subnet voor particuliere IP-adres ruimte (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- bepaalt dat de aangepaste DNS-configuratie een Azure DNS vermelding heeft.
- Hiervoor moet delegering worden verwijderd voordat het subnet of het virtuele netwerk kan worden verwijderd.
- kan niet worden gebruikt met een persoonlijk eind punt als het subnet wordt overgedragen.

De geïnjecteerde Services kunnen ook hun eigen beleid als volgt toevoegen:
- **Beveiligings beleid**: het verzamelen van beveiligings regels die vereist zijn om een bepaalde service te kunnen gebruiken.
- **Routerings beleid**: een verzameling routes die vereist zijn voor het werken met een bepaalde service.

## <a name="what-subnet-delegation-does-not-do"></a>Wat is het delegeren van subnetten niet?

De Azure-Services die worden toegevoegd aan een overgedragen subnet, hebben nog steeds de eenvoudige set eigenschappen die beschikbaar zijn voor niet-gemachtigde subnetten, zoals:
-  Met Azure-Services kunnen instanties worden geinjecteerd in subnetten van klanten, maar dit kan niet van invloed zijn op de bestaande werk belastingen.
-  Het beleid of de routes waarop deze services van toepassing zijn, zijn flexibel en kunnen door de klant worden overschreven.

## <a name="next-steps"></a>Volgende stappen

- [Een subnet delegeren](manage-subnet-delegation.md)
