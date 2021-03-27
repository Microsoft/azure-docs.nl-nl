---
title: Dynamische configuratie in een Spring Boot-app gebruiken
titleSuffix: Azure App Configuration
description: Meer informatie over het dynamisch bijwerken van configuratiegegevens voor Spring Boot-apps
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 590f221b0a4980d462267dd8c3a73ca7d02583fd
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625514"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Zelfstudie: Dynamische configuratie in een Java Spring-app gebruiken

App-configuratie heeft twee bibliotheken voor een bron. `spring-cloud-azure-appconfiguration-config` nood opstartdiskette vereist en maakt een afhankelijkheid van `spring-cloud-context` . `spring-cloud-azure-appconfiguration-config-web` vereist een lente-web samen met een Spring boot. Beide bibliotheken ondersteunen hand matige activering om te controleren op vernieuwde configuratie waarden. `spring-cloud-azure-appconfiguration-config-web` voegt ook ondersteuning toe voor automatische controle van de configuratie vernieuwing.

Met vernieuwen kunt u de configuratie waarden vernieuwen zonder dat u de toepassing opnieuw hoeft op te starten, maar worden alle bonen in de opnieuw `@RefreshScope` gemaakt. De client bibliotheek slaat een hash-id van de momenteel geladen configuraties op om te veel aanroepen naar het configuratie archief te voor komen. De waarde wordt niet door de vernieuwingsbewerking bijgewerkt, zelfs niet wanneer de waarde in de configuratieopslag is gewijzigd. De standaardvervaltijd voor elke aanvraag is dertig seconden. Deze kan zo nodig worden overschreven.

`spring-cloud-azure-appconfiguration-config-web`automatische vernieuwing wordt geactiveerd op basis van activiteit, met name lente van het web `ServletRequestHandledEvent` . Als er `ServletRequestHandledEvent` geen wordt geactiveerd, `spring-cloud-azure-appconfiguration-config-web` wordt door automatisch vernieuwen geen vernieuwing geactiveerd, zelfs niet als de verval tijd van de cache is verlopen.

## <a name="use-manual-refresh"></a>Hand matig vernieuwen gebruiken

In de app-configuratie wordt weer gegeven `AppConfigurationRefresh` Wat kan worden gebruikt om te controleren of de cache is verlopen en of een vernieuwing is verlopen.

```java
import com.microsoft.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh``refreshConfigurations()`retourneert een waarde `Future` die waar is als er een vernieuwings bewerking is geactiveerd en False als dat niet het geval is. ONWAAR betekent dat de verval tijd van de cache niet is verlopen, er geen wijziging is of een andere thread momenteel controleert op vernieuwen.

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
