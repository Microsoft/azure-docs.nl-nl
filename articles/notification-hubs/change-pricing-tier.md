---
title: Prijs categorie van Notification Hubs naam ruimte wijzigen | Microsoft Docs
description: Meer informatie over het wijzigen van de prijs categorie van een Azure Notification Hubs-naam ruimte.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/03/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 1455259bc42aea9d506a9a2a19d725cac3d643f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87562766"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>De prijs categorie van een Azure notification hubs-naam ruimte wijzigen

Notification Hubs wordt aangeboden in drie lagen: **gratis**, **basis** en **standaard**. In dit artikel leest u hoe u de prijs categorie voor een Azure Notification Hubs-naam ruimte kunt wijzigen.

## <a name="overview"></a>Overzicht

In azure Notification Hubs is een *Notification hub* de kleinste resource/entiteit. Het is doorgaans toegewezen aan één toepassing en kan één certificaat bevatten voor elke Platform Notification System (PNS) die voor de app wordt ondersteund. De toepassing kan een hybride of een systeem eigen en platformoverschrijdende toepassing zijn.

Een *naam ruimte* is een verzameling notification hubs. Elke naam ruimte bestaat meestal uit hubs die verwant zijn en worden gebruikt voor een specifiek doel. U kunt bijvoorbeeld drie verschillende naam ruimten hebben voor respectievelijk ontwikkelings-, test-en productie doeleinden.

U kunt een naam ruimte koppelen aan de prijs categorieën **gratis**, **basis** of **standaard** . Voor elke naam ruimte kunt u de laag gebruiken die aansluit bij uw vereisten. In de volgende secties ziet u hoe u de prijs categorie van een Notification Hubs naam ruimte kunt wijzigen.

## <a name="use-azure-portal"></a>Azure Portal gebruiken

Wanneer u de Azure Portal gebruikt, kunt u de prijs categorie voor een naam ruimte op de pagina naam ruimte of op een hub-pagina wijzigen. Wanneer u deze op een hub-pagina wijzigt, wijzigt u deze in werkelijkheid op het niveau van de naam ruimte. De prijs categorie voor de naam ruimte en alle hubs in de naam ruimte wordt gewijzigd.

### <a name="change-tier-on-the-namespace-page"></a>De laag wijzigen op de pagina met de naam ruimte

De volgende procedure laat zien hoe u de prijs categorie voor een naam ruimte op de pagina met de naam ruimte kunt wijzigen. Wanneer u de laag voor een naam ruimte wijzigt, geldt dit voor alle hubs in die naam ruimte.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle services** in het menu links.
3. Selecteer **Notification hub-naam ruimten** in het gedeelte **Internet of Things** . Als u het sterretje ( `*` ) naast de tekst selecteert, wordt het toegevoegd aan de linkernavigatiebalk onder **Favorieten**. Dit helpt u de volgende keer sneller toegang te krijgen tot de pagina naam ruimten. Nadat u deze aan **Favorieten** hebt toegevoegd, selecteert u **Notification hub-naam ruimten**.

    ![Alle services-> notification hub-naam ruimten](./media/change-pricing-tier/all-services-nhub.png)

4. Selecteer op de pagina **Notification hub-naam ruimten** de naam ruimte waarvoor u de prijs categorie wilt wijzigen.
5. Op de pagina met de **Notification hub-naam** ruimte voor uw naam ruimte ziet u de huidige prijs categorie voor de naam ruimte in de sectie **Essentials** . In de volgende afbeelding ziet u dat de prijs categorie van de naam ruimte **gratis** is.

    ![Huidige prijs categorie op de naam ruimte pagina](./media/change-pricing-tier/pricing-tier-before.png)

6. Selecteer op de pagina met de **Notification hub-naam** ruimte voor uw naam ruimte de **prijs categorie** in de sectie **beheren** .

    ![Prijs categorie selecteren op de naam ruimte pagina](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Wijzig de prijs categorie en klik vervolgens op de knop **selecteren** .
8. U ziet de status van de wijzigings actie voor de laag in de **waarschuwingen**.
9. Ga naar de pagina **overzicht** . Controleer of de nieuwe laag wordt weer gegeven voor het veld **prijs categorie** in de sectie **Essentials** .
10. Deze stap is optioneel. Selecteer een hub in de naam ruimte. Controleer of dezelfde prijs categorie wordt weer geven in de sectie **Essentials** . U ziet nu dezelfde prijs categorie voor alle hubs in de naam ruimte.

### <a name="change-tier-on-the-hub-page"></a>De laag wijzigen op de hub-pagina

Volg deze stappen om de prijs categorie van een naam ruimte op de hub-pagina te wijzigen. Hoewel u deze stappen vanaf de hub-pagina uitvoert, wijzigt u de prijs categorie voor de naam ruimte en alle hubs in de naam ruimte.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **alle services** in het menu links.
3. Selecteer **Notification hubs** in het gedeelte **Internet of Things** .
4. Selecteer uw notification **hub**.
5. Selecteer **prijs categorie** in het menu links.
6. Wijzig de prijs categorie en klik op de knop **selecteren** . Met deze actie wijzigt u de instelling van de prijs categorie voor de naam ruimte met de hub. U ziet nu de nieuwe prijs categorie op de naam ruimte pagina en alle Hub Pages.

> [!NOTE]
> Alle wijzigingen in de prijs categorie zijn direct van kracht.

## <a name="use-rest-api"></a>REST API gebruiken

U kunt de volgende REST-Api's van de resource provider gebruiken om de huidige prijs categorie op te halen en deze bij te werken.

### <a name="get-current-pricing-tier-for-a-namespace"></a>Huidige prijs categorie voor een naam ruimte ophalen

Als u de huidige naam ruimte laag wilt ophalen, verzendt u een GET-opdracht, zoals wordt weer gegeven in het volgende voor beeld:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Prijs categorie voor een naam ruimte bijwerken

Als u de naam ruimte-laag wilt bijwerken, verzendt u een PUT-opdracht, zoals wordt weer gegeven in het volgende voor beeld:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over deze lagen en prijzen [Notification hubs prijzen](https://azure.microsoft.com/pricing/details/notification-hubs/).
