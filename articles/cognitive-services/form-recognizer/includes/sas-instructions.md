---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 45ac0e74733ade9801a6f3624d6200c594eff698
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623328"
---
Als u de URL voor de Shared Access Signature (SAS-URL) voor de trainingsgegevens van uw aangepaste model wilt ophalen, gaat u naar uw opslagresource in Azure Portal en selecteert u het tabblad **Storage Explorer**. Ga naar de container, klik er met de rechtermuisknop op en selecteer **Shared Access Signature ophalen**. Het is belangrijk dat u de SAS voor uw container ophaalt, niet voor het opslagaccount zelf. Zorg ervoor dat de machtigingen **lezen**, **schrijven**, **verwijderen** en **lijst** worden geselecteerd en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** naar een tijdelijke locatie. Deze moet de notatie `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` hebben.
