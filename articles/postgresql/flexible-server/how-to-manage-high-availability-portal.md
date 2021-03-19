---
title: Zone redundante hoge Beschik baarheid beheren-Azure Portal-Azure Database for PostgreSQL-flexibele server
description: In dit artikel wordt beschreven hoe u een redundante hoge Beschik baarheid van zone in-of uitschakelt in Azure Database for PostgreSQL-flexibele server via de Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90934908"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>Zone redundante hoge Beschik baarheid beheren op flexibele server

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

In dit artikel wordt beschreven hoe u de redundante configuratie van een zone met hoge Beschik baarheid in-of uitschakelen in uw flexibele server.

Met de functie voor hoge Beschik baarheid wordt een fysiek gescheiden primaire en stand-by replica in verschillende zones ingericht. Zie de [documentatie over concepten met hoge Beschik baarheid](./concepts-high-availability.md)voor meer informatie. U kunt ervoor kiezen om Maxi maal Beschik baarheid in te scha kelen op het moment dat u een flexibele server maakt of nadat het is gemaakt. Deze pagina bevat richt lijnen voor het in-of uitschakelen van hoge Beschik baarheid. Met deze bewerking worden geen andere instellingen gewijzigd, zoals de VNET-configuratie, Firewall instellingen en de retentie van back-ups. Het inschakelen en uitschakelen van hoge Beschik baarheid is een online bewerking en heeft geen invloed op de connectiviteit en bewerkingen van uw toepassing.

## <a name="pre-requisites"></a>Vereisten

Zone redundant hoge Beschik baarheid is alleen beschikbaar in regio's waar meerdere zones worden ondersteund. 

## <a name="enable-high-availability-during-server-creation"></a>Hoge Beschik baarheid inschakelen tijdens het maken van de server

Deze sectie bevat specifieke informatie over HA-gerelateerde velden. U kunt deze stappen volgen om hoge Beschik baarheid te implementeren tijdens het maken van uw flexibele server.

1.  Kies in het [Azure Portal](https://portal.azure.com/)flexibele server en klik op maken.  Zie How-to-documentatie voor het maken van een server voor meer informatie over het invullen van details zoals **abonnement**, **resource groep**, **Server naam**, **regio** en andere velden.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="Abonnement en regio weer geven":::

2.  Kies uw **beschikbaarheids zone**. Dit is handig als u uw toepassing wilt termijnen in dezelfde beschikbaarheids zone als de data base om de latentie te verminderen. Kies **geen voor keur** als u wilt dat de flexibele server op elke beschikbaarheids zone wordt geïmplementeerd.
    ![AZ selectie ]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="beschikbaarheids zone selecteren":::  

3.  Klik op het selectie vakje voor de **zone redundant hoge Beschik baarheid** in de optie Beschik baarheid.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="Selectie vakje hoge Beschik baarheid":::

4.  Als u de standaard Compute en opslag wilt wijzigen, klikt u op  **server configureren**.
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="Server Compute + Storage configureren":::  

5.  Als de optie hoge Beschik baarheid is ingeschakeld, is de Burstable-laag niet beschikbaar om te kiezen. U kunt kiezen voor **algemeen gebruik** of op **geheugen geoptimaliseerde** reken lagen. Vervolgens kunt u de **reken grootte** voor uw keuze selecteren in de vervolg keuzelijst.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="Selectie van Compute-laag":::  


6.  Selecteer **opslag grootte** in GiB met behulp van de schuif balk en selecteer de **Bewaar periode voor back-ups** tussen 7 dagen en 35 dagen.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="Back-ups van opslag"::: 

7. Klik op **Opslaan**. 

## <a name="enable-high-availability-post-server-creation"></a>Het maken van een post server met hoge Beschik baarheid inschakelen

Volg deze stappen om hoge Beschik baarheid in te scha kelen voor uw bestaande flexibele server.

1.  Selecteer uw bestaande PostgreSQL-flexibele server in het [Azure Portal](https://portal.azure.com/).

2.  Klik op de pagina flexibele server op **hoge Beschik baarheid** in het linkerdeel venster om de pagina hoge Beschik baarheid te openen.
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Links paneel selecteren"::: 

3.  Klik op het selectie vakje **redundante maximale Beschik baarheid van zone** om de optie in te **scha kelen** en klik op **Opslaan** om de wijziging op te slaan.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="Hoge beschikbaarheid inschakelen"::: 

4.  Er wordt een bevestigings dialoogvenster weer gegeven waarin staat dat door hoge Beschik baarheid in te scha kelen uw kosten toenemen vanwege extra server-en opslag implementatie.

5.  Klik op de knop **ha inschakelen** om de maximale Beschik baarheid in te scha kelen.

6.  Er wordt een melding weer gegeven met de mede deling dat de implementatie met hoge Beschik baarheid wordt uitgevoerd.

## <a name="disable-high-availability"></a>Hoge Beschik baarheid uitschakelen

Volg deze stappen om maximale Beschik baarheid uit te scha kelen voor uw flexibele server die al is geconfigureerd met zone redundantie.

1.  Selecteer uw bestaande Azure Database for PostgreSQL-flexibele server in de [Azure Portal](https://portal.azure.com/).

2.  Klik op de pagina flexibele server op het voor paneel op **hoge Beschik baarheid** om de pagina hoge Beschik baarheid te openen.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Links paneel selecteren"::: 

3.  Klik op het selectie vakje **redundante maximale Beschik baarheid van zone** om de optie **uit te scha kelen** . Klik vervolgens op **Opslaan** om de wijziging op te slaan.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="Hoge Beschik baarheid uitschakelen"::: 

4.  Er wordt een bevestigings venster weer gegeven waarin u kunt bevestigen dat hoge Beschik baarheid wordt uitgeschakeld.

5.  Klik op de knop **ha uitschakelen** om de maximale Beschik baarheid uit te scha kelen.

6.  Er wordt een melding weer gegeven dat het uit bedrijf nemen van de implementatie met hoge Beschik baarheid wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over [bedrijfs continuïteit](./concepts-business-continuity.md)
-   Meer informatie over [zone redundante hoge Beschik baarheid](./concepts-high-availability.md)
