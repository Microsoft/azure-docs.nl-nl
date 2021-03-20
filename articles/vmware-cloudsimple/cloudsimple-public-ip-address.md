---
title: Azure VMware-oplossing op CloudSimple-openbaar IP-adres
description: Meer informatie over open bare IP-adressen en hun voor delen van Azure VMware-oplossing door CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "77024973"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Overzicht van open bare IP-adressen CloudSimple

Met een openbaar IP-adres kunnen Internet bronnen inkomend communiceren met persoonlijke cloud resources op een privé-IP-adres. Het privé-IP-adres is een virtuele machine of een software load balancer in uw Privécloud. Met het open bare IP-adres kunt u de services die in uw Privécloud worden uitgevoerd, beschikbaar maken op internet.

Het open bare IP-adres is toegewezen aan het privé-IP-adres totdat u de toewijzing ervan ongedaan maakt. Een openbaar IP-adres kan alleen worden toegewezen aan één privé-IP-adres.

Een resource die is gekoppeld aan een openbaar IP-adres maakt altijd gebruik van het open bare IP-adres voor Internet toegang. Standaard is alleen uitgaande internet toegang toegestaan op een openbaar IP-adres.  Binnenkomend verkeer op het open bare IP-adres wordt geweigerd.  Als u inkomend verkeer wilt toestaan, maakt u een firewall regel voor het open bare IP-adres voor de specifieke poort.

## <a name="benefits"></a>Voordelen

Het gebruik van een openbaar IP-adres om inkomende communicatie te geven biedt:

* Preventie van DDoS-aanvallen (Distributed Denial of service). Deze beveiliging wordt automatisch ingeschakeld voor het open bare IP-adres.
* Controle van het verkeer en real-time beperking van veelvoorkomende aanvallen op netwerk niveau. Deze verdedigingen zijn dezelfde verdedigings die worden gebruikt door micro soft onlineservices.
* De volledige schaal van het wereld wijde Azure-netwerk. Het netwerk kan worden gebruikt om het aanvals verkeer tussen regio's te distribueren en te verhelpen.  

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [toewijzen van een openbaar IP-adres](public-ips.md)