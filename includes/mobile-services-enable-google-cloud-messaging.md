---
title: bestand opnemen
description: bestand opnemen
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/06/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b32ff0926aa2b797bdb8dfcbb01cf845080a0ce9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93376238"
---
1. Navigeer naar [Google Cloud Console](https://console.developers.google.com/cloud-resource-manager) en meld u aan met de referenties voor uw Google-account. 
2. Selecteer **Project maken** op de werkbalk. 
   
    ![Nieuw project maken](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
3. Geef bij **Projectnaam** een naam op voor het project en klik op **Maken**.
4. Selecteer de knop **waarschuwingen** op de werkbalk en selecteer uw project in de lijst. U ziet het dashboard voor uw project. U kunt ook rechtstreeks naar het dashboard gaan met behulp van de URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

    ![Uw project selecteren in waarschuwingen](./media/mobile-services-enable-google-cloud-messaging/alert-new-project.png)
5. Noteer het **projectnummer** dat wordt vermeld op de tegel **Projectgegevens** van het dashboard. 

    ![Project-id](./media/mobile-services-enable-google-cloud-messaging/project-number.png)
6. Ga in het dashboard naar de tegel **API's** en selecteer **Naar API-overzicht**. 

    ![Link voor API-overzicht](./media/mobile-services-enable-google-cloud-messaging/go-to-api-overview.png)
7. Selecteer **API'S EN SERVICES INSCHAKELEN** op de pagina **API**. 

    ![Knop API's en services inschakelen](./media/mobile-services-enable-google-cloud-messaging/enable-api-services-button.png)
8. Zoek en selecteer **Google Cloud Messaging**. 

    ![Google Cloud Messaging zoeken en selecteren](./media/mobile-services-enable-google-cloud-messaging/search-select-gcm.png)
9. Als u Google Cloud Messaging wilt inschakelen voor het project, selecteert u **INSCHAKELEN**.

    ![Google Cloud Messaging inschakelen](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-button.png)
10. Selecteer **Referenties maken** op de werkbalk. 

    ![Knop Referenties maken](./media/mobile-services-enable-google-cloud-messaging/create-credentials-button.png)
11. Selecteer de link **API-sleutel maken** op de pagina **Referenties aan uw project toevoegen**. 

    ![Referenties toevoegen](./media/mobile-services-enable-google-cloud-messaging/api-key-button.png)    
12. Selecteer **Maken/Opslaan** op de pagina **API-sleutel**. In het volgende voorbeeld is de optie **IP-adressen** geselecteerd, en is **0.0.0.0/0** ingevoerd voor de toegestane IP-adressen. U moet de API-sleutel dan op de juiste manier beperken. 

    ![Knop API-sleutel maken](./media/mobile-services-enable-google-cloud-messaging/api-key-create-button.png)
13. Kopieer de **API-sleutel** naar het klembord en sla deze ergens op. 

    ![API-sleutel kopiëren](./media/mobile-services-enable-google-cloud-messaging/copy-api-key.png)
   
    Deze API-sleutelwaarde wordt door Azure gebruikt om zich bij GCM te verifiëren en om namens uw app pushmeldingen te verzenden. Als u terug wilt naar het dashboard van het project, gebruikt u deze URL: `https://console.developers.google.com/home/dashboard?project=<YOUR PROJECT NAME>`

