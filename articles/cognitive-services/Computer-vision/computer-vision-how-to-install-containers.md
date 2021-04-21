---
title: Read OCR Docker-containers installeren vanuit Computer Vision
titleSuffix: Azure Cognitive Services
description: Gebruik de Read OCR Docker-containers Computer Vision on-premises tekst te extraheren uit afbeeldingen en documenten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: aahi
ms.custom: seodec18, cog-serv-seo-aug-2020
keywords: on-premises, OCR, Docker, container
ms.openlocfilehash: dead48d7d449d1d403359c518eb842b32a54c634
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779089"
---
# <a name="install-read-ocr-docker-containers"></a>Read OCR Docker-containers installeren

[!INCLUDE [container hosting on the Microsoft Container Registry](../containers/includes/gated-container-hosting.md)]

Met containers kunt u de Computer Vision-API's uitvoeren in uw eigen omgeving. Containers zijn ideaal voor specifieke vereisten voor beveiliging en gegevensbeheer. In dit artikel leert u hoe u uw containers kunt downloaden, installeren Computer Vision uitvoeren.

Met *de* Read OCR-container kunt u gedrukte en handgeschreven tekst extraheren uit afbeeldingen en documenten met ondersteuning voor JPEG-, PNG-, BMP-, PDF- en TIFF-bestandsindelingen. Zie de handleiding voor de [Read-API voor meer informatie.](Vision-API-How-to-Topics/call-read-api.md)

## <a name="read-32-container"></a>Read 3.2-container

De Read 3.2 OCR-container biedt:
* Nieuwe modellen voor verbeterde nauwkeurigheid.
* Ondersteuning voor meerdere talen binnen hetzelfde document.
* Ondersteuning voor in totaal 73 talen. Zie de volledige lijst met [door OCR ondersteunde talen.](./language-support.md#optical-character-recognition-ocr)
* Eén bewerking voor zowel documenten als afbeeldingen.
* Ondersteuning voor grotere documenten en afbeeldingen.
* Betrouwbaarheidsscores.
* Ondersteuning voor documenten met zowel afdruk- als handgeschreven tekst.
* Mogelijkheid om tekst te extraheren uit alleen geselecteerde pagina('s) in een document.
* Kies de uitvoerorder voor tekstregel van standaard naar een natuurlijkere leesorde voor alleen Latijnse talen.
* Tekstregelclassificatie als handgeschreven stijl of niet alleen voor Latijnse talen.

Als u momenteel Read 2.0-containers gebruikt, bekijkt u de [migratiehandleiding](read-container-migration-guide.md) voor meer informatie over wijzigingen in de nieuwe versies.

## <a name="prerequisites"></a>Vereisten

U moet voldoen aan de volgende vereisten voordat u de containers gebruikt:

|Vereist|Doel|
|--|--|
|Docker-engine| De Docker-engine moet zijn geïnstalleerd op een [hostcomputer.](#the-host-computer) Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet worden geconfigureerd zodat de containers verbinding kunnen maken met en factureringsgegevens naar Azure kunnen verzenden. <br><br> **In Windows** moet Docker ook worden geconfigureerd om Linux-containers te ondersteunen.<br><br>|
|Bekendheid met Docker | U moet basiskennis hebben van Docker-concepten, zoals registers, opslagplaatsen, containers en container-afbeeldingen, evenals kennis van `docker` basisopdrachten.| 
|Computer Vision resource |Als u de container wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure **Computer Vision-resource** en de bijbehorende API-sleutel de eindpunt-URI. Beide waarden zijn beschikbaar op de pagina's Overzicht en Sleutels voor de resource en zijn vereist om de container te starten.<br><br>**{API_KEY}**: Een van de twee beschikbare resourcesleutels op de **pagina** Sleutels<br><br>**{ENDPOINT_URI}**: Het eindpunt zoals opgegeven op de **pagina** Overzicht|

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="request-approval-to-run-the-container"></a>Goedkeuring aanvragen om de container uit te voeren

Vul het aanvraagformulier in en [verzend het om](https://aka.ms/csgate) goedkeuring aan te vragen voor het uitvoeren van de container. 

[!INCLUDE [Request access to run the container](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Ondersteuning voor Geavanceerde vectorextensie

De **hostcomputer** is de computer met de Docker-container. De host *moet AVX2* [(Advanced Vector Extensions)](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) ondersteunen. U kunt controleren op AVX2-ondersteuning op Linux-hosts met de volgende opdracht:

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```

> [!WARNING]
> De hostcomputer is *vereist voor* de ondersteuning van AVX2. De container *werkt niet* correct zonder AVX2-ondersteuning.

### <a name="container-requirements-and-recommendations"></a>Vereisten en aanbevelingen voor containers

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>De containerafbeelding met `docker pull`

Container-afbeeldingen voor Lezen zijn beschikbaar.

| Container | Container Registry/ opslagplaats / naam van de afbeelding |
|-----------|------------|
| Read 2.0-preview | `mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview` |
| Read 3.2 | `mcr.microsoft.com/azure-cognitive-services/vision/read:3.2` |

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een containerafbeelding te downloaden.

### <a name="docker-pull-for-the-read-ocr-container"></a>Docker pull voor de Read OCR-container

# <a name="version-32"></a>[Versie 3.2](#tab/version-3-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
```

# <a name="version-20-preview"></a>[Versie 2.0-preview](#tab/version-2)

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview
```

---

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>De container gebruiken

Zodra de container zich op de [hostcomputer heeft,](#the-host-computer)gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run)met de vereiste factureringsinstellingen. Meer [voorbeelden](computer-vision-resource-container-config.md) van de `docker run` opdracht zijn beschikbaar. 
1. [Een query uitvoeren op het voorspellings-eindpunt van de container.](#query-the-containers-prediction-endpoint) 

## <a name="run-the-container-with-docker-run"></a>De container uitvoeren met `docker run`

Gebruik de [opdracht docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg Het [verzamelen van vereiste parameters](#gathering-required-parameters) voor meer informatie over het op halen van de waarden en `{ENDPOINT_URI}` `{API_KEY}` .

[Voorbeelden](computer-vision-resource-container-config.md#example-docker-run-commands) van de `docker run` opdracht zijn beschikbaar.

# <a name="version-32"></a>[Versie 3.2](#tab/version-3-2)

```bash
docker run --rm -it -p 5000:5000 --memory 18g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:3.2 \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert de Read OCR-container uit vanuit de containerafbeelding.
* Wijst 8 CPU-kernen en 18 GIGABYTE (GB) geheugen toe.
* Maakt TCP-poort 5000 beschikbaar en wijst een pseudo-TTY toe voor de container.
* De container wordt automatisch verwijderd nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

# <a name="version-20-preview"></a>[Versie 2.0-preview](#tab/version-2)

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
mcr.microsoft.com/azure-cognitive-services/vision/read:2.0-preview \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert de Read OCR-container uit vanuit de containerafbeelding.
* Wijst 8 CPU-kernen en 16 gigabyte (GB) geheugen toe.
* Maakt TCP-poort 5000 beschikbaar en wijst een pseudo-TTY toe voor de container.
* De container wordt automatisch verwijderd nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.

---


Meer [voorbeelden](./computer-vision-resource-container-config.md#example-docker-run-commands) van de `docker run` opdracht zijn beschikbaar. 

> [!IMPORTANT]
> De opties , en moeten worden opgegeven om de container uit te voeren. Anders `Eula` wordt de container niet `Billing` `ApiKey` start.  Zie Facturering voor meer [informatie.](#billing)

Als u een hogere doorvoer nodig hebt (bijvoorbeeld bij het verwerken van bestanden met meerdere pagina's), kunt u overwegen om meerdere containers op een [Kubernetes-cluster](deploy-computer-vision-on-premises.md)te implementeren met behulp [van Azure Storage](../../storage/common/storage-account-create.md) en [Azure Queue](../../storage/queues/storage-queues-introduction.md).

Als u een Azure Storage voor het opslaan van afbeeldingen voor verwerking, kunt u een connection string [gebruiken](../../storage/common/storage-configure-connection-string.md) bij het aanroepen van de container.

U vindt uw connection string:

1. **Navigeer naar Opslagaccounts** op Azure Portal en zoek uw account.
2. Klik in **de linkernavigatielijst** op Toegangssleutels.
3. Uw connection string bevindt zich onder **Verbindingsreeks**

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Een query uitvoeren op het voorspellingseindpunt van de container

De container bevat op REST gebaseerde eindpunt-API's voor queryvoorspelling. 

# <a name="version-32"></a>[Versie 3.2](#tab/version-3-2)

Gebruik de host, `http://localhost:5000`, voor container-API's. U kunt het Swagger-pad bekijken op: `http://localhost:5000/swagger/vision-v3.2-read/swagger.json` .

# <a name="version-20-preview"></a>[Versie 2.0-preview](#tab/version-2)

Gebruik de host, `http://localhost:5000`, voor container-API's. U kunt het Swagger-pad bekijken op: `http://localhost:5000/swagger/vision-v2.0-preview-read/swagger.json` .

---

### <a name="asynchronous-read"></a>Asynchroon lezen


# <a name="version-32"></a>[Versie 3.2](#tab/version-3-2)

U kunt de bewerkingen `POST /vision/v3.2/read/analyze` en gebruiken om asynchroon een afbeelding te lezen, vergelijkbaar met de manier waarop de Computer Vision-service die bijbehorende `GET /vision/v3.2/read/operations/{operationId}` REST-bewerkingen gebruikt. De asynchrone POST-methode retournatie van een die wordt gebruikt als `operationId` de identifer voor de HTTP GET-aanvraag.


Selecteer in de swagger-gebruikersinterface de `Analyze` om deze uit te vouwen in de browser. Selecteer vervolgens **Try it out** Choose  >  **file**. In dit voorbeeld gebruiken we de volgende afbeelding:

![tabs versus spaties](media/tabs-vs-spaces.png)

Wanneer de asynchrone POST is uitgevoerd, wordt een **HTTP 202-statuscode** retourneert. Als onderdeel van het antwoord is er een `operation-location` header die het resultaat-eindpunt voor de aanvraag bevat.

```http
 content-length: 0
 date: Fri, 04 Sep 2020 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v3.2/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

De `operation-location` is de volledig gekwalificeerde URL en is toegankelijk via een HTTP GET. Hier is het JSON-antwoord van het uitvoeren van de `operation-location` URL uit de voorgaande afbeelding:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2.0",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          },
          {
            "boundingBox": [
              286,
              171,
              415,
              165,
              417,
              197,
              287,
              201
            ],
            "text": "paces",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.746
              }
            },
            "words": [
              {
                "boundingBox": [
                  286,
                  179,
                  404,
                  166,
                  405,
                  198,
                  290,
                  201
                ],
                "text": "paces",
                "confidence": 0.938
              }
            ]
          }
        ]
      }
    ]
  }
}
```

# <a name="version-20-preview"></a>[Versie 2.0-preview](#tab/version-2)

U kunt de bewerkingen `POST /vision/v2.0/read/core/asyncBatchAnalyze` en gebruiken om asynchroon een afbeelding te lezen, vergelijkbaar met de manier waarop de Computer Vision-service die bijbehorende `GET /vision/v2.0/read/operations/{operationId}` REST-bewerkingen gebruikt. Met de asynchrone POST-methode wordt een retourneren die wordt gebruikt als `operationId` de identifer voor de HTTP GET-aanvraag.

Selecteer in de swagger-gebruikersinterface de `asyncBatchAnalyze` om deze uit te vouwen in de browser. Selecteer vervolgens **Try it out** Choose  >  **file**. In dit voorbeeld gebruiken we de volgende afbeelding:

![tabs versus spaties](media/tabs-vs-spaces.png)

Wanneer de asynchrone POST is uitgevoerd, wordt een **HTTP 202-statuscode** retourneert. Als onderdeel van het antwoord is er een `operation-location` header die het resultaat-eindpunt voor de aanvraag bevat.

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

De `operation-location` is de volledig gekwalificeerde URL en is toegankelijk via een HTTP GET. Hier is het JSON-antwoord van het uitvoeren van de `operation-location` URL uit de voorgaande afbeelding:

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [ 56, 39, 317, 50, 313, 134, 53, 123 ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [ 90, 43, 243, 53, 243, 123, 94, 125 ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [ 259, 55, 313, 62, 313, 122, 259, 123 ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [ 221, 148, 417, 146, 417, 206, 227, 218 ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [ 230, 148, 416, 141, 419, 211, 232, 218 ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

---

> [!IMPORTANT]
> Als u meerdere Read OCR-containers achter een load balancer implementeert, bijvoorbeeld onder Docker Compose of Kubernetes, moet u een externe cache hebben. Omdat de verwerkingscontainer en de GET-aanvraagcontainer mogelijk niet hetzelfde zijn, slaat een externe cache de resultaten op en deelt deze over containers. Zie Configure Computer Vision [Docker containers (Docker-containers configureren) voor meer informatie over cache-instellingen.](./computer-vision-resource-container-config.md)

### <a name="synchronous-read"></a>Synchroon lezen

U kunt de volgende bewerking gebruiken om een afbeelding synchroon te lezen. 

# <a name="version-32"></a>[Versie 3.2](#tab/version-3-2)

`POST /vision/v3.2/read/syncAnalyze` 

# <a name="version-20-preview"></a>[Versie 2.0-preview](#tab/version-2)

`POST /vision/v2.0/read/core/Analyze`

---

Wanneer de afbeelding in zijn geheel wordt gelezen, retourneert de API vervolgens alleen een JSON-antwoord. De enige uitzondering hierop is als er een fout optreedt. Wanneer er een fout optreedt, wordt de volgende JSON geretourneerd:

```json
{
    "status": "Failed"
}
```

Het JSON-antwoordobject heeft dezelfde objectgrafiek als de asynchrone versie. Als u een JavaScript-gebruiker bent en de veiligheid van het type wilt, kunt u typeScript gebruiken om het JSON-antwoord te casten.

Zie hier de <a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">TypeScript-sandbox</a> voor een voorbeeld van een gebruiksvoorbeeld en selecteer **Uitvoeren** om het gebruiksgemak te visualiseren.

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container [](./computer-vision-resource-container-config.md#mount-settings) hebt uitgevoerd met een uitvoer mount en logboekregistratie ingeschakeld, genereert de container logboekbestanden die handig zijn voor het oplossen van problemen die zich tijdens het starten of uitvoeren van de container voor doen.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De Cognitive Services verzenden factureringsgegevens naar Azure met behulp van de bijbehorende resource in uw Azure-account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie Containers configureren voor meer informatie [over deze opties.](./computer-vision-resource-container-config.md) 

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstromen geleerd voor het downloaden, installeren en uitvoeren van Computer Vision containers. Samenvatting:

* Computer Vision biedt een Linux-container voor Docker, die Lezen inkapselen.
* Voor de leescontainer-afbeelding is een toepassing vereist om deze uit te voeren. 
* Container-afbeeldingen worden uitgevoerd in Docker.
* U kunt de REST API of SDK gebruiken om bewerkingen in OCR-leescontainers aan te roepen door de host-URI van de container op te geven.
* U moet factureringsgegevens opgeven bij het instantiëren van een container.

> [!IMPORTANT]
> Cognitive Services containers hebben geen licentie om te worden uitgevoerd zonder dat ze zijn verbonden met Azure voor het meten. Klanten moeten de containers in staat stellen om te allen tijde factureringsgegevens te communiceren met de meetservice. Cognitive Services containers verzenden geen klantgegevens (bijvoorbeeld de afbeelding of tekst die wordt geanalyseerd) naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Controleer [Containers configureren](computer-vision-resource-container-config.md) voor configuratie-instellingen
* Bekijk het [OCR-overzicht](overview-ocr.md) voor meer informatie over het herkennen van gedrukte en handgeschreven tekst
* Raadpleeg de [Read-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) voor meer informatie over de methoden die door de container worden ondersteund.
* Raadpleeg [Veelgestelde vragen (FAQ)](FAQ.md) voor het oplossen van problemen met betrekking tot de functionaliteit van Computer Vision.
* Gebruik meer [Cognitive Services-containers](../cognitive-services-container-support.md)
