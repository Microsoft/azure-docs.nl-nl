---
title: Zone redundante hoge Beschik baarheid beheren-Azure Portal-Azure Database for MySQL flexibele server
description: In dit artikel wordt beschreven hoe u via de Azure Portal zone redundante hoge Beschik baarheid kunt in-of uitschakelen in Azure Database for MySQL flexibele server.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: d65b074385311e74444929ef74901e402e29ec03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93241732"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Zone redundante hoge Beschik baarheid beheren in Azure Database for MySQL flexibele server (preview-versie)

In dit artikel wordt beschreven hoe u de redundante configuratie van een zone met hoge Beschik baarheid in-of uitschakelen in uw flexibele server.

Met de functie voor hoge Beschik baarheid wordt een fysiek gescheiden primaire en stand-by replica in verschillende zones ingericht. Zie de [documentatie over concepten met hoge Beschik baarheid](./concepts/../concepts-high-availability.md)voor meer informatie. 

> [!IMPORTANT]
> U kunt zone redundante hoge Beschik baarheid inschakelen tijdens het maken van een flexibele server.

Deze pagina bevat richt lijnen voor het in-of uitschakelen van hoge Beschik baarheid. Met deze bewerking worden geen andere instellingen gewijzigd, zoals de VNET-configuratie, Firewall instellingen en de retentie van back-ups. Op dezelfde manier is het uitschakelen van hoge Beschik baarheid een online bewerking en heeft dit geen invloed op de connectiviteit en bewerkingen van uw toepassing.

> [!IMPORTANT]
> Zone redundant hoge Beschik baarheid is beschikbaar in een beperkte set regio's: Zuidoost-Azië, West 2, Europa-west en VS-Oost.  

## <a name="enable-high-availability-during-server-creation"></a>Hoge Beschik baarheid inschakelen tijdens het maken van de server

Deze sectie bevat specifieke informatie over HA-gerelateerde velden. U kunt deze stappen volgen om hoge Beschik baarheid te implementeren tijdens het maken van uw flexibele server.

1.  Kies in het [Azure Portal](https://portal.azure.com/)flexibele server en klik op **maken**.  Zie How-to-documentatie voor het maken van een server voor meer informatie over het invullen van details zoals **abonnement**, **resource groep**, **Server naam**, **regio** en andere velden.

2.  Klik op het selectie vakje voor de **zone redundant hoge Beschik baarheid** in de optie Beschik baarheid.

3.  Als u de standaard Compute en opslag wilt wijzigen, klikt u op  **server configureren**.

4.  Als de optie hoge Beschik baarheid is ingeschakeld, is de Burstable-laag niet beschikbaar om te kiezen. U kunt kiezen voor **algemeen gebruik** of op **geheugen geoptimaliseerde** reken lagen.

    > [!IMPORTANT]
    > We ondersteunen alleen zone redundante hoge Beschik baarheid voor de prijs categorie ***algemeen gebruik** _ en _ *_geoptimaliseerd voor geheugen_*.

5.  Selecteer de **reken grootte** voor uw keuze in de vervolg keuzelijst.

6.  Selecteer **opslag grootte** in GiB met behulp van de schuif balk en selecteer de **Bewaar periode voor back-ups** tussen 7 dagen en 35 dagen.   

## <a name="disable-high-availability"></a>Hoge Beschik baarheid uitschakelen

Volg deze stappen om maximale Beschik baarheid uit te scha kelen voor uw flexibele server die al is geconfigureerd met zone redundantie.

1.  Selecteer uw bestaande Azure Database for MySQL flexibele server in de [Azure Portal](https://portal.azure.com/).

2.  Klik op de pagina flexibele server op het voor paneel op **hoge Beschik baarheid** om de pagina hoge Beschik baarheid te openen.

3.  Klik op het selectie vakje **redundante maximale Beschik baarheid van zone** om de optie uit te scha kelen en klik op **Opslaan** om de wijziging op te slaan.

4.  Er wordt een bevestigings venster weer gegeven waarin u het uitschakelen van HA kunt bevestigen.

5.  Klik op de knop **ha uitschakelen** om de maximale Beschik baarheid uit te scha kelen.

6.  Er wordt een melding weer gegeven dat het uit bedrijf nemen van de implementatie met hoge Beschik baarheid wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over [bedrijfs continuïteit](./concepts-business-continuity.md)
-   Meer informatie over [zone redundante hoge Beschik baarheid](./concepts-high-availability.md)
