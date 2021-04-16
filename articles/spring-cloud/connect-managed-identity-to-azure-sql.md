---
title: Beheerde identiteit gebruiken om verbinding te Azure SQL met Azure Spring Cloud app
description: Stel een beheerde identiteit in om verbinding te Azure SQL met een Azure Spring Cloud app.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: ed729dde51316b9a67f396e3f7de3d7d9f6d4568
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378785"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Een beheerde identiteit gebruiken om verbinding te Azure SQL Database met een Azure Spring Cloud app

**Dit artikel is van toepassing op:** ✔️ Java

In dit artikel wordt beschreven hoe u een beheerde identiteit maakt voor een Azure Spring Cloud-app en deze gebruikt om toegang te krijgen tot Azure SQL Database.

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) is de intelligente, schaalbare relationele databaseservice die voor de cloud is gebouwd. Het is altijd up-to-date, met ai-functies en geautomatiseerde functies die de prestaties en duurzaamheid optimaliseren. Serverloze reken- en Hyperscale-opslagopties schalen resources automatisch op aanvraag, zodat u zich kunt richten op het bouwen van nieuwe toepassingen zonder dat u zich zorgen hoeft te maken over opslaggrootte of resourcebeheer.

## <a name="prerequisites"></a>Vereisten
In dit voorbeeld worden de volgende resources gebruikt.
* Volg de [Spring Data JPA-zelfstudie](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) om een app in Azure SQL Database en deze lokaal te laten werken met een Java-app
* Volg de [Azure Spring Cloud door het systeem toegewezen beheerde](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) identiteit om een app in te Azure Spring Cloud met MI ingeschakeld

## <a name="grant-permission-to-the-managed-identity"></a>Machtiging verlenen aan de beheerde identiteit
Maak verbinding met uw SQL-server en voer de volgende SQL-query uit:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Dit <MIName> volgt de regel: , bijvoorbeeld `<service instance name>/apps/<app name>` myspringcloud/apps/sqldemo. U kunt ook een query uitvoeren op de MIName met Azure CLI:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Uw Java-app configureren voor het gebruik van beheerde identiteit
Open het `src/main/resources/application.properties` bestand en voeg toe aan het einde van de volgende `Authentication=ActiveDirectoryMSI;` regel. Zorg ervoor dat u de juiste waarde gebruikt voor $AZ_DATABASE_NAME-variabele.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>De app bouwen en implementeren in Azure Spring Cloud
Bouw de app opnieuw en implementeer deze in Azure Spring Cloud app die is ingericht in het tweede punt onder Vereisten. U hebt nu een Spring Boot-toepassing, geverifieerd door een beheerde identiteit, die gebruikmaakt van JPA voor het opslaan en ophalen van gegevens uit een Azure SQL Database in Azure Spring Cloud.

## <a name="next-steps"></a>Volgende stappen

* [Toegang tot de Storage-blob met een beheerde identiteit in een Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Door het systeem toegewezen beheerde identiteit inschakelen voor de Azure Spring Cloud-toepassing](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Meer informatie over beheerde identiteiten voor Azure-resources](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Azure Spring Cloud verifiëren met Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md)