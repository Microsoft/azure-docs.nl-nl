---
title: bestand opnemen
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: f4e34e850391696506beed9f6f386f85528dff24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92284312"
---
Als u een van de Vm's hebt geselecteerd die vooraf zijn geconfigureerd met een besturings systeem (en optionele extra services), hebt u al een standaard Azure VM-grootte gekozen. Het is de aanbevolen methode om uw oplossing te starten met een vooraf geconfigureerd besturings systeem. Als u echter een besturings systeem hand matig installeert, moet u de grootte van uw primaire VHD in uw VM-installatie kopie aanpassen. Zorg ervoor dat de besturingssysteem schijf groter is dan de limieten voor Linux of Windows.

| Besturingssysteem | VHD-grootte |
| --- | --- |
| Linux | 30 tot 1023 GB |
| Windows | 30 tot 250 GB |
|

De basis Windows-of SQL Server installatie kopieën die als goedgekeurde basis zijn opgenomen, voldoen al aan deze vereisten, dus Wijzig de indeling of de grootte van de VHD niet.

Gegevens schijven kunnen Maxi maal 1 TB groot zijn. Houd er bij het bepalen van de grootte rekening mee dat klanten de grootte van Vhd's binnen een installatie kopie niet kunnen wijzigen op het moment van de implementatie. Virtuele harde schijven van gegevens schijven maken als Vhd's met vaste grootte. Ze moeten ook uitbreidbaar (sparse/Dynamic) zijn. Gegevens schijven kunnen in eerste instantie leeg zijn of gegevens bevatten.