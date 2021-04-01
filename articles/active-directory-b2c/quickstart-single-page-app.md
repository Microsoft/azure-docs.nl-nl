---
title: 'Quickstart: Aanmelding voor een app met één pagina (SPA) instellen'
titleSuffix: Azure AD B2C
description: In deze quickstart voert u een voorbeeld van een toepassing met één pagina uit die Azure Active Directory B2C gebruikt voor aanmelding bij een account.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6471d1b5a5ad2b8ba34080ae1220872fa0e2e232
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93421053"
---
# <a name="quickstart-set-up-sign-in-for-a-single-page-app-using-azure-active-directory-b2c"></a>Quickstart: Aanmelding instellen voor een app met één pagina met behulp van Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) bevat functionaliteit voor identiteitsbeheer in de cloud ter bescherming van uw toepassing, bedrijf en klanten. Met Azure AD B2C zijn uw toepassingen in staat om zich met behulp van open-standaardprotocollen te verifiëren bij sociaalnetwerk- en Enterprise-accounts. In deze snelstart gebruikt u een toepassing met één pagina. Deze toepassing wordt gebruikt voor het aanmelden via een id-provider voor sociale netwerken en voor het aanroepen van een met Azure AD B2C beveiligde web-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Visual Studio Code](https://code.visualstudio.com/)
- [Node.js](https://nodejs.org/en/download/)
- Sociaal account van Facebook, Google of Microsoft
- Codevoorbeeld uit GitHub: [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa)

    U kunt [het ZIP-archief downloaden](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) of de opslagplaats klonen:

    ```console
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```

## <a name="run-the-application"></a>De toepassing uitvoeren

1. Start de server door de volgende opdrachten uit te voeren vanaf de Node.js-opdrachtprompt:

    ```console
    npm install && npm update
    npm start
    ```

    De server die is gestart door *server.js* geeft de poort waarop deze luistert weer:

    ```console
    Listening on port 6420...
    ```

1. Blader naar de URL van de toepassing. Bijvoorbeeld `http://localhost:6420`.

    ![Voorbeeld-app met één pagina weergegeven in de browser](./media/quickstart-single-page-app/sample-app-spa.png)

## <a name="sign-in-using-your-account"></a>Aanmelden met uw account

1. Selecteer **Aanmelden** om het gebruikerstraject te starten.
1. Azure AD B2C opent een aanmeldingspagina voor een fictief bedrijf met de naam 'Fabrikam' voor het voorbeeld van de web-app. Selecteer de knop van de id-provider voor sociale netwerken die u wilt gebruiken om u aan te melden via een id-provider voor sociale netwerken.

    ![De pagina Aanmelden of Registreren met de knoppen van de id-provider](./media/quickstart-single-page-app/sign-in-or-sign-up-spa.png)

    U moet zich verifiëren (aanmelden) met behulp van de referenties van uw sociaalnetwerkaccount en de toepassing autoriseren om informatie uit uw sociaalnetwerkaccount te lezen. Door toegang te verlenen, kan de toepassing profielgegevens van het sociaalnetwerkaccount ophalen, zoals uw naam en plaats.

1. Voltooi het aanmeldingsproces voor de id-provider.

## <a name="access-a-protected-api-resource"></a>Toegang tot een beveiligde API-resource

Klik op **API aanroepen** om de weergavenaam van de web-API als JSON-object te retourneren.

![Voorbeeldtoepassing in browser met de respons van de web-API](./media/quickstart-single-page-app/call-api-spa.png)

De voorbeeldtoepassing met één pagina bevat het toegangstoken van Azure AD in de aanvraag voor de beveiligde web-API-resource.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw Azure AD B2C-tenant gebruiken voor andere snelstarts of zelfstudies voor Azure AD B2C. Als u deze niet meer nodig hebt, kunt u [uw Azure AD B2C-tenant verwijderen](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een voorbeeld van een toepassing met één pagina gebruikt voor het volgende:

- Aanmelden met een id-provider voor sociale netwerken
- Een Azure AD B2C-gebruikersaccount maken (automatisch gemaakt bij het aanmelden)
- Een web-API aanroepen die is beveiligd door Azure AD B2C

Aan de slag met het maken van uw eigen Azure AD B2C-tenant.

> [!div class="nextstepaction"]
> [Een Azure Active Directory B2C-tenant maken in Azure Portal](tutorial-create-tenant.md)
