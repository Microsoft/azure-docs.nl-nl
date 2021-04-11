---
title: Beheerde identiteit gebruiken om verbinding te maken tussen Azure SQL en Azure lente Cloud-app
description: Stel beheerde identiteit in om Azure SQL te verbinden met een Azure lente-Cloud-app.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 3f1cde0bf233c67d02b9b7266ce3b3c8a3696db8
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123380"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Een beheerde identiteit gebruiken om Azure SQL Database verbinding te maken met een Azure lente-Cloud-app

**Dit artikel is van toepassing op:** ✔️ Java

Dit artikel laat u zien hoe u een beheerde identiteit kunt maken voor een Azure lente-Cloud-app en deze kunt gebruiken om toegang te krijgen tot Azure SQL Database.

[Azure SQL database](https://azure.microsoft.com/services/sql-database/) is de intelligente, schaal bare relationele database service die is gebouwd voor de Cloud. Het is altijd up-to-date, met AI-ingeschakelde en geautomatiseerde functies waarmee de prestaties en duurzaamheid worden geoptimaliseerd. Met serverloze reken-en grootschalige-opslag opties worden resources op aanvraag automatisch geschaald, zodat u zich kunt richten op het bouwen van nieuwe toepassingen zonder dat u zich zorgen hoeft te maken over de opslag grootte of het beheer

## <a name="prerequisites"></a>Vereisten
In dit voor beeld worden de volgende resources gebruikt.
* Volg de [jpa-zelf studie voor de lente gegevens](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server) om een Azure SQL database in te richten en gebruik te maken van een Java-app lokaal
* Volg de [zelf studie beheerde identiteit van het Azure-systeem](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity) dat door de Cloud wordt toegewezen voor het inrichten van een Azure lente-Cloud toepassing met mi ingeschakeld

## <a name="grant-permission-to-the-managed-identity"></a>Machtigingen verlenen aan de beheerde identiteit
Maak verbinding met uw SQL Server en voer de volgende SQL-query uit:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Dit <MIName> volgt de regel: `<service instance name>/apps/<app name>` , bijvoorbeeld myspringcloud/apps/sqldemo. U kunt ook een query uitvoeren op de MIName met Azure CLI:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Uw Java-app configureren voor het gebruik van beheerde identiteit
Open het `src/main/resources/application.properties` bestand en voeg `Authentication=ActiveDirectoryMSI;` aan het einde van de volgende regel toe. Zorg ervoor dat u de juiste waarde gebruikt voor de variabele $AZ _DATABASE_NAME.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>De app bouwen en implementeren in azure veer Cloud
Bouw de app opnieuw op en implementeer deze in de Azure veer Cloud-app die is ingericht in het tweede opsommings teken onder vereisten. U hebt nu een Spring boot-toepassing die is geverifieerd met een beheerde identiteit en die gebruikmaakt van JPA om gegevens op te slaan en op te halen uit een Azure SQL Database in azure lente-Cloud.

## <a name="next-steps"></a>Volgende stappen

* [Toegang tot de Storage-blob met een beheerde identiteit in een Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Door het systeem toegewezen beheerde identiteit inschakelen voor de Azure Spring Cloud-toepassing](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Meer informatie over beheerde identiteiten voor Azure-resources](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Azure Spring Cloud verifiëren met Key Vault in GitHub Actions](./spring-cloud-github-actions-key-vault.md)