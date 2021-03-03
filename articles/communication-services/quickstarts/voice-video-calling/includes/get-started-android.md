---
title: 'Quickstart: VOIP-aanroepen toevoegen aan een Android-app met behulp van Azure Communication Services'
description: In deze zelfstudie leert u meer over het gebruik van de clientbibliotheek voor aanroepen van Azure Communication Services voor Android
author: matthewrobertson
ms.author: marobert
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: a387261b392ea6718941f5eabe889e0c1a41fd5a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750871"
---
In deze quickstart leert u hoe u een oproep start met behulp van de clientbibliotheek voor oproepen van Azure Communication Services voor Android.

> [!NOTE]
> Dit document maakt gebruik van versie 1.0.0-Beta. 8 van de aanroepende client bibliotheek.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Android Studio](https://developer.android.com/studio), voor het maken van uw Android-toepassing.
- Een geïmplementeerde Communication Services-resource. [Een Communication Services maken](../../create-communication-resource.md).
- Een [Token voor gebruikerstoegang](../../access-tokens.md) voor uw Azure Communication Service.

## <a name="setting-up"></a>Instellen

### <a name="create-an-android-app-with-an-empty-activity"></a>Een Android-app maken met een lege activiteit

Selecteer Een nieuw Android Studio-project starten in Android Studio.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Schermopname waarin de knop Een nieuw Android Studio-project starten is geselecteerd in Android Studio.":::

Selecteer de projectsjabloon Lege activiteit onder Telefoon en tablet.

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Schermopname waarin de optie Lege activiteit is geselecteerd op het scherm Projectsjabloon.":::

Selecteer ten minste clientbibliotheek API 26: Android 8.0 (Oreo) of hoger.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Schermopname waarin de optie Lege activiteit is geselecteerd op het scherm Projectsjabloon 2.":::


### <a name="install-the-package"></a>Het pakket installeren

<!-- TODO: update with instructions on how to download, install and add package to project -->
Zoek de build.gradle op projectniveau op en vergeet niet `mavenCentral()` toe te voegen aan de lijst met opslagplaatsen onder `buildscript` en `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Voeg vervolgens in de build.gradle op moduleniveau de volgende regels toe aan de secties Afhankelijkheden en Android

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}
```

### <a name="add-permissions-to-application-manifest"></a>Machtigingen toevoegen aan het toepassingsmanifest

Als u machtigingen wilt aanvragen die nodig zijn om een aanroep te doen, moeten deze eerst worden gedeclareerd in het toepassingsmanifest (`app/src/main/AndroidManifest.xml`). Vervang de inhoud van het bestand door het volgende:

```xml
    <?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.contoso.acsquickstart">

    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
    <uses-permission android:name="android.permission.RECORD_AUDIO" />
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <!--Our calling client library depends on the Apache HTTP client library.
When targeting Android client library 28+, this library needs to be explicitly referenced.
See https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p-->
        <uses-library android:name="org.apache.http.legacy" android:required="false"/>
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
    
```

### <a name="set-up-the-layout-for-the-app"></a>De indeling van de app instellen

Er moeten twee items worden toegevoegd: een tekstvak voor de id van de persoon die wordt gebeld en een knop om te bellen. U kunt deze onderdelen toevoegen via het ontwerpprogramma of door het XML-bestand van de indeling te bewerken. Maak een knop met een id van `call_button` en een tekstinvoer van `callee_id`. Ga naar (`app/src/main/res/layout/activity_main.xml`) en vervang de inhoud van het bestand door het volgende:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/call_button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="16dp"
        android:text="Call"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent" />

    <EditText
        android:id="@+id/callee_id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:ems="10"
        android:hint="Callee Id"
        android:inputType="textPersonName"
        app:layout_constraintBottom_toTopOf="@+id/call_button"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>De hoofdopbouw en -bindingen van de activiteit maken

Nu de indeling is gemaakt, kunnen de bindingen en de basisopzet van de activiteit worden toegevoegd. De activiteit verwerkt machtigingen voor de runtime van aanvragen. Hiermee wordt de aanroepagent gemaakt en wordt de aanroep gemaakt zodra de gebruiker op de knop drukt. Elk onderdeel wordt in een aparte sectie behandeld. De `onCreate`-methode wordt overschreven om `getAllPermissions` en `createAgent` in te trekken en om de bindingen voor de aanroepknop toe te voegen. Dit wordt slechts eenmalig uitgevoerd wanneer de activiteit wordt gemaakt. Raadpleeg de handleiding [Informatie over de levenscyclus van activiteiten](https://developer.android.com/guide/components/activities/activity-lifecycle) voor meer informatie over `onCreate`.

Ga naar **MainActivity.java** en vervang de inhoud door de volgende code:

```java
package com.contoso.acsquickstart;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.EditText;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationUserIdentifier;
import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.calling.CallAgent;
import com.azure.android.communication.calling.CallClient;
import com.azure.android.communication.calling.StartCallOptions;


import java.util.ArrayList;

public class MainActivity extends AppCompatActivity {
    
    private CallAgent callAgent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        getAllPermissions();
        createAgent();
        
        // Bind call button to call `startCall`
        Button callButton = findViewById(R.id.call_button);
        callButton.setOnClickListener(l -> startCall());
    }

    /**
     * Request each required permission if the app doesn't already have it.
     */
    private void getAllPermissions() {
        // See section on requesting permissions
    }

    /**
      * Create the call agent for placing calls
      */
    private void createAgent() {
        // See section on creating the call agent
    }

    /**
     * Place a call to the callee id provided in `callee_id` text input.
     */
    private void startCall() {
        // See section on starting the call
    }
}

```

### <a name="request-permissions-at-runtime"></a>Machtigingen tijdens runtime aanvragen

Voor Android 6.0 en hoger (API-niveau 23) en `targetSdkVersion` 23 of hoger worden machtigingen toegekend tijdens de runtime in plaats van tijdens de installatie van de app. Om dit te ondersteunen kan `getAllPermissions` worden geïmplementeerd om `ActivityCompat.checkSelfPermission` en `ActivityCompat.requestPermissions` aan te roepen voor elke vereiste machtiging.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO, Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_PHONE_STATE};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 1);
    }
}
```

> [!NOTE]
> Houd tijdens het ontwerpen van uw app rekening met het moment waarop deze machtigingen moeten worden aangevraagd. Machtigingen moeten worden aangevraagd wanneer u ze nodig hebt, niet van tevoren. Zie de [handleiding met machtigingen voor Android](https://developer.android.com/training/permissions/requesting) voor meer informatie.

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services-clientbibliotheek voor aanroepen:

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| De CallClient is het belangrijkste ingangspunt voor de clientbibliotheek voor oproepen.|
| CallAgent | De CallAgent wordt gebruikt om oproepen te starten en te beheren. |
| CommunicationUserCredential | De CommunicationUserCredential wordt als de tokenreferentie gebruikt om de CallAgent te instantiëren.|
| CommunicationIdentifier | De CommunicationIdentifier wordt gebruikt als een ander type deel nemer dat zou kunnen deel nemen aan een aanroep.|

## <a name="create-an-agent-from-the-user-access-token"></a>Een agent maken op basis van het toegangstoken voor gebruikers

Met de gebruikerstoken kan een geverifieerde aanroepagent worden geïnstantieerd. Over het algemeen wordt dit token gegenereerd op basis van een service met een specifieke verificatie voor de toepassing. Raadpleeg de handleiding over [tokens voor gebruikerstoegang](../../access-tokens.md) voor meer informatie over dit onderwerp. Voor de quickstart vervangt u `<User_Access_Token>` door een gebruikerstoegangstoken dat voor uw Azure Communication Service-resource is gegenereerd.

```java

/**
 * Create the call agent for placing calls
 */
private void createAgent() {
    String userToken = "<User_Access_Token>";

    try {
        CommunicationTokenCredential credential = new CommunicationTokenCredential(userToken);
        callAgent = new CallClient().createCallAgent(getApplicationContext(), credential).get();
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create call agent.", Toast.LENGTH_SHORT).show();
    }
}

```

## <a name="start-a-call-using-the-call-agent"></a>Een aanroep starten met behulp van de aanroepagent

U kunt bellen via de agent voor oproepen. Hiervoor moet u alleen een lijst met id's van personen die worden gebeld en de opties voor bellen opgeven. Voor de quickstart worden de standaardopties voor oproepen zonder video en één id van een persoon die wordt gebeld uit de tekstinvoer gebruikt.

```java
/**
 * Place a call to the callee id provided in `callee_id` text input.
 */
private void startCall() {
    EditText calleeIdView = findViewById(R.id.callee_id);
    String calleeId = calleeIdView.getText().toString();
    
    StartCallOptions options = new StartCallOptions();

    callAgent.call(
        getApplicationContext(),
        new CommunicationUserIdentifier[] {new CommunicationUserIdentifier(calleeId)},
        options);
}
```


## <a name="launch-the-app-and-call-the-echo-bot"></a>De app starten en de Echo-bot aanroepen

De app kan nu worden gestart met behulp van de knop App uitvoeren op de werkbalk (Shift + F10). Roep `8:echo123` aan om te controleren of u aanroepen kunt plaatsen. Er wordt een vooraf opgenomen bericht afgespeeld en vervolgens wordt uw bericht aan u herhaald.

:::image type="content" source="../media/android/quickstart-android-call-echobot.png" alt-text="Schermopname met de voltooide toepassing.":::

## <a name="sample-code"></a>Voorbeeldcode

U kunt de voorbeeld-app downloaden uit [GitHub](https://github.com/Azure-Samples/communication-services-android-quickstarts/tree/main/Add%20Voice%20Calling).
