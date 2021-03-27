---
title: Batch-aanvraag voor document vertaling verzenden
titleSuffix: Azure Cognitive Services
description: Een aanvraag voor document vertalingen verzenden naar de document Vertaal service.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 803a0b9f4496a3d785aa6f22833fee4ae6eca6e0
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105613027"
---
# <a name="document-translation-submit-batch-request"></a>Document vertaling: Batch-aanvraag verzenden

Gebruik deze API om een massale (batch) vertalings aanvraag naar de document Vertaal service te verzenden. Elke aanvraag kan meerdere documenten bevatten en moet voor elk document een bron-en doel container bevatten.

Het voor voegsel en achtervoegsel filter (indien opgegeven) worden gebruikt voor het filteren van mappen. Het voor voegsel wordt toegepast op het subpad na de container naam.

Het Glossaries/Vertaal geheugen kan worden opgenomen in de aanvraag en worden toegepast door de service wanneer het document wordt vertaald.

Als de woorden lijst ongeldig of onbereikbaar is tijdens de omzetting, wordt een fout aangegeven in de document status. Als er al een bestand met dezelfde naam op de doel locatie bestaat, wordt dit overschreven. De targetUrl voor elke doel taal moet uniek zijn.

## <a name="request-url"></a>Aanvraag-URL

Een `POST` aanvraag verzenden naar:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
```

Meer informatie over hoe u uw [aangepaste domein naam](../get-started-with-document-translation.md#find-your-custom-domain-name)kunt vinden.

> [!IMPORTANT]
>
> * **Voor alle API-aanvragen voor de document Vertaal service is een aangepast domein eindpunt vereist**.
> * U kunt het eind punt dat u hebt gevonden op uw Azure Portal bron _sleutels en de eindpunt_ pagina of het globale Translator-eind punt niet gebruiken `api.cognitive.microsofttranslator.com` om HTTP-aanvragen voor document omzetting te maken.

## <a name="request-headers"></a>Aanvraagheaders

Aanvraag headers zijn:

|Kopteksten|Beschrijving|
|--- |--- |
|Ocp-Apim-Subscription-Key|Header vereiste aanvraag|

## <a name="request-body-batch-submission-request"></a>Hoofd tekst van aanvraag: aanvraag voor batch-verzen ding

|Naam|Type|Description|
|--- |--- |--- |
|invoer|BatchRequest[]|Hieronder weer gegeven BatchRequest. De invoer lijst van documenten of mappen met documenten. Media typen: ' application/json ', ' text/JSON ', ' application/* + json '.|

### <a name="inputs"></a>Invoerwaarden

Definitie voor de conversie aanvraag voor de batch invoer.

|Naam|Type|Vereist|Beschrijving|
|--- |--- |--- |--- |
|source|SourceInput[]|Waar|hieronder vermelde invoer. source. De bron van de invoer documenten.|
|Para|StorageInputType[]|Waar|invoer. para hieronder weer gegeven. Opslag type van de bron teken reeks voor invoer documenten.|
|host|TargetInput[]|Waar|invoer. doel hieronder weer gegeven. De locatie van het doel voor de uitvoer.|

**invoer. source**

De bron van de invoer documenten.

|Naam|Type|Vereist|Beschrijving|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Niet waar|DocumentFilter [] hieronder weer gegeven.|
|filter. voor voegsel|tekenreeks|Niet waar|Een hoofdletter gevoelige voorvoegsel teken reeks voor het filteren van documenten in het bronpad voor vertaling. Wanneer u bijvoorbeeld een URI van een Azure Storage-BLOB gebruikt, gebruikt u het voor voegsel om submappen te beperken voor vertaling.|
|filter. suffix|tekenreeks|Niet waar|Een hoofdletter gevoelige achtervoegsel teken reeks voor het filteren van documenten in het bronpad voor vertaling. Dit wordt meestal gebruikt voor bestands extensies.|
|language|tekenreeks|Niet waar|Taal code als er geen is opgegeven, wordt automatische detectie uitgevoerd op het document.|
|sourceUrl|tekenreeks|True|Locatie van de map/container of één bestand met uw documenten.|
|storageSource|StorageSource|Niet waar|Hieronder weer gegeven StorageSource.|
|storageSource. AzureBlob|tekenreeks|Niet waar||

**invoer. para**

Opslag type van de bron teken reeks voor invoer documenten.

|Naam|Type|
|--- |--- |
|file|tekenreeks|
|map|tekenreeks|

**invoer. doel**

Doel voor de voltooide, vertaalde documenten.

|Naam|Type|Vereist|Beschrijving|
|--- |--- |--- |--- |
|category|tekenreeks|Niet waar|Categorie/aangepast systeem voor vertaal aanvraag.|
|glossaries|Woorden lijst []|Niet waar|Onderstaande woorden lijst. Lijst met woorden lijsten.|
|Glossaries. Format|tekenreeks|Niet waar|Formatteer.|
|glossaries.glossaryUrl|tekenreeks|Waar (als u Glossaries gebruikt)|De locatie van de woorden lijst. De bestands extensie wordt gebruikt om de opmaak uit te pakken als de indelings parameter niet wordt opgegeven. Als het taal paar voor de vertaling niet aanwezig is in de verklarende woorden lijst, wordt het niet toegepast.|
|glossaries.storageSource|StorageSource|Niet waar|StorageSource die hierboven worden vermeld.|
|targetUrl|tekenreeks|True|Locatie van de map/container met uw documenten.|
|language|tekenreeks|True|Taal code voor het doel van twee letters. Zie een [lijst met taal codes](../../language-support.md).|
|storageSource|StorageSource []|Niet waar|StorageSource [] die hierboven wordt vermeld.|
|versie|tekenreeks|Niet waar|Versie.|

## <a name="example-request"></a>Voorbeeldaanvraag

Hier volgen enkele voor beelden van batch-aanvragen.

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

**Vertalen van alle documenten in een container die Glossaries Toep assen**

Zorg ervoor dat u een woorden lijst-URL hebt gemaakt & SAS-token voor de specifieke BLOB/document (niet voor de container)

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

Zorg ervoor dat u de mapnaam (hoofdletter gevoelig) hebt opgegeven als voor voegsel in filter – Hoewel het SAS-token nog steeds voor de container is.

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

* Zorg ervoor dat u ' para ' hebt opgegeven: ' bestand '
* Zorg ervoor dat u een bron-URL hebt gemaakt & SAS-token voor de specifieke BLOB/het opgegeven document (niet voor de container)
* Zorg ervoor dat u de doel bestandsnaam hebt opgegeven als onderdeel van de doel-URL, hoewel het SAS-token nog steeds voor de container is.
* Hieronder ziet u een voor beeld van een document dat wordt vertaald in twee doel talen

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

## <a name="response-status-codes"></a>Antwoord status codes

Hier volgen de mogelijke HTTP-status codes die een aanvraag retourneert.

|Statuscode|Description|
|--- |--- |
|202|Afgewezen. De aanvraag is voltooid en de batch-aanvraag wordt gemaakt door de service. De header Operation-Location geeft een status-URL aan met de bewerkings-ID. HeadersOperation-locatie: teken reeks|
|400|Ongeldige aanvraag. Ongeldige aanvraag. Controleer de invoer parameters.|
|401|Gasten. Controleer uw referenties.|
|429|De aanvraag frequentie is te hoog.|
|500|Interne server fout.|
|503|De service is momenteel niet beschikbaar.  Probeert u het later nog eens.|
|Andere status codes|<ul><li>Te veel aanvragen</li><li>Tijdelijke server niet beschikbaar</li></ul>|

## <a name="error-response"></a>Fout bericht

|Naam|Type|Description|
|--- |--- |--- |
|code|tekenreeks|Opsommingen met fout codes op hoog niveau. Mogelijke waarden:<br/><ul><li>InternalServerError</li><li>InvalidArgument</li><li>InvalidRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Niet geautoriseerd</li></ul>|
|message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|
|innerError|InnerErrorV2|De nieuwe interne fout indeling, die voldoet aan Cognitive Services API-richt lijnen. Het bevat de vereiste eigenschappen Error code, Message en optionele eigenschappen target, Details (sleutel waarde-paar), interne fout (dit kan worden genest).|
|wend. Code|tekenreeks|Hiermee wordt de fout teken reeks van code opgehaald.|
|innerError. Message|tekenreeks|Hiermee wordt het fout bericht op hoog niveau opgehaald.|

## <a name="examples"></a>Voorbeelden

### <a name="example-successful-response"></a>Voor beeld geslaagde reactie

De volgende informatie wordt geretourneerd in een geslaagde reactie.

U kunt de taak-ID vinden in de antwoord header van de POST-methode Operation-Location URL-waarde. De laatste para meter van de URL is de taak-ID van de bewerking (de teken reeks na "/Operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Voorbeeld fout bericht

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

Volg onze Snelstartgids voor meer informatie over het gebruik van document vertalingen en de client bibliotheek.

> [!div class="nextstepaction"]
> [Aan de slag met document vertalingen](../get-started-with-document-translation.md)
