---
title: Micro soft Push Notification Service configureren in azure Notification Hubs | Microsoft Docs
description: Meer informatie over het configureren van instellingen voor micro soft Push Notification Service voor een Azure notification hub.
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
ms.openlocfilehash: 6784331dccd779390ace7dc632d54fe54aedc458
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87760988"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Instellingen voor micro soft Push Notification Service (MPNS) configureren in de Azure Portal

In dit artikel wordt beschreven hoe u instellingen voor micro soft Push Notification Service (MPNS) voor een Azure notification hub configureert met behulp van de Azure Portal.

## <a name="prerequisites"></a>Vereisten

Maak nu een notification hub als u dat nog niet hebt gedaan. Zie [een Azure notification hub maken in de Azure Portal](create-notification-hub-portal.md)voor meer informatie.

## <a name="configure-microsoft-push-notification-service-mpns"></a>Micro soft Push Notification Service (MPNS) configureren

In de volgende procedure wordt beschreven hoe u instellingen voor micro soft Push Notification Service (MPNS) configureert voor een notification hub:

1. Selecteer in de Azure Portal op de pagina **Notification Hub** **Windows Phone (MPNS)** in het menu links.
2. Niet-geverifieerde of geverifieerde pushmeldingen inschakelen:

   a. Selecteer **Niet-geverifieerde push inschakelen** > **Opslaan** om niet-geverifieerde pushmeldingen in te schakelen.

      ![Schermafbeelding die laat zien hoe u niet-geverifieerde pushmeldingen inschakelt](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Geverifieerde pushmeldingen inschakelen:
      * Selecteer **Certificaat uploaden** op de werkbalk.
      * Selecteer het pictogram van het bestand en selecteer vervolgens het certificaatbestand.
      * Voer het wachtwoord voor het certificaat in.
      * Selecteer **OK**.
      * Selecteer **Opslaan** op de pagina **Windows Phone (MPNS)** .

## <a name="next-steps"></a>Volgende stappen

Zie [Push meldingen verzenden naar Windows Phone-apps met behulp van Notification hubs](notification-hubs-windows-mobile-push-notifications-mpns.md)voor een zelf studie met stapsgewijze instructies voor het pushen van meldingen naar Windows Phone apparaten met behulp van Azure notification hubs en micro soft Push Notification Service (MPNS).
