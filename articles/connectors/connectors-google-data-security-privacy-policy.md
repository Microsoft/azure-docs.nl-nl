---
title: Beleid voor gegevens beveiliging en privacybeleid voor Google-connectors
description: Meer informatie over de invloed die Google-beveiligings-en privacybeleid hebben op Google-connectors, zoals Gmail, in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400754"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Beleid voor gegevens beveiliging en privacy voor Google connectors in Azure Logic Apps

Vanaf **1 mei 2020** kunnen wijzigingen worden aangebracht door het [beleid voor gegevens beveiliging en privacy](https://www.blog.google/technology/safety-security/project-strobe/) van Google, wat van invloed is op uw logische app-werk stromen die gebruikmaken van de [Gmail-connector](/connectors/gmail/). Als uw Logic apps de Gmail-connector gebruiken met een Gmail-consument account (e-mail adres dat eindigt op @gmail.com of @googlemail.com ), kunnen uw Logic apps alleen specifieke [door Google goedgekeurde triggers, acties en connectors](#approved-connectors)gebruiken.

> [!NOTE]
> Als uw Logic apps de Gmail-connector gebruiken met een G-Suite Business-account (e-mail adres met een aangepast domein), heeft dit geen invloed op uw Logic apps en gelden er geen beperkingen voor het gebruik van de Gmail-connector.

## <a name="affected-logic-apps"></a>Betrokken Logic apps

Als u Logic apps hebt die de Gmail-connector gebruiken, ontvangt u een e-mail over potentieel betrokken Logic apps. Vanaf **15 juni 2020** worden niet-compatibele werk stromen echter uitgeschakeld. U kunt een van de volgende acties uitvoeren:

* Werk de betreffende Logic apps bij door [de stappen in dit onderwerp te volgen](#update-affected-workflows). U moet een Google-client-app maken, die een client-ID en client geheim biedt die u voor verificatie gebruikt in uw Gmail-trigger of-actie.

* Werk de betreffende Logic apps bij zodat ze alleen de door [Google goedgekeurde connectors](#approved-connectors) gebruiken voordat u de uitgeschakelde Logic apps opnieuw inschakelt.

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Google-goedgekeurde connectors

Wanneer u onder dit beleid een Gmail-Consumer-account gebruikt, kunt u de Gmail-connector gebruiken met alleen specifieke door Google goedgekeurde Services, die kunnen worden gewijzigd. Onze engineering teams blijven werken met Google om meer services aan deze lijst toe te voegen. Hier volgen nu de door Google goedgekeurde triggers, acties en connectors die u kunt gebruiken in de werk stroom van de logische app met de Gmail-connector wanneer u een Gmail-consument account gebruikt:

* Logic Apps ingebouwde triggers en acties: batch, besturings element, gegevens bewerkingen, datum en tijd, plat bestand, vloeistof, aanvraag, schema, variabelen en XML

  Ingebouwde triggers en acties die niet zijn goedgekeurd door Google, zoals HTTP, Azure Functions, Azure Logic Apps en anderen, maken een logische app die niet voldoet aan de Gmail-connector, omdat de app vanaf elke locatie gegevens kan verzenden of ontvangen.

* Google services: Gmail, Google Calendar, Google Contacts, Google Drive, Google spread sheets en Google-taken

* Goedgekeurde micro soft-Services: Dynamics 365, Excel online, micro soft teams, Microsoft 365, OneDrive en share point online

* Connectors voor door de klant beheerde gegevens bronnen: FTP, RSS, SFTP, SMTP en SQL Server

## <a name="non-compliant-examples"></a>Voor beelden die niet voldoen aan het beleid

Hier volgen enkele voor beelden van het gebruik van de Gmail-connector met ingebouwde triggers en acties of beheerde connectors die niet door Google zijn goedgekeurd:

* Deze logische app maakt gebruik van de Gmail-connector met de ingebouwde HTTP-trigger:

  ![Niet-compatibele logische app-voor beeld 1](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  De logische app maakt ook gebruik van de Google Calendar Connector, die is goedgekeurd.

* Deze logische app maakt gebruik van de Gmail-connector met de Azure Blob Storage-connector:

  ![Niet-compatibele logische app-voor beeld 2](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* Deze logische app maakt gebruik van de Gmail-connector met de Twitter-connector:

  ![Niet-compatibele logische app-voor beeld 3](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

Raadpleeg de [technische documentatie van de Gmail-connector](/connectors/gmail/)voor de meest recente informatie.

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>Stappen voor betrokken Logic apps

Als u de Gmail-connector moet gebruiken met een Gmail-Consumer-account en Google niet-goedgekeurde connectors in een logische app, kunt u uw eigen Google-app maken voor persoonlijk of intern gebruik in uw onderneming. Voor dit scenario zijn de stappen op hoog niveau die u moet uitvoeren:

1. Maak een Google-client-app met behulp van de [Google API-console](https://console.developers.google.com).

1. Gebruik in uw Gmail-connector de client-ID en client Secret-waarden uit uw Google client-app.

Zie de [technische naslag documentatie van de Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application)voor meer informatie.

### <a name="create-google-client-app"></a>Google client-app maken

Als u een project voor uw client-app wilt instellen, gebruikt u de [wizard Google API-console](https://console.developers.google.com/start/api?id=gmail&credential=client_key) en volgt u de instructies. Of Raadpleeg de instructies in de [technische naslag documentatie van de Gmail-connector](/connectors/gmail/#authentication-and-bring-your-own-application)voor gedetailleerde stappen.

Wanneer u klaar bent, ziet uw scherm eruit als in dit voor beeld, behalve dat u beschikt over uw eigen **client-id** en **client Secret** -waarden, die u later in uw logische app gebruikt.

![Client-ID en client geheim voor uw Google client-app](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>Client-app-instellingen gebruiken in logische app

Voer de volgende stappen uit om de client-ID en het client geheim te gebruiken van uw Google client-app in uw Gmail-trigger of-actie:

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Als u een nieuwe Gmail-trigger of-actie wilt toevoegen en een volledig nieuwe verbinding wilt maken, gaat u verder met de volgende stap. Anders selecteert u in de trigger of actie van Gmail de optie **verbinding wijzigen**  >  **nieuwe toevoegen**, bijvoorbeeld:

   ![Selecteer verbinding wijzigen > nieuwe toevoegen](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. Geef uw verbindings gegevens op, bijvoorbeeld:

   ![Verbindings gegevens opgeven](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Verificatie type** | **Uw eigen toepassing meenemen** | Hiermee geeft u op dat u uw eigen client-app voor verificatie wilt gebruiken. |
   | **Client ID** | <*client-ID*> | De client-ID van uw Google client-app |
   | **Clientgeheim** | <*client-geheim*> | Het client geheim van uw Google client-app |
   ||||

1. Wanneer u klaar bent, selecteert u **Aanmelden**.

   Er wordt een pagina weer gegeven met de client-app die u hebt gemaakt. Als u een Gmail-Consumer-account gebruikt, kunt u een pagina krijgen die laat zien dat uw client-app niet is geverifieerd door Google en u wordt gevraagd om eerst toegang tot uw Google-account toe te staan.

   ![Vragen om toegang tot uw Google-account](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. Selecteer indien nodig **toestaan**.

   U kunt nu de Gmail-connector gebruiken zonder beperkingen in uw logische app.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Gmail-connector](/connectors/gmail/)

