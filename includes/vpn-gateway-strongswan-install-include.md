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
ms.openlocfilehash: f02fa49b62a2e3d617617a20518810209d3879b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026678"
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

[Aanvullende instructies voor het installeren van de Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)