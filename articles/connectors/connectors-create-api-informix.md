---
title: Verbinding maken met de IBM Informix-data base
description: Taken en werk stromen automatiseren waarmee resources worden beheerd die zijn opgeslagen in IBM Informix met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
author: gplarsen
ms.author: daberry
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/07/2020
tags: connectors
ms.openlocfilehash: 4995a91783c2302f3bda5cc9409f017248ca29fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88761641"
---
# <a name="manage-ibm-informix-database-resources-by-using-azure-logic-apps"></a>IBM Informix-database bronnen beheren met behulp van Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de [Informix-connector](/connectors/informix/)kunt u geautomatiseerde taken en werk stromen maken waarmee resources worden beheerd in een IBM Informix-data base. Deze connector bevat een micro soft-client die communiceert met externe Informix-Server computers via een TCP/IP-netwerk, inclusief Cloud databases zoals IBM Informix voor Windows die worden uitgevoerd in azure Virtualization en on-premises data bases wanneer u de [on-premises gegevens gateway](../logic-apps/logic-apps-gateway-connection.md)gebruikt. U kunt verbinding maken met deze Informix-platforms en-versies als deze zijn geconfigureerd voor ondersteuning van gedistribueerde DRDA-client verbindingen (Relational Data Base Architecture):

* IBM Informix 12,1
* IBM Informix 11,7

In dit onderwerp wordt beschreven hoe u de connector in een logische app kunt gebruiken om database bewerkingen te verwerken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Voor on-premises data bases [downloadt en installeert u de on-premises gegevens gateway](../logic-apps/logic-apps-gateway-install.md) op een lokale computer en maakt u vervolgens [een Azure data gateway-resource in de Azure Portal](../logic-apps/logic-apps-gateway-connection.md).

* De logische app waar u toegang nodig hebt tot uw Informix-data base. Deze connector biedt alleen acties, dus uw logische app moet al beginnen met een trigger, bijvoorbeeld de trigger voor [terugkeer patroon](../connectors/connectors-native-recurrence.md). 

## <a name="add-an-informix-action"></a>Een Informix-actie toevoegen

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Selecteer **nieuwe stap** onder de stap waar u de Informix-actie wilt toevoegen.

   Als u een actie wilt toevoegen tussen de bestaande stappen, plaatst u de muis aanwijzer op de verbindings pijl. Selecteer het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak `informix` als uw filter in. Selecteer in de lijst acties de gewenste actie, bijvoorbeeld:

   ![Selecteer de Informix-actie die moet worden uitgevoerd](./media/connectors-create-api-informix/select-informix-connector-action.png)

   De connector biedt de volgende acties, waarmee de bijbehorende database bewerkingen worden uitgevoerd:

   * Tabellen ophalen-database tabellen weer geven met behulp van een `CALL` instructie
   * Rijen ophalen: alle rijen lezen met behulp van een `SELECT *` instructie
   * Rij ophalen: een rij lezen met behulp van een `SELECT WHERE` instructie
   * Een rij toevoegen met behulp van een `INSERT` instructie
   * Een rij bewerken met behulp van een `UPDATE` instructie
   * Een rij verwijderen met behulp van een `DELETE` instructie

1. Als u wordt gevraagd om de verbindings gegevens voor uw Informix-Data Base op te geven, volgt u de [stappen voor het maken van de verbinding](#create-connection)en gaat u verder met de volgende stap.

1. Geef de gegevens voor de geselecteerde actie op:

   | Bewerking | Beschrijving | Eigenschappen en beschrijvingen |
   |--------|-------------|-----------------------------|
   | **Tabellen ophalen** | Database tabellen weer geven door een instructie voor een Informix-AANROEP uit te voeren. | Geen |
   | **Rijen ophalen** | Alle rijen in de opgegeven tabel ophalen door een Informix-instructie uit te voeren `SELECT *` . | **Tabel naam**: de naam voor de Informix-tabel die u wilt <p><p>Als u andere eigenschappen aan deze actie wilt toevoegen, selecteert u deze in de lijst **nieuwe para meter toevoegen** . Zie het [naslag onderwerp van de connector](/connectors/informix/)voor meer informatie. |
   | **Rij ophalen** | Een rij uit de opgegeven tabel ophalen door een Informix-instructie uit te voeren `SELECT WHERE` . | - **Tabel naam**: de naam voor de Informix-tabel die u wilt <br>- **Rij-id**: de unieke id voor de rij, bijvoorbeeld `9999` |
   | **Rij invoegen** | Een rij toevoegen aan de opgegeven Informix-tabel door een Informix-instructie uit te voeren `INSERT` . | - **Tabel naam**: de naam voor de Informix-tabel die u wilt <br>- **item**: de rij met de toe te voegen waarden |
   | **Rij bijwerken** | Een rij in de opgegeven Informix-tabel wijzigen door een Informix-instructie uit te voeren `UPDATE` . | - **Tabel naam**: de naam voor de Informix-tabel die u wilt <br>- **Rij-id**: de unieke id voor de rij die moet worden bijgewerkt, bijvoorbeeld `9999` <br>- **Row**: de rij met de bijgewerkte waarden, bijvoorbeeld `102` |
   | **Rij verwijderen** | Een rij uit de opgegeven Informix-tabel verwijderen door een Informix-instructie uit te voeren `DELETE` . | - **Tabel naam**: de naam voor de Informix-tabel die u wilt <br>- **Rij-id**: de unieke id voor de rij die u wilt verwijderen, bijvoorbeeld `9999` |
   ||||

1. Sla uw logische app op. U kunt nu [uw logische app testen](#test-logic-app) of door gaan met het bouwen van uw logische app.

<a name="create-connection"></a>

## <a name="connect-to-informix"></a>Verbinding maken met Informix

1. Als uw logische app verbinding maakt met een on-premises data base, selecteert u **verbinding maken via on-premises gegevens gateway**.

1. Geef deze verbindings gegevens op en selecteer **maken**.

   | Eigenschap | JSON-eigenschap | Vereist | Voorbeeldwaarde | Beschrijving |
   |----------|---------------|----------|---------------|-------------|
   | Verbindingsnaam | `name` | Ja | `informix-demo-connection` | De naam die moet worden gebruikt voor de verbinding met uw Informix-data base |
   | Server | `server` | Ja | Cloud `informixdemo.cloudapp.net:9089` <br>-On-premises: `informixdemo:9089` | Het TCP/IP-adres of de alias in de IPv4-of IPv6-indeling, gevolgd door een dubbele punt en een TCP/IP-poort nummer |
   | Database | `database` | Ja | `nwind` | De DRDA relationele database naam (RDBNAM) of de naam van de Informix-data base (dbname). Informix accepteert een teken reeks van 128 bytes. |
   | Verificatie | `authentication` | Alleen on-premises | **Basic** of **Windows** (Kerberos) | Het verificatie type dat vereist is voor uw Informix-data base. Deze eigenschap wordt alleen weer gegeven wanneer u **verbinding maken via een on-premises gegevens gateway** selecteert. |
   | Gebruikersnaam | `username` | Nee | <*data base-gebruikers naam*> | Een gebruikers naam voor de data base |
   | Wachtwoord | `password` | Nee | <*data base-wacht woord*> | Een wacht woord voor de data base |
   | Gateway | `gateway` | Alleen on-premises | -<*Azure-abonnement*> <br>-<*Azure-on-premises-gegevens gateway-bron*> | Het Azure-abonnement en de Azure-resource naam voor de on-premises gegevens gateway die u hebt gemaakt in de Azure Portal. De eigenschap **Gateway** en subeigenschappen worden alleen weer gegeven wanneer u **verbinding maken via een on-premises gegevens gateway** selecteert. |
   ||||||

   Bijvoorbeeld:

   * **Cloud database**

     ![Verbindings gegevens voor de Cloud database](./media/connectors-create-api-informix/informix-cloud-connection.png)

   * **On-premises data base**

     ![Informatie over on-premises database verbindingen](./media/connectors-create-api-informix/informix-on-premises-connection.png)

1. Sla uw logische app op.

<a name="test-logic-app"></a>

## <a name="test-your-logic-app"></a>Uw logische app testen

1. Selecteer **uitvoeren** op de werk balk van de Logic app-ontwerp functie. Wanneer de logische app wordt uitgevoerd, kunt u de uitvoer van die uitvoering bekijken.

1. Selecteer **overzicht** in het menu van de logische app. Selecteer in het deel venster Overzicht onder **overzicht** van uitvoerings  >  **runs** de meest recente uitvoering.

1. Selecteer onder **Logic app run** de optie **Details uitvoeren**.

1. Selecteer in de lijst acties de actie met de uitvoer die u wilt weer geven, bijvoorbeeld **Get_tables**.

   Als de actie is geslaagd, wordt de eigenschap **status** als **geslaagd** gemarkeerd.

1. Als u de invoer wilt weer geven, selecteert u onder **invoer koppeling** de URL-koppeling. Als u de uitvoer wilt weer geven, selecteert u onder **uitvoer** koppeling de koppeling URL. Hier volgen enkele voor beelden van uitvoer:

   * **Get_tables** ziet u een lijst met tabellen:

     ![Uitvoer van de actie ' Tables ophalen '](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

   * **Get_rows** wordt een lijst met rijen weer gegeven:

     ![Uitvoer van de actie rijen ophalen](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

   * **Get_row** wordt de opgegeven rij weer gegeven:

     ![Uitvoer van de actie ' rij ophalen '](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

   * **Insert_row** wordt de nieuwe rij weer gegeven:

     ![Uitvoer van de actie ' rij invoegen '](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

   * **Update_row** wordt de bijgewerkte rij weer gegeven:

     ![Uitvoer van de actie rij bijwerken](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

   * **Delete_row** wordt de verwijderde rij weer gegeven:

     ![Uitvoer van de actie rij verwijderen](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="connector-specific-details"></a>Connector-specifieke Details

Raadpleeg de [referentie pagina van de connector](/connectors/informix/)voor technische informatie over triggers, acties en limieten die worden beschreven door de Swagger-beschrijving van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](apis-list.md)
