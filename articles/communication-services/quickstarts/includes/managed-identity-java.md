---
ms.openlocfilehash: 1c4aab27eb72afa473f95f1c0956b5e3d66c3940
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073481"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) versie 8 of hoger.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../create-communication-resource.md).

## <a name="add-managed-identity-to-your-communication-services-solution-java"></a>Beheerde identiteit toevoegen aan uw oplossing voor communicatie Services (Java)

### <a name="install-the-sdk-packages"></a>De SDK-pakketten installeren
Voeg in het pom.xml-bestand de volgende afhankelijkheids elementen toe aan de groep afhankelijkheden.

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

Voeg de volgende- `import` instructies toe aan uw code voor het gebruik van de Azure-identiteits-en Azure Communication-sdk's.

```java
import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.io.IOException;
import java.util.*;
```

In de onderstaande voor beelden wordt gebruikgemaakt van de [DefaultAzureCredential](/java/api/com.azure.identity.defaultazurecredential). Deze referentie is geschikt voor productie-en ontwikkelings omgevingen.

Zie [toegang verlenen met beheerde identiteit](../managed-identity-from-cli.md) voor een eenvoudige manier om met beheerde identiteits verificatie in te springen

Voor een uitgebreidere bespreking van de werking van het DefaultAzureCredential-object en hoe u dit kunt gebruiken op manieren die niet zijn opgegeven in deze Snelstartgids, raadpleegt u [Azure Identity client library voor Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Een identiteit maken en een token uitgeven met een beheerde identiteit

In het volgende code voorbeeld ziet u hoe u een service-client object met beheerde identiteit maakt.
Vervolgens gebruikt u de-client om een token uit te geven voor een nieuwe gebruiker:

```java
     public AccessToken createIdentityAndGetTokenAsync() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          AccessToken userToken = communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
          return userToken;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>Een SMS-bericht verzenden met een beheerde identiteit

In het volgende code voorbeeld ziet u hoe u een service-client object met beheerde identiteit maakt en vervolgens de-client gebruikt om een SMS-bericht te verzenden:

```java
     public SendSmsResponse sendSms() {
          // You can find your endpoint and access key from your resource in the Azure portal
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          TokenCredential credential = new DefaultAzureCredentialBuilder().build();

          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(credential)
               .buildClient();

          // Send the message and check the response for a message id
          SmsSendResult response = smsClient.send(
               "<from-phone-number>",
               "<to-phone-number>",
               "your message"
          );
          return response;
    }
```

