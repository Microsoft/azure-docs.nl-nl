---
title: Azure-resources-QnA Maker
description: QnA Maker maakt gebruik van verschillende Azure-bronnen, elk met een ander doel. Als u begrijpt hoe deze afzonderlijk worden gebruikt, kunt u de juiste prijs categorie plannen en selecteren of u moet weten wanneer u de prijs categorie wilt wijzigen. Als u begrijpt hoe ze in combi natie worden gebruikt, kunt u problemen opsporen en oplossen wanneer deze zich voordoen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: cd64c19e7e9af05becd7a6978ceb4d0306112170
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351892"
---
# <a name="azure-resources-for-qna-maker"></a>Azure-resources voor QnA Maker

QnA Maker maakt gebruik van verschillende Azure-bronnen, elk met een ander doel. Als u begrijpt hoe deze afzonderlijk worden gebruikt, kunt u de juiste prijs categorie plannen en selecteren of u moet weten wanneer u de prijs categorie wilt wijzigen. Als u begrijpt hoe ze _in combi natie_ worden gebruikt, kunt u problemen opsporen en oplossen wanneer deze zich voordoen.

## <a name="resource-planning"></a>Resource planning

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Wanneer u voor het eerst een QnA Maker Knowledge Base ontwikkelt, is in de prototype fase gebruikelijk dat er één QnA Maker resource is voor testen en productie.

Wanneer u naar de ontwikkelings fase van het project gaat, moet u rekening houden met het volgende:

* Hoeveel talen bevat uw Knowledge Base-systeem?
* Hoeveel regio's hebt u nodig om de Knowledge Base beschikbaar te stellen?
* Hoeveel documenten in elk domein wordt het systeem bewaard?

Plan voor een enkele QnA Maker resource alle kennis grondslagen met dezelfde taal, dezelfde regio en dezelfde combi natie van onderwerps domeinen.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

Wanneer u voor het eerst een QnA Maker beheerde Knowledge Base ontwikkelt, is in de prototype fase gebruikelijk een enkele QnA Maker beheerde resource voor zowel testen als productie.

Wanneer u naar de ontwikkelings fase van het project gaat, moet u rekening houden met het volgende:

* Hoeveel talen bevat uw Knowledge Base-systeem?
* Hoeveel regio's hebt u nodig om de Knowledge Base beschikbaar te stellen?
* Hoeveel documenten in elk domein wordt het systeem bewaard?

---

## <a name="pricing-tier-considerations"></a>Overwegingen voor prijs Categorieën

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Normaal gesp roken zijn er drie para meters die u moet overwegen:

* **De door Voer die u nodig hebt via de service**:
    * Selecteer het juiste [app-abonnement](https://azure.microsoft.com/pricing/details/app-service/plans/) voor uw app service op basis van uw behoeften. U kunt de app omhoog of omlaag [schalen](../../../app-service/manage-scale-up.md) .
    * Dit moet ook van invloed zijn op uw selectie van Azure **Cognitive Search** SKU. Zie [hier](../../../search/search-sku-tier.md)voor meer informatie. Daarnaast moet u mogelijk Cognitive Search [capaciteit](../../../search/search-capacity-planning.md) aanpassen met replica's.

* **Grootte en aantal kennis grondslagen**: Kies de juiste [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) voor uw scenario. Normaal gesp roken bepaalt u het aantal kennis grondslagen dat u nodig hebt op basis van het aantal verschillende onderwerps domeinen. Zodra het onderwerp domein (voor een enkele taal) in één kennis database moet zijn.

    U kunt N-1 Knowledge bases publiceren in een bepaalde laag, waarbij N de Maxi maal toegestane indexen in de laag is. Controleer ook de maximale grootte en het aantal documenten dat per laag is toegestaan.

    Als uw laag bijvoorbeeld 15 toegestane indexen heeft, kunt u 14 Knowledge bases publiceren (1 index per gepubliceerde kennis basis). De vijftiende index wordt gebruikt voor alle kennis grondslagen voor ontwerpen en testen.

* **Aantal documenten als bronnen**: de gratis SKU van de QnA Maker Management-service beperkt het aantal documenten dat u kunt beheren via de portal en de api's tot 3 (van elke grootte van 1 MB). De standaard-SKU heeft geen limieten voor het aantal documenten dat u kunt beheren. Meer informatie vindt u [hier](https://aka.ms/qnamaker-pricing).

De volgende tabel bevat een aantal richt lijnen op hoog niveau.

|                            | QnA Maker beheer | App Service | Azure Cognitive Search | Beperkingen                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Experimenten**        | Gratis SKU             | Gratis laag   | Gratis laag    | Publiceren tot 2 Kb's, 50 MB aan grootte  |
| **Ontwikkel-en test omgeving**   | Standaard SKU         | Gedeeld      | Basic        | Maxi maal 14 Kb's publiceren, grootte van 2 GB    |
| **Productie omgeving** | Standaard SKU         | Basic       | Standard     | Publiceer tot 49 Kb's, grootte van 25 GB |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

Normaal gesp roken zijn er drie para meters die u moet overwegen:

* **De door Voer die u nodig hebt via de service**:
    * QnA Maker Managed (preview) is een gratis service en de door Voer is momenteel beperkt bij 10 TPS voor zowel beheer-Api's als Voorspellings-Api's.
    * Dit moet ook van invloed zijn op uw selectie van Azure **Cognitive Search** SKU. Zie [hier](../../../search/search-sku-tier.md)voor meer informatie. Daarnaast moet u mogelijk Cognitive Search [capaciteit](../../../search/search-capacity-planning.md) aanpassen met replica's.

* **Grootte en aantal kennis grondslagen**: Kies de juiste [Azure Search-SKU](https://azure.microsoft.com/pricing/details/search/) voor uw scenario. Normaal gesp roken bepaalt u het aantal kennis grondslagen dat u nodig hebt op basis van het aantal verschillende onderwerps domeinen. Zodra het onderwerp domein (voor een enkele taal) in één kennis database moet zijn.

    Met QnA Maker Managed (preview) hebt u de mogelijkheid om uw QnA Maker-service in te stellen voor Kb's in één taal of in meerdere talen. U kunt deze selectie maken wanneer u de eerste Knowledge Base maakt in uw QnA Maker Managed (preview)-service.

    ![QnA Maker Managed (preview) multi-Knowledge Base selecteren](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

    U kunt een N-1 Knowledge Base publiceren van één taal of een N/2 Knowledge Base van verschillende talen in een bepaalde laag, waarbij N de Maxi maal toegestane indexen in de laag is. Controleer ook de maximale grootte en het aantal documenten dat per laag is toegestaan.

    Als uw laag bijvoorbeeld 15 toegestane indexen heeft, kunt u 14 Knowledge bases van dezelfde taal publiceren (1 index per gepubliceerde kennis basis). De vijftiende index wordt gebruikt voor alle kennis grondslagen voor ontwerpen en testen. Als u ervoor kiest om kennis bases in verschillende talen te hebben, kunt u alleen 7 knowledge bases publiceren.

* **Aantal documenten als bronnen**: QnA Maker beheerd (preview) is een gratis service en er zijn geen beperkingen voor het aantal documenten dat u als bronnen kunt toevoegen. Meer informatie vindt u [hier](https://aka.ms/qnamaker-pricing).

De volgende tabel bevat een aantal richt lijnen op hoog niveau.

|                            |Azure Cognitive Search | Beperkingen                      |
| -------------------------- |------------ | -------------------------------- |
| **Experimenten**        |Gratis laag    | Publiceren tot 2 Kb's, 50 MB aan grootte  |
| **Ontwikkel-en test omgeving**   |Basic        | Maxi maal 14 Kb's publiceren, grootte van 2 GB    |
| **Productie omgeving** |Standard     | Publiceer tot 49 Kb's, grootte van 25 GB |

---

## <a name="recommended-settings"></a>Aanbevolen instellingen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

|Doel-QPS | App Service | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 instantie   | S1, 1 instantie    |
| 50         | S3, 10 instanties       | S1, 12 exemplaren         |
| 80         | S3, 10 instanties      |  S3, 12 exemplaren  |
| 100         | P3V2, 10 instanties  | S3, 12 instanties, 3 partities   |
| 200 tot 250         | P3V2, 20 exemplaren | S3, 12 instanties, 3 partities    |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

QnA Maker Managed is een gratis service en de door Voer is momenteel beperkt tot 10 trans acties per seconde voor zowel beheer-Api's als Voorspellings-Api's. Als u 10 trans acties per seconde voor uw service wilt richten, raden we u aan de Cognitive Search van S1 (1-exemplaar) van Azure te bevelen.

---

## <a name="when-to-change-a-pricing-tier"></a>Wanneer u een prijs categorie wilt wijzigen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

|Upgraden|Reden|
|--|--|
|[Upgrade uitvoeren](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker Management-SKU|U wilt meer QnA-paren of document bronnen in uw Knowledge Base hebben.|
|[Upgrade uitvoeren](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) App Service SKU en controleer Cognitive Search laag en [maak Cognitive Search replica's](../../../search/search-capacity-planning.md)|Uw Knowledge Base moet meer aanvragen van uw client-app leveren, zoals een chat-bot.|
|[Upgrade uitvoeren](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search-service|U bent van plan om veel kennis bases te hebben.|

Down load de nieuwste runtime-updates door [uw app service in de Azure portal bij te werken](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

[Upgrade uitvoeren](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Azure Cognitive Search-service wanneer u van plan bent om veel kennis bases te hebben.

---

## <a name="resource-naming-considerations"></a>Overwegingen voor het benoemen van resources

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

De resource naam voor de QnA Maker resource, zoals `qna-westus-f0-b` , wordt ook gebruikt voor het benoemen van de andere resources.

In het venster Azure Portal maken kunt u een QnA Maker resource maken en de prijs categorieën voor de andere resources selecteren.

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding van Azure Portal voor het maken van QnA Maker resources](../media/concept-azure-resource/create-blade.png)

Nadat de resources zijn gemaakt, hebben ze dezelfde naam, met uitzonde ring van de optionele Application Insights resource, die postpends tekens aan de naam.

> [!div class="mx-imgBorder"]
> ![Scherm opname van Azure Portal resource-vermelding](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Maak een nieuwe resource groep wanneer u een QnA Maker resource maakt. Hiermee kunt u alle resources weer geven die zijn gekoppeld aan de QnA Maker resource bij het zoeken op resource groep.

> [!TIP]
> Gebruik een naamgevings Conventie om prijs categorieën aan te geven binnen de naam van de resource of de resource groep. Wanneer er fouten optreden bij het maken van een nieuwe Knowledge Base of het toevoegen van nieuwe documenten, is de Cognitive Search prijs categorie limiet een veelvoorkomend probleem.

### <a name="resource-purposes"></a>Resource doeleinden

Elke Azure-resource die is gemaakt met QnA Maker heeft een specifiek doel:

* QnA Maker resource
* Cognitive Search resource
* App Service
* App-plan service
* Application Insights-service


### <a name="cognitive-search-resource"></a>Cognitive Search resource

De [Cognitive Search](../../../search/index.yml) resource wordt gebruikt voor het volgende:

* De QnA-paren opslaan
* Geef de eerste positie (#1) van de QnA-paren op tijdens runtime

#### <a name="index-usage"></a>Index gebruik

De resource houdt één index om te fungeren als de test index en de resterende indexen voldoen aan één gepubliceerde kennis database.

Een resource die is geprijsd op 15 indexen, houdt 14 gepubliceerde kennis grondslagen en een index wordt gebruikt voor het testen van de kennis bases. Deze test index is gepartitioneerd door de Knowledge Base zodat een query die gebruikmaakt van het interactieve test deel venster de test index gebruikt, maar alleen resultaten van de specifieke partitie die is gekoppeld aan de specifieke Knowledge Base, worden geretourneerd.

#### <a name="language-usage"></a>Taal gebruik

De eerste Knowledge Base die in de QnA Maker resource wordt gemaakt, wordt gebruikt om de _afzonderlijke_ taal sets voor de Cognitive Search resource en alle bijbehorende indexen te bepalen. U kunt slechts _één taal instellen_ voor een QnA Maker-service.

### <a name="qna-maker-resource"></a>QnA Maker resource

De QnA Maker-resource biedt toegang tot de ontwerp-en publicatie-Api's, evenals de NLP (QnA) op basis van de laag voor natuurlijke taal verwerking (micro #2) die tijdens de uitvoering worden gemaakt.

Met de tweede classificatie worden intelligente filters toegepast die meta gegevens en opvolgings vragen kunnen bevatten.

#### <a name="qna-maker-resource-configuration-settings"></a>Configuratie-instellingen van QnA Maker bron

Wanneer u een nieuwe Knowledge Base maakt in de [QnA Maker Portal](https://qnamaker.ai), is de **taal** instelling de enige instelling die op het resource niveau wordt toegepast. U selecteert de taal bij het maken van de eerste Knowledge Base voor de resource.

### <a name="app-service-and-app-service-plan"></a>App service-en app service-plan

De [app service](../../../app-service/index.yml) wordt door uw client toepassing gebruikt om toegang te krijgen tot de gepubliceerde kennis grondslagen via het runtime-eind punt.

Als u een query wilt uitvoeren voor de gepubliceerde kennis database, gebruiken alle gepubliceerde kennis bases hetzelfde URL-eind punt, maar geeft u de **Knowledge Base-id** op in de route.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) wordt gebruikt voor het verzamelen van chat logboeken en telemetrie. Bekijk de algemene [Kusto-query's](../how-to/get-analytics-knowledge-base.md) voor informatie over uw service.

## <a name="share-services-with-qna-maker"></a>Services delen met QnA Maker

QnA Maker maakt verschillende Azure-resources. Gebruik de volgende tabel om te begrijpen wat u wel en niet kunt delen om het beheer te beperken en te profiteren van het delen van kosten:

|Service|Delen|Reden|
|--|--|--|
|Cognitive Services|X|Niet mogelijk door het ontwerp|
|App Service-plan|✔|Er is een vaste schijf ruimte toegewezen voor een App Service plan. Als andere apps die hetzelfde App Service plan delen, veel schijf ruimte gebruiken, ondervinden de QnAMaker App Service-instantie problemen.|
|App Service|X|Niet mogelijk door het ontwerp|
|Application Insights|✔|Kan worden gedeeld|
|Zoekservice|✔|1. `testkb` is een gereserveerde naam voor de QnAMaker-service en kan niet worden gebruikt door anderen.<br>2. het synoniem van de map met de naam `synonym-map` is gereserveerd voor de QnAMaker-service.<br>3. het aantal gepubliceerde kennis grondslagen is beperkt door de zoek service laag. Als er gratis indexen beschikbaar zijn, kunnen andere services deze gebruiken.|

### <a name="using-a-single-cognitive-search-service"></a>Eén Cognitive Search-service gebruiken

Als u een QnA-service en de bijbehorende afhankelijkheden (zoals zoeken) maakt via de portal, wordt er een zoek service voor u gemaakt en gekoppeld aan de QnA Maker-service. Nadat deze resources zijn gemaakt, kunt u de App Service-instelling bijwerken om een eerder bestaande zoek service te gebruiken en de toepassing verwijderen die u zojuist hebt gemaakt.

Meer informatie [over het configureren](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) van QnA Maker voor het gebruik van een andere cognitieve service resource dan die welke is gemaakt als onderdeel van het QnA maker van het maken van resources.

## <a name="management-service-region"></a>Regio beheer service

De beheer service van QnA Maker wordt alleen gebruikt voor de QnA Maker Portal en voor initiële gegevens verwerking. Deze service is alleen beschikbaar in de regio **VS-West** . Er worden geen klant gegevens opgeslagen in deze service vs-West.

## <a name="keys-in-qna-maker"></a>Sleutels in QnA Maker

Uw QnA Maker-service behandelt twee soorten sleutels: het **ontwerpen van sleutels** en **query-eindpunt sleutels** die worden gebruikt met de runtime die wordt gehost in de app service.

Als u op zoek bent naar uw **abonnements sleutel**, [is de terminologie gewijzigd](#subscription-keys).

Gebruik deze sleutels bij het indienen van aanvragen bij de service via Api's.

![Sleutelbeheer](../media/qnamaker-how-to-key-management/key-management.png)

|Naam|Locatie|Doel|
|--|--|--|
|Sleutel ontwerpen|[Azure-portal](https://azure.microsoft.com/free/cognitive-services/)|Deze sleutels worden gebruikt voor toegang tot de Api's van de [QnA Maker Management-service](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Met deze Api's kunt u de vragen en antwoorden in uw Knowledge Base bewerken en uw kennis database publiceren. Deze sleutels worden gemaakt wanneer u een nieuwe QnA Maker-service maakt.<br><br>Deze sleutels vindt u op de pagina **sleutels** van de **Cognitive Services** resource.|
|Query-eindpunt sleutel|[QnA Maker-portal](https://www.qnamaker.ai)|Deze sleutels worden gebruikt om een query uit te geven op het gepubliceerde Knowledge Base-eind punt om een antwoord te krijgen op de vraag van een gebruiker. Normaal gesp roken gebruikt u dit query-eind punt in uw chat-bot of in de client toepassings code die verbinding maakt met de QnA Maker-service. Deze sleutels worden gemaakt wanneer u uw QnA Maker Knowledge Base publiceert.<br><br>Deze sleutels vindt u op de pagina **Service-instellingen** . Ga naar deze pagina vanuit het menu van de gebruiker in de rechter bovenhoek van de pagina in de vervolg keuzelijst.|

### <a name="subscription-keys"></a>Abonnementssleutels

De termen ontwerpen en query-eind punt sleutel zijn herstel voorwaarden. De vorige term is **abonnements sleutel**. Als u andere documentatie raadpleegt die verwijst naar abonnements sleutels, zijn deze gelijk aan de ontwerp-en query-eindpunt sleutels (gebruikt in de runtime).

U moet weten welke sleutel u wilt zoeken met beinformatie over de toegang tot de sleutel, het Knowledge Base-beheer of de Knowledge Base-query.

### <a name="recommended-settings-for-network-isolation"></a>Aanbevolen instellingen voor netwerk isolatie

* Beveilig de cognitieve service resource van open bare toegang door [het virtuele netwerk te configureren](../../cognitive-services-virtual-networks.md?tabs=portal).
* App Service van open bare toegang beveiligen (QnA runtime):
    * Alleen verkeer van de cognitieve service Ip's toestaan. Deze zijn al opgenomen in de service tags ' CognitiveServicesManagement '. Dit is vereist voor het ontwerpen van Api's (Create/update KB) om de app service te kunnen aanroepen en Azure Search-service dienovereenkomstig bij te werken.
    * Zorg ervoor dat u ook andere toegangs punten als bot-service, QnA Maker-Portal (mogelijk uw Corpnet), enzovoort kunt gebruiken. voor de voor spelling ' GenerateAnswer ' API-toegang.
    * Bekijk [meer informatie over service tags.](../../../virtual-network/service-tags-overview.md)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

De resource naam voor de QnA Maker Managed (preview)-resource, zoals `qna-westus-f0-b` , wordt ook gebruikt om de andere resources een naam te geven.

In het venster Azure Portal maken kunt u een resource voor QnA Maker Managed (preview) maken en de prijs categorieën voor de andere resources selecteren.

> [!div class="mx-imgBorder"]
> ![Scherm afbeelding van Azure Portal voor het maken van QnA Maker beheerde (preview)-resource ](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png) nadat de resources zijn gemaakt, hebben ze dezelfde naam.

> [!div class="mx-imgBorder"]
> ![Scherm opname van Azure Portal bron vermelding QnA Maker beheerd (preview-versie)](../media/qnamaker-how-to-setup-service/resources-created-v2.png)
> [!TIP]
> Maak een nieuwe resource groep wanneer u een QnA Maker resource maakt. Hiermee kunt u alle resources weer geven die zijn gekoppeld aan de QnA Maker beheerde resource (preview) bij het zoeken op resource groep.
> [!TIP]
> Gebruik een naamgevings Conventie om prijs categorieën aan te geven binnen de naam van de resource of de resource groep. Wanneer er fouten optreden bij het maken van een nieuwe Knowledge Base of het toevoegen van nieuwe documenten, is de Cognitive Search prijs categorie limiet een veelvoorkomend probleem.

### <a name="resource-purposes"></a>Resource doeleinden

Elke Azure-resource die is gemaakt met QnA Maker Managed (preview) heeft een specifiek doel:

* QnA Maker resource
* Cognitive Search resource

### <a name="azure-cognitive-search-resource"></a>Azure Cognitive Search-resource

De [Cognitive Search](../../../search/index.yml) resource wordt gebruikt voor het volgende:

* De QnA-paren opslaan
* Geef de eerste positie (#1) van de QnA-paren op tijdens runtime

#### <a name="index-usage"></a>Index gebruik

U kunt een N-1 Knowledge Base publiceren van één taal of een N/2 Knowledge Base van verschillende talen in een bepaalde laag, waarbij N de Maxi maal toegestane indexen in de Azure Cognitive Search-laag is. Controleer ook de maximale grootte en het aantal documenten dat per laag is toegestaan.

Als uw laag bijvoorbeeld 15 toegestane indexen heeft, kunt u 14 Knowledge bases van dezelfde taal publiceren (1 index per gepubliceerde kennis basis). De vijftiende index wordt gebruikt voor alle kennis grondslagen voor ontwerpen en testen. Als u ervoor kiest om kennis bases in verschillende talen te hebben, kunt u alleen 7 knowledge bases publiceren.

#### <a name="language-usage"></a>Taal gebruik

Met QnA Maker Managed (preview) hebt u de mogelijkheid om uw QnA Maker-service in te stellen voor Knowledge bases in één taal of in meerdere talen. U maakt deze keuze tijdens het maken van de eerste Knowledge Base in uw QnA Maker-service. [Hier](#pricing-tier-considerations) ziet u hoe u de taal instellingen per Knowledge Base inschakelt.

### <a name="qna-maker-resource"></a>QnA Maker resource

De resource QnA Maker Managed (preview) biedt toegang tot de ontwerp-en publicatie-Api's, host de runtime voor de classificatie en biedt ook telemetrie.

## <a name="region-support"></a>Ondersteuning voor regio

In QnA Maker Managed (preview) kunnen zowel het beheer als de Voorspellings Services zich in dezelfde regio bevinden. Momenteel is QnA Maker beheerd (preview) beschikbaar in **Zuid-Centraal VS, Europa-Noord en Australië-Oost**.

### <a name="keys-in-qna-maker-managed-preview"></a>Sleutels in QnA Maker beheerd (preview-versie)

Uw QnA Maker Managed (preview)-service behandelt twee soorten sleutels: het **ontwerpen van sleutels** en **Azure Cognitive Search sleutels** die worden gebruikt voor toegang tot de service in het abonnement van de klant.

Als u op zoek bent naar uw **abonnements sleutel**, [is de terminologie gewijzigd](#subscription-keys).

Gebruik deze sleutels bij het indienen van aanvragen bij de service via Api's.

![Beheerde Preview voor sleutel beheer](../media/qnamaker-how-to-key-management/qnamaker-v2-key-management.png)

|Naam|Locatie|Doel|
|--|--|--|
|Sleutel ontwerpen|[Azure-portal](https://azure.microsoft.com/free/cognitive-services/)|Deze sleutels worden gebruikt voor toegang tot de Api's van de [QnA Maker Management-service](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Met deze Api's kunt u de vragen en antwoorden in uw Knowledge Base bewerken en uw kennis database publiceren. Deze sleutels worden gemaakt wanneer u een nieuwe QnA Maker-service maakt.<br><br>Deze sleutels vindt u op de pagina **sleutels** van de **Cognitive Services** resource.|
|Azure Cognitive Search-beheer sleutel|[Azure-portal](../../../search/search-security-api-keys.md)|Deze sleutels worden gebruikt om te communiceren met de Azure cognitieve Search-service die is geïmplementeerd in het Azure-abonnement van de gebruiker. Wanneer u een Azure cognitieve zoek opdracht koppelt aan de service QnA Maker Managed (preview), wordt de beheer sleutel automatisch door gegeven aan de QnA Maker-service. <br><br>Op de pagina **sleutels** vindt u deze sleutels op de **Azure Cognitive Search** -resource.|

### <a name="subscription-keys"></a>Abonnementssleutels

De termen ontwerpen en query-eind punt sleutel zijn herstel voorwaarden. De vorige term is **abonnements sleutel**. Als u andere documentatie raadpleegt die verwijst naar abonnements sleutels, zijn deze gelijk aan de ontwerp-en query-eindpunt sleutels (gebruikt in de runtime).

U moet weten welke sleutel u wilt zoeken met beinformatie over de toegang tot de sleutel, het Knowledge Base-beheer of de Knowledge Base-query.

### <a name="recommended-settings-for-network-isolation"></a>Aanbevolen instellingen voor netwerk isolatie 

Beveilig de cognitieve service resource van open bare toegang door [het virtuele netwerk te configureren](../../cognitive-services-virtual-networks.md?tabs=portal).

---

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de QnA Maker [Knowledge Base](../index.yml)
* Inzicht in de [levens cyclus van een Knowledge Base](development-lifecycle-knowledge-base.md)
* [Limieten](../limits.md) voor service-en Knowledge bases controleren