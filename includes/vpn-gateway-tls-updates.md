---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67175859"
---
>[!NOTE]
>Vanaf 1 juli 2018 is ondersteuning voor TLS 1.0 en 1.1 uit Azure VPN Gateway verwijderd. VPN Gateway ondersteunt alleen TLS 1.2. Als u ondersteuning wilt behouden, raadpleegt u de [updates om ondersteuning voor TLS 1.2 in te schakelen](#tls1).

Daarnaast worden de volgende verouderde algoritmen ook vanaf 1 juli 2018 afgeschaft voor TLS:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-7-and-windows-81"></a><a name="tls1"></a>Hoe kan ik ondersteuning voor TLS 1.2 in Windows 7 en Windows 8.1 inschakelen?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
