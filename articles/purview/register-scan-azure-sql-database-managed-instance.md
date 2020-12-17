---
title: Azure SQL Database Managed Instance registeren en scannen
description: In deze zelfstudie wordt beschreven hoe u Azure SQL Database Managed Instance scant
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 6eb17537fd64b192f64c36b38bab57e11d751328
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400774"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance registeren en scannen

In dit artikel wordt beschreven hoe u een gegevensbron van Azure SQL Database Managed Instance in Purview registreert en instelt om er een scan op uit te voeren.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De gegevensbron van Azure SQL Database Managed Instance biedt ondersteuning voor de volgende functionaliteit:

- **Volledige en incrementele scans** voor het vastleggen van metagegevens en classificaties in Azure SQL Database Managed Instance.

- **Herkomst** tussen gegevensassets voor kopieer- en gegevensstroomactiviteiten in ADF.

### <a name="known-limitations"></a>Bekende beperkingen

Azure Purview biedt geen ondersteuning voor het scannen van [weergaven](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15) in een beheerde instantie van Azure SQL Database.

## <a name="prerequisites"></a>Vereisten

- Maak een nieuw Purview-account aan als u er nog geen hebt.

- [Openbaar eindpunt configureren in een beheerd Azure SQL-exemplaar](https://docs.microsoft.com/azure/azure-sql/managed-instance/public-endpoint-configure)
    > [!Note]
    > Uw organisatie moet een openbaar eindpunt kunnen toestaan omdat **privé-eindpunten nog niet worden ondersteund** door Purview. Als u een privé-eindpunt gebruikt, mislukt de scan.

### <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

Verificatie om Azure SQL Database Managed Instance te scannen. Als u nieuwe verificatie wilt maken, moet u de [beheerde instantie van Azure SQL Database autoriseren voor toegang tot de database](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage). Er zijn drie verificatiemethoden die momenteel door Purview worden ondersteund:

- SQL-verificatie
- Service-principal
- Beheerde identiteit

#### <a name="sql-authentication"></a>SQL-verificatie

> [!Note]
> Alleen de principal-aanmelding op serverniveau (gemaakt tijdens het inrichtingsproces) of leden met de databaserol `loginmanager` in de hoofddatabase kunnen nieuwe aanmeldingen maken. Het duurt ongeveer **15 minuten** na het verlenen van de machtiging voordat het Purview-account de juiste machtigingen heeft om de resource(s) te scannen.

U kunt de instructies in [AANMELDING MAKEN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) volgen om een aanmelding voor Azure SQL Database Managed Instance te maken, als u deze niet hebt. U hebt de **gebruikersnaam** en het **wachtwoord** nodig voor de volgende stappen.

1. Ga in Azure Portal naar uw sleutelkluis
1. Selecteer **Instellingen > Geheimen**
1. Selecteer **+ Genereren/importeren** en voer de **naam** en **waarde** in als het *wachtwoord* van uw Azure SQL Database Managed Instance
1. Selecteer **Maken** om te voltooien
1. Als uw sleutelkluis nog niet is verbonden met Purview, moet u [een nieuwe sleutelkluisverbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak tot slot [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de **gebruikersnaam** en het **wachtwoord** om uw scan in te stellen

#### <a name="service-principal-and-managed-identity"></a>Service-principal en beheerde identiteit

Er moeten verschillende stappen worden uitgevoerd om Purview toe te staan service-principal te gebruiken voor het scannen van uw Azure SQL Database Managed Instance

##### <a name="create-or-use-an-existing-service-principal"></a>Een bestaande service-principal maken of gebruiken

> [!Note]
> Sla deze stap over als u **beheerde identiteit** gebruikt

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
- Maak een Azure AD-gebruiker in Azure SQL Database Managed Instance door ervoor te zorgen dat er aan de vereisten wordt voldaan, en door de zelfstudie [Ingesloten gebruikers maken die zijn toegewezen aan Azure AD-identiteiten](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities) te volgen
- Wijs de machtiging `db_owner` toe (**aanbevolen**) aan de identiteit

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>De service-principal toevoegen aan de sleutelkluis en de referentie van Purview

> [!Note]
> Als u van plan bent om de **beheerde identiteit** te gebruiken, kunt u deze stap overslaan omdat de standaard identiteit van Purview al aanwezig is in de **Purview-MSI**

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

    :::image type="content" source="media/register-scan-azure-sql-database/sql-firewall.png" alt-text="opties voor bronnen registreren" border="true":::
    
> [!Note]
> Momenteel biedt Azure Purview geen ondersteuning voor VNET-configuratie. Daarom kunt u geen firewall-instellingen op basis van IP uitvoeren.

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Een gegevensbron van Azure SQL Database Managed Instance registeren

1. Ga naar uw Purview-account

1. Selecteer **Bronnen** in het linkernavigatievenster

1. Selecteer **Registreren**

1. Selecteer **Azure SQL Database Managed Instance** en vervolgens **Doorgaan**

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="De SQL-gegevensbron instellen":::

1. Selecteer **Handmatig invoeren**

1. Geef de **Fully Qualified Domain Name van het openbare eindpunt**  en **poortnummer** op. Selecteer vervolgens **Voltooien** om de gegevensbron te registreren.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Azure SQL Database Managed Instance toevoegen":::

    Bijvoorbeeld `foobar.public.123.database.windows.net,3342`

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> Als u uw scan verwijdert, worden uw assets niet verwijderd uit eerdere scans van Azure SQL Database Managed Instance.

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)
