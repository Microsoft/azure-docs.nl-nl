---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750945"
---
- Deze functie wordt momenteel alleen ondersteund voor Premium-Ssd's.
- U moet de toewijzing van de virtuele machine ongedaan maken of de schijf loskoppelen van een actieve virtuele machine voordat u de laag van de schijf kunt wijzigen.
- De prestatie lagen P60, P70 en P80 kunnen alleen worden gebruikt door schijven die groter zijn dan 4.096 GiB.
- De prestatie-laag van een schijf kan slechts eenmaal per 12 uur worden gedowngraded.

## <a name="change-performance-tier-without-downtime-preview"></a>Prestatie tier zonder downtime wijzigen (preview-versie)

Normaal gesp roken moet u de toewijzing van de virtuele machine ongedaan maken of de schijf loskoppelen om uw prestatie niveau te wijzigen. Maar als u deze preview-functie inschakelt, hoeft u de toewijzing van de virtuele machine niet ongedaan te maken of uw schijf te ontkoppelen om de laag te wijzigen. U kunt zich [hier](https://aka.ms/liveperftiersignup)registreren voor de preview-versie.

De preview heeft de volgende beperkingen:
- Alleen beschikbaar in de EastUS2EUAP-regio.
- Momenteel niet beschikbaar voor gedeelde schijven
- U moet Azure Resource Manager sjablonen met de `2020-12-01` API gebruiken om de prestatie lagen zonder downtime te wijzigen.