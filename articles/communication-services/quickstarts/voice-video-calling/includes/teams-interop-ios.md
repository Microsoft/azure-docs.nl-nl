---
title: 'Quickstart: deelnemen aan een Teams-vergadering vanuit een iOS-app'
description: In deze zelfstudie leert u hoe u deel kunt nemen aan een Teams-vergadering met behulp van de Azure Communication Services Calling SDK voor iOS
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 363799cee5d66b718bb8ba06f4afd442add15148
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564744"
---
In deze quickstart leert u hoe u deel kunt nemen aan een Teams-vergadering met behulp van de Azure Communication Services Calling SDK voor iOS.

## <a name="prerequisites"></a>Vereisten

- Een werkende Communication Services [aanroepen van iOS-app](../getting-started-with-calling.md).
- Een [Teams implementatie](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>De besturingselementen voor de Gebruikersinterface van Teams toevoegen en De besturingselementen voor de gebruikersinterface van Teams inschakelen

Vervang code in ContentView.swift door het volgende codefragment. Het tekstvak wordt gebruikt voor het invoeren van de context van de Teams-vergadering en de knop wordt gebruikt om deel te nemen aan de opgegeven vergadering:

```swift

import SwiftUI
import AzureCommunicationCalling
import AVFoundation

struct ContentView: View {
    @State var meetingLink: String = ""
    @State var callStatus: String = ""
    @State var message: String = ""
    @State var recordingStatus: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var callObserver: CallObserver?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Teams meeting link", text: $meetingLink)
                    Button(action: joinTeamsMeeting) {
                        Text("Join Teams Meeting")
                    }.disabled(callAgent == nil)
                    Button(action: leaveMeeting) {
                        Text("Leave Meeting")
                    }.disabled(call == nil)
                    Text(callStatus)
                    Text(message)
                    Text(recordingStatus)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
            var userCredential: CommunicationTokenCredential?
            do {
                userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
            } catch {
                print("ERROR: It was not possible to create user credential.")
                self.message = "Please enter your token in source code"
                return
            }

            self.callClient = CallClient()

            // Creates the call agent
            self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
                if error == nil {
                    guard let agent = agent else {
                        self.message = "Failed to create CallAgent"
                        return
                    }

                    self.callAgent = agent
                    self.message = "Call agent successfully created."
                } else {
                    self.message = "Failed to create CallAgent with error"
                }
            }
        }
    }

    func joinTeamsMeeting() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                let joinCallOptions = JoinCallOptions()
                let teamsMeetingLinkLocator = TeamsMeetingLinkLocator(meetingLink: self.meetingLink);
                guard let call = self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) else {
                    self.message = "Failed to join Teams meeting"
                    return
                }

                self.call = call
                self.callObserver = CallObserver(self)
                self.call!.delegate = self.callObserver
                self.message = "Teams meeting joined successfully"
            }
        }
    }

    func leaveMeeting() {
        if let call = call {
            call.hangup(options: nil, completionHandler: { (error) in
                if error == nil {
                    self.message = "Leaving Teams meeting was successful"
                } else {
                    self.message = "Leaving Teams meeting failed"
                }
            })
        } else {
            self.message = "No active call to hanup"
        }
    }
}

class CallObserver : NSObject, CallDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func onCallStateChanged(_ call: Call!,
                                   args: PropertyChangedEventArgs!) {
        owner.callStatus = CallObserver.callStateToString(state: call.state)
        if call.state == .disconnected {
            owner.call = nil
            owner.message = "Left Meeting"
        } else if call.state == .inLobby {
            owner.message = "Waiting in lobby !!"
        } else if call.state == .connected {
            owner.message = "Joined Meeting !!"
        }
    }
    
    public func onIsRecordingActiveChanged(_ call: Call!, args: PropertyChangedEventArgs!) {
        if (call.isRecordingActive == true) {
            owner.recordingStatus = "This call is being recorded"
        }
        else {
            owner.recordingStatus = ""
        }
    }

    private static func callStateToString(state: CallState) -> String {
        switch state {
        case .connected: return "Connected"
        case .connecting: return "Connecting"
        case .disconnected: return "Disconnected"
        case .disconnecting: return "Disconnecting"
        case .earlyMedia: return "EarlyMedia"
        case .hold: return "Hold"
        case .incoming: return "Incoming"
        case .none: return "None"
        case .ringing: return "Ringing"
        case .inLobby: return "InLobby"
        default: return "Unknown"
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

## <a name="get-the-teams-meeting-link"></a>Koppeling naar de Teams-vergadering ophalen

De koppeling naar de Teams-vergadering kan worden opgehaald met behulp van Graph API’s. Dit wordt beschreven in [Graph-documentatie](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
The aanroepende SDK voor Communication Services accepteert een volledige koppeling naar een Teams-vergadering. Deze koppeling wordt geretourneerd als onderdeel van de `onlineMeeting` resource, die toegankelijk is onder de [ `joinWebUrl` eigenschap](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). U kunt ook de vereiste informatie over de vergadering verkrijgen via de URL van **de join-vergadering** in de teamsvergadering uitnodiging zelf.

## <a name="launch-the-app-and-join-teams-meeting"></a>De app starten en deelnemen aan de Teams-vergadering

U kunt uw app maken en uitvoeren op een iOS-simulator door **Product** > **Uitvoeren** te selecteren of door de sneltoets (&#8984;-R) te gebruiken.

:::image type="content" source="../media/ios/acs-join-teams-meeting-quickstart.png" alt-text="Schermopname met de voltooide toepassing.":::

Voeg de context van Teams in het tekstvak in en druk op *Deelnemen aan Teams-vergadering* om deel te nemen aan de Teams-vergadering vanuit uw Communication Services-toepassing.
