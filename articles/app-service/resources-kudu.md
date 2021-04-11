---
title: Overzicht van de kudu-service
description: Meer informatie over de engine die doorlopende implementatie in App Service en de functies ervan vastmaakt.
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609260"
---
# <a name="kudu-service-overview"></a>Overzicht van de kudu-service

Kudu is de engine achter een aantal functies in [Azure app service](overview.md) gerelateerd aan de implementatie op basis van broncode beheer en andere implementatie methoden, zoals Dropbox en OneDrive-synchronisatie. 

## <a name="access-kudu-for-your-app"></a>Toegang tot kudu voor uw app
Telkens wanneer u een app maakt, maakt App Service een Companion-app die wordt beveiligd door HTTPS. Deze kudu-app is toegankelijk op:

- App niet in geïsoleerde laag: `https://<app-name>.scm.azurewebsites.net`
- App in geïsoleerde laag (App Service Environment): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

Zie [toegang tot de kudu-service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)voor meer informatie.

## <a name="kudu-features"></a>Kudu-functies

Kudu biedt u nuttige informatie over uw App Service-app, zoals:

- App-instellingen
- Verbindingsreeksen
- Omgevingsvariabelen
- Server variabelen
- HTTP-headers

Het bevat ook andere functies, zoals:

- Voer opdrachten uit in de [kudu-console](https://github.com/projectkudu/kudu/wiki/Kudu-console).
- Down load IIS diagnostische dumps of docker-Logboeken.
- IIS-processen en site-uitbrei dingen beheren.
- Voeg implementatie-webhooks voor Windows APS toe.
- De gebruikers interface van de ZIP-implementatie toestaan met `/ZipDeploy` .
- Hiermee worden [aangepaste implementatie scripts](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)gegenereerd.
- Toegang met [rest API](https://github.com/projectkudu/kudu/wiki/REST-API)toestaan.

## <a name="more-resources"></a>Meer resources

Kudu is een [open-source project](https://github.com/projectkudu/kudu)en heeft de bijbehorende documentatie op [kudu wiki](https://github.com/projectkudu/kudu/wiki).

