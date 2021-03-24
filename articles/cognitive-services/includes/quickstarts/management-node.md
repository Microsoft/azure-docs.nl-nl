---
title: 'Quickstart: Azure Management-clientbibliotheek voor Node.js'
description: Ga in deze quickstart aan de slag met de Azure Management-clientbibliotheek voor Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 41f6c8e260968eacd04249b3f887d4865907df0d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879643"
---
[Referentiedocumentatie](/javascript/api/@azure/arm-cognitiveservices/) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices) | [Pakket (NPM)](https://www.npmjs.com/package/@azure/arm-cognitiveservices) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/arm-cognitiveservices#sample-code)

## <a name="javascript-prerequisites"></a>JavaScript-vereisten

* Een geldig Azure-abonnement - [Maak een gratis abonnement](https://azure.microsoft.com/free/).
* De huidige versie van [Node.js](https://nodejs.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `npm init` uit om een knooppunttoepassing te maken met een `package.json`-bestand. 

```console
npm init
```

Maak een bestand met de naam _index.js_ voordat u verder gaat.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer de volgende NPM-pakketten:

```console
npm install @azure/arm-cognitiveservices
npm install @azure/ms-rest-js
npm install @azure/ms-rest-nodeauth
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

### <a name="import-libraries"></a>Bibliotheken importeren

Open uw _index.js_-script en importeer de volgende bibliotheken.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_imports)]

## <a name="authenticate-the-client"></a>De client verifiëren

Voeg de volgende velden toe aan de hoofdmap van uw script en vul hun waarden in met behulp van de service-principal die u hebt gemaakt en uw Azure-accountgegevens.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_constants)]

Voeg vervolgens de volgende `quickstart`-functie toe om het belangrijkste werk van uw programma te verwerken. Het eerste codeblok vormt een **CognitiveServicesManagementClient**-object met behulp van de referentievariabelen die u hierboven hebt ingevoerd. Dit object is nodig voor al uw Azure Management-bewerkingen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main_auth)]

## <a name="call-management-functions"></a>Beheerfuncties voor aanroep

Voeg de volgende code toe aan het einde van uw `quickstart`-functie om beschikbare resources weer te geven, maak een voorbeeldresource, vermeld uw eigen resources en verwijder vervolgens de voorbeeldresource. U gaat deze functies in de volgende stappen definiëren.

## <a name="create-a-cognitive-services-resource-nodejs"></a>Een Cognitive Services-resource maken (Node.js)

Als u een nieuwe Cognitive Services-resource wilt maken en zich hierop wilt abonneren, gebruikt u de functie **Maken**. Met deze functie voegt u een nieuwe factureerbare resource toe aan de resourcegroep die u doorgeeft. Wanneer u uw nieuwe resource maakt, moet u weten welk soort service u wilt gebruiken, samen met de prijscategorie (of SKU) en een Azure-locatie. Met de volgende functie worden al deze argumenten gebruikt en wordt een resource gemaakt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Een service en prijscategorie kiezen

Wanneer u een nieuwe resource maakt, moet u weten welk soort service u wilt gebruiken, samen met de [prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/) (of SKU) die u wilt. U gebruikt deze en andere informatie als parameters bij het maken van de resource. De volgende functie geeft een overzicht van de beschikbare soorten Cognitive Service.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Uw resources weergeven

Als u alle resources onder uw Azure-account (voor alle resourcegroepen) wilt weergeven, gebruikt u de volgende functie:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_list)]

## <a name="delete-a-resource"></a>Een resource verwijderen

Met de volgende functie verwijdert u de opgegeven resource uit de opgegeven resourcegroep.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_delete)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voeg de volgende code toe bovenaan uw script om uw belangrijkste `quickstart`-functie aan te roepen met foutafhandeling.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/azure_management_service/create_delete_resource.js?name=snippet_main)]

Voer vervolgens in het consolevenster de toepassing uit met de opdracht `node`.

```console
node index.js
```

## <a name="see-also"></a>Zie ook

* Zie **[aanvragen verifiëren voor Azure Cognitive Services](../../authentication.md)** over het veilig werken met Cognitive Services.
* Zie **[Wat is Azure Cognitive Services?](../../what-are-cognitive-services.md)** voor een lijst met verschillende categorieën in cognitive Services.
* Zie **[ondersteuning voor natuurlijke](../../language-support.md)** talen voor een overzicht van de natuurlijke talen die Cognitive Services ondersteunt.
* Zie **[Cognitive Services als containers gebruiken](../../cognitive-services-container-support.md)** voor meer informatie over het gebruik van Cognitive Services on-premises.
* Zie **[kosten plannen en beheren voor Cognitive Services](../../plan-manage-costs.md)** voor het schatten van de kosten van het gebruik van Cognitive Services.
* Raadpleeg de **[naslag documentatie over Azure Management SDK](/javascript/api/@azure/arm-cognitiveservices/)** voor meer informatie over de Management SDK.
