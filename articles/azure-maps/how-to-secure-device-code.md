---
title: Invoer beperkt apparaat beveiligen met Azure AD en Azure Maps REST-Api's
titleSuffix: Azure Maps
description: Het configureren van een toepassing zonder browser die ondersteuning biedt voor aanmelding bij Azure AD en aanroepen van Azure Maps REST-Api's.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3833cbfd0802f334e482203d269984eb0e299797
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895627"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Een invoer beperkt apparaat beveiligen met Azure AD en Azure Maps REST-Api's

In deze hand leiding wordt beschreven hoe u open bare toepassingen of apparaten kunt beveiligen die geen beveiligde geheimen veilig kunnen opslaan of browser invoer accepteren. Deze typen toepassingen vallen onder de categorie IoT of Internet der dingen. Enkele voor beelden van deze toepassingen zijn: Smart TV-apparaten of sensor gegevens waarmee toepassingen worden verzonden. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Een toepassings registratie maken in azure AD

> [!NOTE]
> * **Vereisten lezen:** [scenario: bureau blad-app die web-api's aanroept](../active-directory/develop/scenario-desktop-overview.md)
> * In het volgende scenario wordt de code stroom van het apparaat gebruikt, waarbij geen webbrowser is vereist voor het verkrijgen van een token.

Maak de toepassing op basis van het apparaat in azure AD om aanmelden bij Azure AD in te scha kelen. Aan deze toepassing wordt toegang verleend tot Azure Maps REST-Api's.

1. Selecteer in het Azure Portal in de lijst met Azure-Services **Azure Active Directory**  >  **app-registraties**  >  **nieuwe registratie**.  

    > [!div class="mx-imgBorder"]
    > ![Appregistratie](./media/how-to-manage-authentication/app-registration.png)

2. Voer een **naam** in en kies **accounts in deze organisatie-Directory alleen** als **ondersteund account type**. Geef bij **omleidings-uri's** de **open bare client/systeem eigen (Mobile & bureau blad)** op en voeg deze vervolgens toe `https://login.microsoftonline.com/common/oauth2/nativeclient` aan de waarde. Zie Azure AD desktop-app voor [het aanroepen van web-api's: app-registratie](../active-directory/develop/scenario-desktop-app-registration.md)voor meer informatie. **Registreer** vervolgens de toepassing.

    > [!div class="mx-imgBorder"]
    > ![App-registratie Details voor naam en omleidings-URI toevoegen](./media/azure-maps-authentication/devicecode-app-registration.png)

3. Ga naar **verificatie** en Schakel **toepassing behandelen als een open bare client** in. Hiermee schakelt u verificatie op basis van een apparaatcode in met Azure AD.
    
    > [!div class="mx-imgBorder"]
    > ![App-registratie inschakelen als open bare client](./media/azure-maps-authentication/devicecode-public-client.png)

4.  Als u gedelegeerde API-machtigingen aan Azure Maps wilt toewijzen, gaat u naar de toepassing. Selecteer vervolgens de optie **API-machtigingen** om  >  **een machtiging toe te voegen**. Onder **api's die mijn organisatie gebruikt**, zoekt en selecteert u **Azure Maps**.

    > [!div class="mx-imgBorder"]
    > ![API-machtigingen voor apps toevoegen](./media/how-to-manage-authentication/app-permissions.png)

5. Schakel het selectie vakje in naast **toegang Azure Maps** en selecteer vervolgens **machtigingen toevoegen**.

    > [!div class="mx-imgBorder"]
    > ![App API-machtigingen selecteren](./media/how-to-manage-authentication/select-app-permissions.png)

6. Op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC) configureren voor gebruikers of groepen. Zie [toegang verlenen op basis van rollen voor gebruikers om Azure Maps](#grant-role-based-access-for-users-to-azure-maps).

7. Voeg code toe voor het ophalen van de token stroom in de toepassing, voor implementatie Details Zie [toestel code stroom](../active-directory/develop/scenario-desktop-acquire-token.md#device-code-flow). Bij het ophalen van tokens, verwijst u naar het bereik `user_impersonation` dat is geselecteerd in eerdere stappen.

> [!Tip]
> Gebruik micro soft Authentication Library (MSAL) om toegangs tokens te verkrijgen. Bekijk aanbevelingen voor [desktop-apps die web-api's aanroepen: code configuratie](../active-directory/develop/scenario-desktop-app-configuration.md)

8. Stel de HTTP-aanvraag met het verkregen token van Azure AD samen en verzend een aanvraag met een geldige HTTP-client.

### <a name="sample-request"></a>Voorbeeld aanvraag
Hier volgt een voor beeld van een aanvraag tekst voor het uploaden van een eenvoudige geofence die wordt weer gegeven als een cirkel geometrie met behulp van een middel punt en een RADIUS.

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 De onderstaande voorbeeld aanvraag is in geojson:
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>Voorbeeld antwoord:

Koppen
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

Organen
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Volgende stappen

Zoek de metrische gegevens over het API-gebruik voor uw Azure Maps account:
> [!div class="nextstepaction"]
> [Metrische gegevens over het gebruik weergeven](how-to-view-api-usage.md)