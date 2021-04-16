---
ms.openlocfilehash: b51f52e24ca843abd94a8511e86b3193a797edd5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107511986"
---
## <a name="additional-prerequisites-for-java"></a>Aanvullende vereisten voor Java
Voor Java hebt u ook het volgende nodig:
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) versie 8 of hoger.
- [Apache Maven](https://maven.apache.org/download.cgi).

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
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-sdk-packages"></a>De SDK-pakketten gebruiken

Voeg de volgende instructies `import` toe aan uw code om de Azure Identity- en Azure Communication-SDK's te gebruiken.

```java
import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;
```

## <a name="create-a-defaultazurecredential"></a>Een DefaultAzureCredential maken

We gebruiken de [DefaultAzureCredential](/java/api/com.azure.identity.defaultazurecredential) voor deze quickstart. Deze referentie is geschikt voor productie- en ontwikkelomgevingen. Omdat dit nodig is voor elke bewerking, maken we deze in de `App.java` klasse . Voeg het volgende toe aan de bovenkant van de `App.java` klasse .

```java
private TokenCredential credential = new DefaultAzureCredentialBuilder().build();
```

## <a name="issue-a-token-with-managed-identities"></a>Een token uitgeven met beheerde identiteiten

Nu gaan we code toevoegen die gebruikmaakt van de gemaakte referentie om een VoIP-toegangs token uit te geven. We noemen deze code later;

```java
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }
```

## <a name="send-an-sms-with-managed-identities"></a>Een sms verzenden met beheerde identiteiten

Als een ander voorbeeld van het gebruik van beheerde identiteiten, voegen we deze code toe die dezelfde referentie gebruikt om een sms te verzenden:

```java
     public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
     }
```
## <a name="write-the-main-method"></a>De Main-methode schrijven

Als u al een Main-methode hebt, gaan we code toevoegen die onze eerder gemaakte code aanroept om het gebruik van beheerde `App.java` identiteiten te demonstreren:
```java
    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Managed Identities");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Managed Identities");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
```

Uw `App.java` uiteindelijke ziet er als volgende uit:

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;

public class App 
{

    private TokenCredential credential = new DefaultAzureCredentialBuilder().build();

    public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(this.credential)
               .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
    }
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }

    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Managed Identities");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Managed Identities");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
}
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

De uiteindelijke uitvoer moet er als volgt uit zien:
```
Retrieving new Access Token, using Managed Identities
Retrieved Access Token: ey..A
Sending SMS using Managed Identities
Sms id: Outgoing_202104...33f8ae1f_noam
Send Result Successful: true
```

