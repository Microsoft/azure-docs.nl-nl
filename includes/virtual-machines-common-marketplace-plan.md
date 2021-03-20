---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "71174944"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Een installatie kopie implementeren met Marketplace-voor waarden

Sommige VM-installatie kopieën in de Azure Marketplace hebben aanvullende licentie-en aankoop voorwaarden die u moet accepteren voordat u ze via een programma kunt implementeren.  

Als u een virtuele machine van een dergelijke installatie kopie wilt implementeren, moet u de voor waarden van de installatie kopie accepteren en programmatische implementatie inschakelen. U hoeft dit slechts één keer per abonnement te doen. Telkens wanneer u een virtuele machine via een programma vanuit de installatie kopie implementeert, moet u ook de para meters van het *aankoop plan* opgeven.

In de volgende secties ziet u hoe u:

* Nagaan of er aanvullende licentie voorwaarden zijn voor een Marketplace-installatie kopie 
* De termen via een programma accepteren
* De para meters van het aankoop plan opgeven wanneer u een virtuele machine programmatisch implementeert

