---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 5/22/2020
ms.author: derekol
ms.openlocfilehash: e2804c8f9b1af89ac0ea86ec14136df66d900060
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83846123"
---
1. Selecteer **een resource maken**  >  **Alles bekijken**.

    > [!div class="mx-imgBorder"]
    > ![Zoeken in peering](../media/setup-seeall.png)

1. Zoek op **peering** in het zoekvak en selecteer **Enter** op het toetsen bord. Selecteer een **peering** -resource in de resultaten.

    > [!div class="mx-imgBorder"]
    > ![Peering starten](../media/setup-launch.png)

1. Nadat **peering** is gestart, controleert u de pagina om de details te begrijpen. Wanneer u klaar bent, selecteert u **maken**.

    > [!div class="mx-imgBorder"]
    > ![Peering maken](../media/setup-create.png)

1. Vul op de pagina **een peering maken** op het tabblad **basis beginselen** de selectie vakjes in, zoals hier wordt weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Tabblad basis informatie over peering](../media/setup-basics-tab.png)

    * Selecteer uw Azure- **abonnement**.
    * Voor **resource groep** kunt u een bestaande resource groep in de vervolg keuzelijst kiezen of een nieuwe groep maken door **Nieuw maken** te selecteren. We maken een nieuwe resource groep voor dit voor beeld.
    * De **naam** komt overeen met de naam van de resource en kan alles zijn wat u kiest.
    * De **regio** wordt geselecteerd als u een bestaande resource groep hebt gekozen. Als u ervoor hebt gekozen om een nieuwe resource groep te maken, moet u ook de Azure-regio kiezen waar u de resource wilt opslaan.

        > [!NOTE]
        > De regio waar een resource groep zich bevindt, is onafhankelijk van de locatie waar u peering met micro soft wilt maken. Maar het is een best practice om uw peering resources te organiseren in resource groepen die zich in de dichtstbijzijnde Azure-regio's bevinden. U kunt bijvoorbeeld voor peerings in Ashburn een resource groep maken in VS-Oost of Oost-VS2.

    * Selecteer uw ASN in het vak **peer ASN** .

        > [!IMPORTANT]
        > * U kunt alleen een ASN met ValidationState als goedgekeurd kiezen voordat u een peering-aanvraag indient. Als u zojuist uw PeerAsn-aanvraag hebt verzonden, wacht u 12 uur of voor een ASN-koppeling die u wilt goed keuren. Als de ASN die u hebt geselecteerd, in afwachting is van validatie, wordt een fout bericht weer gegeven. 
        > * Als u de ASN die u nodig hebt, niet ziet, controleert u of u het juiste abonnement hebt geselecteerd. Als dit het geval is, controleert u of u PeerAsn al hebt gemaakt met behulp van [peer ASN koppelen aan Azure-abonnement](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Basis beginselen van peering ingevuld](../media/setup-direct-basics-filled-tab.png)

    * Selecteer **volgende: configuratie >** om door te gaan.
