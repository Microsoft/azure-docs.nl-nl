---
title: Diagnostische logboeken voor Azure Data Lake Analytics inschakelen en weer geven
description: Meer informatie over het instellen en openen van Diagnostische logboeken voor Azure Data Lake Analytics
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 02/12/2018
ms.openlocfilehash: cd339729f2300ff7e13e7422bf73373b4ce4658e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221006"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Diagnostische logboeken openen voor Azure Data Lake Analytics

Met diagnostische logboek registratie kunt u gegevens toegangscontrole verzamelen. Deze logboeken bevatten informatie zoals:

* Een lijst met gebruikers die toegang hebben tot de gegevens.
* Hoe vaak de gegevens worden geopend.
* Hoeveel gegevens worden opgeslagen in het account.

## <a name="enable-logging"></a>Logboekregistratie inschakelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Open uw Data Lake Analytics-account en selecteer **Diagnostische logboeken** in het gedeelte __monitor__ . Selecteer vervolgens __Diagnostische gegevens inschakelen__.

    ![Scherm opname van de geselecteerde actie ' Diagnostische logboeken ' en ' Diagnostische gegevens inschakelen voor het verzamelen van de volgende logboeken ' gemarkeerd.](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Voer een __naam__ in voor de configuratie van de logboek registratie van __Diagnostische instellingen__ en selecteer vervolgens opties voor logboek registratie.

    ![Diagnostische gegevens inschakelen om audit-en aanvraag logboeken te verzamelen](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Diagnostische logboeken inschakelen")

   * U kunt ervoor kiezen om de gegevens op drie verschillende manieren op te slaan of te verwerken.

     * Selecteer __archiveren naar een opslag account__ om logboeken op te slaan in een Azure-opslag account. Gebruik deze optie als u de gegevens wilt archiveren. Als u deze optie selecteert, moet u een Azure Storage-account opgeven om de logboeken op te slaan.

     * Selecteer **streamen naar een event hub** om logboek gegevens naar een Azure Event hub te streamen. Gebruik deze optie als u een downstream verwerkings pijplijn hebt die inkomende Logboeken in realtime analyseert. Als u deze optie selecteert, moet u de details opgeven voor de Azure Event hub die u wilt gebruiken.

     * Selecteer __verzenden naar log Analytics__ om de gegevens naar de Azure Monitor-service te verzenden. Gebruik deze optie als u Azure Monitor logboeken wilt gebruiken om logboeken te verzamelen en te analyseren.
   * Geef op of u audit Logboeken of aanvraag Logboeken of beide wilt ophalen.  In een aanvraag logboek wordt elke API-aanvraag vastgelegd. In een controle logboek worden alle bewerkingen vastgelegd die worden geactiveerd door die API-aanvraag.

   * Geef voor __Archief naar een opslag account__ het aantal dagen op dat de gegevens moeten worden bewaard.

   * Klik op __Opslaan__.

        > [!NOTE]
        > U moet __archiveren naar een opslag account__ selecteren, __streamen naar een event Hub__ of __naar log Analytics verzenden__ voordat u op de knop __Opslaan__ klikt.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Het Azure Storage-account gebruiken dat logboek gegevens bevat

1. Als u wilt weer geven van de BLOB-containers die logboek gegevens bevatten, opent u het Azure Storage account dat wordt gebruikt voor het registreren van Data Lake Analytics voor registratie en klikt u vervolgens op __blobs__.

   * De container **Insights-logs-audit** bevat de audit Logboeken.
   * De container **Insights-logboeken-aanvragen** bevat de logboeken van de aanvraag.

2. Binnen de containers worden de Logboeken opgeslagen in de volgende bestands structuur:

   ```text
   resourceId=/
     SUBSCRIPTIONS/
       <<SUBSCRIPTION_ID>>/
         RESOURCEGROUPS/
           <<RESOURCE_GRP_NAME>>/
             PROVIDERS/
               MICROSOFT.DATALAKEANALYTICS/
                 ACCOUNTS/
                   <DATA_LAKE_ANALYTICS_NAME>>/
                     y=####/
                       m=##/
                         d=##/
                           h=##/
                             m=00/
                               PT1H.json
   ```

   > [!NOTE]
   > De `##` vermeldingen in het pad bevatten het jaar, de maand, de dag en het uur waarop het logboek is gemaakt. Data Lake Analytics maakt elk uur één bestand, dus `m=` bevat altijd een waarde van `00` .

    Het volledige pad naar een audit logboek kan bijvoorbeeld het volgende zijn:

    `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Op dezelfde manier kan het volledige pad naar een logboek voor aanvragen worden:

    `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="log-structure"></a>Logboek structuur

De controle-en aanvraag logboeken bevinden zich in een gestructureerde JSON-indeling.

### <a name="request-logs"></a>Logboeken aanvragen

Hier volgt een voor beeld van een vermelding in het aanvraag logboek in JSON-indeling. Elke BLOB heeft één hoofd object met de naam **records** die een matrix met logboek objecten bevatten.

```json
{
"records":
  [
    . . . .
    ,
    {
         "time": "2016-07-07T21:02:53.456Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
         "category": "Requests",
         "operationName": "GetAggregatedJobHistory",
         "resultType": "200",
         "callerIpAddress": "::ffff:1.1.1.1",
         "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
         "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
         "properties": {
             "HttpMethod":"POST",
             "Path":"/JobAggregatedHistory",
             "RequestContentLength":122,
             "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
             "StartTime":"2016-07-07T21:02:52.472Z",
             "EndTime":"2016-07-07T21:02:53.456Z"
             }
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Schema voor aanvraag logboek

| Naam | Type | Beschrijving |
| --- | --- | --- |
| tijd |Tekenreeks |De tijds tempel (in UTC) van het logboek |
| resourceId |Tekenreeks |De id van de resource waarop de bewerking plaatsvond |
| category |Tekenreeks |De logboek categorie. Bijvoorbeeld **Aanvragen**. |
| operationName |Tekenreeks |De naam van de bewerking die is geregistreerd. Bijvoorbeeld GetAggregatedJobHistory. |
| resultType |Tekenreeks |De status van de bewerking, bijvoorbeeld 200. |
| callerIpAddress |Tekenreeks |Het IP-adres van de client die de aanvraag doet |
| correlationId |Tekenreeks |De id van het logboek. Deze waarde kan worden gebruikt om een reeks gerelateerde logboek vermeldingen te groeperen. |
| identity |Object |De identiteit die het logboek heeft gegenereerd |
| properties |JSON |Zie de volgende sectie (schema van eigenschappen van aanvraag Logboeken) voor meer informatie |

#### <a name="request-log-properties-schema"></a>Schema eigenschappen van het aanvraag logboek

| Naam | Type | Beschrijving |
| --- | --- | --- |
| HttpMethod |Tekenreeks |De HTTP-methode die wordt gebruikt voor de bewerking. Bijvoorbeeld ophalen. |
| Pad |Tekenreeks |Het pad waarin de bewerking is uitgevoerd |
| RequestContentLength |int |De lengte van de inhoud van de HTTP-aanvraag |
| ClientRequestId |Tekenreeks |De unieke id voor het identificeren van deze aanvraag |
| StartTime |Tekenreeks |Het tijdstip waarop de server de aanvraag heeft ontvangen |
| EndTime |Tekenreeks |Het tijdstip waarop de server een antwoord heeft verzonden |

### <a name="audit-logs"></a>Auditlogboeken

Hier volgt een voor beeld van een vermelding in het audit logboek in JSON-indeling. Elke BLOB heeft één hoofd object met de naam **records** die een matrix met logboek objecten bevatten.

```json
{
"records":
  [
    {
         "time": "2016-07-28T19:15:16.245Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
         "category": "Audit",
         "operationName": "JobSubmitted",
         "identity": "user@somewhere.com",
         "properties": {
             "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
             "JobName": "New Job",
             "JobRuntimeName": "default",
             "SubmitTime": "7/28/2016 7:14:57 PM"
             }
    }
  ]
}
```

#### <a name="audit-log-schema"></a>Schema van auditlogboek

| Naam | Type | Beschrijving |
| --- | --- | --- |
| tijd |Tekenreeks |De tijds tempel (in UTC) van het logboek |
| resourceId |Tekenreeks |De id van de resource waarop de bewerking plaatsvond |
| category |Tekenreeks |De logboek categorie. Bijvoorbeeld **audit**. |
| operationName |Tekenreeks |De naam van de bewerking die is geregistreerd. Bijvoorbeeld JobSubmitted. |
| resultType |Tekenreeks |Een substatus voor de taak status (operationname). |
| resultSignature |Tekenreeks |Aanvullende details over de taak status (operationname). |
| identity |Tekenreeks |De gebruiker die de bewerking heeft aangevraagd. Bijvoorbeeld susan@contoso.com. |
| properties |JSON |Zie de volgende sectie (schema van eigenschappen van audit Logboeken) voor meer informatie |

> [!NOTE]
> **resultType** en **resultSignature** bieden informatie over het resultaat van een bewerking en bevatten alleen een waarde als een bewerking is voltooid. Ze bevatten bijvoorbeeld alleen een waarde als **operationname** een waarde van **JobStarted** of **JobEnded** bevat.
>
>

#### <a name="audit-log-properties-schema"></a>Schema eigenschappen van controle logboek

| Naam | Type | Beschrijving |
| --- | --- | --- |
| JobId |Tekenreeks |De ID die aan de taak is toegewezen |
| JobName |Tekenreeks |De naam die voor de taak is ingesteld |
| JobRunTime |Tekenreeks |De runtime die wordt gebruikt om de taak te verwerken |
| SubmitTime |Tekenreeks |De tijd (in UTC) waarnaar de taak is verzonden |
| StartTime |Tekenreeks |De tijd waarop de taak is gestart na verzen ding (in UTC) |
| EndTime |Tekenreeks |Het tijdstip waarop de taak is beëindigd |
| Parallelle uitvoering |Tekenreeks |Het aantal Data Lake Analytics eenheden dat voor deze taak is aangevraagd tijdens het verzenden |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime** en **parallellisme** bieden informatie over een bewerking. Deze vermeldingen bevatten alleen een waarde als die bewerking is gestart of voltooid. Zo bevat **SubmitTime** alleen een waarde als **Operationname** de waarde **JobSubmitted** heeft.

## <a name="process-the-log-data"></a>De logboek gegevens verwerken

Azure Data Lake Analytics biedt een voor beeld van hoe u de logboek gegevens kunt verwerken en analyseren. U kunt het voor beeld vinden op [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) .

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Data Lake Analytics](data-lake-analytics-overview.md)
