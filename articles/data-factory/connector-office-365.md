---
title: Gegevens kopiëren van Office 365 met behulp van Azure Data Factory
description: Informatie over het kopiëren van gegevens uit Office 365 naar ondersteunde Sink-gegevens archieven met behulp van Kopieer activiteit in een Azure Data Factory pijp lijn.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: a7df69e7c5701074b40d6fa8340d8a0e247f00de
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393000"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Gegevens kopiëren van Office 365 naar Azure met behulp van Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory kan worden geïntegreerd met [Microsoft Graph data Connect](/graph/data-connect-concept-overview), zodat u de uitgebreide organisatie gegevens in uw Office 365-Tenant op schaal bare wijze in azure kunt brengen en analyse toepassingen kunt bouwen en inzichten kunt ophalen op basis van deze waardevolle gegevensassets. Integratie met Privileged Access Management biedt beveiligd toegangs beheer voor de waardevolle gecuratore gegevens in Office 365.  Raadpleeg [deze koppeling](/graph/data-connect-concept-overview) voor een overzicht van Microsoft Graph data Connect en Raadpleeg [deze koppeling](/graph/data-connect-policies#licensing) voor informatie over licenties.

In dit artikel wordt beschreven hoe u de Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens uit Office 365 te kopiëren. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van de Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden
Met de ADF Office 365-connector en Microsoft Graph data Connect kunt u op grote schaal opname van verschillende typen gegevens sets van post vakken van Exchange-e-mail berichten maken, zoals contact personen in het adres boek, agenda-items, e-mail berichten, gebruikers gegevens, Postvak instellingen, enzovoort.  [Hier](/graph/data-connect-datasets) vindt u een overzicht van de volledige lijst met beschik bare gegevens sets.

U kunt nu binnen één Kopieer activiteit **gegevens alleen kopiëren van Office 365 naar [Azure Blob Storage](connector-azure-blob-storage.md), [Azure data Lake Storage GEN1](connector-azure-data-lake-store.md)en [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md) in JSON-indeling** (type setOfObjects). Als u Office 365 wilt laden in andere typen gegevens opslag of in andere indelingen, kunt u de eerste Kopieer activiteit koppelen aan een volgende Kopieer activiteit om gegevens te laden in een van de [ondersteunde ADF-doel archieven](copy-activity-overview.md#supported-data-stores-and-formats) (Zie ' ondersteund als een Sink-kolom ' in de tabel ' ondersteunde gegevens archieven en-indelingen ').

>[!IMPORTANT]
>- Het Azure-abonnement met de data factory en het sink-gegevens archief moeten zich onder dezelfde Azure Active Directory (Azure AD) Tenant benemen als Office 365-Tenant.
>- Zorg ervoor dat de regio Azure Integration Runtime gebruikt voor kopieer activiteiten en dat de bestemming zich in dezelfde regio bevindt als de Office 365-postbus van de Tenant gebruikers. [Hier](concepts-integration-runtime.md#integration-runtime-location) vindt u informatie over de wijze waarop de Azure IR locatie wordt bepaald. Raadpleeg [hier de tabel](/graph/data-connect-datasets#regions) voor de lijst met ondersteunde Office-regio's en de bijbehorende Azure-regio's.
>- Service-Principal-verificatie is het enige verificatie mechanisme dat wordt ondersteund voor Azure Blob Storage, Azure Data Lake Storage Gen1 en Azure Data Lake Storage Gen2 als doel archief.

## <a name="prerequisites"></a>Vereisten

Als u gegevens wilt kopiëren van Office 365 naar Azure, moet u de volgende vereiste stappen uitvoeren:

- Uw Office 365-Tenant beheerder moet on-board acties volt ooien, zoals [hier](/graph/data-connect-get-started)wordt beschreven.
- Een Azure AD-webtoepassing maken en configureren in Azure Active Directory.  Zie [een Azure AD-toepassing maken](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)voor instructies.
- Noteer de volgende waarden, die u moet gebruiken om de gekoppelde service voor Office 365 te definiëren:
    - Tenant-ID. Zie [Tenant-id ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)voor instructies.
    - Toepassings-ID en toepassings sleutel.  Zie [toepassings-id en verificatie sleutel ophalen](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)voor instructies.
- Voeg de gebruikers-id toe die de gegevens toegangs aanvraag maakt als de eigenaar van de Azure AD-webtoepassing (van de Azure AD Web Application > instellingen > eigen aren > een eigenaar toevoegen). 
    - De gebruikers-id moet zich bevinden in de Office 365-organisatie waarvan u de gegevens ophaalt en mag geen gast gebruiker zijn.

## <a name="approving-new-data-access-requests"></a>Nieuwe aanvragen voor gegevens toegang goed keuren

Als dit de eerste keer is dat u gegevens aanvraagt voor deze context (een combi natie van welke gegevens tabel wordt geopend, met welk doel account worden de gegevens geladen en welke gebruikers-id de aanvraag voor gegevens toegang maakt, wordt de status van de Kopieer activiteit als ' wordt uitgevoerd ' weer gegeven. Als u op de [koppeling Details onder acties](copy-activity-overview.md#monitoring) klikt, ziet u de status als ' RequestingConsent '.  Een lid van de groep voor het goed keuren van gegevens toegang moet de aanvraag goed keuren in de Privileged Access Management voordat de gegevens extractie kan worden voortgezet.

[Hier](/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) vindt u informatie over de manier waarop de fiatteur de aanvraag voor gegevens toegang kan goed keuren, en [hier](/graph/data-connect-pam) vindt u een uitleg over de algehele integratie met privileged Access Management, met inbegrip van hoe u de groep voor het instellen van gegevens toegang instelt.

## <a name="policy-validation"></a>Beleidsvalidatie

Als ADF is gemaakt als onderdeel van een beheerde app en Azure-beleids toewijzingen worden uitgevoerd op resources binnen de beheer resource groep, wordt voor elke uitvoering van de Kopieer activiteit gecontroleerd of de beleids toewijzingen worden afgedwongen. [Hier](/graph/data-connect-policies#policies) vindt u een lijst met ondersteunde beleids regels.

## <a name="getting-started"></a>Aan de slag

>[!TIP]
>Zie voor een overzicht van het gebruik van Office 365 connector [gegevens laden uit het Office 365](load-office-365-data.md) -artikel.

U kunt met behulp van een van de volgende hulpprogram ma's of Sdk's een pijp lijn maken met de Kopieer activiteit. Selecteer een koppeling om naar een zelf studie te gaan met stapsgewijze instructies voor het maken van een pijp lijn met een Kopieer activiteit. 

- [Azure-portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager sjabloon](quickstart-create-data-factory-resource-manager-template.md). 

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Office 365 connector.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor Office 365 gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Office365** | Yes |
| office365TenantId | De Azure-Tenant-ID waartoe het Office 365-account behoort. | Yes |
| servicePrincipalTenantId | Geef de Tenant gegevens op waaronder uw Azure AD-webtoepassing zich bevindt. | Yes |
| servicePrincipalId | Geef de client-ID van de toepassing op. | Yes |
| servicePrincipalKey | Geef de sleutel van de toepassing op. Markeer dit veld als een SecureString om het veilig op te slaan in Data Factory. | Yes |
| connectVia | Het Integration Runtime dat moet worden gebruikt om verbinding te maken met het gegevens archief.  Als u niets opgeeft, wordt de standaard Azure Integration Runtime gebruikt. | No |

>[!NOTE]
> Het verschil tussen **office365TenantId** en **servicePrincipalTenantId** en de bijbehorende waarde om het volgende te bieden:
>- Als u een bedrijfs ontwikkelaar bent die een toepassing ontwikkelt op basis van Office 365-gegevens voor het gebruik van uw eigen organisatie, moet u dezelfde Tenant-ID voor beide eigenschappen opgeven. Dit is de AAD-Tenant-ID van uw organisatie.
>- Als u een ISV-ontwikkelaar bent die een toepassing ontwikkelt voor uw klanten, zijn office365TenantId van uw klant (Application Installer) de AAD-Tenant-ID van uw bedrijf en servicePrincipalTenantId wordt de AAD-Tenant-ID van uw organisatie.

**Voorbeeld:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. Deze sectie bevat een lijst met eigenschappen die door Office 365 DataSet worden ondersteund.

Als u gegevens wilt kopiëren uit Office 365, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op: **Office365Table** | Yes |
| tableName | De naam van de gegevensset die moet worden geëxtraheerd uit Office 365. [Hier](/graph/data-connect-datasets#datasets) vindt u een lijst met Office 365-gegevens sets die beschikbaar zijn voor uitpakken. | Yes |

Als u hebt ingesteld `dateFilterColumn` , `startTime` , `endTime` en `userScopeFilterUri` in DataSet, wordt deze nog steeds ondersteund als-is, terwijl u het nieuwe model in de activiteit bron wilt gebruiken.

**Voorbeeld**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door Office 365-bron.

### <a name="office-365-as-source"></a>Office 365 als bron

Als u gegevens wilt kopiëren uit Office 365, worden de volgende eigenschappen ondersteund in de sectie **bron** van de Kopieer activiteit:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet zijn ingesteld op: **Office365Source** | Yes |
| allowedGroups | Predikaat groep selecteren.  Gebruik deze eigenschap om Maxi maal 10 gebruikers groepen te selecteren voor wie de gegevens worden opgehaald.  Als er geen groepen zijn opgegeven, worden de gegevens voor de hele organisatie geretourneerd. | No |
| userScopeFilterUri | Wanneer `allowedGroups` de eigenschap niet is opgegeven, kunt u een predicaat expressie gebruiken die wordt toegepast op de hele Tenant om de specifieke rijen te filteren die moeten worden geëxtraheerd uit Office 365. De predicaat indeling moet overeenkomen met de query-indeling van Microsoft Graph Api's, bijvoorbeeld `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` . | No |
| dateFilterColumn | De naam van de datum filter kolom. Gebruik deze eigenschap om het tijds bereik te beperken waarvoor Office 365-gegevens worden geëxtraheerd. | Ja als DataSet een of meer DateTime-kolommen bevat. [Hier](/graph/data-connect-filtering#filtering) vindt u een lijst met gegevens sets waarvoor dit datetime-filter nodig is. |
| startTime | Start datum-waarde voor filteren op. | Ja als `dateFilterColumn` is opgegeven |
| endTime | Eind datum/-waarde waarop moet worden gefilterd. | Ja als `dateFilterColumn` is opgegeven |
| outputColumns | Matrix van de kolommen die naar Sink moeten worden gekopieerd. | No |

**Voorbeeld:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens archieven die worden ondersteund als bronnen en sinks op basis van de Kopieer activiteit in azure Data Factory.