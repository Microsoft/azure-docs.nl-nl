---
title: Gegevens lagen en uitbrei dingen voor het SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Gegevenslaagingen en extensie knooppunten voor SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb780d10996cb22f5e6fe5bc8889e897e8c3854d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666763"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>SAP HANA gegevenslaagings-en uitbrei ding knooppunten gebruiken

SAP ondersteunt een gegevenslaag model voor SAP BW van verschillende versies van SAP NetWeaver en SAP BW/4HANA. Zie voor meer informatie over het gegevenslaag model het SAP [-document SAP BW/4HANA en SAP BW op Hana met SAP Hana extensie knooppunten](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Met HANA grote instanties kunt u de optie-1 configuratie van SAP HANA extensie knooppunten gebruiken, zoals wordt uitgelegd in de veelgestelde vragen en SAP-Blog documenten. De opties voor optie 2 kunnen worden ingesteld met de volgende HANA-Sku's voor grote instanties: S72m, S192, S192m, S384 en S384m. 

Wanneer u de documentatie bekijkt, is het voor deel mogelijk niet direct zichtbaar. Maar wanneer u de SAP-formaat richtlijnen bekijkt, kunt u een voor deel zien met behulp van optie-1 en optie-2 SAP HANA extensie knooppunten. Hier volgen enkele voorbeelden:

- Voor SAP HANA aanpassings richtlijnen is doorgaans dubbele hoeveelheid gegevens volume als geheugen vereist. Wanneer u uw SAP HANA-exemplaar met de warme gegevens uitvoert, hebt u slechts 50 procent of minder geheugen gevuld met gegevens. De rest van het geheugen wordt in het ideale geval opgeslagen SAP HANA de werkzaamheden uit te voeren.
- Dit betekent dat in een HANA grote instantie S192-eenheid met 2 TB geheugen, een SAP BW-data base wordt uitgevoerd, u slechts 1 TB als gegevens volume hebt.
- Als u een extra SAP HANA extensie knooppunt van optie-1 gebruikt, ook een S192 HANA-SKU voor grote instanties, hebt u een extra capaciteit van 2 TB voor gegevens volume. In de optie-2-configuratie krijgt u een extra 4 TB voor het warme gegevens volume. Vergeleken met het hete knoop punt, kan de volledige geheugen capaciteit van het ' warme ' uitbreidings knooppunt worden gebruikt voor het opslaan van gegevens voor optie-1. Dubbel het geheugen kan worden gebruikt voor gegevens volume in optie-2 SAP HANA extensie knooppunt configuratie.
- U hebt een capaciteit van 3 TB voor uw gegevens en een hot-to-warme verhouding van 1:2 voor optie-1. U hebt 5 TB aan gegevens en een verhouding van 1:4 met de optie-2 extensie knooppunt configuratie.

Hoe hoger het gegevens volume ten opzichte van het geheugen is, hoe hoger de kans is dat de warme gegevens die u vraagt, worden opgeslagen op schijf opslag.

**Volgende stappen**
- Raadpleeg de [architectuur van SAP Hana (grote instanties) op Azure](hana-architecture.md)