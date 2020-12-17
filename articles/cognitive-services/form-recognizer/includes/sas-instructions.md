---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8ad682ccd7feb9c7f089e9cf0f066cb1be8c756c
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97505321"
---
Als u de URL voor de Shared Access Signature (SAS-URL) voor de trainingsgegevens van uw aangepaste model wilt ophalen, gaat u naar uw opslagresource in Azure Portal en selecteert u het tabblad **Storage Explorer**. Ga naar de container, klik er met de rechtermuisknop op en selecteer **Shared Access Signature ophalen**. Het is belangrijk dat u de SAS voor uw container ophaalt, niet voor het opslagaccount zelf. Controleer of de machtigingen **Lezen** en **Lijst** zijn ingeschakeld en klik op **Maken**. Kopieer vervolgens de waarde in de sectie **URL** naar een tijdelijke locatie. Deze moet de notatie `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` hebben.
   > [!div class="mx-imgBorder"]
   > ![alt-text](../media/quickstarts/get-sas-url.png)