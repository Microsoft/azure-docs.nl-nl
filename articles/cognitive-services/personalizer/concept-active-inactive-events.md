---
title: Actieve en inactieve gebeurtenissen-persoonlijker
description: In dit artikel wordt het gebruik van actieve en inactieve gebeurtenissen in de Personaler-service besproken.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 7d1044d02ceba1f3d0996b1fe1c8a9a44b31049b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91253646"
---
# <a name="active-and-inactive-events"></a>Actieve en inactieve gebeurtenissen

Een **actieve** gebeurtenis is een aanroep naar positie waar u weet dat u het resultaat wilt weer geven aan de klant en de belonings Score kunt bepalen. Dit is de standaardinstelling.

Een **inactieve** gebeurtenis is een aanroep naar een positie waar u niet zeker weet of de gebruiker de aanbevolen actie zou kunnen zien vanwege bedrijfs logica. Zo kunt u het evenement negeren, zodat Personaler niet wordt getraind met de standaard beloning. Inactieve gebeurtenissen mogen de belonings-API niet aanroepen.

Het is belang rijk dat de learning-lus het werkelijke type gebeurtenis kent. Een inactieve gebeurtenis heeft geen belonings oproep. Een actieve gebeurtenis moet een belonings oproep hebben, maar als de API-aanroep nooit wordt gedaan, wordt de standaard belonings Score toegepast. Wijzig de status van een gebeurtenis van inactief in actief zodra u weet dat dit van invloed is op de gebruikers ervaring.

## <a name="typical-active-events-scenario"></a>Typisch scenario voor actieve gebeurtenissen

Wanneer uw toepassing de Rank API aanroept, ontvangt u de actie, die in de toepassing moet worden weer gegeven in het veld **rewardActionId** .  Vanaf dat moment verwacht het Personaler een belonings aanvraag met een belonings Score die dezelfde gebeurtenis code heeft. De belonings score wordt gebruikt om het model te trainen voor toekomstige positie oproepen. Als er geen belonings oproep wordt ontvangen voor de gebeurtenis-aanvraag, wordt een standaard beloning toegepast. [Standaard beloningen](how-to-settings.md#configure-rewards-for-the-feedback-loop) worden ingesteld op uw persoonlijke resource in de Azure Portal.

## <a name="other-event-type-scenarios"></a>Andere scenario's voor gebeurtenis typen

In sommige scenario's moet de toepassing mogelijk worden opgeroepen voordat deze zelfs weet of het resultaat wordt gebruikt of weer gegeven voor de gebruiker. Dit kan gebeuren in situaties waarin bijvoorbeeld de pagina weergave van gepromoveerde inhoud wordt overschreven door een marketing campagne. Als het resultaat van de positie aanroep nooit is gebruikt en de gebruiker deze nooit zag, stuurt u geen bijbehorende belonings oproep.

Deze scenario's worden meestal uitgevoerd wanneer:

* U prerendert de gebruikers interface die de gebruiker mogelijk niet kan zien.
* Uw toepassing doet voorspellende personalisatie waarbij rang gesprekken worden gedaan met weinig realtime context en de toepassing kan de uitvoer mogelijk niet gebruiken.

In dergelijke gevallen gebruikt u persoonlijke instellingen om de positie aan te roepen en vraagt u de gebeurtenis _inactief_ te zijn. Personaler verwacht geen beloning voor deze gebeurtenis en past geen standaard beloning toe.

Als de toepassing de informatie van de classificatie oproep gebruikt, wordt de gebeurtenis later in de bedrijfs logica _geactiveerd_ . Zodra de gebeurtenis actief is, verwacht de Personaler een gebeurtenis beloning. Als er geen expliciete aanroep wordt gedaan naar de belonings-API, past Personaler een standaard beloning toe.

## <a name="inactive-events"></a>Inactieve gebeurtenissen

Als u de training voor een gebeurtenis wilt uitschakelen, roept u de rang orde aan met behulp van `learningEnabled = False` .

Voor een inactieve gebeurtenis wordt Learning impliciet geactiveerd als u een beloning voor de gebeurtenis-Event stuurt of als u de `activate` API voor die gebeurtenis-activiteit aanroept.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over hoe u de belonings Score kunt bepalen en welke gegevens u moet overwegen](concept-rewards.md).
