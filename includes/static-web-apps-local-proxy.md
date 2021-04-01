---
author: burkeholland
ms.service: static-web-apps
ms.topic: include
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 879d0c2e8c4cd66ce04c0298d00f7d6a1987acf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83597231"
---
#### <a name="local-proxy"></a>Lokale proxy

U kunt een proxy configureren voor de Visual Studio code-extensie van Live server waarmee alle aanvragen naar `/api` het uitgevoerde API-eind punt worden doorgestuurd op `http://127.0.0.1:7071/api` .

1. Open het bestand _. vscode/settings.js_ .

1. Voeg de volgende instellingen toe om een proxy voor live server op te geven.

   ```json
   "liveServer.settings.proxy": {
      "enable": true,
      "baseUri": "/api",
      "proxyUri": "http://127.0.0.1:7071/api"
   }
   ```

   Deze configuratie kan het beste worden opgeslagen in het bestand met Project instellingen, in plaats van in het bestand met gebruikers instellingen.

   Het gebruik van project instellingen zorgt ervoor dat de proxy niet wordt toegepast op alle andere projecten die zijn geopend in Visual Studio code.
