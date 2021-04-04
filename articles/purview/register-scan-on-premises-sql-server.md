---
title: Een on-premises SQL server registreren en scannen
description: In deze zelf studie wordt beschreven hoe u on-premises SQL Server scant met een zelf-hostende IR.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: b5f4218cfcd5f9ccfbe43efac46e2f70fdc30905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99574954"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>Een on-premises SQL server registreren en scannen

In dit artikel wordt beschreven hoe u een SQL Server-gegevens bron registreert in controle sfeer liggen en hoe u er een scan op uitvoert.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

De on-premises gegevens bron van SQL Server ondersteunt de volgende functionaliteit:

- **Volledige en incrementele scans** voor het vastleggen van meta gegevens en classificatie in een on-premises netwerk of een SQL Server die is geïnstalleerd op een virtuele machine van Azure.

- **Afkomst** tussen de gegevensassets voor ADF-Kopieer-en gegevensstroom activiteiten

SQL Server on-premises gegevens bron ondersteunt:

- elke versie van SQL van SQL Server 2019 terug naar SQL Server 2000

- Verificatie methode: SQL-verificatie

### <a name="known-limitations"></a>Bekende beperkingen

Azure controle sfeer liggen biedt geen ondersteuning voor het scannen van [weer gaven](/sql/relational-databases/views/views) in SQL Server.

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.

- Stel een [zelf-hostende Integration runtime](manage-integration-runtimes.md) in om de gegevens bron te scannen.

## <a name="setting-up-authentication-for-a-scan"></a>Verificatie voor een scan instellen

Er is slechts één manier om verificatie voor SQL Server on-premises in te stellen:

- SQL-verificatie

### <a name="sql-authentication"></a>SQL-verificatie

Het SQL-account moet toegang hebben tot de **hoofd** database. Dit komt doordat de `sys.databases` zich in de data base Master bevindt. De controle sfeer liggen-scanner moet worden geïnventariseerd `sys.databases` om alle SQL-data bases op de server te kunnen vinden.

#### <a name="using-an-existing-server-administrator"></a>Een bestaande server beheerder gebruiken

Als u van plan bent een bestaande sa-gebruiker (Server beheerder) te gebruiken om uw on-premises SQL-Server te scannen, controleert u het volgende:

1. `sa` is geen Windows-verificatie account.

2. De aanmeldings gegevens op server niveau die u wilt gebruiken, moeten Server rollen van openbaar en sysadmin hebben. U kunt dit controleren door verbinding te maken met de server, te navigeren naar SQL Server Management Studio (SSMS), naar de beveiliging te navigeren, de aanmeldings gegevens te selecteren die u wilt gebruiken, met de rechter muisknop op **Eigenschappen** en vervolgens **Server functies** te selecteren.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Aanmelden op server niveau.":::

#### <a name="creating-a-new-login-and-user"></a>Een nieuwe aanmelding en gebruiker maken

Als u een nieuwe aanmelding en gebruiker wilt maken om uw SQL-Server te kunnen scannen, volgt u de onderstaande stappen:

> [!Note]
   > Alle onderstaande stappen kunnen worden uitgevoerd met behulp van de code die u [hier](https://github.com/Azure/Purview-Samples/blob/master/TSQL-Code-Permissions/grant-access-to-on-prem-sql-databases.sql) opgeeft

1. Navigeer naar SQL Server Management Studio (SSMS), maak verbinding met de server, navigeer naar beveiliging, klik met de rechter muisknop op aanmelden en maak nieuwe aanmelding. Zorg ervoor dat u SQL-verificatie selecteert.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Nieuwe aanmelding en gebruiker maken.":::

2. Selecteer Server functies in de linkernavigatiebalk en zorg ervoor dat de open bare rol is toegewezen.

3. Selecteer in het navigatie venster links de optie gebruikers toewijzing, selecteer alle data bases in de kaart en selecteer de databaserol: **db_datareader**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="gebruikers toewijzing.":::

4. Klik op OK om op te slaan.

5. Ga opnieuw naar de gebruiker die u hebt gemaakt, door met de rechter muisknop te klikken en **Eigenschappen** te selecteren. Voer een nieuw wacht woord in en bevestig dit. Selecteer oud wacht woord opgeven en voer het oude wacht woord in. **U moet uw wacht woord wijzigen zodra u een nieuwe aanmelding maakt.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="wacht woord wijzigen.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Uw SQL-aanmeldings wachtwoord opslaan in een sleutel kluis en een referentie maken in controle sfeer liggen

1. Navigeer naar uw sleutel kluis in de Azure-portal1. Selecteer **Instellingen > Geheimen**
1. Selecteer **+ genereren/importeren** en voer de **naam** en **waarde** in als het *wacht woord* van uw SQL Server-aanmelding
1. Selecteer **Maken** om te voltooien
1. Als uw sleutelkluis nog niet is verbonden met Purview, moet u [een nieuwe sleutelkluisverbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak tot slot [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met behulp van de **gebruikersnaam** en het **wachtwoord** om uw scan in te stellen

## <a name="register-a-sql-server-data-source"></a>Een SQL Server-gegevens bron registreren

1. Ga naar uw Purview-account

1. Selecteer bij bronnen en scannen in het navigatie venster links de optie **Integration Runtimes**. Zorg ervoor dat een zelf-hostende Integration Runtime is ingesteld. Als deze niet is ingesteld, volgt u de stappen die [hier](manage-integration-runtimes.md) worden beschreven om een zelf-hostende Integration runtime te maken voor het scannen op een on-premises of Azure VM die toegang heeft tot uw on-premises netwerk.

1. Selecteer **Bronnen** in het linkernavigatievenster

1. Selecteer **Registreren**

1. Selecteer **SQL Server** en **Ga door**

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Stel de SQL-gegevens bron in.":::

5. Geef een beschrijvende naam en server eindpunt op en selecteer vervolgens **volt ooien** om de gegevens bron te registreren. Als uw SQL Server-FQDN bijvoorbeeld **foobar.database.Windows.net** is, voert u *Foobar* in als het server-eind punt.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure Purview-gegevenscatalogus](how-to-browse-catalog.md)
- [Zoeken in de Azure Purview-gegevenscatalogus](how-to-search-catalog.md)
