---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81687057"
---
1. Selecteer een peering-verbinding die u wilt inschakelen voor de Azure peering-service. Selecteer vervolgens **...**  >  **Verbinding bewerken**.
    > [!div class="mx-imgBorder"]
    > ![Verbinding voor verbinding met peering bewerken](../media/setup-direct-modify-editconnection.png)
1. Selecteer bij **gebruik voor peering**-service **ingeschakeld** en selecteer vervolgens **Opslaan**.
    > [!div class="mx-imgBorder"]
    > ![Peering-verbinding inschakelen peering service](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. Op het scherm **overzicht** ziet u de details van de implementatie. Nadat de implementatie is voltooid, selecteert **u naar resource gaan**.
    > [!div class="mx-imgBorder"]
    > ![Uw implementatie is voltooid](../media/setup-direct-modify-overview-deployment-complete.png)

1. Selecteer in het deel venster **geregistreerde voor voegsels** de optie **geregistreerd voor voegsel toevoegen**.
    > [!div class="mx-imgBorder"]
    > ![Geregistreerd voor voegsel toevoegen](../media/setup-direct-modify-add-registered-prefix.png)
1. Een voor voegsel registreren door een **naam** en een **voor voegsel** te selecteren en **Opslaan** te selecteren.
    > [!div class="mx-imgBorder"]
    >  ![Een voor voegsel registreren](../media/setup-direct-modify-register-a-prefix.png) 

1. Nadat een voor voegsel is gemaakt, ziet u dit in de lijst met **geregistreerde voor voegsels**. Selecteer de **naam** van het voor voegsel om meer details weer te geven.
    > [!div class="mx-imgBorder"]
    > ![Geregistreerde voor voegsels en verbindingen](../media/setup-direct-modify-registered-prefixes.png)
1. Op de pagina geregistreerd voor voegsel ziet u de volledige details, waarin de **prefix sleutel** voor elk voor voegsel is opgenomen. Deze sleutel moet worden verleend aan de klant die dit voor voegsel van de ISP van de provider heeft toegewezen. De klant kan het voor voegsel binnen hun abonnement registreren met behulp van deze sleutel.
    > [!div class="mx-imgBorder"]
    > ![Voor voegsel met voorvoegsel sleutel](../media/setup-direct-modify-registered-prefix-detail.png)