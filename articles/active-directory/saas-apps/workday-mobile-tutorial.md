---
title: 'Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met mobiele apps van Workday | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en mobiele apps van Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: ef1ca41f54a15554a04fa3edf608bb13f5fb3398
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182016"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met mobiele apps van Workday

In deze zelfstudie leert u hoe u Azure Active Directory (Azure AD), voorwaardelijke toegang en Intune integreert met de mobiele Workday-app. Wanneer u de mobiele Workday-app integreert met Microsoft, kunt u het volgende doen:

* Garanderen dat apparaten voldoen aan uw beleid voordat ze worden aangemeld.
* Besturingselementen toevoegen aan de mobiele Workday-app om ervoor te zorgen dat gebruikers veilig toegang krijgen tot bedrijfsgegevens. 
* In Azure AD beheren wie toegang heeft tot Workday.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij Workday.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

## <a name="prerequisites"></a>Vereisten

Aan de slag:

* Integreer Workday met Azure AD.
* Lees [Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Workday](./workday-tutorial.md).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u het beleid voor voorwaardelijke toegang van Azure AD en Intune met de mobiele Workday-app.

Voor het inschakelen van eenmalige aanmelding (SSO) kunt u de federatieve Workday-app configureren met Azure AD. Zie [Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Workday](./workday-tutorial.md) voor meer informatie.

> [!NOTE] 
> Workday biedt geen ondersteuning voor app-beveiligingsbeleid van Intune. U moet Mobile Device Management gebruiken om gebruik te maken van voorwaardelijke toegang.


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>Zorg ervoor dat gebruikers toegang hebben tot de mobiele Workday-app

Configureer Workday om toegang tot hun mobiele apps toe te staan. U moet de onderstaande beleidsregels configureren voor de mobiele Workday-app:

1. Open het rapport Domain Security Policies for Functional Area.
1. Selecteer het geschikte beveiligingsbeleid:
    * Mobile Usage - Android
    * Mobile Usage - iPad
    * Mobile Usage - iPhone
1. Selecteer **Machtigingen bewerken**.
1. Schakel het selectievakje **Weergeven of wijzigen** in om de beveiligingsgroepen toegang te geven tot beveiligbare items van het rapport of de taak.
1. Schakel het selectievakje **Ophalen of Plaatsen** in om de beveiligingsgroepen toegang te geven tot integratieacties en acties waarmee rapporten of taken worden beveiligd.

Activeer wijzigingen van het beveiligingsbeleid die in behandeling zijn door de taak **Activate Pending Security Policy Changes** uit te voeren.

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>De aanmeldingspagina openen in de browser van de mobiele Workday-app

Als u voorwaardelijke toegang wilt toepassen op de mobiele Workday-app, moet u de app openen in een externe browser. Schakel in **Configuratie tenant bewerken - Beveiliging** het selectievakje **Eenmalige aanmelding mobiele bedrijfseigen apps via een browser inschakelen** in. Hiervoor moet een door Intune goedgekeurde browser worden geïnstalleerd op het apparaat voor iOS en in het werkprofiel voor Android.

![Schermopname van de aanmelding bij de mobiele Workday-app via een browser.](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang instellen

Dit beleid is alleen van invloed op aanmelding bij een iOS- of Android-apparaat. Als u het beleid wilt uitbreiden naar alle platforms, selecteert u **Elk apparaat.** Voor dit beleid is het vereist dat het apparaat compatibel is met het beleid. Dit wordt gecontroleerd via Intune. Omdat Android werkprofielen heeft, kunnen gebruikers zich pas aanmelden bij Workday als ze zich aanmelden via hun werkprofiel en de app hebben geïnstalleerd via de Intune-bedrijfsportal. Voor iOS is een stap extra nodig om ervoor te zorgen dat dezelfde situatie van toepassing is.

Workday ondersteunt de volgende besturingselementen voor toegang:
* Meervoudige verificatie vereisen
* Vereisen dat het apparaat moet worden gemarkeerd als compatibel

De Workday-app biedt geen ondersteuning voor het volgende:
* Goedgekeurde client-apps vereisen
* Beleid voor app-beveiliging vereisen (preview)

Als u Workday wilt instellen als een beheerd apparaat, voert u de volgende stappen uit:

![Schermopname van de items Alleen beheerde apparaten en Cloud-apps of -acties.](./media/workday-tutorial/managed-devices-only.png)

1. Selecteer **Start** > **Microsoft Intune** > **Beleid voor voorwaardelijke toegang**. Selecteer vervolgens **Alleen beheerde apparaten**. 

1. Selecteer in **Alleen beheerde apparaten** onder **Naam** **Alleen beheerde apparaten** en selecteer vervolgens **Cloud-apps of -acties**.

1. Doe het volgende in **Cloud-apps of acties**:

    a. Schakel **Selecteer waarop dit beleid van toepassing is** over naar **Cloud-apps**.

    b. Kies bij **Invoegen** **Apps selecteren**.

    c. Kies **Workday** uit de lijst **Selecteren**.

    d. Selecteer **Gereed**.

1. Schakel **Beleid inschakelen** over naar **Aan**.

1. Selecteer **Opslaan**.

Voer in het deelvenster **Verlenen** de volgende stappen uit:

![Schermopname van de items Alleen beheerde apparaten en Verlenen.](./media/workday-tutorial/managed-devices-only-2.png)

1. Selecteer **Start** > **Microsoft Intune** > **Beleid voor voorwaardelijke toegang**. Selecteer vervolgens **Alleen beheerde apparaten**. 

1. Selecteer op de **Alleen beheerde apparaten** onder **Naam** **Alleen beheerde apparaten**. Onder **Toegangscontroles** selecteert u **Verlenen**.

1. Doe het volgende in **Verlenen**:

    a. Selecteer de besturingselementen die moeten worden afgedwongen voor **Toegang verlenen**.

    b. Selecteer **Vereisen dat het apparaat als conform is gemarkeerd**.

    c. Selecteer **Een van de geselecteerde besturingselementen vereisen**.

    d. Kies **Selecteren**.

1. Schakel **Beleid inschakelen** over naar **Aan**.

1. Selecteer **Opslaan**.

## <a name="set-up-device-compliance-policy"></a>Nalevingsbeleid voor apparaten instellen

Als u ervoor wilt zorgen dat iOS-apparaten alleen kunnen worden aangemeld via Workday onder beheer van Mobile Device Management, moet u de App Store-app blokkeren door **com.workday.workdayapp** toe te voegen aan de lijst met beperkte apps. Dit zorgt ervoor dat alleen apparaten toegang hebben tot Workday waarop Workday is geïnstalleerd via de bedrijfsportal. Via een browser hebben apparaten alleen toegang tot Workday als het apparaat wordt beheerd door Intune en er een beheerde browser wordt gebruikt.

![Schermopname van nalevingsbeleid voor iOS-apparaten.](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Configuratiebeleidsregels voor Intune-apps instellen

| Scenario | Sleutel-waardeparen |
|----------------------------------------------------------------------------------------   |-----------|
| Automatisch de velden Tenant en Webadres invullen voor:<br>● Workday op Android wanneer u Android inschakelt voor werkprofielen.<br>● Workday op iPad en iPhone.     | Gebruik deze waarden om uw tenant te configureren: <br>● Configuratiesleutel = `UserGroupCode`<br>● Waardetype = Tekenreeks <br>●   Configuratiewaarde = Naam van uw tenant. Voorbeeld: `gms`<br>Gebruik deze waarden om uw webadres te configureren:<br>● Configuratiesleutel = `AppServiceHost`<br>●   Waardetype = Tekenreeks<br>●    Configuratiewaarde = De basis-URL voor uw tenant. Voorbeeld: `https://www.myworkday.com`                                |   |
| Deze acties uitschakelen voor Workday op iPad en iPhone:<br>●    Knippen, kopiëren en plakken<br>●   Afdrukken                       | Stel de waarde (Booleaans) in op `False` voor deze sleutels om de functionaliteit uit te schakelen:<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| Schermopnamen uitschakelen voor Workday op Android. |Stel de waarde (Booleaans) in op `False` voor de sleutel `AllowScreenshots` om de functionaliteit uit te schakelen.|
| Voorgestelde updates uitschakelen voor uw gebruikers.|Stel de waarde (Booleaans) in op `False` voor de sleutel `AllowSuggestedUpdates` om de functionaliteit uit te schakelen.|
|De URL van de App Store aanpassen om mobiele gebruikers naar de App Store van uw keuze te leiden.|Gebruik deze waarden om de URL van de App Store te wijzigen:<br>● Configuratiesleutel = `AppUpdateURL`<br>● Waardetype = Tekenreeks<br> ●   Configuratiewaarde = URL van App Store|
|       |


## <a name="ios-configuration-policies"></a>Configuratiebeleid voor iOS

1. Ga naar [Azure Portal](https://portal.azure.com/) en meld u aan.
1. Zoek **Intune** of selecteer de widget in de lijst.
1. Ga naar **Client-apps** > **Apps** > **App-configuratiebeleid**. Selecteer vervolgens **+ Toevoegen** > **Beheerde apparaten**.
1. Voer een naam in.
1. Kies **iOS/iPadOS** onder **Platform**.
1. Kies onder **Gekoppelde app** de Workday for iOS-app die u hebt toegevoegd.
1. Selecteer **Configuratie-instellingen**. Selecteer **XML-gegevens invoeren** onder **Indeling van configuratie-instellingen**.
1. Hier ziet u een XML-voorbeeldbestand. Voeg de configuraties toe die u wilt toepassen. Vervang `STRING_VALUE` door de tekenreeks die u wilt gebruiken. Vervang `<true /> or <false />` door `<true />` of `<false />`. Als u geen configuratie toevoegt, fungeert dit voorbeeld alsof het is ingesteld op `True`.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. Selecteer **Toevoegen**.
1. Vernieuw de pagina en selecteer het zojuist gemaakte beleid.
1. Selecteer **Toewijzingen** en kies op wie u de app wilt toepassen.
1. Selecteer **Opslaan**.

## <a name="android-configuration-policies"></a>Configuratiebeleid voor Android

1. Ga naar [Azure Portal](https://portal.azure.com/) en meld u aan.
2. Zoek **Intune** of selecteer de widget in de lijst.
3. Ga naar **Client-apps** > **Apps** > **App-configuratiebeleid**. Selecteer vervolgens **+ Toevoegen** > **Beheerde apparaten**.
5. Voer een naam in. 
6. Kies onder **Platform** de optie **Android**.
7. Kies onder **Gekoppelde app** de Workday for Android-app die u hebt toegevoegd.
8. Selecteer **Configuratie-instellingen**. Selecteer **JSON-gegevens invoeren** onder **Indeling van configuratie-instellingen**.