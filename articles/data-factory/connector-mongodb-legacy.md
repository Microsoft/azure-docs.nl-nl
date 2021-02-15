---
title: Gegevens kopiëren van MongoDB met verouderde
description: Informatie over het kopiëren van gegevens uit Mongo DB naar ondersteunde Sink-gegevens archieven door gebruik te maken van een Kopieer activiteit in een verouderde Azure Data Factory-pijp lijn.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 08/12/2019
ms.openlocfilehash: e13a1a5a939d314bdf4500c0827fa13201505016
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368843"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory-legacy"></a>Gegevens kopiëren van MongoDB met behulp van Azure Data Factory (verouderd)

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-on-premises-mongodb-connector.md)
> * [Huidige versie](connector-mongodb.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een MongoDB-data base te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

>[!IMPORTANT]
>ADF release een nieuwe MongoDB-connector waarmee betere native MongoDB-ondersteuning wordt geboden, vergeleken met deze op ODBC gebaseerde implementatie, raadpleegt u het artikel over [MongoDb connector](connector-mongodb.md) voor meer informatie. Deze verouderde MongoDB-connector wordt ondersteund als-is voor achterwaartse conformiteit, terwijl u voor elke nieuwe werk belasting de nieuwe connector moet gebruiken.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens uit de MongoDB-data base kopiëren naar elk ondersteund Sink-gegevens archief. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die worden ondersteund als bron/sinks door de Kopieer activiteit.

Deze MongoDB-connector ondersteunt met name:

- MongoDB- **versies 2,4, 2,6, 3,0, 3,2, 3,4 en 3,6**.
- Kopiëren van gegevens met behulp van **eenvoudige** of **anonieme** verificatie.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

De Integration Runtime biedt een ingebouwd MongoDB-stuur programma, dus u hoeft niet hand matig een stuur programma te installeren bij het kopiëren van gegevens uit MongoDB.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten die specifiek zijn voor MongoDB-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor MongoDB gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type |De eigenschap type moet worden ingesteld op: **MongoDb** |Yes |
| server |Het IP-adres of de hostnaam van de MongoDB-server. |Yes |
| poort |TCP-poort die de MongoDB-server gebruikt om te Luis teren naar client verbindingen. |Nee (de standaard waarde is 27017) |
| databaseName |De naam van de MongoDB-data base waartoe u toegang wilt krijgen. |Yes |
| authenticationType | Type verificatie dat wordt gebruikt om verbinding te maken met de MongoDB-data base.<br/>Toegestane waarden zijn: **Basic** en **Anonymous**. |Yes |
| gebruikersnaam |Gebruikers account voor toegang tot MongoDB. |Ja (als basis verificatie wordt gebruikt). |
| wachtwoord |Het wachtwoord voor de gebruiker. Markeer dit veld als SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). |Ja (als basis verificatie wordt gebruikt). |
| authSource |De naam van de MongoDB-data base die u wilt gebruiken om uw referenties voor verificatie te controleren. |Nee. Voor basis verificatie is het standaard om het beheerders account en de data base te gebruiken die is opgegeven met de eigenschap databasename. |
| enableSsl | Hiermee geeft u op of de verbindingen met de server met behulp van TLS worden versleuteld. De standaardwaarde is false.  | No |
| allowSelfSignedServerCert | Hiermee geeft u op of zelfondertekende certificaten van de server mogen worden toegestaan. De standaardwaarde is false.  | No |
| connectVia | Het [Integration runtime](concepts-integration-runtime.md) dat moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

**Voorbeeld:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Zie [gegevens sets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. De volgende eigenschappen worden ondersteund voor MongoDB-gegevensset:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **MongoDbCollection** | Yes |
| collectionName |De naam van de verzameling in de MongoDB-data base. |Ja |

**Voorbeeld:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. Deze sectie bevat een lijst met eigenschappen die door MongoDB-bron worden ondersteund.

### <a name="mongodb-as-source"></a>MongoDB als bron

De volgende eigenschappen worden ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **MongoDbSource** | Yes |
| query |Gebruik de aangepaste SQL-92-query om gegevens te lezen. Bijvoorbeeld: Select * from MyTable. |Nee (als ' verzamelingsset ' in gegevensset is opgegeven) |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Wanneer u de SQL-query opgeeft, let dan op de datum/tijd-indeling. Bijvoorbeeld: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` of om para meter te gebruiken `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schema door Data Factory

Azure Data Factory Service-schema van een MongoDB-verzameling met behulp van de **meest recente 100-documenten** in de verzameling. Als deze 100-documenten geen volledig schema bevatten, kunnen sommige kolommen tijdens de Kopieer bewerking worden genegeerd.

## <a name="data-type-mapping-for-mongodb"></a>Toewijzing van gegevens type voor MongoDB

Bij het kopiëren van gegevens uit MongoDB worden de volgende toewijzingen gebruikt van MongoDB-gegevens typen om tussenliggende gegevens typen te Azure Data Factory. Zie [schema-en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md) voor meer informatie over hoe kopieer activiteit het bron schema en het gegevens type aan de Sink koppelt.

| MongoDB-gegevens type | Data Factory-gegevens type interim |
|:--- |:--- |
| Binair |Byte [] |
| Boolean |Booleaans |
| Date |DateTime |
| NumberDouble |Dubbel |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Tekenreeks |
| Tekenreeks |Tekenreeks |
| UUID |Guid |
| Object |Opnieuw genormaliseerd in kolommen met ' _ ' als genest scheidings teken |

> [!NOTE]
> Raadpleeg voor meer informatie over ondersteuning voor matrices met behulp van virtuele tabellen de sectie [ondersteuning voor complexe typen met virtuele tabellen](#support-for-complex-types-using-virtual-tables) .
>
> De volgende MongoDB-gegevens typen worden momenteel niet ondersteund: DBPointer, java script, max/min-sleutel, reguliere expressie, symbool, Time Stamp, niet gedefinieerd.

## <a name="support-for-complex-types-using-virtual-tables"></a>Ondersteuning voor complexe typen met virtuele tabellen

Azure Data Factory maakt gebruik van een ingebouwd ODBC-stuur programma om verbinding te maken met gegevens uit uw MongoDB-data base en deze te kopiëren. Voor complexe typen, zoals matrices of objecten met verschillende typen in de documenten, worden de gegevens in de bijbehorende virtuele tabellen opnieuw genormaliseerd. Met name als een tabel dergelijke kolommen bevat, genereert het stuur programma de volgende virtuele tabellen:

* Een **basis tabel** met dezelfde gegevens als de echte tabel, met uitzonde ring van de kolommen van het type complex. Voor de basis tabel wordt dezelfde naam gebruikt als voor de echte tabel die deze vertegenwoordigt.
* Een **virtuele tabel** voor elke kolom met complexe typen, waarmee de geneste gegevens worden uitgevouwen. De virtuele tabellen krijgen een naam met de naam van de tabel Real, een scheidings teken ' _ ' en de naam van de matrix of het object.

Virtuele tabellen verwijzen naar de gegevens in de tabel Real, waardoor het stuur programma toegang kan krijgen tot de Gedenormaliseerde gegevens. U kunt toegang krijgen tot de inhoud van MongoDB-matrices door de virtuele tabellen op te vragen en te koppelen.

### <a name="example"></a>Voorbeeld

ExampleTable hier is bijvoorbeeld een MongoDB-tabel met één kolom met een matrix met objecten in elke cel, facturen en één kolom met een matrix van scalaire typen – classificaties.

| _id | Naam van klant | Facturen | Serviceniveau | Waarderingen |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", item: "pop-uptaak", prijs: "456", korting: "0,2"}, {invoice_id: "124", item: "oven", prijs: "1235", korting: "0,2"}] |Zilver |[5, 6] |
| 2222 |XYZ |[{invoice_id: "135", item: "koel kast", prijs: "12543", korting: "0,0"}] |Goud |[1, 2] |

Het stuur programma genereert meerdere virtuele tabellen om deze afzonderlijke tabel weer te geven. De eerste virtuele tabel is de basis tabel met de naam ' ExampleTable ', zoals weer gegeven in het voor beeld. De basis tabel bevat alle gegevens van de oorspronkelijke tabel, maar de gegevens uit de matrices zijn wegge laten en worden uitgevouwen in de virtuele tabellen.

| _id | Naam van klant | Serviceniveau |
| --- | --- | --- |
| 1111 |ABC |Zilver |
| 2222 |XYZ |Goud |

In de volgende tabellen ziet u de virtuele tabellen die de oorspronkelijke matrices in het voor beeld vertegenwoordigen. Deze tabellen bevatten het volgende:

* Een verwijzing naar de oorspronkelijke primaire-sleutel kolom die overeenkomt met de rij van de oorspronkelijke matrix (via de kolom _id)
* Een indicatie van de positie van de gegevens in de oorspronkelijke matrix
* De uitgevouwen gegevens voor elk element in de matrix

**Tabel "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Korting |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |pop- |456 |0,2 |
| 1111 |1 |124 |droog |1235 |0,2 |
| 2222 |0 |135 |koel kast |12543 |0,0 |

**Tabel "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.
