---
title: Verbinding maken met de IBM MQ-server
description: Berichten verzenden en ophalen met een Azure-of on-premises IBM MQ-server en Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 03/10/2021
tags: connectors
ms.openlocfilehash: a07eb6e592c68794f0e4038a7cf9a42bd396b47a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103495229"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Verbinding maken met een IBM MQ-server vanaf Azure Logic Apps

De MQ-connector verzendt en haalt berichten op die zijn opgeslagen in een MQ-server on-premises of in Azure. Deze connector bevat een micro soft MQ-client die communiceert met een externe IBM MQ-server via een TCP/IP-netwerk. Dit artikel bevat een Start handleiding voor het gebruik van de MQ-connector. U kunt beginnen door één bericht in een wachtrij te bladeren en vervolgens andere acties uit te proberen.

De MQ-connector bevat deze acties, maar biedt geen triggers:

- Blader door één bericht zonder het bericht van de MQ-server te verwijderen.
- Bladeren in een batch berichten zonder de berichten van de MQ-server te verwijderen.
- Ontvang één bericht en verwijder het bericht van de MQ-server.
- Ontvang een batch berichten en verwijder de berichten van de MQ-server.
- Eén bericht naar de MQ-server verzenden.

Dit zijn de officieel ondersteunde IBM WebSphere MQ-versies:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0
  * MQ 9,1

## <a name="prerequisites"></a>Vereisten

* Als u een on-premises MQ-server gebruikt, moet u [de on-premises gegevens gateway installeren](../logic-apps/logic-apps-gateway-install.md) op een server in uw netwerk.

  > [!NOTE]
  > Als uw MQ-server openbaar beschikbaar of beschikbaar is in azure, hoeft u de gegevens gateway niet te gebruiken.

  * Voor een goede werking van de MQ-connector moet .NET Framework 4,6 zijn geïnstalleerd op de server waarop u de on-premises gegevens gateway installeert.
  
  * Nadat u de on-premises gegevens gateway hebt geïnstalleerd, moet u ook [een Azure gateway-resource maken voor de on-premises gegevens gateway](../logic-apps/logic-apps-gateway-connection.md) die de MQ-connector gebruikt om toegang te krijgen tot uw on-PREMISes MQ-server.

* De logische app waarvoor u de MQ-connector wilt gebruiken. De MQ-connector heeft geen triggers, dus u moet eerst een trigger toevoegen aan uw logische app. U kunt bijvoorbeeld de [terugkeer patroon trigger](../connectors/connectors-native-recurrence.md)gebruiken. Als u geen ervaring hebt met Logic apps, kunt u deze [Snelstartgids proberen om uw eerste logische app te maken](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limitations"></a>Beperkingen

De MQ-connector ondersteunt of gebruikt het **indelings** veld van het bericht niet en voert geen conversie van teken sets uit. De connector plaatst alleen de gegevens die worden weer gegeven in het bericht veld in een JSON-bericht en verzendt het bericht langs.

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Een MQ-verbinding maken

Als u nog geen MQ-verbinding hebt wanneer u een MQ-actie toevoegt, wordt u gevraagd om de verbinding te maken, bijvoorbeeld:

![Verbindings gegevens opgeven](media/connectors-create-api-mq/connection-properties.png)

1. Als u verbinding maakt met een on-premises MQ-server, selecteert u **verbinding maken via on-premises gegevens gateway**.

1. Geef de verbindings gegevens voor uw MQ-server op.

   * Voor de **Server** kunt u de naam van de MQ-server invoeren of het IP-adres invoeren, gevolgd door een dubbele punt en het poort nummer.

   * Als u Transport Layer Security (TLS) of Secure Sockets Layer (SSL) wilt gebruiken, selecteert u **SSL inschakelen?**.

     De MQ-connector biedt momenteel alleen ondersteuning voor Server verificatie, niet voor client verificatie. Zie [problemen met verbinding en verificatie](#connection-problems)voor meer informatie.

1. Voer in de sectie **Gateway** de volgende stappen uit:

   1. Selecteer in de lijst **abonnement** het Azure-abonnement dat is gekoppeld aan uw Azure gateway-resource.

   1. Selecteer de Azure-gateway resource die u wilt gebruiken in de lijst met **gateways voor verbindingen** .

1. Selecteer **Maken** als u klaar bent.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Verbindings-en verificatie problemen

Wanneer uw logische app verbinding probeert te maken met uw on-premises MQ-server, kan deze fout optreden:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Als u de MQ-connector rechtstreeks in azure gebruikt, moet de MQ-server een certificaat gebruiken dat is uitgegeven door een vertrouwde [certificerings instantie](https://www.ssl.com/faqs/what-is-a-certificate-authority/).

* Als u de on-premises gegevens gateway gebruikt, kunt u, indien mogelijk, een certificaat gebruiken dat is uitgegeven door een vertrouwde [certificerings instantie](https://www.ssl.com/faqs/what-is-a-certificate-authority/) . Als deze optie echter niet mogelijk is, kunt u een zelfondertekend certificaat gebruiken, dat niet wordt uitgegeven door een vertrouwde [certificerings instantie](https://www.ssl.com/faqs/what-is-a-certificate-authority/) . dit wordt beschouwd als minder veilig.

  Als u het zelfondertekende certificaat van de server wilt installeren, kunt u het hulp programma **Windows Certification Manager** (certmgr. msc) gebruiken. Voor dit scenario moet u op de lokale computer waarop de on-premises gegevens Gateway-Service wordt uitgevoerd, het certificaat installeren in het certificaten archief van uw **lokale computer** op het niveau van de **vertrouwde basis certificerings instanties** .

  1. Op de computer waarop de on-premises gegevens Gateway-Service wordt uitgevoerd, opent u het menu Start, zoekt en selecteert u **gebruikers certificaten beheren**.

  1. Wanneer het hulp programma Windows-certificerings beheer wordt geopend, gaat u naar de map **certificaten-lokale computer met**  >   **vertrouwde basis certificerings instanties** en installeert u het certificaat.

     > [!IMPORTANT]
     > Zorg ervoor dat u het certificaat installeert in het archief **certificaten-lokale computer**  >  **vertrouwde basis certificerings instanties** .

* De MQ-server vereist dat u de coderings specificatie definieert die u wilt gebruiken voor TLS/SSL-verbindingen. SslStream in .NET biedt echter geen toestemming om de volg orde voor coderings specificaties op te geven. Als u deze beperking wilt omzeilen, kunt u de MQ-server configuratie wijzigen zodat deze overeenkomt met de eerste coderings specificatie in de suite die de connector verzendt in de TLS/SSL-onderhandeling.

  Wanneer u de verbinding probeert te gebruiken, registreert de MQ-server een gebeurtenis bericht dat aangeeft dat de verbinding is mislukt, omdat het andere end de onjuiste coderings specificatie heeft gebruikt. Het gebeurtenis bericht bevat de coderings specificatie die als eerste wordt weer gegeven in de lijst. Werk de cipher-specificatie in de kanaal configuratie bij zodat deze overeenkomt met de coderings specificatie in het gebeurtenis bericht.

## <a name="browse-single-message"></a>Door één bericht bladeren

1. Selecteer **nieuwe stap** onder de trigger of een andere actie in uw logische app.

1. Typ in het zoekvak `mq` en selecteer de actie **bericht zoeken** .

   ![Selecteer de actie bericht zoeken](media/connectors-create-api-mq/browse-message.png)

1. Als u nog geen MQ-verbinding hebt gemaakt, wordt u gevraagd om [die verbinding te maken](#create-connection).

1. Nadat u de verbinding hebt gemaakt, stelt u de eigenschappen van de actie voor het **Blader bericht** in:

   | Eigenschap | Beschrijving |
   |----------|-------------|
   | **Wachtrij** | Als dit afwijkt van de wachtrij die in de verbinding is opgegeven, geeft u die wachtrij op. |
   | **MessageId**, **CorrelationId**, **GroupId** en andere eigenschappen | Bladeren naar een bericht dat is gebaseerd op de verschillende MQ-bericht eigenschappen |
   | **IncludeInfo** | Als u aanvullende bericht informatie in de uitvoer wilt toevoegen, selecteert u **waar**. Als u aanvullende bericht informatie in de uitvoer wilt weglaten, selecteert u **Onwaar**. |
   | **Out** | Voer een waarde in om te bepalen hoe lang moet worden gewacht tot een bericht in een lege wachtrij arriveert. Als niets wordt ingevoerd, wordt het eerste bericht in de wachtrij opgehaald en wordt er geen tijd besteed aan het wachten op het verschijnen van een bericht. |
   |||

   Bijvoorbeeld:

   ![Eigenschappen voor de actie bericht zoeken](media/connectors-create-api-mq/browse-message-properties.png)

1. Selecteer **Opslaan** op de werkbalk van de ontwerper wanneer u klaar bent. Als u uw app wilt testen, selecteert u **uitvoeren**.

   Nadat de uitvoering is voltooid, worden in de ontwerp functie de werk stroom stappen en hun status weer gegeven, zodat u de uitvoer kunt controleren.

1. Klik op de titel balk van de stap om de details van elke stap weer te geven. Als u meer informatie over de uitvoer van een stap wilt bekijken, selecteert u **onbewerkte uitvoer weer geven**.

   ![Bladeren in bericht uitvoer](media/connectors-create-api-mq/browse-message-output.png)

   Hier volgt een voor beeld van een onbewerkte uitvoer:

   ![Bladeren in bericht onbewerkte uitvoer](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Als u **IncludeInfo** instelt op **True**, wordt extra uitvoer weer gegeven:

   ![Bladeren in bericht gegevens toevoegen](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Door meerdere berichten bladeren

De actie **Bladeren door berichten** bevat een **BatchSize** -optie om aan te geven hoeveel berichten van de wachtrij moeten worden geretourneerd. Als **BatchSize** geen waarde heeft, worden alle berichten geretourneerd. De geretourneerde uitvoer is een matrix van berichten.

1. Volg de vorige stappen, maar Voeg in plaats daarvan de actie **Bladeren berichten** toe.

1. Als u nog geen MQ-verbinding hebt gemaakt, wordt u gevraagd om [die verbinding te maken](#create-connection). Anders wordt de eerste eerder geconfigureerde verbinding standaard gebruikt. Als u een nieuwe verbinding wilt maken, selecteert u **verbinding wijzigen**. Of selecteer een andere verbinding.

1. Geef de informatie op voor de actie.

1. Sla de logische app op en voer deze uit.

   Nadat de logische app is uitgevoerd, ziet u een voor beeld van de uitvoer van de actie **berichten door bladeren** :

   ![Voor beeld van uitvoer van berichten zoeken](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Eén bericht ontvangen

De actie **bericht ontvangen** heeft dezelfde invoer en uitvoer als de actie **bericht bladeren** . Wanneer u **bericht ontvangen** gebruikt, wordt het bericht uit de wachtrij verwijderd.

## <a name="receive-multiple-messages"></a>Meerdere berichten ontvangen

De actie **berichten ontvangen** heeft dezelfde invoer en uitvoer als de actie **berichten door bladeren** . Wanneer u **berichten ontvangen** gebruikt, worden de berichten uit de wachtrij verwijderd.

> [!NOTE]
> Bij het uitvoeren van een Blader bewerking of een ontvangst actie voor een wachtrij die geen berichten bevat, mislukt de actie met deze uitvoer:
>
> ![MQ "geen bericht"-fout](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Bericht verzenden

1. Volg de vorige stappen, maar Voeg in plaats daarvan de actie **bericht verzenden** toe.

1. Als u nog geen MQ-verbinding hebt gemaakt, wordt u gevraagd om [die verbinding te maken](#create-connection). Anders wordt de eerste eerder geconfigureerde verbinding standaard gebruikt. Als u een nieuwe verbinding wilt maken, selecteert u **verbinding wijzigen**. Of selecteer een andere verbinding.

1. Geef de informatie op voor de actie. Selecteer voor **Message type** een geldig bericht type: **Data gram**, **beantwoorden** of **aanvraag**

   ![Eigenschappen voor de actie bericht verzenden](media/connectors-create-api-mq/send-message-properties.png)

1. Sla de logische app op en voer deze uit.

   Nadat de logische app is uitgevoerd, ziet u een voor beeld van de uitvoer van de actie **bericht verzenden** :

   ![Voor beeld van de uitvoer ' bericht verzenden '](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina van de connector](/connectors/mq/)voor technische details, zoals acties en limieten, die worden beschreven in het Swagger-bestand van de connector.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)
