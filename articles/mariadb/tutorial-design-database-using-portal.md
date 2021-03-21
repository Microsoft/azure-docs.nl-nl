---
title: 'Zelfstudie: Een Azure Database for MariaDB ontwerpen - Azure Portal'
description: In deze zelfstudie wordt uitgelegd hoe u een Azure Database for MariaDB-server en -database maakt en beheert via de Azure-portal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: tutorial
ms.date: 3/18/2020
ms.custom: mvc
ms.openlocfilehash: fe503d0e579e30b0c355d3be3e8d02b2bd7a44a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664466"
---
# <a name="tutorial-design-an-azure-database-for-mariadb-database-by-using-the-azure-portal"></a>Zelfstudie: een Azure Database for MariaDB-database ontwerpen met de Azure-portal

Azure Database for MariaDB is een beheerde service waarmee u MySQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. Met behulp van de Azure-portal kunt u eenvoudig uw server beheren en een database ontwerpen.

In deze zelfstudie leert u hoe u Azure Portal kunt gebruiken voor deze bewerkingen:

> [!div class="checklist"]
> * Een Azure Database for MariaDB maken
> * De serverfirewall configureren
> * Het opdrachtregelprogramma mysql gebruiken om een database te maken
> * Voorbeeldgegevens laden
> * Querygegevens
> * Gegevens bijwerken
> * Gegevens terugzetten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Ga in een browser naar de [Azure-portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="create-an-azure-database-for-mariadb-server"></a>Een Azure Database for MariaDB-server maken

U maakt een Azure Database for MariaDB-server met een gedefinieerde set [reken- en opslagresources](concepts-pricing-tiers.md). De server wordt gemaakt in een [Azure-resourcegroep](../azure-resource-manager/management/overview.md).

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal.

2. Selecteer **Databases** > **Azure Database for MariaDB**. U kunt ook **MariaDB** in het zoekvak typen om de service te vinden.

   ![MySQL openen](./media/tutorial-design-database-using-portal/1-Navigate-to-mariadb.png)

3. Selecteer de tegel **Azure Database for MariaDB**. Voer de vereiste informatie in of selecteer deze.

   ![Formulier Maken](./media/tutorial-design-database-using-portal/2-create-form.png)

    Instelling | Voorgestelde waarde | Beschrijving van veld
    ---|---|---
    Servernaam | *een unieke servernaam* | Kies een unieke naam ter identificatie van de Azure Database for MariaDB-server. Bijvoorbeeld **mydemoserver**. De domeinnaam *.mariadb.database.azure.com* wordt toegevoegd aan de servernaam die u opgeeft. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. Dit wachtwoord moet tussen 3 en 63 tekens bevatten.
    Abonnement | *uw abonnement* | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw server. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep | **myresourcegroup** | Voer een nieuwe resourcegroepnaam in of selecteer een bestaande resourcegroep.
    Bron selecteren | **Leeg** | Selecteer **Leeg** om een nieuwe server te maken. (Selecteer **Back-up** als u een server maakt op basis van een geo-back-up van een bestaande Azure Database for MariaDB-server.)
    Aanmeldgegevens van serverbeheerder | **myadmin** | Een aanmeldingsaccount dat moet worden gebruikt om verbinding te maken met de server. De aanmeldingsnaam voor de beheerder kan niet **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** zijn.
    Wachtwoord | *uw keuze* | Voer een nieuw wachtwoord in voor het beheerdersaccount voor de server. Dit wachtwoord moet tussen 8 en 128 tekens bevatten. Uw wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0-9) en niet-alfanumerieke tekens (!, $, #, %, enzovoort).
    Wachtwoord bevestigen | *uw keuze*| Bevestig het wachtwoord voor het beheerdersaccount.
    Locatie | *de regio het dichtst bij uw gebruikers*| Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt.
    Versie | *de nieuwste versie*| De nieuwste versie (tenzij u specifieke vereisten hebt voor het gebruik van een andere versie).
    Prijscategorie | Zie de beschrijving. | De reken-, opslag- en back-upconfiguraties voor de nieuwe server. Selecteer **Prijscategorie** > **Algemeen gebruik**. Behoud de standaardwaarden voor de volgende instellingen:<br><ul><li>**Bewerking voor compute** (Gen 5)</li><li>**vCore** (4 vCores)</li><li>**Opslag** (100 GB)</li><li>**Bewaarperiode voor back-ups** (7 dagen)</li></ul><br>Als u serverback-ups in geografisch redundante opslag wilt inschakelen, selecteert u **Geografisch redundant** in het gedeelte **Redundantieopties voor back-up**. <br><br>Selecteer **OK** om deze geselecteerde prijscategorie op te slaan. Deze selecties worden afgebeeld in de volgende schermopname.
    
   ![Prijscategorie](./media/tutorial-design-database-using-portal/3-pricing-tier.png)

   > [!TIP]
   > Als **automatische groei** voor uw server is ingeschakeld, hebt u meer opslagruimte als u de toewijzingslimiet nadert. Dit is niet van invloed op de workload.

4. Klik op **Controleren + maken**. Klik op de knop **Meldingen** op de werkbalk om het implementatieproces te volgen. De implementatie kan twintig minuten duren.

## <a name="configure-the-firewall"></a>De firewall configureren

Azure Database for MariaDB wordt beveiligd door een firewall. Standaard worden alle verbindingen met de server en de databases op de server geweigerd. Voordat u voor het eerst verbinding maakt met Azure Database for MariaDB moet u de firewall configureren en het openbare netwerk-IP-adres van de clientcomputer (of het IP-adresbereik) toevoegen.

1. Klik op uw zojuist gemaakte server en klik vervolgens op **Verbindingsbeveiliging**.

   ![Verbindingsbeveiliging](./media/tutorial-design-database-using-portal/1-Connection-security.png)
2. U kunt **Mijn IP toevoegen** kiezen of hier firewallregels configureren. Vergeet niet om **Opslaan** te selecteren nadat u regels hebt gemaakt.

U kunt nu verbinding maken met de server met behulp van het opdrachtregelprogramma mysql of met MySQL Workbench.

> [!TIP]
> Azure Database for MariaDB-servers communiceren via poort 3306. Als u verbinding wilt maken vanuit een bedrijfsnetwerk, is uitgaand verkeer via poort 3306 mogelijk niet toegestaan vanwege de firewall van het netwerk. In dat geval kunt u geen verbinding maken met uw Azure Database for MariaDB-server, tenzij de IT-afdeling poort 3306 openstelt.

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

Ga naar de Azure-portal om de volledig gekwalificeerde **servernaam** en de **aanmeldingsnaam van de serverbeheerder** op te halen voor uw Azure Database for MariaDB-server. U gebuikt de volledig gekwalificeerde servernaam om verbinding met uw server te maken via het opdrachtregelprogramma mysql.

1. Selecteer in het meest linkse menu van de [Azure-portal](https://portal.azure.com/) de optie **Alle resources**. Voer de servernaam in en zoek naar uw Azure Database for MariaDB-server. Selecteer de naam van de server om de serverdetails te bekijken.

2. Op de pagina **Overzicht** bekijkt u de waarden bij **Servernaam** en **Aanmeldingsnaam van de serverbeheerder** en noteert u deze. U kunt ook op de knop **Kopiëren** naast elk veld klikken om de gegevens naar het klembord te kopiëren.

   ![Servereigenschappen](./media/tutorial-design-database-using-portal/2-server-properties.png)

In dit voorbeeld is de servernaam **mydemoserver.mariadb.database.azure.com**. De aanmeldingsnaam van de serverbeheerder is **myadmin\@mydemoserver**.

## <a name="connect-to-the-server-by-using-mysql"></a>Verbinding maken met de server met behulp van mysql

Gebruik het [opdrachtregelprogramma mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) om een verbinding tot stand te brengen met uw Azure Database for MariaDB-server. U kunt het opdrachtregelprogramma mysql uitvoeren in de browser met behulp van Azure Cloud Shell of het opdrachtregelprogramma starten vanaf uw eigen computer met behulp van de lokaal geïnstalleerde mysql-hulpprogramma's. Als u de Azure Cloud Shell wilt openen, klikt u op de knop **Uitproberen** in een codeblok in dit artikel of gaat u naar de Azure-portal en klikt u op het pictogram **>_** in de bovenste werkbalk rechts.

Typ de opdracht om verbinding te maken:

```bash
mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Een lege database maken

Zodra u met de server bent verbonden, maakt u een lege database om mee te werken:

```sql
CREATE DATABASE mysampledb;
```

Voer in het prompt de volgende opdracht uit om verbinding te maken met de zojuist gemaakte database:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Tabellen maken in de database

U weet nu hoe u verbinding kunt maken met de Azure Database for MariaDB-database en dus is het tijd om enkele eenvoudige taken uit te voeren.

We gaan eerst een tabel maken en hierin enkele gegevens laden. Laten we een tabel maken waarin voorraadgegevens worden opgeslagen:

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-in-the-tables"></a>Gegevens laden in de tabellen

De volgende stap bestaat uit het toevoegen van gegevens aan de tabel. Voer in het opdrachtprompt de volgende query uit om enkele rijen met gegevens in te voegen:

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

## <a name="query-and-update-the-data-in-the-tables"></a>De gegevens in de tabellen opvragen en bijwerken

Voer de volgende query uit om gegevens op te halen uit de databasetabel:

```sql
SELECT * FROM inventory;
```

U kunt ook de gegevens in de tabellen bijwerken:

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

De rij wordt bijgewerkt wanneer u gegevens ophaalt:

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Een database herstellen naar een eerder tijdstip

Stel dat u per ongeluk een belangrijke databasetabel hebt verwijderd en dat de gegevens niet gemakkelijk kunnen worden hersteld. Azure Database for MariaDB biedt de mogelijkheid om de server te herstellen naar een eerder tijdstip, door het maken van een kopie van de databases op de nieuwe server. U kunt deze nieuwe server dan gebruiken om de verwijderde gegevens te herstellen. Met de volgende stappen wordt de voorbeeldserver hersteld naar een tijdstip waarop de tabel nog niet was toegevoegd:

1. Zoek uw Azure Database voor MariaDB-server in Azure Portal. Selecteer op de pagina **Overzicht** de optie **Herstellen**.

   ![Een database herstellen](./media/tutorial-design-database-using-portal/1-restore-a-db.png)

2. Op de pagina **Herstellen** voert u de volgende informatie in of selecteert u deze:

   ![Formulier herstellen](./media/tutorial-design-database-using-portal/2-restore-form.png)
   
   - **Herstelpunt**: selecteer een tijdstip in het verleden waarnaar u wilt herstellen, binnen de vermelde periode. Zorg ervoor dat u de lokale tijdzone converteert naar UTC.
   - **Herstellen naar nieuwe server**: geef de naam op van de nieuwe server waarnaar u wilt herstellen.
   - **Locatie**: de regio is hetzelfde als die van de bronserver en kan niet worden gewijzigd.
   - **Prijscategorie**: de prijscategorie is hetzelfde als die van de bronserver en kan niet worden gewijzigd.
   
3. Klik op **OK** om de server te [herstellen naar een eerder tijdstip](./howto-restore-server-portal.md), voordat de tabel werd verwijderd. Als u een server herstelt, wordt er een nieuwe kopie van de server gemaakt, op het tijdstip dat u selecteert. 

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u Azure Portal gebruikt om het volgende te leren:

> [!div class="checklist"]
> * Een Azure Database for MariaDB maken
> * De serverfirewall configureren
> * Het opdrachtregelprogramma mysql gebruiken om een database te maken
> * Voorbeeldgegevens laden
> * Querygegevens
> * Gegevens bijwerken
> * Gegevens terugzetten

> [!div class="nextstepaction"]
> [Toepassingen verbinden met Azure Database for MariaDB](howto-connection-string.md)
