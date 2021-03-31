---
title: 'Quickstart: Een hybride modus-exemplaar maken met de Azure-portal'
titleSuffix: Azure Database Migration Service
description: Gebruik de Azure-portal om een Azure Database Migration Service-exemplaar in hybride modus te maken.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: quickstart
ms.date: 03/13/2020
ms.openlocfilehash: 39f893e9375970ff4bb6e3cfa2c93ceea48ec896
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92331801"
---
# <a name="quickstart-create-a-hybrid-mode-instance-with-azure-portal--azure-database-migration-service"></a>Quickstart: Een hybride modus-exemplaar maken met de Azure-portal en Azure Database Migration Service

In de hybride modus van Azure Database Migration Service worden databasemigraties beheerd met een migratiewerkrol die on-premises wordt gehost, samen met een exemplaar van Azure Database Migration Service dat in de cloud wordt uitgevoerd. De hybride modus is vooral nuttig voor scenario's waarin er niet voldoende site-naar-site-connectiviteit is tussen het on-premises netwerk en Azure of een beperkte bandbreedte voor site-naar-site-connectiviteit.

>[!NOTE]
>Momenteel ondersteunt Azure Database Migration Service in de hybride modus SQL Server-migraties naar:
>
>- Met Azure SQL beheerd exemplaar met bijna geen downtime (online).
>- Individuele database van Azure SQL Database met enige downtime (offline).
>- MongoDb naar Azure CosmosDB met bijna geen downtime (online).
>- MongoDb naar Azure CosmosDB met enige downtime (offline).

In deze quickstart gebruikt u de Azure-portal om een Azure Database Migration Service-exemplaar te maken in hybride modus. Daarna kunt u de hybride werkrol in uw on-premises netwerk downloaden, installeren en instellen. Tijdens de preview kunt u Azure Database Migration Service gebruiken voor het migreren van gegevens van een on-premises exemplaar van SQL Server naar Azure SQL Database.

> [!NOTE]
> Het hybride installatieprogramma van Azure Database Migration Service wordt uitgevoerd op Microsoft Windows Server 2012 R2, Windows Server 2016, Windows Server 2019 en Windows 10.

> [!IMPORTANT]
> Voor het hybride installatieprogramma van Azure Database Migration Service is .NET 4.7.2 of hoger vereist. Ga naar de pagina [.NET Framework downloaden](https://dotnet.microsoft.com/download/dotnet-framework) voor de nieuwste versies van .NET.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open de webbrowser, navigeer naar [Microsoft Azure Portal](https://portal.azure.com/), en voer vervolgens uw referenties in om u aan te melden bij de portal.

De standaardweergave is uw service-dashboard.

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

Registreer de Microsoft.DataMigration-resourceprovider voordat u uw eerste Azure Database Migration Service-exemplaar maakt.

1. Selecteer in de Azure-portal **Abonnementen**, selecteer het abonnement waarin u het exemplaar van Azure Database Migration Service wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceprovider zoeken](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-search-resource-provider.png)

2. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/quickstart-create-data-migration-service-hybrid-portal/dms-portal-register-resource-provider.png)

## <a name="create-an-instance-of-the-service"></a>Een exemplaar van de service maken

1. Selecteer **+Een resource maken** om een exemplaar te maken van Azure Database Migration Service.

2. Zoek in Marketplace naar 'migration', selecteer **Azure Database Migration Service** en selecteer vervolgens in het scherm **Azure Database Migration Service**  de optie **Maken**.

3. Ga in het scherm **Migratieservice maken** als volgt te werk:

    - Kies een unieke en gemakkelijk te onthouden **Servicenaam** om uw Azure Database Migration Service-exemplaar te identificeren.
    - Selecteer het Azure-**abonnement** waarin u het exemplaar wilt maken.
    - Selecteer een bestaande **Resourcegroep** of maak een nieuwe.
    - Kies de **locatie** die zich het dichtst bij uw bron- of doelserver bevindt.
    - Selecteer **Hybride (preview)** bij **Servicemodus**.

         ![Migratieservice maken - grondbeginselen](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-basics.png)

4. Selecteer **Controleren + maken**.

5. Bekijk op het tabblad **Beoordelen en maken** de voorwaarden, controleer de andere gegevens en selecteer **Maken**.

    ![Migratieservice maken - Beoordelen en maken](media/quickstart-create-data-migration-service-hybrid-portal/dms-create-service-review-and-create.png)

    Na enkele ogenblikken is uw Azure Database Migration Service-exemplaar in de hybride modus gemaakt en klaar om verder te worden ingesteld. Het exemplaar van Azure Database Migration Service wordt weergegeven zoals in de volgende afbeelding:

    ![Hybride modus-exemplaar van Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode.png)

6. Nadat de service is gemaakt, selecteert u **Eigenschappen** en kopieert u de waarde die wordt weergegeven in het vak **Resource-id**. Deze waarde gebruikt u voor het installeren van de hybride werkrol van Azure Database Migration Service.

    ![Hybride modus van Azure Database Migration Service - eigenschappen](media/quickstart-create-data-migration-service-hybrid-portal/dms-copy-resource-id.png)

## <a name="create-azure-app-registration-id"></a>Registratiesleutels voor de Azure-app maken

U moet een registratie-id voor de Azure-app maken die de on-premises hybride werkrol kan gebruiken om te communiceren met Azure Database Migration Service in de cloud.

1. Selecteer in de Azure-portal achtereenvolgens **Azure Active Directory**, **App-registraties** en **Nieuwe registratie**.
2. Geef een naam op voor de toepassing en selecteer onder **Ondersteunde accounttypen** het type accounts dat moet worden ondersteund om op te geven wie de toepassing kan gebruiken.

    ![Hybride modus van Azure Database Migration Service - toepassing registreren](media/quickstart-create-data-migration-service-hybrid-portal/dms-register-application.png)

3. Gebruik de standaardwaarden voor de velden **Omleidings-URI (optioneel)** en selecteer **Registreren**.

4. Nadat de registratie van de app-id is voltooid, noteert u de **Toepassings-id (client)** , die u gebruikt tijdens de installatie van de hybride werkrol.

5. In de Azure-portal gaat u naar Azure Database Migration Service, selecteert u **Toegangsbeheer (IAM)** en selecteert u vervolgens **Roltoewijzing toevoegen** om toegang als Inzender toe te wijzen aan de app-id.

    ![Hybride modus van Azure Database Migration Service - rol Inzender toewijzen](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-assign-contributor.png)

6. Selecteer **Inzender** als rol, wijs toegang toe aan **Azure AD-user of service-principal** en selecteer de naam van de app-id.

    ![Hybride modus van Azure Database Migration Service - details van rol Inzender toewijzen](media/quickstart-create-data-migration-service-hybrid-portal/dms-add-role-assignment.png)

7. Selecteer **Opslaan** om de roltoewijzing voor de app-id op te slaan in de Azure Database Migration Service-resource.

## <a name="download-and-install-the-hybrid-worker"></a>De hybride werkrol downloaden en installeren

1. Ga in de Azure-portal naar uw exemplaar van Azure Database Migration Service.

2. Selecteer onder **Instellingen** de optie **Hybride** en selecteer **Installatieprogramma downloaden** om de hybride werkrol te downloaden.

    ![Azure Database Migration Service - hybride werkrol downloaden](media/quickstart-create-data-migration-service-hybrid-portal/dms-installer-download.png)

3. Pak het ZIP-bestand uit op de server die als host fungeert voor de hybride werkrol van Azure Database Migration Service.

    > [!IMPORTANT]
    > Voor het hybride installatieprogramma van Azure Database Migration Service is .NET 4.7.2 of hoger vereist. Ga naar de pagina [.NET Framework downloaden](https://dotnet.microsoft.com/download/dotnet-framework) voor de nieuwste versies van .NET.

4. Zoek en open het bestand **dmsSettings.json** in de installatiemap, geef de **ApplicationId** en **resourceId** op en sla het bestand op.

    ![Azure Database Migration Service - instellingen van hybride werkrol](media/quickstart-create-data-migration-service-hybrid-portal/dms-settings.png)

5. Genereer een certificaat waarmee Azure Database Migration Service de communicatie van de hybride werkrol kan verifiëren met de volgende opdracht.

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a GenerateCert
    ```

    Er wordt een certificaat gegenereerd in de installatiemap.

    ![Azure Database Migration Service - certificaat van hybride werkrol](media/quickstart-create-data-migration-service-hybrid-portal/dms-certificate.png)

6. Navigeer in de Azure-portal naar de app-id onder **Beheren**, selecteer **Certificaten en geheimen** en selecteer vervolgens **Certificaat uploaden** om het openbare certificaat te selecteren dat u hebt gegenereerd.

    ![Azure Database Migration Service - certificaat van hybride werkrol uploaden](media/quickstart-create-data-migration-service-hybrid-portal/dms-app-upload-certificate.png)

7. Installeer de hybride werkrol van Azure Database Migration Service op uw on-premises server door de volgende opdracht uit te voeren:

    ```
    <drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a Install -IAcceptDMSLicenseTerms -d
    ```

    > [!NOTE]
    > Wanneer u de installatieopdracht uitvoert, kunt u ook de volgende parameters gebruiken:
    >
    > - **-TelemetryOptOut**: hiermee stopt u het verzenden van telemetrie door de werkrol, maar wordt de lokale logboekregistratie beperkt voortgezet.  Het installatieprogramma verzendt nog wel telemetrie.
    > - **-p {InstallLocation}** . Hiermee schakelt u het wijzigen van het installatiepad in, dat standaard C:\Program Files\DatabaseMigrationServiceHybrid is.

8. Als het installatieprogramma zonder fouten wordt uitgevoerd, wordt de status Online weergegeven in Azure Database Migration Service en kunt u de databases gaan migreren.

    ![Online Azure Database Migration Service](media/quickstart-create-data-migration-service-hybrid-portal/dms-instance-hybrid-mode-online.png)

## <a name="uninstall-azure-database-migration-service-hybrid-mode"></a>Hybride modus van Azure Database Migration Service verwijderen

Momenteel wordt het verwijderen van de hybride modus van Azure Database Migration Service alleen ondersteund via het installatieprogramma van de hybride werkrol van Azure Database Migration Service op de on-premises server, met de volgende opdracht:

```
<drive>:\<folder>\Install>DMSWorkerBootstrap.exe -a uninstall
```

> [!NOTE]
> Wanneer u de opdracht voor verwijderen uitvoert, kunt u ook de parameter -ReuseCert gebruiken, waarmee het AdApp-certificaat wordt behouden dat door de werkstroom generateCert is gegenereerd.  Hierdoor kan hetzelfde certificaat worden gebruikt dat eerder is gegenereerd en geüpload.

## <a name="set-up-the-azure-database-migration-service-hybrid-worker-using-powershell"></a>De hybride werkrol van Azure Database Migration Service instellen met PowerShell

Naast het installeren van de hybride werkrol van Azure Database Migration Service via de Azure-portal, bieden we een [PowerShell-script](https://techcommunity.microsoft.com/gxcuf89792/attachments/gxcuf89792/MicrosoftDataMigration/119/1/DMS_Hybrid_Script.zip) waarmee u de stappen voor installatie van de werkrol kunt automatiseren nadat u een nieuw exemplaar van Azure Database Migration Service in de hybride modus hebt gemaakt. Het script:

1. Maakt een nieuwe AdApp.
2. Downloadt het installatieprogramma.
3. Voert de werkstroom generateCert uit.
4. Uploadt het certificaat.
5. Voegt de AdApp toe als inzender aan uw Azure Database Migration Service-exemplaar.
6. Voert de installatiewerkstroom uit.

Dit script is bedoeld voor een snelle ontwikkeling van prototypen wanneer de gebruiker al over de benodigde machtigingen beschikt in de omgeving. Houd er rekening mee dat de AdApp en het certificaat in uw productieomgeving verschillende vereisten kunnen hebben, waardoor het script kan mislukken.

> [!IMPORTANT]
> In dit script wordt ervan uitgegaan dat er een bestaand exemplaar van Azure Database Migration Service in de hybride modus is en dat het gebruikte Azure-account machtigingen heeft voor het maken van AdApps in de tenant en voor het wijzigen van Azure RBAC in het abonnement.

Vul de parameters bovenaan het script in en voer het script uit vanuit een PowerShell-exemplaar van een administrator.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [SQL Server online migreren naar een met Azure SQL beheerd exemplaar](tutorial-sql-server-managed-instance-online.md)
> [SQL Server offline migreren naar Azure SQL Database](tutorial-sql-server-to-azure-sql.md)
