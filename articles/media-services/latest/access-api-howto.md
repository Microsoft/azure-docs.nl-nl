---
title: Aan de slag met Azure AD-verificatie
description: Meer informatie over toegang tot Azure Active Directory-verificatie (Azure AD) om de Azure Media Services-API te gebruiken.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2f55d1dcc3364f119faab0c728f6e3b7f5ecbb06
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614935"
---
# <a name="get-credentials-to-access-media-services-api"></a>Referenties voor toegang tot Media Services-API ophalen

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Wanneer u Azure AD-verificatie gebruikt voor toegang tot de Azure Media Services-API, hebt u twee verificatie opties:

- **Service-Principal-verificatie** (aanbevolen)

    Verifieer een service. Toepassingen die meestal gebruikmaken van deze verificatie methode zijn apps die daemon services, middelste laag Services of geplande taken uitvoeren: Web apps, functie-apps, Logic apps, Api's of een micro service.
- **Gebruikersverificatie**

    Verifieer een persoon die de app gebruikt om te communiceren met Media Services-resources. De interactieve toepassing moet eerst de gebruiker vragen om referenties. Een voorbeeld is een beheerconsole-app die door geautoriseerde gebruikers wordt gebruikt om coderingstaken of livestreams te bewaken. 

In dit artikel worden de stappen beschreven voor het verkrijgen van referenties voor toegang tot Media Services-API. Kies een van de volgende tabbladen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen account hebt, kunt u beginnen met een [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/).
- Een Media Services-account. Zie [een Azure Media Services-account maken met behulp van de Azure Portal](create-account-howto.md)voor meer informatie.

## <a name="portal"></a>[Portal](#tab/portal/)

### <a name="api-access"></a>API-toegang

Op de pagina **API-toegang** kunt u de verificatie methode selecteren die u wilt gebruiken om verbinding te maken met de API. De pagina bevat ook de waarden die u nodig hebt om verbinding te maken met de API.

1. Selecteer uw Media Services account in de [Azure Portal](https://portal.azure.com/).
2. Selecteer de Blade **API-toegang** op de linkernavigatiebalk.
3. Selecteer onder **verbinding maken met Media Services-API** de versie van de Media Services-API waarmee u verbinding wilt maken (v3 is de nieuwste versie van de service).

### <a name="service-principal-authentication--recommended"></a>Service-Principal-verificatie (aanbevolen)

Verifieert een service met een Azure Active Directory-app (Azure AD) en een geheim. Dit wordt aanbevolen voor alle services in de middelste laag die de Media Services-API aanroepen. Voor beelden zijn Web Apps, functions, Logic Apps, Api's en micro Services. Dit is de aanbevolen verificatie methode.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Uw Azure AD-app en-geheim beheren

Met de sectie **uw Aad-app beheren en geheime** kunt u een nieuwe Azure AD-app selecteren of maken en een geheim genereren. Uit veiligheids overwegingen kan het geheim niet worden weer gegeven nadat de Blade is gesloten. De toepassing maakt gebruik van de toepassings-ID en het geheim voor verificatie om een geldig token voor Media Services te verkrijgen.

Zorg ervoor dat u voldoende machtigingen hebt om een toepassing te registreren bij uw Azure AD-Tenant en de toepassing toe te wijzen aan een rol in uw Azure-abonnement. Zie [vereiste machtigingen](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)voor meer informatie.

#### <a name="connect-to-media-services-api"></a>Verbinding maken met de Media Services-API

De **API Connect to Media Services** biedt u waarden die u gebruikt om verbinding te maken met uw Service Principal-toepassing. U kunt tekst waarden ophalen of de JSON-of XML-blokken kopiëren.

### <a name="user-authentication"></a>Gebruikersverificatie

Deze optie kan worden gebruikt voor het verifiëren van een werk nemer of lid van een Azure Active Directory die een app gebruikt om te communiceren met Media Services resources. De interactieve toepassing moet eerst de gebruiker vragen naar de referenties van de gebruiker. Deze verificatie methode mag alleen worden gebruikt voor beheer toepassingen.

#### <a name="connect-to-media-services-api"></a>Verbinding maken met de Media Services-API

Kopieer uw referenties om uw gebruikers toepassing te verbinden via de sectie **verbinding maken met Media Services-API** . U kunt tekst waarden ophalen of de JSON-of XML-blokken kopiëren.

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Volgende stappen

[Zelf studie: Video's uploaden, coderen en streamen met Media Services v3](stream-files-tutorial-with-api.md).
