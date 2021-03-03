---
title: 'Zelfstudie: Modus voor gedeelde apparaten gebruiken met Microsoft Authentication Library (MSAL) voor Android | Azure'
titleSuffix: Microsoft identity platform
description: In deze zelfstudie krijgt u meer informatie over het voorbereiden van een Android-apparaat voor de gedeelde modus en het uitvoeren van een app voor eerstelijnswerknemers.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6a173ed4dae9237d8aae991c943817ed70246eea
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649052"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>Zelfstudie: Modus voor gedeelde apparaten gebruiken in een Android-toepassing

In deze zelfstudie hebben Android-ontwikkelaars en Azure Active Directory (Azure AD)-tenantbeheerders meer informatie over de code, Authenticator-app en tenantinstellingen die vereist zijn om de modus gedeeld apparaat in te schakelen voor een Android-app.

In deze zelfstudie:

> [!div class="checklist"]
> * Een codevoorbeeld downloaden
> * Modus voor gedeelde apparaten inschakelen en detecteren
> * Modus voor één account of modus voor meerdere accounts detecteren
> * Een verandering van gebruiker detecteren en globale aanmelding en afmelding inschakelen
> * De tenant instellen en de app registreren in de Azure Portal
> * Een Android-apparaat instellen in de modus voor gedeelde apparaten
> * De voorbeeld-app uitvoeren

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="developer-guide"></a>Ontwikkelaarsgids

Dit gedeelte van de zelfstudie biedt ontwikkelrichtlijnen voor het implementeren van de modus voor gedeelde apparaten in een Android-toepassing die gebruikmaakt van MSAL (Microsoft Authentication Library). Raadpleeg de [Android-zelfstudie voor MSAL](./tutorial-v2-android.md) om te zien hoe u MSAL integreert met een Android-app, een gebruiker aanmeldt, Microsoft Graph aanroept, en een gebruiker afmeldt.

### <a name="download-the-sample"></a>Het voorbeeld downloaden

Kloon de [voorbeeldtoepassing](https://github.com/Azure-Samples/ms-identity-android-java/) vanuit GitHub. Het voorbeeld biedt de mogelijkheid om te werken in de [modus voor één of meerdere accounts](./single-multi-account.md).

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>De MSAL SDK toevoegen aan uw lokale Maven-opslagplaats

Als u de voorbeeld-app niet gebruikt, voegt u als volgt de MSAL-bibliotheek als afhankelijkheid toe aan het build.gradle-bestand:

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>Uw app configureren om de modus voor gedeelde apparaten te gebruiken

Raadpleeg de [configuratiedocumentatie](./msal-configuration.md) voor meer informatie over het instellen van het configuratiebestand.

Stel in het MSAL-configuratiebestand `"shared_device_mode_supported"` in op `true`.

Mogelijk bent u niet van plan om ondersteuning te bieden voor de modus voor meerdere accounts. Bijvoorbeeld omdat u geen gedeeld apparaat gebruikt, en de gebruiker zich met meer dan één account tegelijk kan aanmelden bij de app. Als dit het geval is, stelt u `"account_mode"` in op `"SINGLE"`. Dit zorgt ervoor dat de app altijd `ISingleAccountPublicClientApplication` ontvangt, en vereenvoudigt uw MSAL-integratie aanzienlijk. De standaardwaarde van `"account_mode"` is `"MULTIPLE"`. Het is dus belangrijk om deze waarde in het configuratiebestand te wijzigen als u de modus `"single account"` gebruikt.

Hier volgt een voorbeeld van het auth_config.json-bestand dat is opgenomen in de map **app**>**main**>**res**>**raw** van de voorbeeld-app:

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>Modus voor gedeelde apparaten detecteren

De modus voor gedeelde apparaten stelt u in staat Android-apparaten te configureren zodat ze kunnen worden gedeeld door meerdere werknemers, terwijl u ook beheer op basis van Microsoft Identity voor het apparaat biedt. Werknemers kunnen zich aanmelden bij hun apparaten en snel toegang krijgen tot klantgegevens. Wanneer hun dienst of taken erop zitten, kunnen ze zich met slechts één klik afmelden bij alle apps op het gedeelde apparaat, en is het apparaat onmiddellijk gereed voor gebruik door de volgende werknemer.

Gebruik `isSharedDevice()` om te bepalen of een app actief is op een apparaat dat zich in de modus voor gedeeld apparaten bevindt. Uw app kan deze vlag gebruiken om te bepalen of UX dienovereenkomstig moet worden gewijzigd.

Hier volgt een codefragment waarin wordt weergegeven hoe u `isSharedDevice()` kunt gebruiken.  Het is afkomstig uit de klasse `SingleAccountModeFragment` in de voorbeeld-app:

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ? "Shared" : "Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>Het PublicClientApplication-object initialiseren

Als u `"account_mode":"SINGLE"` instelt in het MSAL-configuratiebestand, kunt u het geretourneerde toepassingsobject veilig casten als een `ISingleAccountPublicCLientApplication`.

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>Modus voor één account versus modus voor meerdere accounts detecteren

Als u een app schrijft die alleen gebruikt gaat worden door eerstelijnswerknemers op een gedeeld apparaat, raden we u aan de app zo te schrijven dat deze alleen ondersteuning biedt voor de modus voor één account. Dit omvat de meeste toepassingen die zijn gericht op taken, zoals apps voor medische dossiers of voor facturen, en de meeste Line-Of-Business-apps. Dit vereenvoudigt de ontwikkeling, omdat veel functies van de SDK niet hoeven te worden aangepast.

Als uw app ondersteuning biedt voor meerdere accounts en voor de modus voor gedeelde apparaten, moet u een typecontrole uitvoeren en naar de juiste interface casten zoals hieronder wordt weergegeven.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Ga naar de aangemelde gebruiker en stel vast of een gebruiker op het apparaat is gewijzigd.

Met de methode `loadAccount` wordt het account opgehaald bij de aangemelde gebruiker. Met de methode `onAccountChanged` wordt bepaald of de aangemelde gebruiker is gewijzigd, en zo ja, dan wordt er opgeschoond:

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>Globaal aanmelden als een gebruiker

Hieronder wordt een gebruiker op het apparaat aangemeld bij andere apps die gebruikmaken van MSAL met de Authenticator-app:

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>Globaal afmelden als een gebruiker

Hieronder wordt het aangemelde account verwijderd en worden de tokens opgeschoond die zijn opgeslagen in de cache, niet alleen van de app maar ook van het apparaat dat zich in de modus voor gedeelde apparaten bevindt:

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>Beheerdershandleiding

In de volgende stappen wordt beschreven hoe u de toepassing instelt in Azure Portal, en hoe u de modus voor gedeelde apparaten inschakelt voor uw apparaat.

### <a name="register-your-application-in-azure-active-directory"></a>De toepassing registreren in Azure Active Directory

Registreer de toepassing eerst in de organisatietenant. Geef vervolgens onderstaande waarden op in het auth_config.json-bestand om ervoor te zorgen dat de toepassing juist wordt uitgevoerd.

Raadpleeg [Uw toepassing registreren](./tutorial-v2-android.md#register-your-application) voor meer informatie over hoe u dit doet.

> [!NOTE]
> Gebruik de quickstart aan de linkerkant en selecteer vervolgens **Android** wanneer u de app registreert. U komt nu bij een pagina waar u wordt gevraagd om de **Pakketnaam** en de **Hash voor ondertekening** op te geven voor de app. Dit is heel belangrijk om ervoor te zorgen dat de app-configuratie werkt. U ontvangt vervolgens een configuratie-object dat u kunt gebruiken voor de app, dat u kunt knippen en plakken in het auth_config.json-bestand.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-app.png" alt-text="Uw Android-app-pagina configureren in de quickstart van Azure Portal":::

Selecteer **Dit voor mij wijzigen** en geef vervolgens in Azure Portal de waarden op waarom in de quickstart wordt gevraagd. Als dit is gebeurd, worden alle configuratiebestanden gegenereerd die u nodig hebt.

:::image type="content" source="media/tutorial-v2-shared-device-mode/config-info.png" alt-text="Uw projectpagina configureren in de quickstart van Azure Portal":::

## <a name="set-up-a-tenant"></a>Een tenant instellen

Stel voor testdoeleinden het volgende in de tenant in: minstens twee werknemers, één cloudapparaatbeheerder, en één globale beheerder. Stel in Azure Portal de cloudapparaatbeheerder in door de organisatierollen te wijzigen. Ga in Azure Portal naar uw organisatierollen door het volgende te selecteren: **Azure Active Directory** > **Rollen en beheerders** > **Cloudapparaatbeheerder**. Voeg de gebruikers toe die een apparaat in de gedeelde modus kunnen plaatsen.

## <a name="set-up-an-android-device-in-shared-mode"></a>Een Android-apparaat instellen in de gedeelde modus

### <a name="download-the-authenticator-app"></a>De Authenticator-app downloaden

Download de Microsoft Authenticator-app uit de Google Play Store. Als u de app al hebt gedownload, controleert u of dit de meest recente versie is.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Authenticator-app-instellingen en het apparaat registreren in de cloud

Start de Authenticator-app en ga naar de hoofdaccountpagina. Zodra u de pagina **Account toevoegen** ziet, kunt u het apparaat gaan delen.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-add-account.png" alt-text="Scherm Account toevoegen in Authenticator":::

Ga naar het deelvenster **Instellingen** met behulp van de menubalk aan de rechterkant. Selecteer **Apparaatregistratie** onder **Werk- en schoolaccounts**.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-settings.png" alt-text="Het scherm met Authenticator-instellingen":::

Wanneer u op deze knop klikt, wordt u gevraagd toegang te verlenen tot contactpersonen van het apparaat. Dit komt door de accountintegratie van Android op het apparaat. Kies **Toestaan**.

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png" alt-text="Het bevestigingsscherm toegang Authenticator toestaan":::

De cloudapparaatbeheerder moet het e-mailadres van de organisatie invoeren onder **Of registreren als een gedeeld apparaat**. Vervolgens moet de beheerder op de knop **Registreren als gedeeld apparaat** klikken en de referenties invoeren.

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-device.png" alt-text="Het scherm apparaatregistratie in de app":::

:::image type="content" source="media/tutorial-v2-shared-device-mode/sign-in.png" alt-text="Schermopname van app met Microsoft-aanmeldingspagina":::

Het apparaat bevindt zich nu in de gedeelde modus.

:::image type="content" source="media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png" alt-text="App-scherm met de modus gedeelde apparaten ingeschakeld":::

 Alle aanmeldingen en afmeldingen op het apparaat zijn globaal. Dit betekent dat ze van toepassing zijn op alle apps die zijn geïntegreerd met MSAL en Microsoft Authenticator op het apparaat. U kunt nu toepassingen implementeren op het apparaat waarop functies worden gebruikt van de modus voor gedeelde apparaten.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Het gedeelde apparaat bekijken in Azure Portal

Zodra u een apparaat in de gedeelde modus hebt geplaatst, is het bekend in de organisatie en wordt het bijgehouden in de organisatietenant. U kunt uw gedeelde apparaten bekijken door naar **Join-type** te gaan op de blade Azure Active Directory van Azure Portal.

:::image type="content" source="media/tutorial-v2-shared-device-mode/registered-device-screen.png" alt-text="Het deelvenster Alle apparaten wordt weergegeven in de Azure Portal":::

## <a name="running-the-sample-app"></a>De voorbeeld-app uitvoeren

De voorbeeldtoepassing is een eenvoudige app waarmee de Graph API van uw organisatie wordt aangeroepen. Bij de eerste uitvoering wordt u gevraagd om toestemming omdat de toepassing nieuw is bij uw werknemersaccount.

:::image type="content" source="media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png" alt-text="Informatiescherm over app-configuratie":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het werken met de Microsoft Verification Library en de modus voor gedeelde apparaten op Android-apparaten:

> [!div class="nextstepaction"]
> [Modus voor gedeeld apparaat voor Android-apparaten](msal-android-shared-devices.md)
