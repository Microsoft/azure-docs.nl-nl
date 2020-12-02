---
title: 'Zelfstudie: Een nieuwe app voor Android maken'
description: In deze zelfstudie leert u hoe u een Android-app maakt met behulp van Azure Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: af0d01a20728d2332d4a8d71819f73baf68a65a4
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95998380"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Zelfstudie: Stapsgewijze instructies voor het maken van een nieuwe Android-app met behulp van Azure Spatial Anchors

In deze zelfstudie ontdekt u hoe u een nieuwe Android-app kunt maken die ARCore-functies combineert met Azure Spatial Anchors.

## <a name="prerequisites"></a>Vereisten

Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien:

- Een Windows- of macOS-machine met <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 +</a>.
- Een <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">door een ontwikkelaar geactiveerd</a> en <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">voor ARCore geschikt</a> Android-apparaat.

## <a name="getting-started"></a>Aan de slag

Start Android Studio. Klik op **Een nieuw Android Studio-project starten** in het venster **Welkom bij Android Studio**. Als u al een project hebt geopend, klikt u op **Bestand**->**Nieuw project**.

Kies **Lege activiteit** in het venster **Nieuw project maken** in de sectie **Telefoon en tablet** en klik op **Volgende**. Klik vervolgens `API 26: Android 8.0 (Oreo)` onder **Minimaal API-niveau** en zorg ervoor dat **Taal** is ingesteld op `Java`. U kunt de naam en locatie van het project en de naam van het pakket wijzigen. Laat de andere opties ongewijzigd. Klik op **Voltooien**. Het **installatieprogramma** wordt uitgevoerd. Zodra het klaar is, klikt u op **Voltooien**. Na enkele bewerkingen opent Android Studio de IDE.

## <a name="trying-it-out"></a>Het uitproberen

Om uw nieuwe app uit te testen, verbindt u uw apparaat in ontwikkelaarsmodus met uw ontwikkelcomputer via een USB-kabel. Klik op **Uitvoeren**-> **'App' uitvoeren**. Selecteer uw apparaat in het venster **Implementatiedoel selecteren** en klik op **OK**. Android Studio installeert de app op uw verbonden apparaat en start het op. Als het goed is, ziet u nu dat het bericht 'Hallo wereld!' wordt weergegeven in de app op uw apparaat. Klik op **Uitvoeren**-> **'App' stoppen**.

## <a name="integrating-_arcore_"></a>_ARCore_ integreren

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> is het platform van Google om Augmented Reality-ervaringen te ontwikkelen. Het stelt uw apparaat in staat om de eigen positie op te volgen tijdens het bewegen om zo een eigen begrip te ontwikkelen van de echte wereld.

Wijzig `app\manifests\AndroidManifest.xml` zodat de volgende vermeldingen worden opgenomen in het hoofd`<manifest>`knooppunt. Dit codefragment doet een aantal dingen:

- Het geeft uw app toegang tot de camera van uw apparaat.
- Het zorgt er ook voor dat uw app in de Google Play Store enkel zichtbaar is voor apparaten die ondersteuning bieden voor ARCore.
- Het configureert de Google Play Store om ARCore te downloaden en installeren, indien dat nog niet gebeurd is, zodra uw app is geïnstalleerd.

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera.ar" />

<application>
    ...
    <meta-data android:name="com.google.ar.core" android:value="required" />
    ...
</application>
```

Wijzig `Gradle Scripts\build.gradle (Module: app)` zodat het de volgende vermelding bevat. Deze code zorgt ervoor dat uw app zich richt op ARCore versie 1.8. Na deze wijziging is het mogelijk dat u een melding ontvangt van Gradle met de vraag om te synchroniseren: klik op **Nu synchroniseren**.

```
dependencies {
    ...
    implementation 'com.google.ar:core:1.11.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>_Sceneform_ integreren

[_Sceneform_](https://developers.google.com/sceneform/develop/) maakt het gemakkelijker om realistische 3D-scènes weer te geven in Augmented Reality-apps, zonder dat u OpenGL moet leren.

Wijzig `Gradle Scripts\build.gradle (Module: app)` zodat het de volgende vermeldingen bevat. Deze code zorgt ervoor dat uw app taalconstructs van Java 8 gebruikt, wat vereist is voor `Sceneform`. Het zorgt er ook voor dat uw app gericht is op `Sceneform` versie 1.8, aangezien dit overeen dient te komen met de versie van ARCore die uw app gebruikt. Na deze wijziging is het mogelijk dat u een melding ontvangt van Gradle met de vraag om te synchroniseren: klik op **Nu synchroniseren**.

```
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.11.0'
    ...
}
```

Open uw `app\res\layout\activity_main.xml` en vervang het bestaande Hallo wereld-`<TextView>`element door het volgende ArFragment. Deze code zorgt ervoor dat de camerafeed wordt weergegeven op uw scherm, zodat ARCore de positie van uw apparaat kan volgen wanneer het beweegt.

```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

[Implementeer](#trying-it-out) uw app opnieuw naar uw apparaat om het nogmaals te valideren. Ditmaal wordt u gevraagd om cameramachtigingen. Zodra u instemt, ziet u dat uw camerafeed wordt weergegeven op uw scherm.

## <a name="place-an-object-in-the-real-world"></a>Een object in het echt plaatsen

We gaan een object maken en plaatsen met behulp van uw app. Voeg eerst de volgende importen toe aan uw `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=23-33)]

Voeg vervolgens de volgende lidvariabelen toe aan uw `MainActivity`-klasse:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-57)]

Voeg vervolgens de volgende code toe aan uw methode `app\java\<PackageName>\MainActivity` `onCreate()`. Deze code koppelt een listener, genaamd `handleTap()`, die detecteert wanneer de gebruiker op het scherm van uw apparaat tikt. Als er wordt getikt op een oppervlak in de echte wereld dat al is erkend door ARCore, dan wordt de listener uitgevoerd.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-74,85&highlight=6-7)]

Voeg ten slotte de volgende `handleTap()`-methode toe, dit zal alles met elkaar verbinden. Het creëert een bol en plaatst deze op de aangetikte locatie. De bol is in eerste instantie zwart, aangezien `this.recommendedSessionProgress` nu op zwart is ingesteld. Deze waarde wordt later aangepast.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-172,175-183,199-200)]

[Implementeer](#trying-it-out) uw app opnieuw naar uw apparaat om het nogmaals te valideren. Ditmaal kunt u uw apparaat bewegen zodat ARCore uw omgeving kan beginnen herkennen. Tik vervolgens op het scherm om uw zwarte bol op het gewenste oppervlak te maken en te plaatsen.

## <a name="attach-a-local-azure-spatial-anchor"></a>Een lokaal Azure Spatial Anchor koppelen

Wijzig `Gradle Scripts\build.gradle (Module: app)` zodat het de volgende vermelding bevat. Deze code zorgt ervoor dat uw app zich richt op Azure Spatial Anchors versie 2.2.0. Normaal gezien zijn alle recente versies van Azure Spatial Anchors voldoende. U vindt [hier](https://github.com/Azure/azure-spatial-anchors-samples/releases) de releaseopmerkingen.

```
dependencies {
    ...
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.2.0]"
    implementation "com.microsoft.azure.spatialanchors:spatialanchors_java:[2.2.0]"
    ...
}
```

Klik met de rechtermuisknop op `app\java\<PackageName>`->**Nieuw**->**Java-klasse**. Stel de **Naam** in op _Mijneersteapp_ en **Superklasse** op _Android.app.Application_. Laat de andere opties ongewijzigd. Klik op **OK**. Er wordt een bestand met de naam `MyFirstApp.java` gemaakt. Voeg de volgende import toe:

```java
import com.microsoft.CloudServices;
```

Voeg vervolgens deze code toe aan de nieuwe `MyFirstApp`-klasse. Dit zal ervoor zorgen dat Azure Spatial Anchors wordt geïnitialiseerd met de context van uw toepassing.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Wijzig nu `app\manifests\AndroidManifest.xml` zodat de volgende vermelding wordt opgenomen in het hoofd`<application>`knooppunt. Deze code koppelt de Toepassingsklasse die u hebt gemaakt aan uw app.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

Voeg in `app\java\<PackageName>\MainActivity` de volgende importen toe:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=33-40&highlight=2-8)]

Voeg vervolgens de volgende lidvariabelen toe aan uw `MainActivity`-klasse:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=57-60&highlight=3-4)]

Daarna voegen we de volgende `initializeSession()`-methode toe in uw `mainActivity`-klasse. Zodra deze is aangeroepen, zorgt deze ervoor dat er een Azure Spatial Anchors-sessie wordt gemaakt en op de juiste wijze wordt geïnitialiseerd tijdens het opstarten van uw app.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-97,147)]

We gaan nu uw `initializeSession()`-methode koppelen aan uw `onCreate()`-methode. We zorgen er ook voor dat de frames van uw camerafeed naar Azure Spacial Anchors SDK worden verzonden voor verwerking.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=68-85&highlight=9-17)]

Voeg tot slot de volgende code toe aan uw `handleTap()`-methode. Hiermee wordt een lokale Azure Spatial Anchor gekoppeld aan de zwarte bol die we in het echt gaan plaatsen.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-183,199-200&highlight=12-13)]

[Implementeer](#trying-it-out) uw app nogmaals. Beweeg uw apparaat, tik op het scherm en plaats een zwarte bol. Ditmaal zal uw code een lokale Azure Spatial Anchor maken en aan uw bol koppelen.

Voordat u verder gaat, moet u een Azure Spatial Anchors-account maken om de account-id, -sleutel en -domein te krijgen, als u deze nog niet hebt. Zie de volgende sectie om deze te verkrijgen.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Uw lokale anker uploaden naar de cloud

Zodra u uw account-id, -sleutel en -domein voor Azure Spatial Anchors hebt kunnen we terug in `app\java\<PackageName>\MainActivity` gaan om de volgende importen toe te voegen:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=40-45&highlight=3-6)]

Voeg vervolgens de volgende lidvariabelen toe aan uw `MainActivity`-klasse:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=60-65&highlight=3-6)]

Voeg nu de volgende code toe aan de methode `initializeSession()`. Deze code stelt uw app in staat om de vooruitgang die Azure Spatial Anchors SDK boekt op te volgen terwijl het frames van uw camerafeed verzamelt. Tijdens dat proces verandert de kleur van uw bol van de oorspronkelijke zwarte kleur naar grijs. Zodra er voldoende frames zijn verzameld om uw anker naar de cloud te sturen, wordt de bol wit. Vervolgens voorziet deze code de aanmeldingsgegevens die u nodig heeft om te communiceren met de back-end van de cloud. Hier kunt u uw app configureren om uw account-id, -sleutel en -domein te gebruiken. U hebt ze naar een teksteditor gekopieerd bij het [instellen van de Spatial Anchors-resource](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=89-120,142-148&highlight=11-37)]

Voeg vervolgens de volgende `uploadCloudAnchorAsync()`-methode toe in uw `mainActivity`-klasse. Zodra deze is aangeroepen wacht deze methode asynchroon tot er genoeg frames verzameld zijn van uw apparaat. Zodra dat gebeurd is, verandert de kleur van uw bol naar geel en begint het uw lokale Azure Spatial Anchor te uploaden naar de cloud. Wanneer de upload voltooid is, retourneert de code een anker-id.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Ten slotte gaan we alles aan elkaar koppelen. Voeg de volgende code toe aan de methode `handleTap()`. Hiermee wordt uw `uploadCloudAnchorAsync()`-methode aangeroepen zodra uw bol is gemaakt. Zodra de methode is geretourneerd, voert de onderstaande code een laatste update uit op uw bol, waardoor de kleur wordt gewijzigd in blauw.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=151-159,171-200&highlight=24-37)]

[Implementeer](#trying-it-out) uw app nogmaals. Beweeg uw apparaat, tik op het scherm en plaats uw bol. Ditmaal verandert de kleur van uw bol van zwart naar wit terwijl cameraframes worden verzameld. Zodra er voldoende frames zijn, verandert de bol in geel en wordt het uploaden naar de cloud gestart. Zodra het uploaden is voltooid, wordt de bol blauw. U kunt eventueel ook het venster `Logcat` in Android Studio gebruiken om de logboekberichten te controleren die uw app verzendt. Denk bijvoorbeeld aan de voortgang van de sessie terwijl frames worden vastgelegd of het anker-id dat de cloud retourneert zodra de upload voltooid is.

## <a name="locate-your-cloud-spatial-anchor"></a>Het ruimtelijke anker in de cloud opzoeken

Als het anker naar de cloud is geüpload, gaan we proberen het weer te vinden. Laten we eerst de volgende importen toevoegen aan uw code.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=45-48&highlight=3-4)]

Vervolgens voegen we de volgende code toe aan de methode `handleTap()`. Met deze code gebeurt het volgende:

- Verwijder onze bestaande blauwe bol van het scherm.
- Initialiseer onze Azure Spatial Anchors-sessie opnieuw. Deze actie zorgt ervoor dat het anker waar we naar op zoek gaan afkomstig is uit de cloud en niet het lokale anker dat we gemaakt hebben.
- Voer een query uit voor het anker dat we hebben geüpload naar de cloud.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Laten we nu de code koppelen die zal worden aangeroepen wanneer het gezochte anker gevonden wordt. Voeg de volgende code toe binnen uw `initializeSession()`-methode. Dit fragment maakt en plaatst een groene bol zodra het spatial anchor in de cloud is gevonden. Ook wordt tikken op het scherm weer geactiveerd, zodat u het hele scenario nog een keer kunt herhalen: nog een lokaal anker maken, het uploaden en het weer zoeken.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=34-53)]

Dat is alles. [Implementeer](#trying-it-out) uw app een laatste keer opnieuw uit om het hele scenario van begin tot eind uit te proberen. Verplaats uw apparaat en plaats uw zwarte bol. Beweeg vervolgens uw apparaat om cameraframes vast te leggen totdat de bol geel wordt. Uw lokale anker wordt geüpload en uw bol wordt blauw. Tik ten slotte nog één keer op uw scherm, zodat uw lokale anker wordt verwijderd en zoek vervolgens naar het equivalent in de cloud. Blijf doorgaan met het verplaatsen van uw apparaat totdat het ruimtelijke anker in de cloud is gevonden. Er wordt een groene bol op de juiste locatie weergegeven en u kunt het hele scenario weer herhalen.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]
