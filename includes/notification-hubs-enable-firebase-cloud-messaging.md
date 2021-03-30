---
title: bestand opnemen
description: bestand opnemen
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "70935121"
---
1. Meld u aan bij de [Firebase-console](https://firebase.google.com/console/). Maak een nieuw Firebase-project als u er nog geen hebt.
2. Nadat u uw project hebt gemaakt, selecteert u **Firebase toevoegen aan uw Android-app**. 

    ![Firebase toevoegen aan uw Android-app](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Voer de volgende stappen uit op de pagina **Firebase toevoegen aan uw Android-app**: 
    1. Voor **Android-pakketnaam** kopieert u de waarde van uw **applicationId** in het bestand build.gradle van uw toepassing. In dit voorbeeld is het `com.fabrikam.fcmtutorial1app`. 

        ![Specificeer de pakketnaam](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecteer **App registreren**. 
4. Selecteer **google-services.json downloaden**, sla het bestand op in de map **app** van uw project, en selecteer **Volgende**. 

    ![Download google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Breng de volgende **configuratiewijzigingen** aan in uw project in Android Studio. 
    1.  Voeg in het bestand build.gradle op projectniveau (&lt;project&gt;/build.gradle) de volgende instructie toe aan de sectie **dependencies** (afhankelijkheden). 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. Voeg in het bestand build.gradle op app-niveau (&lt;project&gt;/&lt;app-module&gt;/build.gradle) de volgende instructies toe aan de sectie **dependencies** (afhankelijkheden). 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Voeg de volgende regel toe aan het einde van het build.gradle-bestand op app-niveau na de sectie met afhankelijkheden. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Selecteer **Nu synchroniseren** op de werkbalk. 
 
        ![build.gradle-configuratiewijzigingen](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Selecteer **Next**. 
7. Selecteer **Deze stap overslaan**. 

    ![De laatste stap overslaan](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Selecteer in de Firebase-console het tandwiel van uw project. Selecteer vervolgens **Projectinstellingen**.

    ![Selecteer Projectinstellingen](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Als u het bestand google-services.json niet hebt gedownload in de map **app** van uw Android Studio-project, kunt u dat op deze pagina doen. 
5. Ga naar **Cloud Messaging** bovenaan op het tabblad. 
6. Kopieer de **Serversleutel** en sla deze op voor later gebruik. U gebruikt deze waarde om uw hub te configureren.
