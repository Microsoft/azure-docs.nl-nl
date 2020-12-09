---
title: Een on-premises SQL server registreren en scannen
description: In deze zelf studie wordt beschreven hoe u on-premises SQL Server scant met een zelf-hostende IR.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: 9003366ec0d64057ca7426d5b6b99986bc21fc9d
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920293"
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

Azure controle sfeer liggen biedt geen ondersteuning voor het scannen van [weer gaven](https://docs.microsoft.com/sql/relational-databases/views/views?view=sql-server-ver15) in SQL Server. 

## <a name="prerequisites"></a>Vereisten

- Voordat u gegevens bronnen registreert, maakt u een Azure controle sfeer liggen-account. Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)voor meer informatie over het maken van een controle sfeer liggen-account.

- Stel een [zelf-hostende Integration runtime](manage-integration-runtimes.md) in om de gegevens bron te scannen.

## <a name="setting-up-authentication-for-a-scan"></a>Verificatie instellen voor een scan

Er is slechts één manier om verificatie voor SQL Server on-premises in te stellen:

- SQL-verificatie

### <a name="sql-authentication"></a>SQL-verificatie

De SQL-identiteit moet toegang hebben tot de primaire data base. Deze locatie is waar `sys.databases` opgeslagen. De controle sfeer liggen-scanner moet worden geïnventariseerd `sys.databases` om alle SQL data base-exemplaren op de server te kunnen vinden.

#### <a name="using-an-existing-server-administrator"></a>Een bestaande server beheerder gebruiken

Als u van plan bent een bestaande sa-gebruiker (Server beheerder) te gebruiken om uw on-premises SQL-Server te scannen, controleert u het volgende:

1. `sa` is geen Windows-verificatie type.

2. De gebruiker op server niveau die u wilt gebruiken, moet beschikken over Server rollen van openbaar en sysadmin. U kunt dit controleren door te navigeren naar SQL Server Management Studio (SSMS), verbinding te maken met de server, te navigeren naar beveiliging, de aanmeldings gegevens te selecteren die u wilt gebruiken, met de rechter muisknop op **Eigenschappen** en vervolgens **Server functies** te selecteren.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Aanmelden op server niveau.":::

3. De data bases worden toegewezen aan een gebruiker die ten minste db_datareader niveau toegang heeft voor elke Data Base.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping-sa.png" alt-text="gebruikers toewijzing voor SA.":::

#### <a name="creating-a-new-login-and-user"></a>Een nieuwe aanmelding en gebruiker maken

Als u een nieuwe aanmelding en gebruiker wilt maken om uw SQL-Server te kunnen scannen, volgt u de onderstaande stappen:

1. Navigeer naar SQL Server Management Studio (SSMS), maak verbinding met de server, navigeer naar beveiliging, klik met de rechter muisknop op aanmelden en maak nieuwe aanmelding. Zorg ervoor dat u SQL-verificatie selecteert.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Nieuwe aanmelding en gebruiker maken.":::

2. Selecteer Server functies in het linkernavigatievenster en selecteer zowel openbaar als sysadmin.

3. Selecteer gebruikers toewijzing in het linkernavigatievenster en selecteer alle data bases in de kaart.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="gebruikers toewijzing.":::

4. Klik op OK om op te slaan.

5. Ga opnieuw naar de gebruiker die u hebt gemaakt, door met de rechter muisknop te klikken en **Eigenschappen** te selecteren. Voer een nieuw wacht woord in en bevestig dit. Selecteer oud wacht woord opgeven en voer het oude wacht woord in. **U moet uw wacht woord wijzigen zodra u een nieuwe aanmelding maakt.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="wacht woord wijzigen.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Uw SQL-aanmeldings wachtwoord opslaan in een sleutel kluis en een referentie maken in controle sfeer liggen

1. Navigeer naar uw sleutel kluis in de Azure Portal
1. **Instellingen > geheimen** selecteren
1. Selecteer **+ genereren/importeren** en voer de **naam** en **waarde** in als het *wacht woord* van uw SQL Server-aanmelding
1. Selecteer **maken** om te volt ooien
1. Als uw sleutel kluis nog niet is verbonden met controle sfeer liggen, moet u [een nieuwe sleutel kluis verbinding maken](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Maak ten slotte [een nieuwe referentie](manage-credentials.md#create-a-new-credential) met de **gebruikers naam** en het **wacht woord** om uw scan in te stellen

## <a name="register-a-sql-server-data-source"></a>Een SQL Server-gegevens bron registreren

1. Navigeer naar uw controle sfeer liggen-account

1. Selecteer bij bronnen en scannen in het navigatie venster links de optie **Integration Runtimes**. Zorg ervoor dat een zelf-hostende Integration runtime is ingesteld. Als deze niet is ingesteld, volgt u de stappen die [hier](manage-integration-runtimes.md) worden beschreven om een zelf-hostende Integration runtime te maken voor het scannen op een on-premises of Azure VM die toegang heeft tot uw on-premises netwerk.

1. **Bronnen** selecteren in de linkernavigatiebalk

1. Selecteer **Registreren**

1. Selecteer **SQL Server** en **Ga door**

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Stel de SQL-gegevens bron in.":::

5. Geef een beschrijvende naam en server eindpunt op en selecteer vervolgens **volt ooien** om de gegevens bron te registreren. Als uw SQL Server-FQDN bijvoorbeeld **foobar.database.Windows.net** is, voert u *Foobar* in als het server-eind punt.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Volgende stappen

- [Bladeren door de Azure controle sfeer liggen Data Catalog](how-to-browse-catalog.md)
- [Zoek in de Azure controle sfeer liggen-Data Catalog](how-to-search-catalog.md)
