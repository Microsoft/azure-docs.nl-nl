---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81681070"
---
1. Vul op de pagina **een peering maken** op het tabblad **configuratie** de selectie vakjes in, zoals hier wordt weer gegeven.

    * Selecteer voor **type peering** de optie **direct**.
    * Selecteer voor **micro soft-netwerk** **AS8075**. Maak geen peering met ASN 8069. Het is gereserveerd voor speciale toepassingen en wordt alleen gebruikt door [micro soft-peering](mailto:peering@microsoft.com).
    * Selecteer **SKU** als **gratis basis** versie. Selecteer Gratis Premium, omdat deze is gereserveerd voor speciale toepassingen.
    * Selecteer de **metro** locatie waar u peering wilt instellen.

        > [!NOTE]
        > Als u al peering-verbindingen met micro soft hebt in de geselecteerde **metro** locatie en u de Azure portal voor het eerst gebruikt om peering in te stellen op die locatie, worden uw bestaande peering-verbindingen weer gegeven in de sectie **peering-verbindingen** , zoals wordt weer gegeven. Micro soft converteert deze peering-verbindingen automatisch naar een Azure-resource, zodat u ze allemaal kunt beheren, samen met de nieuwe verbindingen, op één plek. Zie [een verouderde directe peering converteren naar een Azure-resource met behulp van de portal](../howto-legacy-direct-portal.md)voor meer informatie.
        >

1. Onder **peering-verbindingen** selecteert u **nieuwe maken** om een regel toe te voegen voor elke nieuwe verbinding die u wilt instellen.

    * Als u de verbindings instellingen wilt configureren of wijzigen, selecteert u de knop bewerken voor een regel.

        > [!div class="mx-imgBorder"]
        > ![Knop bewerken](../media/setup-direct-conf-tab-edit.png)
    
    * Als u een regel wilt verwijderen, selecteert u **...**  >  **Verwijderen**.

        > [!div class="mx-imgBorder"]
        > ![De knop Verwijderen](../media/setup-direct-conf-tab-delete.png)

    * U moet alle instellingen voor een verbinding opgeven, zoals hier wordt weer gegeven.

         > [!div class="mx-imgBorder"]
         > ![Pagina directe peering-verbinding](../media/setup-direct-conf-tab-connection.png)

        1. Selecteer de **peering-faciliteit** waarbij de verbinding moet worden ingesteld.
        1. **Sessie adres provider** wordt gebruikt om te bepalen wie het subnet heeft dat nodig is voor het instellen van de BGP-sessie tussen uw netwerk en micro soft. Als u het subnet kunt opgeven, selecteert u **peer**. Anders selecteert u **micro soft** -en [micro soft-peering](mailto:peering@microsoft.com) neemt contact met u op. Als u deze optie kiest, duurt het langer voor micro soft om de peering-aanvraag te verwerken. In sommige gevallen kan micro soft mogelijk geen subnets leveren, wat leidt tot een weigering van de aanvraag.
        1. Als u de optie **sessie adres provider** hebt geselecteerd als **peer**, voert u de IPv4-en IPv6-adressen in, respectievelijk de prefix maskers in de vakken **IPv4-voor voegsel voor de sessie** en **IPv6-voor voegsel** .
        1. Voer het aantal IPv4-en IPv6-voor voegsels in dat u wilt adverteren in de vakken **maximum aantal geadverteerde IPv4-adressen** en **maximum aantal geadverteerde IPv6-adressen** .
        1. Pas de schuif regelaar **totale band breedte** aan om de band breedte voor de verbinding weer te geven.
        1. Selecteer **Opslaan** om uw Verbindings instellingen op te slaan.

1. Herhaal de vorige stap om meer verbindingen toe te voegen aan elke faciliteit waar micro soft zich op het netwerk bevindt, in de **metro lijn** die u eerder hebt geselecteerd.

1. Nadat u alle vereiste verbindingen hebt toegevoegd, selecteert u **controleren + maken**.

    > [!div class="mx-imgBorder"]
    > ![Tabblad het configureren van peering is voltooid](../media/setup-direct-conf-tab-final.png)

1. De portal voert basis validatie uit van de gegevens die u hebt ingevoerd. In een lint bovenaan wordt het bericht weer gegeven dat de *laatste validatie uitvoert...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad validatie van peering](../media/setup-direct-review-tab-validation.png)

1. Nadat het bericht is gewijzigd in de *validatie*, controleert u uw gegevens. Dien de aanvraag in door **maken** te selecteren. Als u uw aanvraag wilt wijzigen, selecteert u **vorige** en herhaalt u de stappen.

    > [!div class="mx-imgBorder"]
    > ![Verzenden van peering](../media/setup-direct-review-tab-submit.png)

1. Nadat u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com). Er wordt een geslaagde implementatie weer gegeven zoals hier wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering geslaagd](../media/setup-direct-success.png)
