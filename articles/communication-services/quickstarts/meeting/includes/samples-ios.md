---
title: De Azure Communication Services-teams insluiten voor iOS
description: In dit overzicht leert u hoe u de Azure Communication Services-teams bibliotheek voor iOS kunt gebruiken.
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 0a1dd8f69cb79e42e56ab44981820e31abf204e1
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803686"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Een geïmplementeerde Communication Services-resource. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een `User Access Token` om de aanroepende client in te schakelen. Voor meer informatie over het [verkrijgen van een `User Access Token`](../../access-tokens.md)
- Voltooi de Snelstartgids om aan de [slag te gaan met het toevoegen van teams insluiten in uw toepassing](../getting-started-with-teams-embed.md)

## <a name="teams-embed-events"></a>Gebeurtenissen insluiten in teams

Voeg de `MeetingUIClientDelegate` aan uw klasse toe.

```swift
class ViewController: UIViewController, MeetingUIClientDelegate {

    private var meetingClient: MeetingUIClient?
```

Stel de `meetingUIClientDelegate` to in op `self` .

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    meetingClient?.meetingUIClientDelegate = self
}
```

Implementeer de `didUpdateCallState` `didUpdateRemoteParticipantCount` functies en.

```swift
    func meetingUIClient(didUpdateCallState callState: CallState) {
        switch callState {
        case .connecting:
            print("Call state has changed to 'Connecting'")
        case .connected:
            print("Call state has changed to 'Connected'")
        case .waitingInLobby:
            print("Call state has changed to 'Waiting in Lobby'")
        case .ended:
            print("Call state has changed to 'Ended'")
        }
    }
    
    func meetingUIClient(didUpdateRemoteParticipantCount remoteParticpantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticpantCount)")
    }
```

## <a name="assigning-avatars-for-users"></a>Avatars toewijzen voor gebruikers

Voeg de `MeetingUIClientIdentityProviderDelegate` aan uw klasse toe.

```swift
class ViewController: UIViewController, MeetingUIClientIdentityProviderDelegate {

    private var meetingClient: MeetingUIClient?
```

Stel de `MeetingUIClientIdentityProviderDelegate` in op `self` voordat u deelneemt aan de vergadering.

```swift
private func joinMeeting() {
    meetingClient?.meetingUIClientIdentityProviderDelegate = self
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")

    meetingClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Elk `userMri` met de bijbehorende avatar toewijzen.

```swift
    func avatarFor(userIdentifier: String, completionHandler: @escaping (UIImage?) -> Void) {
        if (userIdentifier.starts(with: "8:teamsvisitor:")) {
            // Anonymous teams user will start with prefix 8:teamsvistor:
            let image = UIImage (named: "avatarPink")
            completionHandler(image!)
        }
        else if (userIdentifier.starts(with: "8:orgid:")) {
            // OrgID user will start with prefix 8:orgid:
            let image = UIImage (named: "avatarDoctor")
            completionHandler(image!)
        }
        else if (userIdentifier.starts(with: "8:acs:")) {
            // ACS user will start with prefix 8:acs:
            let image = UIImage (named: "avatarGreen")
            completionHandler(image!)
        }
        else {
            completionHandler(nil)
        }
}
```