---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: amishu
ms.openlocfilehash: b8dda0347e5713ef35705425b54f29a110803488
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821516"
---
Het verwerken van gecomprimeerde audio wordt geïmplementeerd met behulp van [gstreamer](https://gstreamer.freedesktop.org). Om licentie redenen GStreamer binaire bestanden niet worden gecompileerd en gekoppeld aan de spraak-SDK. Ontwikkel aars moeten verschillende afhankelijkheden en invoeg toepassingen installeren, Zie [installatie op Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) of [installaties op Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). GStreamer binaire bestanden moeten zich in het systeempad bevinden, zodat de spraak-SDK de binaire bestanden kan laden tijdens runtime. Als de spraak-SDK bijvoorbeeld in Windows kan worden gevonden `libgstreamer-1.0-0.dll` tijdens runtime, betekent dit dat de binaire gstreamer-bestanden zich in het systeempad bevinden.

