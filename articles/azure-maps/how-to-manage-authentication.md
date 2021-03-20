---
title: Verificatie beheren
titleSuffix: Azure Maps
description: Vertrouwd raken met Azure Maps-verificatie. Bekijk welke benadering het beste werkt in welk scenario. Meer informatie over het gebruik van de portal om verificatie-instellingen weer te geven.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 57e847116febcea66e1e3ac4ba131617463b6c94
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895763"
---
# <a name="manage-authentication-in-azure-maps"></a>Verificatie in Azure Maps beheren

Nadat u een Azure Maps-account hebt gemaakt, worden er een client-ID en-sleutels gemaakt ter ondersteuning van de verificatie van Azure Active Directory (Azure AD) en de verificatie van de gedeelde sleutel.

## <a name="view-authentication-details"></a>Verificatie gegevens weer geven

Nadat u een Azure Maps account hebt gemaakt, worden de primaire en secundaire sleutels gegenereerd. U wordt aangeraden een primaire sleutel als abonnements sleutel te gebruiken wanneer u [gebruikmaakt van gedeelde sleutel verificatie om Azure Maps aan te roepen](./azure-maps-authentication.md#shared-key-authentication). U kunt een secundaire sleutel gebruiken in scenario's zoals wijzigingen in de Rolling sleutel. Zie [verificatie in azure Maps](./azure-maps-authentication.md)voor meer informatie.

U kunt uw verificatie gegevens weer geven in de Azure Portal. In uw account selecteert u in het menu **instellingen** de optie **verificatie**.

> [!div class="mx-imgBorder"]
> ![Verificatiedetails](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Categorie en scenario detecteren

Afhankelijk van de toepassings behoeften zijn er specifieke paden om de toepassing te beveiligen. Azure AD definieert categorieën voor de ondersteuning van een breed scala aan verificatie stromen. Zie [toepassings categorieën](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories) om te begrijpen in welke categorie de toepassing past.

> [!NOTE]
> Zelfs als u gebruikmaakt van gedeelde sleutel verificatie, is het beter om de toepassing te beveiligen met categorieën en scenario's.

## <a name="determine-authentication-and-authorization"></a>Verificatie en autorisatie bepalen

De volgende tabel bevat een overzicht van de algemene scenario's voor verificatie en autorisatie in Azure Maps. De tabel bevat een vergelijking van de soorten beveiliging die elk scenario biedt.

> [!IMPORTANT]
> Micro soft raadt aan om Azure Active Directory (Azure AD) te implementeren met op rollen gebaseerd toegangs beheer van Azure (Azure RBAC) voor productie toepassingen.

| Scenario                                                                                    | Verificatie | Autorisatie | Ontwikkelings inspanning | Operationele inspanningen |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Vertrouwde daemon/niet-interactieve client toepassing](./how-to-secure-daemon-app.md)        | Gedeelde sleutel     | N.v.t.           | Normaal             | Hoog               |
| [Vertrouwde daemon/niet-interactieve client toepassing](./how-to-secure-daemon-app.md)        | Azure AD       | Hoog          | Laag                | Normaal             |
| [Toepassing met één pagina op internet met interactieve eenmalige aanmelding](./how-to-secure-spa-users.md) | Azure AD       | Hoog          | Gemiddeld             | Gemiddeld             |
| [Toepassing voor één pagina op internet met niet-interactieve aanmelding](./how-to-secure-spa-app.md)      | Azure AD       | Hoog          | Gemiddeld             | Gemiddeld             |
| [Webtoepassing met interactieve eenmalige aanmelding](./how-to-secure-webapp-users.md)          | Azure AD       | Hoog          | Hoog               | Gemiddeld             |
| [IoT-apparaat/invoer beperkt apparaat](./how-to-secure-device-code.md)                     | Azure AD       | Hoog          | Gemiddeld             | Gemiddeld             |

Met de koppelingen in de tabel gaat u naar gedetailleerde configuratie-informatie voor elk scenario.

## <a name="view-role-definitions"></a>Roldefinities weer geven

Ga naar **toegangs beheer (IAM)** om Azure-functies weer te geven die beschikbaar zijn voor Azure Maps. Selecteer **rollen** en zoek vervolgens naar rollen die beginnen met *Azure Maps*. Deze Azure Maps rollen zijn de rollen waaraan u toegang kunt verlenen.

> [!div class="mx-imgBorder"]
> ![Beschik bare rollen weer geven](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Roltoewijzingen weergeven

Ga naar **Access Control (IAM)** om gebruikers en apps weer te geven die toegang hebben gekregen voor Azure Maps. Hier selecteert u **roltoewijzingen** en filtert u vervolgens op **Azure Maps**.

> [!div class="mx-imgBorder"]
> ![Gebruikers en apps weer geven waaraan toegang is verleend](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Tokens aanvragen voor Azure Maps

Een token aanvragen bij het Azure AD-token eindpunt. Gebruik de volgende gegevens in uw Azure AD-aanvraag:

| Azure-omgeving      | Azure AD-token eindpunt             | Azure-Resource-ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Openbare Azure-cloud     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government Cloud | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Zie [verificatie scenario's voor Azure AD](../active-directory/develop/authentication-vs-authorization.md) en specifieke scenario's weer geven in de tabel met [scenario's](./how-to-manage-authentication.md#determine-authentication-and-authorization)voor meer informatie over het aanvragen van toegangs tokens van Azure AD voor gebruikers en service-principals.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure AD en Azure Maps Web SDK](./how-to-use-map-control.md)voor meer informatie.

Zoek de metrische gegevens over het API-gebruik voor uw Azure Maps account:
> [!div class="nextstepaction"]
> [Metrische gegevens over het gebruik weergeven](how-to-view-api-usage.md)

Bekijk de voor beelden die laten zien hoe u Azure AD integreert met Azure Maps:

> [!div class="nextstepaction"]
> [Voor beelden van Azure AD-verificatie](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)