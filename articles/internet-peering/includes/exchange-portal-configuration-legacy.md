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
ms.openlocfilehash: b8869e93a7156b24d61ac555c95b9ca7f850ae34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81678583"
---
1. Vul op de pagina **een peering maken** op het tabblad **configuratie** de selectie vakjes in, zoals hier wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Een peering-pagina maken peering-type uitwisseling](../media/setup-exchange-conf-tab.png)

    * Selecteer **Exchange** bij **peering-type**.
    * Selecteer **SKU** als **gratis basis** versie.
    * Selecteer de **metro** locatie waar u de peering wilt converteren naar een Azure-resource. Als u peering-verbindingen hebt met micro soft in de geselecteerde **metro** locatie die niet worden geconverteerd naar een Azure-resource, worden deze verbindingen weer gegeven in de sectie **peering-verbindingen** , zoals wordt getoond. U kunt deze peering-verbindingen nu converteren naar een Azure-resource.

        > [!div class="mx-imgBorder"]
        > ![Lijst met peering verbindingen](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > U kunt de instellingen voor verouderde peering-verbindingen niet wijzigen. Als u aanvullende peering-verbindingen wilt toevoegen met micro soft in de geselecteerde **metro** locatie, selecteert u **nieuwe maken**. Zie [een Exchange-peering maken of wijzigen met behulp van de portal](../howto-exchange-portal.md)voor meer informatie.
        >

1. Selecteer **Controleren + maken**. De portal voert basis validatie uit van de gegevens die u hebt ingevoerd. In een lint bovenaan wordt het bericht weer gegeven dat de *laatste validatie uitvoert...*.

    > [!div class="mx-imgBorder"]
    > ![Tabblad validatie van peering](../media/setup-direct-review-tab-validation.png)

1. Nadat het bericht is gewijzigd in de *validatie*, controleert u uw gegevens. Dien de aanvraag in door **maken** te selecteren. Als u uw aanvraag wilt wijzigen, selecteert u **vorige** en herhaalt u de stappen.

    > [!div class="mx-imgBorder"]
    > ![Verzenden van peering](../media/setup-exchange-review-tab-submit.png)

1. Nadat u de aanvraag hebt ingediend, wacht u tot de implementatie is voltooid. Als de implementatie mislukt, neemt u contact op met [micro soft-peering](mailto:peering@microsoft.com). Er wordt een geslaagde implementatie weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Peering geslaagd](../media/setup-direct-success.png)
