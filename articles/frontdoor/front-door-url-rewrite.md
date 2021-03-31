---
title: Voor deur van Azure voor het herschrijven van URL'S | Microsoft Docs
description: Dit artikel helpt u inzicht te krijgen in de manier waarop de URL voor uw routes wordt herschreven door Azure front-deur, indien geconfigureerd.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91445484"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL-herschrijvingen (aangepast doorstuurpad)
Azure front-deur ondersteunt het herschrijven van URL'S door een optioneel **aangepast doorstuur traject** te configureren dat moet worden gebruikt bij het maken van de aanvraag om door te sturen naar de back-end. Als een aangepast forwarding-pad niet wordt verstrekt, kopieert de voor deur standaard het binnenkomende URL-pad naar de URL die in de doorgestuurde aanvraag wordt gebruikt. De hostkoptekst in de doorgestuurde aanvraag is de hostkoptekst zoals deze is geconfigureerd voor de geselecteerde back-end. Lees de [back-end-host-header](front-door-backend-pool.md#hostheader) om te ontdekken wat deze doet en hoe u deze kunt configureren.

Het krachtige deel van het herschrijven van URL'S is dat het aangepaste doorstuur traject elk deel van het binnenkomende pad kopieert dat overeenkomt met een pad naar een Joker teken naar het doorgestuurde pad (deze padsegmenten zijn de **groene** segmenten in het onderstaande voor beeld):
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="URL voor de Azure front-deur herschrijven":::

## <a name="url-rewrite-example"></a>Voor beeld van URL rewrite
Overweeg een regel voor door sturen met de volgende combi natie van frontend-hosts en-paden die zijn geconfigureerd:

| Hosts      | Paden       |
|------------|-------------|
| www- \. contoso.com | /\*   |
|            | /foo        |
|            | Foo\*     |
|            | /foo/bar/\* |

In de eerste kolom van de volgende tabel ziet u voor beelden van binnenkomende aanvragen en de tweede kolom laat zien wat de ' meest specifieke ' overeenkomende route ' Path ' zou zijn.  De derde en de kolommen van de tabel zijn voor beelden van geconfigureerde **aangepaste doorstuur paden**.

Als we bijvoorbeeld over de tweede rij lezen, wordt de melding gegeven dat voor een inkomende aanvraag `www.contoso.com/sub` , als het aangepaste forwarding-pad was `/` , het doorgestuurde pad `/sub` . Als het aangepaste forwarding-pad was `/fwd/` , zou het doorgestuurde pad zijn `/fwd/sub` . Voor de resterende kolommen. De **gemarkeerde** delen van de paden vertegenwoordigen de delen die deel uitmaken van het Joker teken.

| Binnenkomende aanvraag       | Pad naar de meest specifieke overeenkomst | /          | /fwd/          | Foo          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www- \. contoso.com/            | /\*                      | /          | /fwd/          | Foo          | /foo/bar/          |
| www \. contoso.com/-**Sub**     | /\*                      | /**chronische**   | /FWD/**Sub**   | /Foo/**Sub**   | /foo/bar/**Sub**   |
| www \. **-contoso.com/a/b/c**   | /\*                      | /**a/b/c** | /FWD/**a/b/c** | /Foo/**a/b/c** | /foo/bar/**a/b/c** |
| www- \. contoso.com/Foo         | /foo                     | /          | /fwd/          | Foo          | /foo/bar/          |
| www- \. contoso.com/Foo/        | Foo\*                  | /          | /fwd/          | Foo          | /foo/bar/          |
| www- \. contoso.com/Foo/**balk** | Foo\*                  | /**streepje**   | /FWD/-**balk**   | /Foo/-**balk**   | /foo/bar/-**balk**   |

## <a name="optional-settings"></a>Optionele instellingen
Er zijn aanvullende optionele instellingen die u ook kunt opgeven voor de instellingen van een bepaalde routerings regel:

* **Cache configuratie** : als deze functie is uitgeschakeld of niet is opgegeven, worden aanvragen die overeenkomen met deze routerings regel niet in de cache opgeslagen en wordt in plaats daarvan altijd opgehaald van de back-end. Meer informatie over [caching met de voor deur](front-door-caching.md).

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
