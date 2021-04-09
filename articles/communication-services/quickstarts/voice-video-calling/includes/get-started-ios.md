---
title: 'Snelstart: oproepen toevoegen aan een iOS-app met behulp van Azure Communication Services'
description: In deze Quick Start leert u hoe u de Azure Communication Services roept SDK voor iOS te gebruiken.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 536b9a9a0d1a7b48841938eef44d181d22b87bf4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609528"
---
In deze Snelstartgids leert u hoe u een gesprek start met behulp van de Azure Communication Services-SDK voor iOS.

> [!NOTE]
> In dit document wordt versie 1.0.0-Beta. 8 van de aanroepende SDK gebruikt.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie moet aan de volgende vereisten worden voldaan:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Een Mac waarop [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) wordt uitgevoerd, evenals een geldig ontwikkelaarscertificaat dat is geïnstalleerd in uw Sleutelhanger.
- Een gebruikte Communication Services-resource. [Een Communication Services maken](../../create-communication-resource.md).
- Een [Token voor gebruikerstoegang](../../access-tokens.md) voor uw Azure Communication Service.

## <a name="setting-up"></a>Instellen

### <a name="creating-the-xcode-project"></a>Het Xcode-project maken

Maak in Xcode een nieuw iOS-project en selecteer de sjabloon **Single View-app** (Toepassing met één weergave). In deze zelfstudie wordt gebruikgemaakt van het [SwiftUI-framework](https://developer.apple.com/xcode/swiftui/). U moet dus de **taal** instellen op **Swift** en de **gebruikersinterface** op **SwiftUI**. Tijdens deze quickstart maakt u geen tests. U kunt **Tests opnemen** uitschakelen.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Schermafbeelding met het venster Nieuw project in Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installeer het pakket en de afhankelijkheden met CocoaPods

1. Als u een Podfile voor uw toepassing wilt maken, opent u de Terminal en navigeert u naar de projectmap en voert u ```pod init```
3. Voeg de volgende code toe aan de Podfile en sla deze op (zorg ervoor dat "doel" overeenkomt met de naam van uw project):

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

3. Voer `pod install` uit.
3. Open de `.xcworkspace` met Xcode.

### <a name="request-access-to-the-microphone"></a>Toegang tot de microfoon aanvragen

Als u toegang wilt krijgen tot de microfoon van het apparaat, moet u de eigenschappenlijst van de app bijwerken met een `NSMicrophoneUsageDescription`. U stelt de bijbehorende waarde in op een `string` die wordt opgenomen in het dialoogvenster dat het systeem gebruikt om toegang te vragen aan de gebruiker.

Klik met de rechtermuisknop op de `Info.plist`-vermelding van de projectstructuur en selecteer **Open As** > **Source Code**. Voeg de volgende regels toe in de bovenste sectie `<dict>` en sla het bestand op.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Open het bestand **ContentView.swift** van het project en voeg boven aan het bestand een `import`-declaratie toe om de `AzureCommunicationCalling library`te importeren. Bovendien moet u `AVFoundation` importeren, we hebben dit nodig voor het aanvragen van audio-toestemming in de code.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Vervang de implementatie van de `ContentView`-struct door enkele eenvoudige UI-besturingselementen waarmee een gebruiker een oproep kan initiëren en beëindigen. We voegen bedrijfslogica toe aan deze besturingselementen in deze snelstart.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services-aanroepende SDK:

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | De CallClient is het belangrijkste ingangs punt voor de aanroepende SDK.|
| CallAgent | De CallAgent wordt gebruikt om oproepen te starten en te beheren. |
| CommunicationTokenCredential | De CommunicationTokenCredential wordt gebruikt als de token referentie voor het instantiëren van de CallAgent.| 
| CommunicationUserIdentifier | De CommunicationUserIdentifier wordt gebruikt om de identiteit van de gebruiker te vertegenwoordigen. Dit kan een van de volgende zijn: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>De client verifiëren

Initialiseer een `CallAgent`-instantie met een token voor gebruikerstoegang waarmee we oproepen kunnen maken en ontvangen. Voeg de volgende code toe aan de `onAppear`-call back in **ContentView.swift**:

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

U moet `<USER ACCESS TOKEN>` vervangen door een geldig token voor gebruikerstoegang voor uw resource. Raadpleeg de documentatie inzake [Token voor gebruikerstoegang](../../access-tokens.md) als u nog geen token hebt.

## <a name="start-a-call"></a>Een oproep starten

De `startCall` methode wordt ingesteld als de actie die wordt uitgevoerd wanneer op de knop *Oproep starten* wordt getikt. Werk de implementatie bij om een oproep te starten met de `ASACallAgent`:

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(identifier: self.callee)]
            self.call = self.callAgent?.call(participants: callees, options: StartCallOptions())
        }
    }
}
```

U kunt ook de eigenschappen in `StartCallOptions` gebruiken om de initiële opties voor de oproep in te stellen (dat wil zeggen dat de oproep kan worden gestart met de microfoon gedempt).

## <a name="end-a-call"></a>Een oproep beëindigen

Implementeer de `endCall`-methode om de huidige oproep te beëindigen wanneer op de knop voor *Oproep beëindigen* wordt getikt.

```swift
func endCall()
{    
    self.call!.hangup(HangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>De code uitvoeren

U kunt uw app maken en uitvoeren op een iOS-simulator door **Product** > **Uitvoeren** te selecteren of door de sneltoets (&#8984;-R) te gebruiken.

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Laatste look en feel van de snelstart-app":::

U kunt een uitgaande VOIP-oproep maken door een gebruikers-ID op te geven in het tekstveld en te tikken op de knop **Oproep starten**. Door `8:echo123` te bellen, wordt u verbonden met een echo-bot. Dit is handig om aan de slag te gaan en te controleren of uw audio-apparaten werken. 

> [!NOTE]
> De eerste keer dat u een oproep doet, wordt u gevraagd om toegang tot de microfoon. In een productietoepassing moet u de `AVAudioSession`API[ gebruiken om de machtigingsstatus ](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) te controleren en het gedrag van uw toepassing bij te werken wanneer geen toestemming wordt verleend.

## <a name="sample-code"></a>Voorbeeldcode

U kunt de voorbeeld-app downloaden uit [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling).
