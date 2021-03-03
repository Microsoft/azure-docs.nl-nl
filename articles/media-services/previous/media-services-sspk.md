---
title: Licenties voor micro soft &reg; Smooth streaming client Porting kit
description: Meer informatie over licenties voor de micro soft &reg; Smooth streaming client Porting kit.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2019
ms.author: xpouyat
ms.openlocfilehash: f6c43f943bb12e8230772f2d195589ecc4543030
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705310"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Licenties voor micro soft &reg; Smooth streaming client Porting kit

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
## <a name="overview"></a>Overzicht
Micro soft Smooth Streaming client Porting kit (**SSPK** for Short) is een Smooth streaming-client implementatie die is geoptimaliseerd voor het helpen van Inge sloten hardwarefabrikanten, kabel en mobiele Opera Tors, aanbieders van leveranciers, leveranciers van Hoorn, onafhankelijke software leveranciers (isv's) en oplossingen voor het maken van adaptieve inhoud in Smooth streaming indeling. SSPK is een apparaat-en platform onafhankelijke implementatie van Smooth Streaming-client die door de licentie kan worden getransporteerd naar elk apparaat en platform. 

Hieronder vindt u een architectuur op hoog niveau en het vak IIS Smooth Streaming Porting kit is de Smooth Streaming-client implementatie die door micro soft wordt geleverd en bevat alle hoofd logica voor het afspelen van Smooth Streaming inhoud. Deze inhoud wordt vervolgens getransporteerd door partners voor een specifiek apparaat of platform door de juiste interfaces te implementeren. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Beschrijving
SSPK wordt in licentie gegeven voor termen die uitstekende bedrijfs waarde bieden. SSPK-licentie biedt de branche de volgende mogelijkheden:

* Smooth Streaming Porting kit-bron in C++ 
  * implementeert Smooth Streaming-client functionaliteit
  * voegt indelings parsering, heuristiek, buffer logica, enzovoort toe.
* Api's voor de Player-toepassing 
  * programmeer interfaces voor interactie met een Media Player-toepassing
* Interface voor de PAL-laag (platform Abstraction Layer) 
  * programmeer interfaces voor interactie met het besturings systeem (threads, sockets)
* HAL-interface (Hardware Abstraction Layer) 
  * programmeer interfaces voor interactie met hardware A/V-decoders (decoderen, rendering)
* Digital Rights Management (DRM)-interface 
  * programmeer interfaces voor het verwerken van DRM via de DRM Abstraction Layer (DAL)
  * Micro soft PlayReady Porting kit wordt afzonderlijk verzonden, maar via deze interface geïntegreerd. [Bekijk meer informatie over micro soft PlayReady-apparaat-licenties](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Implementatie voorbeelden 
  * voor beeld PAL-implementatie voor Linux
  * voor beeld van een HAL-implementatie voor GStreamer

## <a name="licensing-options"></a>Licentie opties
Micro soft Smooth Streaming client Porting kit wordt beschikbaar gesteld voor licentie verlening onder twee verschillende licentie overeenkomsten: één voor het ontwikkelen van Smooth Streaming client interim-producten en een andere voor het distribueren van eind producten van Smooth Streaming client naar eind gebruikers.

* Voor chipset-fabrikanten, systeem integrators of onafhankelijke software leveranciers (Isv's) die een bron code-kit nodig hebben voor het ontwikkelen van interim-producten, moet een micro soft Smooth Streaming client porting Kit **interim-product licentie** worden uitgevoerd.
* Voor fabrikanten van apparaten of Isv's die distributie rechten nodig hebben voor de eind gebruikers van Smooth Streaming client, moet de micro soft Smooth Streaming client porting Kit- **product licentie** worden uitgevoerd.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Interim product licentie voor micro soft Smooth Streaming client Porting kit
Onder deze licentie biedt micro soft een Smooth Streaming client Porting kit en de benodigde intellectuele eigendoms rechten voor het ontwikkelen en distribueren van Smooth Streaming client interim-producten naar andere Smooth Streaming client Porting kit-apparaatlicentie waarmee Smooth Streaming client eind producten worden gedistribueerd.

#### <a name="fee-structure"></a>Kosten structuur
Een Amerikaanse versie van $50.000 licentie kosten biedt toegang tot de Smooth Streaming client Porting kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Micro soft Smooth Streaming client Porting kit-eind product licentie
Onder deze licentie biedt micro soft alle benodigde intellectuele eigendoms rechten voor het ontvangen van Smooth Streaming client interim-producten van andere Smooth Streaming-licenties voor client poort, en voor het distribueren van bedrijfs eigen Smooth Streaming client eind producten aan eind gebruikers.

#### <a name="fee-structure"></a>Kosten structuur
Het eind product van de Smooth Streaming-client wordt aangeboden onder een royalty-model zoals in:

* $0,10 per apparaat-implementatie verzonden
* De royalty wordt elk jaar gelimiteerd op $50.000
* Geen royalty voor eerste 10.000-apparaat-implementaties per jaar 

## <a name="licensing-procedure-and-sspk-access"></a>Licentie procedure en SSPK-toegang
E-mail [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) voor alle licentie query's.

De SSPK-distributie Portal is toegankelijk voor geregistreerde interim-licenties.

Interim-en definitieve SSPK-licenties kunnen technische vragen verzenden naar [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com) .

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Licentie voor micro soft Smooth Streaming client interim-product overeenkomst

* Adroit Business Solutions, Inc
* Advanced Digital Broadcast SA
* Kablosuz Iletism Sanayive dis Ticaret W.S.
* Albis Technologies Ltd.
* Alticast Corporation
* Amazon Digital Services, Inc.
* Arion Technology, Inc.
* AVC multi media-software co., Ltd.
* Cavium, Inc.
* EchoStar-aankoop maatschappij
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Shikun elektronica., Ltd.
* HANDAN BroadInfoCom co., Ltd.
* Infomir GMBH
* Irdeto USA Inc.
* Vrijheid wereld wijde Services BV
* MediaTek Inc.
* MStar Co, Ltd
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Saffron digitaal beperkt
* Sichuan Changhong Electric Co., Ltd
* SoftAtHome
* Sony Corporation
* Tatung Technology Inc.
* Belangrijkste Victory-investeringen, Ltd.
* Vestel Elektronik Sanayi ve Ticaret W.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Licenties voor micro soft Smooth Streaming-client eindproduct overeenkomst
* Advanced Digital Broadcast SA
* Kablosuz Iletism Sanayive dis Ticaret W.S.
* AmTRAN Technology Co., Ltd 
* Arcadyan Technology Corporation
* Arcelik A. S
* Comp-elektronica, Inc.
* EXPRESSIS-TECHNOLOGIE BEPERKT
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Hisense International Co., Ltd.
* Hisense Visual Technology Co., Ltd
* HKC Corporation, beperkt
* Hongkong Konka Ltd
* Innolux Corporation
* Jinpin Electric Company Ltd. Zhuhai. S. E. Z
* K-Tronics (Suzhou) Technology Co., Ltd. 
* Kaonmedia co., Ltd.
* KDDI Corporation
* Mega Fame Electronics Co. Limited
* MIRC-elektronica beperkt
* MOKA INTERNATIONAAL BEPERKT
* Nintendo Co., Ltd.
* ONEPLUS ELECTRONICS (SHENZHEN) CO., LTD.
* Panasonic Corporation
* Qingdao Haier Optronics co., Ltd.
* Shenzhen ATEKO PHOTO Electric Co., Ltd.
* Shenzhen Chuangwei-RGB co., Ltd.
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen KTC Technology Co., Ltd. 
* Shenzhen Maxmade Technology Co., Ltd
* Shenzhen MTC co., Ltd
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* Skardin industrieel Corp.
* Lucht CP Ltd
* Deutschland Fernsehen GmbH & co. KG
* SMARDTV GLOBAL SAS
* SoftAtHome
* Sony Corporation
* Technicolor-bezorgings technologieën, SAS
* Belangrijkste Victory-investeringen, Ltd.
* UMC Polen SP. z. o.o.
* Vizio, Inc.
* ZTE Corporation

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

