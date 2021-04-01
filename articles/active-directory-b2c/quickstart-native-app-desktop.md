---
title: 'Snelstart: Aanmelden voor een bureaublad-app instellen'
titleSuffix: Azure AD B2C
description: In deze quickstart voert u een voorbeeld van een WPF-bureaubladtoepassing uit die Azure Active Directory B2C gebruikt voor aanmelding bij een account.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/12/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebed2f5e8664bd4336219f9387b8d27c8f3a1c59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78187299"
---
# <a name="quickstart-set-up-sign-in-for-a-desktop-app-using-azure-active-directory-b2c"></a>Snelstart: aanmelden instellen voor een bureaublad-app met Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) bevat functionaliteit voor identiteitsbeheer in de cloud ter bescherming van uw toepassing, bedrijf en klanten. Met Azure AD B2C kunnen uw toepassingen zich met behulp van open-standaardprotocollen te verifiëren bij sociale en enterpriseaccounts. In deze snelstart gebruikt u een WPF-desktoptoepassing (Windows Presentation Foundation) om u aan te melden via een id-provider voor sociale netwerken en voor het aanroepen van een met Azure AD B2C beveiligde web-API.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de **ASP.NET- en webontwikkelworkload**.
- Een sociaal account van Facebook, Google of Microsoft.
- [Download een zip-bestand](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip) of kloon de opslagplaats [Azure-Samples/active-directory-b2c-dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) van GitHub.

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
    ```

## <a name="run-the-application-in-visual-studio"></a>De toepassing uitvoeren in Visual Studio

1. In de projectmap van de voorbeeldtoepassing opent u de oplossing **active-directory-b2c-wpf.sln** in Visual Studio.
2. Druk op **F5** om fouten in de toepassing op te sporen.

## <a name="sign-in-using-your-account"></a>Aanmelden met uw account

1. Klik op **Aanmelden** om de werkstroom **Registreren of aanmelden** te starten.

    ![Schermafbeelding van de voorbeeld WPF-toepassing](./media/quickstart-native-app-desktop/wpf-sample-application.png)

    Het voorbeeld ondersteunt verschillende registratieopties. U kunt bijvoorbeeld een id-provider voor sociale netwerken gebruiken of een lokaal account maken met behulp van een e-mailadres. Voor deze quickstart gebruikt u een account van een id-provider voor sociale netwerken (Facebook, Google of Microsoft).


2. Azure AD B2C opent een aanmeldingspagina voor een fictief bedrijf genaamd Fabrikam voor het voorbeeld van de webtoepassing. Klik op de knop van de id-provider voor sociale netwerken die u wilt gebruiken om u aan te melden via een id-provider voor sociale netwerken.

    ![De pagina Aanmelden of Registreren met weergave van de id-providers](./media/quickstart-native-app-desktop/sign-in-or-sign-up-wpf.png)

    U moet zich verifiëren (aanmelden) met behulp van de referenties van uw sociaalnetwerkaccount en de toepassing autoriseren om informatie uit uw sociaalnetwerkaccount te lezen. Door toegang te verlenen, kan de toepassing profielgegevens van het sociaalnetwerkaccount ophalen, zoals uw naam en plaats.

2. Voltooi het aanmeldingsproces voor de id-provider.

    De profielgegevens van uw nieuwe account worden ingevuld met informatie uit uw sociale account.

## <a name="edit-your-profile"></a>Het profiel bewerken

Azure AD B2C biedt functionaliteit waarmee gebruikers hun profielen kunnen bijwerken. In de voorbeeldweb-app wordt een Azure AD B2C-gebruikersstroom voor profielbewerking gebruikt voor de werkstroom.

1. Klik in de menubalk van de toepassing op **Profiel bewerken** om het door u gemaakte profiel te bewerken.

    ![Knop Profiel bewerken gemarkeerd in WPF-voorbeeldapp](./media/quickstart-native-app-desktop/edit-profile-wpf.png)

2. Kies de id-provider die is gekoppeld aan het account dat u hebt gemaakt. Als u bijvoorbeeld Facebook als id-provider hebt gebruikt bij het maken van uw account, kiest u Facebook om de gekoppelde profielgegevens te wijzigen.

3. Wijzig uw **weergavenaam** of **plaats** en klik op **Doorgaan**.

    In het tekstvak *Tokengegevens* wordt een nieuw toegangstoken weergegeven. Als u de wijzigingen in uw profiel wilt controleren, kopieert u het toegangstoken en plakt u het in de tokendecoder https://jwt.ms.

## <a name="access-a-protected-api-resource"></a>Toegang tot een beveiligde API-resource

Klik op **API aanroepen** om een aanvraag te verzenden naar de beveiligde resource.

![API aanroepen](./media/quickstart-native-app-desktop/call-api-wpf.png)

De toepassing bevat het toegangstoken van Azure AD in de aanvraag voor de beveiligde web-API-resource. De web-API retourneert de weergavenaam uit het toegangstoken.

U hebt uw Azure AD B2C-gebruikersaccount gebruikt voor het geautoriseerd aanroepen van een web-API die met Azure AD B2C is beveiligd.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw Azure AD B2C-tenant gebruiken voor andere snelstarts of zelfstudies voor Azure AD B2C. Als u deze niet meer nodig hebt, kunt u [uw Azure AD B2C-tenant verwijderen](faq.md#how-do-i-delete-my-azure-ad-b2c-tenant).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een voorbeeld bureaubladtoepassing gebruikt om het volgende te doen:

* Aanmelden met een aangepaste aanmeldingspagina
* Aanmelden met een id-provider voor sociale netwerken
* Een Azure AD B2C-account maken
* Een web-API aanroepen die is beveiligd door Azure AD B2C

Aan de slag met het maken van uw eigen Azure AD B2C-tenant.

> [!div class="nextstepaction"]
> [Een Azure Active Directory B2C-tenant maken in Azure Portal](tutorial-create-tenant.md)
