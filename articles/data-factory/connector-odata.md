---
title: Gegevens uit OData-bronnen kopiëren met behulp van Azure Data Factory
description: Informatie over het kopiëren van gegevens van OData-bronnen naar ondersteunde Sink-gegevens archieven met behulp van een Kopieer activiteit in een Azure Data Factory-pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: jingwang
ms.openlocfilehash: 9dd86b4982edf5d206e64431a5e1458c4b848e9e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968488"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Gegevens kopiëren van een OData-bron met behulp van Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-odata-connector.md)
> * [Huidige versie](connector-odata.md)

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit een OData-bron te kopiëren. Het artikel bouwt voort op de [Kopieer activiteit in azure Data Factory](copy-activity-overview.md), waarin een algemeen overzicht van de Kopieer activiteit wordt weer gegeven.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze OData-connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met een [ondersteunde bron/Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit een OData-bron kopiëren naar elk ondersteund Sink-gegevens archief. Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en Sinks.

Deze OData-connector ondersteunt met name:

- OData-versie 3,0 en 4,0.
- Gegevens kopiëren met behulp van een van de volgende authenticaties: **anoniem**, **basis**, **Windows** en **Aad-Service-Principal**.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die u kunt gebruiken voor het definiëren van Data Factory entiteiten die specifiek zijn voor een OData-connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor een gekoppelde OData-service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** moet worden ingesteld op **OData**. |Yes |
| url | De basis-URL van de OData-service. |Yes |
| authenticationType | Het type verificatie dat wordt gebruikt om verbinding te maken met de OData-bron. Toegestane waarden zijn **anoniem**, **basis**, **Windows** en **AadServicePrincipal**. OAuth op basis van de gebruiker wordt niet ondersteund. U kunt verificatie headers ook configureren in de `authHeader` eigenschap.| Yes |
| authHeaders | Aanvullende HTTP-aanvraag headers voor authenticatie.<br/> Als u bijvoorbeeld API-sleutel verificatie wilt gebruiken, kunt u verificatie type selecteren als ' anoniem ' en de API-sleutel in de header opgeven. | No |
| userName | Geef een **gebruikers naam** op als u basis-of Windows-verificatie gebruikt. | No |
| wachtwoord | Geef het **wacht woord** op voor het gebruikers account dat u hebt opgegeven voor de **gebruikers naam**. Markeer dit veld als **SecureString** -type om het veilig op te slaan in Data Factory. U kunt ook [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | No |
| servicePrincipalId | Geef de client-ID van de Azure Active Directory toepassing op. | No |
| aadServicePrincipalCredentialType | Geef het referentie type op dat moet worden gebruikt voor Service-Principal-verificatie. Toegestane waarden zijn: `ServicePrincipalKey` of `ServicePrincipalCert` . | No |
| servicePrincipalKey | Geef de sleutel van de Azure Active Directory toepassing op. Markeer dit veld als **SecureString** om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | No |
| servicePrincipalEmbeddedCert | Geef het met base64 gecodeerde certificaat op van uw toepassing die is geregistreerd in Azure Active Directory. Markeer dit veld als **SecureString** om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | No |
| servicePrincipalEmbeddedCertPassword | Geef het wacht woord van uw certificaat op als uw certificaat is beveiligd met een wacht woord. Markeer dit veld als **SecureString** om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md).  | No|
| tenant | Geef de Tenant gegevens op (domein naam of Tenant-ID) waaronder uw toepassing zich bevindt. U kunt deze ophalen door de muis in de rechter bovenhoek van de Azure Portal aan te wijzen. | No |
| aadResourceId | Geef de AAD-resource op die u aanvraagt voor autorisatie.| No |
| azureCloudType | Voor Service-Principal-verificatie geeft u het type van de Azure-cloud omgeving op waarvoor uw AAD-toepassing is geregistreerd. <br/> Toegestane waarden zijn **AzurePublic**, **AzureChina**, **AzureUsGovernment** en **AzureGermany**. De cloud omgeving van de data factory wordt standaard gebruikt. | No |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Meer informatie vindt u in de sectie [vereisten](#prerequisites) . Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. |No |

**Voor beeld 1: anonieme verificatie gebruiken**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Voor beeld 2: basis verificatie gebruiken**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**Voor beeld 3: Windows-verificatie gebruiken**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**Voor beeld 4: Service Principal Key-verificatie gebruiken**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Voor beeld 5: verificatie van Service-Principal-certificaten gebruiken**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Voor beeld 6: API-sleutel verificatie gebruiken**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "APIKey": {
                    "type": "SecureString",
                    "value": "<API key>"
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

In deze sectie vindt u een lijst met eigenschappen die door de OData-gegevensset worden ondersteund.

Zie [gegevens sets en gekoppelde services](concepts-datasets-linked-services.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. 

Als u gegevens wilt kopiëren uit OData, stelt u de eigenschap **type** van de gegevensset in op **ODataResource**. De volgende eigenschappen worden ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de DataSet moet worden ingesteld op **ODataResource**. | Yes |
| leertraject | Het pad naar de OData-resource. | Yes |

**Voorbeeld**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van Kopieer activiteit

In deze sectie vindt u een lijst met eigenschappen die door de OData-bron worden ondersteund.

Zie [pijp lijnen](concepts-pipelines-activities.md)voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. 

### <a name="odata-as-source"></a>OData als bron

Als u gegevens wilt kopiëren uit OData, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap **type** van de bron van de Kopieer activiteit moet zijn ingesteld op **ODataSource**. | Yes |
| query | OData-query opties voor het filteren van gegevens. Bijvoorbeeld: `"$select=Name,Description&$top=5"`.<br/><br/>**Opmerking**: de OData-connector kopieert gegevens van de gecombineerde URL: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]` . Zie [ODATA URL Components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)(Engelstalig) voor meer informatie. | No |
| httpRequestTimeout | De time-out (de time **span** -waarde) voor de HTTP-aanvraag om een antwoord te krijgen. Deze waarde is de time-out voor het verkrijgen van een reactie, niet de time-out voor het lezen van antwoord gegevens. Als niet wordt opgegeven, is de standaard waarde **00:30:00** (30 minuten). | No |

**Voorbeeld**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Als u `RelationalSource` getypte bron gebruikt, wordt deze nog steeds ondersteund als-is, terwijl u wordt geadviseerd om het nieuwe item te gebruiken.

## <a name="data-type-mapping-for-odata"></a>Toewijzing van gegevens type voor OData

Wanneer u gegevens van OData kopieert, worden de volgende toewijzingen gebruikt tussen OData-gegevens typen en Azure Data Factory tussenliggende gegevens typen. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over de manier waarop Kopieer activiteiten het bron schema en het gegevens type aan de Sink toewijzen.

| OData-gegevens type | Data Factory tussentijds gegevens type |
|:--- |:--- |
| EDM. binary | Byte [] |
| Edm.Boolean | Booleaanse waarde |
| EDM. byte | Byte [] |
| EDM. DateTime | DateTime |
| EDM. decimaal | Decimaal |
| Edm.Double | Dubbel |
| EDM. single | Enkelvoudig |
| EDM. GUID | Guid |
| EDM. Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| EDM. SByte | Int16 |
| Edm.String | Tekenreeks |
| EDM. tijd | TimeSpan |
| Edm.DateTimeOffset | Date time offset |

> [!NOTE]
> Complexe OData-gegevens typen (zoals **object**) worden niet ondersteund.

## <a name="copy-data-from-project-online"></a>Gegevens uit project online kopiëren

Als u gegevens wilt kopiëren uit project online, kunt u gebruikmaken van de OData-connector en een toegangs token die is verkregen van hulpprogram ma's zoals postman.

> [!CAUTION]
> Het toegangs token verloopt over 1 uur. standaard moet u een nieuw toegangs Token ophalen wanneer het verloopt.

1. Gebruik **postman** om het toegangs token op te halen:

   1. Ga naar het tabblad **autorisatie** op de Postman-website.
   1. In het vak **type** selecteert u **OAuth 2,0** en selecteert u in het vak **autorisatie gegevens toevoegen aan** de optie **aanvraag headers**.
   1. Vul de volgende gegevens in op de pagina **nieuw token configureren** om een nieuw toegangs token op te halen: 
      - **Toekennings type**: Selecteer een **autorisatie code**.
      - **URL voor terugbellen**: invoeren `https://www.localhost.com/` . 
      - **Verificatie-URL**: Voer in `https://login.microsoftonline.com/common/oauth2/authorize?resource=https://<your tenant name>.sharepoint.com` . Vervang door `<your tenant name>` de naam van uw eigen Tenant. 
      - **URL van toegangs token**: Enter `https://login.microsoftonline.com/common/oauth2/token` .
      - **Client-id**: Voer de principal-id van uw Aad-service in.
      - **Client geheim**: Voer het geheim van de Service-Principal in.
      - **Client verificatie**: Selecteer **verzenden als Basic auth-header**.
     
   1. U wordt gevraagd om u aan te melden met uw gebruikers naam en wacht woord.
   1. Wanneer u uw toegangs token hebt ontvangen, moet u het kopiëren en opslaan voor de volgende stap.
   
    [![Postman gebruiken om het toegangs token op te halen](./media/connector-odata/odata-project-online-postman-access-token-inline.png)](./media/connector-odata/odata-project-online-postman-access-token-expanded.png#lightbox)

1. De gekoppelde OData-service maken:
    - **Service-URL**: Voer in `https://<your tenant name>.sharepoint.com/sites/pwa/_api/Projectdata` . Vervang door `<your tenant name>` de naam van uw eigen Tenant. 
    - **Verificatie type**: Selecteer **anoniem**.
    - **Verificatie headers**:
        - **Eigenschaps naam**: Kies **autorisatie**.
        - **Waarde**: Voer het **toegangs token** in dat u uit stap 1 hebt gekopieerd.
    - De gekoppelde service testen.

    ![Gekoppelde OData-service maken](./media/connector-odata/odata-project-online-linked-service.png)

1. De OData-gegevensset maken:
    1. Maak de gegevensset met de gekoppelde OData-service die u hebt gemaakt in stap 2.
    1. Preview-gegevens.
 
    ![Preview-gegevens](./media/connector-odata/odata-project-online-preview-data.png)
 


## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Controleer de [opzoek activiteit](control-flow-lookup-activity.md)voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteunde gegevens archieven en-indelingen](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die door de Kopieer activiteit worden ondersteund als bronnen en sinks in azure Data Factory.