---
title: Azure AD Multi-Factor Authentication inschakelen
description: In deze zelfstudie leert u hoe u Azure AD Multi-Factor Authentication inschakelt voor een groep van gebruikers en hoe u het vragen om de secundaire factor bij het aanmelden kunt testen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62818ae5be079dc154e6d6faef4a8ebaae8fcd9d
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837869"
---
# <a name="tutorial-secure-user-sign-in-events-with-azure-ad-multi-factor-authentication"></a>Zelfstudie: Aanmeldingen van gebruikers beveiligen met Azure AD Multi-Factor Authentication

Multi-Factor Authentication (MFA) is een proces waarbij een gebruiker tijdens een aanmelding wordt gevraagd naar aanvullende vormen van identificatie. U kunt dit doen door een code in te voeren op de mobiele telefoon of door een vingerafdruk te scannen. Wanneer u een tweede vorm van verificatie vereist, neemt de beveiliging toe omdat deze aanvullende factor niet eenvoudig door een aanvaller kan worden verkregen of gedupliceerd.

Het beleid van Azure AD Multi-Factor Authentication en van Conditional Access bieden de flexibiliteit om MFA voor gebruikers in te schakelen tijdens specifieke aanmeldingen.

> [!IMPORTANT]
> Deze zelfstudie laat zien hoe een beheerder Azure AD Multi-Factor Authentication kan inschakelen.
>
> Neem voor hulp contact op met de helpdesk als uw IT-team de mogelijkheid om Azure AD Multi-Factor Authentication te gebruiken niet heeft ingeschakeld of als u problemen ondervindt tijdens het aanmelden.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Conditional Access-beleid maken om Azure AD Multi-Factor Authentication in te schakelen voor een groep van gebruikers
> * De beleidsvoorwaarden configureren die vragen om MFA
> * Het MFA-proces testen als een gebruiker

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, hebt u de volgende resources en machtigingen nodig:

* Een werkende Azure AD-tenant waarop minimaal een Azure AD Premium P1- of -proeflicentie is ingeschakeld.
    * [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) indien nodig.
* Een account met de bevoegdheden van een *globale beheerder*.
* Een niet-beheerder met een wachtwoord dat u kent, zoals *testuser*. In deze zelfstudie test u met dit account de Azure AD Multi-Factor Authentication voor eindgebruikers.
    * Als u een gebruiker wilt maken, raadpleegt u [Snelstart: Nieuwe gebruikers toevoegen aan Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) als een testgebruiker zonder beheerdersbevoegdheden een wachtwoord heeft dat u kent, en u een gebruiker moet maken.
* Een groep waarvan de niet-beheerder lid is, zoals *MFA-Test-Group*. In deze zelfstudie schakelt u Azure AD Multi-Factor Authentication voor deze groep in.
    * Zie [Een groep maken en leden toevoegen in Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md) als u een groep wilt maken.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

De aanbevolen manier om Azure AD Multi-Factor Authentication in te schakelen is door middel van Conditional Access-beleid. Met voorwaardelijke toegang kunt u beleid maken en definiëren waarmee op aanmeldingsgebeurtenissen wordt gereageerd en aanvullende acties worden aangevraagd voordat een gebruiker toegang tot een toepassing of service krijgt.

![Overzichtsdiagram van de werking van voorwaardelijke toegang om het aanmeldingsproces te beveiligen](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

Beleid voor voorwaardelijke toegang kan nauwkeurig en specifiek zijn, met als doel gebruikers in staat te stellen overal en altijd productief te zijn, maar ook uw organisatie te beschermen. In deze zelfstudie maken we basisbeleid voor voorwaardelijke toegang om te vragen om MFA wanneer een gebruiker zich aanmeldt bij Azure Portal. In een latere zelfstudie in deze serie configureert u Azure AD Multi-Factor Authentication met behulp van een op risico gebaseerd Conditional Access-beleid.

Maak eerst beleid voor voorwaardelijke toegang en wijs de testgroep met gebruikers als volgt toe:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een account met machtigingen op het niveau van *globale beheerder*.
1. Zoek en selecteer **Azure Active Directory** en kies in het menu aan de linkerkant **Beveiliging**.
1. Selecteer **Voorwaardelijke toegang** en kies vervolgens **+ Nieuw beleid**.
1. Voer een naam in voor het beleid, bijvoorbeeld *MFA-testfase*.
1. Kies onder **Toewijzingen** **Gebruikers en groepen** en klik vervolgens op het keuzerondje bij **Gebruikers en groepen selecteren**.
1. Schakel het selectie vakje in bij **Gebruikers en groepen** en klik op **Selecteren** om te door de beschikbare Azure AD-gebruikers en -groepen te bladeren.
1. Selecteer uw Azure AD-groep, bijvoorbeeld *MFA-Test-Group* en kies vervolgens **Selecteren**.

    [ ![Selecteer de Azure AD-groep die u wilt gebruiken voor het beleid voor voorwaardelijke toegang](media/tutorial-enable-azure-mfa/select-group-for-conditional-access-cropped.png) ](media/tutorial-enable-azure-mfa/select-group-for-conditional-access.png#lightbox)

1. Selecteer **Gereed** als u het beleid voor voorwaardelijke toegang op de groep wilt toepassen.

## <a name="configure-the-conditions-for-multi-factor-authentication"></a>De voorwaarden voor Multi-Factor Authentication configureren

Wanneer het beleid voor voorwaardelijke toegang is gemaakt en een testgroep van gebruikers is toegewezen, definieert u de cloud-apps of acties waarmee het beleid wordt geactiveerd. Deze cloud-apps of acties zijn de scenario's waarvoor u aanvullende verwerking nodig hebt, bijvoorbeeld om te vragen om MFA. U kunt bijvoorbeeld besluiten dat toegang tot een financiële toepassing of het gebruik van beheerhulpprogramma's vereist is als een extra vraag om verificatie.

In deze zelfstudie configureert u het beleid voor voorwaardelijke toegang zodanig dat MFA wordt vereist wanneer een gebruiker zich aanmeldt bij Azure Portal.

1. Selecteer **Cloud-apps of acties**. U kunt ervoor kiezen om het beleid voor voorwaardelijke toegang toe te passen op *Alle cloud-apps* of *Apps selecteren*. Als u flexibiliteit wilt bieden, kunt u ook bepaalde apps van het beleid uitsluiten.

    Voor deze zelfstudie kiest u op de pagina *Opnemen* het keuzerondje bij **Apps selecteren**.

1. Kies **Selecteren** en blader vervolgens door de lijst met beschikbare aanmeldingsgebeurtenissen die kunnen worden gebruikt.

    Voor deze zelfstudie kiest u **Microsoft Azure Management** zodat het beleid geldig is voor aanmeldingsgebeurtenissen bij Azure Portal.

1. Als u de geselecteerde apps wilt toepassen, kiest u **Selecteren** en vervolgens **Gereed**.

    ![Selecteer de Microsoft Azure Management-app die u wilt toevoegen in het beleid voor voorwaardelijke toegang](media/tutorial-enable-azure-mfa/select-azure-management-app.png)

Met besturingselementen voor toegang kunt u de vereisten definiëren die een gebruiker moet hebben om toegang te krijgen, zoals een goedgekeurde client-app of het gebruik van een apparaat dat hybride Azure AD-gekoppeld is. In deze zelfstudie configureert u de besturingselementen voor toegang om MFA te vereisen tijdens een aanmeldingsgebeurtenis bij Azure Portal.

1. Kies onder *Besturingselementen voor toegang* de optie **Verlenen** en controleer of het keuzerondje **Toegang verlenen** is geselecteerd.
1. Schakel het selectievakje voor **Multi-Factor Authentication vereisen** in en kies **Selecteren**.

Beleidsregels voor voorwaardelijke toegang kunnen worden ingesteld op *Alleen rapporteren* als u wilt weten wat de invloed van de configuratie op gebruikers is, of op *Uit* als u het beleid nu niet wilt gebruiken. Als een testgroep met gebruikers voor deze zelfstudie is gebruikt, kunt u het beleid inschakelen en vervolgens Azure AD Multi-Factor Authentication testen.

1. Stel de wisselknop *Beleid inschakelen* in op **Aan**.
1. Selecteer **Maken** om het beleid voor voorwaardelijke toegang toe te passen.

## <a name="test-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication testen

U gaat nu uw beleid voor Conditional Access en Azure AD Multi-Factor Authentication in werking zien. Meld u eerst aan bij een resource waarvoor geen MFA is vereist:

1. Open een nieuw browservenster in de InPrivate- of incognitomodus en blader naar [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)
1. Meld u aan als testgebruiker die geen beheerder is, bijvoorbeeld *testuser*. U wordt niet gevraagd om MFA te voltooien.
1. Sluit het browservenster.

Meld u aan bij Azure Portal. Als Azure Portal in het Conditional Access-beleid zodanig is geconfigureerd dat extra verificatie is vereist, ontvangt u een verzoek van Azure AD Multi-Factor Authentication.

1. Open een nieuw browservenster in de InPrivate- of incognitomodus en blader naar [https://portal.azure.com](https://portal.azure.com).
1. Meld u aan als testgebruiker die geen beheerder is, bijvoorbeeld *testuser*. U moet zich registreren voor Azure AD Multi-Factor Authentication en het gaan gebruiken. Volg de aanwijzingen om het proces te voltooien en te controleren of u zich hebt aangemeld bij Azure Portal.

    ![Volg de aanwijzingen in de browser en klik vervolgens op uw geregistreerde Multi-Factor Authentication-prompt om u aan te melden](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

1. Sluit het browservenster.

## <a name="clean-up-resources"></a>Resources opschonen

Als u het Conditional Access-beleid niet meer wilt gebruiken om Azure AD Multi-Factor Authentication in te schakelen als onderdeel van deze zelfstudie, verwijdert u het beleid met behulp van de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek en selecteer **Azure Active Directory** en kies in het menu aan de linkerkant **Beveiliging**.
1. Selecteer **Voorwaardelijke toegang** en kies vervolgens het beleid dat u hebt gemaakt, bijvoorbeeld *MFA-testfase*
1. Kies **Verwijderen** en bevestig dat u het beleid wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure AD Multi-Factor Authentication ingeschakeld met behulp van een Conditional Access-beleid voor een geselecteerde groep van gebruikers. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een Conditional Access-beleid maken om Azure AD Multi-Factor Authentication in te schakelen voor een groep van Azure AD-gebruikers
> * De beleidsvoorwaarden configureren die vragen om MFA
> * Het MFA-proces testen als een gebruiker

> [!div class="nextstepaction"]
> [Wachtwoord terugschrijven inschakelen voor self-service voor wachtwoordherstel (SSPR)](./tutorial-enable-sspr-writeback.md)