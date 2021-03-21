---
title: Paradigma voor toegang tot software
description: Inzicht in de stroom op hoog niveau van het API-aanroep patroon voor programmatische analyse. De Api's voor toegang tot analyse rapporten in micro soft Commercial Marketplace worden ook behandeld.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 8e0b94a46e96dd8ba16040e16b421520eb67de19
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583866"
---
# <a name="programmatic-access-paradigm"></a>Paradigma voor toegang tot software

Dit diagram toont het API-aanroep patroon dat wordt gebruikt om een nieuwe rapport sjabloon te maken, het aangepaste rapport te plannen en fout gegevens op te halen.

[ ![ Illustreert het API-aanroep patroon dat wordt gebruikt om een nieuwe rapport sjabloon te maken, het aangepaste rapport te plannen en fout gegevens op te halen.](./media/analytics-programmatic-access/analytics-high-level-flow.png)](./media/analytics-programmatic-access/analytics-high-level-flow.png#lightbox) 
 ***Afbeelding 1: stroom op hoog niveau van het API-aanroep patroon***

In deze lijst vindt u meer informatie over afbeelding 1.

1. De client toepassing kan het aangepaste rapport schema/de sjabloon definiëren door de [Create report query-API](#create-report-query-api)aan te roepen. U kunt ook een rapport sjabloon ( `QueryId` ) gebruiken uit de [lijst met systeem query's](analytics-system-queries.md).
1. Als de sjabloon voor het maken van het rapport is voltooid, wordt de weer gegeven `QueryId` .
1. De client toepassing roept vervolgens de [Create report API](#create-report-api) aan met behulp `QueryID` van de begin datum van het rapport, het herhalings interval, het terugkeer patroon en een optionele call back-URI.
1. Als de API wordt [gemaakt](#create-report-api) , wordt de geretourneerd `ReportID` .
1. De client toepassing ontvangt een melding bij de call back-URI zodra de rapport gegevens gereed zijn om te worden gedownload.
1. De client toepassing gebruikt vervolgens de [API rapport uitvoeringen ophalen](#get-report-executions-api) om de status van het rapport met het datum bereik op te vragen `Report ID` .
1. Bij voltooiing wordt de koppeling voor het downloaden van het rapport geretourneerd en kan de toepassing het downloaden van de gegevens initiëren.

## <a name="report-query-language-specification"></a>Taal specificatie rapport query

Hoewel we [systeem query's](analytics-system-queries.md) bieden die u kunt gebruiken om rapporten te maken, kunt u ook uw eigen query's maken op basis van de behoeften van uw bedrijf. Zie [aangepaste query specificatie](analytics-custom-query-specification.md)voor meer informatie over aangepaste query's.

## <a name="create-report-query-api"></a>Rapport query-API maken

Met deze API kunt u aangepaste query's maken om de gegevensset te definiëren waaruit de kolommen en metrische gegevens moeten worden geëxporteerd. De API biedt de flexibiliteit om een nieuwe rapportage sjabloon te maken op basis van de behoeften van uw bedrijf.

U kunt ook de door ons verstrekte [systeem query's](analytics-system-queries.md) gebruiken. Als u geen aangepaste rapport sjablonen nodig hebt, kunt u de [Create report API](#create-report-api) direct gebruiken met behulp van de [QueryIds](analytics-system-queries.md) van de systeem query's die we opgeven.

In het volgende voor beeld ziet u hoe u een aangepaste query maakt om _genormaliseerd gebruik en geschatte financiële kosten te verkrijgen voor betaalde sku's_ uit de [ISVUsage](analytics-make-your-first-api-call.md#programmatic-api-call) -gegevensset voor de afgelopen maand.

*Syntaxis van aanvraag*

| Methode | Aanvraag-URI |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries` |
|||

*Aanvraag header*

| Header | Type | Beschrijving |
| ------------- | ------------- | ------------- |
| Autorisatie | tekenreeks | Vereist. Het toegangs token Azure Active Directory (Azure AD). De indeling is `Bearer <token>`. |
| Content-Type | `string` | `application/JSON` |
||||

*Para meter Path*

Geen

*Queryparameter*

Geen

*Voor beeld van Payload aanvragen*

```json
{
    "Name": "ISVUsageQuery",
    "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
    "Query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"
}
```

*Woordenlijst*

Deze tabel bevat de belangrijkste definities van elementen in de aanvraag lading.

| Parameter | Vereist | Beschrijving | Toegestane waarden |
| ------------ | ------------- | ------------- | ------------- |
| `Name` | Ja | Beschrijvende naam van de query | tekenreeks |
| `Description` | No | Beschrijving van de retour waarde van de query | tekenreeks |
| `Query` | Ja | Query teken reeks rapport | Gegevens type: teken reeks<br>[Aangepaste query](analytics-sample-queries.md) op basis van bedrijfs behoeften |
|||||

> [!NOTE]
> Zie voor [beelden van voorbeeld query's](analytics-sample-queries.md)voor voor beelden van aangepaste query's.

*Voorbeeld antwoord*

De nettolading van de reactie is als volgt gestructureerd:

Antwoord codes: 200, 400, 401, 403, 500

Voor beeld van een nettolading van antwoorden:

```json
{
  "value": [
        {
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "name": " ISVUsageQuery",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs”,
            "query": " SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "type": "userDefined",
            "user": "142344300",
            "createdTime": "2021-01-06T05:38:34Z"
        }
    ],
    "totalCount": 1,
    "message": "Query created successfully",
    "statusCode": 200
}
```

*Woordenlijst*

Deze tabel bevat de belangrijkste definities van elementen in het antwoord.

| Parameter | Beschrijving |
| ------------ | ------------- |
| `QueryId` | UUID (Universally Unique Identifier) van de query die u hebt gemaakt |
| `Name` | Beschrijvende naam die wordt gegeven aan de query in de aanvraag lading |
| `Description` | Beschrijving gegeven tijdens het maken van de query |
| `Query` | Rapport query door gegeven als invoer tijdens het maken van query's |
| `Type` | Ingesteld op `userDefined` |
| `User` | Gebruikers-ID die is gebruikt voor het maken van de query |
| `CreatedTime` | UTC-tijd waarop de query is gemaakt met de volgende indeling: JJJJ-MM-DDTuu: mm: ssZ |
| `TotalCount` | Aantal gegevens sets in de waarde-matrix |
| `StatusCode` | Resultaatcode<br>De mogelijke waarden zijn 200, 400, 401, 403, 500 |
| `message` | Status bericht van de uitvoering van de API |
|||

## <a name="create-report-api"></a>Rapport-API maken

Bij het maken van een aangepast rapport sjabloon en het ontvangen `QueryID` als onderdeel van het antwoord [rapport query maken](#create-report-query-api) , kan deze API worden aangeroepen om een query te plannen die met regel matige tussen pozen moet worden uitgevoerd. U kunt een frequentie en schema instellen voor het afleveren van het rapport. Voor systeem query's die we opgeven, kan de Create report API ook worden aangeroepen met [QueryId](analytics-sample-queries.md).

*Syntaxis van aanvraag*

| Methode | Aanvraag-URI |
| ------------ | ------------- |
| POST | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport` |
|||

*Aanvraag header*

| Header | Type | Beschrijving |
| ------ | ---- | ----------- |
| Autorisatie | tekenreeks | Vereist. Het toegangs token Azure Active Directory (Azure AD). De indeling is `Bearer <token>`. |
| Type inhoud | tekenreeks | `application/JSON` |
||||

*Para meter Path*

Geen

*Queryparameter*

Geen

*Voor beeld van Payload aanvragen*

```json
{
  "ReportName": "ISVUsageReport",
  "Description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
  "QueryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c ",
  "StartTime": "2021-01-06T19:00:00Z ",
  "RecurrenceInterval": 48,
  "RecurrenceCount": 20,
  "Format": "csv",
  "CallbackUrl": "https://<SampleCallbackUrl>"
}
```

*Woordenlijst*

Deze tabel bevat de belangrijkste definities van elementen in de aanvraag lading.

| Parameter | Vereist | Beschrijving | Toegestane waarden |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Ja | Naam die aan het rapport is toegewezen | tekenreeks |
| `Description` | No | Beschrijving van het gemaakte rapport | tekenreeks |
| `QueryId` | Ja | Rapport query-ID | tekenreeks |
| `StartTime` | Ja | UTC-tijds tempel waarop het genereren van het rapport wordt gestart.<br>De notatie moet zijn: JJJJ-MM-DDTuu: mm: ssZ | tekenreeks |
| `RecurrenceInterval` | Ja | De frequentie in uren waarmee het rapport moet worden gegenereerd.<br>De minimum waarde is 4 en de maximum waarde is 90. | geheel getal |
| `RecurrenceCount` | Nee | Aantal rapporten dat moet worden gegenereerd. | geheel getal |
| `Format` | Nee | Bestands indeling van het geëxporteerde bestand.<br>De standaard indeling is. Bestand. | CSV/TSV |
| `CallbackUrl` | Nee | Een openbaar bereik bare URL die optioneel kan worden geconfigureerd als de Terugbel bestemming. | Teken reeks (http-URL) |
| `ExecuteNow` | Nee | Deze para meter moet worden gebruikt om een rapport te maken dat slechts één keer wordt uitgevoerd. `StartTime`, `RecurrenceInterval` en `RecurrenceCount` worden genegeerd als deze is ingesteld op `true` . Het rapport wordt direct op asynchrone wijze uitgevoerd. | waar/onwaar |
| `QueryStartTime` | Nee | Hiermee geeft u eventueel de start tijd op voor de query die de gegevens ophaalt. Deze para meter is alleen van toepassing voor eenmalig uitvoerings rapport dat is `ExecuteNow` ingesteld op `true` . De notatie moet JJJJ-MM-DDTuu: mm: ssZ | Tijds tempel als teken reeks |
| `QueryEndTime` | Nee | Hiermee geeft u eventueel de eind tijd op voor de query die de gegevens ophaalt. Deze para meter is alleen van toepassing voor eenmalig uitvoerings rapport dat is `ExecuteNow` ingesteld op `true` . De notatie moet JJJJ-MM-DDTuu: mm: ssZ | Tijds tempel als teken reeks |
|||||

*Voorbeeldantwoord*

De nettolading van de reactie is als volgt gestructureerd:

Antwoord code: 200, 400, 401, 403, 404, 500

Nettolading van reactie:

```json
{
  "Value": [
    {
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "reportName": "ISVUsageReport",
            "description": "Normalized Usage and Estimated Financial Charges for PAID SKUs",
            "queryId": "78be43f2-e35f-491a-8cd5-78fe14194f9c",
            "query": "SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paid' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH",
            "user": "142344300",
            "createdTime": "2021-01-06T05:46:00Z",
            "modifiedTime": null,
            "startTime": "2021-01-06T19:00:00Z",
            "reportStatus": "Active",
            "recurrenceInterval": 48,
            "recurrenceCount": 20,
            "callbackUrl": "https://<SampleCallbackUrl>",
            "format": "csv"    
    }
  ],
  "TotalCount": 1,
  "Message": "Report created successfully",
  "StatusCode": 200
}
```

*Woordenlijst*

Deze tabel bevat de belangrijkste definities van elementen in het antwoord.

| Parameter | Beschrijving |
| ------------ | ------------- |
| `ReportId` | UUID (Universally Unique Identifier) van het rapport dat u hebt gemaakt |
| `ReportName` | De naam die aan het rapport is gegeven in de aanvraag lading |
| `Description` | Beschrijving gegeven tijdens het maken van het rapport |
| `QueryId` | Query-ID die is door gegeven op het moment dat het rapport is gemaakt |
| `Query` | Query tekst die voor dit rapport wordt uitgevoerd |
| `User` | Gebruikers-ID die is gebruikt om het rapport te maken |
| `CreatedTime` | UTC-tijd waarop het rapport is gemaakt met de volgende indeling: JJJJ-MM-DDTuu: mm: ssZ |
| `ModifiedTime` | UTC-tijd waarop het rapport het laatst is gewijzigd in deze indeling: JJJJ-MM-DDTuu: mm: ssZ |
| `StartTime` | UTC-tijd van de uitvoering van het rapport begint met deze notatie: JJJJ-MM-DDTuu: mm: ssZ |
| `ReportStatus` | De status van de rapport uitvoering. De mogelijke waarden worden **onderbroken**, **actief** en **inactief**. |
| `RecurrenceInterval` | Het interval voor het terugkeer patroon tijdens het maken van het rapport |
| `RecurrenceCount` | Aantal herhalingen dat is gegeven tijdens het maken van het rapport |
| `CallbackUrl` | Call back-URL die in de aanvraag is opgenomen |
| `Format` | Indeling van de rapport bestanden. De mogelijke waarden zijn CSV of TSV. |
| `TotalCount` | Aantal gegevens sets in de waarde-matrix |
| `StatusCode` | Resultaatcode<br>De mogelijke waarden zijn 200, 400, 401, 403, 500 |
| `message` | Status bericht van de uitvoering van de API |
|||

## <a name="get-report-executions-api"></a>API voor rapport uitvoeringen ophalen

U kunt deze methode gebruiken om de status van een rapport uitvoering op te vragen met behulp `ReportId` van de ontvangen [rapport-API](#create-report-api). De-methode retourneert de koppeling voor het downloaden van rapporten als het rapport gereed is om te worden gedownload. Anders retourneert de methode de status. U kunt deze API ook gebruiken om alle uitvoeringen op te halen die voor een bepaald rapport hebben plaatsgevonden.

> [!IMPORTANT]
> Voor deze API zijn standaard query parameters ingesteld voor `executionStatus=Completed` en  `getLatestExecution=true` . Daarom wordt het aanroepen van de API voordat de eerste geslaagde uitvoering van het rapport 404 retourneert. Uitvoeringen in behandeling kunnen worden verkregen door in te stellen `executionStatus=Pending` .

*Syntaxis van aanvraag*

| Methode | Aanvraag-URI |
| ------------ | ------------- |
| Ophalen | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/execution/{reportId}?executionId={executionId}&executionStatus={executionStatus}&getLatestExecution={getLatestExecution}` |
|||

*Aanvraag header*

| Header | Type | Beschrijving |
| ------ | ------ | ------ |
| Autorisatie | tekenreeks | Vereist. Het toegangs token Azure Active Directory (Azure AD). De indeling is `Bearer <token>`. |
| Inhoudstype | tekenreeks | `application/json` |
||||

*Para meter Path*

Geen

*Queryparameter*

| Parameternaam | Vereist | Type | Beschrijving |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Ja | tekenreeks | Filter om uitvoerings Details op te halen van alleen rapporten die `reportId` in dit argument zijn opgegeven. U `reportIds` kunt meerdere opgeven door deze te scheiden met een punt komma '; '. |
| `executionId` | Nee | tekenreeks | Filter om details op te halen van alleen rapporten die `executionId` in dit argument staan vermeld. U `executionIds` kunt meerdere opgeven door deze te scheiden met een punt komma '; '. |
| `executionStatus` | Nee | teken reeks/Enum | Filter om details op te halen van alleen rapporten die `executionStatus` in dit argument staan vermeld.<br>Geldige waarden zijn: `Pending` , `Running` , `Paused` en `Completed` <br>De standaardwaarde is `Completed`. U kunt meerdere statussen opgeven door deze te scheiden met een punt komma '; '. |
| `getLatestExecution` | Nee | booleaans | De API retourneert Details van de meest recente rapport uitvoering.<br>Deze para meter is standaard ingesteld op `true` . Als u ervoor kiest om de waarde van deze para meter door te geven als `false` , retourneert de API de laatste instanties van 90 dagen. |
|||||

*Payload aanvragen*

Geen

*Voorbeeldantwoord*

De nettolading van de reactie is als volgt gestructureerd:

Antwoord codes: 200, 400, 401, 403, 404, 500

Voor beeld van een nettolading van antwoorden:

```json
{
    "value": [
        {
            "executionId": "a0bd78ad-1a05-40fa-8847-8968b718d00f",
            "reportId": "72fa95ab-35f5-4d44-a1ee-503abbc88003",
            "recurrenceInterval": 4,
            "recurrenceCount": 10,
            "callbackUrl": null,
            "format": "csv",
            "executionStatus": "Completed",
            "reportAccessSecureLink": "https://<path to report for download>",
            "reportExpiryTime": null,
            "reportGeneratedTime": "2021-01-13T14:40:46Z"
        }
    ],
    "totalCount": 1,
    "message": null,
    "statusCode": 200
}
```

Zodra de uitvoering van het rapport is voltooid, wordt de uitvoerings status `Completed` weer gegeven. U kunt het rapport downloaden door de URL te selecteren in de `reportAccessSecureLink` .

*Woordenlijst*

De belangrijkste definities van elementen in het antwoord.

| Parameter | Beschrijving |
| ------------ | ------------- |
| `ExecutionId` | UUID (Universally Unique Identifier) van het uitvoerings exemplaar |
| `ReportId` | Rapport-ID gekoppeld aan het uitvoerings exemplaar |
| `RecurrenceInterval` | Het interval voor het terugkeer patroon tijdens het maken van het rapport |
| `RecurrenceCount` | Aantal herhalingen dat is gegeven tijdens het maken van het rapport |
| `CallbackUrl` | De call back-URL die is gekoppeld aan het uitvoerings exemplaar |
| `Format` | Indeling van het gegenereerde bestand aan het einde van de uitvoering |
| `ExecutionStatus` | Status van het exemplaar van het rapport uitvoer.<br>Geldige waarden zijn: `Pending` , `Running` , `Paused` en `Completed` |
| `ReportAccessSecureLink` | Koppeling waarmee het rapport veilig kan worden geopend |
| `ReportExpiryTime` | UTC-tijd waarna de rapport koppeling verloopt in deze notatie: JJJJ-MM-DDTuu: mm: ssZ |
| `ReportGeneratedTime` | UTC-tijd waarop het rapport is gegenereerd met de volgende indeling: JJJJ-MM-DDTuu: mm: ssZ |
| `TotalCount` | Aantal gegevens sets in de waarde-matrix |
| `StatusCode` | Resultaatcode <br>De mogelijke waarden zijn 200, 400, 401, 403, 404 en 500 |
| `message` | Status bericht van de uitvoering van de API |
|||

## <a name="next-steps"></a>Volgende stappen
- U kunt de Api's uitproberen via de [URL van SWAGGER API](https://api.partnercenter.microsoft.com/insights/v1/cmp/swagger/index.html)
- Aan de slag met programmatische toegang tot analyse gegevens
