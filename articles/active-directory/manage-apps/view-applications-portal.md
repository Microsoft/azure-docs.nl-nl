---
title: 'Quickstart: De lijst met toepassingen weergeven die gebruikmaken van uw Azure Active Directory-tenant (Azure AD) voor identiteitsbeheer'
description: In deze quickstart gebruikt u Azure Portal om de lijst met toepassingen weer te geven die zijn geregistreerd voor het gebruik van uw Azure Active Directory-tenant (Azure AD) voor identiteitsbeheer.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: it-pro
ms.openlocfilehash: 4bf0353148a5f8474270b314a85d55c3cfd753ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257558"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Quickstart: De lijst met toepassingen weergeven die gebruikmaken van uw Azure Active Directory-tenant (Azure AD) voor identiteitsbeheer

Ga aan de slag met Azure AD als uw IAM-systeem (identiteits- en toegangsbeheer) voor de toepassingen die uw organisatie gebruikt. In deze quickstart ziet u de toepassingen, ook wel apps genoemd, die al zijn ingesteld voor het gebruik van uw Azure AD-tenant als id-provider (IdP).

## <a name="prerequisites"></a>Vereisten

Als u toepassingen wilt weergeven die zijn geregistreerd in uw Azure AD-Tenant, hebt u het volgende nodig:

- Een Azure-account. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

>[!IMPORTANT]
>U kunt het beste een niet-productieomgeving gebruiken om de stappen in deze quickstart te testen.

## <a name="find-the-list-of-applications-in-your-tenant"></a>De lijst met toepassing in uw tenant zoeken

De toepassingen die zijn geregistreerd bij uw Azure AD-tenant, worden weergegeven in de sectie **Bedrijfsapps** van Azure Portal.

De toepassingen weergeven die zijn geregistreerd in uw tenant:

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com).
2. Selecteer **Azure Active Directory** in het navigatiepaneel aan de linkerkant.
3. Selecteer **Bedrijfstoepassingen** in het deelvenster **Azure Active Directory**.
4. Selecteer in de vervolgkeuzelijst **Toepassingstype** de optie **Alle toepassingen** en kies **Toepassen**. Er wordt een willekeurige selectie uit uw tenanttoepassingen weergegeven.
5. Als u meer toepassingen wilt zien, selecteert u onderaan de lijst **Meer laden**. Als uw tenant meerdere toepassingen bevat, is het welllicht gemakkelijker om te zoeken naar een bepaalde toepassing in plaats van door de lijst te schuiven. Het zoeken naar een bepaalde toepassing wordt verderop in deze quickstart besproken.

## <a name="select-viewing-options"></a>Weergaveopties selecteren

Selecteer opties op basis van wat u zoekt.

1. U kunt de toepassingen weergeven op **Toepassingstype**, **Toepassingsstatus** en **Zichtbaarheid van toepassing**.
2. Kies onder **Toepassingstype** een van de volgende opties:
    - **Bedrijfstoepassingen** geeft niet-Microsoft-toepassingen weer.
    - **Microsoft-toepassingen** geeft Microsoft-toepassingen weer.
    - **Alle toepassingen** geeft zowel niet-Microsoft- als Microsoft-toepassingen weer.
3. Kies onder **Toepassingsstatus** een van de volgende opties: **Alle**, **Uitgeschakeld** of **Ingeschakeld**. De optie **Alle** omvat zowel de uitgeschakelde als de ingeschakelde toepassingen.
4. Kies onder **Zichtbaarheid van toepassing** de optie **Alle** of **Verborgen**. De optie **Verborgen** geeft toepassingen weer die zich in de tenant bevinden maar die niet zichtbaar zijn voor gebruikers.
5. Als u de gewenste opties hebt gekozen, selecteert u **Toepassen**.

## <a name="search-for-an-application"></a>Zoeken naar een toepassing

Ga als volgt te werk om naar een bepaalde toepassing te zoeken:

1. Selecteer in het menu **Toepassingstype** de optie **Alle toepassingen** en kies **Toepassen**.
2. Voer de naam in van de toepassing die u zoekt. Als de toepassing is toegevoegd aan uw Azure AD-tenant, wordt deze weergegeven in de zoekresultaten. In dit voorbeeld ziet u dat GitHub niet is toegevoegd aan de tenanttoepassingen.
    ![Voorbeeld toont een app die niet is toegevoegd aan de tenant](media/view-applications-portal/search-for-tenant-application.png)
3. Voer de eerste paar letters van een toepassingsnaam in. In dit voorbeeld ziet u dat alle toepassingen beginnen met **Verkoop**.
    ![Voorbeeld toont alle apps die beginnen met Sales](media/view-applications-portal/search-by-prefix.png)


> [!TIP]
> U kunt het beheer van apps automatiseren met behulp van de Graph API. Zie [App-beheer automatiseren met de Microsoft Graph API](/graph/application-saml-sso-configure-api).


## <a name="clean-up-resources"></a>Resources opschonen

U hebt geen nieuwe resources gemaakt in deze quickstart, dus u hoeft niets op te schonen.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het gebruik van Azure AD als de id-provider voor een app.
> [!div class="nextstepaction"]
> [Een app toevoegen](add-application-portal.md)