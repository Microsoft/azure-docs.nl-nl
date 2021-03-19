---
title: Azure Cache voor Redis verbinden met uw Azure Spring Cloud-toepassing
description: Meer informatie over het verbinden van Azure Cache voor Redis met uw Azure Spring Cloud-toepassing
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: ef77ba6e34f2a699c8c4f06fde8cb602ae98c728
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90885675"
---
# <a name="bind-azure-cache-for-redis-to-your-azure-spring-cloud-application"></a>Azure Cache voor Redis verbinden met uw Azure Spring Cloud-toepassing 

**Dit artikel is van toepassing op:** ✔️ Java

In plaats van uw Spring Boot-toepassingen handmatig te configureren, kunt u de geselecteerde Azure-services automatisch met uw toepassingen verbinden met behulp van Azure Spring Cloud. In dit artikel wordt uitgelegd hoe u uw toepassing verbindt met Azure Cache voor Redis.

## <a name="prerequisites"></a>Vereisten

* Een geïmplementeerd Azure Spring Cloud-exemplaar
* Een exemplaar van de Azure Cache voor Redis-service
* De Azure Spring Cloud-extensie voor de Azure CLI

Als u geen geïmplementeerd Azure Spring Cloud-exemplaar hebt, volgt u de stappen in de [quickstart voor het implementeren van een Azure Spring Cloud-toepassing](spring-cloud-quickstart.md).

## <a name="bind-azure-cache-for-redis"></a>Azure Cache voor Redis verbinden

1. Voeg de volgende afhankelijkheid toe aan het bestand pom.xml van uw project:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
    </dependency>
    ```
1. Verwijder alle `spring.redis.*`-eigenschappen uit het bestand `application.properties`

1. Werk de huidige implementatie bij met `az spring-cloud app update` of maak een nieuwe implementatie met `az spring-cloud app deployment create`.

1. Ga naar de pagina van Azure Spring Cloud in de Azure-portal. Ga naar **Toepassingsdashboard** en selecteer de toepassing die u met Azure Cache voor Redis wilt verbinden. Dit is dezelfde toepassing als die u in de vorige stap hebt bijgewerkt of geïmplementeerd.

1. Selecteer **Servicebinding** en selecteer de knop **Servicebinding maken**. Vul het formulier in en selecteer bij **Bindingstype** de optie **Azure Cache voor Redis**, en selecteer uw Azure Cache voor Redis-server en de sleuteloptie **Primair**.

1. De app opnieuw starten. De binding moet nu werken.

1. Als u wilt controleren of de servicebinding juist is, selecteert u de naam van de binding en verifieert u de details ervan. Het veld `property` moet er als volgt uitzien:
    ```
    spring.redis.host=some-redis.redis.cache.windows.net
    spring.redis.port=6380
    spring.redis.password=abc******
    spring.redis.ssl=true
    ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u meer geleerd over het verbinden van uw Azure Spring Cloud-toepassing met Azure Cache voor Redis. Zie [Verbinden met een Azure Database for MySQL-exemplaar](spring-cloud-tutorial-bind-mysql.md) voor meer informatie over bindingsservices voor uw toepassing.
