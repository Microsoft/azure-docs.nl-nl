---
title: 'Quickstart: Aanmelding met Microsoft toevoegen aan een Android-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart leert u hoe u met Android-toepassingen een API kunt aanroepen waarvoor toegangstokens zijn vereist die zijn verstrekt door het Microsoft-identiteitsplatform.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/15/2019
ms.author: marsma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:Android
ms.openlocfilehash: 49c69a79b4841f13623c9e6ab3daea72dd7fde26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583651"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Snelstart: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een Android-app

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers kunnen worden aangemeld met een Android-toepassing, en een toegangstoken kunnen krijgen om de Microsoft Graph API aan te roepen. 

Zie [Hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.

Toepassingen moeten worden voorgesteld door een app-object in Azure Active Directory, zodat het Microsoft identity platform uw toepassing van tokens kan voorzien.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Android Studio
* Android 16+

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> Voeg voor het voorbeeld code in deze Snelstartgids een **omleidings-URI** toe die compatibel is met de auth Broker.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-android/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken
>
> ### <a name="step-2-download-the-project"></a>Stap 2: Het project downloaden
> [!div class="sxs-lookup" renderon="portal"]
> Voer het project uit met Android Studio.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip)
>
> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Stap 3: Uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app en is klaar om te worden uitgevoerd.
> De voorbeeld-app wordt gestart op het scherm **Modus voor één account**. Er wordt standaard een standaardbereik **user.read** verstrekt. Dit wordt gebruikt tijdens het lezen van uw eigen profielgegevens tijdens de aanroep van de Microsoft Graph API. De URL voor de aanroep van de Microsoft Graph API wordt standaard verstrekt. U kunt ze allebei desgewenst wijzigen.
>
> ![Gebruik van MSAL-voorbeeld-app met één account of meerdere accounts](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Gebruik het app-menu om tussen modi met één of meerdere accounts te schakelen.
>
> Meld u in de modus voor één account aan met een werk- of thuisaccount:
>
> 1. Selecteer **Graafgegevens interactief ophalen** om de gebruiker om referenties te vragen. U ziet de uitvoer van de aanroep naar de Microsoft Graph API onder in het scherm.
> 2. Nadat u bent aangemeld, selecteert u **Graafgegevens op de achtergrond ophalen** om een aanroep naar de Microsoft Graph API uit te voeren zonder dat de gebruiker opnieuw om referenties wordt gevraagd. U ziet de uitvoer van de aanroep naar de Microsoft Graph API onder in het scherm.
>
> In de modus voor meerdere accounts kunt u dezelfde stappen herhalen.  Daarnaast kunt u het aangemelde account verwijderen, waardoor ook de tokens in de cache voor dat account worden verwijderd.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> ## <a name="step-1-get-the-sample-app"></a>Stap 1: De voorbeeld-app downloaden
>
> [Download de code](https://github.com/Azure-Samples/ms-identity-android-java/archive/master.zip).
>
> ## <a name="step-2-run-the-sample-app"></a>Stap 2: De voorbeeld-app uitvoeren
>
> Selecteer de emulator of het fysieke apparaat in de vervolgkeuzelijst **Beschikbare apparaten** in Android Studio en voer de app uit.
>
> De voorbeeld-app wordt gestart op het scherm **Modus voor één account**. Er wordt standaard een standaardbereik **user.read** verstrekt. Dit wordt gebruikt tijdens het lezen van uw eigen profielgegevens tijdens de aanroep van de Microsoft Graph API. De URL voor de aanroep van de Microsoft Graph API wordt standaard verstrekt. U kunt ze allebei desgewenst wijzigen.
>
> ![Gebruik van MSAL-voorbeeld-app met één account of meerdere accounts](./media/quickstart-v2-android/quickstart-sample-app.png)
>
> Gebruik het app-menu om tussen modi met één of meerdere accounts te schakelen.
>
> Meld u in de modus voor één account aan met een werk- of thuisaccount:
>
> 1. Selecteer **Graafgegevens interactief ophalen** om de gebruiker om referenties te vragen. U ziet de uitvoer van de aanroep naar de Microsoft Graph API onder in het scherm.
> 2. Nadat u bent aangemeld, selecteert u **Graafgegevens op de achtergrond ophalen** om een aanroep naar de Microsoft Graph API uit te voeren zonder dat de gebruiker opnieuw om referenties wordt gevraagd. U ziet de uitvoer van de aanroep naar de Microsoft Graph API onder in het scherm.
>
> In de modus voor meerdere accounts kunt u dezelfde stappen herhalen.  Daarnaast kunt u het aangemelde account verwijderen, waardoor ook de tokens in de cache voor dat account worden verwijderd.

## <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
![Schermafbeelding van de voorbeeld-app](media/quickstart-v2-android/android-intro.svg)


De code is ingedeeld in fragmenten. Deze laten zien hoe u een MSAL-app voor één en meerdere accounts schrijft. De codebestanden zijn als volgt ingedeeld:

| File  | Demonstreert  |
|---------|---------|
| MainActivity | Beheert de gebruikersinterface |
| MSGraphRequestWrapper  | Roept de Microsoft Graph API aan met behulp van het token dat door MSAL is verschaft |
| MultipleAccountModeFragment  | Initialiseert een toepassing met meerdere accounts, laadt een gebruikersaccount en haalt een token op om de Microsoft Graph API aan te roepen |
| SingleAccountModeFragment | Initialiseert een toepassing met één account, laadt een gebruikersaccount en haalt een token op om de Microsoft Graph API aan te roepen |
| res/auth_config_multiple_account.json  | Het configuratiebestand voor meerdere accounts |
| res/auth_config_single_account.json  | Het configuratiebestand voor één account |
| Gradle Scripts/build.grade (Module:app) | De afhankelijkheden van de MSAL-bibliotheek worden hier toegevoegd |

Deze bestanden worden nu in meer detail bekeken, met name de voor MSAL specifieke code.

### <a name="adding-msal-to-the-app"></a>MSAL toevoegen aan de app

MSAL ([com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal)) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt voor toegang tot een API die wordt beveiligd door het Microsoft identity platform. De bibliotheek wordt door Gradle 3.0+ geïnstalleerd wanneer u het volgende aan **Gradle Scripts** > **build.gradle (Module: app)** onder **afhankelijkheden** toevoegt:

```gradle
implementation 'com.microsoft.identity.client:msal:2.+'
```

U kunt dit zien in het voorbeeldproject in build.gradle (Module: app):

```java
dependencies {
    ...
    implementation 'com.microsoft.identity.client:msal:2.+'
    ...
}
```

Hiermee wordt Gradle geïnstrueerd om MSAL van Maven Central te downloaden en te compileren.

### <a name="msal-imports"></a>MSAL-importbewerkingen

De importbewerkingen die relevant zijn voor de MSAL-bibliotheek zijn `com.microsoft.identity.client.*`.  U ziet bijvoorbeeld `import com.microsoft.identity.client.PublicClientApplication;`. Dit is de naamruimte voor de klasse `PublicClientApplication`, die uw openbare clienttoepassing voorstelt.

### <a name="singleaccountmodefragmentjava"></a>SingleAccountModeFragment.java

Dit bestand laat zien hoe u een MSAL-app met één account maakt en een Microsoft Graph API aanroept.

Apps met één account worden slechts door één gebruiker gebruikt.  U hebt bijvoorbeeld slechts één account waarmee u zich aanmeldt bij uw toewijzings-app.

#### <a name="single-account-msal-initialization"></a>MSAL-initialisatie met één account

In `auth_config_single_account.json`, in `onCreateView()`, wordt één account, `PublicClientApplication`, gemaakt met behulp van de configuratie-informatie die is opgeslagen in het bestand `auth_config_single_account.json`.  Op deze manier initialiseert u de MSAL-bibliotheek voor gebruik in een MSAL-app met één account:

```java
...
// Creates a PublicClientApplication object with res/raw/auth_config_single_account.json
PublicClientApplication.createSingleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_single_account,
        new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(ISingleAccountPublicClientApplication application) {
                /**
                 * This test app assumes that the app is only going to support one account.
                 * This requires "account_mode" : "SINGLE" in the config json file.
                 **/
                mSingleAccountApp = application;
                loadAccount();
            }

            @Override
            public void onError(MsalException exception) {
                displayError(exception);
            }
        });
```

#### <a name="sign-in-a-user"></a>Een gebruiker aanmelden

In `SingleAccountModeFragment.java` bevindt de code voor het aanmelden van een gebruiker zich in `initializeUI()`, in de `signInButton`-click-handler.

Roep `signIn()` aan voordat u tokens gaat ophalen. `signIn()` gedraagt zich alsof `acquireToken()` wordt aangeroepen, wat resulteert in een interactieve prompt voor de gebruiker om zich aan te melden.

Het aanmelden van een gebruiker is een asynchrone bewerking. Er wordt een callback doorgegeven die de Microsoft Graph API aanroept en de gebruikersinterface bijwerkt zodra de gebruiker zich aanmeldt:

```java
mSingleAccountApp.signIn(getActivity(), null, getScopes(), getAuthInteractiveCallback());
```

#### <a name="sign-out-a-user"></a>Een gebruiker afmelden

In `SingleAccountModeFragment.java` bevindt de code voor het afmelden van een gebruiker zich in `initializeUI()`, in de `signOutButton`-click-handler.  Het afmelden van een gebruiker is een asynchrone bewerking. Als u de gebruiker afmeldt, wordt ook de tokencache voor dat account gewist. Er wordt een callback gemaakt om de gebruikersinterface bij te werken nadat het gebruikersaccount is afgemeld:

```java
mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
    @Override
    public void onSignOut() {
        updateUI(null);
        performOperationOnSignOut();
    }

    @Override
    public void onError(@NonNull MsalException exception) {
        displayError(exception);
    }
});
```

#### <a name="get-a-token-interactively-or-silently"></a>Interactief of op de achtergrond een token ophalen

Als u de gebruiker zo min mogelijk prompts wilt presenteren, ontvangt u normaal gesproken een token op de achtergrond. Als er een fout optreedt, kunt u interactief een token verkrijgen. De eerste keer dat de app `signIn()` aanroept, fungeert het effectief als een aanroep `acquireToken()`, waarmee de gebruiker om referenties wordt gevraagd.

Bepaalde situaties waarin de gebruiker kan worden gevraagd een account te selecteren, referenties in te voeren of toestemming te geven voor de machtigingen die uw app heeft aangevraagd, zijn:

* De eerste keer dat een gebruiker zich bij de toepassing aanmeldt
* Als gebruiker hun wachtwoord opnieuw instellen, moeten ze hun referenties invoeren
* Als de toestemming is ingetrokken
* Als uw app expliciet toestemming vereist
* Wanneer via de toepassing voor het eerst toegang wordt aangevraagd tot een resource
* Wanneer MFA of ander beleid voor voorwaardelijke toegang is vereist

De code voor het interactief ophalen van een token, dat wil zeggen met de gebruikersinterface waarbij de gebruiker is betrokken, bevindt zich in `SingleAccountModeFragment.java`, in `initializeUI()`, in de `callGraphApiInteractiveButton`-click-handler:

```java
/**
 * If acquireTokenSilent() returns an error that requires an interaction (MsalUiRequiredException),
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your Single Sign-On refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mSingleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Als de gebruiker al is aangemeld, staat `acquireTokenSilentAsync()` toe tokens op de achtergrond aanvragen, zoals getoond in `initializeUI()`, in de `callGraphApiSilentButton`-click-handler:

```java
/**
 * Once you've signed the user in,
 * you can perform acquireTokenSilent to obtain resources without interrupting the user.
 **/
  mSingleAccountApp.acquireTokenSilentAsync(getScopes(), AUTHORITY, getAuthSilentCallback());
```

#### <a name="load-an-account"></a>Een account laden

De code voor het laden van een account bevindt zich in `SingleAccountModeFragment.java` in `loadAccount()`.  Het laden van het gebruikers account is een asynchrone bewerking, dus af te handelen callbacks wanneer het account wordt geladen, gewijzigd of wanneer een fout optreedt, worden doorgegeven aan MSAL.  De volgende code verwerkt ook `onAccountChanged()`, wat plaatsvindt wanneer een account wordt verwijderd, de gebruiker naar een ander account overschakelt, enzovoort.

```java
private void loadAccount() {
    ...

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }

        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
```

#### <a name="call-microsoft-graph"></a>Microsoft Graph aanroepen

Wanneer een gebruiker is aangemeld, wordt de aanroep naar Microsoft Graph uitgevoerd via een HTTP-aanvraag door `callGraphAPI()`, die in `SingleAccountModeFragment.java` is gedefinieerd. Deze functie is een wrapper waarmee het voorbeeld wordt vereenvoudigd door bepaalde taken uit te voeren, zoals het ophalen van het toegangstoken van de `authenticationResult`, het verpakken van de aanroep in de MSGraphRequestWrapper en het weergeven van de resultaten van de aanroep.

```java
private void callGraphAPI(final IAuthenticationResult authenticationResult) {
    MSGraphRequestWrapper.callGraphAPIUsingVolley(
            getContext(),
            graphResourceTextView.getText().toString(),
            authenticationResult.getAccessToken(),
            new Response.Listener<JSONObject>() {
                @Override
                public void onResponse(JSONObject response) {
                    /* Successfully called graph, process data and send to UI */
                    ...
                }
            },
            new Response.ErrorListener() {
                @Override
                public void onErrorResponse(VolleyError error) {
                    ...
                }
            });
}
```

### <a name="auth_config_single_accountjson"></a>auth_config_single_account.json

Dit is het configuratiebestand voor een MSAL-app die gebruikmaakt van één account.

Zie [Inzicht krijgen in het Android MSAL-configuratiebestand ](msal-configuration.md) voor een uitleg van deze velden.

Let op de aanwezigheid van `"account_mode" : "SINGLE"`, waarmee deze app wordt geconfigureerd voor het gebruik van één account.

`"client_id"` is vooraf geconfigureerd voor het gebruik van een app-objectregistratie die door Microsoft wordt onderhouden.
`"redirect_uri"` is vooraf geconfigureerd voor het gebruik van de ondertekeningssleutel die met het codevoorbeeld wordt meegeleverd.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "SINGLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

### <a name="multipleaccountmodefragmentjava"></a>MultipleAccountModeFragment.java

Dit bestand laat zien hoe u een MSAL-app met meerdere accounts maakt en een Microsoft Graph API aanroept.

Een voorbeeld van een app met meerdere accounts is een e-mail-app waarmee u kunt werken met meerdere gebruikersaccounts, zoals een werkaccount en een persoonlijk account.

#### <a name="multiple-account-msal-initialization"></a>MSAL-initialisatie met meerdere accounts

In het bestand `MultipleAccountModeFragment.java`, in `onCreateView()`, wordt een app-object met meerdere acoounts (`IMultipleAccountPublicClientApplication`) gemaakt met behulp van de configuratie-informatie die is opgeslagen in het bestand `auth_config_multiple_account.json file`:

```java
// Creates a PublicClientApplication object with res/raw/auth_config_multiple_account.json
PublicClientApplication.createMultipleAccountPublicClientApplication(getContext(),
        R.raw.auth_config_multiple_account,
        new IPublicClientApplication.IMultipleAccountApplicationCreatedListener() {
            @Override
            public void onCreated(IMultipleAccountPublicClientApplication application) {
                mMultipleAccountApp = application;
                loadAccounts();
            }

            @Override
            public void onError(MsalException exception) {
                ...
            }
        });
```

Het gemaakte `MultipleAccountPublicClientApplication`-object wordt opgeslagen in een klasselidvariabele, zodat het kan worden gebruikt om te communiceren met de MSAL-bibliotheek om tokens te verkrijgen en het gebruikersaccount te laden en verwijderen.

#### <a name="load-an-account"></a>Een account laden

Apps met meerdere accounts roepen gewoonlijk `getAccounts()` aan om het account te selecteren dat moet worden gebruikt voor MSAL-bewerkingen. De code om een account te laden, bevindt zich in het bestand `MultipleAccountModeFragment.java` in `loadAccounts()`.  Het laden van het gebruikersaccount is een asynchrone bewerking. Een callback verwerkt dus de situaties waarin het account wordt geladen, gewijzigd of wanneer een fout optreedt.

```java
/**
 * Load currently signed-in accounts, if there's any.
 **/
private void loadAccounts() {
    if (mMultipleAccountApp == null) {
        return;
    }

    mMultipleAccountApp.getAccounts(new IPublicClientApplication.LoadAccountsCallback() {
        @Override
        public void onTaskCompleted(final List<IAccount> result) {
            // You can use the account data to update your UI or your app database.
            accountList = result;
            updateUI(accountList);
        }

        @Override
        public void onError(MsalException exception) {
            displayError(exception);
        }
    });
}
```

#### <a name="get-a-token-interactively-or-silently"></a>Interactief of op de achtergrond een token ophalen

Bepaalde situaties waarin de gebruiker kan worden gevraagd een account te selecteren, referenties in te voeren of toestemming te geven voor de machtigingen die uw app heeft aangevraagd, zijn:

* De eerste keer dat gebruikers zich aanmelden bij de toepassing
* Als gebruiker hun wachtwoord opnieuw instellen, moeten ze hun referenties invoeren
* Als de toestemming is ingetrokken
* Als uw app expliciet toestemming vereist
* Wanneer via de toepassing voor het eerst toegang wordt aangevraagd tot een resource
* Wanneer MFA of ander beleid voor voorwaardelijke toegang is vereist

Voor apps met meerdere accounts moeten tokens doorgaans interactief worden verkregen, dat wil zeggen met de gebruikersinterface waarbij de gebruiker betrokken is en met een aanroep naar `acquireToken()`.  De code voor het interactief ophalen van een token bevindt zich in het bestand `MultipleAccountModeFragment.java` in `initializeUI()`, in de `callGraphApiInteractiveButton`-click-handler:

```java
/**
 * Acquire token interactively. It will also create an account object for the silent call as a result (to be obtained by getAccount()).
 *
 * If acquireTokenSilent() returns an error that requires an interaction,
 * invoke acquireToken() to have the user resolve the interrupt interactively.
 *
 * Some example scenarios are
 *  - password change
 *  - the resource you're acquiring a token for has a stricter set of requirement than your SSO refresh token.
 *  - you're introducing a new scope which the user has never consented for.
 **/
mMultipleAccountApp.acquireToken(getActivity(), getScopes(), getAuthInteractiveCallback());
```

Gebruikers zouden zich niet telkens hoeven aanmelden wanneer apps een token aanvragen. Als de gebruiker al is aangemeld, staat `acquireTokenSilentAsync()` toe tokens aan te vragen zonder tussenkomst van de gebruiker, zoals getoond in het bestand`MultipleAccountModeFragment.java` in `initializeUI()`, in de `callGraphApiSilentButton`-click-handler:

```java
/**
 * Performs acquireToken without interrupting the user.
 *
 * This requires an account object of the account you're obtaining a token for.
 * (can be obtained via getAccount()).
 */
mMultipleAccountApp.acquireTokenSilentAsync(getScopes(),
    accountList.get(accountListSpinner.getSelectedItemPosition()),
    AUTHORITY,
    getAuthSilentCallback());
```

#### <a name="remove-an-account"></a>Een account verwijderen

De code voor het verwijderen van een account en alle tokens voor het account die in de cache zijn opgeslagen, bevindt zich in het bestand `MultipleAccountModeFragment.java` in `initializeUI()`, in de handler voor de knop Account verwijderen. Voordat u een account kunt verwijderen, hebt u een accountobject nodig dat u hebt verkregen met MSAL-methoden als `getAccounts()` en `acquireToken()`. Aangezien het verwijderen van een account een asynchrone bewerking is, wordt de `onRemoved`-callback verstrekt om de gebruikersinterface bij te werken.

```java
/**
 * Removes the selected account and cached tokens from this app (or device, if the device is in shared mode).
 **/
mMultipleAccountApp.removeAccount(accountList.get(accountListSpinner.getSelectedItemPosition()),
        new IMultipleAccountPublicClientApplication.RemoveAccountCallback() {
            @Override
            public void onRemoved() {
                ...
                /* Reload account asynchronously to get the up-to-date list. */
                loadAccounts();
            }

            @Override
            public void onError(@NonNull MsalException exception) {
                displayError(exception);
            }
        });
```

### <a name="auth_config_multiple_accountjson"></a>auth_config_multiple_account.json

Dit is het configuratiebestand voor een MSAL-app die gebruikmaakt van meerdere accounts.

Zie [Inzicht krijgen in het Android MSAL-configuratiebestand ](msal-configuration.md) voor een uitleg van de verschillende velden.

In tegenstelling tot het [auth_config_single_account.json](#auth_config_single_accountjson)-configuratiebestand heeft dit configuratie `"account_mode" : "MULTIPLE"` in plaats van `"account_mode" : "SINGLE"`, omdat dit een app voor meerdere accounts is.

`"client_id"` is vooraf geconfigureerd voor het gebruik van een app-objectregistratie die door Microsoft wordt onderhouden.
`"redirect_uri"` is vooraf geconfigureerd voor het gebruik van de ondertekeningssleutel die met het codevoorbeeld wordt meegeleverd.

```json
{
  "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
  "authorization_user_agent" : "DEFAULT",
  "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "account_mode" : "MULTIPLE",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount",
        "tenant_id": "common"
      }
    }
  ]
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar de Android-zelfstudie waarin u een Android-app bouwt die een toegangstoken van het Microsoft-identiteitsplatform ontvangt en dit gebruikt om de Microsoft Graph API aan te roepen.

> [!div class="nextstepaction"]
> [Zelfstudie: Gebruikers aanmelden en de Microsoft Graph aanroepen vanaf een Android-toepassing](tutorial-v2-android.md)
