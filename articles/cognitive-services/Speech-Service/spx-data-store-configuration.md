---
title: Configuratieopties voor Spraak-CLI - Spraak-service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken en beheren van configuratiebestanden voor gebruik met de Azure Spraak-CLI.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.author: erhopf
ms.openlocfilehash: aa8551e49c8ef16984783c4e9735c987174b180a
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540222"
---
# <a name="speech-cli-configuration-options"></a>Configuratieopties voor Speech-CLI

Het gedrag van de Spraak-CLI kan afhankelijk zijn van instellingen in configuratiebestanden, waarnaar u kunt verwijzen met een `@`-teken. Met de Spraak-CLI wordt een nieuwe instelling opgeslagen in een nieuwe `./spx/data`-submap, die wordt gemaakt in de huidige werkmap voor de Spraak-CLI. Bij het zoeken naar een configuratiewaarde zoekt de Spraak-CLI in uw huidige werkmap en vervolgens in het gegevensarchief in `./spx/data`, en vervolgens in andere gegevensarchieven, waaronder een gegevensarchief voor definitieve alleen-lezenversies in het binaire bestand `spx`. 

In de quickstart over de Spraak-CLI hebt u het gegevensarchief gebruikt om de waarden `@key` en `@region` op te slaan, zodat u deze niet bij elke `spx`-opdracht hoeft op te geven. U kunt configuratiebestanden gebruiken om uw eigen configuratie-instellingen op te slaan, of zelfs gebruiken voor het doorgeven van URL's of andere dynamische inhoud die tijdens runtime is gegenereerd.

## <a name="create-and-manage-configuration-files-in-the-datastore"></a>Configuratiebestanden in het gegevensarchief maken en beheren

In deze sectie wordt het gebruik van een configuratiebestand in het lokale gegevensarchief beschreven voor het opslaan en ophalen van de opdrachtinstellingen met behulp van `spx config`, en wordt de uitvoer van de Spraak-CLI opgeslagen met behulp van de optie `--output`.

In het volgende voorbeeld wordt het configuratiebestand `@my.defaults` gewist, worden sleutelwaardeparen toegevoegd voor **sleutel** en **regio** in het bestand, en wordt de configuratie gebruikt in een aanroep van `spx recognize`.

```console
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

U kunt ook dynamische inhoud naar een configuratiebestand schrijven. Met de volgende opdracht maakt u bijvoorbeeld een aangepast spraakmodel en slaat u de URL van het nieuwe model op in een configuratiebestand. De volgende opdracht wacht tot het model op die URL gereed is voor gebruik voordat het wordt geretourneerd.

```console
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

In het volgende voorbeeld worden twee URL's naar het configuratiebestand `@my.datasets.txt` geschreven. In dit scenario kan `--output` een optioneel **toevoegen**-trefwoord bevatten om een configuratiebestand te maken of toe te voegen aan de bestaande.


```console
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Voer de volgende opdracht in voor meer informatie over gegevensarchiefbestanden, inclusief het gebruik van standaard configuratiebestanden (`@spx.default`, `@default.config`en `@*.default.config` voor opdracht-specifieke standaardinstellingen):

```console
spx help advanced setup
```

## <a name="next-steps"></a>Volgende stappen 

* [Batchbewerkingen met de Spraak-CLI](./spx-batch-operations.md)