---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "70737488"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Fout ConditionHeadersNotSupported vanuit een webtoepassing met behulp van Azure Files vanuit de browser

De ConditionHeadersNotSupported-fout treedt op wanneer toegang tot inhoud die wordt gehost in Azure Files via een toepassing die gebruikmaakt van voorwaardelijke kopteksten, zoals een webbrowser, niet kan worden geopend. De fout geeft aan dat de conditie headers niet worden ondersteund.

![Fout met voorwaardelijke koptekst Azure Files](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Oorzaak

Voorwaardelijke kopteksten worden nog niet ondersteund. Toepassingen die ze implementeren, moeten het volledige bestand aanvragen wanneer het bestand wordt geopend.

### <a name="workaround"></a>Tijdelijke oplossing

Wanneer een nieuw bestand wordt geüpload, is de eigenschap Cache-Control standaard ingesteld op ' no-cache '. Om ervoor te zorgen dat de toepassing het bestand elke keer kan aanvragen, moet de eigenschap Cache-Control van het bestand worden bijgewerkt van ' no-cache ' in ' no-cache ', no-Store, moet opnieuw worden gevalideerd '. Dit kan worden bereikt met behulp van [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

![Wijzigingen in de inhoud van de opslag Verkenner voor Azure Files voorwaardelijke kopteksten](media/storage-files-condition-headers/storage-explorer-cache.png)