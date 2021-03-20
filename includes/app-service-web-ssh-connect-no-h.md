---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91822765"
---
Als u een directe SSH-sessie opent met uw container, moet uw app worden uitgevoerd.

Plak de volgende URL in uw browser en vervang `<app-name>` door de naam van uw app:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Als u nog niet bent geverifieerd moet u zich verifiëren met uw Azure-abonnement om verbinding te maken. Nadat u bent geverifieerd, ziet u een shell in de browser waarin u opdrachten binnen uw container kunt uitvoeren.

![SSH-verbinding](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
