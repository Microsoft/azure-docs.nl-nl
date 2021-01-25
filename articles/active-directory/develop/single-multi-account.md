---
title: Enkele en open bare client-apps voor één account | Azure
description: Een overzicht van de open bare client-apps voor één en meerdere accounts.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 5bea1753c87c11094e78f95a1bbadb02fb0b95e2
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752985"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Open bare client-apps met één of meer accounts

Dit artikel helpt u bij het begrijpen van de typen die worden gebruikt in één account en open bare client-apps met één account, met een focus op open bare client-apps voor een enkele rekening. 

Met de Azure Active Directory-verificatie bibliotheek (ADAL) worden de-server modellen gemodelleerd.  In plaats daarvan modellen uw client toepassing in de micro soft Authentication Library (MSAL).  De meeste Android-apps worden beschouwd als open bare clients. Een open bare client is een app waarmee een geheim niet veilig kan worden bewaard.  

MSAL is gespecialiseerd in het API-Opper vlak van `PublicClientApplication` om de ontwikkelings ervaring te vereenvoudigen en te verduidelijken voor apps waarmee slechts één account tegelijk kan worden gebruikt. `PublicClientApplication` is subklasseeerd door `SingleAccountPublicClientApplication` en `MultipleAccountPublicClientApplication` .  In het volgende diagram ziet u de relatie tussen deze klassen.

![Diagram van SingleAccountPublicClientApplication UML-klasse](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Open bare client toepassing met één account

Met de- `SingleAccountPublicClientApplication` klasse kunt u een app op basis van MSAL maken waarmee slechts één account tegelijk kan worden aangemeld. `SingleAccountPublicClientApplication` verschilt van `PublicClientApplication` op de volgende manieren:

- MSAL houdt het account dat momenteel is aangemeld bij.
  - Als uw app gebruikmaakt van een Broker (de standaard tijdens het registreren van Azure Portal app) en is geïnstalleerd op een apparaat waarop een Broker aanwezig is, controleert MSAL of het account nog steeds beschikbaar is op het apparaat.
- `signIn` Hiermee kunt u een account expliciet en onafhankelijk van het aanvragen van scopes aanmelden.
- `acquireTokenSilent` vereist geen account parameter.  Als u een account opgeeft en het account dat u opgeeft, niet overeenkomt met het huidige account dat wordt getraceerd door MSAL, wordt er een `MsalClientException` gegenereerd.
- `acquireToken` de gebruiker mag niet overschakelen naar een ander account. Als de gebruiker probeert over te scha kelen naar een ander account, wordt er een uitzonde ring gegenereerd.
- `getCurrentAccount` retourneert een resultaat object dat het volgende biedt:
  - Een Booleaanse waarde die aangeeft of het account is gewijzigd. Een account kan worden gewijzigd als gevolg van het verwijderen van het apparaat, bijvoorbeeld.
  - Het vorige account. Dit is handig als u lokaal gegevens opschoning wilt uitvoeren wanneer het account wordt verwijderd van het apparaat of wanneer een nieuw account is aangemeld.
  - De currentAccount.
- `signOut` Hiermee verwijdert u de tokens die zijn gekoppeld aan uw client van het apparaat.  

Wanneer een Android-verificatie Broker, zoals Microsoft Authenticator of Intune-bedrijfsportal op het apparaat is geïnstalleerd en uw app is geconfigureerd voor het gebruik van de Broker, wordt `signOut` het account niet van het apparaat verwijderd.

## <a name="single-account-scenario"></a>Scenario met één account

De volgende pseudo code illustreert het gebruik van `SingleAccountPublicClientApplication` .

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Open bare client toepassing voor meerdere accounts

De `MultipleAccountPublicClientApplication` klasse wordt gebruikt voor het maken van MSAL-apps waarmee meerdere accounts tegelijkertijd kunnen worden aangemeld. U kunt als volgt accounts ophalen, toevoegen en verwijderen:

### <a name="add-an-account"></a>Een account toevoegen

Gebruik een of meer accounts in uw toepassing door `acquireToken` een of meer keren aan te roepen.  

### <a name="get-accounts"></a>Accounts ophalen

- Aanroep `getAccount` om een specifiek account op te halen.
- Aanroep `getAccounts` voor het ophalen van een lijst met accounts die op dit moment bekend zijn bij de app.

Uw app kan niet alle micro soft Identity platform-accounts op het apparaat opsommen die bekend zijn bij de Broker-app. Het kan alleen accounts opsommen die door uw app zijn gebruikt.  Accounts die zijn verwijderd van het apparaat, worden niet geretourneerd door deze functies.

### <a name="remove-an-account"></a>Een account verwijderen

Verwijder een account door aan te roepen `removeAccount` met een account-id.

Als uw app is geconfigureerd voor het gebruik van een Broker en er een Broker op het apparaat is geïnstalleerd, wordt het account niet uit de Broker verwijderd wanneer u aanroept `removeAccount` .  Alleen tokens die zijn gekoppeld aan uw client, worden verwijderd.

## <a name="multiple-account-scenario"></a>Scenario met meerdere accounts

De volgende pseudo code laat zien hoe u een app voor meerdere accounts maakt, accounts op het apparaat lijsteert en tokens aanschaft.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
