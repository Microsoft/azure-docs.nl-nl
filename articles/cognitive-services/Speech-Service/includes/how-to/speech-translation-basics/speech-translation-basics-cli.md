---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a5796dfb066ea8ac7c59c02b29ef67b184c67edb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94425567"
---
Een van de belangrijkste functies van de speech-service is de mogelijkheid om menselijke spraak te herkennen en te vertalen naar andere talen. In deze Snelstartgids leert u hoe u de Speech SDK in uw apps en producten kunt gebruiken om spraak vertalingen van hoge kwaliteit uit te voeren. Deze Quick Start zet spraak van de microfoon om in tekst in een andere taal.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Bron-en doel taal instellen

Met deze opdracht wordt een spraak-CLI aangeroepen om spraak van de microfoon van Italiaans naar Frans te vertalen.

```shell
 spx translate --microphone --source it-IT --target fr
```