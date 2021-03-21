---
title: Diagnostische logboeken weer geven voor Azure Data Lake Storage Gen1 | Microsoft Docs
description: 'Meer informatie over het instellen en openen van Diagnostische logboeken voor Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 0b84e26962b00ee5b9d4c34cab7efbcc9aa0bf01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582795"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Diagnostische logboeken openen voor Azure Data Lake Storage Gen1
Meer informatie over het inschakelen van diagnostische logboek registratie voor uw Azure Data Lake Storage Gen1-account en het weer geven van de logboeken die voor uw account zijn verzameld.

Organisaties kunnen diagnostische logboek registratie inschakelen voor hun Azure Data Lake Storage Gen1-account om gegevens toegangscontrole te verzamelen die informatie biedt, zoals een lijst met gebruikers die toegang hebben tot de gegevens, hoe vaak de gegevens worden geopend, hoeveel gegevens er worden opgeslagen in het account, enzovoort. Wanneer deze optie is ingeschakeld, worden de diagnostische gegevens en/of aanvragen op basis van de beste inspanningen geregistreerd. Zowel aanvragen als logboek vermeldingen voor diagnostische gegevens worden alleen gemaakt als er aanvragen voor het service-eind punt zijn gedaan.

## <a name="prerequisites"></a>Vereisten
* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Azure data Lake Storage gen1-account**. Volg de instructies in aan [de slag met Azure data Lake Storage gen1 met behulp van de Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Diagnostische logboek registratie inschakelen voor uw Data Lake Storage Gen1-account
1. Meld u aan bij de nieuwe [Azure Portal](https://portal.azure.com).
2. Open uw Data Lake Storage Gen1-account en klik op de Blade Data Lake Storage Gen1 account op **Diagnostische instellingen**.
3. Klik op de Blade **Diagnostische instellingen** op **Diagnostische gegevens inschakelen**.

    ![Scherm opname van het Data Lake Storage gen 1-account met de optie diagnostische instelling en de optie diagnostische gegevens inschakelen.](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Diagnostische logboeken inschakelen")

3. Voer op de Blade **Diagnostische instellingen** de volgende wijzigingen uit om diagnostische logboek registratie in te stellen.
   
    ![Scherm afbeelding van de sectie diagnostische instelling met het tekstvak Naam en de optie opslaan.](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Diagnostische logboeken inschakelen")
   
   * Voer bij **naam** een waarde in voor de configuratie van het diagnostische logboek.
   * U kunt ervoor kiezen om de gegevens op verschillende manieren op te slaan/te verwerken.
     
        * Selecteer de optie voor het **archiveren naar een opslag account** om logboeken op te slaan in een Azure Storage-account. U gebruikt deze optie als u de gegevens wilt archiveren die op een later tijdstip door batch worden verwerkt. Als u deze optie selecteert, moet u een Azure Storage-account opgeven om de logboeken op te slaan.
        
        * Selecteer de optie om **te streamen naar een event hub** om logboek gegevens naar een Azure Event hub te streamen. Waarschijnlijk zult u deze optie gebruiken als u een downstream verwerkings pijplijn hebt om inkomende Logboeken in realtime te analyseren. Als u deze optie selecteert, moet u de details opgeven voor de Azure Event hub die u wilt gebruiken.

        * Selecteer de optie om **naar log Analytics te verzenden** om de Azure Monitor-service te gebruiken om de gegenereerde logboek gegevens te analyseren. Als u deze optie selecteert, moet u de Details voor de Log Analytics-werk ruimte opgeven waarvoor u de logboek analyse uitvoeren wilt gebruiken. Zie [verzamelde gegevens weer geven of analyseren met Azure monitor logboeken zoeken](../azure-monitor/logs/log-analytics-tutorial.md) voor meer informatie over het gebruik van Azure monitor-Logboeken.
     
   * Geef op of u audit Logboeken of aanvraag Logboeken of beide wilt ophalen.
   * Geef het aantal dagen op dat de gegevens moeten worden bewaard. Bewaren is alleen van toepassing als u Azure Storage-account gebruikt om logboek gegevens te archiveren.
   * Klik op **Opslaan**.

Wanneer u Diagnostische instellingen hebt ingeschakeld, kunt u de logboeken bekijken op het tabblad **Diagnostische logboeken** .

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Diagnostische logboeken voor uw Data Lake Storage Gen1-account weer geven
Er zijn twee manieren om de logboek gegevens voor uw Data Lake Storage Gen1-account weer te geven.

* In de weer gave instellingen van Data Lake Storage Gen1 account
* Van het Azure Storage-account waar de gegevens zijn opgeslagen

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>De weer gave Data Lake Storage Gen1 instellingen gebruiken
1. Klik op de Blade **instellingen** van data Lake Storage gen1 account op **Diagnostische logboeken**.
   
    ![Diagnostische logboeken weergeven](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Diagnostische logboeken weergeven") 
2. Op de Blade **Diagnostische logboeken** ziet u de logboeken die zijn gecategoriseerd door **audit logboeken** en **Logboeken aanvragen**.
   
   * De aanvraag logboeken vastleggen elke API-aanvraag die is gemaakt voor het Data Lake Storage Gen1-account.
   * Audit logboeken zijn vergelijkbaar met aanvraag logboeken, maar bieden een veel gedetailleerde uitsplitsing van de bewerkingen die worden uitgevoerd op het Data Lake Storage Gen1-account. Eén voor beeld van een API-aanroep in de aanvraag logboeken kan ertoe leiden dat meerdere toevoeg bewerkingen worden uitgevoerd in de audit Logboeken.
3. Als u de logboeken wilt downloaden, klikt u op de **Download** koppeling voor elke logboek vermelding.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Van het Azure Storage-account dat logboek gegevens bevat
1. Open de Blade Azure Storage account die is gekoppeld aan Data Lake Storage Gen1 voor logboek registratie en klik vervolgens op blobs. Op de Blade **BLOB service** worden twee containers weer gegeven.
   
    ![Scherm afbeelding van de Blade Data Lake Storage gen 1 de optie blobs is geselecteerd en de Blade blog service met de namen van de twee BLOB-services die worden genoemd.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Diagnostische logboeken weergeven")
   
   * De container **Insights-logs-audit** bevat de audit Logboeken.
   * De container **Insights-logboeken-aanvragen** bevat de logboeken van de aanvraag.
2. In deze containers worden de Logboeken opgeslagen onder de volgende structuur.
   
    ![Scherm opname van de logboek structuur, zoals deze is opgeslagen in de container.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Diagnostische logboeken weergeven")
   
    Een voor beeld: het volledige pad naar een audit logboek kan worden `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Het volledige pad naar een aanvraag logboek kan ook worden `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Inzicht in de structuur van de logboek gegevens
De audit-en aanvraag logboeken hebben een JSON-indeling. In deze sectie kijken we naar de structuur van JSON voor aanvraag-en audit Logboeken.

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
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Requests",
        "operationName": "GETCustomerIngressEgress",
        "resultType": "200",
        "callerIpAddress": "::ffff:1.1.1.1",
        "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
        "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
        "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"StoreIngressSize":0 ,"StoreEgressSize":4096,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z","QueryParameters":"api-version=<version>&op=<operationName>"}
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
| resourceId |Tekenreeks |De ID van de resource waarop de bewerking plaatsvond |
| category |Tekenreeks |De logboek categorie. Bijvoorbeeld **Aanvragen**. |
| operationName |Tekenreeks |De naam van de bewerking die is geregistreerd. Bijvoorbeeld getfilestatus. |
| resultType |Tekenreeks |De status van de bewerking, bijvoorbeeld 200. |
| callerIpAddress |Tekenreeks |Het IP-adres van de client die de aanvraag doet |
| correlationId |Tekenreeks |De ID van het logboek dat kan worden gebruikt om een set gerelateerde logboek vermeldingen samen te groeperen |
| identity |Object |De identiteit die het logboek heeft gegenereerd |
| properties |JSON |Zie hieronder voor meer informatie |

#### <a name="request-log-properties-schema"></a>Schema eigenschappen van het aanvraag logboek
| Naam | Type | Beschrijving |
| --- | --- | --- |
| HttpMethod |Tekenreeks |De HTTP-methode die wordt gebruikt voor de bewerking. Bijvoorbeeld ophalen. |
| Pad |Tekenreeks |Het pad waarin de bewerking is uitgevoerd |
| RequestContentLength |int |De lengte van de inhoud van de HTTP-aanvraag |
| ClientRequestId |Tekenreeks |De unieke ID voor deze aanvraag |
| StartTime |Tekenreeks |Het tijdstip waarop de server de aanvraag heeft ontvangen |
| EndTime |Tekenreeks |Het tijdstip waarop de server een antwoord heeft verzonden |
| StoreIngressSize |Omvang |Grootte in bytes die zijn ontvangen naar Data Lake Store |
| StoreEgressSize |Omvang |Grootte in bytes egressed van Data Lake Store |
| QueryParameters |Tekenreeks |Beschrijving: Dit zijn de HTTP-query parameters. Voor beeld 1: API-Version = 2014-01-01&op = getfilestatus voor beeld 2: op = APPEND&toevoegen = True&syncFlag = DATA&filesessionid = bee3355a-4925-4435-bb4d-ceea52811aeb&leaseid = bee3355a-4925-4435-bb4d-ceea52811aeb&offset = 28313319&API-Version = 2017-08-01 |

### <a name="audit-logs"></a>Auditlogboeken
Hier volgt een voor beeld van een vermelding in het audit logboek in JSON-indeling. Elke BLOB heeft één hoofd object met de naam **records** dat een matrix met logboek objecten bevat

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-08T19:08:59.359Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Audit",
        "operationName": "SeOpenStream",
        "resultType": "0",
        "resultSignature": "0",
        "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
        "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
        "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="audit-log-schema"></a>Schema van auditlogboek
| Naam | Type | Beschrijving |
| --- | --- | --- |
| tijd |Tekenreeks |De tijds tempel (in UTC) van het logboek |
| resourceId |Tekenreeks |De ID van de resource waarop de bewerking plaatsvond |
| category |Tekenreeks |De logboek categorie. Bijvoorbeeld **audit**. |
| operationName |Tekenreeks |De naam van de bewerking die is geregistreerd. Bijvoorbeeld getfilestatus. |
| resultType |Tekenreeks |De status van de bewerking, bijvoorbeeld 200. |
| resultSignature |Tekenreeks |Meer informatie over de bewerking. |
| correlationId |Tekenreeks |De ID van het logboek dat kan worden gebruikt om een set gerelateerde logboek vermeldingen samen te groeperen |
| identity |Object |De identiteit die het logboek heeft gegenereerd |
| properties |JSON |Zie hieronder voor meer informatie |

#### <a name="audit-log-properties-schema"></a>Schema eigenschappen van controle logboek
| Naam | Type | Beschrijving |
| --- | --- | --- |
| StreamName |Tekenreeks |Het pad waarin de bewerking is uitgevoerd |

## <a name="samples-to-process-the-log-data"></a>Voor beelden voor het verwerken van de logboek gegevens
Bij het verzenden van logboeken van Azure Data Lake Storage Gen1 naar Azure Monitor-Logboeken (Zie [gegevens weer geven of analyseren die zijn verzameld met Azure monitor logboeken zoeken](../azure-monitor/logs/log-analytics-tutorial.md) voor meer informatie over het gebruik van Azure monitor Logboeken), wordt met de volgende query een tabel geretourneerd met een lijst met gebruikers weergave namen, de tijd van de gebeurtenissen en het aantal gebeurtenissen voor het tijdstip van de gebeurtenis samen met een visueel diagram. Het kan eenvoudig worden gewijzigd om gebruikers-GUID of andere kenmerken weer te geven:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 biedt een voor beeld van hoe u de logboek gegevens kunt verwerken en analyseren. U kunt het voor beeld vinden op [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) . 

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
