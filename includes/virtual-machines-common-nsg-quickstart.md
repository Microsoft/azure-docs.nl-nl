---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/12/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ec6cbcbc93fe87634c87caeb0041b75ec916a22f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176005"
---
U opent een poort of maakt een eind punt naar een virtuele machine (VM) in azure door een netwerk filter te maken op een subnet of een VM-netwerk interface. U plaatst deze filters, waarmee zowel binnenkomend als uitgaand verkeer worden beheerd op een netwerk beveiligings groep die is gekoppeld aan de resource die het verkeer ontvangt.

In het voor beeld in dit artikel ziet u hoe u een netwerk filter maakt dat gebruikmaakt van de standaard TCP-poort 80 (ervan wordt uitgegaan dat u de juiste services al hebt gestart en de firewall regels van het besturings systeem op de virtuele machine hebt geopend).

Nadat u een virtuele machine hebt gemaakt die is geconfigureerd voor webaanvragen op de standaard TCP-poort 80, kunt u het volgende doen:

1. Maak een netwerkbeveiligingsgroep.

2. Maak een regel voor binnenkomende beveiliging die verkeer toestaat en waarden toewijst aan de volgende instellingen:

   - **Poortbereiken van doel**: 80

   - **Poort bereik van bron**: * (staat wille keurige bron poort toe)

   - **Prioriteits waarde**: Voer een waarde in die kleiner is dan 65.500 en hoger dan de standaard regel voor het weigeren van binnenkomende verbindingen.

3. Koppel de netwerk beveiligings groep aan de VM-netwerk interface of het subnet.

Hoewel in dit voor beeld een eenvoudige regel wordt gebruikt om HTTP-verkeer toe te staan, kunt u ook netwerk beveiligings groepen en-regels gebruiken om complexere netwerk configuraties te maken. 




