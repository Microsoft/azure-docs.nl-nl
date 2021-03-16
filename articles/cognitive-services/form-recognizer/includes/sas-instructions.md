---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 12/11/2020
ms.author: lajanuar
ms.openlocfilehash: 8bf78d4c2f703ee10b26b1936620f7cf23ed7c14
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467196"
---
Als u de URL voor de Shared Access Signature (SAS-URL) voor de trainingsgegevens van uw aangepaste model wilt ophalen, gaat u naar uw opslagresource in Azure Portal en selecteert u het tabblad **Storage Explorer**. Ga naar de container, klik er met de rechtermuisknop op en selecteer **Shared Access Signature ophalen**. Het is belangrijk dat u de SAS voor uw container ophaalt, niet voor het opslagaccount zelf. Zorg ervoor dat de machtigingen **lezen**, **schrijven**, **verwijderen** en **lijst** worden geselecteerd en klik op **maken**. Kopieer vervolgens de waarde in de sectie **URL** naar een tijdelijke locatie. Deze moet de notatie `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>` hebben.
