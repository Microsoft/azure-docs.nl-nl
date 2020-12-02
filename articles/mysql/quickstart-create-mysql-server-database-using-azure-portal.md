---
title: 'Quickstart: Een server maken - Azure Portal - Azure Database for MySQL'
description: Dit artikel leidt u stapsgewijs door de Azure Portal zodat u in ongeveer vijf minuten een voorbeeld van een Azure-database voor MySQL-server kunt maken.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: f71bcc1fd3b92a32a3e6d9fa056bae7131a663bd
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492604"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Quickstart: Een Azure-database voor MySQL-server maken met behulp van Azure Portal

Azure Database for MySQL is een beheerde service waarmee u MySQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze quickstart ontdekt u hoe u Azure Portal gebruikt om één Azure Database for MySQL-server te maken. U ziet ook hoe u verbinding maakt met de server.

## <a name="prerequisites"></a>Vereisten
Een Azure-abonnement is vereist. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Eén Azure Database for MySQL-server maken
1. Ga naar [Azure Portal](https://portal.azure.com/) om één Azure Database for MySQL-server-database te maken. Zoek en selecteer **Azure Database for MySQL**:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Azure Database for MySQL opzoeken":::

1. Selecteer **Toevoegen**.

2. Selecteer op de pagina **Implementatieoptie Azure Database for MySQL** **Enkele server**:
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Schermopname van de optie Enkele server.":::

3. Voer de basisinstellingen in voor een nieuwe enkele server:

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Schermopname van de optie MySQL-server maken.":::

   **Instelling** | **Voorgestelde waarde** | **Beschrijving**
   ---|---|---
   Abonnement | Uw abonnement | Selecteer het gewenste Azure-abonnement.
   Resourcegroep | **myresourcegroup** | Voer een nieuwe resourcegroep in kies of een bestaande uit uw abonnement.
   Servernaam | **mydemoserver** | Voer een unieke naam in. De servernaam mag alleen kleine letters, cijfers en het koppelteken (-) bevatten. De naam moet 3 tot 63 tekens bevatten.
   Gegevensbron |**Geen** | Selecteer **Geen** om een nieuwe server te maken. Selecteer **Back-up** alleen als u herstelt vanuit een back-up voor geografische gebieden van een bestaande server.
   Locatie |Uw gewenste locatie | Selecteer een locatie in de lijst.
   Versie | De meest recente primaire versie| Gebruik de meest recente primaire versie. Zie [alle ondersteunde versies](../postgresql/concepts-supported-versions.md).
   Compute en opslag | De standaardwaarden gebruiken| De standaardprijscategorie is **Algemeen gebruik** met **4 vCores** en **100 GB** opslag. Back-upretentie is ingesteld op **7 dagen**, met de back-upoptie **Geografisch redundant**.<br/>Bekijk de pagina met [prijzen](https://azure.microsoft.com/pricing/details/mysql/) en werk indien nodig de standaardwaarden bij.
   Gebruikersnaam van beheerder | **mydemoadmin** | Voer de gebruikersnaam van de serverbeheerder in. Het is niet toegestaan om **azure_superuser**, **admin**, **administrator**, **root**, **guest** of **public** voor de gebruikersnaam van de beheerder te gebruiken.
   Wachtwoord | Een wachtwoord | Een nieuw wachtwoord voor de gebruikersnaam van de serverbeheerder. Het wachtwoord moet 8 tot 128 tekens lang zijn en moet bestaan uit een combinatie van hoofdletters, kleine letters, cijfers en niet-alfanumerieke tekens (!, $, #, %, enzovoort).
  

   > [!NOTE]
   > Overweeg het gebruik van de prijscategorie Basic als lichte reken- en I/O-capaciteit voldoende is voor uw workload. Servers die zijn gemaakt in de prijscategorie Basic kunnen later niet meer worden geschaald voor Algemeen gebruik of Geoptimaliseerd voor geheugen.

4. Selecteer **Beoordelen en maken** om de server in te richten.

5. Wacht tot de portal-pagina **Uw implementatie is voltooid** weergeeft. Selecteer **Ga naar resource** om naar de zojuist gemaakte serverpagina te gaan:

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="Schermopname met het bericht Uw implementatie is voltooid.":::

[Hebt u problemen? Laat het ons weten.](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Een serverfirewallregel configureren

De nieuwe server is standaard beveiligd met een firewall. Als u verbinding wilt maken, moet u toegang tot uw IP-adres verlenen door de volgende stappen uit te voeren:

1. Ga vanuit het linkerdeelvenster voor uw serverresource naar **Verbindingsbeveiliging**. Als u niet weet hoe u uw resource moet vinden, raadpleegt u [Een resource openen](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Schermopname van de pagina Verbindingsbeveiliging > Firewallregels.":::

2. Selecteer **Huidig IP-adres van client toevoegen** en selecteer vervolgens **Opslaan**.

   > [!NOTE]
   > Controleer of uw netwerk uitgaand verkeer toestaat via poort 3306, die wordt gebruikt door Azure Database for MySQL, om connectiviteitsproblemen te vermijden. 

U kunt meer IP-adressen toevoegen of een IP-bereik opgeven om vanaf die IP-adressen verbinding te maken met uw server. Zie [Firewallregels beheren op een Azure Database for MySQL-server](./concepts-firewall-rules.md) voor meer informatie.


[Hebt u problemen? Laat het ons weten](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-by-using-mysqlexe"></a>Verbinding maken met de server met behulp van mysql.exe
U kunt [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) of [MySQL Workbench](./connect-workbench.md) gebruiken om vanuit uw lokale omgeving verbinding met de server te maken. In deze quickstart voeren we mysql.exe in [Azure Cloud Shell](../cloud-shell/overview.md) uit om verbinding te maken met de server.


1. Open Azure Cloud Shell in de portal door de eerste knop op de werkbalk te selecteren, zoals in de volgende schermopname wordt weergegeven. Noteer de servernaam, de naam van de serverbeheerder en het abonnement voor uw nieuwe server in de sectie **Overzicht**, zoals weergegeven in de schermopname.

    > [!NOTE]
    > Als u Cloud Shell voor de eerste keer opent, wordt u gevraagd om een resourcegroep en opslagaccount te maken. Dit is een eenmalige stap. Deze wordt voor alle volgende sessies automatisch toegevoegd.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Schermopname van Cloud Shell in Azure Portal.":::
2. Voer de volgende opdracht uit in de Azure Cloud Shell-terminal. Vervang de waarden door uw daadwerkelijke servernaam en gebruikersnaam van de beheerder. Voor Azure Database for MySQL moet u `@\<servername>` toevoegen aan de gebruikersnaam van de beheerder, zoals hier wordt weergegeven: 

      ```azurecli-interactive
      mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p
      ```

      Zo ziet het eruit in de Cloud Shell-terminal:

      ```
      Requesting a Cloud Shell.Succeeded.
      Connecting terminal...

      Welcome to Azure Cloud Shell

      Type "az" to use Azure CLI
      Type "help" to learn about Cloud Shell

      user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
      Enter password:
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      Your MySQL connection id is 64796
      Server version: 5.6.42.0 Source distribution

      Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.
      Oracle is a registered trademark of Oracle Corporation and/or its
      affiliates. Other names may be trademarks of their respective
      owners.

      Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
      mysql>
      ```
3. Maak in dezelfde Azure Cloud Shell-terminal een database met de naam `guest`:
      ```
      mysql> CREATE DATABASE guest;
      Query OK, 1 row affected (0.27 sec)
      ```
4. Ga naar de `guest`-database:
      ```
      mysql> USE guest;
      Database changed
      ```
5. Voer `quit` in en selecteer vervolgens **Enter** om mysql af te sluiten.

[Hebt u problemen? Laat het ons weten.](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Resources opschonen
U hebt nu een Azure Database for MySQL-server in een resourcegroep gemaakt.  Als u deze resources in de toekomst waarschijnlijk niet nodig hebt, kunt u ze verwijderen door de resourcegroep te verwijderen. U kunt ook de MySQL-server verwijderen. Voltooi de volgende stappen om de resourcegroep te verwijderen:
1. Zoek en selecteer **Resourcegroepen** in de Azure-portal.
2. Selecteer in de lijst met resourcegroepen de naam van de resourcegroep.
3. Selecteer op de pagina **Overzicht** voor uw resourcegroep de optie **Resourcegroep verwijderen**.
4. Typ de naam van de resourcegroep in het bevestigingsvenster. Selecteer vervolgens **Verwijderen**.

Om uw server te verwijderen, selecteert u **Verwijderen** op de pagina **Overzicht** van uw server, zoals hier wordt weergegeven:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Schermopname met de knop Verwijderen op de pagina Overzicht van de server.":::

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
>[Een PHP-app bouwen in Windows met MySQL](../app-service/tutorial-php-mysql-app.md) <br/>

> [!div class="nextstepaction"]
>[PHP-app bouwen op Linux met MySQL](../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%3fpivots%3dplatform-linux)<br/><br/>

[Kunt u niet vinden wat u zoekt? Laat het ons weten.](https://aka.ms/mysql-doc-feedback)