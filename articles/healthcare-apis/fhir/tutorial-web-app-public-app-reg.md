---
title: Zelfstudie voor web-apps - Clienttoepassing instellen
description: In deze zelfstudie worden de stappen beschreven voor het registreren van een openbare toepassing ter voorbereiding van de implementatie van een web-app
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bf357dcc9c4790632a1131278edc41c13ac810b1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018167"
---
# <a name="client-application-registration"></a>Clienttoepassingen registreren
In de vorige zelfstudie hebt u de Azure-API for FHIR geïmplementeerd en ingesteld. Nu u Azure API for FHIR hebt ingesteld, gaan we een openbare clienttoepassing registreren. U kunt de volledige instructiegids voor het [registreren van een openbare client-app](register-public-azure-ad-client-app.md) lezen voor meer informatie of om problemen op te lossen, maar voor deze zelfstudie hebben we de belangrijkste stappen op een rij gezet.

1. Navigeren naar Azure Active Directory
1. Selecteer **App-registratie** --> **Nieuwe registratie**
1. Geef de toepassing een naam
1. Selecteer **Openbare client/systeemeigen (mobiel en desktop)** en stel de omleidings-URL in op `https://www.getpostman.com/oauth2/callback`.

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="Schermopname van het deelvenster voor het registreren van een toepassing, en een voorbeeld van een toepassingsnaam en omleidings-URL.":::

## <a name="client-application-settings"></a>Clienttoepassingsinstellingen

Nadat de clienttoepassing is geregistreerd, kopieert u de id van de toepassing (client) en de tenant-id van de pagina Overzicht. U hebt deze twee waarden later nodig voor toegang tot de client.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="Schermopname van het deelvenster met instellingen voor de clienttoepassing, met de id's van de toepassing en map gemarkeerd.":::

### <a name="connect-with-web-app"></a>Verbinding maken met de web-app

Als u [uw web-app hebt geschreven](tutorial-web-app-write-web-app.md) om verbinding te maken met de Azure-API voor FHIR, moet u ook de juiste verificatieopties instellen. 

1. Selecteer hiervoor **Verificatie** in het linkermenu onder **Beheren**. 

1. Selecteer **Web** om een nieuwe platformconfiguratie toe te voegen.

1. Stel de omleidings-URI in als voorbereiding voor wanneer u uw webtoepassing in het vierde deel van deze zelfstudie gaat maken. U kunt dit doen door `https://\<WEB-APP-NAME>.azurewebsites.net` toe te voegen aan de lijst met omleidings-URI's. Als u een andere naam kiest tijdens de stap waarin u [uw web-app schrijft](tutorial-web-app-write-web-app.md), moet u terugkeren en deze bijwerken.

1. Schakel de selectievakjes **Toegangstoken** en **Id-token** in.

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="Schermopname van de blade met verificatie-instellingen voor de app, met de stappen voor het toevoegen van een platform gemarkeerd.":::

## <a name="add-api-permissions"></a>API-machtigingen toevoegen

Nu u de juiste verificatie hebt ingesteld, stelt u de API-machtigingen in:

1. Selecteer **API-machtigingen** en klik op **Een machtiging toevoegen**.
1. Zoek onder **API's waarvan mijn organisatie gebruikmaakt** naar Azure Healthcare API's.
1. Selecteer **user_impersonation** en klik op **machtigingen toevoegen**.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="Schermopname van de blade API-machtigingen toevoegen, met de stappen voor het toevoegen van API-machtigingen gemarkeerd.":::

## <a name="next-steps"></a>Volgende stappen
U hebt nu een openbare clienttoepassing. In de volgende zelfstudie wordt u begeleid bij het testen en verkrijgen van toegang tot deze toepassing via Postman.

>[!div class="nextstepaction"]
>[Clienttoepassing testen in Postman](tutorial-web-app-test-postman.md)
