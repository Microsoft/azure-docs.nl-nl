---
title: On-premises Azure Cognitive Services containers gebruiken
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van Docker-containers Cognitive Services on-premises.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18, cog-serv-seo-aug-2020
ms.service: cognitive-services
ms.topic: article
ms.date: 04/16/2021
ms.author: aahi
keywords: on-premises, Docker, container, Kubernetes
ms.openlocfilehash: c40e91d81df448021be74af768bc9d5952b263dd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588221"
---
# <a name="azure-cognitive-services-containers"></a>Azure Cognitive Services-containers

Azure Cognitive Services biedt verschillende [Docker-containers,](https://www.docker.com/what-container) zodat u dezelfde API's kunt gebruiken die on-premises beschikbaar zijn in Azure. Het gebruik van deze containers biedt u de flexibiliteit om uw gegevens Cognitive Services te brengen om nalevings-, beveiligings- of andere operationele redenen. Ondersteuning voor containers is momenteel beschikbaar voor een subset van Azure Cognitive Services.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

Containerisatie is een benadering van softwaredistributie waarbij een toepassing of service, met inbegrip van de afhankelijkheden & configuratie, samen wordt verpakt als een containerafbeelding. Met weinig of geen wijzigingen kan een container-afbeelding worden geïmplementeerd op een containerhost. Containers worden van elkaar en het onderliggende besturingssysteem geïsoleerd, met een kleinere footprint dan een virtuele machine. Containers kunnen worden gemaakt vanuit containerafbeeldingen voor kortetermijntaken en worden verwijderd wanneer ze niet meer nodig zijn.

## <a name="features-and-benefits"></a>Functies en -voordelen

- **Onveranderbare infrastructuur:** DevOps-teams kunnen gebruikmaken van een consistente en betrouwbare set bekende systeemparameters, terwijl ze zich kunnen aanpassen aan veranderingen. Containers bieden de flexibiliteit om te draaien binnen een voorspelbaar ecosysteem en configuratiedrift te voorkomen.
- **Controle over gegevens:** kies waar uw gegevens worden verwerkt door Cognitive Services. Dit kan essentieel zijn als u geen gegevens naar de cloud kunt verzenden, maar wel toegang nodig hebt tot Cognitive Services-API's. Ondersteuning voor consistentie in hybride omgevingen: voor gegevens, beheer, identiteit en beveiliging.
- **Controle over modelupdates:** flexibiliteit bij het versiebeheer en het bijwerken van modellen die in hun oplossingen zijn geïmplementeerd.
- **Draagbare architectuur:** hiermee kunt u een draagbare toepassingsarchitectuur maken die kan worden geïmplementeerd in Azure, on-premises en aan de rand. Containers kunnen rechtstreeks worden geïmplementeerd [in Azure Kubernetes Service](../aks/index.yml), [Azure Container Instances](../container-instances/index.yml)of op een [Kubernetes-cluster](https://kubernetes.io/) dat is geïmplementeerd [Azure Stack](/azure-stack/operator). Zie [Kubernetes implementeren naar Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)voor meer Azure Stack.
- **Hoge doorvoer/lage latentie:** bied klanten de mogelijkheid om te schalen voor hoge doorvoer en lage latentievereisten door Cognitive Services in staat te stellen om fysiek dicht bij hun toepassingslogica en -gegevens te worden uitgevoerd. Containers hebben geen limiet voor transacties per seconde (TPS) en kunnen worden gemaakt om omhoog en uit te schalen om de vraag af te handelen als u de benodigde hardwarebronnen verstrekt.
- **Schaalbaarheid:** met de steeds groeiende populariteit van containerisatie- en container orchestration-software, zoals Kubernetes; schaalbaarheid staat in de voorgrond van technologische ontwikkelingen. Door gebruik te maken van een schaalbare clusterbouw, is de ontwikkeling van toepassingen geschikt voor hoge beschikbaarheid.

## <a name="containers-in-azure-cognitive-services"></a>Containers in Azure Cognitive Services

Azure Cognitive Services containers bieden de volgende set Docker-containers, die elk een subset van functionaliteit van services in Azure Cognitive Services. U vindt instructies en afbeeldingslocaties in de onderstaande tabellen. Er is ook een [lijst met containerafbeeldingen](containers/container-image-tags.md) beschikbaar.

### <a name="decision-containers"></a>Beslissingscontainers

| Service |  Container | Beschrijving | Beschikbaarheid |
|--|--|--|--|
| [Anomaliedetectie][ad-containers] | **Anomaly Detector** ([afbeelding](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | Met de Anomaly Detector-API kunt u anomalieën in uw tijdreeksgegevens controleren en detecteren met behulp van machine learning. | Algemeen beschikbaar |

### <a name="language-containers"></a>Taalcontainers

| Service |  Container | Beschrijving | Beschikbaarheid |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([afbeelding](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Laadt een getraind of gepubliceerd Language Understanding-model, ook wel een LUIS-app genoemd, in een Docker-container en biedt toegang tot de queryvoorspellingen van de API-eindpunten van de container. U kunt querylogboeken van de container verzamelen en deze weer uploaden naar de [LUIS-portal](https://www.luis.ai) om de nauwkeurigheid van de voorspelling van de app te verbeteren. | Algemeen beschikbaar |
| [Tekstanalyse][ta-containers-keyphrase] | **Sleuteltermextractie** ([afbeelding](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extraheert sleuteltermen om de belangrijkste punten te identificeren. Bijvoorbeeld, voor de invoertekst 'het eten was heerlijk en de bediening fantastisch' retourneert de API de belangrijkste gespreksonderwerpen: 'eten' en 'bediening fantastisch'. | Preview |
| [Tekstanalyse][ta-containers-language] |  **Tekst Taaldetectie** ([afbeelding](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | Detecteert voor maximaal 120 talen in welke taal de invoertekst is geschreven en rapporteert één taalcode voor elk document dat bij de aanvraag is ingediend. De taalcode is gekoppeld aan een score die de sterkte van de score aangeeft. | Algemeen beschikbaar |
| [Tekstanalyse][ta-containers-sentiment] | **Sentimentanalyse v3** ([afbeelding](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analyseert onbewerkte tekst op aanwijzingen over positief of negatief sentiment. Deze versie van sentimentanalyse retourneert sentimentlabels (bijvoorbeeld *positief* of *negatief)* voor elk document en elke zin in het document. |  Algemeen beschikbaar |
| [Tekstanalyse][ta-containers-health] |  **Text Analytics voor status** | Extraheren en labelen van medische gegevens uit ongestructureerde klinische tekst. | Gated preview. [Vraag toegang aan.][request-access] |

### <a name="speech-containers"></a>Spraakcontainers

> [!NOTE]
> Als u Speech-containers wilt gebruiken, moet u een [onlineaanvraagformulier invullen.](https://aka.ms/csgate)

| Service |  Container | Beschrijving | Beschikbaarheid |
|--|--|--|
| [Speech Service-API][sp-containers-stt] |  **Spraak-naar-tekst** ([afbeelding](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transcribeert continue realtime spraak naar tekst. | Algemeen beschikbaar |
| [Speech Service-API][sp-containers-cstt] | **Aangepaste spraak-naar-tekst** ([afbeelding](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Transcribert continue realtime spraak naar tekst met behulp van een aangepast model. | Algemeen beschikbaar |
| [Speech Service-API][sp-containers-tts] | **Tekst-naar-spraak** ([afbeelding](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Converteert tekst naar natuurlijk klinkende spraak. | Algemeen beschikbaar |
| [Speech Service-API][sp-containers-ctts] | **Aangepaste tekst-naar-spraak** ([afbeelding](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Converteert tekst naar natuurlijk klinkende spraak met behulp van een aangepast model. | Gated preview |
| [Speech Service-API][sp-containers-ntts] | **Neurale tekst naar spraak** ([afbeelding](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | Converteert tekst naar natuurlijk klinkende spraak met behulp van deep neurale netwerktechnologie, waardoor meer natuurlijke gesynthetiseerde spraak mogelijk is. | Algemeen beschikbaar |
| [Speech Service-API][sp-containers-lid] | **Spraaktaaldetectie** ([afbeelding](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Bepaalt de taal van gesproken audio. | Gated preview |

### <a name="vision-containers"></a>Vision-containers

> [!WARNING]
> Op 11 juni 2020 kondigde Microsoft aan dat het geen gezichtsherkenningssoftware verkoopt aan politieafdelingen in de Verenigde Staten totdat er solide wetgeving op basis van mensenrechten in werking is getreden. Daardoor is het mogelijk dan klanten gezichtsherkenningsfuncties of een functionaliteit in Azure Services, zoals Face of Video Indexer, niet kunnen gebruiken als de klant deze services gebruikt of laat gebruiken voor of door een politieafdeling in de Verenigde Staten.

| Service |  Container | Beschrijving | Beschikbaarheid |
|--|--|--|--|
| [Computer Vision][cv-containers] | **OCR lezen** ([afbeelding](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Met de Read OCR-container kunt u gedrukte en handgeschreven tekst extraheren uit afbeeldingen en documenten met ondersteuning voor JPEG-, PNG-, BMP-, PDF- en TIFF-bestandsindelingen. Zie de Read [API-documentatie voor meer informatie.](./computer-vision/overview-ocr.md) | Gated preview. [Vraag toegang aan.][request-access] |
| [Ruimtelijke analyse][spa-containers] | **Ruimtelijke analyse** ([afbeelding](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analyseert realtime streamingvideo om inzicht te krijgen in ruimtelijke relaties tussen mensen, hun verplaatsing en interacties met objecten in fysieke omgevingen. | Gated preview. [Vraag toegang aan.][request-access] |
| [Face][fa-containers] | **Face** | Detecteert menselijke gezichten in afbeeldingen en identificeert kenmerken, waaronder gezichtsherkenningspunten (zoals neus en ogen), geslacht, leeftijd en andere door machines voorspelde gezichtskenmerken. Naast detectie kan Face controleren of twee gezichten in dezelfde afbeelding of verschillende afbeeldingen hetzelfde zijn met behulp van een betrouwbaarheidsscore, of gezichten vergelijken met een database om te zien of er al een vergelijkbaar of identiek gezicht bestaat. Het kan ook vergelijkbare gezichten in groepen organiseren met behulp van gedeelde visuele kenmerken. | Niet beschikbaar |
| [Form Recognizer][fr-containers] | **Form Recognizer** | Form Understanding is van machine learning technologie voor het identificeren en extraheren van sleutel-waardeparen en tabellen uit formulieren. | Niet beschikbaar | 


<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

Daarnaast worden sommige containers ondersteund in de Cognitive Services [voor meerdere serviceresources.](cognitive-services-apis-create-account.md) U kunt één enkele Cognitive Services All-In-One-resource en dezelfde factureringssleutel gebruiken voor ondersteunde services voor de volgende services:

* Computer Vision
* Face
* LUIS
* Text Analytics

## <a name="prerequisites"></a>Vereisten

U moet voldoen aan de volgende vereisten voordat u Azure Cognitive Services containers:

**Docker-engine:** Docker Engine moet lokaal zijn geïnstalleerd. Docker biedt pakketten die de Docker-omgeving configureren op [macOS,](https://docs.docker.com/docker-for-mac/) [Linux](https://docs.docker.com/engine/installation/#supported-platforms)en [Windows.](https://docs.docker.com/docker-for-windows/) In Windows moet Docker worden geconfigureerd om Linux-containers te ondersteunen. Docker-containers kunnen ook rechtstreeks worden geïmplementeerd in [Azure Kubernetes Service](../aks/index.yml) of [Azure Container Instances](../container-instances/index.yml).

Docker moet worden geconfigureerd zodat de containers verbinding kunnen maken met en factureringsgegevens naar Azure kunnen verzenden.

Bekendheid met Microsoft Container Registry en **Docker:** u moet basiskennis hebben van zowel Microsoft Container Registry- als Docker-concepten, zoals registers, opslagplaatsen, containers en containerafbeeldingen, evenals kennis van basisopdrachten. `docker`

Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.

Afzonderlijke containers kunnen ook hun eigen vereisten hebben, waaronder vereisten voor server- en geheugentoewijzing.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Voorbeelden voor ontwikkelaars

Voorbeelden voor ontwikkelaars zijn beschikbaar in onze [GitHub-opslagplaats.](https://github.com/Azure-Samples/cognitive-services-containers-samples)

## <a name="next-steps"></a>Volgende stappen

Meer informatie [over containerrecepten](containers/container-reuse-recipe.md) die u kunt gebruiken met de Cognitive Services.

Installeer en verken de functionaliteit van containers in Azure Cognitive Services:

* [Anomaly Detector containers][ad-containers]
* [Computer Vision containers][cv-containers]
* [Gezichtscontainers][fa-containers]
* [Form Recognizer containers][fr-containers]
* [Language Understanding (LUIS)-containers][lu-containers]
* [Speech Service API-containers][sp-containers]
* [Text Analytics containers][ta-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: face/face-how-to-install-containers.md
[fr-containers]: form-recognizer/form-recognizer-container-howto.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: text-analytics/how-tos/text-analytics-how-to-install-containers.md
[ta-containers-keyphrase]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-containers-language]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-containers-sentiment]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
[ta-containers-health]: text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health
[request-access]: https://aka.ms/csgate
