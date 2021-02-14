---
title: Webhook-activiteit in Azure Data Factory
description: De activiteit van de webhook blijft de uitvoering van de pijp lijn voortzetten totdat de gekoppelde gegevensset wordt gevalideerd met bepaalde criteria die de gebruiker opgeeft.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 435cad4d1ef002261b194431dbdb787e072808f5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361482"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Webhook-activiteit in Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met een webhook-activiteit kan de uitvoering van pijp lijnen via uw aangepaste code worden beheerd. Met de webhook-activiteit kan de code van klanten een eind punt aanroepen en een URL voor terugbellen door geven. De pijplijn uitvoering wacht op het aanroepen van de retour aanroep voordat de volgende activiteit wordt uitgevoerd.

## <a name="syntax"></a>Syntax

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>Type-eigenschappen

Eigenschap | Beschrijving | Toegestane waarden | Vereist
-------- | ----------- | -------------- | --------
**name** | De naam van de webhook-activiteit. | Tekenreeks | Ja |
**type** | Moet worden ingesteld op webhook. | Tekenreeks | Ja |
**methode** | De REST API methode voor het doel eindpunt. | Tekenreeks. Het ondersteunde type is ' POST '. | Yes |
**URL** | Het doel eindpunt en-pad. | Een teken reeks of expressie met de waarde van het **resultType** van een teken reeks. | Yes |
**koppen** | Kopteksten die naar de aanvraag worden verzonden. Hier volgt een voor beeld waarin de taal en het type van een aanvraag worden ingesteld: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Een teken reeks of expressie met de waarde van het **resultType** van een teken reeks. | Ja. Een `Content-Type` koptekst zoals `"headers":{ "Content-Type":"application/json"}` is vereist. |
**organen** | Vertegenwoordigt de nettolading die naar het eind punt wordt verzonden. | Een geldige JSON of een expressie met de waarde van het **resultType** van JSON. Zie [Payload-schema aanvragen](./control-flow-web-activity.md#request-payload-schema) voor het schema van de aanvraag lading. | Yes |
**verificatie** | De verificatie methode die wordt gebruikt om het eind punt aan te roepen. De ondersteunde typen zijn "Basic" en "ClientCertificate". Zie [Verificatie](./control-flow-web-activity.md#authentication) voor meer informatie. Als verificatie niet is vereist, sluit u deze eigenschap. | Een teken reeks of expressie met de waarde van het **resultType** van een teken reeks. | No |
**timeout** | Hoe lang de activiteit wacht op de call back die is opgegeven door **callBackUri** , om te worden aangeroepen. De standaard waarde is 10 minuten ("00:10:00"). De waarden hebben de time span-indeling *d*. *uu*:*mm*:*SS*. | Tekenreeks | No |
**Rapport status bij terugbellen** | Hiermee kan een gebruiker de mislukte status van een webhook-activiteit rapporteren. | Booleaans | No |

## <a name="authentication"></a>Verificatie

Een webhook-activiteit ondersteunt de volgende verificatie typen.

### <a name="none"></a>Geen

Als verificatie niet is vereist, neemt u de eigenschap **Authentication** niet op.

### <a name="basic"></a>Basic

Geef de gebruikers naam en het wacht woord op die moeten worden gebruikt met basis verificatie.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>Client certificaat

Geef de met base64 gecodeerde inhoud van een PFX-bestand en een wacht woord op.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Beheerde identiteit

Gebruik de beheerde identiteit van de data factory om de bron-URI op te geven waarvoor het toegangs token is aangevraagd. Gebruik om de Azure Resource Management-API aan te roepen `https://management.azure.com/` . Zie voor meer informatie over de werking van beheerde identiteiten het [overzicht beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Als uw data factory is geconfigureerd met een Git-opslag plaats, moet u uw referenties in Azure Key Vault opslaan om basis-of verificatie van client certificaten te gebruiken. Azure Data Factory slaat geen wacht woorden op in Git.

## <a name="additional-notes"></a>Aanvullende opmerkingen

Data Factory geeft de extra eigenschap **callBackUri** in de hoofd tekst die naar het URL-eind punt is verzonden. Data Factory verwacht dat deze URI wordt aangeroepen vóór de opgegeven time-outwaarde. Als de URI niet wordt aangeroepen, mislukt de activiteit met de status ' out '.

De webhook-activiteit mislukt wanneer de aanroep van het aangepaste eind punt mislukt. Elk fout bericht kan worden toegevoegd aan de hoofd tekst van de call back en wordt gebruikt in een latere activiteit.

Voor elke REST API-aanroep wordt een time-out van de client uitgevoerd als het eind punt niet binnen één minuut reageert. Dit gedrag is standaard HTTP best practice. Om dit probleem op te lossen, implementeert u een 202-patroon. In het huidige geval retourneert het eind punt 202 (geaccepteerd) en de client polls.

De time-out van één minuut op de aanvraag heeft niets te maken met de time-out van de activiteit. De laatste wordt gebruikt om te wachten op de call back die is opgegeven door **callbackUri**.

De hoofd tekst die is door gegeven aan de call back-URI moet een geldige JSON zijn. Stel de `Content-Type` koptekst in op `application/json` .

Wanneer u de eigenschap **rapport status bij terugbellen** gebruikt, moet u de volgende code aan de hoofd tekst toevoegen wanneer u de call back maakt:

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende controle stroom activiteiten die worden ondersteund door Data Factory:

- [If Condition Activity](control-flow-if-condition-activity.md)
- [Activiteit uitvoeren van pijplijn](control-flow-execute-pipeline-activity.md)
- [Voor elke activiteit](control-flow-for-each-activity.md)
- [Activiteit ophalen van metagegevens](control-flow-get-metadata-activity.md)
- [Opzoekactiviteit](control-flow-lookup-activity.md)
- [Webactiviteit](control-flow-web-activity.md)
- [Until-activiteit](control-flow-until-activity.md)