---
title: Container voor de formulier herkenning installeren en uitvoeren
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de Azure Cognitive Services Form Recognizer-container kunt gebruiken om formulier-en tabel gegevens te parseren.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 1be8afb58b22435f4f43b2d6884332a38b7f1e11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467473"
---
# <a name="install-and-run-form-recognizer-containers-retiring"></a>Containers voor formulier herkenning installeren en uitvoeren (buiten gebruik stellen)

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Azure Form Recognizer past machine learning technologie toe om sleutel-waardeparen en tabellen uit formulieren te identificeren en uit te pakken. Er worden waarden en tabel items gekoppeld aan de sleutel-waardeparen en vervolgens gestructureerde gegevens uitgevoerd die de relaties in het oorspronkelijke bestand bevatten. 

Als u de complexiteit wilt reduceren en eenvoudig een aangepast model wilt integreren in uw werk stroom automatiserings proces of een andere toepassing, kunt u het model aanroepen met behulp van een eenvoudige REST API. Er zijn slechts vijf formulier documenten nodig, zodat u snel en nauw keurig resultaten kunt ophalen en aanpassen aan uw specifieke inhoud. Er is geen zware hand matige interventie of uitgebreide expertise van gegevens wetenschap nood zakelijk. Er zijn geen gegevens labeling of gegevens aantekening nodig.

| Functie | Functies |
|----------|----------|
| Form Recognizer | <li>Verwerkt PDF-, PNG-en JPG-bestanden<li>Aangepaste modellen met mini maal vijf formulieren van dezelfde indeling <li>Haalt sleutel-waardeparen en tabel gegevens op <li>Maakt gebruik van de functie Azure Cognitive Services Computer Vision-API Tekst herkennen voor het detecteren en extra heren van afgedrukte tekst uit afbeeldingen in formulieren<li>Vereist geen aantekening of labeling |

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voordat u de formulieren Recognizer-containers gebruikt, moet aan de volgende vereisten worden voldaan:

| Vereist | Doel |
|----------|---------|
| Docker-engine | De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet worden geconfigureerd zodat de containers verbinding kunnen maken met en facturerings gegevens kunnen verzenden naar Azure. <br><br> In Windows moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br> |
| Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker` opdrachten. |
| De Azure CLI | Installeer de [Azure cli](/cli/azure/install-azure-cli) op uw host. |
| Computer Vision-API resource | Als u gescande documenten en installatie kopieën wilt verwerken, hebt u een Computer Vision resource nodig. U kunt de functie Tekst herkennen openen als een Azure-resource (de REST API of SDK) of een *cognitieve-Services-tekst* [container](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull). De gebruikelijke facturerings kosten zijn van toepassing. <br><br>Geef zowel de API-sleutel als de eind punten voor uw Computer Vision resource (Azure-Cloud of Cognitive Services-container) door. Gebruik deze API-sleutel en het eind punt als **{COMPUTER_VISION_API_KEY}** en **{COMPUTER_VISION_ENDPOINT_URI}**.<br><br> Als u de *cognitieve-Services-Recognize-Text* -container gebruikt, zorgt u ervoor dat:<br><br>Uw Computer Vision sleutel voor de indeling van de formulier Recognizer is de sleutel die is opgegeven in de Computer Vision `docker run` -opdracht voor de ' *cognitieve-Services-' recognize-tekst* container.<br>Uw facturerings eindpunt is het eind punt van de container (bijvoorbeeld `http://localhost:5000` ). Als u zowel de Computer Vision container als de formulier Recognizer-container samen op dezelfde host gebruikt, kunnen ze niet beide worden gestart met de standaard poort *5000*. |
| Resource voor formulier herkenning | Als u deze containers wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure **Form Recognizer** -resource om de bijbehorende API-sleutel en eind punt-URI op te halen. Beide waarden zijn beschikbaar op de Azure Portal **formulier Recognizer** Overview en de pagina sleutels en beide waarden zijn vereist om de container te starten.<br><br>**{FORM_RECOGNIZER_API_KEY}**: een van de twee beschik bare bron sleutels op de pagina sleutels<br><br>**{FORM_RECOGNIZER_ENDPOINT_URI}**: het eind punt op de pagina overzicht |

> [!NOTE]
> De naam van de Computer Vision resource moet één woord zijn, zonder een koppel teken `-` of andere speciale tekens. Deze beperking is van kracht om de compatibiliteit van formulieren en Tekst herkennen container te garanderen.

## <a name="gathering-required-parameters"></a>Vereiste parameters verzamelen

Er zijn drie primaire para meters voor alle vereiste containers van Cognitive Services. De gebruiksrecht overeenkomst (EULA) moet aanwezig zijn met een waarde van `accept` . Bovendien zijn er een eindpunt-URL en API-sleutel nodig.

### <a name="endpoint-uri-computer_vision_endpoint_uri-and-form_recognizer_endpoint_uri"></a>Eind punt-URI `{COMPUTER_VISION_ENDPOINT_URI}` en `{FORM_RECOGNIZER_ENDPOINT_URI}`

De URI-waarde van het **eind punt** is beschikbaar op de pagina *overzicht* van Azure portal van de bijbehorende cognitieve service resource. Ga naar de *overzichts* pagina, beweeg de muis aanwijzer over het eind punt en er `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> wordt een pictogram weer gegeven. Kopieer en gebruik waar nodig.

![De URI van het eind punt verzamelen voor later gebruik](../containers/media/overview-endpoint-uri.png)

### <a name="keys-computer_vision_api_key-and-form_recognizer_api_key"></a>Sleutels `{COMPUTER_VISION_API_KEY}` en `{FORM_RECOGNIZER_API_KEY}`

Deze sleutel wordt gebruikt om de container te starten en is beschikbaar op de pagina sleutels van de Azure Portal van de bijbehorende cognitieve service resource. Ga naar de pagina *sleutels* en klik op het `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> pictogram.

![Een van de twee sleutels ophalen voor later gebruik](../containers/media/keys-copy-api-key.png)

> [!IMPORTANT]
> Deze abonnementssleutels worden gebruikt om toegang te krijgen tot de API van Cognitive Services. Deel uw sleutels niet. Sla ze veilig op, bijvoorbeeld met behulp van Azure Key Vault. We raden u ook aan om deze sleutels regelmatig opnieuw te genereren. Er is slechts één sleutel nodig om een API-aanroep te maken. Wanneer u de eerste sleutel opnieuw genereert, kunt u de tweede sleutel gebruiken voor verdere toegang tot de service.

## <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Container vereisten en aanbevelingen

De minimale en aanbevolen CPU-kernen en het geheugen die moeten worden toegewezen voor elke formulier Recognizer-container, worden beschreven in de volgende tabel:

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Form Recognizer | 2 kernen, 4 GB geheugen | 4 kernen, 8 GB geheugen |
| Tekst herkennen | 1 kern geheugen van 8 GB | 2 kernen, 8 GB geheugen |

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van de `docker run` opdracht.

> [!Note]
> De minimale en aanbevolen waarden zijn gebaseerd op docker-limieten en *niet* op de hostcomputer bronnen.

U hebt de formulieren Recognizer en Tekst herkennen containers nodig, maar u ziet dat de **tekst herkennen** -container [uitvoerig in dit artikel wordt beschreven.](../Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>De container gebruiken

Nadat de container zich op de [hostcomputer](#the-host-computer)bevindt, gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-by-using-the-docker-run-command)met de vereiste facturerings instellingen. Er zijn meer [voor beelden](form-recognizer-container-configuration.md#example-docker-run-commands) van de `docker run` opdracht beschikbaar.
1. [Zoek het Voorspellings eindpunt van de container](#query-the-containers-prediction-endpoint)op.

## <a name="run-the-container-by-using-the-docker-run-command"></a>Voer de container uit met behulp van de opdracht docker run

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg de [vereiste para meters verzamelen](#gathering-required-parameters) voor meer informatie over het ophalen van de `{COMPUTER_VISION_ENDPOINT_URI}` `{COMPUTER_VISION_API_KEY}` waarden, `{FORM_RECOGNIZER_ENDPOINT_URI}` en `{FORM_RECOGNIZER_API_KEY}` .

[Voor beelden](form-recognizer-container-configuration.md#example-docker-run-commands) van de `docker run` opdracht zijn beschikbaar.

### <a name="form-recognizer"></a>Form Recognizer

> [!NOTE]
> De mappen die `--mount` in deze voor beelden worden gebruikt, zijn Windows directory-paden. Als u Linux of macOS gebruikt, wijzigt u de para meter voor uw omgeving. 

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Met deze opdracht gebeurt het volgende:

* Voert een formulier Recognizer-container uit vanuit de container installatie kopie.
* Wijst twee CPU-kernen en 8 GB aan geheugen toe.
* Beschrijft TCP-poort 5000 en wijst een pseudo-TTY voor de container toe.
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.
* Hiermee koppelt u een/input en een/output-volume aan de container.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Afzonderlijke containers als afzonderlijke docker-opdrachten uitvoeren

Gebruik de volgende twee voor beelden van docker CLI-opdrachten voor de combi natie van formulier herkenning en tekst herkenning die lokaal wordt gehost op dezelfde host:

Voer de eerste container uit op poort 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={FORM_RECOGNIZER_ENDPOINT_URI} \
ApiKey={FORM_RECOGNIZER_API_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Voer de tweede container uit op poort 5001.

```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Elke volgende container moet zich op een andere poort bevinden. 

### <a name="run-separate-containers-with-docker-compose"></a>Afzonderlijke containers uitvoeren met docker opstellen

Zie het volgende voor beeld van een docker YAML-bestand voor de combi natie van formulier herkenning en tekst herkenning die lokaal wordt gehost op dezelfde host. De tekst herkenning `{COMPUTER_VISION_API_KEY}` moet hetzelfde zijn voor de `formrecognizer` `ocr` containers en. De `{COMPUTER_VISION_ENDPOINT_URI}` wordt alleen gebruikt in de `ocr` container, omdat de `formrecognizer` container de `ocr` naam en de poort gebruikt. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: "{COMPUTER_VISION_API_KEY}"

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{FORM_RECOGNIZER_ENDPOINT_URI}"
      apikey: "{FORM_RECOGNIZER_API_KEY}"
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"
```

> [!IMPORTANT]
> De `Eula` , `Billing` , en `ApiKey` , en de `FormRecognizer:ComputerVisionApiKey` `FormRecognizer:ComputerVisionEndpointUri` Opties en moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart. Zie [facturering](#billing)voor meer informatie.

## <a name="query-the-containers-prediction-endpoint"></a>Een query uitvoeren op het voorspellingseindpunt van de container

|Container|Eindpunt|
|--|--|
|formulier-Recognizer|http://localhost:5000

### <a name="form-recognizer"></a>Form Recognizer

De container biedt op websockets gebaseerde query-eindpunt Api's, die u kunt openen via de [SDK-documentatie voor formulieren Recognizer Services](./index.yml).

De formulier Recognizer SDK maakt standaard gebruik van de onlineservices. Als u de container wilt gebruiken, moet u de initialisatie methode wijzigen. Zie de onderstaande voorbeelden.

#### <a name="for-c"></a>Voor C #

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep:

```csharp
var config =
    FormRecognizerConfig.FromSubscription(
        "YourSubscriptionKey",
        "YourServiceRegion");
```
voor deze aanroep, waarbij gebruik wordt gemaakt van het container eindpunt:

```csharp
var config =
    FormRecognizerConfig.FromEndpoint(
        "ws://localhost:5000/formrecognizer/v1.0-preview/custom",
        "YourSubscriptionKey");
```

#### <a name="for-python"></a>Voor python

Wijzigen van het gebruik van deze Azure-Cloud initialisatie aanroep:

```python
formrecognizer_config =
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key, region=service_region)
```

voor deze aanroep, waarbij gebruik wordt gemaakt van het container eindpunt:

```python
formrecognizer_config = 
    formrecognizersdk.FormRecognizerConfig(
        subscription=formrecognizer_key,
        endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Form Recognizer

De container bevat REST-endpoint Api's, die u op de referentie pagina [Form Recognizer API] kunt vinden https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) .


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een uitvoer [koppeling](form-recognizer-container-configuration.md#mount-settings) en logboek registratie ingeschakeld, genereert de container logboek bestanden die handig zijn om problemen op te lossen die optreden tijdens het starten of uitvoeren van de container.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De containers voor formulier herkenning verzenden facturerings gegevens naar Azure met behulp van een resource voor _formulier herkenning_ in uw Azure-account.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [containers configureren](form-recognizer-container-configuration.md)voor meer informatie over deze opties.

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd voor het downloaden, installeren en uitvoeren van containers voor formulier herkenning. Samenvatting:

* Formulier herkenning biedt één Linux-container voor docker.
* Container installatie kopieën worden gedownload uit het persoonlijke container register in Azure.
* Container installatie kopieën worden uitgevoerd in docker.
* U kunt de REST API of de REST-SDK gebruiken voor het aanroepen van bewerkingen in de container voor de formulier herkenning door de URI van de host op te geven van de container.
* U moet de facturerings gegevens opgeven bij het instantiëren van een container.

> [!IMPORTANT]
>  Cognitive Services-containers mogen niet worden uitgevoerd zonder te zijn verbonden met Azure voor meting. Klanten moeten de containers in staat stellen om de facturerings gegevens te allen tijde met de meet service te communiceren. Cognitive Services containers verzenden geen klant gegevens (bijvoorbeeld de afbeelding of tekst die wordt geanalyseerd) naar micro soft.

## <a name="next-steps"></a>Volgende stappen

* Bekijk [containers configureren](form-recognizer-container-configuration.md) voor configuratie-instellingen.
* Meer [Cognitive Services containers](../cognitive-services-container-support.md)gebruiken.