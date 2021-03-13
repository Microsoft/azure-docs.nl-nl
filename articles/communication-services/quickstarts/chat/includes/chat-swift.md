---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 2/11/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 75983fe89c7f8643701da96493d1b6c9ca1c159f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021741"
---
## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat, moet u het volgende doen:

- Maak een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie. 
- [Xcode](https://developer.apple.com/xcode/) en [Cocoapods](https://cocoapods.org/)installeren we gebruiken Xcode om een IOS-toepassing te maken voor de Snelstartgids en Cocoapods om afhankelijkheden te installeren.
- Maak een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../../create-communication-resource.md) voor meer informatie. U moet **uw resource-eind punt vastleggen** voor deze Quick Start.
- Maak **twee** ACS-gebruikers en geef ze een [toegangs token](../../access-tokens.md)voor gebruikers toegangs token. Zorg ervoor dat u het bereik instelt op **Chat** en **Noteer de token teken reeks, evenals de teken reeks GebruikersID**. In deze Snelstartgids maakt u een thread met een eerste deel nemer en voegt u vervolgens een tweede deel nemer aan de thread toe.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-ios-application"></a>Een nieuwe iOS-toepassing maken

Open Xcode en selecteer `Create a new Xcode project` .

Selecteer in het volgende venster `iOS` als platform en `App` voor de sjabloon.

Bij het kiezen van opties voert u `ChatQuickstart` de project naam in. Selecteer `Storyboard` als de-interface, `UIKit App Delegate` als de levens cyclus en `Swift` als de taal.

Klik op volgende en selecteer de map waarin u het project wilt maken.

### <a name="install-the-libraries"></a>De bibliotheken installeren

We gebruiken Cocoapods om de benodigde communicatie Services-afhankelijkheden te installeren.

Ga vanaf de opdracht regel in de hoofdmap van het `ChatQuickstart` IOS-project.

Een Podfile maken: `pod init`

Open de Podfile en voeg de volgende afhankelijkheden toe aan het `ChatQuickstart` doel:
```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

De afhankelijkheden installeren, wordt er ook een Xcode-werk ruimte gemaakt: `pod install`

**Nadat u de pod-installatie hebt uitgevoerd, opent u het project opnieuw in Xcode door de zojuist gemaakte optie te selecteren `.xcworkspace` .**

### <a name="setup-the-placeholders"></a>De tijdelijke aanduidingen instellen

Open de werk ruimte `ChatQuickstart.xcworkspace` in Xcode en open `ViewController.swift` .

In deze Snelstartgids voegen we onze code toe aan `viewController` en bekijken we de uitvoer in de Xcode-console. In deze Quick Start wordt het bouwen van een gebruikers interface in iOS niet geadresseerd. 

Bovenaan `viewController.swift` importeren de `AzureCommunication` `AzureCommunicatonChat` bibliotheken en:

```
import AzureCommunication
import AzureCommunicationChat
```

Kopieer de volgende code in de `viewDidLoad()` methode van `ViewController` :

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

We gebruiken een semafoor om onze code te synchroniseren voor demonstratie doeleinden. In de volgende stappen worden de tijdelijke aanduidingen vervangen door voorbeeld code met behulp van de Azure Communication Services-chat bibliotheek.


### <a name="create-a-chat-client"></a>Een chat-client maken

Vervang de opmerking `<CREATE A CHAT CLIENT>` door de volgende code:

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
    let credential =
    try CommunicationTokenCredential(
        token: "<ACCESS_TOKEN>"
    )
    let options = AzureCommunicationChatClientOptions()

    let chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: options
    )
```

Vervang door `<ACS_RESOURCE_ENDPOINT>` het eind punt van uw ACS-resource.
Vervang door `<ACCESS_TOKEN>` een geldig ACS-toegangs token.

Deze Snelstartgids heeft geen betrekking op het maken van een servicelaag voor het beheren van tokens voor uw chat toepassing, hoewel dit wordt aanbevolen. Raadpleeg de volgende documentatie voor meer informatie over de [architectuur van chatten](../../../concepts/chat/concepts.md)

Meer informatie over [tokens voor gebruikerstoegang](../../access-tokens.md).

## <a name="object-model"></a>Objectmodel 
De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services chat-clientbibliotheek voor JavaScript.

| Naam                                   | Beschrijving                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Deze klasse is vereist voor de chat-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om threads te maken, krijgen en verzenden. |
| ChatThreadClient | Deze klasse is vereist voor de functionaliteit van de chat-thread. U verkrijgt een instantie via de ChatClient en gebruikt deze om berichten te verzenden, te ontvangen, bij te werken of te verwijderen, gebruikers toe te voegen, te verwijderen of op te halen, getypte meldingen te verzenden, bevestigingen te lezen en u op chatgebeurtenissen te abonneren. |

## <a name="start-a-chat-thread"></a>Een chat-thread starten

Nu gaan we ons gebruiken `ChatClient` om een nieuwe thread te maken met een eerste gebruiker.

Vervang de opmerking `<CREATE A CHAT THREAD>` door de volgende code:

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

Vervang door `<USER_ID>` een geldige gebruikers-id voor communicatie Services.

We gebruiken hier een semafoor om te wachten op de voltooiings-handler voordat u doorgaat. We gebruiken de `threadId` van de reactie die wordt geretourneerd naar de voltooiings-handler in latere stappen.

## <a name="get-a-chat-thread-client"></a>Een chat-thread-client ophalen

Nu we een chat-thread hebben gemaakt, krijgen we een `ChatThreadClient` bewerking voor het uitvoeren van bewerkingen binnen de thread.

Vervang de opmerking `<CREATE A CHAT THREAD CLIENT>` door de volgende code:

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Vervang de opmerking `<SEND A MESSAGE>` door de volgende code:

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
)

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

Eerst maken we de `SendChatMessageRequest` gegevens die de weergave naam van de inhoud en de afzenders bevatten (de geschiedenis van de share kan eventueel ook worden opgenomen). Het antwoord dat wordt geretourneerd naar de voltooiings-handler bevat de ID van het bericht dat is verzonden.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Een gebruiker toevoegen als deel nemer aan de chat thread

Vervang de opmerking `<ADD A USER>` door de volgende code:

```
let user = Participant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

Vervang door `<USER_ID>` de ACS-gebruikers-id van de gebruiker die u wilt toevoegen.

Wanneer een deel nemer aan een thread wordt toegevoegd, kan het antwoord dat de voltooiing retourneert fouten bevatten. Deze fouten duiden op een fout bij het toevoegen van bepaalde deel nemers.

## <a name="list-users-in-a-thread"></a>Gebruikers in een thread weer geven

Vervang de opmerking `<LIST USERS>` door de volgende code:

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print(user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>Gebruiker verwijderen uit een chat-thread

Vervang de opmerking `<REMOVE A USER>` door de volgende code:

```
chatThreadClient
    .remove(
        participant: CommunicationUserIdentifier("<USER_ID>")
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

Vervang door `<USER ID>` de gebruikers-id van de communicatie services van de deel nemer die wordt verwijderd.

## <a name="run-the-code"></a>De code uitvoeren

Klik in Xcode op de knop uitvoeren om het project te bouwen en uit te voeren. In de-console kunt u de uitvoer van de code en de logboek uitvoer van de ChatClient weer geven.


