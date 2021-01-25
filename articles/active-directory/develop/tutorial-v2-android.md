---
title: 'Zelfstudie: Een Android-app maken die gebruikmaakt van het Microsoft identiteitsplatform voor verificatie | Azure'
titleSuffix: Microsoft identity platform
description: In deze zelfstudie bouwt u een Android-app die gebruikmaakt van het Microsoft-identiteitsplatform voor het aanmelden van gebruikers. U krijgt een toegangstoken waarmee u de Microsoft Graph API namens hen kunt aanroepen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f3dee72180d0850ce6d920c7e3180cebcbe2f4b4
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98753033"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-application"></a>Zelfstudie: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een Android-app

In deze zelf studie bouwt u een Android-app die kan worden geïntegreerd met het micro soft Identity-platform om gebruikers aan te melden en om een toegangs token op te halen voor het aanroepen van de Microsoft Graph-API.

Wanneer u de zelfstudie hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke Microsoft-accounts (waaronder outlook.com, live.com en overige accounts), maar ook werk- of schoolaccounts van elk bedrijf of elke organisatie waar Azure Active Directory wordt gebruikt.

In deze zelfstudie hebt u het volgende gedaan: 

> [!div class="checklist"]
> * Een Android-app-project maken in *Android Studio*
> * De app registreren in de Azure-portal
> * Code toevoegen voor de ondersteuning van het aan- en afmelden van gebruikers
> * Code toevoegen om de Microsoft Graph API aan te roepen
> * De app testen

## <a name="prerequisites"></a>Vereisten

* Android Studio 3.5+

## <a name="how-this-tutorial-works"></a>Hoe deze zelfstudie werkt

![Er wordt getoond hoe de voorbeeld-app werkt die wordt gegenereerd in deze zelfstudie](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

Met de app in deze zelfstudie worden gebruikers aangemeld, en worden namens hen gegevens opgehaald. Deze gegevens worden geopend via een beveiligde API (Microsoft Graph API), waarvoor autorisatie is vereist en die is beveiligd met Microsoft Identity Platform.

Met name:

* Met uw app wordt de gebruiker aangemeld via een browser of via Microsoft Authenticator en Intune-bedrijfsportal.
* De eindgebruiker accepteert de machtigingen die met de toepassing zijn aangevraagd.
* Aan de app wordt een toegangstoken verleend voor de Microsoft Graph API.
* Het toegangstoken is opgenomen in de HTTP-aanvraag voor de web-API.
* Verwerk het Microsoft Graph-antwoord.

In dit voorbeeld wordt de Microsoft Authentication Library for Android (MSAL) gebruikt voor het implementeren van Verificatie: [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal).

In MSAL worden tokens automatisch vernieuwd, eenmalige aanmelding (SSO) geboden tussen andere apps op het apparaat, en de accounts beheerd.

In deze zelfstudie worden vereenvoudigde voorbeelden getoond van het werken met MSAL for Android. Ter vereenvoudiging wordt in deze zelfstudie alleen gebruik gemaakt van de modus voor een enkele account. Als u meer complexe scenario's wilt verkennen, raadpleegt u een voltooid [voorbeeld van werkende code](https://github.com/Azure-Samples/ms-identity-android-java/) op GitHub.

## <a name="create-a-project"></a>Een project maken
Als u nog geen Android-toepassing hebt, volgt u de volgende stappen om een nieuw project in te stellen.

1. Open Android Studio en selecteer **Een nieuw Android Studio-project starten**.
2. Selecteer **Basisactiviteit** en selecteer **Volgende**.
3. Geef uw toepassing een naam.
4. Sla de naam van het pakket op. U gaat deze later in Azure Portal invoeren.
5. Wijzig de taal van **Kotlin** in **Java**.
6. Stel het **minimale API-niveau** in op **API 19** of hoger en klik op **Voltooien**.
7. Kies in de projectweergave **Project** in de vervolgkeuzelijst om bron- en niet-bronprojectbestanden weer te geven, open **app/build.gradle** en stel `targetSdkVersion` in op `28`.

## <a name="integrate-with-the-microsoft-authentication-library"></a>Integreren met de Microsoft Authentication Library

### <a name="register-your-application"></a>Uw toepassing registreren

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
1. Selecteer **Registreren**.
1. Selecteer onder **Beheren** achtereenvolgens **Verificatie** > **Een platform toevoegen** > **Android**.
1. Voer de pakketnaam van het project in. Als u de code hebt gedownload, is deze waarde `com.azuresamples.msalandroidapp`.
1. Selecteer in de sectie **Handtekening-hash** van de pagina **Uw Android-app configureren** de optie **Een handtekening-hash voor de ontwikkeling genereren.** en kopieer de KeyTool-opdracht die u wilt gebruiken voor uw platform.


     KeyTool.exe is geïnstalleerd als onderdeel van de JDK (Java Development Kit). U moet ook het OpenSSL-hulpprogramma installeren om de KeyTool-opdracht uit te voeren. Raadpleeg de [Android-documentatie over het genereren van een sleutel](https://developer.android.com/studio/publish/app-signing#generate-key) voor meer informatie.

1. Voer de **handtekening-hash** in die is gegenereerd door KeyTool.
1. Selecteer **Configureren** en sla de **MSAL-configuratie** op die wordt weergegeven op de pagina **Android-configuratie**, zodat u deze kunt invoeren wanneer u de app later configureert.  
1. Selecteer **Gereed**.

### <a name="configure-your-application"></a>Uw toepassing configureren

1. Ga in het projectdeelvenster van Android Studio naar **app\src\main\res**.
1. Klik met de rechtermuisknop op **res** en kies **Nieuw** > **Map**. Voer `raw` in als de naam van de nieuwe map en klik op **OK**.
1. Maak in **app** > **src** > **main** > **res** > **raw** een nieuw JSON-bestand met de naam `auth_config_single_account.json` en plak de MSAL-configuratie die u eerder hebt opgeslagen.

    Plak deze onder de omleidings-URI:
    ```json
      "account_mode" : "SINGLE",
    ```
    Het configuratiebestand moet er als volgt uitzien:
    ```json
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "broker_redirect_uri_registered" : true,
      "account_mode" : "SINGLE",
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

     In deze zelfstudie wordt alleen getoond hoe u een app kunt configureren in de modus voor één account. Raadpleeg de documentatie voor meer informatie over [modus voor één account versus de modus voor meerdere accounts](./single-multi-account.md) en [het configureren van uw app](./msal-configuration.md)

4. Voeg in **app** > **src** > **main** > **AndroidManifest.xml**, de onderstaande `BrowserTabActivity`-activiteit toe aan de hoofdtekst van de toepassing. Met deze vermelding kan Microsoft uw toepassing weer aanroepen wanneer de verificatie is voltooid:

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    Vervang de naam van het pakket dat u hebt geregistreerd in Azure Portal door de waarde `android:host=`.
    Vervang de sleutel-hash die u hebt geregistreerd in Azure Portal door de waarde `android:path=`. De handtekening-hash mag **niet** als URL zijn gecodeerd. Zorg ervoor dat er een voorloop-`/` staat aan het begin van uw handtekening-hash.
    
    De "pakket naam" u vervangt de `android:host` waarde met moet er ongeveer als volgt uitzien: "com. azuresamples. msalandroidapp".
    De "Signature hash" u vervangt uw `android:path` waarde met moet er ongeveer als volgt uitzien: "/1wIqXSqBj7w + h11ZifsnqwgyKrY =".
    
    U kunt deze waarden ook vinden in de Blade verificatie van de app-registratie. Merk op dat uw omleidings-URI er ongeveer als volgt uitziet: msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D. Als de handtekening-hash als URL is gecodeerd aan het einde van deze waarde, mag de handtekening-hash **niet** als URL zijn gecodeerd in uw waarde `android:path`.

## <a name="use-msal"></a>MSAL gebruiken

### <a name="add-msal-to-your-project"></a>MSAL toevoegen aan uw project

1. Ga in het Android Studio-projectvenster naar **app** > **src** > **build.gradle** en voeg het volgende toe:

    ```gradle
    repositories{
        jcenter()
    }
    dependencies{
        implementation 'com.microsoft.identity.client:msal:2.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version")
    }
    ```
    [Meer informatie over de Microsoft Graph-SDK](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>Vereiste imports

Voeg het volgende toe bovenaan **app** > **src** > **main**> **java** > **com.example(yourapp)**  > **MainActivity.java**

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>PublicClientApplication instantiëren
#### <a name="initialize-variables"></a>Variabelen initialiseren
```java
private final static String[] SCOPES = {"Files.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
In de klasse `MainActivity` raadpleegt u de volgende onCreate()-methode om MSAL te instantiëren met behulp van de `SingleAccountPublicClientApplication`.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount

```java
//When app comes to the foreground, load existing account to determine if user is signed in
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

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
}
```

### <a name="initializeui"></a>initializeUI
Luister dienovereenkomstig naar knoppen- en oproepmethoden of logboekfouten.
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);

        //Sign in user
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });

        //Interactive
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> Bij afmelden met MSAL wordt alle bekende informatie over een gebruiker verwijderd uit de toepassing. De gebruiker beschikt dan nog steeds over een actieve sessie op het apparaat. Als de gebruiker zich opnieuw probeert aan te melden, ziet deze mogelijk de gebruikersinterface voor aanmelden, maar hoeft de gebruiker mogelijk niet opnieuw referenties op te geven, omdat de sessie van het apparaat nog steeds actief is.

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
Callback die wordt gebruikt voor interactieve aanvragen.

```java
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
Callback die wordt gebruikt voor aanvragen op de achtergrond
```java
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Microsoft Graph API aanroepen

De volgende code laat zien hoe u de GraphAPI aanroept met de Graph-SDK.

### <a name="callgraphapi"></a>callGraphAPI

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>Gebruikersinterface toevoegen
### <a name="activity"></a>Activiteit
Als u uw gebruikersinterface wilt modelleren buiten deze zelfstudie om, bieden de volgende methoden aanwijzingen voor het bijwerken van tekst en het luisteren naar knoppen.

#### <a name="updateui"></a>updateUI
Knoppen in- of uitschakelen op basis van de aanmeldingsstatus en tekst instellen.
```java
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
Methode voor het bijwerken van de tekst in de gebruikersinterface om afmelden weer te geven.

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>Layout

Voorbeeldbestand `activity_main.xml` om knoppen en tekstvakken weer te geven.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>Uw app testen

### <a name="run-locally"></a>Lokaal uitvoeren

Compileer en implementeer de app op een testapparaat of emulator. U moet zich kunnen aanmelden en tokens ontvangen voor Azure AD of persoonlijke Microsoft-accounts.

Nadat u zich hebt aangemeld, verschijnen in de app de gegevens die zijn geretourneerd via het Microsoft Graph `/me`-eindpunt.
PR 4
### <a name="consent"></a>Toestemming

De eerste keer dat een gebruiker zich aanmeldt bij uw app, wordt door Microsoft gevraagd toestemming te geven voor de aangevraagde machtigingen. Voor sommige Azure AD-tenants is gebruikerstoestemming uitgeschakeld. In dit geval moeten beheerders toestemming geven namens alle gebruikers. Ter ondersteuning van dit scenario moet u uw eigen tenant maken of toestemming van de beheerder krijgen.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder het app-object dat u hebt gemaakt in de stap [Uw toepassing registreren](#register-your-application) als u het niet meer nodig hebt.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bouwen van mobiele apps die beveiligde web-API's aanroepen in onze reeks met meerdere scenario's.

> [!div class="nextstepaction"]
> [Scenario: Een mobiele app die web-API's aanroept](scenario-mobile-overview.md)
