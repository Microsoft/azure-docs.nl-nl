---
title: Verbinding maken met Azure Media Services v3-API - Java
description: In dit artikel wordt beschreven hoe u verbinding maakt met de Azure Media Services v3-API met Java.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: 769b4bc431040ee4d872fa60270196db96978ed2
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94748162"
---
# <a name="connect-to-media-services-v3-api---java"></a>Verbinding maken met de Media Services v3-API - Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel ziet u hoe u verbinding maakt met de Azure Media Services v3 Java-SDK met behulp van de aanmeldingsmethode met een service-principal.

In dit artikel wordt Visual Studio Code gebruikt om de voorbeeld-app te ontwikkelen.

## <a name="prerequisites"></a>Vereisten

- Volg [Java schrijven met Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) om het volgende te installeren:

   - JDK
   - Apache Maven
   - Java-uitbreidingspakket
- Vergeet niet de omgevingsvariabelen `JAVA_HOME` en `PATH` in te stellen.
- [Een Azure Media Services-account maken](./create-account-howto.md). Vergeet niet welke namen u gebruikt voor de resourcegroep en het Media Services-account.
- Volg de stappen in het onderwerp [Toegangs-API's](./access-api-howto.md). Leg de abonnements-id, de toepassings-id (client-id), en de verificatiesleutel (geheim) en de tenant-id vast die u in een latere stap nodig hebt.

Bekijk ook het volgende:

- [Java in Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Java Project Management in VS Code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Bekijk [naamconventies](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Een Maven-project maken

Open een opdrachtregelprogramma en `cd` in een map waarin u het project wilt maken.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Wanneer u de opdracht uitvoert, worden de `pom.xml`, `App.java` en andere bestanden gemaakt. 

## <a name="add-dependencies"></a>Afhankelijkheden toevoegen

1. Open de map met uw project in Visual Studio Code
1. Zoek en open de `pom.xml`
1. Voeg de benodigde afhankelijkheden toe.

   Bekijk `pom.xml` in het voorbeeld van [Videocodering](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESCustomPreset/pom.xml).

## <a name="connect-to-the-java-client"></a>Maak verbinding met de Java-client

1. Open het `App.java`-bestand onder `src\main\java\com\azure\ams` en controleer of uw pakket bovenaan staat:

    ```java
    package com.azure.ams;
    ```
1. Voeg onder de pakketinstructie deze importinstructies toe:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Voor het maken van de Active Directory-referenties die u nodig hebt om aanvragen te maken, voegt u de volgende code toe aan de hoofdmethode van de App-klasse en stelt u de waarden in die u hebt opgehaald uit [Toegangs-API's](./access-api-howto.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Voer de app uit.

## <a name="see-also"></a>Zie ook

- [Media Services-concepts](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Naslaginformatie over Java](/java/api/overview/azure/mediaservices/management)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Volgende stappen

U kunt nu `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` insluiten en entiteiten gaan bewerken.

Zie de opslagplaats [Voorbeelden van Java-SDK's](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) voor meer codevoorbeelden.
