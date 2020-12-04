---
title: Opmerkingen bij de release van Azure Data Box Gateway & Azure Data Box Edge 1906 | Microsoft Docs
description: Hierin worden essentiële openstaande problemen en oplossingen voor de Azure Data Box Gateway en Azure Data Box Edge uitgevoerd 1906-versie beschreven.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 51f4995ffd6a86022d95df15ae0eb7694d878c60
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582222"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Release opmerkingen bij Azure Data Box Edge en Azure Data Box Gateway 1906

De volgende opmerkingen bij de release identificeren de kritieke openstaande problemen en de opgeloste problemen voor de 1906-release voor Azure Data Box Edge en Azure Data Box Gateway. 

De release opmerkingen worden voortdurend bijgewerkt en als er kritieke problemen worden gedetecteerd die een tijdelijke oplossing vereisen, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw Data Box Edge/Data Box Gateway implementeert.

Deze release komt overeen met de software versies:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> Update 1906 kan alleen worden toegepast op Data Box Edge apparaten met een algemene Beschik baarheid (GA) of 1905 versie van de software.

## <a name="whats-new"></a>Nieuwe functies

- **Fout oplossing in de werk stroom voor herstel sleutel beheer** : in de eerdere versie is er een fout opgetreden, omdat de herstel sleutel niet werd toegepast. Deze fout is opgelost in deze release. We raden u ten zeerste aan deze update toe te passen als herstel sleutel, zodat u de gegevens op het apparaat kunt herstellen, in het geval dat het apparaat niet wordt opgestart. Zie voor meer informatie hoe u [de herstel sleutel opslaat bij het implementeren van data Box Edge of Data Box gateway](../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- De **FPGA-logboek registratie van de Programmeer bare Gate Array-matrix (in) is verbeterd** : de start van 1905 release, logboek registratie en waarschuwings verbeteringen met betrekking tot FPGA zijn gemaakt. Dit blijft een vereiste update voor Data Box Edge als u de functie Edge Compute gebruikt met de FPGA. Zie [gegevens transformeren met Edge Compute op uw data Box Edge](../databox-online/azure-stack-edge-deploy-configure-compute-advanced.md)voor meer informatie.

## <a name="known-issues-in-ga-release"></a>Bekende problemen in GA release

Er worden geen nieuwe problemen vermeld voor deze release. Alle vermelde release problemen zijn overgenomen uit de vorige releases. Als u een lijst met bekende problemen wilt zien, gaat u naar [bekende problemen in de Ga-release](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Volgende stappen

- [Voorbereidingen voor de implementatie van Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Voorbereidingen voor de implementatie van Azure Data Box Edge](../databox-online/azure-stack-edge-deploy-prep.md)
