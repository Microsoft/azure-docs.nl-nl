---
title: Azure SQL Database registeren en scannen
description: In deze zelfstudie wordt beschreven hoe u Azure SQL Database scant
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 15708e35fa27bb4a1f72368df6f49ff747eb799b
ms.sourcegitcommit: 44844a49afe8ed824a6812346f5bad8bc5455030
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/23/2020
ms.locfileid: "97739787"
---
# <a name="register-and-scan-an-azure-sql-database"></a>Een Azure SQL Database registeren en scannen

In dit artikel wordt beschreven hoe u een gegevensbron van een Azure SQL Database in Purview registreert en instelt om er een scan op uit te voeren.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De gegevensbron van de Azure SQL Database biedt ondersteuning voor de volgende functionaliteit:

- **Volledige en incrementele scans** voor het vastleggen van metagegevens en classificaties in de Azure SQL Database.

- **Herkomst** tussen gegevensassets voor kopieer- en gegevensstroomactiviteiten in ADF.

### <a name="known-limitations"></a>Bekende beperkingen

Azure Purview biedt geen ondersteuning voor het scannen van [weergaven](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15&preserve-view=true) in een Azure SQL Database. 

## <a name="prerequisites"></a>Vereisten

1. Maak een nieuw Purview-account aan als u er nog geen hebt.

1. Netwerktoegang tussen het Purview-account en Azure SQL Database.


### <a name="set-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

Verificatie om een Azure SQL Database te scannen. Als u nieuwe verificatie wilt maken, moet u de [SQL Database autoriseren voor toegang tot de database](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage). Er zijn drie verificatiemethoden die momenteel door Purview worden ondersteund:

- SQL-verificatie
- Service-principal
- Beheerde identiteit

#### <a name="sql-authentication"></a>SQL-verificatie

> [!Note]
> Alleen de principal-aanmelding op serverniveau (gemaakt tijdens het inrichtingsproces) of leden met de databaserol `loginmanager` in de hoofddatabase kunnen nieuwe aanmeldingen maken. Het duurt ongeveer **15 minuten** na het verlenen van de machtiging voordat het Purview-account de juiste machtigingen heeft om de resource(s) te scannen.

U kunt de instructies in [AANMELDING MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) volgen om een aanmelding voor de Azure SQL Database te maken, als u deze niet hebt. U hebt de **gebruikersnaam** en het **wachtwoord** nodig voor de volgende stappen.

1. Ga in Azure Portal naar uw sleutelkluis
1. Selecteer **Instellingen > Geheimen**
1. Selecteer **+ Genereren/importeren** en voer de **Naam** en **Waarde** in als het *wachtwoord* van uw Azure SQL Database
1. Selecteer **Maken** om te voltooien
1. Als uw sleutelkluis nog niet is verbonden met Purview, moet u [een nieuwe sleutelkluisverbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak tot slot [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de **gebruikersnaam** en het **wachtwoord** om uw scan in te stellen

#### <a name="service-principal-and-managed-identity"></a>Service-principal en beheerde identiteit

Er moeten verschillende stappen worden uitgevoerd om Purview toe te staan service-principal of **beheerde identiteit** van Purview te gebruiken voor het scannen van uw Azure SQL Database

   > [!Note]
   > Purview heeft de **Toepassings-id (client)** en het **clientgeheim** nodig om te kunnen scannen.

##### <a name="create-or-use-an-existing-service-principal"></a>Een bestaande service-principal maken of gebruiken

> [!Note]
> Sla deze stap over als u **beheerde identiteit** van Purview gebruikt

Als u een service-principal wilt gebruiken, kunt u een bestaande gebruiken of een nieuwe maken. 

> [!Note]
> Als u een nieuwe service-principal moet maken, volgt u deze stappen:
> 1. Navigeer naar [Azure Portal](https://portal.azure.com).
> 1. Selecteer **Azure Active Directory** in het menu aan de linkerkant.
> 1. Selecteer **App-registraties**.
> 1. Selecteer **+ Nieuwe toepassing registreren**.
> 1. Voer een naam in voor de **toepassing** (de service-principal-naam).
> 1. Selecteer **Alleen accounts in deze organisatiemap**.
> 1. Bij Omleidings-URI selecteert u **Web** en voert u de gewenste URL in. Dit hoeft geen echte of werkende URL te zijn.
> 1. Selecteer vervolgens **Registreren**.

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Azure AD-verificatie configureren in het databaseaccount

De service-principal of beheerde identiteit moet gemachtigd zijn om metagegevens op te halen voor de database, schema's en tabellen. Deze moet ook in staat zijn om query's uit te voeren op tabellen voor het nemen van steekproeven voor classificatiedoeleinden.

- [Azure AD-verificatie configureren en beheren met Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure)
- Als u beheerde identiteit gebruikt, heeft uw Purview-account een eigen beheerde identiteit die in principe bestaat uit uw Purview-naam van het moment waarop u deze hebt gemaakt. U moet een Azure AD-gebruiker maken in Azure SQL Database met de exacte beheerde identiteit van Purview of uw eigen service-principal door de zelfstudie te volgen op [De gebruiker van de service-principal in Azure SQL Database maken](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial#create-the-service-principal-user-in-azure-sql-database). U moet de juiste machtigingen (bijvoorbeeld `db_owner` of `db_datareader`) toewijzen aan de identiteit. Voorbeeld van een SQL-syntaxis om een gebruiker te maken en machtiging te geven:

    ```sql
    CREATE USER [Username] FROM EXTERNAL PROVIDER
    GO
    
    EXEC sp_addrolemember 'db_owner', [Username]
    GO
    ```

    > [!Note]
    > De `Username` is uw eigen service-principal of beheerde identiteit van Purview. U kunt meer lezen over [functies voor vaste databases en de bijbehorende mogelijkheden](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15&preserve-view=true#fixed-database-roles).
    
##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>De service-principal toevoegen aan de sleutelkluis en de referentie van Purview

> [!Note]
> Als u van plan bent om de **beheerde identiteit** van Purview te gebruiken, kunt u deze stap overslaan omdat de standaard beheerde identiteit van Purview al aanwezig is in de referentie **Purview-MSI**.

Het is vereist om de toepassings-id en het geheim van de service-principal op te halen:

1. Navigeer naar uw service-principal in [Azure Portal](https://portal.azure.com)
1. Kopieer de waarde van de **Toepassings-id (client)** uit **Overzicht** en van **Clientgeheim** uit **Certificaten & geheimen**.
1. Navigeer naar uw sleutelkluis
1. Selecteer **Instellingen > Geheimen**
1. Selecteer **+ Genereren/importeren** en voer de **Naam** van uw keuze in en de **Waarde** als het **Clientgeheim** van uw service-principal
1. Selecteer **Maken** om te voltooien
1. Als uw sleutelkluis nog niet is verbonden met Purview, moet u [een nieuwe sleutelkluisverbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak tot slot [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de service-principal om uw scan in te stellen

### <a name="firewall-settings"></a>Firewallinstellingen

De databaseserver moet toestaan dat Azure-verbindingen worden ingeschakeld. Hierdoor kan Azure Purview de server bereiken en er verbinding mee maken. U kunt de instructiegids voor [Verbindingen vanuit Azure](../azure-sql/database/firewall-configure.md#connections-from-inside-azure) volgen.

1. Navigeer naar uw databaseaccount
1. Selecteer de servernaam op de pagina **Overzicht**
1. Selecteer **Beveiliging > Firewalls en virtuele netwerken**
1. Selecteer **Ja** bij **Toestaan dat Azure-services en -resources toegang tot deze server krijgen**

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="Toestaan dat Azure-services en -resources toegang tot deze server krijgen." border="true":::
    
> [!Note]
> Momenteel biedt Azure Purview geen ondersteuning voor VNET-configuratie. Daarom kunt u geen firewall-instellingen op basis van IP uitvoeren.

## <a name="register-an-azure-sql-database-data-source"></a>Een Azure SQL Database-gegevensbron registeren

Ga als volgt te werk om een nieuwe Azure SQL Database in uw gegevenscatalogus te registreren:

1. Ga naar uw Purview-account

1. Selecteer **Bronnen** in het linkernavigatievenster

1. Selecteer **Registreren**

1. Selecteer **Azure SQL Database** op **Bronnen registreren**. Selecteer **Doorgaan**.

:::image type="content" source="media/register-scan-azure-sql-database/register-new-data-source.png" alt-text="nieuwe gegevensbron registreren" border="true":::

Ga als volgt te werk op het scherm **Bronnen registreren (Azure SQL Database)** :

1. Voer een **Naam** in waarvan de gegevensbron wordt vermeld in de catalogus.
1. Kies hoe u wilt verwijzen naar het gewenste opslagaccount:
   1. Selecteer **In Azure-abonnement**, selecteer het juiste abonnement in de vervolgkeuzelijst van het **Azure-abonnement** en de juiste server in de vervolgkeuzelijst **Servernaam**.
   1. U kunt ook **Handmatig invoeren** selecteren en een **Servernaam** invoeren.
1. **Voltooi** om de gegevensbron te registreren.

:::image type="content" source="media/register-scan-azure-sql-database/add-azure-sql-database.png" alt-text="opties voor bronnen registreren" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> Als u uw scan verwijdert, worden uw assets niet verwijderd uit eerdere Azure SQL Database-scans.

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)
