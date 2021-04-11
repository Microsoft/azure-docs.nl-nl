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
ms.openlocfilehash: cdf1267d53abc2214521f584b6cfb4738b808204
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113233"
---
Ga aan de slag met Azure Communication Services met behulp van de SMS-SDK voor communicatie Services om SMS-berichten te verzenden.

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
    <version>1.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

```xml
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

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de SMS-SDK voor Azure Communication Services voor Java.

| Naam                                                             | Beschrijving                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Deze klasse maakt de SmsClient. U geeft een eindpunt, referentie en een HTTP-client op. |
| SmsClient                    | Deze klasse is vereist voor alle sms-functionaliteit. U kunt deze gebruiken om sms-berichten te verzenden.                |
| SmsSendOptions               | Deze klasse bevat opties voor het toevoegen van aangepaste tags en het configureren van leverings rapporten. Als deliveryReportEnabled is ingesteld op True, wordt een gebeurtenis verzonden wanneer de levering is geslaagd |        
| SmsSendResult                | Deze klasse bevat het resultaat van de SMS-service.                                          |

## <a name="authenticate-the-client"></a>De client verifiëren

Breng een `SmsClient` tot stand met uw verbindingsreeks. (Referentie is de `Key` van de Azure Portal. Meer informatie over het [beheren van de Connection String van uw resource](../../create-communication-resource.md#store-your-connection-string). Daarnaast kunt u de-client met elke aangepaste HTTP-client initialiseren. de interface wordt geïmplementeerd `com.azure.core.http.HttpClient` .

Voeg de volgende code aan de `main` methode toe:

```java
// You can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .buildClient();
```

U kunt ook de volledige verbindingsreeks opgeven met behulp van de functie connectionString() in plaats van het eindpunt en de toegangssleutel op te geven.
```java
// You can find your connection string from your resource in the Azure portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
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

Vervang door `<from-phone-number>` een SMS-telefoon nummer dat is gekoppeld aan uw communicatie Services-resource en `<to-phone-number>` een telefoon nummer waarnaar u een bericht wilt verzenden.

> [!WARNING]
> Telefoonnummers moeten worden opgegeven in de internationale standaardindeling E.164. (bijvoorbeeld: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Een SMS-bericht van 1: N verzenden met opties
Als u een SMS-bericht naar een lijst met ontvangers wilt verzenden, roept u de- `send` methode aan met een lijst met telefoon nummers van de ontvanger. U kunt ook aanvullende para meters door geven om op te geven of het leverings rapport moet worden ingeschakeld en aangepaste tags moet worden ingesteld.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.sendWithResponse(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE).getValue();

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

Vervang door `<from-phone-number>` een SMS-telefoon nummer dat is gekoppeld aan uw communicatie Services-bron en `<to-phone-number-1>` en met een `<to-phone-number-2>` telefoon nummer (s) waarnaar u een bericht wilt verzenden.

> [!WARNING]
> Telefoonnummers moeten worden opgegeven in de internationale standaardindeling E.164. (bijvoorbeeld: + 14255550123).

De `setDeliveryReportEnabled` methode wordt gebruikt voor het configureren van leverings rapportage. Dit is handig voor scenario's waarin u gebeurtenissen wilt verzenden wanneer sms-berichten worden bezorgd. Raadpleeg de quickstart [Sms-gebeurtenissen verwerken](../handle-sms-events.md) voor het configureren van leveringsrapporten voor uw sms-berichten.

De `setTag` methode wordt gebruikt om een label toe te passen op het leverings rapport.

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
