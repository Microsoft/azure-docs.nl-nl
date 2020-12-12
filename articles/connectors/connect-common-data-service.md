---
title: Verbinding maken met Common Data Service (micro soft Dataverse)
description: Common Data Service-records (micro soft Dataverse) met Azure Logic Apps maken en beheren
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 12/11/2020
tags: connectors
ms.openlocfilehash: b17c3d54b7065a18e015363a0362766f844e4e10
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355114"
---
# <a name="create-and-manage-records-in-common-data-service-microsoft-dataverse-by-using-azure-logic-apps"></a>Records maken en beheren in Common Data Service (micro soft Dataverse) met behulp van Azure Logic Apps

> [!NOTE]
> In november 2020 is Common Data Service gewijzigd in micro soft Dataverse.

Met [Azure Logic apps](../logic-apps/logic-apps-overview.md) en de [common data service-connector](/connectors/commondataservice/)kunt u geautomatiseerde werk stromen bouwen waarmee records in uw [common data service, nu micro soft Dataverse-data base, worden](/powerapps/maker/common-data-service/data-platform-intro) beheerd. Met deze werk stromen kunnen records worden gemaakt, records worden bijgewerkt en andere bewerkingen worden uitgevoerd. U kunt ook gegevens uit uw Common Data Service-data base ophalen en de uitvoer beschikbaar maken voor andere acties die u in uw logische app kunt gebruiken. Wanneer bijvoorbeeld een record wordt bijgewerkt in uw Common Data Service-data base, kunt u een e-mail bericht verzenden met behulp van de Office 365 Outlook-Connector.

Dit artikel laat zien hoe u een logische app kunt maken waarmee een taak record wordt gemaakt wanneer er een nieuwe lead record wordt aangemaakt.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Een [common data service omgeving](/power-platform/admin/environments-overview), een ruimte waar uw organisatie Bedrijfs gegevens en een common data service data base opslaat, beheert en deelt. Zie de volgende bronnen voor meer informatie:<p>

  * [Meer informatie: aan de slag met Common Data Service](/learn/modules/get-started-with-powerapps-common-data-service/)
  * [Power platform-overzicht van omgevingen](/power-platform/admin/environments-overview)

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md) en de logische app van waaruit u toegang wilt krijgen tot de records in uw common data service-data base. Als u uw logische app wilt starten met een Common Data Service trigger, hebt u een lege logische app nodig. Als u niet bekend bent met Azure Logic Apps, raadpleegt u [Quick Start: uw eerste werk stroom maken met behulp van Azure Logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-common-data-service-trigger"></a>Common Data Service trigger toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Voor dit voor beeld voegt u de trigger Common Data Service toe die wordt geactiveerd wanneer een nieuwe record wordt gemaakt.

1. Open in de [Azure Portal](https://portal.azure.com)uw lege logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Typ `common data service` in het zoekvak. Voor dit voor beeld selecteert u onder de lijst triggers deze trigger: **Wanneer een record wordt gemaakt**

   ![De trigger ' wanneer een record wordt gemaakt ' selecteren](./media/connect-common-data-service/select-when-record-created-trigger.png)

1. Meld u aan bij de Common Data Service als u hierom wordt gevraagd.

1. Geef in de trigger informatie op over de omgeving waarin u de nieuwe lead records wilt bewaken, bijvoorbeeld:

   ![Informatie over het activeren van de omgeving die moet worden bewaakt](./media/connect-common-data-service/when-record-created-trigger-details.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Omgeving** | Yes | De omgeving waarin u bijvoorbeeld ' fabrikam Sales production ' wilt bewaken. Zie [overzicht van Power platform-omgevingen](/power-platform/admin/environments-overview)voor meer informatie. |
   | **Naam van entiteit** | Yes | De entiteit die u wilt bewaken, bijvoorbeeld ' leads ' |
   | **Bereik** | Yes | De bron die de nieuwe record heeft gemaakt, bijvoorbeeld een gebruiker in uw bedrijfs eenheid of een gebruiker in uw organisatie. In dit voor beeld wordt ' Business Unit ' gebruikt. |
   ||||

## <a name="add-common-data-service-action"></a>Common Data Service actie toevoegen

Voeg nu een Common Data Service actie toe waarmee een taak record voor een nieuwe lead record wordt gemaakt.

1. Selecteer **nieuwe stap** onder de trigger **Wanneer een record wordt gemaakt** .

1. Typ `common data service` in het zoekvak. Selecteer in de lijst acties deze actie: **een nieuwe record maken**

   ![Selecteer de actie een nieuwe record maken](./media/connect-common-data-service/select-create-new-record-action.png)

1. Geef in de actie de informatie op over de omgeving waarin u de nieuwe taak record wilt maken. Indien beschikbaar, worden ook andere eigenschappen weer gegeven op basis van de entiteit die u voor deze actie hebt geselecteerd, bijvoorbeeld:

   ![Actie-informatie voor de omgeving waar de record moet worden gemaakt](./media/connect-common-data-service/create-new-record-action-details.png)

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Naam van organisatie** | Yes | De omgeving waarin u de record wilt maken, die niet dezelfde omgeving in uw trigger moet zijn, maar ' fabrikam Sales production ' in dit voor beeld is |
   | **Naam van entiteit** | Yes | De entiteit waar u de record wilt maken, bijvoorbeeld ' taken ' |
   | **Onderwerp** | Ja, op basis van de entiteit die in dit voor beeld is geselecteerd | Een korte beschrijving van de doel stelling voor deze taak |
   ||||

   1. Voer voor de eigenschap **onderwerp** de volgende tekst in met een spatie:

      `Follow up with new lead:`

   1. Bewaar uw aanwijzer in het vak **onderwerp** zodat de lijst met dynamische inhoud zichtbaar blijft.
   
   1. Selecteer in de lijst van de sectie **Wanneer een record wordt gemaakt** de trigger uitvoer die u wilt opnemen in de taak record, bijvoorbeeld:

      ![Trigger uitvoer selecteren voor gebruik in taak record](./media/connect-common-data-service/create-new-record-action-select-trigger-outputs.png)

      | Uitvoer activeren | Description |
      |----------------|-------------|
      | **Voornaam** | De voor naam van de lead record die moet worden gebruikt als de primaire contact persoon in de taak record |
      | **Achternaam** | De achternaam van de lead record die moet worden gebruikt als de primaire contact persoon in de taak record |
      | **Beschrijving** | Andere uitvoer die moet worden opgenomen in de taak record, zoals e-mail adres en zakelijk telefoon nummer |
      |||

   Wanneer u klaar bent, kan de actie eruitzien als in dit voor beeld:

   ![De actie ' een nieuwe record maken ' is voltooid](./media/connect-common-data-service/finished-create-record-action-details.png)

1. Sla uw logische app op. Selecteer **Opslaan** op de werkbalk van de ontwerper.

1. Als u de logische app hand matig wilt starten, selecteert u **uitvoeren** op de werk balk van de ontwerp functie. Als u uw logische app wilt testen, maakt u een nieuwe lead record.

## <a name="trigger-only-on-updated-attributes"></a>Alleen activeren bij bijgewerkte kenmerken

Voor triggers die worden uitgevoerd wanneer records worden bijgewerkt, zoals de actie **Wanneer een record wordt bijgewerkt** , kunt u filter kenmerken gebruiken zodat uw logische app alleen wordt uitgevoerd wanneer de opgegeven kenmerken worden bijgewerkt. Deze mogelijkheid helpt u om te voor komen dat de logische app wordt uitgevoerd.

1. Selecteer **kenmerk filters** in de lijst **nieuwe para meter toevoegen** van de trigger.

   ![Scherm afbeelding met de actie ' wanneer een record wordt bijgewerkt ' en de lijst ' nieuwe para meter toevoegen ' met de eigenschap ' attribute filters ' geselecteerd.](./media/connect-common-data-service/when-record-updated-trigger-add-attribute-filters.png)

1. Selecteer voor elk **item kenmerk filters** het kenmerk dat u wilt controleren op updates, bijvoorbeeld:

   ![Eigenschap attribute filters toevoegen](./media/connect-common-data-service/when-record-updated-trigger-select-attribute-filter.png)

## <a name="list-records-based-on-a-filter"></a>Records weer geven op basis van een filter

Voor acties die records retour neren, zoals de actie **records weer** geven, kunt u een ODATA-query gebruiken waarmee records worden geretourneerd op basis van het opgegeven filter. U hebt bijvoorbeeld de actie lijst alleen de records voor actieve accounts.

1. Open in de actie de lijst **nieuwe para meter toevoegen** en selecteer de query-eigenschap **filteren** .

   ![De eigenschap filter query toevoegen](./media/connect-common-data-service/list-records-action-filter-query.png)

1. Voer in de **filter query** -eigenschap die nu wordt weer gegeven in de actie deze ODATA-filter query in: `statuscode eq 1`

   ![ODATA-filter query voor het filteren van records invoeren](./media/connect-common-data-service/list-records-action-filter-query-value.png)

`$filter`Zie [common data service-filter resultaten](/powerapps/developer/common-data-service/webapi/query-data-web-api#filter-results)voor meer informatie over opties voor systeem query's.

## <a name="list-records-based-on-an-order"></a>Records weer geven op basis van een order

Voor acties die records retour neren, zoals de actie **records lijst** , kunt u een ODATA-query gebruiken waarmee records worden geretourneerd in een opgegeven volg orde, die varieert op basis van de records die de actie retourneert. U kunt de actie bijvoorbeeld de records laten weer geven op basis van de account naam.

1. Open in de actie de lijst **nieuwe para meter toevoegen** en selecteer de eigenschap **order by** .

   ![Eigenschap Order by toevoegen](./media/connect-common-data-service/list-records-action-order-by.png)

1. Voer in de eigenschap **order by** die nu wordt weer gegeven in de actie deze ODATA-filter query in: `name`

   ![ODATA-filter query voor het ordenen van records invoeren](./media/connect-common-data-service/list-records-action-order-by-value.png)

`$orderby`Zie [common data service-order resultaten](/powerapps/developer/common-data-service/webapi/query-data-web-api#order-results)voor meer informatie over opties voor systeem query's.

## <a name="field-data-types"></a>Veld gegevens typen

Ongeacht of u hand matig een waarde invoert of een waarde selecteert uit de lijst met dynamische inhoud voor een veld in een trigger of actie, het gegevens type van de waarde moet overeenkomen met het vereiste gegevens type van het veld.

In deze tabel worden enkele veld typen en de gegevens typen beschreven die voor deze velden zijn vereist.

| Veld | Gegevenstype | Beschrijving |
|-------|-----------|-------------|
| Tekst veld | Eén tekstregel | Vereist een enkele tekst regel of dynamische inhoud met het gegevens type Text, bijvoorbeeld deze eigenschappen: <p><p>- **Beschrijvingen** <br>- **Rubriek** |
| Veld met gehele getallen | Geheel getal | Vereist een geheel getal of dynamische inhoud met het gegevens type geheel getal, bijvoorbeeld deze eigenschappen: <p><p>- **Percentage voltooid** <br>- **Hebben** |
| Datum veld | Datum en tijd | Vereist een datum in de notatie MM/DD/YYY of dynamische inhoud met het gegevens type datum, bijvoorbeeld deze eigenschappen: <p><p>- **Gemaakt op** <br>- **Begin datum** <br>- **Werkelijke begin datum** <br>- **Werkelijk einde** <br>- **Verval datum** |
| Veld dat verwijst naar een andere entiteits record | Primaire sleutel | Vereist zowel een record-ID, zoals een GUID, als een opzoek type, wat betekent dat waarden van de lijst met dynamische inhoud niet werken, bijvoorbeeld deze eigenschappen: <p><p>- **Eigenaar**: moet een geldige gebruikers-id of een team record-id zijn. <br>- **Type eigenaar**: moet een opzoek type zijn, zoals `systemusers` of `teams` . <p><p>- **Over**: moet een geldige record-id zijn, zoals een account-id of een record-id van een contact persoon. <br>- **Met betrekking tot type**: moet een opzoek type zijn `accounts` , zoals of `contacts` . <p><p>- **Klant**: moet een geldige record-id zijn, zoals een account-id of record-id van een contact persoon. <br>- **Klant type**: moet het type lookup zijn, zoals `accounts` of `contacts` . |
||||

Dit voor beeld laat zien hoe met de actie **een nieuwe record maken** een nieuwe "tasks record" wordt gemaakt die is gekoppeld aan andere entiteit records, met name een gebruikers record en een account record. De actie specificeert de Id's en zoek typen voor die entiteit records met behulp van waarden die overeenkomen met de verwachte gegevens typen voor de relevante eigenschappen.

* Op basis van de eigenschap **eigenaar** , die een gebruikers-id opgeeft, en de eigenschap **type eigenaar** , die het `systemusers` type lookup specificeert, koppelt de actie het nieuwe "tasks"-record aan een specifieke gebruiker.

* Op basis van de eigenschap **betreft** , waarmee een record-id wordt opgegeven, en de eigenschap **over het type** ', waarmee het opzoek type wordt opgegeven, wordt `accounts` de nieuwe "tasks record" gekoppeld aan een specifiek account.

![Een "tasks"-record maken die is gekoppeld aan Id's en opzoek typen](./media/connect-common-data-service/create-new-record-task-properties.png)

## <a name="connector-reference"></a>Connector-verwijzing

Zie de [referentie pagina van de connector](/connectors/commondataservice/)voor technische informatie op basis van de Swagger-beschrijving van de connector, zoals triggers, acties, limieten en andere details.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over andere [connectors voor Azure Logic apps](../connectors/apis-list.md)
