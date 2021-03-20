---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 02ced43f8c3fc7c83359b78362e8ad0feeab3070
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "72168374"
---
>[!NOTE]
>Bij het werken met standaard beleid kan Azure optreden als initiator en responder tijdens het instellen van een IPsec-tunnel. Er is alleen ondersteuning voor Azure als een responder.
>

### <a name="initiator"></a>Initiator

In de volgende secties worden de ondersteunde beleids combinaties vermeld als Azure de initiator is voor de tunnel.

**Fase-1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256 DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256 DH_GROUP_2

**Fase-2**

* GCM_AES_256, GCM_AES_256 PFS_NONE
* AES_256, SHA_1 PFS_NONE
* AES_256, SHA_256 PFS_NONE
* AES_128, SHA_1 PFS_NONE

### <a name="responder"></a>Responder

In de volgende secties worden de ondersteunde beleids combinaties vermeld als Azure de responder voor de tunnel is.

**Fase-1**

* AES_256, SHA1, DH_GROUP_2
* AES_256, SHA_256 DH_GROUP_2
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256 DH_GROUP_2

**Fase-2**

* GCM_AES_256, GCM_AES_256 PFS_NONE
* AES_256, SHA_1 PFS_NONE
* AES_256, SHA_256 PFS_NONE
* AES_128, SHA_1 PFS_NONE
* AES_256, SHA_1 PFS_1
* AES_256, SHA_1 PFS_2
* AES_256, SHA_1 PFS_14
* AES_128, SHA_1 PFS_1
* AES_128, SHA_1 PFS_2
* AES_128, SHA_1 PFS_14
* AES_256, SHA_256 PFS_1
* AES_256, SHA_256 PFS_2
* AES_256, SHA_256 PFS_14
* AES_256, SHA_1 PFS_24
* AES_256, SHA_256 PFS_24
* AES_128, SHA_256 PFS_NONE
* AES_128, SHA_256 PFS_1
* AES_128, SHA_256 PFS_2
* AES_128, SHA_256 PFS_14