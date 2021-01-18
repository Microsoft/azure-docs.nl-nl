---
title: Azure Synapse Analytics scannen
description: Hier vindt u informatie over het scannen van Azure Synapse Analytics.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: c95f8b9e4466b22519a4dea580a86a0dcda83857
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555930"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Azure Synapse Analytics registreren en controleren

In dit artikel wordt beschreven hoe u een exemplaar van Azure Synapse Analytics (voorheen SQL DW) in controle sfeer liggen registreert en scant.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Azure Synapse Analytics (voorheen SQL DW) biedt ondersteuning voor volledige en incrementele scans voor het vastleggen van de meta gegevens en het schema. Met scans worden de gegevens ook automatisch geclassificeerd op basis van systeem-en aangepaste classificatie regels.

### <a name="known-limitations"></a>Bekende beperkingen

Azure controle sfeer liggen biedt geen ondersteuning voor het scannen van [weer gaven](/sql/relational-databases/views/views?view=azure-sqldw-latest&preserve-view=true) in azure Synapse Analytics

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.
- U moet een Azure controle sfeer liggen-gegevens bron beheerder zijn
- Netwerk toegang tussen het controle sfeer liggen-account en Azure Synapse Analytics.
 
## <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

Er zijn drie manieren om verificatie voor Azure Blob-opslag in te stellen:

- Beheerde identiteit
- SQL-verificatie
- Service-principal

    > [!Note]
    > Alleen de principal-aanmelding op serverniveau (gemaakt tijdens het inrichtingsproces) of leden met de databaserol `loginmanager` in de hoofddatabase kunnen nieuwe aanmeldingen maken. Het duurt ongeveer 15 minuten na het verlenen van de machtiging voordat het Purview-account de juiste machtigingen heeft om de resource(s) te scannen.

### <a name="managed-identity-recommended"></a>Beheerde identiteit (aanbevolen) 
   
Uw controle sfeer liggen-account heeft een eigen beheerde identiteit die in principe uw controle sfeer liggen-naam is wanneer u deze hebt gemaakt. U moet een Azure AD-gebruiker maken in azure Synapse Analytics (voorheen SQL DW) met de exacte naam van de beheerde identiteit van de controle sfeer liggen door de vereisten en zelf studie te volgen voor het [maken van Azure AD-gebruikers met Azure AD-toepassingen](/azure/azure-sql/database/authentication-aad-service-principal-tutorial).

Voorbeeld van een SQL-syntaxis om een gebruiker te maken en machtiging te geven:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

De verificatie moet gemachtigd zijn om meta gegevens op te halen voor de data base, schema's en tabellen. Deze moet ook in staat zijn om query's uit te voeren op tabellen voor het nemen van steekproeven voor classificatiedoeleinden. De aanbeveling is om `db_owner` machtigingen toe te wijzen aan de identiteit.

### <a name="service-principal"></a>Service-principal

Als u Service-Principal-verificatie voor scans wilt gebruiken, kunt u een bestaande gebruiken of een nieuw account maken. 

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

Het is vereist om de toepassings-ID en het geheim van de Service-Principal op te halen:

1. Navigeer naar uw service-principal in [Azure Portal](https://portal.azure.com)
1. Kopieer de waarde van de **Toepassings-id (client)** uit **Overzicht** en van **Clientgeheim** uit **Certificaten & geheimen**.
1. Navigeer naar uw sleutelkluis
1. Selecteer **Instellingen > Geheimen**
1. Selecteer **+ Genereren/importeren** en voer de **Naam** van uw keuze in en de **Waarde** als het **Clientgeheim** van uw service-principal
1. Selecteer **Maken** om te voltooien
1. Als uw sleutelkluis nog niet is verbonden met Purview, moet u [een nieuwe sleutelkluisverbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak tot slot [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de service-principal om uw scan in te stellen 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>De Service-Principal toegang verlenen tot uw Azure Synapse Analytics (voorheen SQL DW)

Daarnaast moet u ook een Azure AD-gebruiker maken in azure Synapse Analytics door de vereisten en zelf studie te volgen voor het [maken van Azure AD-gebruikers met Azure AD-toepassingen](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial). Voorbeeld van een SQL-syntaxis om een gebruiker te maken en machtiging te geven:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> Purview heeft de **Toepassings-id (client)** en het **clientgeheim** nodig om te kunnen scannen.

### <a name="sql-authentication"></a>SQL-verificatie

U kunt de instructies in [login maken](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples-1) volgen om een aanmelding voor Azure Synapse Analytics (voorheen SQL DW) te maken als u er nog geen hebt.

Wanneer de geselecteerde verificatie methode is ingesteld op **SQL-verificatie**, moet u uw wacht woord ophalen en opslaan in de sleutel kluis:

1. Het wacht woord voor uw SQL-aanmelding ophalen
1. Navigeer naar uw sleutelkluis
1. Selecteer **Instellingen > Geheimen**
1. Selecteer **+ genereren/importeren** en voer de **naam** en **waarde** in als het *wacht woord* voor uw SQL-aanmelding
1. Selecteer **Maken** om te voltooien
1. Als uw sleutelkluis nog niet is verbonden met Purview, moet u [een nieuwe sleutelkluisverbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak ten slotte [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de sleutel voor het instellen van de scan

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Een Azure Synapse Analytics-exemplaar (voorheen SQL DW) registreren

Ga als volgt te werk om een nieuwe Azure Synapse Analytics-server te registreren in uw Data Catalog:

1. Ga naar uw Purview-account
1. Selecteer **Bronnen** in het linkernavigatievenster
1. Selecteer **Registreren**
1. Selecteer **Azure Synapse Analytics (voorheen SQL DW)** bij **bronnen registreren**.
1. Selecteer **Doorgaan**

Ga als volgt te werk op het scherm **bronnen registreren (Azure Synapse Analytics)** :

1. Voer een **Naam** in waarvan de gegevensbron wordt vermeld in de catalogus.
1. Kies hoe u wilt verwijzen naar het gewenste opslagaccount:
   1. Selecteer een **Azure-abonnement**, selecteer het juiste abonnement in de vervolg keuzelijst van het Azure- **abonnement** en de juiste server in de vervolg keuzelijst **Server naam** .
   1. U kunt ook **Handmatig invoeren** selecteren en een **Servernaam** invoeren.
1. **Voltooi** om de gegevensbron te registreren.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="opties voor bronnen registreren" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)

