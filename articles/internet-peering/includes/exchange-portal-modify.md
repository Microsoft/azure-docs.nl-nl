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
ms.openlocfilehash: e06b5261ca6923e158c818d236a30cf6ebff189b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680948"
---
In deze sectie wordt beschreven hoe u de volgende wijzigings bewerkingen voor directe peering uitvoert.

### <a name="add-exchange-peering-connections"></a>Exchange-peering verbindingen toevoegen

1. Selecteer de knop **+ verbindingen toevoegen** en configureer een nieuwe peering-verbinding.
    > [!div class="mx-imgBorder"]
    > ![Bron weergave voor peering](../media/setup-exchange-modify-addconnection.png)
1. Vul het verbindings formulier voor **Exchange-peering** in en selecteer **Opslaan**. Voor hulp bij het configureren van een peering-verbinding raadpleegt u de stappen in de sectie ' een directe peering maken en inrichten '.
    > [!div class="mx-imgBorder"]
    > ![Exchange peering-verbindings formulier](../media/setup-exchange-modify-savenewconnection.png)

### <a name="remove-exchange-peering-connections"></a>Exchange-peering verbindingen verwijderen

1. Selecteer een peering-verbinding die u wilt verwijderen en selecteer vervolgens **...**  >  **Verbinding verwijderen**.
    > [!div class="mx-imgBorder"]
    > ![Knop verbinding verwijderen](../media/setup-exchange-modify-deleteconnection.png)
1. Voer de resource-ID in het vak **verwijderen bevestigen** in en selecteer **verwijderen**.
    > [!div class="mx-imgBorder"]
    > ![Bevestiging verwijderen](../media/setup-exchange-modify-deleteconnectionconfirm.png)

### <a name="add-an-ipv4-or-ipv6-session-on-active-connections"></a>Een IPv4-of IPv6-sessie toevoegen aan actieve verbindingen

1. Selecteer een peering-verbinding die u wilt wijzigen en selecteer vervolgens **...**  >  **Verbinding bewerken**.
    > [!div class="mx-imgBorder"]
    > ![Knop verbinding bewerken](../media/setup-exchange-modify-editconnection.png)
1. Voeg informatie over **IPv4-adres** of **IPv6-adres** toe en selecteer **Opslaan**.
    > [!div class="mx-imgBorder"]
    > ![Peering-verbindings wijzigingen](../media/setup-exchange-modify-editconnectionsettings.png)

### <a name="remove-an-ipv4-or-ipv6-session-on-active-connections"></a>Een IPv4-of IPv6-sessie op actieve verbindingen verwijderen

Het verwijderen van een IPv4-of IPv6-sessie van een bestaande verbinding wordt momenteel niet ondersteund in de portal. Neem contact op met [micro soft-peering](mailto:peeringexperience@microsoft.com)voor meer informatie.