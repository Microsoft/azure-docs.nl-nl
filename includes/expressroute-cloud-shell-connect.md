---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67175768"
---
 Als u Azure Cloud Shell gebruikt, wordt u automatisch aangemeld bij uw Azure-account nadat u op Probeer het nu hebt geklikt. Als u zich lokaal wil aanmelden, opent u de PowerShell-console met verhoogde rechten en voert u de cmdlet uit om verbinding te maken.

```azurepowershell
Connect-AzAccount
```

Als u meer dan één abonnement hebt, haalt u een lijst met uw abonnementen op.

```azurepowershell-interactive
Get-AzSubscription
```

Geef het abonnement op dat u wilt gebruiken.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```