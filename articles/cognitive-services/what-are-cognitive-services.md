---
title: Wat zijn Azure Cognitive Services?
titleSuffix: Azure Cognitive Services
description: Cognitive Services maakt AI toegankelijk voor elke ontwikkelaar, zonder dat hiervoor ervaring met machine learning en data science nodig is. U hoeft alleen maar een API-aanroep van uw toepassing te maken om de mogelijkheid voor zien (Geavanceerd zoeken en herkennen van afbeeldingen), horen, spreken, zoeken en besluiten aan uw apps toe te voegen.
services: cognitive-services
author: nitinme
manager: nitinme
keywords: Cognitive Services, cognitieve intelligentie, cognitieve oplossingen, AI-services, cognitieve inzichten, cognitieve functies
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 10/22/2020
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 71f8635d1cd96a6436cfc902622bf18bc608a143
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867182"
---
# <a name="what-are-azure-cognitive-services"></a>Wat zijn Azure Cognitive Services?

Azure Cognitive Services zijn cloudgebaseerde services met REST-API's en clientbibliotheek-SDK's die beschikbaar zijn om u te helpen cognitieve intelligentie in uw applicaties in te bouwen. U kunt cognitieve functies aan uw toepassingen toevoegen zonder dat u kennis nodig heeft van kunstmatige intelligentie (AI) of data science. Azure Cognitive Services bestaan uit diverse AI-services waarmee u cognitieve oplossingen kunt bouwen die kunnen zien, horen, spreken, begrijpen en zelfs beslissingen kunnen nemen.

## <a name="categories-of-cognitive-services"></a>Categorieën van Cognitive Services

De catalogus met cognitieve services, die cognitieve intelligentie bieden, is onderverdeeld in vijf hoofdcategorieën:

* Vision
* Speech
* Taal
* Besluit
* Search

In de volgende secties in dit artikel vindt u een lijst met services die deel uitmaken van deze vijf pijlers.

## <a name="vision-apis"></a>Vision-API's

|Servicenaam|Beschrijving van de service|
|:-----------|:------------------|
|[Computer Vision](./computer-vision/index.yml "Computer Vision")|De Computer Vision-service geeft u toegang tot geavanceerde cognitieve algoritmen voor het verwerken van afbeeldingen en het retourneren van informatie. Zie [Computer Vision Snelstartgids](./computer-vision/quickstarts-sdk/client-library.md) om aan de slag te gaan met de service.|
|[Custom Vision Service](./custom-vision-service/index.yml "Custom Vision Service")|Met de Custom Vision Service kunt u uw eigen afbeeldings classificaties bouwen, implementeren en verbeteren. Een afbeeldings classificatie is een AI-service waarmee labels worden toegepast op installatie kopieën, op basis van hun visuele kenmerken. |
|[Face](./face/index.yml "Face")| De Face-service biedt toegang tot geavanceerde gezichtsalgoritmen, waardoor detectie en herkenning van gezichtskenmerken mogelijk worden. Zie [gezichts Snelstartgids](./face/quickstarts/client-libraries.md) om aan de slag te gaan met de service.|
|[Form Recognizer](./form-recognizer/index.yml "Form Recognizer")|Form Recognizer identificeert en extraheert sleutel-waardeparen en tabelgegevens uit formulierdocumenten. Vervolgens voert het gestructureerde gegevens uit, inclusief de relaties in het oorspronkelijke bestand. Zie [Quick start voor formulier herkenning](./form-recognizer/quickstarts/client-library.md) om aan de slag te gaan.|
|[Video Indexer](../media-services/video-indexer/video-indexer-overview.md "Video Indexer")|Extraheer inzichten uit uw video met Video Indexer. Zie [video indexer Snelstartgids](/media-services/video-indexer/video-indexer-get-started.md) om aan de slag te gaan.|

## <a name="speech-apis"></a>Speech-API's

|Servicenaam|Beschrijving van de service|
|:-----------|:------------------|
|[Speech Service](./speech-service/index.yml "Speech Service")|Speech-service voegt spraakfuncties toe aan toepassingen. De spraakservice bevat verschillende mogelijkheden zoals spraak-naar-tekst, tekst-naar-spraak, spraakomzetting en nog veel meer.|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>Taal-API's

|Servicenaam|Beschrijving van de service|
|:-----------|:------------------|
|[Language Understanding (LUIS)](./luis/index.yml "Taal begrijpen")|Language Understanding (LUIS) is een cloudgebaseerde AI met gespreksfuncties die aangepaste machine-learningintelligentie toepast op de natuurlijke spreektaal van een gebruiker in tekstvorm om daar de algemene betekenis van te voorspellen en relevante detailinformatie uit te destilleren. [Zie Luis Quick](./luis/get-started-portal-build-app.md) start om aan de slag te gaan met de service.|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|Met QnA Maker kunt u een vraag-en-antwoordservice maken op basis van uw semi-gestructureerde inhoud. [Zie QnA Maker Snelstartgids](./qnamaker/quickstarts/create-publish-knowledge-base.md) om aan de slag te gaan met de service.|
|[Tekstanalyse](./text-analytics/index.yml "Tekstanalyse")| Text Analytics biedt natuurlijke taalverwerking via onbewerkte tekst voor sentimentanalyse, sleuteltermextractie en taaldetectie. Zie [Text Analytics Snelstartgids](./text-analytics/quickstarts/client-libraries-rest-api.md) om aan de slag te gaan met de service.|
|[Translator](./translator/index.yml "Vertaler")|Translator biedt bijna realtime machinevertaling van teksten.|
| [Immersive Reader](./immersive-reader/index.yml "Immersive Reader") | De insluitende lezer voegt mogelijkheden voor het lezen en begrijpen van schermen toe aan uw toepassingen. Zie [Snelstartgids voor insluitende lezer](./immersive-reader/quickstarts/client-libraries.md) om aan de slag te gaan met de service. |

## <a name="decision-apis"></a>Beslissings-API's

|Servicenaam|Beschrijving van de service|
|:-----------|:------------------|
|[Anomaliedetectie](./anomaly-detector/index.yml "Anomaly Detector") |Met anomaliedetectie kunt u afwijkingen in uw tijdreeksgegevens controleren en detecteren. Zie [Quick start voor anomalie detectie](./anomaly-detector/quickstarts/client-libraries.md) om aan de slag te gaan met de service|
|[Content Moderator](./content-moderator/overview.md "Content Moderator")|Content Moderator controleert mogelijk aanstootgevende, ongewenste en riskant inhoud. Zie [Content moderator Snelstartgids](./content-moderator/client-libraries.md) om aan de slag te gaan met de service.|
|[Metrics Advisor](./metrics-advisor/index.yml) (preview) | Metrics Advisor bevat functies als aangepaste anomaliedetectie voor uiteenlopende tijdreeksgegevens en een volledig uitgeruste webportal om u te helpen bij het gebruik van de service. Bekijk de Quick start voor [metrische gegevens Advisor](./metrics-advisor/quickstarts/rest-api-and-client-library.md) om aan de slag te gaan met de service. |
|[Personalizer](./personalizer/index.yml "Personalizer")|Met Personalizer kunt u de beste ervaring kunt kiezen om aan uw gebruikers te laten zien, waarbij u leert van hun real-time gedrag. Zie de [Snelstartgids van personaler](./personalizer/quickstart-personalizer-sdk.md) om aan de slag te gaan met de service.|

## <a name="search-apis"></a>Search-API's

> [!NOTE]
> Op zoek naar [Azure Cognitive Search](../search/index.yml)? Hoewel het Cognitive Services gebruikt voor bepaalde taken, is het een verschillende zoektechnologie die ander scenario's ondersteunt.

|Servicenaam|Beschrijving van de service|
|:-----------|:------------------|
|[Bing Nieuws zoeken](/azure/cognitive-services/bing-news-search/ "Zoeken in Bing Nieuws")|Bing News Search retourneert een lijst met nieuwsartikelen die relevant zijn voor de query van de gebruiker.|
|[Bing Video's zoeken](/azure/cognitive-services/Bing-Video-Search/ "Bing Video's zoeken")|Bing Video Search retourneert een lijst met video's die relevant zijn voor de query van de gebruiker.|
|[Bing Webzoekopdrachten](./bing-web-search/index.yml "Bing Webzoekopdrachten")|Bing Web Search retourneert een lijst met zoekresultaten die relevant zijn voor de query van de gebruiker.|
|[Bing Automatische suggesties](/azure/cognitive-services/Bing-Autosuggest "Bing Automatische suggesties")|Met Bing Automatische suggesties kunt u een gedeeltelijke query-zoekterm naar Bing verzenden en een lijst ontvangen met voorgestelde query’s.|
|[Bing Aangepaste zoekopdrachten](/azure/cognitive-services/bing-custom-search "Bing Aangepaste zoekopdrachten")|Met Bing Aangepaste zoekopdrachten kunt u op maat gemaakte zoekervaringen maken voor onderwerpen die u interesseren.|
|[Bing Entiteiten zoeken](/azure/cognitive-services/bing-entities-search/ "Bing Entiteiten zoeken")|Bing Entiteiten zoeken retourneert informatie over entiteiten die volgens Bing relevant zijn voor een query van een gebruiker.|
|[Bing Afbeeldingen zoeken](/azure/cognitive-services/bing-image-search "Bing Afbeeldingen zoeken")|Bing Image Search retourneert een weergave van afbeeldingen die relevant zijn voor de query van de gebruiker.|
|[Bing Visuele zoekopdrachten](/azure/cognitive-services/bing-visual-search "Bing Visuele zoekopdrachten")|Bing Visual Search retourneert inzichten over een afbeelding zoals visueel vergelijkbare afbeeldingen, bronnen voor winkelen voor producten die zijn gevonden in de afbeelding en gerelateerde zoekopdrachten.|
|[Bing Zoeken naar lokale bedrijven](/azure/cognitive-services/bing-local-business-search/ "Bing Zoeken naar lokale bedrijven")| Bing-API voor zoeken naar lokale bedrijven stelt uw toepassingen in staat om contact- en locatiegegevens van lokale bedrijven te vinden op basis van zoekquery's.|
|[Bing Spellingcontrole](/azure/cognitive-services/bing-spell-check/ "Bing Spellingcontrole")|Met Bing Spellingcontrole kunt u contextuele grammatica- en spellingcontrole uitvoeren.|

## <a name="get-started-with-cognitive-services"></a>Aan de slag met Cognitive Services

Begin door een Cognitive Services-resource te maken met praktische quickstarts, met behulp van de:

* [Azure Portal](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure Portal")
* [Azure-CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK-clientbibliotheken](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [ARM-sjablonen (Azure Resource Manager)](./create-account-resource-manager-template.md?tabs=portal "ARM-sjablonen (Azure Resource Manager)")

## <a name="using-cognitive-services-in-different-development-environments"></a>Cognitive Services gebruiken in verschillende ontwikkel omgevingen

Met Azure en Cognitive Services hebt u toegang tot verschillende ontwikkelopties, zoals:

* Automatiserings- en integratiehulpprogramma's, zoals Logic Apps en Power Automate.
* Implementatie-opties zoals Azure Functions en de App Service. 
* Cognitive Services Docker-containers voor veilige toegang.
* Hulpprogramma's als Apache Spark, Azure Databricks, Azure Synapse Analytics en Azure Kubernetes Service voor big data-scenario's. 

Zie [Ontwikkelopties voor Cognitive Services](./cognitive-services-development-options.md) voor meer informatie.

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>Cognitive Services veilig gebruiken

Azure Cognitive Services biedt een gelaagd beveiligingsmodel, inclusief [verificatie](authentication.md "verificatie") met Azure Active Directory-referenties, een geldige resourcesleutel en [Azure Virtual Networks](cognitive-services-virtual-networks.md "Virtuele netwerken van Azure").

## <a name="containers-for-cognitive-services"></a>Containers voor Cognitive Services

 Azure Cognitive Services biedt verschillende docker-containers waarmee u dezelfde Api's kunt gebruiken die beschikbaar zijn in azure, on-premises. Met deze containers hebt u de flexibiliteit om Cognitive Services dichter bij uw gegevens te brengen voor naleving, beveiliging of andere operationele redenen. Ontdek meer over [Cognitive Services-containers](cognitive-services-container-support.md "Cognitive Services-containers").

## <a name="regional-availability"></a>Regionale beschikbaarheid

De API's in Cognitive Services worden gehost op een groeiende netwerk van door Microsoft beheerde datacenters. U kunt de regionale beschikbaarheid voor elke API vinden in de [Lijst met Azure-regio's](https://azure.microsoft.com/regions "Lijst met Azure-regio's").

Bent u op zoek naar een regio die nog niet wordt ondersteund? Laat het ons weten door een functieverzoek in te dienen op ons [UserVoice-forum](https://cognitive.uservoice.com/ "UserVoice-forum").

## <a name="supported-cultural-languages"></a>Ondersteunde culturele talen

Cognitive Services biedt ondersteuning voor een groot aantal culturele talen op het service niveau. U kunt de beschikbare talen voor elke API terugvinden in de [lijst met ondersteunde talen](language-support.md "lijst met ondersteunde talen").

## <a name="certifications-and-compliance"></a>Certificering en compliance

Cognitive Services kreeg certificeringen toegekend, zoals CSA STAR Certification, FedRAMP Moderate en HIPAA BAA. U kunt certificeringen [downloaden](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "downloadenen") voor uw eigen audits en beveiligingscontroles.

Ga naar het [Vertrouwenscentrum](https://servicetrust.microsoft.com/ "Vertrouwenscentrum") voor informatie over privacy en gegevensbeheer.

## <a name="support"></a>Ondersteuning

Cognitive Services biedt verschillende ondersteuningsopties die u kunt gebruiken voor het maken van intelligente toepassingen. Cognitive Services heeft ook een krachtige community van ontwikkelaars die u kunnen helpen bij het beantwoorden van uw specifieke vragen. Zie [Ondersteuning en Help-opties voor Cognitive Services](cognitive-services-support-options.md "Ondersteuning en Help-opties voor Cognitive Services") voor een volledige lijst met beschikbare opties.

## <a name="next-steps"></a>Volgende stappen

* [Een Cognitive Services-account maken](cognitive-services-apis-create-account.md "Een Cognitive Services-account maken")
* [Wat is er nieuw in Cognitive Services-documenten?](whats-new-docs.md "Wat is er nieuw in Cognitive Services-documenten?")
* [Kosten plannen en beheren voor Cognitive Services](plan-manage-costs.md)
