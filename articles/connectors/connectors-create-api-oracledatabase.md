---
title: Verbinding maken met Oracle Database
description: Records invoegen en beheren met Oracle Database REST-Api's en Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 05/20/2020
tags: connectors
ms.openlocfilehash: 91873a2d6a498712773bfe721653e64c3364666f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674819"
---
# <a name="get-started-with-the-oracle-database-connector"></a>Aan de slag met de Oracle Database-connector

Met behulp van de Oracle Database-connector maakt u organisatie werk stromen die gebruikmaken van de gegevens in uw bestaande data base. Deze connector kan verbinding maken met een on-premises Oracle Database, of een virtuele machine van Azure met Oracle Database geïnstalleerd. Met deze connector kunt u het volgende doen:

* Bouw uw werk stroom door een nieuwe klant toe te voegen aan een klanten database of een bestelling bij te werken in een Data Base voor orders.
* Gebruik acties om een rij gegevens op te halen, een nieuwe rij in te voegen en zelfs te verwijderen. Als er bijvoorbeeld een record wordt gemaakt in Dynamics CRM Online (een trigger), voegt u een rij in een Oracle Database (een actie). 

Deze connector biedt geen ondersteuning voor de volgende items:

* Weergaven 
* Een tabel met samengestelde sleutels
* Geneste object typen in tabellen
* Database functies met niet-scalaire waarden

Dit artikel laat u zien hoe u de Oracle Database-Connector in een logische app kunt gebruiken.

## <a name="prerequisites"></a>Vereisten

* Ondersteunde Oracle-versies: 
    * Oracle 9 en hoger
    * Oracle-clientsoftware 8.1.7 en hoger

* De on-premises gegevens gateway installeren. [Als u verbinding maakt met on-premises gegevens uit Logic apps](../logic-apps/logic-apps-gateway-connection.md) , worden de stappen weer gegeven. De gateway is vereist om verbinding te maken met een on-premises Oracle Database, of een virtuele machine van Azure met Oracle DB geïnstalleerd. 

    > [!NOTE]
    > De on-premises gegevens gateway fungeert als een brug en biedt een beveiligde gegevens overdracht tussen on-premises gegevens (gegevens die zich niet in de Cloud bevindt) en uw Logic apps. Dezelfde gateway kan worden gebruikt met meerdere services en meerdere gegevens bronnen. Het is dus mogelijk dat u de gateway slechts één keer hoeft te installeren.

* Installeer de Oracle-client op de computer waarop u de on-premises gegevens gateway hebt geïnstalleerd. Zorg ervoor dat u de 64-bits Oracle-gegevens provider voor .NET van Oracle installeert:  

  [64-bits ODAC 12c release 4 (12.1.0.2.4) voor Windows x64](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)

    > [!TIP]
    > Als de Oracle-client niet is geïnstalleerd, treedt er een fout op wanneer u de verbinding probeert te maken of gebruiken. Zie de veelvoorkomende fouten in dit artikel.


## <a name="add-the-connector"></a>De connector toevoegen

> [!IMPORTANT]
> Deze connector heeft geen triggers. Het heeft alleen acties. Wanneer u uw logische app maakt, voegt u dus een andere trigger toe om uw logische app te starten, zoals **planning-recurrence** of **aanvraag/antwoord-antwoord**. 

1. Maak in de [Azure Portal](https://portal.azure.com)een lege logische app.

2. Selecteer aan het begin van uw logische app de trigger **aanvraag/antwoord-aanvraag** : 

    ![Een dialoog venster bevat een vak om alle triggers te doorzoeken. Er is ook één trigger weer gegeven, ' aanvraag/antwoord-aanvraag ', met een selectie knop.](./media/connectors-create-api-oracledatabase/request-trigger.png)

3. Selecteer **Opslaan**. Wanneer u opslaat, wordt er automatisch een aanvraag-URL gegenereerd. 

4. Selecteer **Nieuwe stap** en selecteer **Een actie toevoegen**. Typ in `oracle` om de beschik bare acties weer te geven: 

    ![Een zoekvak bevat ' Oracle '. De zoek opdracht produceert één treffer met het label Oracle Database. Er bevindt zich een pagina met tabbladen, één tabblad met ' TRIGGERS (0) ', een andere met ' acties (6) '. Er worden zes acties weer gegeven. De eerste hiervan is ' voor beeld van rij ophalen '.](./media/connectors-create-api-oracledatabase/oracledb-actions.png)

    > [!TIP]
    > Dit is ook de snelste manier om de triggers en acties weer te geven die beschikbaar zijn voor elke connector. Typ een deel van de naam van de connector, zoals `oracle` . In de ontwerp functie worden alle triggers en acties weer gegeven. 

5. Selecteer een van de acties, zoals **Oracle database-rij ophalen**. Selecteer **verbinding via on-premises gegevens gateway**. Voer de naam van de Oracle-Server, de verificatie methode, de gebruikers naam, het wacht woord en selecteer de gateway:

    ![Het dialoog venster heeft de titel "Oracle Database-Get rij". Er is een selectie vakje ' verbinding maken via on-premises gegevens gateway ' ingeschakeld. Hieronder ziet u de vijf andere tekst vakken.](./media/connectors-create-api-oracledatabase/create-oracle-connection.png)

6. Zodra de verbinding is gemaakt, selecteert u een tabel in de lijst en voert u de rij-ID in voor uw tabel. U moet de id van de tabel weten. Als u dat niet weet, neemt u contact op met uw Oracle DB-beheerder en haalt u de uitvoer van `select * from yourTableName` . Dit geeft u de informatie die u nodig hebt om door te gaan.

    In het volgende voor beeld worden taak gegevens geretourneerd vanuit een Human Resources-Data Base: 

    ![Het dialoog venster ' Get Row (preview) ' heeft twee tekst vakken: ' tabel naam ', dat ' H R JOBS ' bevat en een vervolg keuzelijst heeft, en ' rij i d ', die ' S A _ REP ' bevat.](./media/connectors-create-api-oracledatabase/table-rowid.png)

7. In deze volgende stap kunt u een van de andere connectors gebruiken om uw werk stroom te bouwen. Als u het ophalen van gegevens van Oracle wilt testen, stuurt u een e-mail met de Oracle-gegevens via een van de e-mail connectors verzenden, zoals Office 365 Outlook. Gebruik de dynamische tokens uit de Oracle-tabel om de `Subject` en `Body` van uw e-mail te bouwen:

    ![Er zijn twee dialoog vensters. Het vak ' een e-mail verzenden ' bevat vakjes om het ' hoofd tekst ', het onderwerp en het adres van de e-mail op te geven. Het dialoog venster dynamische inhoud toevoegen bevat een zoek opdracht van dynamische inhoud van de apps en services van de stroom.](./media/connectors-create-api-oracledatabase/oracle-send-email.png)

8. **Sla** de logische app op en selecteer vervolgens **uitvoeren**. Sluit de ontwerp functie en Bekijk de uitvoerings geschiedenis voor de status. Als dit mislukt, selecteert u de map met het mislukte bericht. De ontwerp functie wordt geopend en toont u de stap die is mislukt. Daarnaast wordt de fout informatie weer gegeven. Als dit lukt, ontvangt u een e-mail bericht met de informatie die u hebt toegevoegd.


### <a name="workflow-ideas"></a>Werk stroom ideeën

* U wilt de #oracle hashtag bewaken en de tweets in een Data Base plaatsen, zodat deze kunnen worden opgevraagd en in andere toepassingen kan worden gebruikt. Voeg in een logische app de `Twitter - When a new tweet is posted` trigger toe en voer de **#oracle** hashtag in. Voeg vervolgens de `Oracle Database - Insert row` actie toe en selecteer de tabel:

    ![Het dialoog venster ' wanneer een nieuwe tweet wordt gepost ' bevat ' hashtag Oracle ' als Zoek tekst en stelt u in staat om de frequentie van de controle in te stellen. In dit dialoog venster wordt het dialoog venster "Oracle Database" genoemd, waarmee u de actie kunt selecteren.](./media/connectors-create-api-oracledatabase/twitter-oracledb.png)

* Berichten worden verzonden naar een Service Bus wachtrij. U wilt deze berichten ophalen en in een Data Base opnemen. Voeg de trigger toe aan een logische app `Service Bus - when a message is received in a queue` en selecteer de wachtrij. Voeg vervolgens de `Oracle Database - Insert row` actie toe en selecteer de tabel:

    ![De ' wanneer een bericht wordt ontvangen... ' in het dialoog venster worden ' orders ' weer gegeven als de wachtrij naam en kunt u de frequentie van de controle opgeven. In dit vak wordt het dialoog venster ' invoegrij (preview) ' weer gegeven waarin u ' tabel naam ' kunt selecteren.](./media/connectors-create-api-oracledatabase/sbqueue-oracledb.png)

## <a name="common-errors"></a>Algemene fouten

#### <a name="error-cannot-reach-the-gateway"></a>**Fout**: kan de gateway niet bereiken

**Oorzaak**: de on-premises gegevens gateway kan geen verbinding maken met de Cloud. 

**Risico beperking**: Zorg ervoor dat uw gateway wordt uitgevoerd op de on-premises machine waarop u deze hebt geïnstalleerd en dat deze verbinding kan maken met internet.  U wordt aangeraden de gateway niet te installeren op een computer die kan worden uitgeschakeld of in de slaap stand kan worden gezet. U kunt ook de on-premises gegevens Gateway Service (PBIEgwService) opnieuw starten.

#### <a name="error-the-provider-being-used-is-deprecated-systemdataoracleclient-requires-oracle-client-software-version-817-or-greater-see-httpsgomicrosoftcomfwlinkplinkid272376-to-install-the-official-provider"></a>**Fout**: de gebruikte provider is afgeschaft: System. data. OracleClient vereist Oracle client software version 8.1.7 of hoger. Zie [https://go.microsoft.com/fwlink/p/?LinkID=272376](/power-bi/connect-data/desktop-connect-oracle-database) de officiële provider installeren.

**Oorzaak**: de Oracle-client-SDK is niet geïnstalleerd op de computer waarop de on-premises gegevens gateway wordt uitgevoerd.  

**Oplossing**: down load en installeer de Oracle-client-SDK op dezelfde computer als de on-premises gegevens gateway.

#### <a name="error-table-tablename-does-not-define-any-key-columns"></a>**Fout**: in tabel [TableName] worden geen sleutel kolommen gedefinieerd

**Oorzaak**: de tabel heeft geen primaire sleutel.  

**Oplossing**: de Oracle Database-Connector vereist dat er een tabel met een primaire sleutel kolom wordt gebruikt.
 
## <a name="connector-specific-details"></a>Connector-specifieke Details

Bekijk de triggers en acties die zijn gedefinieerd in Swagger en Zie ook eventuele limieten in de details van de [connector](/connectors/oracle/). 

## <a name="get-some-help"></a>Hulp krijgen

De [pagina micro soft Q&een vraag voor Azure Logic apps](/answers/topics/azure-logic-apps.html) is een goede plaats om vragen te stellen, vragen te beantwoorden en te zien wat andere Logic Apps gebruikers doen. 

U kunt Logic Apps en connectors verbeteren door te stemmen en uw ideeën in te dienen [https://aka.ms/logicapps-wish](https://aka.ms/logicapps-wish) . 


## <a name="next-steps"></a>Volgende stappen
[Maak een logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md)en verken de beschik bare connectors in Logic apps in de [lijst api's](apis-list.md).