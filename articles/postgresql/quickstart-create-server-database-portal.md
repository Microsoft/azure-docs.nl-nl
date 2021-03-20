---
title: 'Quickstart: Server maken - Azure-portal - Azure Database for PostgreSQL - enkele server'
description: In deze quickstart gaat u een Azure Database for PostgreSQL-server maken en beheren met behulp van de Azure-portal.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 000ab3e3911c65554622a48d34abda79d60411df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492383"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Quickstart: Een Azure Database for PostgreSQL-server maken met behulp van de Azure-portal

Azure Database for PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. Deze quickstart laat zien hoe u een individuele Azure Database for PostgreSQL-server maakt, en verbinding met deze server maakt.

## <a name="prerequisites"></a>Vereisten
Een Azure-abonnement is vereist. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken
Ga naar [Azure Portal](https://portal.azure.com/) om een enkele Azure Database for PostgreSQL-serverdatabase te maken. Zoek en selecteer *Azure Database for PostgreSQL-servers.*

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="Zoek Azure Database for PostgreSQL.":::

1. Selecteer **Toevoegen**.

2. Selecteer op de pagina Een Azure Database for PostgreSQL maken de optie  **Enkele server**.

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="Enkele server selecteren":::

3. Vul nu het formulier **Basisbeginselen** in met de volgende gegevens.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Schermopname waarin het tabblad Basisbeginselen voor het maken van een enkele server wordt weergegeven.":::

   |Instelling|Voorgestelde waarde|Beschrijving|
   |:---|:---|:---|
   |Abonnement|De naam van uw abonnement|Selecteer het gewenste Azure-abonnement.|
   |Resourcegroep|*myresourcegroup*| Een nieuwe of bestaande resourcegroep uit uw abonnement.|
   |Servernaam |*mydemoserver*|Een unieke naam ter identificatie van uw Azure Database for PostgreSQL-server. De domeinnaam *postgres.database.azure.com* wordt toegevoegd aan de servernaam die u opgeeft. De server mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. De naam moet 3 tot 63 tekens bevatten.|
   |Gegevensbron | Geen | Selecteer **Geen** om een nieuwe server te maken. Selecteer **Back-up** alleen als u herstelt vanuit een back-up voor geografische gebieden van een bestaande server.|
   |Gebruikersnaam van beheerder |*myadmin*| Voer de gebruikersnaam van de serverbeheerder in. Deze naam mag niet beginnen met **pg_** , en de volgende waarden zijn niet toegestaan: **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest** of **public**.|
   |Wachtwoord |Uw wachtwoord| Een nieuw wachtwoord voor de gebruikersnaam van de serverbeheerder. Het moet 8 tot 128 tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0 t/m 9) en niet-alfanumerieke tekens (bijvoorbeeld !, $, #, %).|
   |Locatie|de gewenste locatie| Selecteer een locatie in de vervolgkeuzelijst.|
   |Versie|De meest recente primaire versie| De nieuwste primaire versie van PostgreSQL, tenzij u specifieke andere vereisten hebt.|
   |Compute en opslag | *standaardwaarden gebruiken*| De standaardprijscategorie is **Algemeen gebruik** met **4 vCores** en **100 GB** opslag. Het bewaren van back-ups is ingesteld op **7 dagen** met de optie **Geografisch redundante** back-up.<br/>Lees meer over de [prijzen](https://azure.microsoft.com/pricing/details/postgresql/server/) en werk, indien nodig, de standaardinstellingen bij.|


   > [!NOTE]
   > Overweeg het gebruik van de prijscategorie Basic als lichte reken- en I/O-capaciteit voldoende is voor uw workload. Servers die zijn gemaakt in de prijscategorie Basic kunnen later niet meer worden geschaald voor Algemeen gebruik of Geoptimaliseerd voor geheugen.

5. Selecteer **Beoordelen en maken** om uw selecties te controleren. Selecteer **Maken** om de server in te richten. Deze bewerking kan enkele minuten duren.
    > [!NOTE]
    > Er wordt een lege database, **postgres**, gemaakt. Er is ook een database **azure_maintenance** die wordt gebruikt om de processen van de beheerde service van gebruikersacties te onderscheiden. U kunt de database **azure_maintenance** niet openen.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="geslaagde implementatie.":::

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>Een firewallregel configureren
De server die u maakt, is standaard niet openbaar toegankelijk. U moet toegang tot uw IP-adres verlenen. Ga in de Azure-portal naar uw serverresource en selecteer **Verbindingsbeveiliging** in het linkermenu van uw serverresource. Zie [Resources openen](../azure-resource-manager/management/manage-resources-portal.md#open-resources) als u niet weet hoe u uw resource moet vinden.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Schermopname waarin firewallregels voor verbindingsbeveiliging worden weergegeven.":::

Selecteer **Huidig IP-adres van client toevoegen** en selecteer vervolgens **Opslaan**. U kunt meer IP-adressen toevoegen of een IP-bereik opgeven om verbinding met uw server te maken vanaf die IP-adressen. Zie [Firewallregels in Azure Database for PostgreSQL](./concepts-firewall-rules.md) voor meer informatie.

> [!NOTE]
> Om connectiviteitsproblemen te vermijden, controleert u of uw netwerk uitgaand verkeer via poort 5432 toestaat. Azure Database for PostgreSQL gebruikt die poort.

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>Verbinding maken met de server met behulp van psql

U kunt [psql](http://postgresguide.com/utilities/psql.html) of [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html) (populaire PostgreSQL-clients) gebruiken. In deze quickstart maakt u verbinding met behulp van psql in [Azure Cloud Shell](../cloud-shell/overview.md) binnen de Azure-portal.

1. Noteer uw servernaam, aanmeldings-id voor de serverbeheerder, wachtwoord en abonnements-id voor de nieuwe server in de sectie **Overzicht** van de server.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="verbindingsgegevens ophalen.":::


2. Open Azure Cloud Shell in de portal door het pictogram in de linkerbovenhoek te selecteren.

   > [!NOTE]
   > Als u Cloud Shell voor de eerste keer opent, wordt er een prompt weergegeven om een resourcegroep en opslagaccount te maken. Dit is een eenmalige stap en ze worden voor alle volgende sessies automatisch toegevoegd.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Schermopname waarin servergegevens en het pictogram voor het openen van Azure Cloud Shell worden weergegeven.":::

3. Voer de volgende opdracht uit in de Azure Cloud Shell-terminal. Vervang de waarden door uw servernaam en aanmeldings-id voor de gebruiker met beheerdersrechten. Gebruik de lege database **postgres** met een gebruiker met beheerdersrechten in deze indeling: `<admin-username>@<servername>`.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   Zo ziet het proces eruit in de Cloud Shell-terminal:

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. Maak in dezelfde Azure Cloud Shell-terminal een database met de naam **guest**.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Schakel de verbindingen over naar de nieuwe **guest**-database.

   ```bash
   \c guest
   ```
6. Typ `\q` en druk vervolgens op Enter om psql te sluiten.

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Resources opschonen
U hebt een Azure Database for PostgreSQL-server in een resourcegroep gemaakt. Als u deze resources in de toekomst waarschijnlijk niet nodig hebt, kunt u ze verwijderen door de resourcegroep of de PostgreSQL-server te verwijderen.

Zo verwijdert u de resourcegroep:

1. Zoek en selecteer **Resourcegroepen** in de Azure-portal.
2. Kies in de lijst met resourcegroepen de naam van uw resourcegroep.
3. Selecteer op de pagina **Overzicht** van uw resourcegroep de optie **Resourcegroep verwijderen**.
4. Typ de naam van de resourcegroep in het bevestigingsvenster en selecteer **Verwijderen**.

Om de server te verwijderen, selecteert u de knop **Verwijderen** op de pagina **Overzicht** van uw server:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Schermopname waarin de knop voor het verwijderen van een server wordt weergegeven.":::

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een database migreren met behulp van exporteren en importeren](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [Een database ontwerpen](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[Kunt u niet vinden wat u zoekt? Laat het ons weten.](https://aka.ms/postgres-doc-feedback)