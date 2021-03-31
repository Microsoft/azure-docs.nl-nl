---
title: bestand opnemen
description: bestand opnemen
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "68728819"
---
1. Meld u aan bij de [Firebase-console](https://firebase.google.com/console/). Maak een nieuw Firebase-project als u er nog geen hebt.
2. Nadat u uw project hebt gemaakt, selecteert u **Firebase toevoegen aan uw Android-app**. 

    ![Firebase toevoegen aan uw Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Voer de volgende stappen uit op de pagina **Firebase toevoegen aan uw Android-app**: 
    1. Voer voor de **Android-pakketnaam** een naam in voor uw pakket. Bijvoorbeeld: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Specificeer de pakketnaam](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecteer **App registreren**.  
    1. Selecteer **google services.json downloaden**. Sla het bestand vervolgens op in de map van het project en selecteer **Volgende**. Als het Visual Studio-project nog niet hebt gemaakt, kunt u deze stap uitvoeren nadat u het project hebt gemaakt. 

        ![Download google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Selecteer **Next**. 
    7. Selecteer **Deze stap overslaan**. 

        ![De laatste stap overslaan](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Selecteer in de Firebase-console het tandwiel van uw project. Selecteer vervolgens **Projectinstellingen**.

    ![Selecteer Projectinstellingen](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Als u het bestand **google-services.json** niet hebt gedownload, kunt u het downloaden op deze pagina. 

    ![google-services.json downloaden via het tabblad Algemeen](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Ga naar **Cloud Messaging** bovenaan op het tabblad. Kopieer de **Serversleutel** en sla deze op voor later gebruik. U gebruikt deze waarde om uw Notification Hub te configureren.

    ![Serversleutel kopiëren](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
