---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: 2739079b67d80f3e4a9f367aaa58f6dcbbb650ca
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622194"
---
Ga aan de slag met Azure Communication Services door de sms-clientbibliotheek in Java van Communications Services te gebruiken om sms-berichten te verzenden.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

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
    <version>1.0.0-beta.4</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Voeg de afhankelijkheid `azure-core-http-netty` toe aan uw bestand **pom.xml**.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.8.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

Open **/src/main/java/com/communication/quickstart/App.java** in een teksteditor, voeg importregels toe en verwijder de instructie `System.out.println("Hello world!");`:

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
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
| SmsSendResult                | Deze klasse bevat het resultaat van de SMS-service.                                          |
| SmsSendOptions               | Deze klasse bevat opties voor het toevoegen van aangepaste tags en het configureren van leverings rapporten. Als deliveryReportEnabled is ingesteld op True, wordt een gebeurtenis verzonden wanneer de levering is geslaagd|                           |

## <a name="authenticate-the-client"></a>De client verifiëren

Breng een `SmsClient` tot stand met uw verbindingsreeks. (Referentie is de `Key` van de Azure Portal. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../../create-communication-resource.md#store-your-connection-string).

Voeg de volgende code aan de `main` methode toe:

```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .httpClient(httpClient)
                .buildClient();
```

U kunt de client initialiseren met een aangepaste HTTP-client. Hiermee implementeert u de `com.azure.core.http.HttpClient`-interface. De bovenstaande code toont het gebruik van de [Azure Core Netty HTTP-client](/java/api/overview/azure/core-http-netty-readme) die wordt verschaft door `azure-core`.

U kunt ook de volledige verbindingsreeks opgeven met behulp van de functie connectionString() in plaats van het eindpunt en de toegangssleutel op te geven. 
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .httpClient(httpClient)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>Een SMS-bericht van 1:1 verzenden

Als u een SMS-bericht naar één ontvanger wilt verzenden, roept u de `send` methode aan vanuit de SmsClient met één ontvanger-telefoon nummer. U kunt ook aanvullende para meters door geven om op te geven of het leverings rapport moet worden ingeschakeld en aangepaste tags moet worden ingesteld.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```
## <a name="send-a-1n-sms-message-with-options"></a>Een SMS-bericht van 1: N verzenden met opties
Als u een SMS-bericht naar een lijst met ontvangers wilt verzenden, roept u de- `send` methode aan met een lijst met telefoon nummers van de ontvanger. U kunt ook aanvullende para meters door geven om op te geven of het leverings rapport moet worden ingeschakeld en aangepaste tags moet worden ingesteld.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.send(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE);

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

Vervang door `<from-phone-number>` een SMS-telefoon nummer dat is gekoppeld aan uw communicatie Services-bron en `<to-phone-number>` met het telefoon nummer of een lijst met telefoon nummers waarnaar u een bericht wilt verzenden.

## <a name="optional-parameters"></a>Optionele parameters

De parameter `deliveryReportEnabled` is een optionele parameter die u kunt gebruiken voor het configureren van leveringsrapporten. Dit is handig voor scenario's waarin u gebeurtenissen wilt verzenden wanneer sms-berichten worden bezorgd. Raadpleeg de quickstart [Sms-gebeurtenissen verwerken](../handle-sms-events.md) voor het configureren van leveringsrapporten voor uw sms-berichten.

De `tag` para meter is een optionele para meter die u kunt gebruiken om een label toe te passen op het leverings rapport.

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