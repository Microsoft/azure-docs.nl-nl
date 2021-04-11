---
title: Docker-containers voor de Text Analytics-API installeren en uitvoeren
titleSuffix: Azure Cognitive Services
description: Gebruik de docker-containers voor de Text Analytics-API om natuurlijke taal verwerking uit te voeren, zoals sentiment-analyse, on-premises.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: aahi
keywords: on-premises, docker, container, sentiment analyse, natuurlijke taal verwerking
ms.openlocfilehash: 012e725e31097af5af634a1aba7693048c4c6b3e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277467"
---
# <a name="install-and-run-text-analytics-containers"></a>De Text Analytics-containers installeren en uitvoeren

> [!NOTE]
> * De container voor Sentimentanalyse en taal detectie is nu algemeen beschikbaar. De container voor het uitpakken van sleutel woorden is beschikbaar als een open bare preview-versie.
> * Entiteits koppelingen en NER zijn momenteel niet beschikbaar als container.
> * Voor toegang tot de Text Analytics voor de status container is een [aanvraag formulier](https://aka.ms/csgate)vereist. Op dit moment wordt er geen kosten in rekening gebracht voor het gebruik.
> * De locaties van de container installatie kopie zijn mogelijk onlangs gewijzigd. Lees dit artikel om de bijgewerkte locatie voor deze container te bekijken.

Containers bieden u de mogelijkheid de Text Analytics-API's in uw eigen omgeving uit te voeren en zijn ideaal voor uw specifieke vereisten voor beveiliging en gegevensbeheer. De Text Analytics-containers bieden geavanceerde verwerking van natuurlijke taal via onbewerkte tekst en bevatten drie belang rijke functies: sentiment analyse, extractie van sleutel zinnen en taal detectie. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

> [!IMPORTANT]
> Het gratis account is beperkt tot 5.000 trans acties per maand en alleen de <a href="https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics" target="_blank">prijs categorieën</a> **gratis** en **standaard** zijn geldig voor containers. Zie [gegevens limieten](../concepts/data-limits.md)voor meer informatie over de tarieven voor transactie aanvragen.

## <a name="prerequisites"></a>Vereisten

Als u een van de Text Analytics containers wilt uitvoeren, moet u beschikken over de host-computer en de container omgeving.

## <a name="preparation"></a>Voorbereiding

U moet voldoen aan de volgende vereisten voordat u Text Analytics containers gebruikt:

|Vereist|Doel|
|--|--|
|Docker-engine| De docker-engine moet zijn geïnstalleerd op een [hostcomputer](#the-host-computer). Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet worden geconfigureerd zodat de containers verbinding kunnen maken met en facturerings gegevens kunnen verzenden naar Azure. <br><br> **In Windows** moet docker ook worden geconfigureerd voor de ondersteuning van Linux-containers.<br><br>|
|Vertrouwd met docker | U moet een basis kennis hebben van docker-concepten, zoals registers, opslag plaatsen, containers en container installatie kopieën, en kennis van basis `docker` opdrachten.| 
|Text Analytics resource |Als u de container wilt gebruiken, hebt u het volgende nodig:<br><br>Een Azure [Text Analytics-resource](../../cognitive-services-apis-create-account.md) met de gratis (F0) of Standard (S) [prijs categorie](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/). U moet de bijbehorende API-sleutel en eind punt-URI ophalen door te navigeren naar de **sleutel en de eindpunt** pagina van uw resource in de Azure Portal. <br><br>**{API_KEY}**: een van de twee beschik bare resource sleutels. <br><br>**{ENDPOINT_URI}**: het eind punt voor uw resource. |

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

Als u de Text Analytics gebruikt voor de status container, moet de bevestigingen van de [verantwoordelijke AI](https://docs.microsoft.com/legal/cognitive-services/text-analytics/transparency-note-health)  (Rai) ook aanwezig zijn met een waarde van `accept` .

## <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Container vereisten en aanbevelingen

In de volgende tabel worden de minimale en aanbevolen specificaties voor de Text Analytics containers beschreven. Er zijn ten minste 2 gigabyte (GB) aan geheugen vereist en elke CPU-kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn. De toegestane trans acties per sectie (TPS) worden ook vermeld.

|  | Minimale specificaties van de host | Aanbevolen specificaties voor de host | Minimale TPS | Maximum aantal TPS|
|---|---------|-------------|--|--|
| **Taal detectie, extractie van sleutel woorden**   | 1 Core, 2 GB geheugen | 1 kern geheugen van 4 GB |15 | 30|
| **Sentimentanalyse**   | 1 Core, 2 GB geheugen | 4 kernen, 8 GB geheugen |15 | 30|
| **Text Analytics voor status-1 document/aanvraag**   |  4-core, 10 GB geheugen | 6 kern geheugen van 12 GB |15 | 30|
| **Text Analytics voor status-10 documenten/aanvraag**   |  6 core, 16 GB geheugen | 8 kern geheugen van 20 GB |15 | 30|

De CPU-kern en het geheugen komen overeen met de `--cpus` `--memory` instellingen en, die worden gebruikt als onderdeel van de `docker run` opdracht.

## <a name="get-the-container-image-with-docker-pull"></a>De container installatie kopie ophalen met `docker pull`

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

Container installatie kopieën voor Text Analytics zijn beschikbaar op de micro soft-Container Registry.

# <a name="sentiment-analysis"></a>[Sentimentanalyse ](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Sleuteltermextractie (preview-versie)](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Taaldetectie](#tab/language)

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Text Analytics voor status (preview)](#tab/healthcare)

[!INCLUDE [docker-pull-health-container](../includes/docker-pull-health-container.md)]

***

## <a name="how-to-use-the-container"></a>De container gebruiken

Wanneer de container zich op de [hostcomputer](#the-host-computer)bevindt, gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run)met de vereiste facturerings instellingen.
1. [Zoek het Voorspellings eindpunt van de container](#query-the-containers-prediction-endpoint)op.

## <a name="run-the-container-with-docker-run"></a>Voer de container uit met `docker run`

Gebruik de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/) om de containers uit te voeren. De container blijft actief totdat u deze stopt.

> [!IMPORTANT]
> * De docker-opdrachten in de volgende secties gebruiken de back slash, `\` , als een regel voortzetting teken. Vervang of verwijder dit op basis van de vereisten van uw host-besturings systeem. 
> * De `Eula` `Billing` Opties, en `ApiKey` moeten worden opgegeven om de container uit te voeren. anders wordt de container niet gestart.  Zie [facturering](#billing)voor meer informatie.
> * De sentiment-analyse-en taal detectie containers zijn algemeen beschikbaar. De container voor de extractie van sleutel woorden gebruikt v2 van de API en is in preview.

# <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

# <a name="key-phrase-extraction-preview"></a>[Sleuteltermextractie (preview-versie)](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detection"></a>[Taaldetectie](#tab/language)

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="text-analytics-for-health-preview"></a>[Text Analytics voor status (preview)](#tab/healthcare)

[!INCLUDE [docker-run-health-container](../includes/docker-run-health-container.md)]

***

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Een query uitvoeren op het voorspellingseindpunt van de container

De container bevat op REST gebaseerde eindpunt-API's voor queryvoorspelling.

Gebruik de host, `http://localhost:5000`, voor container-API's.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een uitvoer [koppeling](../text-analytics-resource-container-config.md#mount-settings) en logboek registratie ingeschakeld, genereert de container logboek bestanden die handig zijn om problemen op te lossen die optreden tijdens het starten of uitvoeren van de container.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De Text Analytics-containers verzenden facturerings gegevens naar Azure met behulp van een _Text Analytics_ resource in uw Azure-account. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [containers configureren](../text-analytics-resource-container-config.md)voor meer informatie over deze opties.

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd om Text Analytics containers te downloaden, te installeren en uit te voeren. Samenvatting:

* Text Analytics biedt drie Linux-containers voor docker, met inkapseling van diverse mogelijkheden:
   * *Sentimentanalyse*
   * *Sleuteltermextractie (preview-versie)* 
   * *Taaldetectie*
   * *Text Analytics voor status (preview)*
* Container installatie kopieën worden gedownload uit de micro soft Container Registry (MCR) of de voor beeld-container opslagplaats.
* Container installatie kopieën worden uitgevoerd in docker.
* U kunt de REST API of SDK gebruiken voor het aanroepen van bewerkingen in Text Analytics containers door de URI van de host op te geven van de container.
* U moet factuur gegevens opgeven bij het instantiëren van een container.

> [!IMPORTANT]
> Cognitive Services-containers mogen niet worden uitgevoerd zonder te zijn verbonden met Azure voor meting. Klanten moeten de containers in staat stellen om de facturerings gegevens te allen tijde met de meet service te communiceren. Cognitive Services containers verzenden geen klant gegevens (bijv. tekst die wordt geanalyseerd) naar micro soft.

## <a name="next-steps"></a>Volgende stappen

* Controleer [Containers configureren](../text-analytics-resource-container-config.md) voor configuratie-instellingen
* Raadpleeg Veelgestelde [vragen (FAQ)](../text-analytics-resource-faq.md) om problemen met betrekking tot de functionaliteit op te lossen.
