---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e96d205ef1a8f94baa3a0cfe6c5127b6cf570e5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80420952"
---
In dit artikel wordt uitgelegd hoe u een VHD van uw lokale machine uploadt naar een door Azure beheerde schijf of een beheerde schijf kopieert naar een andere regio met behulp van AzCopy. Met deze procedure kunt u direct uploaden ook een VHD uploaden naar 32 TiB rechtstreeks naar een beheerde schijf. Op dit moment wordt direct uploaden ondersteund voor standaard schijven, standaard SSD en Premium SSD Managed disks. Deze functie wordt nog niet ondersteund voor Ultra disks.

Als u een back-upoplossing voor IaaS-Vm's in azure levert, kunt u het beste direct uploaden gebruiken om de back-ups van de klant te herstellen naar Managed disks. Wanneer u een VHD uploadt van een externe bron naar Azure, zijn de snelheden afhankelijk van uw lokale band breedte. Wanneer u een virtuele Azure-machine uploadt of kopieert, is uw band breedte hetzelfde als standaard Hdd's.