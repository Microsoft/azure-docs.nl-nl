---
title: Gegevens uit de share point online-lijst kopiëren met behulp van Azure Data Factory
description: Informatie over het kopiëren van gegevens uit een share point online-lijst naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f8074b69b97a6ef96837e73a1082d2deb67084d9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177858"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>Gegevens uit de share point online-lijst kopiëren met behulp van Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit de share point online-lijst te kopiëren. Het artikel bouwt voort op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md), waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze share point online List-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit de share point online-lijst kopiëren naar elk ondersteund Sink-gegevens archief. Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

Deze share Point List Online-connector maakt gebruik van Service-Principal-verificatie en haalt gegevens op via het OData-protocol.

> [!TIP]
> Deze connector ondersteunt het kopiëren van gegevens uit de share point online- **lijst** , maar niet voor het bestand. Meer informatie over het kopiëren van een bestand uit het gedeelte [copy file from share point online](#copy-file-from-sharepoint-online) .

## <a name="prerequisites"></a>Vereisten

De share Point List Online-connector maakt gebruik van Service-Principal-verificatie om verbinding te maken met share point. Volg deze stappen om het in te stellen:

1. Registreer een toepassings entiteit in Azure Active Directory (Azure AD) door [uw toepassing te registreren bij een Azure AD-Tenant](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Noteer de volgende waarden, die u gebruikt om de gekoppelde service te definiëren:

    - Toepassings-id
    - Toepassings sleutel
    - Tenant-id

2. Share point online-site machtigingen verlenen aan uw geregistreerde toepassing: 

    > [!NOTE]
    > Voor deze bewerking is toestemming van de share point online-site vereist. U kunt de eigenaar vinden door naar de start pagina van de site te gaan > op de X-leden in de rechter bovenhoek te klikken > controleren wie de rol ' eigenaar ' heeft.

    1. Open share point online-site koppeling bijvoorbeeld `https://[your_site_url]/_layouts/15/appinv.aspx` (Vervang de URL van de site).
    2. Zoek de toepassings-ID die u hebt geregistreerd, vul de lege velden in en klik op maken.

        - App-domein: `localhost.com`
        - Omleidings-URL: `https://www.localhost.com`
        - XML voor machtigings aanvraag:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![share point-machtiging verlenen](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. Klik op het vertrouwen voor deze app.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor share point online List connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor een gekoppelde service van een share point online-lijst:

| **Eigenschap**        | **Beschrijving**                                              | **Vereist** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| type                | De eigenschap type moet worden ingesteld op: **SharePointOnlineList**.  | Ja          |
| siteUrl             | De URL van de share point online-site, bijvoorbeeld `https://contoso.sharepoint.com/sites/siteName` . | Ja          |
| servicePrincipalId  | De client-ID van de toepassing die is geregistreerd in Azure Active Directory. | Ja          |
| servicePrincipalKey | De sleutel van de toepassing. Markeer dit veld als **SecureString** om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja          |
| tenantId            | De Tenant-ID waaronder uw toepassing zich bevindt.          | Ja          |
| connectVia          | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie over de [vereisten](#prerequisites)vindt u eerder in dit artikel. Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | Nee           |

**Voorbeeld:**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie [gegevens sets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. De volgende sectie bevat een lijst met de eigenschappen die worden ondersteund door de SAP-tabel gegevensset.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **SharePointOnlineLResource**. | Ja |
| listName | De naam van de share point online-lijst. | Ja |

**Voorbeeld**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

Zie [pijp lijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten.  De volgende sectie bevat een lijst met de eigenschappen die worden ondersteund door de share point online-lijst Bron.

### <a name="sharepoint-online-list-as-source"></a>Share point online-lijst als bron

Als u gegevens wilt kopiëren uit de share point online-lijst, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **SharePointOnlineListSource**. | Ja |
| query | Aangepaste OData-query opties voor het filteren van gegevens. Bijvoorbeeld: `"$top=10&$select=Title,Number"`. | Nee |
| httpRequestTimeout | De time-out (in seconden) voor de HTTP-aanvraag om een antwoord te krijgen. De standaard waarde is 300 (5 minuten). | Nee |

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
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
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!NOTE]
> In Azure Data Factory kunt u niet meer dan één *keuze* gegevens type selecteren voor een share point online-lijst Bron.

## <a name="data-type-mapping-for-sharepoint-online-list"></a>Toewijzing van gegevens type voor share point online-lijst

Wanneer u gegevens kopieert vanuit share point online-lijst, worden de volgende toewijzingen gebruikt tussen gegevens typen van share point online en Azure Data Factory tussenliggende gegevens typen. 

| **Share point online-gegevens type**                 | **OData-gegevens type**                                  | **Azure Data Factory tussentijds gegevens type** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Eén tekstregel                             | Edm.String                                           | Tekenreeks                                   |
| Meerdere tekstregels                          | Edm.String                                           | Tekenreeks                                   |
| Keuze (menu om uit te kiezen)                    | Edm.String                                           | Tekenreeks                                   |
| Getal (1, 1,0, 100)                            | Edm.Double                                           | Dubbel                                   |
| Valuta ($, ¥, &euro; )                              | Edm.Double                                           | Dubbel                                   |
| Datum en tijd                                   | EDM. DateTime                                         | DateTime                                 |
| Opzoeken (informatie die al op deze site aanwezig is)       | Edm.Int32                                            | Int32                                    |
| Ja/Nee (selectie vakje)                              | Edm.Boolean                                          | Booleaans                                  |
| Persoon of groep                                 | Edm.Int32                                            | Int32                                    |
| Hyper Link of afbeelding                            | Edm.String                                           | Tekenreeks                                   |
| Berekend (berekening op basis van andere kolommen) | EDM. String/EDM. Double/EDM. DateTime/EDM. Boolean | Teken reeks/dubbele/DateTime/Boolean     |
| Bijlage                                      | Niet ondersteund                                        |                                          |
| Taakresultaat                                    | Niet ondersteund                                        |                                          |
| Externe gegevens                                   | Niet ondersteund                                        |                                          |
| Beheerde metagegevens                                | Niet ondersteund                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>Bestand kopiëren van share point online

U kunt een bestand vanuit share point online kopiëren met behulp van de **webactiviteit** voor het verifiëren en optrekken van het toegangs token van SPO en vervolgens door geven naar de volgende **Kopieer activiteit** om gegevens te kopiëren met de **http-connector als bron**.

![share point-bestands stroom kopiëren](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. Volg de sectie [vereisten](#prerequisites) voor het maken van een Aad-toepassing en het verlenen van machtigingen aan share point online. 

2. Maak een **webactiviteit** om het toegangs token op te halen uit share point online:

    - **URL**: `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2` . Vervang de Tenant-ID.
    - **Methode**: post
    - **Kopteksten**:
        - Content-type: Application/x-www-form-urlencoded
    - **Hoofd tekst**:  `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]` . Vervang de client-ID, het client geheim, de Tenant-ID en de Tenant naam.

    > [!CAUTION]
    > Stel de optie voor beveiligde uitvoer in op waar in de webactiviteit om te voor komen dat de token waarde wordt vastgelegd in tekst zonder opmaak. Voor verdere activiteiten waarvoor deze waarde wordt gebruikt, moet de optie voor beveiligde invoer zijn ingesteld op waar.

3. Keten met een **Kopieer activiteit** met een http-connector als bron voor het kopiëren van share point online-bestands inhoud:

    - Gekoppelde HTTP-service:
        - **Basis-URL**: `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value` . Vervang de site-URL en het relatieve pad naar het bestand. Voor beeld van relatief pad naar bestand als `/sites/site2/Shared Documents/TestBook.xlsx` .
        - **Verificatie type:** Anoniem *(om het Bearer-token te gebruiken dat later is geconfigureerd in de bron van de Kopieer activiteit)*
    - Gegevensset: Kies de gewenste indeling. Als u het bestand wilt kopiëren, selecteert u binair type.
    - Bron van Kopieer activiteit:
        - **Aanvraag methode**: ophalen
        - **Extra header**: gebruik de volgende expressie `@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}` , die het Bearer-token gebruikt dat is gegenereerd door de Web-activiteit upstream als autorisatie-header. Vervang de naam van de webactiviteit.
    - Configureer de Sink voor kopieer activiteiten zoals gebruikelijk.

> [!NOTE]
> Zelfs als een Azure AD-toepassing `FullControl` machtigingen heeft voor share point online, kunt u geen bestanden kopiëren van document bibliotheken waarvoor IRM is ingeschakeld.

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en sinks in azure Data Factory.
