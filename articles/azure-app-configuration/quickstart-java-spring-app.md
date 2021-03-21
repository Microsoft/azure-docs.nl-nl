---
title: 'Quickstart: leren werken met Azure App Configuration'
description: In deze quickstart maakt u een Java Spring-app met Azure App Configuration om opslag en beheer van toepassingsinstellingen gescheiden van uw code te centraliseren.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: 40c8fae292f465b3ecc8778a21e4903bdf423a3a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932027"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Quickstart: Een Java Spring-app maken met Azure App Configuration

In deze quickstart neemt u Azure App Configuration op in een Java Spring-console-app om opslag en beheer van toepassingsinstellingen gescheiden van uw code te centraliseren.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- Een ondersteunde [Java Development Kit (JDK)](/java/azure/jdk) met versie 8.
- [Apache Maven](https://maven.apache.org/download.cgi) versie 3.0 of hoger.

## <a name="create-an-app-configuration-store"></a>Een App Configuration-archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecteer **Configuratieverkenner** >  **+ Maken** > **Sleutel-waarde** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | /application/config.message | Hello |

    Laat **Label** en **Inhoudstype** nog even leeg.

8. Selecteer **Toepassen**.

## <a name="create-a-spring-boot-app"></a>Een Spring Boot-app maken

Gebruik de [Spring Initializr](https://start.spring.io/) om een nieuw Spring Boot-project te maken.

1. Blader naar <https://start.spring.io/>.

1. Geef de volgende opties op:

   - Genereer een **Maven**-project met **Java**.
   - Geef een **Spring Boot**-versie op van 2.0 of hoger.
   - Geef de namen voor **Groep** en **Artefact** voor uw toepassing op.
   - Voeg de **Spring Web**-afhankelijkheid toe.

1. Nadat u de vorige opties hebt opgegeven, selecteert u **Project genereren**. Wanneer u hierom wordt gevraagd, downloadt u het project naar een pad op uw lokale computer.

## <a name="connect-to-an-app-configuration-store"></a>Verbinding maken met een App Configuration-archief

1. Nadat u de bestanden op uw lokale systeem hebt uitgepakt, is uw eenvoudige Spring Boot-toepassing gereed om te bewerken. Zoek het bestand *pom.xml* in de hoofdmap van uw app.

1. Open het bestand *pom.xml* in een teksteditor en voeg de starter Spring Cloud Azure Config toe aan de lijst van `<dependencies>`:

    **Spring Cloud 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.5</version>
    </dependency>
    ```

    **Spring Cloud 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.7</version>
    </dependency>
    ```

1. Maak een nieuw Java-bestand met de naam *MessageProperties.java* in de pakketmap van uw app. Voeg de volgende regels toe:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Maak een nieuw Java-bestand met de naam *HelloController.java* in de pakketmap van uw app. Voeg de volgende regels toe:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. Open het Java-bestand van de hoofdtoepassing en voeg `@EnableConfigurationProperties` toe om deze functie in te schakelen.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Maak een nieuw bestand met de naam `bootstrap.properties` onder de resourcesmap van uw app en voeg de volgende regels toe aan het bestand. Vervang de voorbeeldwaarden door de juiste eigenschappen voor uw app-configuratiearchief.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Stel een omgevingsvariabele in met de naam **APP_CONFIGURATION_CONNECTION_STRING** en stel deze in op de toegangssleutel van het App Configuration-archief. Voer op de opdrachtregel de volgende opdracht uit en start de opdrachtprompt opnieuw op om de wijziging door te voeren:

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Als u Windows PowerShell gebruikt, voert u de volgende opdracht uit:

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Als u macOS of Linux gebruikt, voert u de volgende opdracht uit:

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>De app lokaal compileren en uitvoeren

1. Maak de Spring Boot-toepassing met Maven en voer deze uit; bijvoorbeeld:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. Nadat uw toepassing wordt uitgevoerd, kunt u *curl* gebruiken om uw toepassing te testen; bijvoorbeeld:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Er wordt een bericht weergegeven dat u zich in het app-configuratiearchief bevindt.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een nieuw app-configuratiearchief gemaakt en deze gebruikt met een Java Spring-app. Zie [Spring op Azure](/java/azure/spring-framework/) voor meer informatie. Ga door naar de volgende zelfstudie voor meer informatie over het configureren van uw Java Spring-app om configuratie-instellingen dynamisch te vernieuwen.

> [!div class="nextstepaction"]
> [Dynamische configuratie inschakelen](./enable-dynamic-configuration-java-spring-app.md)