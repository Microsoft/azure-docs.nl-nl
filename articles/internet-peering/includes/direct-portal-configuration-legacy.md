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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81678833"
---
1. Vul op de pagina **een peering maken** op het tabblad **configuratie** de selectie vakjes in, zoals hier wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Een tabblad voor het configureren van een peering-pagina maken](../media/setup-direct-conf-tab.png)

    * Selecteer voor **type peering** de optie **direct**.
    * Selecteer voor **micro soft-netwerk** **AS8075**. Selecteer niet ASN 8069. Het is gereserveerd voor speciale toepassingen en wordt alleen gebruikt door [micro soft-peering](mailto:peering@microsoft.com).
    * Selecteer **SKU** als **gratis basis** versie. Selecteer Gratis Premium, omdat deze is gereserveerd voor speciale toepassingen.
    * Selecteer de **metro** locatie waar u de peering wilt converteren naar een Azure-resource. Als u peering-verbindingen hebt met micro soft in de geselecteerde **metro** locatie die niet worden geconverteerd naar een Azure-resource, worden deze verbindingen weer gegeven in de sectie **peering-verbindingen** , zoals wordt getoond. U kunt deze peering-verbindingen nu converteren naar een Azure-resource.

        > [!div class="mx-imgBorder"]
        > ![Lijst met peering verbindingen](../media/setup-directlegacy-conf-tab.png)

1. Als u band breedte wilt bijwerken, selecteert u de knop bewerken voor een regel om de verbindings instellingen te wijzigen.

    > [!div class="mx-imgBorder"]
    > ![Knop bewerken](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Als u aanvullende peering-verbindingen wilt toevoegen met micro soft in de geselecteerde **metro** locatie, selecteert u **nieuwe maken**. Zie [direct peering maken of wijzigen met behulp van de portal](../howto-direct-portal.md)voor meer informatie.
    >

1. Selecteer **Controleren + maken**. De portal voert basis validatie uit van de gegevens die u hebt ingevoerd. In een lint bovenaan wordt het bericht weer gegeven dat de *laatste validatie uitvoert...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad validatie van peering](../media/setup-direct-review-tab-validation.png)

1. Nadat het bericht is gewijzigd in de *validatie*, controleert u uw gegevens. Dien de aanvraag in door **maken** te selecteren. Als u uw aanvraag wilt wijzigen, selecteert u **vorige** en herhaalt u de stappen.

    > [!div class="mx-imgBorder"]
    > ![Verzenden van peering](../media/setup-direct-review-tab-submit.png)

1. Nadat u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com). Er wordt een geslaagde implementatie weer gegeven zoals hier wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering geslaagd](../media/setup-direct-success.png)
