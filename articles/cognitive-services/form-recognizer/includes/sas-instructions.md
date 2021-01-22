---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 3f4272f5445c8530e97726bd4fdca6bf662719c2
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807804"
---
Als u de URL voor de Shared Access Signature (SAS-URL) voor de trainingsgegevens van uw aangepaste model wilt ophalen, gaat u naar uw opslagresource in Azure Portal en selecteert u het tabblad **Storage Explorer**. Ga naar de container, klik er met de rechtermuisknop op en selecteer **Shared Access Signature ophalen**. Het is belangrijk dat u de SAS voor uw container ophaalt, niet voor het opslagaccount zelf. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de sectie **URL** naar een tijdelijke locatie. Deze moet de notatie `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` hebben.