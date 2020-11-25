---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: c9ed54f11cade20af67a1c9bfe948b03e9d7b0d3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95097047"
---
In deze quickstart leert u hoe u de Speech Devices SDK voor Windows kunt gebruiken om een spraakgestuurd product te maken of dit te gebruiken als apparaat voor [gesprektranscriptie](../conversation-transcription.md). Voor gesprektranscriptie wordt alleen de [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) ondersteund. Voor andere soorten spraak gebruikt u lineaire microfoonmatrices die een microfoonmatrixgeometrie bieden. Deze wordt ondersteund.

De toepassing wordt gebouwd met het Speech SDK-pakket en de Eclipse Java IDE (v4) op 64-bits Windows. De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

Voor deze gids is een [Azure Cognitive Services-account](../overview.md#try-the-speech-service-for-free) vereist met een Speech-serviceresource.

De broncode voor de [voorbeeldtoepassing](https://aka.ms/sdsdk-download-JRE) is opgenomen in de Speech Devices SDK. Deze is ook [beschikbaar op GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Vereisten

Voor deze snelstart zijn de volgende zaken vereist:

* Besturingssysteem: 64-bits Windows
* Een microfoonmatrix, bijvoorbeeld [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Eclipse Java IDE](https://www.eclipse.org/downloads/)
* Alleen [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) of [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Microsoft Visual C++ Redistributable](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)
* Een Azure-abonnementssleutel voor de Spraakservice. [Gratis downloaden](../overview.md#try-the-speech-service-for-free).
* Download de nieuwste versie van de [Speech Devices SDK](https://aka.ms/sdsdk-download-JRE) voor Java en pak het zipbestand uit in uw werkmap.
   > [!NOTE]
   > In deze quickstart wordt ervan uitgegaan dat de app is geëxtraheerd naar C:\SDSDK\JRE-Sample-Release

Gesprektranscriptie is momenteel alleen beschikbaar voor en-US en zh-CN in de regio's centraalus en eastasia. Voor deze regio's moet u een spraaksleutel hebben om gesprektranscriptie te kunnen gebruiken.

Als u van plan bent om de intenties te gebruiken, hebt u een [Language Understanding Service (LUIS)](../../luis/luis-how-to-azure-subscription.md)-abonnement nodig. Zie [Spraakintenties herkennen met LUIS, C#](../how-to-recognize-intents-from-speech-csharp.md) voor meer informatie over LUIS en intentieherkenning. Er is een [LUIS-voorbeeldmodel](https://aka.ms/sdsdk-luis) beschikbaar voor deze app.

## <a name="create-and-configure-the-project"></a>Het project maken en configureren

1. Start Eclipse.

1. Voer in de **Eclipse IDE Launcher**, in het veld **Werkruimte**, de naam van een nieuwe werkruimtemap in. Selecteer vervolgens **Starten**.

   ![Schermopname met het Eclipse-startprogramma waar u de naam van de map voor de werkruimte invoert.](../media/speech-devices-sdk/eclipse-launcher.png)

1. Binnen enkele ogenblikken verschijnt het hoofdvenster van de Eclipse-IDE. Sluit het welkomstscherm als dit verschijnt.

1. Maak via de Eclipse-menubalk een nieuw project door **Bestand** > **Nieuw** > **Java-project** te kiezen. Kies **Project** en daarna **Java-project** als dit niet beschikbaar is.

1. De wizard **Nieuw Java-project** wordt gestart. **Blader**  naar de locatie van het voorbeeldproject. Selecteer **Finish**.

   ![Schermopname van de wizard Nieuw Java-project.](../media/speech-devices-sdk/eclipse-new-java-project.png)

1. Klik in de **Package Explorer** met de rechtermuisknop op uw project. Kies in het contextmenu **Configureren** > **Naar Maven-project converteren**. Selecteer **Finish**.

   ![Schermopname van Package explorer](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Open het bestand pom.xml en bewerk dit.

    Maak aan het einde van het bestand, vóór afsluitcode `</project>`, de elementen `repositories` en `dependencies`, zoals hier weergegeven, en zorg ervoor dat `version` overeenkomt met uw huidige versie:
    ```xml
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.14.0</version>
        </dependency>
    </dependencies>
   ```

1. Kopieer de inhoud van **Windows-x64-** naar de locatie van het Java-project, bijvoorbeeld **C:\SDSDK\JRE-Sample-Release**

1. Kopieer `kws.table`, `participants.properties` en `Microsoft.CognitiveServices.Speech.extension.pma.dll` naar de projectmap **target\classes**

## <a name="configure-the-sample-application"></a>De voorbeeldtoepassing configureren

1. Voeg de sleutel van uw Speech-abonnement toe aan de broncode. Als u de intentieherkenning wilt proberen, voegt u ook de sleutel en toepassings-id van uw [Language Understanding Service](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)-abonnement toe.

   Voor Speech en LUIS gaat uw informatie naar `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Als u gesprektranscriptie gebruikt, zijn uw Speech-sleutel en regio-informatie ook vereist in `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. Het standaardtrefwoord (trefwoord) is 'computer'. U kunt ook een van de andere beschikbare trefwoorden, zoals 'machine' of 'assistent', proberen. De resourcebestanden voor deze alternatieve trefwoorden bevinden zich in de Speech Devices SDK, in de map met trefwoorden. `C:\SDSDK\JRE-Sample-Release\keyword\Computer` bevat bijvoorbeeld de bestanden die worden gebruikt voor het trefwoord 'computer'.

    > [!TIP]
    > U kunt ook [een aangepast trefwoord maken](../custom-keyword-basics.md).

    Als u een nieuw trefwoord wilt gebruiken, werkt u de volgende regel in `FunctionsList.java` bij, en kopieert u het trefwoord naar uw app. Ga als volgt te werk als u bijvoorbeeld het trefwoord 'machine' uit het trefwoordpakket `machine.zip` wilt gebruiken:

   * Kopieer het `kws.table`-bestand vanuit het zip-pakket naar de projectmap **target/classes**.
   * Werk `FunctionsList.java` bij met de naam van het trefwoord:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>De voorbeeldtoepassing vanuit Eclipse uitvoeren

1. Selecteer op de Eclipse-menubalk **Run** > **Run As** > **Java Application**. Selecteer vervolgens **FunctionsList** en **OK**.

   ![Schermopname van een Java-toepassing selecteren](../media/speech-devices-sdk/eclipse-run-sample.png)

1. De Speech Devices SDK-voorbeeldtoepassing wordt gestart en de volgende opties worden weergegeven:

   ![Schermopname van een Speech Devices SDK-voorbeeldtoepassing en de opties.](../media/speech-devices-sdk/java-sample-app-windows.png)

1. Probeer de nieuwe demo voor **gesprektranscriptie**. Begin met transcriberen met **Sessie** > **Start**. Standaard is iedereen een gast. Als u echter stemkarakteristieken van een deelnemer hebt, kunnen ze in een bestand `participants.properties` in projectmap **target/classes** worden geplaatst. Als u de stemkarakteristiek wilt genereren, bekijkt u [Gesprekken transcriberen (SDK)](../how-to-use-conversation-transcription.md).

   ![Schermopname van een demo van de gesprektranscriptietoepassing.](../media/speech-devices-sdk/cts-sample-app-windows.png)

## <a name="create-and-run-a-standalone-application"></a>Een zelfstandige toepassing maken en uitvoeren

1. Klik in de **Package Explorer** met de rechtermuisknop op uw project. Kies **Exporteren**.

1. Het venster **Exporteren** wordt weergegeven. Vouw **Java**, selecteer **Uitvoerbaar JAR-bestand** en vervolgens **Volgende**.

   ![Schermopname van het venster Exporteren waar u het uitvoerbare JAR-bestand selecteert.](../media/speech-devices-sdk/eclipse-export-windows.png)

1. Het venster **Export van uitvoerbaar JAR-bestand** wordt weergegeven. Kies een **exportbestemming** voor de toepassing en selecteer **Voltooien**.

   ![Schermopname van het venster Export van uitvoerbaar JAR-bestand waar u het exportdoel kiest.](../media/speech-devices-sdk/eclipse-export-jar-windows.png)

1. Plaats `kws.table`, `participants.properties`, `unimic_runtime.dll`, `pma.dll` en `Microsoft.CognitiveServices.Speech.extension.pma.dll` in de hierboven gekozen doelmap, omdat deze bestanden nodig zijn voor de toepassing.

1. Zelfstandige toepassing uitvoeren

   ```powershell
   java -jar SpeechDemo.jar
   ```