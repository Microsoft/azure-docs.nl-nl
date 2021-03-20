---
title: Internet Analyzer-client insluiten | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de Internet Analyzer java script-client insluit in uw toepassing.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 0d4b27b85ac7bc61e14a79f29e4e26ec4973ced1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84744048"
---
# <a name="embed-the-internet-analyzer-client"></a>De Internet Analyzer-client insluiten

In dit artikel wordt beschreven hoe u de Java script-client insluit in uw toepassing. De installatie van deze client is nodig om tests uit te voeren en Score Card analyses te ontvangen. **De profiel-specifieke java script-client wordt opgegeven nadat de eerste test is geconfigureerd.** U kunt dan door gaan met het toevoegen of verwijderen van tests aan dat profiel zonder dat u een nieuw script hoeft in te sluiten. Bekijk het [overzicht](internet-analyzer-overview.md) voor meer informatie over Internet Analyzer. 

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voordat u begint

Internet Analyzer vereist toegang tot Azure en andere micro soft-Services om goed te laten functioneren. Zorg ervoor dat u toegang hebt tot `fpc.msedge.net` het netwerk en eventuele vooraf geconfigureerde eind punt-url's (zichtbaar via [cli](internet-analyzer-cli.md)) voordat u de client insluit.

## <a name="find-the-client-script-url"></a>De URL van het client script zoeken

U kunt de script-URL vinden via de Azure Portal of de Azure CLI nadat een test is geconfigureerd. Zie [Een Internet Analyzer-resource maken](internet-analyzer-create-test-portal.md) voor meer informatie.

Optie 1. Gebruik [deze koppeling](https://aka.ms/InternetAnalyzerPreviewPortal) In het Azure Portal om de pagina preview portal te openen voor Azure Internet Analyzer. Navigeer naar uw Internet Analyzer-profiel om de URL van het script te bekijken door te gaan naar **instellingen > configuratie**.

Optie 2. Controleer de eigenschap met behulp van de Azure CLI `scriptFileUri` .
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Details van client

Het script wordt specifiek voor uw profiel en tests gegenereerd. Nadat het script is geladen, wordt het uitgevoerd met een vertraging van twee seconden. Eerst neemt het contact op met de Internet Analyzer-service voor het ophalen van de lijst met eind punten die in uw tests zijn geconfigureerd. Vervolgens worden de metingen uitgevoerd en worden de getimede resultaten naar de Internet Analyzer-service geüpload.

## <a name="client-examples"></a>Client voorbeelden

In deze voor beelden ziet u enkele eenvoudige methoden om de client-java script in te sluiten in uw webpagina of toepassing. We gebruiken `0bfcb32638b44927935b9df86dcfe397` als een voorbeeld profiel-id voor de script-URL.

### <a name="run-on-page-load"></a>Uitvoeren bij laden pagina
De eenvoudigste methode is het gebruik van de script-tag in het META-code blok. Met deze tag wordt het script één keer per pagina geladen uitgevoerd.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Internet Analyzer](internet-analyzer-faq.md)
