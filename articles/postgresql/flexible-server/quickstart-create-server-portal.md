---
title: 'Quickstart: Server maken - Azure-portal - Azure Database for PostgreSQL - Flexible Server'
description: Quickstart voor het maken en beheren van een enkele Azure Database for PostgreSQL - Flexible Server via de Azure Portal-gebruikersinterface.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 6d10298b016cbcf362af2d272f9fa822db6e569d
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604750"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>Quickstart: Een Azure Database for PostgreSQL - Flexible Server maken in de Azure-portal

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server is als preview-versie beschikbaar

Azure Database for PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze snelstart ontdekt u hoe u in ongeveer vijf minuten een Azure Database for PostgreSQL - Flexible Server maakt met behulp van de Azure-portal.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open uw webbrowser en ga naar de [portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="create-an-azure-database-for-postgresql-server"></a>Een Azure-database voor PostgreSQL-server maken

Een Azure Database for PostgreSQL-server wordt gemaakt met een geconfigureerde set [reken- en opslagresources](./concepts-compute-storage.md). De server wordt gemaakt in een [Azure-resourcegroep](../../azure-resource-manager/management/overview.md).

Volg deze stappen voor het maken van een Azure Database for PostgreSQL-server:

1. Selecteer in de linkerbovenhoek van de portal **Een resource maken** (+).

2. Selecteer **Databases** > **Azure Database for PostgreSQL**.

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Azure Database for PostgreSQL in het menu":::

3. Selecteer de implementatieoptie **Flexible Server**.

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="Implementatieoptie Azure Database for PostgreSQL - Flexible Server selecteren":::

4. Vul het formulier **Basisbeginselen** in met de volgende gegevens:

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="Een server maken":::

    Instelling|Voorgestelde waarde|Beschrijving
    ---|---|---
    Abonnement|De naam van uw abonnement|Het Azure-abonnement dat u wilt gebruiken voor uw server. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wilt worden gefactureerd voor de resource.
    Resourcegroep|*myresourcegroup*| Een nieuwe resourcegroepnaam of een bestaande naam uit uw abonnement.
    Servernaam |*mydemoserver*|Een unieke naam ter identificatie van uw Azure Database for PostgreSQL-server. De domeinnaam *postgres.database.azure.com* wordt toegevoegd aan de naam van de server die u opgeeft. De server mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. Hij moet ten minste 3 en mag maximaal 63 tekens bevatten.
    Gebruikersnaam van beheerder |*myadmin*| Uw eigen aanmeldingsaccount dat moet worden gebruikt om verbinding te maken met de server. De aanmeldingsnaam voor de beheerder mag niet **azure_superuser,** **azure_pg_admin,** **admin,** **administrator,** **root,** **guest** of **public** zijn. De naam mag niet met **pg_** beginnen.
    Wachtwoord |Uw wachtwoord| Een nieuw wachtwoord voor het beheerdersaccount voor de server. Dit wachtwoord moet tussen 8 en 128 tekens bevatten. Uw wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0 tot en met 9) en niet-alfanumerieke tekens (!, $, #, %, enzovoort).
    Locatie|De regio het dichtst bij uw gebruikers| De locatie die zich het dichtst bij uw gebruikers bevindt.
    Versie|De meest recente primaire versie| De nieuwste primaire versie van PostgreSQL, tenzij u specifieke andere vereisten hebt.
    Compute en opslag | **Algemeen**, **4 vCores**, **512 GB**, **7 dagen** | De reken-, opslag- en back-upconfiguraties voor de nieuwe server. Selecteer **Server configureren**. *Algemeen*, *4 vCores*, *512 GB* en *7 dagen* zijn de standaardwaarden voor **Compute-laag**, **vCore**, **Opslag** en **Bewaartermijn voor back-ups**. U kunt deze schuifregelaars laten zoals ze zijn of ze aanpassen. Selecteer **OK** om deze geselecteerde prijscategorie op te slaan. Deze selecties worden afgebeeld in de volgende schermopname.

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="Het deelvenster Prijscategorie":::
    
5. Netwerkopties configureren

    Op het Netwerk-tabblad kunt u kiezen hoe de server bereikbaar is. In Azure Database for PostgreSQL wordt een firewall op serverniveau gemaakt. De firewall voorkomt dat externe toepassingen en hulpprogramma's verbinding maken met de server of databases op de server, tenzij u een firewallregel maakt om de firewall te openen voor specifieke IP-adressen. Wij raden u aan de server openbaar toegankelijk te maken:

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="Het deelvenster Netwerken":::

    En deze vervolgens beperken tot uw eigen client-IP-adres:

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Selecteer Huidig IP-adres van client toevoegen":::

6. Selecteer **Beoordelen en maken** om uw selecties te controleren. Selecteer **Maken** om de server in te richten. Deze bewerking kan enkele minuten duren.

7. Selecteer het pictogram **Meldingen** (een klok) op de werkbalk om het implementatieproces te bewaken. Na de implementatie kunt u **Vastmaken aan dashboard** selecteren. Hiermee maakt u een tegel voor deze server op uw dashboard in Azure Portal als snelkoppeling naar de **overzichtspagina** van de server. Als u **Naar de resource gaan** selecteert, wordt de **overzichtspagina** van de server weergegeven.

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="Het deelvenster Meldingen":::

   Standaard word een **postgres**-database op uw server gemaakt. De database [postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) is een standaarddatabase die kan worden gebruikt door gebruikers, hulpprogramma's en toepassingen van derden. (De andere standaarddatabase is **azure_maintenance**. De functie van de database is het scheiden van beheerde serviceprocessen van acties van de gebruiker. U hebt geen toegang tot deze database.)

    > [!NOTE]
    > Verbindingen met uw Azure Database voor PostgreSQL-server communiceren via poort 5432. Als u verbinding wilt maken vanuit een bedrijfsnetwerk, is uitgaand verkeer via poort 5432 mogelijk niet toegestaan vanwege de firewall van het netwerk. In dat geval kunt u alleen verbinding maken met uw server als uw IT-afdeling poort 5432 openstelt.
    >

## <a name="get-the-connection-information"></a>De verbindingsgegevens ophalen

Wanneer u de Azure Database for PostgreSQL-server maakt, wordt een standaarddatabase met de naam **postgres** gemaakt. Voor verbinding met uw databaseserver, hebt u de volledige servernaam en aanmeldingsreferenties van de beheerder nodig. U hebt deze waarden mogelijk al eerder in dit artikel genoteerd. Als u dat niet hebt gedaan, kunt u de servernaam en aanmeldingsgegevens gemakkelijk terugvinden op de **overzichtspagina** voor de server in de portal.

Open de pagina **Overzicht** van de server. Noteer de **servernaam** en de **gebruikersnaam van de serverbeheerder**. Beweeg de cursor over elk veld. Het kopieersymbool wordt rechts van de tekst weergegeven. Selecteer het kopieerpictogram indien nodig om de waarden te kopiëren.

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="De overzichtspagina van de server":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>Verbinding maken met de PostgreSQL-database met behulp van psql

Er zijn een aantal toepassingen die kunt gebruiken om verbinding te maken met uw Azure Database voor PostgreSQL-server. Als op uw clientcomputer PostgreSQL is geïnstalleerd, kunt u een lokale instantie van [psql](https://www.postgresql.org/docs/current/static/app-psql.html) gebruiken om verbinding te maken met een Azure PostgreSQL-server. We gaan nu het opdrachtregelprogramma psql gebruiken om verbinding te maken met de Azure PostgreSQL-server.

1. Voer de volgende psql-opdracht uit om verbinding te maken met een Azure-database voor PostgreSQL-server

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Met de volgende opdracht maakt u bijvoorbeeld verbinding met de standaarddatabase **postgres** op uw PostgreSQL-server **mydemoserver.postgres.database.azure.com** met behulp van toegangsreferenties. Voer het `<server_admin_password>` in dat u koos toen u werd gevraagd om een wachtwoord.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Als de verbinding tot stand is gebracht, wordt er door het hulpprogramma psql een postgres-prompt weergegeven. Hier kunt u sql-opdrachten typen. In de uitvoer van de eerste verbinding kan een waarschuwing worden weergegeven, omdat de psql die u gebruikt mogelijk niet dezelfde versie heeft als de Azure Database for PostgreSQL-server.

   Voorbeeld van psql-uitvoer:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Als de firewall niet is geconfigureerd voor het toestaan van het IP-adres van uw client, wordt het volgende foutbericht weergegeven:
   >
   > 'psql: FATAL:  no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Please specify SSL options and retry.
   >
   > Controleer of het IP-adres van de client is toegestaan in de bovenstaande stap voor firewallregels.

2. Maak een lege database met de naam 'mypgsqldb' door bij de prompt de volgende opdracht te typen:

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. In de prompt voert u de volgende opdracht uit om verbinding te maken met de zojuist gemaakte database **mypgsqldb**:

    ```bash
    \c mypgsqldb
    ```

4. Typ `\q` en druk vervolgens op Enter om psql af te sluiten.

U hebt nu verbinding gemaakt met de Azure Database for PostgreSQL-server via psql en u hebt een lege gebruikersdatabase gemaakt.

## <a name="clean-up-resources"></a>Resources opschonen

De resources die u hebt gemaakt in de Quick Start kunt u op een van de volgende twee manieren opschonen. U kunt de Azure-resourcegroep verwijderen, met alle resources uit de resourcegroep. Als u de andere resources intact wilt houden, verwijdert u alleen de serverresource.

> [!TIP]
> Andere Quick Starts in deze verzameling zijn op deze Quick Start gebaseerd. Als u van plan bent om ook deze Quick Starts te volgen, moet u geen resources opschonen die u in deze Quick Start hebt gemaakt. Als u niet wilt doorgaan, gebruikt u de volgende stappen om resources te verwijderen die tijdens deze Quick Start in de portal zijn gemaakt.

De hele resourcegroep verwijderen, met inbegrip van de nieuwe server:

1. Zoek de resourcegroep in de portal. Selecteer **Resourcegroepen** in het menu aan de linkerkant. Selecteer vervolgens de naam van de resourcegroep, zoals in het voorbeeld **myresourcegroup**.

2. Selecteer **Verwijderen** op de pagina van de resourcegroep. Voer vervolgens in het tekstvak de naam van de resourcegroep in, zoals **myresourcegroup** uit het voorbeeld, om het verwijderen te bevestigen. Selecteer **Verwijderen**.

Alleen de zojuist gemaakte server verwijderen:

1. Als de server niet is geopend, zoekt u de server in de portal. Selecteer **Alle resources** in het menu aan de linkerkant. Zoek vervolgens naar de server die u hebt gemaakt.

2. Selecteer **Verwijderen** op de pagina **Overzicht**.

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="De knop Verwijderen":::

3. Controleer de naam van de server die u gaat verwijderen en kijk welke databases eveneens worden verwijderd. Voer in het tekstvak de naam van uw server in, zoals **mydemoserver** uit het voorbeeld. Selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Een Django-app implementeren met App Service en PostgreSQL](tutorial-django-app-service-postgres.md)