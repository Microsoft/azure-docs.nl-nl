---
title: Vertaling starten
titleSuffix: Azure Cognitive Services
description: Start een aanvraag voor documentvertaling met de Service voor documentvertaling.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 1d167962e22953a4a9ca69ece347f8427b9218c9
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836245"
---
# <a name="start-translation"></a>Vertaling starten

Gebruik deze API om een bulksgewijs (batch)-vertaalaanvraag te starten met de Service voor documentvertaling. Elke aanvraag kan meerdere documenten bevatten en moet een bron- en doelcontainer voor elk document bevatten.

Het voorvoegsel en het achtervoegselfilter (indien opgegeven) worden gebruikt om mappen te filteren. Het voorvoegsel wordt toegepast op het subpad na de containernaam.

Woordenlijsten/vertaalgeheugen kunnen worden opgenomen in de aanvraag en worden toegepast door de service wanneer het document wordt vertaald.

Als de woordenlijst ongeldig of onbereikbaar is tijdens de vertaling, wordt er een fout aangegeven in de documentstatus. Als er al een bestand met dezelfde naam op de bestemming bestaat, wordt het overschreven. De targetUrl voor elke doeltaal moet uniek zijn.

## <a name="request-url"></a>Aanvraag-URL

Verzend een `POST` aanvraag naar:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Meer informatie over het vinden [van uw aangepaste domeinnaam](../get-started-with-document-translation.md#find-your-custom-domain-name).

> [!IMPORTANT]
>
> * **Voor alle API-aanvragen voor de documentvertalingsservice is een aangepast domein-eindpunt vereist.**
> * U kunt het eindpunt dat u op uw  Azure Portal resourcesleutels en eindpuntpagina hebt gevonden, noch het globale translator-eindpunt gebruiken om HTTP-aanvragen te maken voor `api.cognitive.microsofttranslator.com` documentvertaling.

## <a name="request-headers"></a>Aanvraagheaders

Aanvraagheaders zijn:

|Kopteksten|Beschrijving|
|--- |--- |
|Ocp-Apim-Subscription-Key|Vereiste aanvraagheader|

## <a name="request-body-batch-submission-request"></a>Aanvraag body: batch indienen aanvraag

|Naam|Type|Description|
|--- |--- |--- |
|Ingangen|BatchRequest[]|BatchRequest wordt hieronder vermeld. De invoerlijst met documenten of mappen met documenten. Mediatypen: "application/json", "text/json", "application/*+json".|

### <a name="inputs"></a>Invoerwaarden

Definitie voor de aanvraag voor batchvertaling van invoer.

|Naam|Type|Vereist|Beschrijving|
|--- |--- |--- |--- |
|source|SourceInput[]|Waar|inputs.source die hieronder wordt vermeld. Bron van de invoerdocumenten.|
|storageType|StorageInputType[]|Waar|inputs.storageType wordt hieronder vermeld. Opslagtype van de bronreeks van de invoerdocumenten.|
|Doelstellingen|TargetInput[]|Waar|inputs.target die hieronder wordt vermeld. Locatie van de bestemming voor de uitvoer.|

**inputs.source**

Bron van de invoerdocumenten.

|Naam|Type|Vereist|Beschrijving|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Niet waar|DocumentFilter[] hieronder vermeld.|
|filter.prefix|tekenreeks|Niet waar|Een tekenreeks met een casegevoelig voorvoegsel om documenten in het bronpad te filteren voor vertaling. Als u bijvoorbeeld een Azure Storage Blob URI gebruikt, gebruikt u het voorvoegsel om submappen voor vertaling te beperken.|
|filter.achtervoegsel|tekenreeks|Niet waar|Een tekenreeks met een casegevoelig achtervoegsel om documenten in het bronpad te filteren voor vertaling. Dit wordt meestal gebruikt voor bestandsextensies.|
|language|tekenreeks|Niet waar|Taalcode Als er geen is opgegeven, wordt automatisch detecteren op het document.|
|sourceUrl|tekenreeks|True|Locatie van de map/container of één bestand met uw documenten.|
|storageSource|StorageSource|Niet waar|StorageSource die hieronder wordt vermeld.|
|storageSource.AzureBlob|tekenreeks|Niet waar||

**inputs.storageType**

Opslagtype van de bronreeks van de invoerdocumenten.

|Naam|Type|
|--- |--- |
|file|tekenreeks|
|map|tekenreeks|

**inputs.target**

Doel voor de voltooide vertaalde documenten.

|Naam|Type|Vereist|Beschrijving|
|--- |--- |--- |--- |
|category|tekenreeks|Niet waar|Categorie/aangepast systeem voor vertaalaanvraag.|
|Glossaria|Verklarende woordenlijst[]|Niet waar|Verklarende woordenlijst die hieronder wordt vermeld. Lijst met woordenlijst.|
|glossaries.format|tekenreeks|Niet waar|Formaat.|
|glossaries.glossaryUrl|tekenreeks|Waar (als u woordenlijsten gebruikt)|Locatie van de woordenlijst. We gebruiken de bestandsextensie om de opmaak te extraheren als de indelingsparameter niet is opgegeven. Als het vertaaltaalpaar niet aanwezig is in de woordenlijst, wordt het niet toegepast.|
|glossaries.storageSource|StorageSource|Niet waar|StorageSource die hierboven wordt vermeld.|
|targetUrl|tekenreeks|True|Locatie van de map/container met uw documenten.|
|language|tekenreeks|True|Doeltaalcode van twee letters. Zie [de lijst met taalcodes](../../language-support.md).|
|storageSource|StorageSource []|Niet waar|StorageSource [] hierboven vermeld.|
|versie|tekenreeks|Niet waar|Versie.|

## <a name="example-request"></a>Voorbeeldaanvraag

Hier volgen enkele voorbeelden van batchaanvragen.

**Alle documenten in een container vertalen**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Alle documenten in een container vertalen die woordenlijsten toepassen**

Zorg ervoor dat u een verklarende URL hebt & SAS-token voor de specifieke blob/het specifieke document (niet voor de container)

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
     "glossaries": [
                        {
                            "glossaryUrl": https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D,
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**Een specifieke map in een container vertalen**

Zorg ervoor dat u de mapnaam (casegevoelig) als voorvoegsel in het filter hebt opgegeven, hoewel het SAS-token nog steeds voor de container is.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D,
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Een specifiek document in een container vertalen**

* Zorg ervoor dat u 'storageType': 'File' hebt opgegeven
* Zorg ervoor dat u de bron-URL hebt & SAS-token voor de specifieke blob/het specifieke document (niet voor de container)
* Zorg ervoor dat u de doelbestandsnaam hebt opgegeven als onderdeel van de doel-URL, hoewel het SAS-token nog steeds voor de container is.
* Voorbeeldaanvraag hieronder toont één document dat wordt vertaald in twee doeltalen

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>Antwoordstatuscodes

Hier volgen de mogelijke HTTP-statuscodes die een aanvraag retourneert.

|Statuscode|Description|
|--- |--- |
|202|Aanvaard. Een geslaagde aanvraag en de batchaanvraag worden gemaakt door de service. De header Operation-Location geeft een status-URL aan met de bewerking ID.HeadersOperation-Location: string|
|400|Slechte aanvraag. Ongeldige aanvraag. Controleer de invoerparameters.|
|401|Onbevoegde. Controleer uw referenties.|
|429|De aanvraagsnelheid is te hoog.|
|500|Interne serverfout.|
|503|De service is momenteel niet beschikbaar.  Probeert u het later nog eens.|
|Andere statuscodes|<ul><li>Te veel aanvragen</li><li>Server tijdelijk niet beschikbaar</li></ul>|

## <a name="error-response"></a>Foutbericht

|Naam|Type|Description|
|--- |--- |--- |
|code|tekenreeks|Enums met foutcodes op hoog niveau. Mogelijke waarden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Haalt een foutbericht op hoog niveau op.|
|innerError|InnerErrorV2|Nieuwe interne foutindeling, die voldoet aan Cognitive Services API-richtlijnen. Het bevat vereiste eigenschappen: ErrorCode, message en optional properties target, details(sleutelwaardepaar), interne fout (dit kan worden genest).|
|Innerlijke. Foutcode|tekenreeks|Haalt een codefoutreeks op.|
|innerError.message|tekenreeks|Haalt een foutbericht op hoog niveau op.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voorbeeld van een geslaagd antwoord

De volgende informatie wordt geretourneerd als een geslaagd antwoord.

U vindt de taak-id in de antwoordheader van de POST-methode Operation-Location URL-waarde. De laatste parameter van de URL is de taak-id van de bewerking (de tekenreeks na "/operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Voorbeeld van een foutbericht

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

Volg onze quickstart voor meer informatie over het gebruik van Documentvertaling en de clientbibliotheek.

> [!div class="nextstepaction"]
> [Aan de slag met documentvertaling](../get-started-with-document-translation.md)
