---
title: 'Procedure: sjablonen voor het genereren van talen gebruiken voor spraak reacties-spraak service'
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u sjablonen voor het genereren van talen kunt gebruiken met aangepaste opdrachten. Met sjablonen voor het maken van talen kunt u de antwoorden aanpassen die worden verzonden naar de client en worden er verschillen in de antwoorden ingevoerd.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 40c5e3474d3992108ef61d34e745bc63c1f7a713
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020943"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>Taalgeneratiesjablonen toevoegen voor spraakreacties

In dit artikel leert u hoe u sjablonen voor het genereren van talen kunt gebruiken met aangepaste opdrachten. Met sjablonen voor het maken van talen kunt u de antwoorden aanpassen die worden verzonden naar de client en worden er verschillen in de antwoorden ingevoerd. Aanpassing van de taal kan worden bereikt door:

- Gebruik van sjablonen voor het genereren van talen
- Gebruik van adaptieve expressies

## <a name="prerequisites"></a>Vereisten

U moet de stappen in de volgende artikelen hebben voltooid:

> [!div class="checklist"]
> * [Procedure: een toepassing maken met eenvoudige opdrachten](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Procedure: para meters toevoegen aan opdrachten](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>Sjablonen voor het genereren van talen

Aangepaste opdrachten sjablonen zijn gebaseerd op de LG- [sjablonen](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)van BotFramework. Aangezien aangepaste opdrachten een nieuwe LG-sjabloon maken, indien nodig (dat wil zeggen, voor spraak reacties in para meters of acties), hoeft u de naam van de LG-sjabloon niet op te geven. In plaats van uw sjabloon te definiëren als:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

U hoeft alleen de hoofd tekst van de sjabloon als volgt te definiëren zonder de naam.

> [!div class="mx-imgBorder"]
> ![voor beeld van sjabloon editor](./media/custom-commands/template-editor-example.png)


Met deze wijziging wordt het verschil in de spraak reacties naar de client verzonden. Voor dezelfde utterance zou de bijbehorende spraak reactie dus wille keurig worden gekozen uit de beschik bare opties.

Door gebruik te maken van LG sjablonen kunt u ook complexe spraak reacties voor opdrachten definiëren met behulp van adaptieve expressies. U kunt de indeling van de [LG-sjablonen](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) raadplegen voor meer informatie. Aangepaste opdrachten bieden standaard ondersteuning voor alle mogelijkheden met de volgende kleine verschillen:

* In de entiteiten van LG-sjablonen worden weer gegeven als $ {EntityName}. In aangepaste opdrachten gebruiken we geen entiteiten, maar para meters kunnen worden gebruikt als variabelen met een van de volgende weer gaven $ {para meter naam} of {para meter naam}
* Sjabloon samenstelling en-uitbrei ding worden niet ondersteund in aangepaste opdrachten. Dit komt doordat u het bestand nooit `.lg` rechtstreeks bewerkt, maar alleen de antwoorden van automatisch gemaakte sjablonen.
* Aangepaste functies die worden geïnjecteerd door LG, worden niet ondersteund in aangepaste opdrachten. Vooraf gedefinieerde functies worden nog steeds ondersteund.
* Opties (strict, replaceNull & lineBreakStyle) worden niet ondersteund in aangepaste opdrachten.

## <a name="add-template-responses-to-turnonoff-command"></a>Sjabloon Reacties toevoegen aan de opdracht TurnOnOff

Wijzig de **TurnOnOff** -opdracht om een nieuwe para meter toe te voegen aan de volgende configuratie:

| Instelling            | Voorgestelde waarde       | 
| ------------------ | --------------------- | 
| Name               | `SubjectContext`         | 
| Is wereld wijd          | uitgeschakeld             | 
| Vereist           | uitgeschakeld               | 
| Type               | Tekenreeks                |
| Standaardwaarde      | `all` |
| Configuratie      | Vooraf gedefinieerde invoer waarden accepteren van interne catalogus | 
| Vooraf gedefinieerde invoer waarden | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>Voltooiings regel wijzigen

Bewerk de sectie **acties** van bestaande voltooiings regel **ConfirmationResponse**. Schakel in het pop-upvenster **actie bewerken** over naar **sjabloon editor** en vervang de tekst door het volgende voor beeld.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

**Train** en **test** uw toepassing als volgt. Let op de variatie van de antwoorden als gevolg van het gebruik van meerdere alternatieven van de sjabloon waarde, en ook voor het gebruik van adaptieve expressies.

* Invoer: de TV inschakelen
* Uitvoer: OK, TV in-of uitschakelen
* Invoer: de TV inschakelen
* Uitvoer: gereed, de TV ingeschakeld
* Invoer: de lichten uitschakelen
* Uitvoer: OK, alle lichten uitschakelen
* Invoer: kamer verlichting uitschakelen
* Uitvoer: OK, de kamer verlichting uitschakelen

## <a name="use-custom-voice"></a>Custom Voice gebruiken

Een andere manier om aangepaste opdrachten te beantwoorden, is door een aangepaste uitvoer stem te selecteren. Gebruik de volgende stappen om de standaard stem naar een aangepaste stem te scha kelen.

1. Selecteer in uw aangepaste opdrachten toepassing **instellingen** in het linkerdeel venster.
1. Selecteer **aangepaste spraak** in het middelste deel venster.
1. Selecteer de gewenste aangepaste of open bare stem in de tabel.
1. Selecteer **Opslaan**.

> [!div class="mx-imgBorder"]
> ![Voorbeeld zinnen met para meters](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Voor **open bare stemmen** zijn **Neural typen** alleen beschikbaar voor bepaalde regio's. Zie [standaard en Neural stemmen per regio/eind punt](./regions.md#standard-and-neural-voices)om de beschik baarheid te controleren.
> - Voor **aangepaste stemmen** kunnen ze worden gemaakt op de pagina aangepast spraak project. Zie [aan de slag met aangepaste spraak](./how-to-custom-voice.md).

De toepassing reageert nu op de geselecteerde stem, in plaats van de standaard stem.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Integreer uw aangepaste opdrachten met de spraak-SDK](./how-to-custom-commands-setup-speech-sdk.md).