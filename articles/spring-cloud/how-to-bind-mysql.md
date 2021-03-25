---
title: Een Azure Database for MySQL-exemplaar koppelen aan uw Azure Spring Cloud-toepassing
description: Meer informatie over het verbinden van een Azure Database for MySQL-exemplaar met uw Azure Spring Cloud-toepassing
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cc1c186f9acb6d49314f5d581a4b51ffe49d6627
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878051"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Een Azure Database for MySQL-exemplaar verbinden met uw Azure Spring Cloud-toepassing 

**Dit artikel is van toepassing op:** ✔️ Java

Met Azure Spring Cloud kunt u bepaalde Azure-services automatisch met uw toepassingen verbinden, in plaats van de Spring Boot-toepassing handmatig te configureren. In dit artikel leest u hoe u uw toepassing aan uw Azure Database for MySQL-exemplaar koppelt.

## <a name="prerequisites"></a>Vereisten

* Een geïmplementeerd Azure Spring Cloud-exemplaar
* Een Azure Database for MySQL-account
* Azure CLI

Als u geen geïmplementeerd exemplaar van Azure Spring Cloud hebt, volgt u de instructies in [Quickstart: Een Azure Spring Cloud-toepassing starten met behulp van de Azure-portal](spring-cloud-quickstart.md) om uw eerste Spring Cloud-toepassing te implementeren.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Uw toepassing verbinden met uw Azure Database for MySQL-exemplaar

1. Noteer de gebruikersnaam en het wachtwoord van de beheerder van uw Azure Database for MySQL-account. 

1. Maak verbinding met de server, maak een database met de naam **testdb** vanaf een MySQL-client, en maak vervolgens een nieuw niet-beheerdersaccount.

1. Voeg de volgende afhankelijkheid toe aan het bestand *pom.xml* van uw project:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. Verwijder in het bestand *application.properties* alle `spring.datasource.*`-eigenschappen.

1. Werk de huidige implementatie bij door `az spring-cloud app update` uit te voeren of maak een nieuwe implementatie voor deze wijziging door `az spring-cloud app deployment create` uit te voeren.  Met deze opdrachten kunt u de toepassing met de nieuwe afhankelijkheid bijwerken of maken.

1. Zoek in de Azure-portal op de pagina van de **Azure Spring Cloud**-service naar het **Toepassingsdashboard** en selecteer vervolgens de toepassing die u met uw Azure Database for MySQL exemplaar wilt verbinden.  Dit is dezelfde toepassing als die u in de vorige stap hebt bijgewerkt of geïmplementeerd. 

1. Selecteer **Servicebinding** en selecteer vervolgens de knop **Servicebinding maken**. 

1. Vul het formulier in en selecteer **Azure MySQL** als het **bindingstype**, met dezelfde databasenaam die u eerder hebt gebruikt en dezelfde gebruikersnaam en hetzelfde wachtwoord als die u in de eerste stap hebt genoteerd.

1. Start de toepassing opnieuw op. Deze binding zou nu moeten werken.

1. Als u wilt controleren of de servicebinding juist is, selecteert u de naam van de binding en verifieert u de details ervan. Het veld `property` moet er als volgt uitzien:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u meer geleerd over het verbinden van een Azure Spring Cloud-toepassing met een Azure Database for MySQL-exemplaar. Zie [Een Azure Cosmos DB-database verbinden met een Azure Spring Cloud-toepassing](spring-cloud-howto-bind-cosmos.md) voor meer informatie over het verbinden van services met een toepassing.
