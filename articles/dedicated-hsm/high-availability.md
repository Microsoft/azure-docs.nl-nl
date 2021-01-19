---
title: Hoge Beschik baarheid-specifieke HSM voor Azure | Microsoft Docs
description: Meer informatie over de basis overwegingen voor de hoge Beschik baarheid van de speciale HSM van Azure. Dit artikel bevat een voor beeld.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 85aa9d355b2440535ea13c8c15e95500bac22352
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567250"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Hoge Beschik baarheid van exclusieve HSM voor Azure

De specifieke HSM van Azure wordt uitgebreid door de Maxi maal beschik bare data centers van micro soft. Elk Data Center met hoge Beschik baarheid is echter kwetsbaar voor gelokaliseerde storingen en in extreme omstandigheden, regionale niveau fouten. Micro soft implementeert HSM-apparaten in verschillende data centers binnen een regio om ervoor te zorgen dat meerdere apparaten worden ingericht, waardoor niet kan worden bepaald welke apparaten één rek delen. U kunt een hoger niveau voor hoge Beschik baarheid bereiken door deze Hsm's te koppelen aan de data centers in een regio met behulp van de functie Thales HA Group. Het is ook mogelijk om apparaten in verschillende regio's te koppelen om de regionale failover in een nood herstel situatie te verhelpen. Met deze configuratie met hoge Beschik baarheid in meerdere lagen wordt een storing in het apparaat automatisch opgelost om toepassingen te laten werken. Alle data centers hebben ook reserve apparaten en onderdelen op locatie, zodat elk mislukt apparaat tijdig kan worden vervangen.

## <a name="high-availability-example"></a>Voor beeld van hoge Beschik baarheid

Informatie over het configureren van HSM-apparaten voor hoge Beschik baarheid op het software niveau vindt u in de Thales Luna 7 HSM-beheer handleiding. Dit document is beschikbaar op de  [Thales HSM-pagina](https://thalesdocs.com/gphsm/Content/luna/network/luna_network_releases.htm).

Het volgende diagram toont een Maxi maal beschik bare architectuur. Het gebruikt meerdere apparaten in de regio en meerdere apparaten die zijn gekoppeld aan een afzonderlijke regio. Deze architectuur maakt gebruik van Mini maal vier HSM-apparaten en onderdelen van virtuele netwerken.

![Diagram met hoge Beschik baarheid](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Volgende stappen

Het wordt aanbevolen om alle belang rijke concepten van de service, zoals hoge Beschik baarheid en beveiliging, goed te begrijpen vóór het inrichten van apparaten en het ontwerpen of implementeren van toepassingen.
Meer onderwerpen over het concept niveau:

* [Implementatie architectuur](deployment-architecture.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)
* [Ondersteuning](supportability.md)
* [Controle](monitoring.md)

Voor specifieke informatie over het configureren van HSM-apparaten voor maximale Beschik baarheid raadpleegt u de Thales-portal voor klanten ondersteuning voor de beheerders handleidingen en raadpleegt u sectie 6.
