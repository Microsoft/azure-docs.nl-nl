---
title: De status van Azure CDN resources controleren | Microsoft Docs
description: Meer informatie over het controleren van de status van uw Azure CDN-resources met behulp van Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 81c92b6ef5bdf89391364bc238f55200eb3679f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778165"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>De status van Azure CDN-resources bewaken
  
Azure CDN resource Health is een subset van [Azure-resource status](../service-health/resource-health-overview.md).  U kunt Azure resource Health gebruiken om de status van CDN-resources te controleren en richt lijnen te ontvangen om problemen op te lossen.

>[!IMPORTANT] 
>Azure CDN resource Health alleen momenteel accounts voor de status van globale CDN-bezorgings-en API-mogelijkheden.  Azure CDN resource Health verifieert geen individuele CDN-eind punten.
>
>De signalen die feed Azure CDN resource Health kan tot 15 minuten vertraging hebben.

## <a name="how-to-find-azure-cdn-resource-health"></a>Azure CDN resource Health zoeken

1. Blader in het [Azure Portal](https://portal.azure.com)naar uw CDN-profiel.

2. Klik op de knop **instellingen** .

    ![Knop Instellingen](./media/cdn-resource-health/cdn-profile-settings.png)

3. Klik onder *ondersteuning en probleem oplossing* op **resource status**.

    ![CDN-resource status](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>U kunt ook CDN-resources vinden die worden vermeld op de tegel *resource status* op de Blade *Help en ondersteuning* .  U kunt snel toegang krijgen tot *Help en ondersteuning* door te klikken op de omcirkeld **?** in de rechter bovenhoek van de portal.
>
> ![Help en ondersteuning](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN-specifieke berichten

Statussen met betrekking tot Azure CDN resource status vindt u hieronder.

|Bericht | Aanbevolen actie |
|---|---|
|Een of meer CDN-eindpunten zijn mogelijk gestopt, verwijderd of verkeerd geconfigureerd | Een of meer CDN-eindpunten zijn mogelijk gestopt, verwijderd of verkeerd geconfigureerd.|
|De CDN-beheerservice is momenteel niet beschikbaar | Kom hier terug voor status updates. Neem contact op met de ondersteuning als het probleem zich blijft voordoen na de verwachte oplossings tijd.|
|Uw CDN-eindpunten worden mogelijk getroffen door lopende problemen met een aantal van onze CDN-providers | Kom hier terug voor status updates. Gebruik het hulp programma problemen oplossen om te leren hoe u uw oorsprong en CDN-eind punt kunt testen. Neem contact op met de ondersteuning als het probleem zich blijft voordoen na de verwachte oplossings tijd. |
|De wijzigingen in de configuratie van uw CDN-eindpunten ondervinden helaas vertragingen bij de doorgifte | Kom hier terug voor status updates. Neem contact op met de ondersteuning als uw configuratie wijzigingen niet volledig worden doorgevoerd op de verwachte tijd.|
|Er zijn momenteel problemen met het laden van de aanvullende portal | Kom hier terug voor status updates. Neem contact op met de ondersteuning als het probleem zich blijft voordoen na de verwachte oplossings tijd.|
Er zijn momenteel problemen met een aantal van onze CDN-providers | Kom hier terug voor status updates. Neem contact op met de ondersteuning als het probleem zich blijft voordoen na de verwachte oplossings tijd. |

## <a name="next-steps"></a>Volgende stappen

- [Een overzicht van de Azure-resource status lezen](../service-health/resource-health-overview.md)
- [Problemen met CDN-compressie oplossen](./cdn-troubleshoot-compression.md)
- [Problemen met 404-fouten oplossen](./cdn-troubleshoot-endpoint.md)