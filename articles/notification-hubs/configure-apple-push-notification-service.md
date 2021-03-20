---
title: Apple Push Notification Service configureren in azure Notification Hubs | Microsoft Docs
description: Meer informatie over het configureren van een Azure notification hub met Apple Push Notification Service (APNS)-instellingen.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 63c7e0c9569428b55420911f253deee52ce440cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85255395"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Apple Push Notification Service-instellingen configureren voor een notification hub in de Azure Portal

In dit artikel wordt beschreven hoe u Apple Push Notification Service (APNS)-instellingen configureert voor een Azure notification hub met behulp van de Azure Portal.

## <a name="prerequisites"></a>Vereisten

Maak nu een notification hub als u dat nog niet hebt gedaan. Zie [een Azure notification hub maken in de Azure Portal](create-notification-hub-portal.md)voor meer informatie.

## <a name="configure-apple-push-notification-service"></a>Apple Push Notification Service configureren

De volgende procedure bevat stappen voor het configureren van Apple Push Notification Service (APNS)-instellingen voor een notification hub:

1. Selecteer in het Azure Portal op de pagina **Notification hub** **Apple (APNS)** in het menu links.

1. Selecteer **Certificaat** of **Token** als **Verificatiemodus**.

   - Als u **Certificaat** selecteert:
      - Selecteer het pictogram van het bestand en selecteer het *.p12*-bestand dat u wilt uploaden.
      - Voer een wachtwoord in.
      - Selecteer de modus **Sandbox**. Of selecteer de modus **Productie** als u pushmeldingen wilt verzenden naar gebruikers die uw app in de Store hebben gekocht.

     ![Schermopname van een configuratie van een APNS-certificaat in de Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   - Als u **Token** selecteert:
      - Voer de waarden in voor **sleutel-id**, **bundel-id**, **team-id** en **token**.
      - Selecteer de modus **Sandbox**. Of selecteer de modus **Productie** als u pushmeldingen wilt verzenden naar gebruikers die uw app in de Store hebben gekocht.

     ![Schermafbeelding van een configuratie van een APNS-token in de Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Volgende stappen

Voor een zelf studie met stapsgewijze instructies voor het verzenden van meldingen naar iOS-apparaten raadpleegt u het volgende artikel: [Push meldingen verzenden naar IOS-apps met behulp van Azure notification hubs](ios-sdk-get-started.md).
