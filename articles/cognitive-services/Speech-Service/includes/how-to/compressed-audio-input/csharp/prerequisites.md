---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 8c77efe9e3e301573b032d1dc1dd32bb4a5ab1a1
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2021
ms.locfileid: "103417732"
---
Het verwerken van gecomprimeerde audio wordt geïmplementeerd met behulp van [gstreamer](https://gstreamer.freedesktop.org). Om licentie redenen GStreamer binaire bestanden niet worden gecompileerd en gekoppeld aan de spraak-SDK. Ontwikkel aars moeten verschillende afhankelijkheden en invoeg toepassingen installeren, Zie [installatie op Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) of [installaties op Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). GStreamer binaire bestanden moeten zich in het systeempad bevinden, zodat de spraak-SDK de binaire bestanden kan laden tijdens runtime. Als de spraak-SDK bijvoorbeeld in Windows kan zoeken naar `libgstreamer-1.0-0.dll` of `gstreamer-1.0-0.dll` (voor de nieuwste GStreamer) tijdens runtime, betekent dit dat de binaire gstreamer-bestanden zich in het systeempad bevinden.

