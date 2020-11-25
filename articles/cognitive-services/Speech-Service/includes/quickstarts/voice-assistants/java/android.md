---
title: 'Quickstart: Een Custom Voice-assistent maken, Java (Android) - Speech-service'
titleSuffix: Azure Cognitive Services
description: Leer een Custom Voice-assistent maken in Java op Android met behulp van Speech-SDK.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: travisw
ms.openlocfilehash: bc27c73fbf9e827ce9a95c8aaaddc82ffb0aec53
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95096874"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een resource voor de Azure-spraakservice maken](../../../../overview.md#try-the-speech-service-for-free)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](~/articles/cognitive-services/speech-service/quickstarts/setup-platform.md?tabs=android&pivots=programming-language-java)
> * Een bot maken die verbonden is met het [Direct Line Speech-kanaal](/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Ervoor zorgen dat u toegang tot een microfoon hebt voor het vastleggen van audio

  > [!NOTE]
  > Raadpleeg [de lijst met ondersteunde regio's voor spraakassistenten](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) en zorg ervoor dat uw resources in een van deze regio's worden geïmplementeerd.

## <a name="create-and-configure-a-project"></a>Een project maken en configureren

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Gebruikersinterface maken

In deze sectie gaan we een eenvoudige gebruikersinterface (UI) maken voor de toepassing. Laten we beginnen met de hoofdactiviteit te openen: `activity_main.xml`. Het basissjabloon bevat een titelbalk met de naam van de toepassing en een `TextView` met de boodschap 'Hallo wereld!'.

Vervang daarna de inhoud van bestand `activity_main.xml` door de volgende code:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Deze XML zorgt voor een eenvoudige gebruikersinterface om te communiceren met uw bot.

- Het `button`-element initieert een interactie en roept de `onBotButtonClicked`-methode aan wanneer erop wordt geklikt.
- Het `recoText`-element geeft de spraak-naar-tekst-resultaten weer terwijl u met uw bot praat.
- Het `activityText`-element geeft de JSON-nettolading voor de meest recente Bot Framework-activiteit van uw bot weer.

De tekst en de grafische weergave van uw gebruikersinterface moeten er nu ongeveer als volgt uitzien:

![Schermopname van hoe uw gebruikersinterface voor 'Praten met uw bot' eruit zou moeten zien.](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open `MainActivity.java` en vervang de inhoud door de volgende code:

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from speech subscription information
            BotFrameworkConfig config = BotFrameworkConfig.fromSubscription(speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * De methode `onCreate` omvat code die toestemming vraagt voor gebruik van de microfoon en internet.

   * De methode `onBotButtonClicked` is, zoals eerder gezegd, de methode voor het afhandelen van het klikken op de knop. met een druk op de knop wordt een enkele interactie ('beurt') met uw bot geactiveerd.

   * De `registerEventListeners`-methode toont de gebeurtenissen die gebruikt worden door de `DialogServiceConnector` en de basisverwerking van inkomende activiteiten.

1. Vervang in hetzelfde bestand de configuratietekenreeksen zodat deze overeenkomen met uw resources:

    * Vervang `YourSpeechSubscriptionKey` door uw abonnementssleutel.

    * Vervang `YourServiceRegion` door de [regio](~/articles/cognitive-services/speech-service/regions.md) van uw abonnement. Momenteel bieden slechts enkele regio's van Speech-service ondersteuning voor Direct Line Speech. Zie [regio's](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) voor meer informatie.

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

1. Sluit uw Android-apparaat aan op de ontwikkelmachine. Zorg ervoor dat u de [ontwikkelmodus en USB-foutopsporing](https://developer.android.com/studio/debug/dev-options) hebt ingeschakeld op het apparaat.

1. Druk op Ctrl + F9 om de toepassing te compileren, of kies **Build** > **Make Project** in de menubalk.

1. Als u de toepassing wilt starten, drukt u op Shift + F10 of kiest u **Run** > **Run 'app'** .

1. Kies uw Android-apparaat in het venster Select Deployment Target dat wordt weergegeven.

   ![Schermafbeelding van het venster Select Deployment Target](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-12-deploy.png)

Zodra de toepassing en de bijbehorende activiteiten zijn opgestart, klikt u op de knop om met uw bot te beginnen praten. Terwijl u spreekt wordt getranscribeerde tekst weergegeven. De meest recente activiteit die u van uw bot heeft ontvangen, verschijnt wanneer die beschikbaar is. Als uw bot is geconfigureerd voor gesproken reacties, dan speelt de spraak-naar-tekst automatisch.

![Schermafbeelding van de Android-toepassing](~/articles/cognitive-services/speech-service/media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]