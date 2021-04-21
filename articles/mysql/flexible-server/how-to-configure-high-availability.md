---
title: Zone-redundante hoge beschikbaarheid beheren - Azure Portal - Azure Database for MySQL Flexible Server
description: In dit artikel wordt beschreven hoe u zone-redundante hoge beschikbaarheid in of uit Azure Database for MySQL flexibele server via de Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: e217dcaeafd553803f5c9699ab6d7779ed755b67
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818278"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Zone-redundante hoge beschikbaarheid beheren in Azure Database for MySQL Flexible Server (preview)

In dit artikel wordt beschreven hoe u een zone-redundante configuratie met hoge beschikbaarheid in uw flexibele server kunt in- of uitschakelen.

Met de functie voor hoge beschikbaarheid worden fysiek afzonderlijke primaire en stand-byreplica's in verschillende zones gemaakt. Zie de documentatie over concepten [voor hoge beschikbaarheid voor meer informatie.](./concepts/../concepts-high-availability.md) 

> [!IMPORTANT]
> U kunt zone-redundante hoge beschikbaarheid alleen inschakelen tijdens het maken van flexibele servers.

Deze pagina bevat richtlijnen voor het in- of uitschakelen van hoge beschikbaarheid. Deze bewerking wijzigt uw andere instellingen niet, waaronder VNET-configuratie, firewallinstellingen en back-upretentie. Op dezelfde manier is het uitschakelen van hoge beschikbaarheid een onlinebewerking en heeft dit geen invloed op de connectiviteit en bewerkingen van uw toepassing.

> [!IMPORTANT]
> Zone-redundante hoge beschikbaarheid is beschikbaar in een beperkte set regio's: Azië - zuidoost, VS - west 2, Europa - west vs - oost.  

## <a name="enable-high-availability-during-server-creation"></a>Hoge beschikbaarheid inschakelen tijdens het maken van de server

Deze sectie bevat details voor velden met betrekking tot ha. U kunt deze stappen volgen om hoge beschikbaarheid te implementeren tijdens het maken van uw flexibele server.

1.  Kies in [Azure Portal](https://portal.azure.com/)flexibele server en klik op **Maken.**  Zie de documentatie voor het maken van de server voor meer informatie over het invullen van details zoals **Abonnement,** **Resourcegroep,** **Servernaam,** Regio en andere velden.

2.  Klik op het selectievakje voor **Zone-redundante hoge beschikbaarheid** in de optie Beschikbaarheid.

3.  Als u de standaard reken- en opslaginstellingen wilt wijzigen, klikt u op **Server configureren.**

4.  Als de optie voor hoge beschikbaarheid is ingeschakeld, kan de burstable-laag niet worden gekozen. U kunt kiezen voor **de rekenlagen** Algemeen gebruik of **Geoptimaliseerd** voor geheugen.

    > [!IMPORTANT]
    > We ondersteunen alleen zone-redundante hoge beschikbaarheid voor de prijscategorie ***Algemeen** gebruik _ en _ *_Geoptimaliseerd voor_* geheugen * .

5.  Selecteer de **rekenkracht** voor uw keuze in de vervolgkeuzekeuze.

6.  Selecteer **Opslaggrootte** in GiB met behulp van de sliding bar en selecteer **de** bewaarperiode voor back-ups tussen 7 dagen en 35 dagen.   

## <a name="disable-high-availability"></a>Hoge beschikbaarheid uitschakelen

Volg deze stappen om hoge beschikbaarheid uit te schakelen voor uw flexibele server die al is geconfigureerd met zone-redundantie.

1.  Selecteer in [Azure Portal](https://portal.azure.com/)de bestaande Azure Database for MySQL flexibele server.

2.  Klik op de pagina flexibele server op **Hoge beschikbaarheid in** het voorpaneel om de pagina voor hoge beschikbaarheid te openen.

3.  Klik op het **selectievakje zone-redundante hoge beschikbaarheid** om de optie uit te schakelen en klik op **Opslaan** om de wijziging op te slaan.

4.  Er wordt een bevestigingsdialoogvenster weergegeven waarin u kunt bevestigen dat u de ha-ha uit kunt uitschakelen.

5.  Klik **op de knop Hoge beschikbaarheid** uitschakelen om de hoge beschikbaarheid uit te schakelen.

6.  Er wordt een melding weer geven dat de implementatie met hoge beschikbaarheid buiten gebruik wordt gesteld.


## <a name="forced-failover"></a>Geforceerd failover

Volg deze stappen om failover van uw primaire naar stand-by flexibele server af te dwingen

1.  Selecteer in [Azure Portal](https://portal.azure.com/)uw bestaande Azure Database for MySQL flexibele server waarvoor de functie voor hoge beschikbaarheid is ingeschakeld.

2.  Klik op de pagina flexibele server op **Hoge beschikbaarheid in** het voorpaneel om de pagina voor hoge beschikbaarheid te openen.

3.  Controleer de **primaire beschikbaarheidszone** en de **stand-bybeschikbaarheidszone**

4.  Klik op **Geforceerd failover om** de procedure voor handmatige failover te starten. Er wordt een pop-upvenster weergegeven met informatie over de verwachte tijd van de failover, afhankelijk van de huidige werkbelasting op de primaire en de versie van het laatste controlepunt, leest u het bericht en klikt u op OK.
 
5. Er wordt een melding weer geven dat de failover wordt uitgevoerd.

6. Zodra de failover naar de stand-byserver is geslaagd, verschijnt er een melding.

7. Controleer de nieuwe **primaire beschikbaarheidszone en** de **beschikbaarheidszone Stand-by.**

![Geforceerd failoveren](media/how-to-configure-high-availability/how-to-forced-failover.png) 

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over [bedrijfscontinuïteit](./concepts-business-continuity.md)
-   Meer informatie over [zone-redundante hoge beschikbaarheid](./concepts-high-availability.md)
