---
title: Een sjabloon plan voor oplossingen configureren
description: Meer informatie over het configureren van een oplossings sjabloon plan voor uw Azure-toepassings aanbieding in Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 8469cad02009d054bd8ba97fb4aabfdae84ef842
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96744615"
---
# <a name="configure-a-solution-template-plan"></a>Een sjabloon plan voor oplossingen configureren

Dit artikel is alleen van toepassing op oplossings sjabloon plannen voor een Azure-toepassings aanbieding. Als u een beheerd toepassings abonnement wilt configureren, gaat u naar [een beheerd toepassings plan configureren](create-new-azure-apps-offer-managed.md).

## <a name="choose-who-can-see-your-plan"></a>Kies wie uw abonnement kan zien

U kunt elk plan zodanig configureren dat het zichtbaar is voor iedereen of alleen voor een specifieke doel groep. U verleent toegang tot een persoonlijke doel groep met behulp van Azure-abonnement-Id's met de optie om een beschrijving op te nemen van elke abonnements-ID die u toewijst. U kunt Maxi maal 10 abonnements-Id's hand matig of Maxi maal 10.000 abonnement-Id's toevoegen met behulp van een. CSV-bestand. Azure-abonnements-Id's worden weer gegeven als GUID'S en letters moeten kleine letters zijn.

> [!NOTE]
> Als u een privé-abonnement publiceert, kunt u de zicht baarheid later wijzigen in openbaar. Wanneer u echter een openbaar abonnement publiceert, kunt u de zicht baarheid niet wijzigen in privé.

Voer op het tabblad **Beschik baarheid** onder **plan zicht baarheid** een van de volgende handelingen uit:

- Als u het plan openbaar wilt maken, selecteert u het **open bare** keuze rondje (ook wel een _keuze rondje_ genoemd).
- Als u het plan privé wilt maken, selecteert u het **persoonlijke** keuze rondje en voegt u de id's van het Azure-abonnement hand matig of met een CSV-bestand toe.

    > [!NOTE]
    > Een persoonlijke of beperkte doel groep wijkt af van de preview-doel groep die u hebt gedefinieerd op het tabblad **Preview** . Een preview-doel groep heeft toegang tot uw aanbieding voordat deze live op Marketplace wordt gepubliceerd. Hoewel de keuze van de privé doelgroep alleen van toepassing is op een specifiek abonnement, kan de preview-doel groep alle plannen (privé of niet) voor validatie doeleinden bekijken.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Hand matig Azure-abonnements-Id's voor een privé-abonnement toevoegen

1. Selecteer onder **plan zicht baarheid** het keuze rondje **persoonlijk** .
1. Voer in het vak ID van het **Azure-abonnement** dat wordt weer gegeven, de Azure-abonnements-id in van de doel groep die u toegang wilt verlenen tot dit privé-abonnement. Er is mini maal één abonnements-ID vereist.
1. Beschrijving Voer een beschrijving van deze doel groep in het vak **Beschrijving** in.
1. Als u nog een abonnements-ID wilt toevoegen, selecteert u de koppeling **id toevoegen (Maxi maal 10)** en herhaalt u stap 2 en 3.

## <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Gebruik een. CSV-bestand voor het toevoegen van Azure-abonnement-Id's voor een privé abonnement

1. Selecteer onder **plan zicht baarheid** het keuze rondje **persoonlijk** .
1. Selecteer de koppeling **doel groep exporteren (CSV)** .
1. Open de. CSV-bestand en voeg de Id's van het Azure-abonnement toe die u toegang wilt verlenen tot de persoonlijke aanbieding aan de **id-** kolom.
1. Voer desgewenst een beschrijving in voor elke doel groep in de kolom **Beschrijving** .
1. Voeg ' SubscriptionId ' toe aan de kolom **type** voor elke rij met een abonnements-id.
1. Sla het op. CSV-bestand.
1. Selecteer op het tabblad **Beschik baarheid** onder **plan zicht baarheid** de koppeling **doel groep importeren (CSV)** .
1. In het dialoog venster dat wordt weer gegeven, selecteert u **Ja**.
1. Selecteer de. CSV-bestand en selecteer **openen**. Er wordt een bericht weer gegeven waarin wordt aangegeven dat de. Het CSV-bestand is geïmporteerd.

### <a name="hide-your-plan"></a>Uw abonnement verbergen

Als uw oplossings sjabloon alleen indirect moet worden geïmplementeerd als er een andere oplossings sjabloon of beheerde toepassing wordt verwezen, schakelt u het selectie vakje onder plan voor het publiceren van uw oplossings sjabloon **verbergen** in, maar deze verbergen bij klanten die zoeken en direct ernaar bladeren.

Selecteer **concept opslaan** voordat u doorgaat naar de volgende sectie: de technische configuratie definiëren.

## <a name="define-the-technical-configuration"></a>De technische configuratie definiëren

Op het tabblad **technische configuratie** uploadt u het implementatie pakket waarmee klanten uw plan kunnen implementeren en een versie nummer voor het pakket kunnen opgeven.

> [!NOTE]
> Dit tabblad wordt niet weer gegeven als u ervoor hebt gekozen om pakketten van een ander abonnement opnieuw te gebruiken op het tabblad **installatie plannen** . Als dit het geval is, gaat u naar [uw plannen weer geven](#view-your-plans).

### <a name="assign-a-version-number-for-the-package"></a>Een versie nummer voor het pakket toewijzen

Geef in het vak **versie** de huidige versie van de technische configuratie op. Verhoog deze versie telkens wanneer u een wijziging op deze pagina publiceert. Het versie nummer moet de volgende indeling hebben: integer. integer. integer. Bijvoorbeeld `1.0.2`.

### <a name="upload-a-package-file"></a>Een pakket bestand uploaden

Sleep het pakket bestand onder **pakket bestand (. zip)** naar het grijze vak of selecteer de koppeling **zoeken naar uw bestand (en)** .

> [!NOTE]
> Als u een probleem hebt met het uploaden van bestanden, moet u ervoor zorgen dat uw lokale netwerk de door `https://upload.xboxlive.com` Partner Center gebruikte service niet blokkeert.

### <a name="previously-published-packages"></a>Eerder gepubliceerde pakketten

Nadat u uw aanbieding Live hebt gepubliceerd, wordt het subtabblad **gepubliceerde pakketten** op de pagina **technische configuratie** weer gegeven. Op dit tabblad worden alle eerder gepubliceerde versies van uw technische configuratie weer gegeven.

## <a name="view-your-plans"></a>Uw plannen weer geven

- Selecteer **concept opslaan** en selecteer in de linkerbovenhoek van de pagina **plan overzicht** om terug te keren naar de **overzichts** pagina van het abonnement.

Nadat u een of meer plannen hebt gemaakt, ziet u de naam van het abonnement, de plan-ID, het plan type, de beschik baarheid (openbaar of privé), de huidige publicatie status en eventuele beschik bare acties op het tabblad Overzicht van het **plan** .

De acties die beschikbaar zijn in de kolom **actie** van het tabblad **plan overzicht** variëren afhankelijk van de status van uw abonnement en kunnen het volgende omvatten:

- Als de plan status **concept** is, wordt in de kolom **actie** **concept verwijderen** weer te zeggen.
- Als de status van het abonnement **Live** is, wordt de koppeling in de kolom **actie** **stoppen** of **privé publiek synchroniseren**. Met de koppeling **persoonlijke doel groep synchroniseren** worden alleen de wijzigingen in uw persoonlijke doel groepen gepubliceerd, zonder dat u andere updates publiceert die u mogelijk hebt gemaakt in de aanbieding.
- Als u een ander abonnement voor deze aanbieding wilt maken, selecteert u **+ nieuw plan maken** bovenaan het tabblad Overzicht van het **plan** . Herhaal de stappen in de [procedure voor het maken van plannen voor uw Azure-toepassings aanbieding](create-new-azure-apps-offer-plans.md). Als u klaar bent met het maken van plannen, gaat u naar de volgende sectie: volgende stappen.

## <a name="next-steps"></a>Volgende stappen

- [Uw Azure-toepassing-aanbieding testen en publiceren](create-new-azure-apps-offer-test-publish.md).
- Meer informatie [over het verkopen van uw Azure-toepassing-aanbod](create-new-azure-apps-offer-marketing.md) via het samen verkopen met micro soft en door verkopen via csp's-Program ma's.
