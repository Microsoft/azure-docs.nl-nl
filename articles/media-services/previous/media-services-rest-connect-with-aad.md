---
title: Azure AD-verificatie gebruiken om toegang te krijgen tot Azure Media Services-API met REST | Microsoft Docs
description: Meer informatie over toegang tot Azure Media Services-API met Azure Active Directory-verificatie met behulp van REST.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: willzhan; johndeu
ms.openlocfilehash: a2b4e7bf03ebb1fbc197b78287cb50b3f421d713
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017304"
---
# <a name="use-azure-ad-authentication-to-access-the-media-services-api-with-rest"></a>Azure Active Directory-verificatie gebruiken voor toegang tot de Media Services-API met REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Wanneer u Azure AD-verificatie gebruikt met Azure Media Services, kunt u op een van de volgende twee manieren verifiëren:

- **Gebruikers verificatie** verifieert een persoon die de app gebruikt om te communiceren met Azure Media Services-resources. De interactieve toepassing moet eerst de gebruiker vragen om referenties. Een voor beeld is een beheer console-app die wordt gebruikt door geautoriseerde gebruikers om de versleutelings taken of live streamen te controleren. 
- **Service-Principal-verificatie** verifieert een service. Toepassingen die meestal gebruikmaken van deze verificatie methode zijn apps die daemon services, middelste laag Services of geplande taken uitvoeren, zoals web apps, functie-apps, Logic apps, Api's of micro Services.

    In deze zelf studie leert u hoe u de Azure AD- **Service-Principal** -verificatie gebruikt voor toegang tot AMS API met rest. 

    > [!NOTE]
    > **Service-Principal** is de aanbevolen best practice voor de meeste toepassingen die verbinding maken met Azure Media Services. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De verificatie gegevens ophalen van de Azure Portal
> * Het toegangs Token ophalen met behulp van postman
> * De **activa** -API testen met behulp van het toegangs token


> [!IMPORTANT]
> Media Services ondersteunt momenteel het Azure Access Control Services-verificatie model. Access Control verificatie wordt echter afgeschaft op 1 juni 2018. We raden u aan om zo snel mogelijk naar het Azure Active Directory-verificatiemodel te migreren.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.
- [Maak een Azure Media Services-account met behulp van de Azure Portal](media-services-portal-create-account.md).
- Bekijk de Access [Azure Media Services-API met het overzichts artikel van Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md) .
- Installeer de [postman](https://www.getpostman.com/) rest-client voor het uitvoeren van de rest-api's die in dit artikel worden weer gegeven. 

    In deze zelf studie gebruiken we **postman** , maar een rest hulp programma is geschikt. Enkele andere alternatieven: **Visual Studio Code** met de REST-invoegtoepassing of **Telerik Fiddler**. 

## <a name="get-the-authentication-information-from-the-azure-portal"></a>De verificatie gegevens ophalen van de Azure Portal

### <a name="overview"></a>Overzicht

Als u toegang wilt krijgen tot Media Services-API, moet u de volgende gegevens punten verzamelen.

|Instelling|Voorbeeld|Beschrijving|
|---|-------|-----|
|Azure Active Directory Tenant domein|microsoft.onmicrosoft.com|Azure AD als een STS-eind punt (Secure Token Service) wordt gemaakt met de volgende indeling: <https://login.microsoftonline.com/{your-ad-tenant-name.onmicrosoft.com}/oauth2/token> . Azure AD geeft een JWT-verbinding om toegang te krijgen tot bronnen (een toegangs token).|
|Eindpunt van de REST-API|<https://amshelloworld.restv2.westus.media.azure.net/api/>|Dit is het eind punt waartegen alle Media Services REST API-aanroepen in uw toepassing worden gedaan.|
|Client-ID (toepassings-ID)|f7fbbb29-a02d-4d91-bbc6-59a2579259d2|Client-ID van Azure AD-toepassing. De client-ID is vereist om het toegangs token op te halen. |
|Clientgeheim|+ mUERiNzVMoJGggD6aV1etzFGa1n6KeSlLjIq + Dbim0 =|Azure AD-toepassings sleutels (client geheim). Het client geheim is vereist om het toegangs token op te halen.|

### <a name="get-azure-active-directory-auth-info-from-the-azure-portal"></a>Azure Active Directory auth-gegevens ophalen van de Azure Portal

Voer de volgende stappen uit om de informatie op te halen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Navigeer naar uw AMS-exemplaar.
3. Selecteer **API-toegang**.
4. Klik op **verbinding maken met Azure Media Services API met Service-Principal**.

    ![Scherm afbeelding met de optie ' A P I Access ' die is geselecteerd in het menu ' Media Services ' en ' verbinding maken met Azure Media Services een P I met Service-Principal ' geselecteerd in het rechterdeel venster.](./media/connect-with-rest/connect-with-rest01.png)

5. Selecteer een bestaande **Azure AD-toepassing** of maak een nieuwe, (zie hieronder).

    > [!NOTE]
    > Voor een succes volle Azure media-aanvraag moet de aanroepende gebruiker een rol **Inzender** of **eigenaar** hebben voor het Media Services account dat toegang probeert te krijgen. Als u een uitzonde ring krijgt met de tekst ' de externe server heeft een fout geretourneerd: (401) niet gemachtigd, ' Zie [toegangs beheer](media-services-use-aad-auth-to-access-ams-api.md#access-control).

    Als u een nieuwe AD-App wilt maken, voert u de volgende stappen uit:
    
   1. Druk op **nieuwe maken**.
   2. Voer een naam in.
   3. Klik opnieuw op **nieuwe maken** .
   4. Klik op **Opslaan**.

      ![Scherm opname waarin het dialoog venster ' nieuwe maken ' wordt weer gegeven, waarbij het tekstvak ' app maken ' is gemarkeerd en de knop ' opslaan ' is geselecteerd.](./media/connect-with-rest/new-app.png)

      De nieuwe app wordt op de pagina weer gegeven.

6. Haal de **client-id** op (toepassings-id).
    
   1. Selecteer de toepassing.
   2. Haal de **client-id** op in het venster aan de rechter kant. 

      ![Scherm afbeelding met de geselecteerde ' Azure A D-app ' en ' toepassings beheer ' geselecteerd en de ' client I D ' gemarkeerd in het rechterdeel venster.](./media/connect-with-rest/existing-client-id.png)

7. De **sleutel** van de toepassing (client geheim) ophalen. 

   1. Klik op de knop **toepassing beheren** (Let op dat de client-id-informatie onder **toepassings-id** is). 
   2. Druk op de **toets**.
    
       ![Scherm opname waarin de knop toepassing beheren wordt weer gegeven, het deel venster ' toepassing I D ' is gemarkeerd en ' sleutels ' geselecteerd in het rechterdeel venster.](./media/connect-with-rest/manage-app.png)
   3. Genereer de app-sleutel (client geheim) door de **Beschrijving** in te vullen en te **verloopt** en op **Opslaan** te drukken.
    
       Wanneer u op de knop **Opslaan** hebt geklikt, wordt de sleutel waarde weer gegeven. Kopieer de sleutel waarde voordat u de Blade verlaat.

   ![API-toegang](./media/connect-with-rest/connect-with-rest03.png)

U kunt waarden voor AD-verbindings parameters toevoegen aan uw web.config-of app.config-bestand, zodat ze later in uw code kunnen worden gebruikt.

> [!IMPORTANT]
> De **client sleutel** is een belang rijk geheim en moet goed worden beveiligd in een sleutel kluis of worden versleuteld in de productie omgeving.

## <a name="get-the-access-token-using-postman"></a>Het toegangs Token ophalen met behulp van postman

In deze sectie wordt beschreven hoe u **postman** gebruikt om een rest API uit te voeren waarmee een JWT Bearer-token (toegangs token) wordt geretourneerd. Als u Media Services REST API wilt aanroepen, moet u de header Authorization toevoegen aan de aanroepen en de waarde ' Bearer *your_access_token*' toevoegen aan elke aanroep (zoals weer gegeven in de volgende sectie van deze zelf studie). 

1. Open **postman**.
2. Selecteer **POST**.
3. Voer de URL in die uw Tenant naam bevat met de volgende indeling: de naam van de Tenant moet eindigen op **. onmicrosoft.com** en de URL moet eindigen op **oauth2/token**: 

    `https://login.microsoftonline.com/{your-aad-tenant-name.onmicrosoft.com}/oauth2/token`

4. Selecteer het tabblad **kopteksten** .
5. Voer de gegevens van de **headers** in met behulp van het gegevens raster ' sleutel/waarde '. 

    ![Scherm opname van het tabblad ' kopteksten ' en de actie voor bulk bewerking geselecteerd.](./media/connect-with-rest/headers-data-grid.png)

    U kunt ook op de koppeling **bulk bewerking** rechts van het venster postman klikken en de volgende code plakken.

    ```javascript
    Content-Type:application/x-www-form-urlencoded
    Keep-Alive:true
    ```

6. Klik op het tabblad **hoofd tekst** .
7. Voer de informatie over de hoofd tekst in met behulp van het gegevens raster ' sleutel/waarde ' (Vervang de client-ID en geheime waarden). 

    ![Gegevens raster](./media/connect-with-rest/data-grid.png)

    U kunt ook op **bulk bewerking** klikken rechts van het venster postman en de volgende hoofd tekst plakken (Vervang de client-id en de geheime waarden):

    ```javascript
    grant_type:client_credentials
    client_id:{Your Client ID that you got from your Azure AD Application}
    client_secret:{Your client secret that you got from your Azure AD Application's Keys}
    resource:https://rest.media.azure.net
    ```

8. Druk op **Verzenden**.

    ![Scherm afbeelding met de tabbladen ' post ', ' kopteksten ' en ' hoofd tekst ' en ' access_token ' gemarkeerd en de knop ' verzenden ' gedetecteerd.](./media/connect-with-rest/connect-with-rest04.png)

Het geretourneerde antwoord bevat het **toegangs token** dat u nodig hebt om toegang te krijgen tot alle AMS-api's.

## <a name="test-the-assets-api-using-the-access-token"></a>De **activa** -API testen met behulp van het toegangs token

In deze sectie wordt beschreven hoe u **de Asset** -API opent met behulp van **postman**.

1. Open **postman**.
2. Selecteer **GET**.
3. Plak het REST API-eind punt (bijvoorbeeld https://amshelloworld.restv2.westus.media.azure.net/api/Assets)
4. Selecteer het tabblad **autorisatie** . 
5. Selecteer een **Bearer-token**.
6. Plak het token dat in de vorige sectie is gemaakt.

    ![Token ophalen](./media/connect-with-rest/connect-with-rest05.png)

    > [!NOTE]
    > De Postman UX kan verschillen tussen een Mac en een PC. Als de Mac-versie niet de optie ' Bearer-token ' heeft in de vervolg keuzelijst **verificatie** sectie, moet u de **autorisatie** -header hand matig toevoegen op de Mac-client.

   ![Authentication Header](./media/connect-with-rest/auth-header.png)

7. Selecteer **kopteksten**.
5. Klik in het venster postman op koppeling **bulk bewerking** .
6. Plak de volgende headers:

    ```javascript
    x-ms-version:2.19
    Accept:application/json
    Content-Type:application/json
    DataServiceVersion:3.0
    MaxDataServiceVersion:3.0
    ```

7. Druk op **Verzenden**.

Het geretourneerde antwoord bevat de assets in uw account.

## <a name="next-steps"></a>Volgende stappen

* Probeer deze voorbeeld code in [Azure AD-verificatie voor Azure Media Services toegang: beide via rest API](https://github.com/willzhan/WAMSRESTSoln)
* [Bestanden uploaden met .NET](media-services-dotnet-upload-files.md)
