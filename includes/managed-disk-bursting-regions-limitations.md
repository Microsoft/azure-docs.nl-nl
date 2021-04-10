---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102178357"
---
- Bursting op aanvraag kan niet worden ingeschakeld voor een Premium SSD met minder dan 512 GiB. Premium-Ssd's die kleiner zijn dan 512 GiB, gebruiken altijd burstisatie op basis van krediet.
- Bursting op aanvraag wordt alleen ondersteund voor Premium-Ssd's. Als een Premium SSD met on-demand bursting is ingeschakeld, wordt het type schijf bursting uitgeschakeld.
- Bursting op aanvraag wordt niet automatisch uitgeschakeld wanneer de laag van de prestaties wordt gewijzigd. Als u de prestatie laag wilt wijzigen, maar geen schijf burstie wilt gebruiken, moet u deze uitschakelen.
- Bursting op aanvraag kan alleen worden ingeschakeld wanneer de schijf wordt losgekoppeld van een virtuele machine of wanneer de virtuele machine wordt gestopt. Bursting op aanvraag kan 12 uur worden uitgeschakeld nadat deze is ingeschakeld.