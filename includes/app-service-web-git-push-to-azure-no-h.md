---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8539696f4521a1b4a2f56fe7d2936b45dec26ec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102206007"
---
Voeg, eenmaal terug in het lokale terminalvenster, een externe Azure-instantie toe aan uw lokale Git-opslagplaats. Vervang *\<deploymentLocalGitUrl-from-create-step>* door de URL van de externe Git-instantie die u hebt opgeslagen bij [Een web-app maken](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Push naar de externe Azure-instantie om uw app te implementeren met de volgende opdracht. Wanneer Git Credential Manager u om referenties vraagt, geeft u de referenties op die u hebt gemaakt in **Een implementatiegebruiker configureren**, en niet de referenties die u gebruikt om u aan te melden bij de Azure-portal.

```bash
git push azure master
```

Het kan enkele minuten duren voor deze opdracht is uitgevoerd. De opdracht geeft informatie weer die lijkt op het volgende voorbeeld:
