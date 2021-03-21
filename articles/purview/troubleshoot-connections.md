---
title: Problemen met uw verbindingen in azure controle sfeer liggen oplossen
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen met uw verbindingen in azure controle sfeer liggen.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: c176fcafe13749ba89c04b34854f036aa5aea516
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677648"
---
# <a name="troubleshoot-your-connections-in-azure-purview"></a>Problemen met uw verbindingen in azure controle sfeer liggen oplossen

In dit artikel wordt beschreven hoe u verbindings fouten oplost tijdens het instellen van scans voor gegevens bronnen in azure controle sfeer liggen.

## <a name="permission-the-credential-on-the-data-source"></a>Toestemming van de referentie voor de gegevens bron

Als u een beheerde identiteit of Service-Principal als verificatie methode voor scans gebruikt, moet u ervoor zorgen dat deze identiteiten toegang hebben tot uw gegevens bron.

Er zijn specifieke instructies voor elk bron type:

- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Cosmos DB](register-scan-azure-cosmos-database.md#setting-up-authentication-for-a-scan)
- [Azure Data Explorer](register-scan-azure-data-explorer.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Beheerde instantie Azure SQL Database](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
- [SQL Server](register-scan-on-premises-sql-server.md#setting-up-authentication-for-a-scan)
- [Power BI](register-scan-power-bi-tenant.md)
- [Amazon S3](register-scan-amazon-s3.md#create-a-purview-credential-for-your-aws-bucket-scan)
## <a name="storing-your-credential-in-your-key-vault-and-using-the-right-secret-name-and-version"></a>Uw referenties opslaan in uw sleutel kluis en de juiste geheime naam en versie gebruiken

U moet uw referenties ook opslaan in uw Azure Key Vault-exemplaar en de juiste geheime naam en versie gebruiken.

Controleer dit door de volgende stappen te volgen:

1. Navigeer naar uw Key Vault.
1. Selecteer **Instellingen** > **Geheimen**.
1. Selecteer het geheim dat u gebruikt om uw gegevens bron te verifiëren voor scans.
1. Selecteer de versie die u wilt gebruiken en controleer of het wacht woord of de account sleutel juist is door te klikken op **geheime waarde weer geven**. 

## <a name="verify-permissions-for-the-purview-managed-identity-on-your-azure-key-vault"></a>Controleer de machtigingen voor de door controle sfeer liggen beheerde identiteit op uw Azure Key Vault

Controleer of de juiste machtigingen zijn geconfigureerd voor de beheerde identiteit controle sfeer liggen om toegang te krijgen tot uw Azure Key Vault.

Voer de volgende stappen uit om dit te controleren:

1. Ga naar de sleutel kluis en de sectie **toegangs beleid**

1. Controleer of uw door controle sfeer liggen beheerde identiteit wordt weer gegeven in de sectie *huidige toegangs beleid* met ten minste de machtigingen **Get** en **List** voor geheimen

   :::image type="content" source="./media/troubleshoot-connections/verify-minimum-permissions.png" alt-text="Afbeelding van de keuze lijst met opties voor ophalen en lijsten":::

Als uw beheerde identiteit voor controle sfeer liggen niet wordt weer gegeven, volgt u de stappen in [referenties voor scannen maken en beheren](manage-credentials.md) om deze toe te voegen. 

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)
