---
title: Toegang tot en beheer van IBM DB2-resources
description: IBM DB2-resources lezen, bewerken, bijwerken en beheren door automatische werk stromen te bouwen met behulp van Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 765bb66b572f0c046222cfb617fe4caa80925256
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967398"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Toegang tot en beheer van IBM DB2-resources met behulp van Azure Logic Apps

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de [IBM DB2-connector](/connectors/db2/)kunt u geautomatiseerde taken en werk stromen maken op basis van de resources die in de DB2-Data Base zijn opgeslagen. Uw werk stromen kunnen verbinding maken met de resources in uw data base, uw database tabellen lezen en weer geven, rijen toevoegen, rijen wijzigen, rijen verwijderen en nog veel meer. U kunt acties in uw Logic apps toevoegen die reacties ophalen uit uw data base en de uitvoer beschikbaar maken voor andere acties.

In dit artikel wordt beschreven hoe u een logische app kunt maken waarmee verschillende database bewerkingen worden uitgevoerd. Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="supported-platforms-and-versions"></a>Ondersteunde platforms en versies

De DB2-connector bevat een micro soft-client die communiceert met externe DB2-servers in een TCP/IP-netwerk. U kunt deze connector gebruiken voor toegang tot Cloud databases zoals IBM DB2 voor Windows die wordt uitgevoerd in azure virtualisatie. U kunt ook toegang krijgen tot on-premises DB2-data bases nadat u [de on-premises gegevens gateway hebt geïnstalleerd en ingesteld](../logic-apps/logic-apps-gateway-connection.md).

De IBM DB2-connector ondersteunt deze IBM DB2-platforms en-versies samen met IBM DB2-compatibele producten die ondersteuning bieden voor Distributed relation data base Architecture (DRDA) SQL Access Manager (SQLAM)-versies 10 en 11:

| Platform | Versie | 
|----------|---------|
| IBM DB2 voor z/O'S | 12, 11,1, 10,1 |
| IBM DB2 voor i | 7,3, 7,2, 7,1 |
| IBM DB2 voor LUW | 11, 10,5 |
|||

## <a name="supported-database-operations"></a>Ondersteunde database bewerkingen

De IBM DB2-connector ondersteunt deze database bewerkingen, die zijn toegewezen aan de bijbehorende acties in de connector:

| Database bewerking | Connector actie |
|--------------------|------------------|
| Database tabellen weer geven | Tabellen ophalen |
| Eén rij lezen met selecteren | Rij ophalen |
| Alle rijen lezen met behulp van selecteren | Rijen ophalen |
| Eén rij toevoegen met INSERT | Rij invoegen |
| Eén rij bewerken met UPDATE | Rij bijwerken |
| Eén rij verwijderen met behulp van verwijderen | Rij verwijderen |
|||

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een IBM DB2-Data Base, ofwel Cloud-of on-premises

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* De logische app waartoe u toegang wilt krijgen tot de DB2-Data Base. Deze connector biedt alleen acties, dus als u uw logische app wilt starten, selecteert u een afzonderlijke trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .
In de voor beelden in dit artikel wordt de **terugkeer patroon** trigger gebruikt.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>DB2-actie toevoegen-tabellen ophalen

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies **nieuwe stap** onder de trigger.

1. Voer in het zoekvak ' DB2 ' in als uw filter. Voor dit voor beeld selecteert u onder de lijst acties de volgende actie: **tabellen ophalen (preview)**

   ![Actie selecteren](./media/connectors-create-api-db2/select-db2-action.png)

   U wordt nu gevraagd om de verbindings gegevens voor uw DB2-Data Base op te geven.

1. Volg de stappen voor het maken van verbindingen voor [Cloud databases](#cloud-connection) of [on-premises data bases](#on-premises-connection).

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Verbinding maken met Cloud DB2

Als u de verbinding wilt instellen, geeft u deze verbindings Details op wanneer u hierom wordt gevraagd, kiest u **maken** en slaat u vervolgens uw logische app op:

| Eigenschap | Vereist | Beschrijving |
|----------|----------|-------------|
| **Verbinding maken via on-premises gateway** | Nee | Geldt alleen voor on-premises verbindingen. |
| **Verbindingsnaam** | Ja | De naam voor de verbinding, bijvoorbeeld ' MyLogicApp-DB2-Connection ' |
| **Server** | Ja | Het adres of de alias dubbele poort nummer voor uw DB2-Server, bijvoorbeeld ' myDB2server.cloudapp.net:50000 ' <p><p>**Opmerking**: deze waarde is een teken reeks die een TCP/IP-adres of alias vertegenwoordigt, hetzij in IPv4-of IPv6-notatie, gevolgd door een dubbele punt en een TCP/IP-poort nummer. |
| **Database** | Ja | De naam voor uw data base <p><p>**Opmerking**: deze waarde is een teken reeks die een DRDA relationele database naam vertegenwoordigt (RDBNAM): <p>-DB2 voor z/O'S accepteert een 16-byte teken reeks waarbij de data base de locatie ' IBM DB2 for z/OS ' wordt genoemd. <br>-DB2 voor ik accepteert een teken reeks van 18 bytes waarbij de data base de relationele data base ' IBM DB2 for i ' wordt genoemd. <br>-DB2 voor LUW accepteert een 8-byte teken reeks. |
| **Gebruikersnaam** | Ja | Uw gebruikers naam voor de data base <p><p>**Opmerking**: deze waarde is een teken reeks waarvan de lengte is gebaseerd op de specifieke Data Base: <p><p>-DB2 voor z/O'S accepteert een 8-byte teken reeks. <br>-DB2 voor ik accepteert een teken reeks van 10 bytes. <br>-DB2 voor Linux of UNIX accepteert een 8-byte teken reeks. <br>-DB2 voor Windows accepteert een teken reeks van 30 bytes. |
| **Wachtwoord** | Ja | Uw wacht woord voor de data base |
||||

Bijvoorbeeld:

![Verbindings Details voor Cloud databases](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Verbinding maken met on-premises DB2

Voordat u een verbinding maakt, moet uw on-premises gegevens gateway al zijn geïnstalleerd. Anders kunt u de instelling van uw verbinding niet volt ooien. Als u de gateway hebt geïnstalleerd, gaat u door met het leveren van deze verbindings gegevens en kiest u **maken**.

| Eigenschap | Vereist | Beschrijving |
|----------|----------|-------------|
| **Verbinding maken via on-premises gateway** | Ja | Is van toepassing wanneer u een on-premises verbinding wilt en de on-premises verbindings eigenschappen wilt weer geven. |
| **Verbindingsnaam** | Ja | De naam voor de verbinding, bijvoorbeeld ' MyLogicApp-DB2-Connection ' | 
| **Server** | Ja | Het adres of de alias dubbele poort nummer voor uw DB2-Server, bijvoorbeeld "myDB2server: 50.000" <p><p>**Opmerking**: deze waarde is een teken reeks die een TCP/IP-adres of alias vertegenwoordigt, hetzij in IPv4-of IPv6-notatie, gevolgd door een dubbele punt en een TCP/IP-poort nummer. |
| **Database** | Ja | De naam voor uw data base <p><p>**Opmerking**: deze waarde is een teken reeks die een DRDA relationele database naam vertegenwoordigt (RDBNAM): <p>-DB2 voor z/O'S accepteert een 16-byte teken reeks waarbij de data base de locatie ' IBM DB2 for z/OS ' wordt genoemd. <br>-DB2 voor ik accepteert een teken reeks van 18 bytes waarbij de data base de relationele data base ' IBM DB2 for i ' wordt genoemd. <br>-DB2 voor LUW accepteert een 8-byte teken reeks. |
| **Verificatie** | Ja | Het verificatie type voor de verbinding, bijvoorbeeld ' Basic ' <p><p>**Opmerking**: Selecteer deze waarde in de lijst, die basis of Windows (Kerberos) omvat. |
| **Gebruikersnaam** | Ja | Uw gebruikers naam voor de data base <p><p>**Opmerking**: deze waarde is een teken reeks waarvan de lengte is gebaseerd op de specifieke Data Base: <p><p>-DB2 voor z/O'S accepteert een 8-byte teken reeks. <br>-DB2 voor ik accepteert een teken reeks van 10 bytes. <br>-DB2 voor Linux of UNIX accepteert een 8-byte teken reeks. <br>-DB2 voor Windows accepteert een teken reeks van 30 bytes. |
| **Wachtwoord** | Ja | Uw wacht woord voor de data base |
| **Gateway** | Ja | De naam voor de geïnstalleerde on-premises gegevens gateway <p><p>**Opmerking**: Selecteer deze waarde uit de lijst, die alle geïnstalleerde gegevens gateways binnen uw Azure-abonnement en resource groep bevat. |
||||

Bijvoorbeeld:

![Verbindings Details voor on-premises data bases](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Uitvoer tabellen weer geven

Als u uw logische app hand matig wilt uitvoeren, kiest u **uitvoeren** op de werk balk van de ontwerp functie. Nadat de logische app is uitgevoerd, kunt u de uitvoer van de uitvoering bekijken.

1. Selecteer **Overzicht** in het menu van de logische app.

1. Onder **samen vatting** selecteert u in de sectie **uitvoerings geschiedenis** de meest recente uitvoering, het eerste item in de lijst.

   ![Uitvoeringsgeschiedenis weergeven](./media/connectors-create-api-db2/run-history.png)

1. Onder **Logic app run** kunt u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
Vouw de actie **tabellen ophalen** uit.

   ![Actie uitvouwen](./media/connectors-create-api-db2/expand-action-step.png)

1. Kies **onbewerkte invoer weer** geven om de invoer weer te geven.

1. Kies **onbewerkte uitvoer weer** geven om de uitvoer weer te geven.

   De uitvoer bevat een lijst met tabellen.

   ![Uitvoer tabellen weer geven](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Rij ophalen

Als u één record in een DB2-database tabel wilt ophalen, gebruikt u de actie **rij ophalen** in uw logische app. Met deze actie wordt een DB2 `SELECT WHERE` -instructie uitgevoerd, bijvoorbeeld `SELECT FROM AREA WHERE AREAID = '99999'` .

1. Als u geen DB2-acties eerder hebt gebruikt in uw logische app, leest u de stappen in de sectie [DB2-actie toevoegen-tabellen ophalen](#add-db2-action) , maar voegt u in plaats daarvan de actie **rij ophalen** toe, en keert u vervolgens terug om door te gaan.

   Nadat u de actie **rij ophalen** hebt toegevoegd, ziet u hier hoe uw voorbeeld logische app wordt weer gegeven:

   ![De actie rij ophalen](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Geef waarden op voor alle vereiste eigenschappen (*). Nadat u een tabel hebt geselecteerd, toont de actie de relevante eigenschappen die specifiek zijn voor records in die tabel.

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Tabelnaam** | Ja | De tabel met de gewenste record, zoals ' gebied ' in dit voor beeld |
   | **Gebieds-ID** | Ja | De ID voor de record die u wilt, zoals ' 99999 ' in dit voor beeld |
   ||||

   ![Scherm afbeelding met de actie ' Get Row (preview) ' met de geopende lijst ' tabel naam ' en de waarde ' gebied ' geselecteerd.](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Wanneer u klaar bent, kiest u **Opslaan** op de werk balk van de ontwerp functie.

### <a name="view-output-row"></a>Uitvoer rij weer geven

Als u uw logische app hand matig wilt uitvoeren, kiest u **uitvoeren** op de werk balk van de ontwerp functie. Nadat de logische app is uitgevoerd, kunt u de uitvoer van de uitvoering bekijken.

1. Selecteer **Overzicht** in het menu van de logische app.

1. Onder **samen vatting** selecteert u in de sectie **uitvoerings geschiedenis** de meest recente uitvoering, het eerste item in de lijst.

1. Onder **Logic app run** kunt u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
Vouw de actie **rij ophalen** uit.

1. Kies **onbewerkte invoer weer** geven om de invoer weer te geven.

1. Kies **onbewerkte uitvoer weer** geven om de uitvoer weer te geven.

   De uitvoer bevat de opgegeven rij.

   ![Uitvoer rij weer geven](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Rijen ophalen

Als u alle records in een DB2-database tabel wilt ophalen, gebruikt u de actie **rijen ophalen** in uw logische app. Met deze actie wordt een DB2 `SELECT` -instructie uitgevoerd, bijvoorbeeld `SELECT * FROM AREA` .

1. Als u geen DB2-acties eerder hebt gebruikt in uw logische app, leest u de stappen in de sectie [DB2-actie toevoegen-tabellen ophalen](#add-db2-action) , maar voegt u in plaats daarvan de actie **rijen ophalen** toe, en keert u vervolgens terug om door te gaan.

   Nadat u de actie **rijen ophalen** hebt toegevoegd, ziet u hier hoe uw voorbeeld logische app wordt weer gegeven:

   ![De actie rijen ophalen](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Open de lijst **tabel naam** en selecteer in dit voor beeld de tabel die u wilt, ' Opper vlakte ':

   ![Scherm afbeelding met de actie ' Get Row (preview) ' met de waarde ' AREA ' die is geselecteerd in de lijst ' tabel naam '.](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Als u een filter of query voor resultaten wilt opgeven, kiest u **Geavanceerde opties weer geven**.

1. Wanneer u klaar bent, kiest u **Opslaan** op de werk balk van de ontwerp functie.

### <a name="view-output-rows"></a>Uitvoer rijen weer geven

Als u uw logische app hand matig wilt uitvoeren, kiest u **uitvoeren** op de werk balk van de ontwerp functie. Nadat de logische app is uitgevoerd, kunt u de uitvoer van de uitvoering bekijken.

1. Selecteer **Overzicht** in het menu van de logische app.

1. Onder **samen vatting** selecteert u in de sectie **uitvoerings geschiedenis** de meest recente uitvoering, het eerste item in de lijst.

1. Onder **Logic app run** kunt u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
Vouw de actie **rijen ophalen** uit.

1. Kies **onbewerkte invoer weer** geven om de invoer weer te geven.

1. Kies **onbewerkte uitvoer weer** geven om de uitvoer weer te geven.

   De uitvoer bevat alle records in de opgegeven tabel.

   ![Uitvoer rijen weer geven](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Rij invoegen

Als u één record wilt toevoegen aan een tabel met een DB2-Data Base, gebruikt u de actie **rij invoegen** in uw logische app. Met deze actie wordt een DB2 `INSERT` -instructie uitgevoerd, bijvoorbeeld `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)` .

1. Als u geen DB2-acties eerder hebt gebruikt in uw logische app, leest u de stappen in de sectie [DB2-actie toevoegen-tabellen ophalen](#add-db2-action) , maar voegt u in plaats daarvan de actie **rij invoegen** toe en keert u vervolgens terug om door te gaan.

   Nadat u de actie **rij invoegen** hebt toegevoegd, ziet u hier hoe uw voorbeeld logische app wordt weer gegeven:

   ![Rij-actie invoegen](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Geef waarden op voor alle vereiste eigenschappen (*). Nadat u een tabel hebt geselecteerd, toont de actie de relevante eigenschappen die specifiek zijn voor records in die tabel.

   In dit voor beeld ziet u de volgende eigenschappen:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Tabelnaam** | Ja | De tabel waar de record moet worden toegevoegd, zoals ' gebied ' |
   | **Gebieds-ID** | Ja | De ID voor het gebied dat moet worden toegevoegd, zoals "99999" |
   | **Beschrijving van gebied** | Ja | De beschrijving voor het gebied dat moet worden toegevoegd, zoals ' gebied 99999 ' |
   | **Regio-ID** | Ja | De ID voor de toe te voegen regio, bijvoorbeeld "102" |
   |||| 

   Bijvoorbeeld:

   ![Scherm opname waarin de Logic Apps Designer wordt weer gegeven met de actie-en voorbeeld waarden van de eigenschap invoegen (preview).](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Wanneer u klaar bent, kiest u **Opslaan** op de werk balk van de ontwerp functie.

### <a name="view-insert-row-outputs"></a>Uitvoer van rij invoegen weer geven

Als u uw logische app hand matig wilt uitvoeren, kiest u **uitvoeren** op de werk balk van de ontwerp functie. Nadat de logische app is uitgevoerd, kunt u de uitvoer van de uitvoering bekijken.

1. Selecteer **Overzicht** in het menu van de logische app.

1. Onder **samen vatting** selecteert u in de sectie **uitvoerings geschiedenis** de meest recente uitvoering, het eerste item in de lijst.

1. Onder **Logic app run** kunt u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
Vouw de actie **rij invoegen** uit.

1. Kies **onbewerkte invoer weer** geven om de invoer weer te geven.

1. Kies **onbewerkte uitvoer weer** geven om de uitvoer weer te geven.

   De uitvoer bevat de record die u hebt toegevoegd aan de opgegeven tabel.

   ![Uitvoer weer geven met ingevoegde rij](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Rij bijwerken

Als u één record in een DB2-database tabel wilt bijwerken, gebruikt u de actie **rij bijwerken** in uw logische app. Met deze actie wordt een DB2 `UPDATE` -instructie uitgevoerd, bijvoorbeeld `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)` .

1. Als u geen DB2-acties eerder hebt gebruikt in uw logische app, leest u de stappen in de sectie [DB2-actie toevoegen-tabellen ophalen](#add-db2-action) , maar voegt u in plaats daarvan de actie **rij bijwerken** toe en keert u vervolgens terug om door te gaan.

   Nadat u de actie **rij bijwerken** hebt toegevoegd, ziet u hier hoe uw voorbeeld logische app wordt weer gegeven:

   ![Actierij bijwerken](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Geef waarden op voor alle vereiste eigenschappen (*). Nadat u een tabel hebt geselecteerd, toont de actie de relevante eigenschappen die specifiek zijn voor records in die tabel.

   In dit voor beeld ziet u de volgende eigenschappen:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Tabelnaam** | Ja | De tabel waar de record moet worden bijgewerkt, bijvoorbeeld ' gebied ' |
   | **Rij-ID** | Ja | De ID voor de record die moet worden bijgewerkt, bijvoorbeeld "99999" |
   | **Gebieds-ID** | Ja | De nieuwe gebieds-ID, zoals ' 99999 ' |
   | **Beschrijving van gebied** | Ja | De beschrijving van het nieuwe gebied, zoals "bijgewerkt 99999" |
   | **Regio-ID** | Ja | De nieuwe regio-ID, bijvoorbeeld "102" |
   ||||

   Bijvoorbeeld:

   ![Scherm opname waarin de Logic Apps Designer wordt weer gegeven met de actie ' update Row (preview) ', waarbij u een tabel selecteert.](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Wanneer u klaar bent, kiest u **Opslaan** op de werk balk van de ontwerp functie.

### <a name="view-update-row-outputs"></a>Resultaten van een bijgewerkte rij weer geven

Als u uw logische app hand matig wilt uitvoeren, kiest u **uitvoeren** op de werk balk van de ontwerp functie. Nadat de logische app is uitgevoerd, kunt u de uitvoer van de uitvoering bekijken.

1. Selecteer **Overzicht** in het menu van de logische app.

1. Onder **samen vatting** selecteert u in de sectie **uitvoerings geschiedenis** de meest recente uitvoering, het eerste item in de lijst.

1. Onder **Logic app run** kunt u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
Vouw de actie **rij bijwerken** uit.

1. Kies **onbewerkte invoer weer** geven om de invoer weer te geven.

1. Kies **onbewerkte uitvoer weer** geven om de uitvoer weer te geven.

   De uitvoer bevat het record dat u in de opgegeven tabel hebt bijgewerkt.

   ![Uitvoer weer geven met bijgewerkte rij](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Rij verwijderen

Als u één record uit een DB2-database tabel wilt verwijderen, gebruikt u de actie **rij verwijderen** in uw logische app. Met deze actie wordt een DB2 `DELETE` -instructie uitgevoerd, bijvoorbeeld `DELETE FROM AREA WHERE AREAID = '99999'` .

1. Als u geen DB2-acties eerder hebt gebruikt in uw logische app, leest u de stappen in de sectie [DB2-actie toevoegen-tabellen ophalen](#add-db2-action) , maar voegt u in plaats daarvan de actie **rij verwijderen** toe en keert u vervolgens terug om door te gaan.

   Nadat u de actie **rij verwijderen** hebt toegevoegd, ziet u hier hoe uw voorbeeld logische app wordt weer gegeven:

   ![Actierij verwijderen](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Geef waarden op voor alle vereiste eigenschappen (*). Nadat u een tabel hebt geselecteerd, toont de actie de relevante eigenschappen die specifiek zijn voor records in die tabel.

   In dit voor beeld ziet u de volgende eigenschappen:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Tabelnaam** | Ja | De tabel waar de record moet worden verwijderd, zoals ' gebied ' |
   | **Rij-ID** | Ja | De ID voor de record die moet worden verwijderd, bijvoorbeeld "99999" |
   ||||

   Bijvoorbeeld:

   ![Scherm opname waarin de Logic Apps Designer wordt weer gegeven met de actie ' rij verwijderen (preview) ', waarin u een tabel selecteert die u wilt verwijderen.](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Wanneer u klaar bent, kiest u **Opslaan** op de werk balk van de ontwerp functie.

### <a name="view-delete-row-outputs"></a>Uitvoer van rij verwijderen weer geven

Als u uw logische app hand matig wilt uitvoeren, kiest u **uitvoeren** op de werk balk van de ontwerp functie. Nadat de logische app is uitgevoerd, kunt u de uitvoer van de uitvoering bekijken.

1. Selecteer **Overzicht** in het menu van de logische app.

1. Onder **samen vatting** selecteert u in de sectie **uitvoerings geschiedenis** de meest recente uitvoering, het eerste item in de lijst.

1. Onder **Logic app run** kunt u nu de status, invoer en uitvoer voor elke stap in uw logische app bekijken.
Vouw de actie **rij verwijderen** uit.

1. Kies **onbewerkte invoer weer** geven om de invoer weer te geven.

1. Kies **onbewerkte uitvoer weer** geven om de uitvoer weer te geven.

   In de uitvoer is de record die u hebt verwijderd uit de opgegeven tabel niet meer opgenomen.

   ![Uitvoer weer geven zonder verwijderde rij](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Connector-verwijzing

Voor meer technische informatie over deze connector, zoals triggers, acties en limieten, zoals beschreven in het Swagger-bestand van de connector, raadpleegt u de [referentie pagina van de connector](/connectors/db2/).

> [!NOTE]
> Voor Logic apps in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), maakt de ISE-versie van deze connector gebruik van de [ISE-bericht limieten](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) in plaats daarvan.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)

