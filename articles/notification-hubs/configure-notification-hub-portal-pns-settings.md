---
title: Pushmeldingen instellen in Azure Notification Hubs | Microsoft Docs
description: Meer informatie over het instellen van Azure Notification Hubs in de Azure Portal met behulp van PNS-instellingen (Platform Notification System).
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5dd1044895ba55d1fbc6be7f4f4a2d7f615daa16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94887260"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>Quickstart: Pushmeldingen instellen in een notification hub

Azure Notification Hubs biedt een push-engine die gebruiksvriendelijk is en kan worden geschaald. Gebruik Notification Hubs om meldingen te verzenden naar elk platform (iOS, Android, Windows, Baidu) en vanaf elke mogelijke back-end (in de cloud of on-premises). Zie [Wat is Azure Notification Hubs?](notification-hubs-push-notification-overview.md) voor meer informatie.

In deze quickstart gebruikt u de PNS-instellingen (Platform Notification System) in Notification Hubs om pushmeldingen in te stellen op meerdere platforms. In de quickstart ziet u de stappen die in de Azure Portal worden gezet. [Google Firebase Cloud Messaging](?tabs=azure-cli#google-firebase-cloud-messaging-fcm) bevat instructies voor het gebruik van de Azure CLI.

Maak nu een notification hub als u dat nog niet hebt gedaan. Zie [Een Azure notification hub maken met behulp van Azure Portal](create-notification-hub-portal.md) of [Een Azure notification hub maken met behulp van de Azure-CLI](create-notification-hub-azure-cli.md) voor meer informatie.

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Apple Push Notification Service (APNS) instellen:

1. Selecteer in de Azure Portal op de pagina **Notification Hub** **Apple (APNS)** in het menu aan de linkerkant.

1. Selecteer **Certificaat** of **Token** als **Verificatiemodus**.

   a. Als u **Certificaat** selecteert:
   * Selecteer het pictogram van het bestand en selecteer het *.p12*-bestand dat u wilt uploaden.
   * Voer een wachtwoord in.
   * Selecteer de modus **Sandbox**. Of selecteer de modus **Productie** als u pushmeldingen wilt verzenden naar gebruikers die uw app in de Store hebben gekocht.

     ![Schermopname van een configuratie van een APNS-certificaat in de Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. Als u **Token** selecteert:

   * Voer de waarden in voor **sleutel-id**, **bundel-id**, **team-id** en **token**.
   * Selecteer de modus **Sandbox**. Of selecteer de modus **Productie** als u pushmeldingen wilt verzenden naar gebruikers die uw app in de Store hebben gekocht.

     ![Schermafbeelding van een configuratie van een APNS-token in de Azure Portal](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

Zie [Pushmeldingen verzenden naar iOS-apps met Azure Notification Hubs](ios-sdk-get-started.md) voor meer informatie.

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ga als volgt te werk om pushmeldingen in te stellen voor Google FCM:

1. Selecteer in de Azure Portal op de pagina **Notification Hub** **Google (GCM/FCM)** in het menu aan de linkerkant.
2. Plak de **API-sleutel** voor het Google FCM-project dat u eerder hebt opgeslagen.
3. Selecteer **Opslaan**.

   ![Schermafbeelding die laat zien hoe u Notification Hubs configureert voor Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

Als u deze stappen hebt voltooid, ziet u een waarschuwing die aangeeft dat de notification hub is bijgewerkt. De knop **Opslaan** kan niet worden gekozen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U hebt de **API-sleutel** voor uw Google Firebase Cloud Messaging-project (FCM) nodig.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- Voor dit artikel is versie 2.0.67 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

### <a name="set-up-push-notifications-for-google-fcm"></a>Pushmeldingen instellen voor Google FCM

1. Gebruik de opdracht[az notification-hub credential gcm update](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) om uw Google API-sleutel toe te voegen aan uw notification hub.

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. De Android-app heeft een verbindingsreeks nodig om verbinding te maken met de notification hub.  Gebruik de opdracht [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) om het beschikbare toegangsbeleid weer te geven.  Gebruik de opdracht [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) om de verbindingsreeksen voor het toegangsbeleid te verkrijgen.  Geef de **primaryConnectionString** of **secondaryConnectionString** in de `--query`-parameter op om de primaire verbindingsreeks rechtstreeks te verkrijgen.

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. Gebruik de opdracht [az notification-hub test-send](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) om het verzenden van berichten naar de Android-app te testen.

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

Verkrijg Azure CLI-verwijzingen voor andere platforms met de opdracht [az notification-hub credential](/cli/azure/ext/notification-hub/notification-hub/credential).

Zie [Pushmeldingen naar Android-apparaten verzenden met Firebase](notification-hubs-android-push-notification-google-fcm-get-started.md) voor meer informatie over het verzenden van meldingen naar een Android-app.

---

## <a name="windows-push-notification-service"></a>Windows Push Notification Services

Windows Push Notification Services (WNS) instellen:

1. Selecteer in de Azure Portal op de pagina **Notification Hub** **Windows (WNS)** in het menu aan de linkerkant.
2. Voer waarden in voor **Pakket-SID** en **Beveiligingssleutel**.
3. Selecteer **Opslaan**.

   ![Schermafbeelding die de vakken Pakket-SID en Beveiligingssleutel weergeeft](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Zie [Meldingen verzenden naar UWP-apps met Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) voor meer informatie.

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Microsoft Push Notification Service voor Windows Phone

Ga als volgt te werk om Microsoft Push Notification Service (MPNS) voor Windows Phone in te stellen:

1. Selecteer in de Azure Portal op de pagina **Notification Hub** **Windows Phone (WNS)** in het menu aan de linkerkant.
1. Niet-geverifieerde of geverifieerde pushmeldingen inschakelen:

   a. Selecteer **Niet-geverifieerde push inschakelen** > **Opslaan** om niet-geverifieerde pushmeldingen in te schakelen.

      ![Schermafbeelding die laat zien hoe u niet-geverifieerde pushmeldingen inschakelt](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Geverifieerde pushmeldingen inschakelen:
      * Selecteer **Certificaat uploaden** op de werkbalk.
      * Selecteer het pictogram van het bestand en selecteer vervolgens het certificaatbestand.
      * Voer het wachtwoord voor het certificaat in.
      * Selecteer **OK**.
      * Selecteer **Opslaan** op de pagina **Windows Phone (MPNS)** .

Zie [Pushmeldingen verzenden naar Windows Phone-apps met Azure Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md) voor meer informatie.

## <a name="baidu-android-china"></a>Baidu (Android China)

Ga als volgt te werk om pushmeldingen in te stellen voor Baidu:

1. Selecteer in de Azure Portal op de pagina **Notification Hub** **Baidu (Android China)** in het menu aan de linkerkant.
2. Voer de **API-sleutel** in die u via de Baidu-console hebt gekregen in het Baidu Cloud Push-project.
3. Voer de **geheime sleutel** in die u hebt gekregen via de Baidu-console, in het Baidu Cloud Push-project.
4. Selecteer **Opslaan**.

    ![Schermafbeelding van Notification Hubs waarin de configuratie van Baidu (Android China) voor pushmeldingen wordt weergegeven](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

Als u deze stappen hebt voltooid, ziet u een waarschuwing die aangeeft dat de notification hub is bijgewerkt. De knop **Opslaan** kan niet worden gekozen.

Zie [Aan de slag met Notification Hubs met behulp van Baidu](notification-hubs-baidu-china-android-notifications-get-started.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u instellingen voor platformmeldingensystemen kunt configureren voor een notification hub in de Azure Portal.

Zie deze zelfstudies voor meer informatie over het pushen van meldingen naar verschillende platforms:

* [Pushmeldingen verzenden naar iOS-apps met Azure Notification Hubs](ios-sdk-get-started.md)
* [Meldingen verzenden naar Android-apparaten met Notification Hubs en Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Meldingen verzenden naar een UWP-app die wordt uitgevoerd op een Windows-apparaat](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [Meldingen verzenden naar een Windows Phone 8-app met behulp van MPNS](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Meldingen verzenden met behulp van Notification Hubs en Baidu Cloud Push](notification-hubs-baidu-china-android-notifications-get-started.md)
