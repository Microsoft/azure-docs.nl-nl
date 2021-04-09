---
title: Een Translator-resource maken
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u een Azure Cognitive Services Translator-resource maakt en een abonnements sleutel en eind punt-URL ontvangt.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a0d8532d19aff41bc5e7defb3b58462e81018749
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "101712926"
---
# <a name="create-a-translator-resource"></a>Een Translator-resource maken

In dit artikel leert u hoe u een Translator-resource maakt in de Azure Portal. [Azure Translator](translator-info-overview.md) is een Cloud service voor automatische vertaling die deel uitmaakt van de [Azure Cognitive Services](../what-are-cognitive-services.md) -familie van rest-api's. Azure-resources zijn exemplaren van services die u maakt. Alle API-aanvragen voor Azure-Services vereisen een **eind punt** -URL en een alleen-lezen **abonnements sleutel** voor het verifiëren van de toegang.

## <a name="prerequisites"></a>Vereisten

U hebt een actief [**Azure-account**](https://azure.microsoft.com/free/cognitive-services/)nodig om aan de slag te gaan.  Als u er nog geen hebt, kunt u [**een gratis abonnement van 12 maanden maken**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Resource typen van Translator

De Translator-service kan worden geopend via twee verschillende resource typen:

* Bron typen **met één service** bieden toegang tot een enkele service-API-sleutel en een eind punt.  

* Resource typen met **meerdere services** bieden toegang tot meerdere Cognitive Services met één API-sleutel en een eind punt. De Cognitive Services resource is momenteel beschikbaar voor de volgende services:
  * Language ([Translator](../translator/translator-info-overview.md), [Language Understanding (LUIS)](../luis/what-is-luis.md), [Text Analytics](../text-analytics/overview.md))  
  * Vision ([Computer Vision](../computer-vision/overview.md)), ([gezicht](../face/overview.md))  
  * Beslissing ([Content moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>Uw resource maken

* Ga rechtstreeks naar de pagina [**Translator maken**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) in het Azure Portal om uw project details te volt ooien.

* Ga rechtstreeks naar de pagina [**Cognitive Services maken**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) in de Azure Portal om uw project details te volt ooien.

>[!TIP]
>Als u wilt, kunt u beginnen op de start pagina van Azure Portal om het proces te **maken** als volgt:
>
> 1. Ga naar de start pagina van [**Azure Portal**](https://ms.portal.azure.com/#home) .
> 1. Selecteer ➕**een resource maken**  in het menu van Azure-Services.
>1. Voer in het zoekvak **Zoeken in Marketplace de** optie **Translator** (single-service resource) of **Cognitive Services** (meerdere service resources) in.  *Zie* [uw resource type kiezen](#create-your-resource)hierboven.
> 1. Selecteer **maken** en u wordt naar de pagina project details geleid.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>De details van uw project en exemplaar volt ooien

1. **Abonnement**. Selecteer een van de beschik bare Azure-abonnementen.

1. **Resourcegroep**. De Azure-resource groep die u als een virtuele container wilt gebruiken voor uw nieuwe resource. U kunt een nieuwe resource groep maken of uw resource toevoegen aan een vooraf bestaande resource groep die dezelfde levens cyclus, machtigingen en beleids regels deelt.

1. **Resource regio**. Kies **globaal** tenzij uw bedrijf of toepassing een specifieke regio vereist. Translator is een niet-regionale service: er is geen afhankelijkheid voor een specifieke Azure-regio. *Zie* [regio's en Beschikbaarheidszones in azure](../../availability-zones/az-overview.md).

1. **Naam**. Voer de naam in die u voor uw resource hebt gekozen. De naam die u kiest, moet uniek zijn in Azure.

> [!NOTE]
> Als u een Translator-functie gebruikt waarvoor een aangepast domein eindpunt vereist is, is de waarde die u invoert in het veld naam de aangepaste domein naam para meter voor het eind punt.

5. **Prijscategorie**. Selecteer een [prijs categorie](https://azure.microsoft.com/pricing/details/cognitive-services/translator) die aan uw behoeften voldoet:

   * Elk abonnement heeft een gratis laag.
   * De laag gratis heeft dezelfde functies en functionaliteit als betaalde abonnementen en verloopt niet.
   * Er is slechts één gratis abonnement per account toegestaan.</li></ul>

1. Als u een resource met meerdere services hebt gemaakt, moet u aanvullende gebruiks gegevens bevestigen via de selectie vakjes.

1. Selecteer **Controleren + maken**.

1. Controleer de service voorwaarden en selecteer **maken** om uw resource te implementeren.

1. Nadat uw resource is geïmplementeerd, selecteert **u Ga naar resource**.

### <a name="authentication-keys-and-endpoint-url"></a>Verificatie sleutels en eind punt-URL

Voor alle Cognitive Services API-aanvragen is een eind punt-URL en een alleen-lezen sleutel voor verificatie vereist.

* **Verificatie sleutels**. Uw sleutel is een unieke teken reeks die wordt door gegeven voor elke aanvraag bij de Vertaal service. U kunt uw sleutel door geven via een query-teken reeks parameter of door deze op te geven in de header van de HTTP-aanvraag.

* **Eind punt-URL**. Gebruik het globale eind punt in uw API-aanvraag, tenzij u een specifieke Azure-regio nodig hebt. *Zie* [basis-url's](reference/v3-0-reference.md#base-urls). De URL van het globale eind punt is `api.cognitive.microsofttranslator.com` .

## <a name="get-your-authentication-keys-and-endpoint"></a>Uw verificatie sleutels en eind punt ophalen

1. Nadat de nieuwe resource is geïmplementeerd, selecteert **u naar resource gaan** of gaat u rechtstreeks naar de resource pagina.
1. Selecteer in de rails onder *resource management* de optie **sleutels en eind punt**.
1. Kopieer en plak uw abonnements sleutels en eind punt-URL op een handige locatie, zoals *micro soft Klad blok*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Sleutel en eind punt ophalen.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Een resource of resource groep verwijderen

> [!Warning]
> Als u een resource groep verwijdert, worden ook alle resources uit de groep verwijderd.

Als u een Cognitive Services-of Translator-resource wilt verwijderen, kunt u **de resource verwijderen** of **de resource groep verwijderen**.

De resource verwijderen:

1. Navigeer naar uw resource groep in de Azure Portal.
1. Selecteer de resources die moeten worden verwijderd door het selectie vakje ernaast in te scha kelen.
1. Selecteer **verwijderen** in het menu bovenaan in de buurt van de rechter rand.
1. Typ *Ja* in het dialoog venster **verwijderde resources** .
1. Selecteer **Verwijderen**.

De resourcegroep verwijderen:

1. Navigeer naar uw resource groep in de Azure Portal.
1. Selecteer de **resource groep verwijderen** in de bovenste menu balk bij de linkerrand.
1. Bevestig het verwijderings verzoek door de naam van de resource groep in te voeren en **verwijderen** te selecteren.

## <a name="how-to-get-started-with-translator"></a>Aan de slag met Translator

In onze Snelstartgids leert u hoe u de Translator-service gebruikt met REST Api's.

> [!div class="nextstepaction"]
> [Aan de slag met Translator](quickstart-translator.md)

## <a name="more-resources"></a>Meer bronnen

* [Code voorbeelden van micro soft Translator](https://github.com/MicrosoftTranslator).  Multi-Language Translator-code voorbeelden zijn beschikbaar op GitHub.
* [Ondersteunings forum voor micro soft Translator](https://www.aka.ms/TranslatorForum)
* [Aan de slag met Azure (video van 3 minuten)](https://azure.microsoft.com/get-started/?b=16.24)