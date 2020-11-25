---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: c341ba20ece26e15255faf086e5bd2904fbaa797
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95097104"
---
In deze Quick Start leert u hoe u de Speech Devices SDK voor Android kunt gebruiken om een spraakgestuurd product te maken of dit te gebruiken als [gesprektranscriptie](../conversation-transcription.md)apparaat.

Voor deze hulplijn is een [Azure Cognitive Services-account](../overview.md#try-the-speech-service-for-free) met een Spraakservice-resource vereist.

De broncode voor de voorbeeldtoepassing is opgenomen in de Speech Devices SDK. Deze is ook [beschikbaar op GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het gebruik van de Speech Devices SDK, moet u het volgende doen:

- Volg de instructies in uw [Development Kit](../get-speech-devices-sdk.md) om het apparaat in te schakelen.

- Download de nieuwste versie van de [Speech Devices SDK](../speech-devices-sdk.md) en pak het zipbestand uit in uw werkmap.

  > [!NOTE]
  > In deze quickstart wordt ervan uitgegaan dat de app is geëxtraheerd naar C:\SDSDK\Android-Sample-Release

- Een [Azure-abonnementssleutel voor Spraakservice](../overview.md#try-the-speech-service-for-free) verkrijgen

- Als u van plan bent om de gesprektranscriptie te gebruiken, moet u een [circulair microfoonapparaat gebruiken](../get-speech-devices-sdk.md); deze functie is momenteel alleen beschikbaar voor en-US en zh-CN in de regio's centraalus en eastasia. U moet een spraaksleutel hebben in een van deze regio's om gesprektranscriptie te kunnen gebruiken.

- Als u van plan bent de Spraakservice te gebruiken om de intenties (of acties) te identificeren van uitingen van gebruikers, hebt u een [Language Understanding Service (LUIS)](../../luis/luis-how-to-azure-subscription.md)-abonnement nodig. Zie [Spraakintenties herkennen met LUIS, C#](../how-to-recognize-intents-from-speech-csharp.md) voor meer informatie over LUIS en intentieherkenning.

  U kunt [een eenvoudig LUIS-model maken](../../luis/index.yml) of het LUIS-voorbeeldmodel gebruiken, LUIS-example.json. Het LUIS-voorbeeldmodel is beschikbaar op de [downloadsite van Speech Devices SDK](https://aka.ms/sdsdk-luis). Als u het JSON-bestand van uw model wilt uploaden naar de [LUIS-portal](https://www.luis.ai/home), selecteert u **Nieuwe app importeren** en selecteert u vervolgens het JSON-bestand.

- Installeer [Android Studio](https://developer.android.com/studio/) en [Vysor](https://vysor.io/download/) op uw pc.

## <a name="set-up-the-device"></a>Het apparaat instellen

1. Start Vysor op uw computer.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. Uw apparaat moet zijn vermeld onder **Kies een apparaat**. Selecteer de knop **Weergave** naast het apparaat.

1. Maak verbinding met uw draadloze netwerk door het mappictogram te selecteren en vervolgens **Instellingen** > **WLAN** te selecteren.

   ![WLAN voor Vysor](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Als uw bedrijf beleidsregels heeft over het verbinden van apparaten met het Wi-Fi-systeem, moet u het MAC-adres verkrijgen en contact opnemen met uw IT-afdeling voor informatie over hoe u verbinding kunt maken met de Wi-Fi van uw bedrijf.
   >
   > Als u het MAC-adres van de Development Kit wilt vinden, selecteert u het pictogram van de bestandsmap op het bureaublad van de Development Kit.
   >
   > ![Bestandsmap Vysor](../media/speech-devices-sdk/qsg-10.png)
   >
   > Selecteer **Instellingen**. Zoek naar 'MAC-adres' en selecteer vervolgens **Mac-adres** > **Geavanceerd WLAN**. Noteer het MAC-adres dat onderaan in het dialoogvenster wordt weergegeven.
   >
   > ![MAC-adres van Vysor](../media/speech-devices-sdk/qsg-11.png)
   >
   > Sommige bedrijven hebben mogelijk een tijdslimiet voor hoe lang een apparaat verbonden kan blijven met hun Wi-Fi-systeem. Mogelijk moet u de registratie van de Development Kit bij uw Wi-Fi-systeem na een bepaald aantal dagen verlengen.

## <a name="run-the-sample-application"></a>De voorbeeldtoepassing uitvoeren

Als u de Setup van de Development Kit wilt valideren, maakt en installeert u de voorbeeldtoepassing:

1. Start Android Studio.

1. Selecteer **Open een bestaand Android Studio project**.

   ![Android Studio - Open een bestaand project](../media/speech-devices-sdk/qsg-5.png)

1. Ga naar C:\SDSDK\Android-Sample-Release\example. Selecteer **OK** om het voorbeeldproject te openen.

1. Configureer Gradle om te verwijzen naar de Speech SDK. De volgende bestanden zijn te vinden onder **Gradle-scripts** in Android Studio.

    Werk de **build.gradle(Project:example)** bij, zodat het AllProjects-blok met het onderstaande overeenkomt, door de Maven-regels toe te voegen.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    Werk de **build.gradle(Module:app)** bij door deze regel toe te voegen aan de sectie met afhankelijkheden. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.14.0'
    ```
    
1. Voeg de sleutel van uw Speech-abonnement toe aan de broncode. Als u de intentieherkenning wilt proberen, voegt u ook de sleutel en toepassings-id van uw [Language Understanding Service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)-abonnement toe.

   Voor spraak en LUIS gaat uw informatie naar MainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Als u gesprektranscriptie gebruikt, zijn uw spraaksleutel en regio-informatie ook vereist in conversation.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Het standaardtrefwoord is 'computer'. U kunt ook een van de andere beschikbare trefwoorden, zoals 'machine' of 'assistent', proberen. De resourcebestanden voor deze alternatieve trefwoorden bevinden zich in de Speech Devices SDK in de map voor trefwoorden. C:\SDSDK\Android-Sample-Release\keyword\Computer bevat bijvoorbeeld de bestanden die worden gebruikt voor het trefwoord 'computer'.

   > [!TIP]
   > U kunt ook [een aangepast trefwoord maken](../custom-keyword-basics.md).

   Als u een nieuw trefwoord wilt gebruiken, werkt u de volgende twee regels bij in `MainActivity.java`, en kopieert u het trefwoordpakket naar uw app. Als u bijvoorbeeld het trefwoord 'machine' uit het trefwoordpakket kws-machine.zip wilt gebruiken:

   - Kopieer het trefwoordpakket naar de map C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\\".
   - Werk de `MainActivity.java` bij met het trefwoord en de pakketnaam:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Werk de volgende regels bij, die de geometrie-instellingen van de microfoonmatrix bevatten:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Deze tabel geeft een lijst van ondersteunde waarden weer:

   | Variabele | Betekenis | Beschikbare waarden |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Fysieke microfoonconfiguratie | Voor een circulaire Development Kit: `Circular6+1` |
   |          |         | Voor een lineaire Development Kit: `Linear4` |
   | `SelectedGeometry` | Softwareconfiguratie microfoon | Voor een circulaire Development Kit die gebruikmaakt van alle microfoons: `Circular6+1` |
   |          |         | Voor een circulaire Development Kit die gebruikmaakt van vier microfoons: `Circular3+1` |
   |          |         | Voor een lineaire Development Kit die gebruikmaakt van alle microfoons: `Linear4` |
   |          |         | Voor een lineaire Development Kit die gebruikmaakt van twee microfoons: `Linear2` |

1. Als u de toepassing wilt compileren, selecteert u in het menu **Uitvoeren** **'App' uitvoeren**. Het dialoogvenster **Implementatiedoel selecteren** wordt weergegeven.

1. Selecteer uw apparaat en selecteer daarna **OK** om de toepassing op het apparaat te implementeren.

   ![Het dialoogvenster Implementatiedoel selecteren](../media/speech-devices-sdk/qsg-7.png)

1. De Speech Devices SDK-voorbeeldtoepassing wordt gestart en de volgende opties worden weergegeven:

   ![Voorbeeld van de Speech Devices SDK-voorbeeldtoepassing en opties](../media/speech-devices-sdk/qsg-8.png)

1. Probeer de nieuwe demo voor gesprektranscriptie. Begin met transcriberen met 'Sessie starten'. Standaard is iedereen een gast. Als u echter stemkarakteristieken van een deelnemer hebt, kunnen ze in een bestand `/video/participants.properties` op het apparaat worden geplaatst. Als u de stemkarakteristiek wilt genereren, bekijkt u [Gesprekken transcriberen (SDK)](../how-to-use-conversation-transcription.md).

   ![Demo gesprektranscriptietoepassing](../media/speech-devices-sdk/qsg-15.png)

1. Experiment!

## <a name="troubleshooting"></a>Problemen oplossen

Als u geen verbinding kunt maken met het spraakapparaat. Typ de volgende opdracht in het opdrachtpromptvenster. Er wordt dan een lijst met apparaten geretourneerd:

```powershell
 adb devices
```

> [!NOTE]
> Deze opdracht maakt gebruik van de Android Debug Bridge, `adb.exe`, die deel uitmaakt van de Android Studio-installatie. Dit hulpprogramma bevindt zich in C:\Users\[gebruikersnaam] \AppData\Local\Android\Sdk\platform-tools. U kunt deze map toevoegen aan uw pad om het gemakkelijker te maken om `adb` aan te roepen. Anders moet u het volledige pad naar uw installatie van adb.exe opgeven in elke opdracht waarin `adb` wordt aangeroepen.
>
> Als u een fout `no devices/emulators found` opmerkt, controleer dan of de USB-kabel is aangesloten en of er een kabel met een hoge kwaliteit wordt gebruikt.