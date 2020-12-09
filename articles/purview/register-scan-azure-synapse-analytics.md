---
title: Azure Synapse Analytics scannen
description: Hier vindt u informatie over het scannen van Azure Synapse Analytics.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: e0a1d8dba9ea284322584de3b4be2ae390d15fdf
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920259"
---
# <a name="register-and-scan-azure-synapse-analytics"></a>Azure Synapse Analytics registreren en controleren

In dit artikel wordt beschreven hoe u een exemplaar van Azure Synapse Analytics (voorheen SQL DW) in controle sfeer liggen registreert en scant.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Azure Synapse Analytics (voorheen SQL DW) biedt ondersteuning voor volledige en incrementele scans voor het vastleggen van de meta gegevens en het schema. Met scans worden de gegevens ook automatisch geclassificeerd op basis van systeem-en aangepaste classificatie regels.

### <a name="known-limitations"></a>Bekende beperkingen

Azure controle sfeer liggen biedt geen ondersteuning voor het scannen van [weer gaven](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15) in azure Synapse Analytics

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.
- U moet een Azure controle sfeer liggen-gegevens bron beheerder zijn
- Netwerk toegang tussen het controle sfeer liggen-account en Azure Synapse Analytics.
 
## <a name="setting-up-authentication-for-a-scan"></a>Verificatie instellen voor een scan

Er zijn drie manieren om verificatie voor Azure Blob-opslag in te stellen:

- Beheerde identiteit
- SQL-verificatie
- Service-principal

    > [!Note]
    > Alleen de principal-aanmelding op server niveau (gemaakt door het inrichtings proces) of leden van de `loginmanager` databaserol in de hoofd database kunnen nieuwe aanmeldingen maken. Het duurt ongeveer 15 minuten nadat u een machtiging hebt verleend, het controle sfeer liggen-account moet de juiste machtigingen hebben om de resource (s) te kunnen scannen.

### <a name="managed-identity-recommended"></a>Beheerde identiteit (aanbevolen) 
   
Uw controle sfeer liggen-account heeft een eigen beheerde identiteit die in principe uw controle sfeer liggen-naam is wanneer u deze hebt gemaakt. U moet een Azure AD-gebruiker maken in azure Synapse Analytics (voorheen SQL DW) met de exacte naam van de beheerde identiteit van de controle sfeer liggen door de vereisten en zelf studie te volgen voor het [maken van Azure AD-gebruikers met Azure AD-toepassingen](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial).

Voor beeld van een SQL-syntaxis om een gebruiker te maken en toestemming te verlenen:

```sql
CREATE USER [PurviewManagedIdentity] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [PurviewManagedIdentity]
GO
```

De verificatie moet gemachtigd zijn om meta gegevens op te halen voor de data base, schema's en tabellen. Het moet ook in staat zijn om de tabellen te doorzoeken op steek proeven voor classificatie. De aanbeveling is om `db_owner` machtigingen toe te wijzen aan de identiteit.

### <a name="service-principal"></a>Service-principal

Als u Service-Principal-verificatie voor scans wilt gebruiken, kunt u een bestaande gebruiken of een nieuw account maken. 

> [!Note]
> Als u een nieuwe Service-Principal moet maken, volgt u deze stappen:
> 1. Navigeer naar [Azure Portal](https://portal.azure.com).
> 1. Selecteer **Azure Active Directory** in het menu aan de linkerkant.
> 1. Selecteer **App-registraties**.
> 1. Selecteer **+ nieuwe toepassing registreren**.
> 1. Voer een naam in voor de **toepassing** (de Service Principal Name).
> 1. Selecteer **alleen accounts in deze organisatie Directory**.
> 1. Voor omleidings-URI selecteert u **Web** en voert u de gewenste URL in. het hoeft niet echt of werk te zijn.
> 1. Selecteer vervolgens **Registreren**.

Het is vereist om de toepassings-ID en het geheim van de Service-Principal op te halen:

1. Navigeer naar uw Service-Principal in de [Azure Portal](https://portal.azure.com)
1. Kopieer de waarden van de **toepassings-id van de toepassing (client)** van het **overzicht** en het **client geheim** van **certificaten & geheimen**.
1. Navigeer naar uw sleutelkluis
1. **Instellingen > geheimen** selecteren
1. Selecteer **+ genereren/importeren** en voer de **naam** van uw keuze en **waarde** in als het **client geheim** van de Service-Principal
1. Selecteer **maken** om te volt ooien
1. Als uw sleutel kluis nog niet is verbonden met controle sfeer liggen, moet u [een nieuwe sleutel kluis verbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak ten slotte [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de service-principal voor het instellen van de scan 

#### <a name="granting-the-service-principal-access-to-your-azure-synapse-analytics-formerly-sql-dw"></a>De Service-Principal toegang verlenen tot uw Azure Synapse Analytics (voorheen SQL DW)

Daarnaast moet u ook een Azure AD-gebruiker maken in azure Synapse Analytics door de vereisten en zelf studie te volgen voor het [maken van Azure AD-gebruikers met Azure AD-toepassingen](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-service-principal-tutorial). Voor beeld van een SQL-syntaxis om een gebruiker te maken en toestemming te verlenen:

```sql
CREATE USER [ServicePrincipalName] FROM EXTERNAL PROVIDER
GO

EXEC sp_addrolemember 'db_owner', [ServicePrincipalName]
GO
```

> [!Note]
> Controle sfeer liggen heeft de **toepassings-id** en het **client geheim** nodig om te kunnen scannen.

### <a name="sql-authentication"></a>SQL-verificatie

U kunt de instructies in [login maken](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) volgen om een aanmelding voor Azure Synapse Analytics (voorheen SQL DW) te maken als u er nog geen hebt.

Wanneer de geselecteerde verificatie methode is ingesteld op **SQL-verificatie**, moet u uw wacht woord ophalen en opslaan in de sleutel kluis:

1. Het wacht woord voor uw SQL-aanmelding ophalen
1. Navigeer naar uw sleutelkluis
1. **Instellingen > geheimen** selecteren
1. Selecteer **+ genereren/importeren** en voer de **naam** en **waarde** in als het *wacht woord* voor uw SQL-aanmelding
1. Selecteer **maken** om te volt ooien
1. Als uw sleutel kluis nog niet is verbonden met controle sfeer liggen, moet u [een nieuwe sleutel kluis verbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak ten slotte [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de sleutel voor het instellen van de scan

## <a name="register-an-azure-synapse-analytics-instance-formerly-sql-dw"></a>Een Azure Synapse Analytics-exemplaar (voorheen SQL DW) registreren

Ga als volgt te werk om een nieuwe Azure Synapse Analytics-server te registreren in uw Data Catalog:

1. Navigeer naar uw controle sfeer liggen-account
1. **Bronnen** selecteren in de linkernavigatiebalk
1. Selecteer **Registreren**
1. Selecteer **Azure Synapse Analytics (voorheen SQL DW)** bij **bronnen registreren**.
1. Selecteer **Doorgaan**

Ga als volgt te werk op het scherm **bronnen registreren (Azure Synapse Analytics)** :

1. Voer een **naam** in die voor de gegevens bron wordt weer gegeven in de catalogus.
1. Kies hoe u wilt verwijzen naar het gewenste opslag account:
   1. Selecteer een **Azure-abonnement**, selecteer het juiste abonnement in de vervolg keuzelijst van het Azure- **abonnement** en de juiste server in de vervolg keuzelijst **Server naam** .
   1. U kunt **ook hand matig invoeren** selecteren en een **Server naam** invoeren.
1. **Volt ooien** om de gegevens bron te registreren.

:::image type="content" source="media/register-scan-azure-synapse-analytics/register-sources.png" alt-text="bronnen opties registreren" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure controle sfeer liggen Data Catalog](how-to-browse-catalog.md)
- [Zoek in de Azure controle sfeer liggen-Data Catalog](how-to-search-catalog.md)

