---
title: Gegevens kopiëren van en naar Sales Force
description: Meer informatie over het kopiëren van gegevens uit Sales Force naar ondersteunde Sink-gegevens archieven of van ondersteunde bron gegevens archieven naar Sales Force met behulp van een Kopieer activiteit in een data factory pijp lijn.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/11/2021
ms.openlocfilehash: 2c60e8c71c38e5a6e92939b655cef9fcc1e04f70
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98072072"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Sales Force met behulp van Azure Data Factory

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-salesforce-connector.md)
> * [Huidige versie](connector-salesforce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar Sales Force. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze Sales Force-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit Sales Force kopiëren naar elk ondersteund Sink-gegevens archief. U kunt ook gegevens van elk ondersteund brongegevens archief kopiëren naar Sales Force. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bronnen of sinks op basis van de Kopieer activiteit.

Met name deze Sales Force-connector ondersteunt:

- Edities van Sales Force Developer, Professional, Enter prise of Unlimited.
- Gegevens kopiëren van en naar Sales Force-productie, sandbox en aangepast domein.

De Sales Force-connector is gebaseerd op de REST/bulk API Sales Force. Bij het kopiëren van gegevens uit Sales Force maakt de connector standaard gebruik van [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) en wordt er automatisch gekozen tussen rest-en bulk-api's op basis van de gegevens grootte – wanneer de resultatenset groot is, wordt er voor betere prestaties een bulk-API gebruikt. Wanneer u gegevens naar Sales Force schrijft, gebruikt de connector [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) van de bulk-API. U kunt ook expliciet de API-versie instellen die wordt gebruikt om gegevens te lezen/schrijven via de [ `apiVersion` eigenschap](#linked-service-properties) in de gekoppelde service.

## <a name="prerequisites"></a>Vereisten

API-machtiging moet zijn ingeschakeld in Sales Force. Zie [Enable API Access in Sales Force by permission set](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Engelstalig) voor meer informatie.

## <a name="salesforce-request-limits"></a>Limieten voor Sales Force-aanvragen

Sales Force heeft limieten voor zowel het totale aantal API-aanvragen als gelijktijdige API-aanvragen. Houd rekening met de volgende punten:

- Als het aantal gelijktijdige aanvragen de limiet overschrijdt, wordt er een beperking weer gegeven en ziet u wille keurige fouten.
- Als het totale aantal aanvragen de limiet overschrijdt, wordt het Sales Force-account 24 uur geblokkeerd.

Het fout bericht ' REQUEST_LIMIT_EXCEEDED ' kan in beide scenario's ook worden weer gegeven. Zie de sectie ' API-aanvraag limieten ' in [Sales Force-ontwikkelaars limieten](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm)voor meer informatie.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor de Sales Force-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de gekoppelde service Sales Force.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |De eigenschap type moet worden ingesteld op **Sales Force**. |Ja |
| environmentUrl | Geef de URL van het Sales Force-exemplaar op. <br> -Standaard is `"https://login.salesforce.com"` . <br> -Als u gegevens wilt kopiëren uit de sandbox, geeft u op `"https://test.salesforce.com"` . <br> -Als u gegevens wilt kopiëren uit een aangepast domein, geeft u bijvoorbeeld op `"https://[domain].my.salesforce.com"` . |Nee |
| gebruikersnaam |Geef een gebruikers naam op voor het gebruikers account. |Ja |
| wachtwoord |Geef een wacht woord op voor het gebruikers account.<br/><br/>Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| Security |Geef een beveiligings token op voor het gebruikers account. <br/><br/>Zie [beveiliging en de API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)voor meer informatie over beveiligings tokens in het algemeen. Het beveiligings token kan alleen worden overgeslagen als u het IP-adres van de Integration Runtime toevoegt aan de [lijst met vertrouwde IP-adressen](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) in Sales Force. Als u Azure IR gebruikt, raadpleegt u [Azure Integration runtime IP-adressen](azure-integration-runtime-ip-addresses.md).<br/><br/>Zie [een beveiligings Token ophalen](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)voor instructies over het ophalen en opnieuw instellen van een beveiligings token. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |Nee |
| apiVersion | Geef de Sales Force-API-versie op die u wilt gebruiken, bijvoorbeeld `48.0` . De connector gebruikt standaard [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) om gegevens uit Sales Force te kopiëren en maakt gebruik van [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) om gegevens te kopiëren naar Sales Force. | Nee |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee voor bron, ja voor Sink als de gekoppelde bron service geen Integration runtime heeft |

>[!IMPORTANT]
>Wanneer u gegevens naar Sales Force kopieert, kan de standaard Azure Integration Runtime niet worden gebruikt voor het uitvoeren van een kopie. Met andere woorden, als uw gekoppelde bron service geen opgegeven Integration runtime heeft, maakt u expliciet [een Azure Integration runtime](create-azure-integration-runtime.md#create-azure-ir) met een locatie in de buurt van uw Sales Force-exemplaar. Koppel de gekoppelde service Sales Force als in het volgende voor beeld.

**Voor beeld: referenties opslaan in Data Factory**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld: referenties opslaan in Key Vault**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de Sales Force-gegevensset.

Als u gegevens wilt kopiëren van en naar Sales Force, stelt u de eigenschap type van de gegevensset in op **SalesforceObject**. De volgende eigenschappen worden ondersteund.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op **SalesforceObject**.  | Ja |
| objectApiName | De naam van het Sales Force-object waaruit gegevens worden opgehaald. | Nee voor bron, ja voor Sink |

> [!IMPORTANT]
> Het deel ' __c ' van de **API-naam** is vereist voor een aangepast object.

![Naam van Data Factory Sales Force-API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Voorbeeld:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>Voor achterwaartse compatibiliteit: wanneer u gegevens uit Sales Force kopieert en u het vorige type gegevensset ' RelationalTable ' gebruikt, blijft deze werken terwijl er een suggestie wordt weer gegeven om over te scha kelen naar het nieuwe type ' SalesforceObject '.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de DataSet moet worden ingesteld op **RelationalTable**. | Ja |
| tableName | De naam van de tabel in Sales Force. | Nee (als ' query ' in de activiteit bron is opgegeven) |

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Sales Force-bron en Sink.

### <a name="salesforce-as-a-source-type"></a>Sales Force als bron type

Als u gegevens wilt kopiëren uit Sales Force, stelt u het bron type in de Kopieer activiteit in op **SalesforceSource**. De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op **SalesforceSource**. | Ja |
| query |Gebruik de aangepaste query om gegevens te lezen. U kunt de [Sales Force object query language-query (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) of SQL-92-query gebruiken. Meer tips vindt u in de sectie [query tips](#query-tips) . Als er geen query is opgegeven, worden alle gegevens van het Sales Force-object opgegeven in ' objectApiName ' in dataset opgehaald. | Nee (als "objectApiName" in de gegevensset is opgegeven) |
| readBehavior | Hiermee wordt aangegeven of een query moet worden doorzocht op de bestaande records, of dat alle records moeten worden opgevraagd met een query. Als deze niet wordt opgegeven, is het standaard gedrag het voormalige. <br>Toegestane waarden: **query** (standaard), **queryAll**.  | Nee |

> [!IMPORTANT]
> Het deel ' __c ' van de **API-naam** is vereist voor een aangepast object.

![Lijst met namen van Data Factory Sales Force-API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Voor achterwaartse compatibiliteit: wanneer u gegevens uit Sales Force kopieert en u het vorige type kopie ' RelationalSource ' gebruikt, blijft de bron actief terwijl u een suggestie ziet om over te scha kelen naar het nieuwe type ' SalesforceSource '.

### <a name="salesforce-as-a-sink-type"></a>Sales Force als Sink-type

Als u gegevens wilt kopiëren naar Sales Force, stelt u het sink-type in de Kopieer activiteit in op **SalesforceSink**. De volgende eigenschappen worden ondersteund in het gedeelte **sink** van de Kopieer activiteit.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op **SalesforceSink**. | Ja |
| writeBehavior | Het schrijf gedrag voor de bewerking.<br/>Toegestane waarden zijn **Insert** en **Upsert**. | Nee (standaard waarde is invoegen) |
| externalIdFieldName | De naam van het veld externe ID voor de bewerking upsert. Het opgegeven veld moet worden gedefinieerd als externe ID-veld in het Sales Force-object. De waarde mag geen NULL-waarden bevatten in de bijbehorende invoer gegevens. | Ja voor ' Upsert ' |
| writeBatchSize | Het aantal rijen van gegevens dat in elke batch naar Sales Force wordt geschreven. | Nee (de standaard waarde is 5.000) |
| ignoreNullValues | Hiermee wordt aangegeven of NULL-waarden moeten worden genegeerd uit de invoer gegevens tijdens een schrijf bewerking.<br/>Toegestane waarden zijn **waar** en **Onwaar**.<br>- **Waar**: laat de gegevens in het doel object ongewijzigd wanneer u een upsert of update-bewerking doet. Voeg een gedefinieerde standaard waarde in wanneer u een INSERT-bewerking wilt uitvoeren.<br/>- **Onwaar**: werk de gegevens in het doel object bij naar NULL wanneer u een upsert of update-bewerking uitgevoerd. Voeg een NULL-waarde toe wanneer u een INSERT-bewerking uitgevoerd. | Nee (standaard waarde is False) |

**Voor beeld: Sales Force-sink in een Kopieer activiteit**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Query tips

### <a name="retrieve-data-from-a-salesforce-report"></a>Gegevens ophalen uit een Sales Force-rapport

U kunt gegevens ophalen uit Sales Force-rapporten door een query op te geven als `{call "<report name>"}` . Een voorbeeld is `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Verwijderde records uit de Prullenbak voor Sales Force ophalen

Als u de voorlopig verwijderde records uit de Prullenbak voor Sales Force wilt opvragen, kunt u `readBehavior` als opgeven `queryAll` . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Verschil tussen de SOQL-en SQL-query syntaxis

Bij het kopiëren van gegevens uit Sales Force kunt u de SOQL-query of SQL-query gebruiken. Houd er rekening mee dat deze twee verschillende syntaxis-en functionaliteits ondersteuning hebben, maar niet kunnen combi neren. U wordt aangeraden de SOQL-query te gebruiken, die systeem eigen wordt ondersteund door Sales Force. De volgende tabel bevat de belangrijkste verschillen:

| Syntax | SOQL-modus | SQL-modus |
|:--- |:--- |:--- |
| Kolom selectie | U moet de velden opsommen die moeten worden gekopieerd in de query, bijvoorbeeld `SELECT field1, filed2 FROM objectname` | `SELECT *` wordt ondersteund naast kolom selectie. |
| Aanhalings tekens | Gearchiveerde/object namen kunnen niet worden opgenomen in een aanhalings teken. | De namen van velden/objecten kunnen worden opgenomen in een aanhalings teken, bijvoorbeeld `SELECT "id" FROM "Account"` |
| Datum notatie |  Raadpleeg [hier](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) de details en voor beelden in de volgende sectie. | Raadpleeg [hier](/sql/odbc/reference/develop-app/date-time-and-timestamp-literals) de details en voor beelden in de volgende sectie. |
| Booleaanse waarden | Wordt weer gegeven als `False` en `True` , bijvoorbeeld `SELECT … WHERE IsDeleted=True` | Wordt weer gegeven als 0 of 1, bijvoorbeeld `SELECT … WHERE IsDeleted=1` . |
| Kolom naam wijzigen | Niet ondersteund. | Ondersteund, bijvoorbeeld: `SELECT a AS b FROM …` . |
| Relatie | Ondersteund, bijvoorbeeld `Account_vod__r.nvs_Country__c` . | Niet ondersteund. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Gegevens ophalen met behulp van een WHERE-component in de datum/tijd-kolom

Wanneer u de SOQL of SQL-query opgeeft, moet u rekening best Eden aan het verschil in datum-en tijd notatie. Bijvoorbeeld:

* Voor **beeld van SOQL**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL**-voor beeld: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>Fout van MALFORMED_QUERY: afgekapt

Als u de fout melding ' MALFORMED_QUERY: afgekapt ' krijgt, is het normaal dat de kolom Type JunctionIdList in data en Sales Force is beperkt tot het ondersteunen van dergelijke gegevens met een groot aantal rijen. Als u het probleem wilt oplossen, probeert u de kolom JunctionIdList uit te sluiten of het aantal te kopiëren rijen te beperken (u kunt partitioneren op meerdere uitvoeringen van de Kopieer activiteit).

## <a name="data-type-mapping-for-salesforce"></a>Toewijzing van gegevens type voor Sales Force

Wanneer u gegevens uit Sales Force kopieert, worden de volgende toewijzingen gebruikt uit Sales Force-gegevens typen voor het Data Factory van tussenliggende gegevens typen. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe de Kopieer activiteit het bron schema en het gegevens type aan de Sink toewijst.

| Sales Force-gegevens type | Data Factory tussentijds gegevens type |
|:--- |:--- |
| Automatisch nummer |Tekenreeks |
| Checkbox |Booleaans |
| Valuta |Decimaal |
| Date |DateTime |
| Datum/tijd |DateTime |
| E-mail |Tekenreeks |
| Id |Tekenreeks |
| Opzoek relatie |Tekenreeks |
| Meervoudige selectie lijst |Tekenreeks |
| Aantal |Decimaal |
| Percentage |Decimaal |
| Telefoon |Tekenreeks |
| Picklist |Tekenreeks |
| Tekst |Tekenreeks |
| Tekst gebied |Tekenreeks |
| Tekst gebied (lang) |Tekenreeks |
| Tekst gebied (uitgebreid) |Tekenreeks |
| Tekst (versleuteld) |Tekenreeks |
| URL |Tekenreeks |

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.


## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in Data Factory.