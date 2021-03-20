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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680961"
---
1. Vul op de pagina **een peering maken** op het tabblad **configuratie** de vakken in zoals weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Een peering-pagina maken peering-type uitwisseling](../media/setup-exchange-conf-tab.png)

    * Selecteer **Exchange** bij **peering-type**.
    * Selecteer **SKU** als **gratis basis** versie.
    * Selecteer de **metro** locatie waar u peering wilt instellen.

        > [!NOTE]
        > Als u al peering-verbindingen met micro soft hebt in de geselecteerde **metro** locatie en u de portal voor het eerst gebruikt om peering in te stellen op die locatie, worden uw bestaande peering-verbindingen weer gegeven in de sectie **peering-verbindingen** , zoals wordt weer gegeven. Deze peering verbindingen worden door micro soft automatisch geconverteerd naar een Azure-resource, zodat u ze allemaal kunt beheren, samen met de nieuwe verbindingen op één plek. Zie [een verouderde Exchange-peering converteren naar een Azure-resource met behulp van de portal](../howto-legacy-exchange-portal.md)voor meer informatie.
        >

1. Onder **peering-verbindingen** selecteert u **nieuwe maken** om een regel toe te voegen voor elke nieuwe verbinding die u wilt instellen.

    * Als u de verbindings instellingen wilt configureren of wijzigen, selecteert u de knop bewerken voor een regel.

        > [!div class="mx-imgBorder"]
        > ![Knop bewerken](../media/setup-exchange-conf-tab-edit.png)

    * Als u een regel wilt verwijderen, selecteert u **...**  >  **Verwijderen**.

        > [!div class="mx-imgBorder"]
        > ![De knop Verwijderen](../media/setup-exchange-conf-tab-delete.png)

    * U moet alle instellingen voor een verbinding opgeven, zoals hier wordt weer gegeven.

         > [!div class="mx-imgBorder"]
         > ![Pagina Exchange peering-verbinding](../media/setup-exchange-conf-tab-connection.png)

        1. Selecteer de **peering-faciliteit** waarbij de verbinding moet worden ingesteld.
        1. Voer in de vakken **IPv4-adres** en **IPv6-adres** de IPv4-en IPv6-adressen in, respectievelijk die in micro soft routers worden geconfigureerd met behulp van de opdracht neighbor.
        1. Voer het aantal IPv4-en IPv6-voor voegsels in dat u wilt adverteren in de vakken **maximum aantal geadverteerde IPv4-adressen** en **maximum aantal geadverteerde IPv6-adressen** .
        1. Selecteer **OK** om uw Verbindings instellingen op te slaan.

1. Herhaal de stap om meer verbindingen toe te voegen aan elke faciliteit waar micro soft zich in de geselecteerde **metro lijn** bevindt met uw netwerk.

1. Nadat u alle vereiste verbindingen hebt toegevoegd, selecteert u **controleren + maken**.

    > [!div class="mx-imgBorder"]
    > ![Tabblad Configuratie van peering](../media/setup-exchange-conf-tab-final.png)

1. De portal voert basis validatie uit van de gegevens die u hebt ingevoerd. In een lint bovenaan wordt het bericht weer gegeven dat de *laatste validatie uitvoert...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad validatie van peering](../media/setup-direct-review-tab-validation.png)

1. Nadat het bericht is gewijzigd in de *validatie*, controleert u uw gegevens. Dien de aanvraag in door **maken** te selecteren. Als u uw aanvraag wilt wijzigen, selecteert u **vorige** en herhaalt u de stappen.

    > [!div class="mx-imgBorder"]
    > ![Verzenden van peering](../media/setup-exchange-review-tab-submit.png)

1. Nadat u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com). Er wordt een geslaagde implementatie weer gegeven zoals hier wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering geslaagd](../media/setup-direct-success.png)
