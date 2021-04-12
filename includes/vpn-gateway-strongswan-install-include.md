---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 089ad704a466590a9649107fef245a88d6a4d6e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244631"
---
De volgende configuratie is gebruikt voor de volgende stappen:

- Computer: Ubuntu Server 18,04
- Afhankelijkheden: strongSwan


Gebruik de volgende opdrachten om de vereiste strongSwan-configuratie te installeren:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Gebruik de volgende opdracht om de Azure-opdracht regel interface te installeren:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Aanvullende instructies voor het installeren van de Azure CLI](/cli/azure/install-azure-cli-apt)