---
title: B2B-berichten bewaken met behulp van Azure Monitor
description: Problemen met AS2-, X12-en EDIFACT-berichten oplossen door Azure Monitor Logboeken in te stellen en diagnostische gegevens te verzamelen voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 765c15897bd5d435503d3bef07e76a93b148971c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596862"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Azure Monitor-logboeken instellen en diagnostische gegevens verzamelen voor B2B-berichten in Azure Logic Apps

Nadat u B2B-communicatie tussen handels partners in uw integratie account hebt ingesteld, kunnen deze partners berichten uitwisselen met behulp van protocollen zoals AS2, X12 en EDIFACT. Als u wilt controleren of deze communicatie werkt zoals verwacht, kunt u [Azure monitor logboeken](../azure-monitor/logs/data-platform-logs.md) instellen voor uw integratie account. [Azure monitor](../azure-monitor/overview.md) helpt u bij het bewaken van uw Cloud-en on-premises omgevingen, zodat u hun Beschik baarheid en prestaties gemakkelijker kunt onderhouden. Met Azure Monitor-Logboeken kunt u gegevens over runtime gegevens en-gebeurtenissen vastleggen en opslaan, zoals trigger gebeurtenissen, uitvoer gebeurtenissen en actie gebeurtenissen in een [log Analytics-werk ruimte](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). Voor berichten verzamelt ook logboek registratie informatie zoals:

* Aantal berichten en status
* Status van bevestigingen
* Correlaties tussen berichten en bevestigingen
* Gedetailleerde fout beschrijvingen voor fouten

Met Azure Monitor kunt u [logboek query's](../azure-monitor/logs/log-query-overview.md) maken waarmee u deze gegevens kunt vinden en controleren. U kunt [deze diagnostische gegevens ook gebruiken met andere Azure-Services](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data), zoals Azure Storage en Azure Event hubs.

Als u logboek registratie wilt instellen voor uw integratie account, [installeert u de logische apps B2B oplossing](#install-b2b-solution) in de Azure Portal. Deze oplossing biedt geaggregeerde informatie over evenementen voor B2B-berichten. Stel vervolgens [Azure monitor-logboeken](#set-up-resource-logs)in om logboek registratie in te scha kelen en query's te maken voor deze informatie.

In dit artikel wordt beschreven hoe u Azure Monitor logboek registratie inschakelt voor uw integratie account.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten

* Een Log Analytics-werkruimte. Als u geen Log Analytics-werk ruimte hebt, leert u [hoe u een log Analytics-werk ruimte maakt](../azure-monitor/logs/quick-create-workspace.md).

* Een logische app die is ingesteld met Azure Monitor logboek registratie en verzendt deze gegevens naar een Log Analytics-werk ruimte. Meer informatie [over het instellen van Azure monitor-logboeken voor uw logische app](../logic-apps/monitor-logic-apps.md).

* Een integratie account dat is gekoppeld aan uw logische app. Meer informatie [over het koppelen van uw integratie account aan uw logische app](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Logische apps B2B oplossing installeren

Voordat Azure Monitor Logboeken de B2B-berichten voor uw logische app kunt volgen, voegt u de **logische apps B2B** oplossing toe aan uw log Analytics-werk ruimte.

1. Voer in het zoekvak van de [Azure Portal](https://portal.azure.com)in `log analytics workspaces` en selecteer vervolgens **log Analytics werk ruimten**.

   ![Selecteer ' Log Analytics werk ruimten '](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. Selecteer onder **log Analytics werk ruimten** uw werk ruimte.

   ![Uw Log Analytics-werk ruimte selecteren](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. Selecteer in het deel venster Overzicht onder **aan de slag met log Analytics**  >  **bewakings oplossingen configureren** de optie **oplossingen weer geven**.

   ![Selecteer in het deel venster Overzicht de optie oplossingen weer geven.](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. Selecteer in het deel venster Overzicht de optie **toevoegen**.

   ![Voeg in het deel venster Overzicht een nieuwe oplossing toe](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. Nadat de **Marketplace** is geopend, voert u in het zoekvak in `logic apps b2b` en selecteert u **logische apps B2B**.

   ![Selecteer ' Logic Apps-beheer ' in Marketplace](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. Selecteer **maken** in het deel venster beschrijving van oplossing.

   ![Selecteer ' maken ' om de oplossing ' Logische apps B2B ' toe te voegen](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Controleer en bevestig de Log Analytics-werk ruimte waar u de oplossing wilt installeren en selecteer opnieuw **maken** .

   ![Selecteer ' maken ' voor ' Logische apps B2B '](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Nadat Azure de oplossing heeft geïmplementeerd voor de Azure-resource groep met uw Log Analytics-werk ruimte, wordt de oplossing weer gegeven in het deel venster samen vatting van de werk ruimte. Wanneer B2B-berichten worden verwerkt, wordt het aantal berichten in dit deel venster bijgewerkt.

   ![Deel venster werkruimte overzicht](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor-logboeken instellen

U kunt Azure Monitor logboek registratie rechtstreeks vanuit uw integratie account inschakelen.

1. Zoek en selecteer uw integratie account in de [Azure Portal](https://portal.azure.com).

   ![Uw integratie account zoeken en selecteren](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. Klik in het menu van het integratie account onder **bewaking** op **Diagnostische instellingen**. Selecteer **Diagnostische instellingen toevoegen**.

   ![Onder bewaking selecteert u Diagnostische instellingen.](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Voer de volgende stappen uit om de instelling te maken:

   1. Geef een naam op voor de instelling.

   1. Selecteer **verzenden naar log Analytics**.

   1. Selecteer bij **abonnement** het Azure-abonnement dat is gekoppeld aan uw log Analytics-werk ruimte.

   1. Voor **log Analytics werk ruimte** selecteert u de werk ruimte die u wilt gebruiken.

   1. Selecteer onder **logboek** de categorie **IntegrationAccountTrackingEvents** , waarmee de gebeurtenis categorie wordt opgegeven die u wilt opnemen.

   1. Selecteer **Opslaan** als u klaar bent.

   Bijvoorbeeld: 

   ![Azure Monitor logboeken instellen voor het verzamelen van diagnostische gegevens](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Bericht status weer geven

Wanneer de logische app wordt uitgevoerd, kunt u de status en gegevens over die berichten bekijken in uw Log Analytics-werk ruimte.

1. Zoek in het zoekvak van [Azure Portal](https://portal.azure.com) uw log Analytics-werk ruimte en open deze.

1. Selecteer in het menu van de werk ruimte de optie **werk ruimte samenvatting**  >  **logische apps B2B**.

   ![Deel venster werkruimte overzicht](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Als de tegel Logische apps B2B niet meteen resultaten weergeeft na een uitvoering, kunt u het beste **vernieuwen** of op korte tijd wachten voordat u het opnieuw probeert.

   Standaard toont de tegel **logische apps B2B** gegevens op basis van één dag. Als u het gegevens bereik wilt wijzigen in een ander interval, selecteert u het bereik besturings element aan de bovenkant van de pagina:

   ![Interval wijzigen](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Nadat het dash board bericht status wordt weer gegeven, kunt u meer informatie weer geven voor een specifiek bericht type, waarin gegevens worden weer gegeven op basis van één dag. Selecteer de tegel voor **AS2**, **X12** of **EDIFACT**.

   ![Statussen voor berichten weer geven](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Er wordt een lijst met berichten weer gegeven voor de gekozen tegel. Zo kan een AS2-berichten lijst er als volgt uitzien:

   ![Statussen en Details voor AS2-berichten](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Zie voor meer informatie over de eigenschappen voor elk bericht type deze beschrijving van de bericht eigenschap:

   * [Eigenschappen van AS2-bericht](#as2-message-properties)
   * [Eigenschappen van X12-bericht](#x12-message-properties)
   * [Eigenschappen van EDIFACT-bericht](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../azure-monitor/logs/log-query-overview.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Beschrijvingen van eigenschappen en naam indelingen voor AS2-, X12-en EDIFACT-berichten

Hier volgen de beschrijvingen van eigenschappen en naam indelingen voor gedownloade bericht bestanden voor elk bericht type.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Beschrijvingen van AS2-bericht eigenschappen

Hier volgen de beschrijvingen van de eigenschappen voor elk AS2-bericht.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Afzender** | De gast partner die is opgegeven in de **instellingen voor ontvangen** of de host-partner die is opgegeven in instellingen voor het **verzenden** van een AS2-overeenkomst |
| **Ontvangst** | De host-partner die is opgegeven in de instellingen voor **ontvangen** of de gast partner die is opgegeven in **instellingen verzenden** voor een AS2-overeenkomst |
| **Logische app** | De logische app waar de AS2 acties worden ingesteld |
| **Status** | De status van het AS2-bericht <br>Geslaagd = er is een geldig AS2-bericht ontvangen of verzonden. Er is geen MDN ingesteld. <br>Geslaagd = er is een geldig AS2-bericht ontvangen of verzonden. MDN wordt ingesteld en ontvangen, of MDN wordt verzonden. <br>Mislukt = er is een ongeldig AS2-bericht ontvangen. Er is geen MDN ingesteld. <br>In behandeling = er is een geldig AS2-bericht ontvangen of verzonden. MDN is ingesteld en MDN wordt verwacht. |
| **TERUG** | De status van het MDN-bericht <br>Geaccepteerd = er is een positief MDN ontvangen of verzonden. <br>In behandeling = wachten op ontvangen of verzenden van een MDN. <br>Afgewezen = er is een negatieve MDN ontvangen of verzonden. <br>Niet vereist = MDN is niet ingesteld in de overeenkomst. |
| **Richting** | De richting van het AS2-bericht |
| **Tracerings-ID** | De ID die overeenkomt met alle triggers en acties in een logische app |
| **Bericht-id** | De AS2 bericht-ID van de AS2-bericht koppen |
| **Tijdstempel** | Het tijdstip waarop de AS2-actie het bericht heeft verwerkt |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Beschrijvingen van X12-bericht eigenschappen

Hier volgen de beschrijvingen van de eigenschappen voor elk X12-bericht.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Afzender** | De gast partner die is opgegeven in de **instellingen voor ontvangen** of de host-partner die is opgegeven in instellingen voor het **verzenden** van een X12-overeenkomst |
| **Ontvangst** | De host-partner die is opgegeven in de instellingen voor **ontvangen** of de gast partner die is opgegeven in **instellingen verzenden** voor een X12-overeenkomst |
| **Logische app** | De logische app waar de X12 acties worden ingesteld |
| **Status** | De status van het X12-bericht <br>Geslaagd = er is een geldig X12-bericht ontvangen of verzonden. Er is geen functionele ACK ingesteld. <br>Geslaagd = er is een geldig X12-bericht ontvangen of verzonden. Functionele ACK is ingesteld en ontvangen, of er wordt een functionele ACK verzonden. <br>Mislukt = er is een ongeldig X12-bericht ontvangen of verzonden. <br>In behandeling = er is een geldig X12-bericht ontvangen of verzonden. Functionele ACK is ingesteld en er wordt een functionele ACK verwacht. |
| **TERUG** | Status van functionele ACK (997) <br>Geaccepteerd = een positieve functionele Ack ontvangen of verzonden. <br>Afgewezen = er is een negatieve functionele Ack ontvangen of verzonden. <br>In behandeling = er is een functionele ACK verwacht, maar deze is niet ontvangen. <br>In behandeling = er is een functionele ACK gegenereerd, maar er kan niet naar de partner worden verzonden. <br>Niet vereist = functionele ACK is niet ingesteld. |
| **Richting** | De richting van het X12-bericht |
| **Tracerings-ID** | De ID die overeenkomt met alle triggers en acties in een logische app |
| **Msg-type** | Het EDI X12-bericht type |
| **ICN** | Het uitwisselings controle nummer voor het X12-bericht |
| **TSCN** | Het controle nummer voor de Transactieset voor het X12-bericht |
| **Tijdstempel** | Het tijdstip waarop de X12-actie het bericht heeft verwerkt |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Beschrijvingen van EDIFACT-bericht eigenschappen

Hier volgen de beschrijvingen van de eigenschappen voor elk EDIFACT-bericht.

| Eigenschap | Beschrijving |
|----------|-------------|
| **Afzender** | De gast partner die is opgegeven in de **instellingen voor ontvangen** of de host-partner die is opgegeven in instellingen voor het **verzenden** van een Edifact-overeenkomst |
| **Ontvangst** | De host-partner die is opgegeven in de instellingen voor **ontvangen** of de gast partner die is opgegeven in **instellingen verzenden** voor een Edifact-overeenkomst |
| **Logische app** | De logische app waar de EDIFACT acties worden ingesteld |
| **Status** | De status van het EDIFACT-bericht <br>Geslaagd = er is een geldig EDIFACT-bericht ontvangen of verzonden. Er is geen functionele ACK ingesteld. <br>Geslaagd = er is een geldig EDIFACT-bericht ontvangen of verzonden. Functionele ACK is ingesteld en ontvangen, of er wordt een functionele ACK verzonden. <br>Mislukt = er is een ongeldig EDIFACT-bericht ontvangen of verzonden <br>In behandeling = er is een geldig EDIFACT-bericht ontvangen of verzonden. Functionele ACK is ingesteld en er wordt een functionele ACK verwacht. |
| **TERUG** | Status van functionele ACK (CONTRL) <br>Geaccepteerd = een positieve functionele Ack ontvangen of verzonden. <br>Afgewezen = er is een negatieve functionele Ack ontvangen of verzonden. <br>In behandeling = er is een functionele ACK verwacht, maar deze is niet ontvangen. <br>In behandeling = er is een functionele ACK gegenereerd, maar er kan niet naar de partner worden verzonden. <br>Niet vereist = functionele ACK is niet ingesteld. |
| **Richting** | De richting van het EDIFACT-bericht |
| **Tracerings-ID** | De ID die overeenkomt met alle triggers en acties in een logische app |
| **Msg-type** | Het EDIFACT-bericht type |
| **ICN** | Het uitwisselings controle nummer voor het EDIFACT-bericht |
| **TSCN** | Het controle nummer voor de Transactieset voor het EDIFACT-bericht |
| **Tijdstempel** | Het tijdstip waarop de EDIFACT-actie het bericht heeft verwerkt |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Volgende stappen

* [Bewakings- en traceringsquery's maken](../logic-apps/create-monitoring-tracking-queries.md)
