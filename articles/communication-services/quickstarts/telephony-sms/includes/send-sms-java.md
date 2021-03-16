---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: 313832c29eb3f3f175fb20a60ba712937fdede0a
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488299"
---
Ga aan de slag met Azure Communication Services door de sms-clientbibliotheek in Java van Communications Services te gebruiken om sms-berichten te verzenden.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](/java/azure/jdk/) versie 8 of hoger.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een telefoonnummer met sms-functionaliteit. [Een telefoonnummer aanvragen](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controle van vereisten

- Voer `mvn -v` in een terminal of opdrachtvenster uit om te controleren of maven is geïnstalleerd.
- Als u de telefoonnummers wilt weergeven die zijn gekoppeld aan uw Communication Services-resource, meldt u zich aan bij [Azure Portal](https://portal.azure.com/), zoekt u uw Communication Services-resource en opent u het tabblad **telefoonnummers** vanuit het navigatiedeelvenster aan de linkerkant.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-java-application"></a>Een nieuwe Java-toepassing maken

Open uw terminal- of opdrachtvenster en navigeer naar de map waarin u uw Java-toepassing wilt maken. Voer de onderstaande opdracht uit om het Java-project te genereren op basis van de maven-archetype-snelstart-sjabloon.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Het doel 'genereren' zal een map maken met dezelfde naam als de artifactId. De map **src/main/java** bevat de broncode van het project, de map **src/test/java** bevat de testbron en het bestand **pom.xml** is het Project Object Model van het project of POM.

### <a name="install-the-package"></a>Het pakket installeren

Open het bestand **pom.xml** in uw teksteditor. Voeg het volgende afhankelijkheidselement toe aan de groep met afhankelijkheden.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Voeg de afhankelijkheid `azure-core-http-netty` toe aan uw bestand **pom.xml**.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

Open **/src/main/java/com/communication/quickstart/App.java** in een teksteditor, voeg importregels toe en verwijder de instructie `System.out.println("Hello world!");`:

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services sms-clientbibliotheek voor Java.

| Naam                                                             | Beschrijving                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Deze klasse maakt de SmsClient. U geeft een eindpunt, referentie en een HTTP-client op. |
| SmsClient                    | Deze klasse is vereist voor alle sms-functionaliteit. U kunt deze gebruiken om sms-berichten te verzenden.                |
| SendSmsResponse               | Deze klasse bevat de reactie van de sms-service.                                          |
| PhoneNumber                   | Deze klasse bevat telefoonnummergegevens

## <a name="authenticate-the-client"></a>De client verifiëren

Breng een `SmsClient` tot stand met uw verbindingsreeks. Met de onderstaande code worden het eindpunt en de referentietekenreeksen voor de resource opgehaald uit de omgevingsvariabelen met de naam `COMMUNICATION_SERVICES_ENDPOINT_STRING` en `COMMUNICATION_SERVICES_CREDENTIAL_STRING` (referentie is de `Key` van de Azure Portal. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../../create-communication-resource.md#store-your-connection-string).

Voeg de volgende code aan de `main` methode toe:

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .accessKey(accessKey)
    .httpClient(httpClient)
    .buildClient();
```

U kunt de client initialiseren met een aangepaste HTTP-client. Hiermee implementeert u de `com.azure.core.http.HttpClient`-interface. De bovenstaande code toont het gebruik van de [Azure Core Netty HTTP-client](/java/api/overview/azure/core-http-netty-readme) die wordt verschaft door `azure-core`.

U kunt ook de volledige verbindingsreeks opgeven met behulp van de functie connectionString() in plaats van het eindpunt en de toegangssleutel op te geven. 
```java
// Your can find your connection string from your resource in the Azure Portal
String connectionString = "<connection_string>";
SmsClient client = new SmsClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="send-an-sms-message"></a>Een sms-bericht verzenden

Een sms-bericht verzenden door de sendMessage-methode aan te roepen. Voeg deze code toe aan het einde van de `main`-methode:

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

U moet `<leased-phone-number>` vervangen door een telefoonnummer met sms-functionaliteit dat is gekoppeld aan uw Communication Services-resources en `<to-phone-number>` met het telefoonnummer waarnaar u een bericht wilt verzenden.

De parameter `enableDeliveryReport` is een optionele parameter die u kunt gebruiken voor het configureren van leveringsrapporten. Dit is handig voor scenario's waarin u gebeurtenissen wilt verzenden wanneer sms-berichten worden bezorgd. Raadpleeg de quickstart [Sms-gebeurtenissen verwerken](../handle-sms-events.md) voor het configureren van leveringsrapporten voor uw sms-berichten.

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>De code uitvoeren

Navigeer naar de map die het bestand **pom.xml** bevat en compileer het project met de opdracht `mvn`.

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