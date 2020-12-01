---
title: Share Point-bestanden-QnA Maker
description: Voeg beveiligde share point-gegevens bronnen aan uw Knowledge Base toe om de Knowledge Base te verrijken met vragen en antwoorden die kunnen worden beveiligd met Active Directory.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c231ac95841043e5576f064e683dd86d9695b108
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353184"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Een beveiligde share point-gegevens bron toevoegen aan uw Knowledge Base

Voeg beveiligde share point-gegevens bronnen op basis van de Cloud toe aan uw kennis database om de Knowledge Base te verrijken met vragen en antwoorden die kunnen worden beveiligd met Active Directory.

Wanneer u een beveiligd share point-document aan uw Knowledge Base toevoegt als QnA Maker Manager, moet u Active Directory machtigingen voor QnA Maker aanvragen. Zodra deze machtiging van de Active Directory Manager is ontvangen QnA Maker om toegang te krijgen tot share point, hoeft deze niet opnieuw te worden opgegeven. Elk volgend document toevoegen aan de Knowledge Base heeft geen autorisatie nodig als het zich in dezelfde share point-resource bevindt.

Als de QnA Maker Knowledge Base manager niet de Active Directory Manager is, moet u met de Active Directory Manager communiceren om dit proces te volt ooien.

## <a name="prerequisites"></a>Vereisten

* In de cloud gebaseerde share point-QnA Maker gebruikt Microsoft Graph voor machtigingen. Als uw share point on-premises is, kunt u niet extra heren uit share point omdat Microsoft Graph geen machtigingen kunt vaststellen.
* URL-indeling-QnA Maker ondersteunt alleen share point-url's die zijn gegenereerd voor delen en zijn van het formaat `https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Ondersteunde bestands typen toevoegen aan de Knowledge Base

U kunt alle door QnA Maker ondersteunde [Bestands typen](../index.yml) van een share point-site toevoegen aan uw Knowledge Base. Mogelijk moet u [machtigingen](#permissions) verlenen als de bron van het bestand is beveiligd.

1. Selecteer in de bibliotheek met de share point-site het weglatings menu van het bestand `...` .
1. Kopieer de URL van het bestand.

   ![Haal de URL van het share point-bestand op door het menu met het beletsel teken van het bestand te selecteren en de URL te kopiëren.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. In de QnA Maker Portal, op de pagina **instellingen** , voegt u de URL toe aan de Knowledge Base.

### <a name="images-with-sharepoint-files"></a>Installatie kopieën met share Point-bestanden

Als bestanden installatie kopieën bevatten, worden deze niet uitgepakt. U kunt de installatie kopie vanuit de QnA Maker portal toevoegen nadat het bestand is geëxtraheerd in QnA-paren.

Voeg de installatie kopie met de volgende afkortings syntaxis toe:

```markdown
![Explanation or description of image](URL of public image)
```

De tekst in de vier Kante haken, met `[]` uitleg over de afbeelding. De URL tussen haakjes, `()` , is de directe koppeling naar de afbeelding.

Wanneer u het QnA-paar in het interactieve test paneel test, wordt de afbeelding in het QnA Maker portal weer gegeven in plaats van de tekst voor de prijs verlaging. Hiermee wordt gecontroleerd of de installatie kopie openbaar kan worden opgehaald uit uw client toepassing.

## <a name="permissions"></a>Machtigingen

Het verlenen van machtigingen gebeurt wanneer een beveiligd bestand van een share Point-server wordt toegevoegd aan een Knowledge Base. Afhankelijk van hoe de share point is ingesteld en de machtigingen van de persoon die het bestand toevoegt, kan dit het volgende vereisen:

* geen extra stappen: de persoon die het bestand toevoegt, heeft alle benodigde machtigingen.
* stappen door zowel [Knowledge Base manager](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) als [Active Directory Manager](#active-directory-manager-grant-file-read-access-to-qna-maker).

Zie de stappen die hieronder worden weer gegeven.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Knowledge Base-beheer: share point-gegevens bron toevoegen in QnA Maker Portal

Wanneer de **QnA Maker Manager** een beveiligd share point-document aan een Knowledge Base toevoegt, initieert de Knowledge Base manager een aanvraag om toestemming die de Active Directory manager moet volt ooien.

De aanvraag begint met een pop-up om te verifiëren met een Active Directory-account.

![Gebruikers account verifiëren](../media/add-sharepoint-datasources/authenticate-user-account.png)

Zodra de QnA Maker Manager het account selecteert, ontvangt de Azure Active Directory-beheerder een melding dat de QnA Maker app (niet de QnA Maker Manager) toegang tot de share point-resource nodig heeft. De Azure Active Directory manager moet dit doen voor elke share point-resource, maar niet elk document in die resource.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active Directory-beheer: Lees toegang verlenen aan het bestand QnA Maker

De Active Directory Manager (niet de QnA Maker Manager) moet toegang verlenen tot QnA Maker om toegang te krijgen tot de share point-resource door [deze koppeling](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) te selecteren om de QnA Maker Portal share point Enter prise-app toestemming te geven om machtigingen voor bestanden te lezen.

![Azure Active Directory Manager verleent interactief machtigingen](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Toegang verlenen vanuit het Azure Active Directory-beheer centrum

1. De Active Directory Manager meldt zich aan bij de Azure Portal en opent **[bedrijfs toepassingen](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**.

1. Zoek naar `QnAMakerPortalSharePoint` de QnA Maker-app selecteren.

    [![Zoeken naar QnAMakerPortalSharePoint in de lijst met zakelijke apps](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Onder **beveiliging** gaat u naar **machtigingen**. Selecteer **toestemming geven voor de beheerder voor de organisatie**.

    [![Geverifieerde gebruiker voor Active Directory beheerder selecteren](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Selecteer een Sign-On account met machtigingen voor het verlenen van machtigingen voor de Active Directory.



<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker.

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64).

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes.

    * Remove the equal character, `=` from the end of the value.
    * Replace `/` with `_`.
    * Replace `+` with `-`.
    * Append `u!` to be beginning of the string.

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) or [updating a knowledge base](/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Samen werken aan uw Knowledge Base](../index.yml)