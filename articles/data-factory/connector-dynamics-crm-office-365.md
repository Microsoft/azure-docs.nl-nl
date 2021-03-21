---
title: Gegevens kopiëren in Dynamics (Common Data Service)
description: Informatie over het kopiëren van gegevens van micro soft Dynamics CRM of micro soft Dynamics 365 (Common Data Service/micro soft Dataverse) naar ondersteunde Sink-gegevens archieven of van ondersteunde brongegevens archieven naar Dynamics CRM of Dynamics 365 door gebruik te maken van een Kopieer activiteit in een data factory-pijp lijn.
ms.service: data-factory
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: ec24fa1bde21c70aa95fc33c92048aebc9f6659c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597382"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-servicemicrosoft-dataverse-or-dynamics-crm-by-using-azure-data-factory"></a>Gegevens kopiëren van en naar Dynamics 365 (Common Data Service/micro soft Dataverse) of Dynamics CRM door gebruik te maken van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel wordt beschreven hoe u een Kopieer activiteit in Azure Data Factory kunt gebruiken om gegevens te kopiëren van en naar micro soft Dynamics 365 en micro soft Dynamics CRM. Het is gebaseerd op het artikel overzicht van de [Kopieer activiteit](copy-activity-overview.md) . Dit geeft een algemeen overzicht van een Kopieer activiteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

Deze connector wordt ondersteund voor de volgende activiteiten:

- [Kopieer activiteit](copy-activity-overview.md) met [ondersteunde bron-en Sink-matrix](copy-activity-overview.md)
- [Activiteit Lookup](control-flow-lookup-activity.md)

U kunt gegevens uit Dynamics 365 (Common Data Service/micro soft Dataverse) of Dynamics CRM kopiëren naar een ondersteunde Sink-gegevens opslag. U kunt ook gegevens uit een ondersteund brongegevens archief kopiëren naar Dynamics 365 (Common Data Service) of Dynamics CRM. Zie de tabel [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevens archieven die door een Kopieer activiteit worden ondersteund als bronnen en Sinks.

Deze Dynamics-connector ondersteunt Dynamics versie 7 tot en met 9 voor zowel online als on-premises. Met name:

- Versie 7 wordt toegewezen aan Dynamics CRM 2015.
- Versie 8 wordt toegewezen aan Dynamics CRM 2016 en de vroege versie van Dynamics 365.
- Versie 9 wordt toegewezen aan de nieuwere versie van Dynamics 365.

Raadpleeg de volgende tabel met ondersteunde verificatie typen en configuraties voor Dynamics-versies en-producten.

| Dynamics-versies | Verificatietypen | Voor beelden van gekoppelde services |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 online <br/><br/> Dynamics CRM Online | Azure Active Directory-Service-Principal (Azure AD) <br/><br/> Office 365 | [Dynamics online-en Azure AD-Service-Principal-of Office 365-verificatie](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 on-premises met Internet gerichte implementatie (IFD) <br/><br/> Dynamics CRM 2016 on-premises met IFD <br/><br/> Dynamics CRM 2015 on-premises met IFD | IFD | [On-premises Dynamics met IFD en IFD-verificatie](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Voor Dynamics 365 worden de volgende toepassings typen ondersteund:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Deze connector biedt geen ondersteuning voor andere toepassings typen zoals Finance, Operations en talen.

>[!TIP]
>U kunt de [Dynamics AX-connector](connector-dynamics-ax.md)gebruiken om gegevens uit Dynamics 365-Financiën en-bewerkingen te kopiëren.

Deze Dynamics-connector is gebaseerd op [Dynamics xRM-hulp middelen](/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

## <a name="prerequisites"></a>Vereisten

Als u deze connector met Azure AD-Service-Principal-verificatie wilt gebruiken, moet u server-naar-Server-verificatie (S2S) in Common Data Service of Dynamics instellen. Raadpleeg [dit artikel](/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) voor gedetailleerde stappen.

## <a name="get-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over eigenschappen die worden gebruikt voor het definiëren van Data Factory entiteiten die specifiek zijn voor Dynamics.

## <a name="linked-service-properties"></a>Eigenschappen van gekoppelde service

De volgende eigenschappen worden ondersteund voor de Dynamics gekoppelde service.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 en Dynamics CRM Online

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op "Dynamics", "DynamicsCrm" of "CommonDataServiceForApps". | Yes |
| deploymentType | Het implementatie type van het Dynamics-exemplaar. De waarde moet ' online ' zijn voor Dynamics online. | Yes |
| serviceUri | De service-URL van uw Dynamics-exemplaar, dezelfde versie die u hebt geopend vanuit de browser. Een voor beeld is "https:// \<organization-name> . CRM [x]. Dynamics. com". | Yes |
| authenticationType | Het verificatie type om verbinding te maken met een Dynamics-Server. Geldige waarden zijn ' AADServicePrincipal ' en ' Office365 '. | Yes |
| servicePrincipalId | De client-ID van de Azure AD-toepassing. | Ja wanneer verificatie ' AADServicePrincipal ' is |
| servicePrincipalCredentialType | Het referentie type dat moet worden gebruikt voor Service-Principal-verificatie. Geldige waarden zijn ' ServicePrincipalKey ' en ' ServicePrincipalCert '. | Ja wanneer verificatie ' AADServicePrincipal ' is |
| servicePrincipalCredential | De Service-Principal-referentie. <br/><br/>Wanneer u ' ServicePrincipalKey ' als referentie type gebruikt, `servicePrincipalCredential` kan dit een teken reeks zijn die tijdens de implementatie van de gekoppelde service Azure Data Factory versleuteld. Het kan ook een verwijzing naar een geheim in Azure Key Vault zijn. <br/><br/>Wanneer u ' ServicePrincipalCert ' als referentie gebruikt, `servicePrincipalCredential` moet u een verwijzing naar een certificaat in azure Key Vault hebben. | Ja wanneer verificatie ' AADServicePrincipal ' is |
| gebruikersnaam | De gebruikers naam om verbinding te maken met Dynamics. | Ja wanneer verificatie ' Office365 ' is |
| wachtwoord | Het wacht woord voor het gebruikers account dat u hebt opgegeven als de gebruikers naam. Markeer dit veld met SecureString om het veilig op te slaan in Data Factory, of om te [verwijzen naar een geheim dat is opgeslagen in azure Key Vault](store-credentials-in-key-vault.md). | Ja wanneer verificatie ' Office365 ' is |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Als er geen waarde is opgegeven, gebruikt de eigenschap de standaard Azure Integration runtime. | No |

>[!NOTE]
>De Dynamics-connector heeft voorheen de eigenschap **organisatie naam** gebruikt om uw Dynamics CRM-of Dynamics 365 online-exemplaar te identificeren. Hoewel deze eigenschap nog steeds werkt, wordt u aangeraden de nieuwe **serviceUri** -eigenschap op te geven in plaats van de prestaties van de detectie van instanties te verbeteren.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>Voor beeld: Dynamics online met Azure AD-Service-Principal-en sleutel verificatie

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>Voor beeld: Dynamics online met Azure AD-Service-Principal-en certificaat verificatie

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

#### <a name="example-dynamics-online-using-office-365-authentication"></a>Voor beeld: Dynamics online met Office 365-verificatie

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 en Dynamics CRM on-premises met IFD

Aanvullende eigenschappen die worden vergeleken met Dynamics online zijn **hostnaam** en **poort**.

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op "Dynamics", "DynamicsCrm" of "CommonDataServiceForApps". | Ja. |
| deploymentType | Het implementatie type van het Dynamics-exemplaar. De waarde moet ' OnPremisesWithIfd ' zijn voor on-premises Dynamics op locatie met IFD.| Ja. |
| Hostnaam | De hostnaam van de on-premises Dynamics-Server. | Ja. |
| poort | De poort van de on-premises Dynamics-Server. | Nee. De standaard waarde is 443. |
| organisatie naam | De naam van de organisatie van het Dynamics-exemplaar. | Ja. |
| authenticationType | Het verificatie type om verbinding te maken met de Dynamics-Server. Geef ' IFD ' op voor on-premises Dynamics met IFD. | Ja. |
| gebruikersnaam | De gebruikers naam om verbinding te maken met Dynamics. | Ja. |
| wachtwoord | Het wacht woord voor het gebruikers account dat u hebt opgegeven voor de gebruikers naam. U kunt dit veld markeren met SecureString om het veilig op te slaan in Data Factory. Of u kunt een wacht woord opslaan in Key Vault en de Kopieer activiteit laten ophalen wanneer het een gegevens kopie is. Meer informatie over [Store-referenties vindt u in Key Vault](store-credentials-in-key-vault.md). | Ja. |
| connectVia | De [Integration runtime](concepts-integration-runtime.md) die moet worden gebruikt om verbinding te maken met het gegevens archief. Als er geen waarde is opgegeven, gebruikt de eigenschap de standaard Azure Integration runtime. | No |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>Voor beeld: on-premises Dynamics met IFD met IFD-verificatie

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

Zie het artikel [gegevens sets](concepts-datasets-linked-services.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van gegevens sets. In deze sectie vindt u een lijst met eigenschappen die door Dynamics DataSet worden ondersteund.

Als u gegevens wilt kopiëren van en naar Dynamics, worden de volgende eigenschappen ondersteund:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de gegevensset moet worden ingesteld op "DynamicsEntity", "DynamicsCrmEntity" of "CommonDataServiceForAppsEntity". |Yes |
| entityName | De logische naam van de entiteit die moet worden opgehaald. | Nee voor bron als de activiteit bron is opgegeven als ' query ' en ja voor Sink |

#### <a name="example"></a>Voorbeeld

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie het artikel [pijp lijnen](concepts-pipelines-activities.md) voor een volledige lijst met secties en eigenschappen die beschikbaar zijn voor het definiëren van activiteiten. In deze sectie vindt u een lijst met eigenschappen die worden ondersteund door de typen Dynamics-bron en-Sink.

### <a name="dynamics-as-a-source-type"></a>Dynamics als bron type

Als u gegevens wilt kopiëren uit Dynamics, ondersteunt het gedeelte **bron** van Kopieer activiteit de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron van de Kopieer activiteit moet worden ingesteld op "DynamicsSource", "DynamicsCrmSource" of "CommonDataServiceForAppsSource". | Yes |
| query | FetchXML is een eigen query taal die wordt gebruikt in Dynamics online en on-premises. Zie het volgende voorbeeld Zie [Query's bouwen met FetchXML](/previous-versions/dynamicscrm-2016/developers-guide/gg328332(v=crm.8))voor meer informatie. | Nee if `entityName` in de gegevensset is opgegeven |

>[!NOTE]
>De PK-kolom wordt altijd gekopieerd, zelfs als de kolom projectie die u configureert in de FetchXML-query deze niet bevat.

> [!IMPORTANT]
>- Wanneer u gegevens uit Dynamics kopieert, is expliciete kolom toewijzing van Dynamics naar Sink optioneel. We raden u echter aan om een deterministische Kopieer resultaat te bieden.
>- Wanneer Data Factory een schema in de gebruikers interface van de ontwerp functie importeert, wordt het schema afgeschreven. Dit doet u door de bovenste rijen van het resultaat van de Dynamics-query te bemonsteren om de bron kolom lijst te initialiseren. In dat geval worden kolommen zonder waarden in de bovenste rijen wegge laten. Dit geldt ook voor het kopiëren van uitvoeringen als er geen expliciete toewijzing is. U kunt meer kolommen controleren en toevoegen aan de toewijzing, die tijdens de Kopieer runtime worden voldaan.

#### <a name="example"></a>Voorbeeld

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Voor beeld van FetchXML-query

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics als Sink-type

Als u gegevens wilt kopiëren naar Dynamics, ondersteunt het gedeelte **sink** van Kopieer activiteit de volgende eigenschappen:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de Sink voor kopieer activiteiten moet worden ingesteld op ' DynamicsSink ', ' DynamicsCrmSink ' of ' CommonDataServiceForAppsSink '. | Ja. |
| writeBehavior | Het schrijf gedrag van de bewerking. De waarde moet ' Upsert ' zijn. | Yes |
| alternateKeyName | De alternatieve sleutel naam die op uw entiteit is gedefinieerd om een upsert uit te voeren. | Nee. |
| writeBatchSize | Het aantal rijen van gegevens dat in elke batch naar Dynamics is geschreven. | Nee. De standaardwaarde is 10. |
| ignoreNullValues | Hiermee wordt aangegeven of Null-waarden moeten worden genegeerd voor andere invoer gegevens dan de sleutel velden tijdens een schrijf bewerking.<br/><br/>Geldige waarden zijn **True** en **False**:<ul><li>**Waar**: laat de gegevens in het doel object ongewijzigd wanneer u een upsert of update-bewerking doet. Voeg een gedefinieerde standaard waarde in wanneer u een INSERT-bewerking wilt uitvoeren.</li><li>**Onwaar**: werk de gegevens in het doel object bij naar een null-waarde wanneer u een upsert of update-bewerking uitgevoerd. Voeg een null-waarde toe wanneer u een INSERT-bewerking uitgevoerd.</li></ul> | Nee. De standaard waarde is **False**. |
| maxConcurrentConnections |De bovengrens van gelijktijdige verbindingen die tot het gegevens archief zijn gemaakt tijdens de uitvoering van de activiteit. Geef alleen een waarde op als u gelijktijdige verbindingen wilt beperken.| No |

>[!NOTE]
>De standaard waarde voor de Sink- **writeBatchSize** en de **[parallelCopies](copy-activity-performance-features.md#parallel-copy)** voor de Kopieer activiteit voor de Dynamics-sink is 10. Daarom worden records van 100 standaard gelijktijdig verzonden naar Dynamics.

Voor Dynamics 365 online geldt een limiet van [twee gelijktijdige batch-aanroepen per organisatie](/previous-versions/dynamicscrm-2016/developers-guide/jj863631(v=crm.8)#Run-time%20limitations). Als deze limiet wordt overschreden, wordt de uitzonde ring ' server bezet ' gegenereerd voordat de eerste aanvraag wordt uitgevoerd. Bewaar **writeBatchSize** op 10 of minder om te voor komen dat gelijktijdige aanroepen worden beperkt.

De optimale combi natie van **writeBatchSize** en **parallelCopies** is afhankelijk van het schema van uw entiteit. Schema-elementen bevatten het aantal kolommen, de Rijgrootte en het aantal invoeg toepassingen, werk stromen of werk stroom activiteiten dat aan deze aanroepen is toegevoegd. De standaard instelling van **writeBatchSize** (10) &times; **parallelCopies** (10) is de aanbeveling volgens de Dynamics-service. Deze waarde werkt voor de meeste Dynamics-entiteiten, hoewel het mogelijk niet de beste prestaties biedt. U kunt de prestaties afstemmen door de combi natie te wijzigen in de instellingen voor de Kopieer activiteit.

#### <a name="example"></a>Voorbeeld

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="retrieving-data-from-views"></a>Gegevens ophalen uit weer gaven

Als u gegevens wilt ophalen uit Dynamics-weer gaven, moet u de opgeslagen query van de weer gave ophalen en de query gebruiken om de gegevens op te halen.

Er zijn twee entiteiten die verschillende soorten weer gave opslaan: de systeem weergave ' opgeslagen query's ' en ' gebruikers query ' slaat gebruikers weergave op. Als u de gegevens van de weer gaven wilt ophalen, raadpleegt u de volgende FetchXML-query en vervangt u ' TARGETENTITY ' door `savedquery` of `userquery` . Elk entiteits type heeft meer beschik bare kenmerken die u aan de query kunt toevoegen op basis van uw behoeften. Meer informatie over de entiteit [savedquery](https://docs.microsoft.com/dynamics365/customer-engagement/web-api/savedquery) en [userquery](https://docs.microsoft.com/dynamics365/customer-engagement/web-api/userquery).

```xml
<fetch top="5000" >
  <entity name="<TARGETENTITY>">
    <attribute name="name" />
    <attribute name="fetchxml" />
    <attribute name="returnedtypecode" />
    <attribute name="querytype" />
  </entity>
</fetch>
```

U kunt ook filters toevoegen om de weer gaven te filteren. Voeg bijvoorbeeld het volgende filter toe om een weer gave met de naam ' Mijn actieve accounts ' in account entiteit op te halen.

```xml
<filter type="and" >
    <condition attribute="returnedtypecode" operator="eq" value="1" />
    <condition attribute="name" operator="eq" value="My Active Accounts" />
</filter>
```

## <a name="data-type-mapping-for-dynamics"></a>Toewijzing van gegevens type voor Dynamics

Wanneer u gegevens uit Dynamics kopieert, worden in de volgende tabel de toewijzingen van Dynamics-gegevens typen naar Data Factory tussenliggende gegevens typen weer gegeven. Zie [schema en gegevens type toewijzingen](copy-activity-schema-and-type-mapping.md)voor meer informatie over hoe een Kopieer activiteit wordt toegewezen aan een bron schema en een gegevens type wordt toegewezen aan een sink.

Configureer het bijbehorende Data Factory gegevens type in een gegevensset structuur die is gebaseerd op uw bron Dynamics-gegevens type met behulp van de volgende toewijzings tabel:

| Dynamics-gegevens type | Data Factory tussentijds gegevens type | Ondersteund als bron | Ondersteund als Sink |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode. BigInt | Omvang | ✓ | ✓ |
| AttributeTypeCode. Boolean | Booleaans | ✓ | ✓ |
| AttributeType. klant | GUID | ✓ | ✓ (Zie de [richt lijnen](#writing-data-to-a-lookup-field)) |
| AttributeType. DateTime | Datum/tijd | ✓ | ✓ |
| AttributeType. decimaal | Decimaal | ✓ | ✓ |
| AttributeType. Double | Dubbel | ✓ | ✓ |
| AttributeType. EntityName | Tekenreeks | ✓ | ✓ |
| AttributeType. integer | Int32 | ✓ | ✓ |
| AttributeType. lookup | GUID | ✓ | ✓ (Zie de [richt lijnen](#writing-data-to-a-lookup-field)) |
| AttributeType. ManagedProperty | Booleaans | ✓ | |
| AttributeType. Memo | Tekenreeks | ✓ | ✓ |
| AttributeType. geld | Decimaal | ✓ | ✓ |
| AttributeType. owner | GUID | ✓ | ✓ (Zie de [richt lijnen](#writing-data-to-a-lookup-field)) |
| AttributeType. selectie lijst | Int32 | ✓ | ✓ |
| AttributeType. uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType. String | Tekenreeks | ✓ | ✓ |
| AttributeType. status | Int32 | ✓ | ✓ |
| AttributeType. status | Int32 | ✓ | ✓ |

> [!NOTE]
> De Dynamics-gegevens typen **AttributeType. CalendarRules**, **AttributeType. MultiSelectPicklist** en **AttributeType. PartyList** worden niet ondersteund.

## <a name="writing-data-to-a-lookup-field"></a>Gegevens schrijven naar een opzoek veld

Als u gegevens wilt schrijven naar een opzoek veld met meerdere doelen als klant en eigenaar, volgt u deze richt lijnen en bijvoorbeeld:

1. Zorg ervoor dat de bron zowel de veld waarde als de bijbehorende naam van de doel entiteit bevat.
   - Als alle records worden toegewezen aan dezelfde doel entiteit, controleer dan een van de volgende voor waarden:
      - De bron gegevens hebben een kolom waarin de naam van de doel entiteit wordt opgeslagen.
      - U hebt een extra kolom toegevoegd aan de bron van de Kopieer activiteit om de doel entiteit te definiëren.
   - Als verschillende records zijn gekoppeld aan verschillende doel entiteiten, zorgt u ervoor dat de bron gegevens een kolom hebben waarin de bijbehorende naam van de doel entiteit wordt opgeslagen.

1. Wijs zowel de kolom waarde als entiteit verwijzing toe van de bron naar de sink. De verwijzings kolom entiteit moet worden toegewezen aan een virtuele kolom met het speciale naamgevings patroon `{lookup_field_name}@EntityReference` . De kolom bestaat niet echt in Dynamics. Dit wordt gebruikt om aan te geven dat deze kolom de kolom meta gegevens is van het opgegeven opzoek veld voor meerdere doel items.

Stel bijvoorbeeld dat de bron deze twee kolommen bevat:

- Kolom **CustomerField** van het type **GUID**. Dit is de primaire sleutel waarde van de doel entiteit in Dynamics.
- **Doel** kolom van het type **teken reeks**. Dit is de logische naam van de doel entiteit.

Stel ook dat u dergelijke gegevens wilt kopiëren naar het entiteits veld van de Sink Dynamics- **CustomerField** van het type **klant**.

Wijs in kolom toewijzing van Copy-activiteit de twee kolommen als volgt toe:

- **CustomerField** naar **CustomerField**. Deze toewijzing is de normale veld toewijzing.
- **Doel** naar **CustomerField \@ EntityReference**. De kolom sink is een virtuele kolom die de verwijzing naar de entiteit vertegenwoordigt. Voer dergelijke veld namen in een toewijzing in, aangezien deze niet worden weer gegeven door het importeren van schema's.

![Dynamics Lookup-veld toewijzing van kolom](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

Als alle bron records zijn gekoppeld aan dezelfde doel entiteit en de bron gegevens niet de naam van de doel entiteit bevatten, is hier een snelkoppeling: Voeg in de bron Kopieer activiteit een extra kolom toe. Noem de nieuwe kolom met behulp van het patroon `{lookup_field_name}@EntityReference` , stel de waarde in op de naam van de doel entiteit en ga daarna verder met de kolom toewijzing. Als de namen van de bron-en Sink-kolom identiek zijn, kunt u ook expliciete kolom toewijzing overs Laan, omdat met de Kopieer activiteit standaard kolommen worden toegewezen op naam.

![Dynamics Lookup-veld een kolom van het verwijzings item toevoegen](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Eigenschappen van opzoek activiteit

Zie [lookup activity](control-flow-lookup-activity.md)(Engelstalig) voor meer informatie over de eigenschappen.

## <a name="next-steps"></a>Volgende stappen
Zie [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevens die de Kopieer activiteit in Data Factory ondersteunt als bronnen en Sinks.