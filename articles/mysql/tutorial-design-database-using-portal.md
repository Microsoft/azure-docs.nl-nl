---
title: 'Zelfstudie: Een server ontwerpen - Azure Portal - Azure Database for MySQL'
description: In deze zelfstudie wordt uitgelegd hoe u een Azure Database for MySQL-server en -database maakt en beheert via Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: tutorial
ms.date: 3/20/2020
ms.custom: mvc
ms.openlocfilehash: 7559bc2246ca26cf2b14071396e075b28d2af3a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94532678"
---
# <a name="tutorial-design-an-azure-database-for-mysql-database-using-the-azure-portal"></a>Zelfstudie: een Azure Database for MySQL-database ontwerpen met Azure Portal

Azure Database voor MySQL is een beheerde service waarmee u MySQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. Met behulp van Azure Portal kunt u eenvoudig uw server beheren en een database ontwerpen.

In deze zelfstudie leert u hoe u Azure Portal kunt gebruiken voor deze bewerkingen:

> [!div class="checklist"]
> * Een Azure Database voor MySQL maken
> * De serverfirewall configureren
> * Het opdrachtregelprogramma mysql gebruiken om een database te maken
> * Voorbeeldgegevens laden
> * Querygegevens
> * Gegevens bijwerken
> * Gegevens terugzetten

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Open uw favoriete webbrowser en ga naar [Microsoft Azure Portal](https://portal.azure.com/). Voer uw referenties in om u aan te melden bij de portal. De standaardweergave is uw service-dashboard.

## <a name="create-an-azure-database-for-mysql-server"></a>Een Azure-database voor MySQL-server maken

Een Azure Database voor MySQL-server wordt gemaakt met een gedefinieerde set [reken- en opslagresources](./concepts-pricing-tiers.md). De server wordt gemaakt in een [Azure-resourcegroep](../azure-resource-manager/management/overview.md).

1. Selecteer de knop **Een resource maken** (+) in de linkerbovenhoek van de portal.

2. Selecteer **Databases** > **Azure Database voor MySQL**. Als u MySQL Server niet ziet in de categorie **Databases**, klikt u op **Alles weergeven** om alle beschikbare databaseservices weer te geven. U kunt ook **Azure Database voor MySQL** in het zoekvak typen om de service snel te vinden.
   
   :::image type="content" source="./media/tutorial-design-database-using-portal/1-Navigate-to-MySQL.png" alt-text="Navigeren naar MySQL":::

3. Klik op de tegel **Azure Database for MySQL-server**. Vul het Azure Database for MySQL-formulier in.
   
   :::image type="content" source="./media/tutorial-design-database-using-portal/2-create-form.png" alt-text="Formulier Maken":::

    **Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**
    ---|---|---
    Servernaam | Unieke servernaam | Kies een unieke naam ter identificatie van de Azure Database voor MySQL-server. Bijvoorbeeld: mydemoserver. De domeinnaam *mysql.database.azure.com* wordt toegevoegd aan de servernaam die u opgeeft. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. en moet 3 tot 63 tekens lang zijn.
    Abonnement | Uw abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw server. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep | *myresourcegroup* | Geef een nieuwe of bestaande resourcegroepnaam op.
    Bron selecteren | *Leeg* | Selecteer *Leeg* om een nieuwe server te maken. (Selecteer *Back-up* als u een server maakt op basis van een geo-back-up van een bestaande Azure Database for MySQL-server).
    Aanmeldgegevens van serverbeheerder | myadmin | Een aanmeldingsaccount die moet worden gebruikt om verbinding te maken met de server. De aanmeldingsnaam voor de beheerder kan niet **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** zijn.
    Wachtwoord | *Uw keuze* | Geef een nieuw wachtwoord op voor het beheerdersaccount voor de server. Het wachtwoord moet tussen 8 en 128 tekens lang zijn. Uw wachtwoord moet tekens bevatten uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0-9) en niet-alfanumerieke tekens (!, $, #, %, enzovoort).
    Wachtwoord bevestigen | *Uw keuze*| Bevestig het wachtwoord voor het beheerdersaccount.
    Locatie | *De regio het dichtst bij uw gebruikers*| Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt.
    Versie | *De nieuwste versie*| De nieuwste versie (tenzij u specifieke vereisten hebt en een andere versie moet gebruiken).
    Prijscategorie | **Algemeen**, **Gen 5**, **2 vCores**, **5 GB**, **7 dagen**, **Geografisch redundant** | De reken-, opslag- en back-upconfiguraties voor de nieuwe server. Selecteer **Prijscategorie**. Selecteer vervolgens het tabblad **Algemeen**. *Gen 5*, *2 vCores*, *5 GB* en *7 dagen* zijn de standaardwaarden voor **Bewerking voor compute**, **vCore**, **Opslag** en **Bewaarperiode voor back-up**. U kunt deze schuifregelaars laten zoals ze zijn. Als u serverback-ups in geografisch redundante opslag wilt inschakelen, selecteert u **Geografisch redundant** in het gedeelte **Redundantieopties voor back-up**. Selecteer **OK** om deze geselecteerde prijscategorie op te slaan. Deze selecties worden afgebeeld in de volgende schermopname.

   :::image type="content" source="./media/tutorial-design-database-using-portal/3-pricing-tier.png" alt-text="Prijscategorie":::

   > [!TIP]
   > Als **automatische groei** voor uw server is ingeschakeld, hebt u meer opslagruimte als u de toewijzingslimiet nadert. Dit is niet van invloed op de workload.

4. Klik op **Controleren + maken**. Klik op de knop **Meldingen** op de werkbalk om het implementatieproces te volgen. De implementatie kan twintig minuten duren.

## <a name="configure-firewall"></a>Firewall configureren

Databases van Azure Database voor MySQL worden beveiligd door een firewall. Standaard worden alle verbindingen met de server en de databases op de server geweigerd. Voordat u voor het eerst verbinding maakt met Azure Database voor MySQL moet u de firewall configureren en het IP-adres (of IP-adresbereik) van het openbare netwerk van de clientcomputer toevoegen.

1. Klik op uw zojuist gemaakte server en klik vervolgens op **Verbindingsbeveiliging**.

   :::image type="content" source="./media/tutorial-design-database-using-portal/1-Connection-security.png" alt-text="Verbindingsbeveiliging":::
2. U kunt **Mijn IP toevoegen** kiezen of hier firewallregels configureren. Vergeet niet op **Opslaan** te klikken nadat u de regels hebt gemaakt.
U kunt nu verbinding maken met de server met behulp van het opdrachtregelprogramma mysql of met het GUI-hulpprogramma MySQL Workbench.

> [!TIP]
> Azure Database voor MySQL-servers communiceren via poort 3306. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 3306 mogelijk niet toegestaan door de firewall van uw netwerk. In dat geval kunt u geen verbinding maken met uw Azure MySQL-server, tenzij de IT-afdeling poort 3306 openstelt.

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

Ga naar Azure Portal om de volledig gekwalificeerde **servernaam** en **aanmeldingsnaam van de serverbeheerder** op te halen voor uw Azure-Database voor MySQL-server. U gebuikt de volledig gekwalificeerde servernaam om verbinding met uw server te maken via het opdrachtregelprogramma mysql.

1. Klik in [Azure Portal](https://portal.azure.com/) op **Alle resources** in het menu aan de linkerkant, typ de naam en zoek naar uw Azure Database voor MySQL-server. Selecteer de naam van de server om de details te bekijken.

2. Noteer op de pagina **Overzicht** de waarden voor de **servernaam** en de **aanmeldingsgegevens van de serverbeheerder**. U kunt op de knop Kopiëren naast elk veld klikken om de gegevens naar het klembord te kopiëren.
   :::image type="content" source="./media/tutorial-design-database-using-portal/2-server-properties.png" alt-text="4-2 Servereigenschappen":::

In dit voorbeeld is de servernaam *mydemoserver.mysql.database.azure.com* en zijn de aanmeldingsgegevens van de serverbeheerder *myadmin\@mydemoserver*.

## <a name="connect-to-the-server-using-mysql"></a>Verbinding maken met de server met behulp van mysql

Gebruik het [opdrachtregelprogramma mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) om een verbinding tot stand te brengen met uw Azure-database voor MySQL-server. U kunt het opdrachtregelprogramma mysql uitvoeren in de browser met behulp van Azure Cloud Shell of het opdrachtregelprogramma starten vanaf uw eigen computer met behulp van de lokaal geïnstalleerde mysql-hulpprogramma's. Als u de Azure Cloud Shell wilt starten, klikt u op de knop `Try It` in een codeblok in dit artikel of gaat u naar Azure Portal en klikt u op het pictogram `>_` in de bovenste werkbalk rechts.

Typ de opdracht voor verbinding:

```azurecli-interactive
mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>Een lege database maken

Zodra u met de server bent verbonden, maakt u een lege database om mee te werken.

```sql
CREATE DATABASE mysampledb;
```

Voer bij de prompt de volgende opdracht uit om verbinding te maken met de zojuist gemaakte database:

```sql
USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>Tabellen maken in de database

U weet nu hoe u verbinding kunt maken met de Azure Database voor MySQL-database en dus is het tijd om enkele eenvoudige taken uit te voeren:

We gaan eerst een tabel maken en hierin enkele gegevens laden. Laten we een tabel maken waarin voorraadgegevens worden opgeslagen.

```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>Gegevens laden in de tabellen

De volgende stap bestaat uit het toevoegen van gegevens aan de tabel. Voer bij de opdrachtprompt de volgende query uit om enkele rijen met gegevens in te voegen.

```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150);
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

We hebben nu twee rijen met voorbeeldgegevens in de tabel die u eerder hebt gemaakt.

## <a name="query-and-update-the-data-in-the-tables"></a>De gegevens in de tabellen opvragen en bijwerken

Voer de volgende query uit om gegevens op te halen uit de databasetabel.

```sql
SELECT * FROM inventory;
```

U kunt ook de gegevens in de tabellen bijwerken.

```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

De rij wordt dan meteen bijgewerkt wanneer u gegevens ophaalt.

```sql
SELECT * FROM inventory;
```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Een database herstellen naar een eerder tijdstip

Stel dat u per ongeluk een belangrijke databasetabel hebt verwijderd en dat de gegevens niet gemakkelijk kunnen worden hersteld. Azure Database voor MySQL biedt gelukkig de mogelijkheid om de server te herstellen naar een eerder tijdstip, door het maken van een kopie van de databases op de nieuwe server. U kunt deze nieuwe server dan gebruiken om de verwijderde gegevens te herstellen. Met de volgende stappen wordt de voorbeeldserver hersteld naar een punt voordat de tabel is toegevoegd.

1. Zoek uw Azure Database voor MySQL-server in Azure Portal. Klik op de pagina **Overzicht** op **Herstellen** op de werkbalk. De gelijknamige pagina wordt geopend.

   :::image type="content" source="./media/tutorial-design-database-using-portal/1-restore-a-db.png" alt-text="10-1 Een database herstellen":::

2. Vul in het formulier **Herstellen** de vereiste gegevens in.

   :::image type="content" source="./media/tutorial-design-database-using-portal/2-restore-form.png" alt-text="10-2 Formulier Herstellen":::

   - **Herstelpunt**: selecteer een tijdstip in het verleden waarnaar u wilt herstellen, binnen de vermelde periode. Zorg ervoor dat u de lokale tijdzone converteert naar UTC.
   - **Herstellen naar nieuwe server**: geef de naam op van de nieuwe server waarnaar u wilt herstellen.
   - **Locatie**: de regio is hetzelfde als van de bronserver en kan niet worden gewijzigd.
   - **Prijscategorie**: de prijscategorie is hetzelfde als van de bronserver en kan niet worden gewijzigd.
   
3. Klik op **OK** om de server te [herstellen naar een eerder tijdstip](./howto-restore-server-portal.md), voordat de tabel werd toegevoegd. Als u een server herstelt, wordt er een nieuwe kopie van de server gemaakt, vanaf het tijdstip dat u opgeeft.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze resources in de toekomst waarschijnlijk niet nodig hebt, kunt u ze verwijderen door de resourcegroep of de MySQL-server te verwijderen. Voer de volgende stappen uit om de resourcegroep te verwijderen:
1. Zoek en selecteer **Resourcegroepen** in de Azure-portal. 
2. Kies in de lijst met resourcegroepen de naam van uw resourcegroep.
3. Selecteer op de pagina Overzicht van uw resourcegroep de optie **Resourcegroep verwijderen**.
4. Typ de naam van de resourcegroep in het bevestigingsvenster. Selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Azure Portal gebruikt om het volgende te leren:

> [!div class="checklist"]
> * Een Azure Database voor MySQL maken
> * De serverfirewall configureren
> * Het opdrachtregelprogramma mysql gebruiken om een database te maken
> * Voorbeeldgegevens laden
> * Querygegevens
> * Gegevens bijwerken
> * Gegevens terugzetten

> [!div class="nextstepaction"]
> [Toepassingen koppelen met Azure Database voor MySQL](./howto-connection-string.md)