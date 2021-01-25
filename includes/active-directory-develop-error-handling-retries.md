---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760678"
---
## <a name="retrying-after-errors-and-exceptions"></a>Opnieuw proberen na fouten en uitzonde ringen

U wordt verwacht uw eigen beleid voor opnieuw proberen te implementeren bij het aanroepen van MSAL. MSAL maakt HTTP-aanroepen naar de Azure AD-service en kan af en toe storingen ondervinden. Het netwerk kan bijvoorbeeld omlaag gaan of de server overbelast is.  

### <a name="http-429"></a>HTTP 429

Wanneer de service token server (STS) is overbelast met te veel aanvragen, wordt HTTP-fout 429 geretourneerd met een hint over hoe lang totdat u het antwoord veld opnieuw kunt proberen `Retry-After` .