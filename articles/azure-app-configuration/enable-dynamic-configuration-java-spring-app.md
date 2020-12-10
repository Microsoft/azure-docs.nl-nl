---
title: Dynamische configuratie in een Spring Boot-app gebruiken
titleSuffix: Azure App Configuration
description: Meer informatie over het dynamisch bijwerken van configuratiegegevens voor Spring Boot-apps
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 08/06/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: c32e928bd4a83b4884c99e3ec3a9c647f5433e87
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929154"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Zelfstudie: Dynamische configuratie in een Java Spring-app gebruiken

De App Configuration Spring Boot-clientbibliotheek biedt ondersteuning voor het bijwerken van een set configuratie-instellingen op aanvraag, zonder dat een toepassing opnieuw hoeft te worden opgestart. De clientbibliotheek slaat elke instelling in de cache op om te veel aanroepen naar het configuratiearchief te voorkomen. De waarde wordt niet door de vernieuwingsbewerking bijgewerkt, zelfs niet wanneer de waarde in de configuratieopslag is gewijzigd. De standaardvervaltijd voor elke aanvraag is dertig seconden. Deze kan zo nodig worden overschreven.

U kunt op aanvraag op bijgewerkte instellingen controleren door methode `refreshConfigurations()` van `AppConfigurationRefresh` aan te roepen.

U kunt ook pakket `spring-cloud-azure-appconfiguration-config-web` gebruiken, dat afhankelijk is van `spring-web` voor het afhandelen van automatische vernieuwing.

## <a name="use-automated-refresh"></a>Automatisch vernieuwen gebruiken

Als u automatisch vernieuwen wilt gebruiken, begint u met een Spring Boot-app die gebruikmaakt van App Configuration, zoals de app die u hebt gemaakt door de [Spring Boot-quickstart voor App Configuration](quickstart-java-spring-app.md) te volgen.

Open vervolgens bestand *pom.xml* in een teksteditor en voeg een `<dependency>` voor `spring-cloud-azure-appconfiguration-config-web` toe.

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>De app lokaal uitvoeren en testen

1. Maak uw Spring Boot-app met Maven en voer deze uit.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Open een browservenster en ga naar de URL: `http://localhost:8080`.  U ziet het bericht dat bij uw sleutel hoort. 

    U kunt ook *curl* gebruiken om uw toepassing te testen, bijvoorbeeld: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Als u de dynamische configuratie wilt testen, opent u de Azure App Configuration-portal die bij uw toepassing hoort. Selecteer **Configuratie Explorer** en werk de waarde van uw weergegeven sleutel bij, bijvoorbeeld:
    | Sleutel | Waarde |
    |---|---|
    | application/config.message | Hallo - bijgewerkt |

1. Vernieuw de browserpagina om het nieuwe bericht te zien.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u uw Spring Boot-app ingeschakeld voor het dynamisch vernieuwen van configuratie-instellingen vanuit App Configuration. Als u wilt weten hoe u een door Azure beheerde identiteit kunt gebruiken om de toegang tot App Configuration te stroomlijnen, gaat u verder met de volgende zelfstudie.

> [!div class="nextstepaction"]
> [Integratie van beheerde identiteit](./howto-integrate-azure-managed-service-identity.md)
