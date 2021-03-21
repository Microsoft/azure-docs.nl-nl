---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 4a281e547691b7dee2f1a6d3e57b1c0030dbbd57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612197"
---
## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat, moet u het volgende doen:

- Maak een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie.
- Installeer [Android Studio](https://developer.android.com/studio), we gebruiken Android Studio om een Android-toepassing te maken voor de Snelstartgids om afhankelijkheden te installeren.
- Maak een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../../create-communication-resource.md) voor meer informatie. U moet **uw resource-eind punt vastleggen** voor deze Quick Start.
- Maak **twee** communicatie Services-gebruikers en geef ze een [toegangs token](../../access-tokens.md)voor gebruikers toegangs token. Zorg ervoor dat u het bereik instelt op **Chat** en **Noteer de teken reeks token en de teken reeks GebruikersID**. In deze Quick Start maakt u een thread met een eerste deel nemer en voegt u vervolgens een tweede deel nemer aan de thread toe.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-android-application"></a>Een nieuwe Android-toepassing maken

1. Open Android Studio en selecteer `Create a new project` . 
2. Selecteer in het volgende venster `Empty Activity` als project sjabloon.
3. Wanneer u opties kiest, voert u `ChatQuickstart` in als project naam.
4. Klik op volgende en selecteer de map waarin u het project wilt maken.

### <a name="install-the-libraries"></a>De bibliotheken installeren

We gebruiken Gradle om de benodigde communicatie Services-afhankelijkheden te installeren. Navigeer vanuit de opdracht regel in de hoofdmap van het `ChatQuickstart` project. Open het bestand build. gradle van de app en voeg de volgende afhankelijkheden toe aan het `ChatQuickstart` doel:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.8'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.8'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Meta bestanden in de verpakkings opties uitsluiten in de hoofdmap build. gradle
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="add-a-maven-resource-in-root-buildgradle"></a>Een Maven-resource toevoegen in root build. gradle
```
allprojects {
    repositories {
        ...
        maven {
            url 'https://trouterpublicpackages.z13.web.core.windows.net'
        }
    }
```

Klik op nu synchroniseren in Android Studio.

#### <a name="alternative-to-install-libraries-through-maven"></a>Vervangen Bibliotheken installeren via maven
Als u de bibliotheek wilt importeren in uw project met behulp van het [maven](https://maven.apache.org/) -buildsysteem, voegt u deze toe aan de `dependencies` sectie van het bestand van de app en `pom.xml` geeft u de artefact-id en de versie op die u wilt gebruiken:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.7</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>De tijdelijke aanduidingen instellen

Open het bestand en bewerk het `MainActivity.java` . In deze Snelstartgids voegen we onze code toe aan `MainActivity` en bekijken we de uitvoer in de-console. Deze Quick Start helpt niet bij het bouwen van een gebruikers interface. Importeer bovenaan het bestand de `Communication common` `Communication chat` bibliotheken en:

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Kopieer de volgende code naar het bestand `MainActivity` :

```java
    private String second_user_id = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.7";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

In de volgende stappen worden de tijdelijke aanduidingen vervangen door voorbeeld code met behulp van de Azure Communication Services-chat bibliotheek.


### <a name="create-a-chat-client"></a>Een chat-client maken

Vervang de opmerking door `<CREATE A CHAT CLIENT>` de volgende code (plaats de instructies voor importeren boven aan het bestand):

```java
import com.azure.android.communication.chat.ChatClient;
import com.azure.android.core.http.HttpHeader;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient client = new ChatAsyncClient.Builder()
        .endpoint(endpoint)
        .credentialInterceptor(chain -> chain.proceed(chain.request()
                .newBuilder()
                .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                .build()))
        .build();
```

1. Gebruik de `ChatAsyncClient.Builder` om een exemplaar van te configureren en te maken `ChatAsyncClient` .
2. Vervang door `<resource>` uw communicatie Services-resource.
3. Vervang door `<user_access_token>` een geldig toegangs token voor communicatie Services.

## <a name="object-model"></a>Objectmodel
De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services chat-clientbibliotheek voor JavaScript.

| Naam                                   | Beschrijving                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Deze klasse is vereist voor de chat-functionaliteit. U maakt de app met uw abonnements gegevens en gebruikt deze om threads te maken, op te halen en te verwijderen. |
| ChatThreadClient/ChatThreadAsyncClient | Deze klasse is vereist voor de functionaliteit van de chat-thread. U verkrijgt een instantie via de ChatClient en gebruikt deze om berichten te verzenden, te ontvangen, bij te werken of te verwijderen, gebruikers toe te voegen, te verwijderen of op te halen, getypte meldingen te verzenden, bevestigingen te lezen en u op chatgebeurtenissen te abonneren. |

## <a name="start-a-chat-thread"></a>Een chat-thread starten

We gebruiken ons `ChatClient` om een nieuwe thread te maken met een eerste gebruiker.

Vervang de opmerking `<CREATE A CHAT THREAD>` door de volgende code:

```java
//  The list of ChatParticipant to be added to the thread.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
        .setCommunicationIdentifier(new CommunicationIdentifierModel().setCommunicationUser(new CommunicationUserIdentifierModel().setId(id)))
        .setDisplayName(displayName)
);

// The topic for the thread.
final String topic = "General";
// The model to pass to the create method.
CreateChatThreadRequest thread = new CreateChatThreadRequest()
  .setTopic(topic)
  .setParticipants(participants);

// optional, set a repeat request ID
final String repeatabilityRequestID = "";

client.createChatThread(thread, repeatabilityRequestID, new Callback<CreateChatThreadResult>() {
      public void onSuccess(CreateChatThreadResult result, okhttp3.Response response) {
              ChatThread chatThread = result.getChatThread();
              threadId = chatThread.getId();
              // take further action
              Log.i(TAG, "threadId: " + threadId);
      }

      public void onFailure(Throwable throwable, okhttp3.Response response) {
              // Handle error.
              Log.e(TAG, throwable.getMessage());
      }
});
```

Vervang door `<user_id>` een geldige gebruikers-id voor communicatie Services. We gebruiken `threadId` in latere stappen de van de reactie die wordt geretourneerd naar de voltooiings-handler, dus Vervang de `<thread_id>` in de-klasse door de van `threadId` deze aanvraag ontvangen en voer de app opnieuw uit.

## <a name="get-a-chat-thread-client"></a>Een chat-thread-client ophalen

Nu u een chat-thread hebt gemaakt, krijgen we een `ChatThreadClient` bewerking voor het uitvoeren van bewerkingen binnen de thread. Vervang de opmerking `<CREATE A CHAT THREAD CLIENT>` door de volgende code:

```
ChatThreadAsyncClient threadClient =
        new ChatThreadAsyncClient.Builder()
                .endpoint(endpoint)
                .credentialInterceptor(chain -> chain.proceed(chain.request()
                    .newBuilder()
                    .header(HttpHeader.AUTHORIZATION, "Bearer " + userAccessToken)
                    .build()))
                .build();
```

## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Zorg ervoor dat we hebben vervangen `<thread_id>` door een geldige thread-id. we sturen nu een bericht naar die thread.

Vervang de opmerking `<SEND A MESSAGE>` door de volgende code:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageRequest message = new SendChatMessageRequest()
        .setType(ChatMessageType.TEXT)
        .setContent(content)
        .setSenderDisplayName(senderDisplayName);

threadClient.sendChatMessage(threadId, message, new Callback<String>() {
        @Override
        public void onSuccess(String messageId, okhttp3.Response response) {
                // A string is the response returned from sending a message, it is an id,
                // which is the unique ID of the message.
                chatMessageId = messageId;
                // Take further action.
                Log.i(TAG, "chatMessageId: " + chatMessageId);
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Nadat we hebben ontvangen `chatMessageId` , kunnen we vervangen `<chat_message_id>` door `chatMessageId` voor toekomstig gebruik van de methode in Quick Start en de app opnieuw uitvoeren.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Een gebruiker toevoegen als deel nemer aan de chat thread

Vervang de opmerking `<ADD A USER>` door de volgende code:

```java
//  The list of ChatParticipant to be added to the thread.
participants = new ArrayList<>();
// The display name for the thread participant.
displayName = "a new participant";
participants.add(new ChatParticipant().setCommunicationIdentifier(
          new CommunicationIdentifierModel().setCommunicationUser(
              new CommunicationUserIdentifierModel().setId(second_user_id)
          )).setDisplayName(secondUserDisplayName));
// The model to pass to the add method.
AddChatParticipantsRequest addParticipantsRequest = new AddChatParticipantsRequest()
  .setParticipants(participants);

threadClient.addChatParticipants(threadId, addParticipantsRequest, new Callback<AddChatParticipantsResult>() {
        @Override
        public void onSuccess(AddChatParticipantsResult result, okhttp3.Response response) {
                // Take further action.
                Log.i(TAG, "add chat participants success");
        }
        
        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Vervang `<second_user_id>` in de klasse door de gebruikers-id van de communicatie services van de gebruiker die u wilt toevoegen. 

## <a name="list-users-in-a-thread"></a>Gebruikers in een thread weer geven

Vervang de opmerking `<LIST USERS>` door de volgende code:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

threadClient.listChatParticipantsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection,
            okhttp3.Response response) {
                // pageCollection enables enumerating list of chat participants.
                pageCollection.getFirstPage(new Callback<Page<ChatParticipant>>() {
                    @Override
                    public void onSuccess(Page<ChatParticipant> firstPage, okhttp3.Response response) {
                        for (ChatParticipant participant : firstPage.getItems()) {
                            // Take further action.
                            Log.i(TAG, "participant: " + participant.getDisplayName());
                        }
                        listChatParticipantsNext(firstPage.getNextPageId(), pageCollection);
                    }

                    @Override
                    public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                    }
                });

                @Override
                public void onFailure(Throwable throwable, okhttp3.Response response) {
                        // Handle error.
                        Log.e(TAG, throwable.getMessage());
                }
                });
        }

        @Override
        public void onFailure(Throwable throwable, okhttp3.Response response) {
                // Handle error.
                Log.e(TAG, throwable.getMessage());
        }
});
```

Plaats de volgende Help-methode in de-klasse:

```java
void listChatParticipantsNext(String nextLink, AsyncPagedDataCollection<ChatParticipant, Page<ChatParticipant>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatParticipant>>() {
            @Override
            public void onSuccess(Page<ChatParticipant> nextPage, Response response) {
                for (ChatParticipant participant : nextPage.getItems()) {
                    // Take further action.
                    Log.i(TAG, "participant: " + participant.getDisplayName());
                }
                if (nextPage.getPageId() != null) {
                    listChatParticipantsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}
```


## <a name="remove-user-from-a-chat-thread"></a>Gebruiker verwijderen uit een chat-thread

Zorg ervoor dat u vervangt door `<second_user_id>` een geldige gebruikers-id. de tweede gebruiker wordt nu uit de thread verwijderd.

Vervang de opmerking `<REMOVE A USER>` door de volgende code:

```java
CommunicationIdentifierModel communicationIdentifierModel = new CommunicationIdentifierModel().setCommunicationUser(new CommunicationUserIdentifierModel().setId(second_user_id));
threadClient.removeChatParticipant(threadId, communicationIdentifierModel, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, okhttp3.Response response) {
        // Take further action.
        Log.i(TAG, "remove a user successfully");
    }

    @Override
    public void onFailure(Throwable throwable, okhttp3.Response response) {
        // Handle error.
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-typing-notification"></a>Een type melding verzenden

Vervang de opmerking `<SEND A TYPING NOTIFICATION>` door de volgende code:

```java
threadClient.sendTypingNotification(threadId, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a typing notification successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="send-a-read-receipt"></a>Een lees bevestiging verzenden

Zorg ervoor dat u vervangt `<chat_message_id>` door een geldige chat bericht-id. er wordt nu een lees bevestiging voor dit bericht verzonden.

Vervang de opmerking `<SEND A READ RECEIPT>` door de volgende code:

```java
SendReadReceiptRequest readReceipt = new SendReadReceiptRequest()
    .setChatMessageId(chatMessageId);
threadClient.sendChatReadReceipt(threadId, readReceipt, new Callback<Void>() {
    @Override
    public void onSuccess(Void result, Response response) {
        Log.i(TAG, "send a read receipt successfully");
    }

    @Override
    public void onFailure(Throwable throwable, Response response) {
        Log.e(TAG, throwable.getMessage());
    }
});
```

## <a name="list-read-receipts"></a>Lees bevestigingen weer geven

Vervang de opmerking `<READ RECEIPTS>` door de volgende code:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
threadClient.listChatReadReceiptsPages(threadId,
    maxPageSize,
    skip,
    new Callback<AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>>>() {
        @Override
        public void onSuccess(AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection,
                              Response response) {
            // pageCollection enables enumerating list of chat participants.
            pageCollection.getFirstPage(new Callback<Page<ChatMessageReadReceipt>>() {
                @Override
                public void onSuccess(Page<ChatMessageReadReceipt> firstPage, Response response) {
                    for (ChatMessageReadReceipt receipt : firstPage.getItems()) {
                        Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                    }
                    listChatReadReceiptsNext(firstPage.getNextPageId(), pageCollection);
                }

                @Override
                public void onFailure(Throwable throwable, Response response) {
                    Log.e(TAG, throwable.getMessage());
                }
            });
        }

        @Override
        public void onFailure(Throwable throwable, Response response) {
            Log.e(TAG, throwable.getMessage());
        }
});
```

Plaats de volgende Help-methode in de-klasse:
```java
void listChatReadReceiptsNext(String nextLink, AsyncPagedDataCollection<ChatMessageReadReceipt, Page<ChatMessageReadReceipt>> pageCollection) {
    if (nextLink != null) {
        pageCollection.getPage(nextLink, new Callback<Page<ChatMessageReadReceipt>>() {
            @Override
            public void onSuccess(Page<ChatMessageReadReceipt> nextPage, Response response) {
                for (ChatMessageReadReceipt receipt : nextPage.getItems()) {
                    Log.i(TAG, "receipt: " + receipt.getChatMessageId());
                }
                if (nextPage.getPageId() != null) {
                    listChatReadReceiptsNext(nextPage.getPageId(), pageCollection);
                }
            }

            @Override
            public void onFailure(Throwable throwable, Response response) {
                Log.e(TAG, throwable.getMessage());
            }
        });
    }
}

```


## <a name="run-the-code"></a>De code uitvoeren

In Android Studio, klikt u op de knop uitvoeren om het project te bouwen en uit te voeren. In de-console kunt u de uitvoer van de code en de logboek uitvoer van de ChatClient bekijken.
