---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96018351"
---
Let op de schuifregelaar voor **Waarschijnlijkheidsdrempel** in het linkerdeelvenster van het tabblad **Prestaties**. Dit is het betrouwbaarheidsniveau dat een voorspelling moet hebben om als correct te worden beschouwd (wat betreft de rekennauwkeurigheid en het terughalen). 

Wanneer u voorspellingsaanroepen met een hoge drempelwaarde interpreteert, ziet u dat ze doorgaans resultaten met hoge precisie retourneren, wat ten koste gaat van het terughalen. De gedetecteerde classificaties zijn juist, maar vele worden niet gedetecteerd. Voor een drempelwaarde met een lage waarschijnlijkheid geldt het omgekeerde: de meeste werkelijke classificaties worden gedetecteerd, maar binnen die verzameling komen meer fout-positieven voor. U dient de waarschijnlijkheidsdrempelwaarde dus in te stellen op basis van de specifieke behoeften voor uw project. Later, wanneer u voorspellingen aan de clientzijde ontvangt, dient u dezelfde waarschijnlijkheidsdrempelwaarde te gebruiken als u hier hebt gedaan.