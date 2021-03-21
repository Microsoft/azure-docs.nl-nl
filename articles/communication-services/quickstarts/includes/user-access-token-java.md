---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 6b75548d6fce7539c2eeb71523a5a045b0b6607b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495300"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](/java/azure/jdk/) versie 8 of hoger.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../create-communication-resource.md).

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-java-application"></a>Een nieuwe Java-toepassing maken

Open uw terminal-of opdrachtvenster. Navigeer naar de map waarin u uw Java-toepassing wilt maken. Voer de onderstaande opdracht uit om het Java-project te genereren op basis van de maven-archetype-snelstart-sjabloon.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

U ziet dat de taak ' genereren ' een map heeft gemaakt met dezelfde naam als de `artifactId`. In deze map bevat de map src/main/Java de broncode van het project, de `src/test/java directory` bevat de testbron en het bestand `pom.xml` het projectobjectmodel van het project of POM.

### <a name="install-the-package"></a>Het pakket installeren

Open het bestand **pom.xml** in uw teksteditor. Voeg het volgende afhankelijkheidselement toe aan de groep met afhankelijkheden.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Ga naar de map */src/main/java/com/communication/quickstart*
1. Open het bestand *App.java* in uw editor
1. De `System.out.println("Hello world!");`-instructie vervangen
1. Voeg `import`-instructies toe

Gebruik de volgende code om te beginnen:

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;
import com.azure.core.http.*;
import com.azure.core.http.netty.*;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>De client verifiëren

Instanteer een `CommunicationIdentityClient` met de toegangssleutel en het eindpunt van uw resource. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../create-communication-resource.md#store-your-connection-string).

Voeg de volgende code aan de `main` methode toe:

```java
// Your can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
// -> Add "import com.azure.core.http.netty.*;"
// -> Add azure-core-http-netty dependency to file pom.xml

HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(new AzureKeyCredential(accessKey))
        .httpClient(httpClient)
        .buildClient();
```

U kunt de client initialiseren met een aangepaste HTTP-client. Hiermee implementeert u de `com.azure.core.http.HttpClient`-interface. De bovenstaande code toont het gebruik van de [Azure Core Netty HTTP-client](/java/api/overview/azure/core-http-netty-readme) die wordt verschaft door `azure-core`.

U kunt ook de volledige connection string opgeven met behulp `connectionString()` van de functie in plaats van het eind punt en de toegangs sleutel op te geven.
```java
// Your can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="create-an-identity"></a>Een identiteit maken

Azure Communication Services onderhoudt een lichte identiteitsmap. Gebruik de methode `createUser` om een nieuwe vermelding in de map te maken met een unieke `Id`. Sla de ontvangen identiteit op met een toewijzing aan gebruikers van uw toepassing. U kunt dit bijvoorbeeld doen door ze op te slaan in de database van uw toepassingsserver. De identiteit is later vereist voor het uitgeven van toegangstokens.

```java
CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + user.getId());
```

## <a name="issue-access-tokens"></a>Toegangstokens uitgeven

Gebruik de methode `getToken` om een toegangstoken voor de al bestaande Communication Services-identiteit uit te geven. Met de parameter `scopes` wordt een set primitieven gedefinieerd, waarmee dit toegangstoken wordt geautoriseerd. Raadpleeg de [lijst met ondersteunde acties](../../concepts/authentication.md). Er kan een nieuw exemplaar van de parameter `user` worden samengesteld op basis van de tekenreeksweergave van de Azure Communication Service-identiteit.

```java
// Issue an access token with the "voip" scope for a user identity
List<CommunicationTokenScope> scopes = new ArrayList<>(Arrays.asList(CommunicationTokenScope.VOIP));
AccessToken accessToken = communicationIdentityClient.getToken(user, scopes);
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'voip' scope that expires at: " + expiresAt + ": " + token);
```

## <a name="create-an-identity-and-issue-token-in-one-call"></a>Een identiteits-en uitgifte token in één aanroep maken

U kunt ook de methode ' createUserAndToken ' gebruiken om een nieuw item in de Directory te maken met een unieke naam en om een `Id` toegangs token uit te geven.

```java
List<CommunicationTokenScope> scopes = Arrays.asList(CommunicationTokenScope.CHAT);
CommunicationUserIdentifierAndToken result = communicationIdentityClient.createUserAndToken(scopes);
CommunicationUserIdentifier user = result.getUser();
System.out.println("\nCreated a user identity with ID: " + user.getId());
AccessToken accessToken = result.getUserToken();
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'chat' scope that expires at: " + expiresAt + ": " + token);
```

Toegangstokens zijn kortdurende referenties die opnieuw moeten worden uitgegeven. Als u dit niet doet, kan dit leiden tot onderbrekingen van de gebruikerservaring van uw toepassing. De `expiresAt` eigenschap geeft de levens duur van het toegangs token aan.

## <a name="refresh-access-tokens"></a>Toegangstokens vernieuwen

Als u een toegangstoken wilt vernieuwen, gebruikt u het `CommunicationUserIdentifier`-object om het opnieuw uit te geven:

```java
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUserIdentifier identity = new CommunicationUserIdentifier(existingIdentity);
AccessToken response = communicationIdentityClient.getToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>Toegangstokens intrekken

In sommige gevallen kunt u toegangstokens expliciet intrekken. Wanneer de gebruiker van een toepassing bijvoorbeeld het wachtwoord wijzigt dat wordt gebruikt voor verificatie bij uw service. Met de methode `revokeTokens` worden alle actieve toegangstokens die zijn verleend aan de identiteit ongeldig gemaakt.

```java
communicationIdentityClient.revokeTokens(user);
System.out.println("\nSuccessfully revoked all access tokens for user identity with ID: " + user.getId());
```

## <a name="delete-an-identity"></a>Een identiteit verwijderen

Als u een identiteit verwijdert, worden alle actieve toegangstokens ingetrokken en wordt voorkomen dat er toegangstokens voor de identiteit worden uitgegeven. Ook wordt alle persistente inhoud verwijderd die aan de identiteit is gekoppeld.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nDeleted the user identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>De code uitvoeren

Navigeer naar de map die het bestand *pom.xml* bevat en compileer het project met behulp van de volgende `mvn`-opdracht.

```console
mvn compile
```

Bouw vervolgens het pakket.

```console
mvn package
```

Voer de volgende `mvn`-opdracht uit om de app uit te voeren.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
