---
title: Schaal bewerkingen-Azure Portal-Azure Database for PostgreSQL-flexibele server
description: In dit artikel wordt beschreven hoe u schaal bewerkingen kunt uitvoeren in Azure Database for PostgreSQL via de Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90936122"
---
# <a name="scale-operations-in-flexible-server"></a>Schaal bewerkingen op een flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

In dit artikel worden de stappen beschreven voor het uitvoeren van schaal bewerkingen voor Compute en opslag. U kunt de reken lagen wijzigen tussen de bebreek bare Sku's voor algemeen gebruik en geoptimaliseerd voor geheugen, inclusief het aantal vCores dat geschikt is om uw toepassing uit te voeren. U kunt uw opslag ook omhoog schalen. Verwachte IOPS worden weer gegeven op basis van de compute-laag, vCores en de opslag capaciteit. De schatting van de kosten wordt ook weer gegeven op basis van uw selectie.

> [!IMPORTANT]
> U kunt de opslag niet omlaag schalen.

## <a name="pre-requisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

-   U moet een Azure Database for PostgreSQL-flexibele server hebben. Dezelfde procedure is ook van toepassing op een flexibele server die is geconfigureerd met zone redundantie.
> [!IMPORTANT]
> Wanneer u een hoge Beschik baarheid hebt geconfigureerd, kunt u geen bebreek bare SKU kiezen. Tijdens de schaal bewerking wordt de stand-by eerste geschaald naar de gewenste grootte, wordt er een failover voor de primaire server uitgevoerd en wordt de primaire geschaald. 

## <a name="scaling-the-compute-tier-and-size"></a>De compute-laag en-grootte schalen

Volg deze stappen om de compute-laag te kiezen.
 
1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server waarvan u de back-up wilt terugzetten.

2.  Klik op **Compute + Storage**.

3.  Er wordt een pagina met de huidige instellingen weer gegeven.
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="berekenings-en opslag weergave":::

4.  U kunt de compute-klasse kiezen tussen de laagst bebreekbaar, algemeen gebruik en geoptimaliseerd voor geheugen.
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="Reken lagen weer geven":::


5.  Als u goed met de standaard vCores en-geheugen grootten werkt, kunt u de volgende stap overs Laan.

6.  Als u het aantal vCores wilt wijzigen, klikt u op de vervolg keuzelijst van de **berekenings grootte** en klikt u op het gewenste aantal VCores/geheugen in de lijst.
    
    - Burstable Compute-laag: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="Burstable Compute":::

    - Compute-laag voor algemeen gebruik: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="Compute voor algemeen"::: gebruik

    - Compute-laag geoptimaliseerd voor geheugen: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="reken kracht geoptimaliseerd voor geheugen":::

7.  Klik op **Opslaan**. 
8.  Er wordt een bevestigings bericht weer gegeven. Klik op **OK** als u wilt door gaan. 
9.  Er wordt een melding over de schaal bewerking uitgevoerd.


## <a name="scaling-storage-size"></a>Opslag grootte schalen

Volg deze stappen om uw opslag grootte te verg Roten.

1.  Kies in het [Azure Portal](https://portal.azure.com/)de flexibele server waarvoor u de opslag ruimte wilt verg Roten.
2.  Klik op **Compute + Storage**.

3.  Er wordt een pagina met de huidige instellingen weer gegeven.
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="Klik op Compute + Storage":::
4.  De **grootte van het veld opslag in GiB** met een schuif balk wordt weer gegeven met de huidige grootte.

5.  Schuif de balk naar de gewenste grootte. Het bijbehorende IOPS-nummer wordt weer gegeven. De IOPS is afhankelijk van de compute-laag en de grootte. De gegevens over de kosten worden ook weer gegeven. 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="opslag omhoog schalen":::

6.  Als u goed bent met de opslag grootte, klikt u op **Opslaan**. 
7.  Er wordt een bevestigings bericht weer gegeven. Klik op **OK** als u wilt door gaan. 
8.  Er wordt een melding over de schaal bewerking uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over [bedrijfs continuïteit](./concepts-business-continuity.md)
-   Meer informatie over [hoge Beschik baarheid](./concepts-high-availability.md)
-   Meer informatie over [back-up en herstel](./concepts-backup-restore.md)
