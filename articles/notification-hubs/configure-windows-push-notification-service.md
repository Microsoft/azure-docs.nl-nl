---
title: Windows Push Notification Service configureren in azure Notification Hubs | Microsoft Docs
description: Meer informatie over het configureren van Windows Push Notification Service-instellingen voor een Azure notification hub.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 62d73a23fff3bd15d873a579d451b2f91509b7fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87758727"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Instellingen voor Windows Push Notification Service configureren in de Azure Portal

In dit artikel wordt beschreven hoe u WNS-instellingen (Windows Notification Service) configureert voor een Azure notification hub met behulp van de Azure Portal.  

## <a name="prerequisites"></a>Vereisten

Maak nu een notification hub als u dat nog niet hebt gedaan. Zie [een Azure notification hub maken in de Azure Portal](create-notification-hub-portal.md)voor meer informatie.

## <a name="configure-windows-push-notification-service-wns"></a>Windows Push Notification Service (WNS) configureren

In de volgende procedure worden de stappen beschreven voor het configureren van WNS-instellingen (Windows Push Notification Service) voor een notification hub:

1. Selecteer in de Azure Portal op de pagina **Notification hub** de optie **Windows (WNS)** in het menu links.
2. Voer waarden in voor **Pakket-SID** en **Beveiligingssleutel**.
3. Selecteer **Opslaan**.

   ![Schermafbeelding die de vakken Pakket-SID en Beveiligingssleutel weergeeft](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Volgende stappen

Zie [meldingen verzenden naar UWP-apps met behulp van azure notification hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)voor een zelf studie met stapsgewijze instructies voor het verzenden van push meldingen naar universeel Windows-platform-toepassingen met behulp van Azure notification hubs en Windows Push Notification Service (WNS).
