---
title: Regels voor het onderdrukken van waarschuwingen gebruiken om onjuiste positieve of andere ongewenste beveiligings waarschuwingen in Azure Security Center te onderdrukken.
description: In dit artikel wordt uitgelegd hoe u de onderdrukkings regels van Azure Security Center gebruikt om ongewenste beveiligings waarschuwingen te verbergen
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 02/17/2021
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 646495597565bbb033ac3adaa15f3754f33e8fd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634159"
---
# <a name="suppress-alerts-from-azure-defender"></a>Waarschuwingen van Azure Defender onderdrukken

Op deze pagina wordt uitgelegd hoe u regels voor het onderdrukken van waarschuwingen kunt gebruiken om foutieve positieve of andere ongewenste beveiligings waarschuwingen van Azure Defender te onderdrukken.

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Releasestatus:|Algemene Beschik baarheid (GA)|
|Prijzen:|Gratis<br>(De meeste beveiligings waarschuwingen zijn alleen beschikbaar met Azure Defender)|
|Vereiste rollen en machtigingen:|**Beveiligings beheerder** en- **eigenaar** kunnen regels maken/verwijderen.<br>**Beveiligings lezer** en **lezer** kunnen regels weer geven.|
|Clouds:|![Ja](./media/icons/yes-icon.png) Commerciële clouds<br>![Ja](./media/icons/yes-icon.png) Nationaal/onafhankelijk (Overheid van de VS, China, andere overheden)|
|||


## <a name="what-are-suppression-rules"></a>Wat zijn onderdrukkings regels?

De verschillende Azure Defender-abonnementen detecteren bedreigingen in elk gebied van uw omgeving en genereren beveiligings waarschuwingen.

Wanneer één waarschuwing niet interessant of relevant is, kunt u deze hand matig verwijderen. U kunt ook de functie onderdrukkings regels gebruiken om in de toekomst automatisch vergelijk bare waarschuwingen te negeren. Normaal gesproken gebruikt u een onderdrukkingsregel voor het volgende:

- Waarschuwingen onderdrukken die u als foutieve positieven hebt geïdentificeerd

- Waarschuwingen onderdrukken die te vaak worden geactiveerd om nuttig te zijn

Met de onderdrukkings regels worden de criteria gedefinieerd waarvoor waarschuwingen automatisch moeten worden genegeerd.

> [!CAUTION]
> Het onderdrukken van beveiligings waarschuwingen vermindert de effectiviteit van de bedreigings beveiliging van Azure Defender. U moet de mogelijke gevolgen van elke onderdrukkings regel zorgvuldig controleren en deze na verloop van tijd controleren.

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="Waarschuwings onderdrukkings regel maken":::

## <a name="create-a-suppression-rule"></a>Onderdrukkingsregel maken

Er zijn een paar manieren waarop u regels kunt maken om ongewenste beveiligingswaarschuwingen te onderdrukken:

- Gebruik Azure Policy om waarschuwingen op het niveau van de beheergroep te onderdrukken
- Als u waarschuwingen op het abonnementsniveau wilt onderdrukken, kunt u de Azure Portal of de REST API gebruiken zoals hieronder wordt uitgelegd

Onderdrukkings regels kunnen alleen waarschuwingen negeren die al zijn geactiveerd voor de geselecteerde abonnementen.

Een regel rechtstreeks in het Azure Portal maken:

1. Van de pagina Beveiligingswaarschuwingen in Security Center:

    - Selecteer de specifieke waarschuwing die u niet meer wilt weer geven en selecteer in het detail venster **actie ondernemen**.

    - Of selecteer de koppeling **onderdrukt regels** boven aan de pagina en selecteer op de pagina onderdrukkings regels de optie **nieuwe onderdrukkings regel maken**:

        ![Nieuwe onderdrukkings regel maken * * knop](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. Voer in het deel venster nieuwe onderdrukkings regel de details van de nieuwe regel in.
    - Uw regel kan de waarschuwing voor **alle resources** negeren, zodat u geen waarschuwingen zoals deze in de toekomst ontvangt.     
    - Uw regel kan de waarschuwing **voor specifieke criteria** negeren: wanneer deze betrekking heeft op een specifiek IP-adres, een proces naam, een gebruikers account, een Azure-resource of een locatie.

    > [!TIP]
    > Als u de pagina nieuwe regel van een specifieke waarschuwing hebt geopend, worden de waarschuwing en het abonnement automatisch geconfigureerd in de nieuwe regel. Als u de koppeling **nieuwe onderdrukkings regel maken** hebt gebruikt, zullen de geselecteerde abonnementen overeenkomen met het huidige filter in de portal.

    [![Deel venster voor het maken van de onderdrukkings regel](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. Voer de details van de regel in:
    - **Naam** : een naam voor de regel. Regelnamen moeten beginnen met een letter of een cijfer, tussen 2 en 50 tekens lang zijn en mogen geen symbolen bevatten behalve streepjes (-) of onderstrepingstekens (_). 
    - **Status** : ingeschakeld of uitgeschakeld.
    - **Reden** : Selecteer een van de ingebouwde redenen of Overig als ze niet voldoen aan uw behoeften.
    - **Verval datum** : een eind datum en-tijd voor de regel. Regels kunnen maximaal zes maanden worden uitgevoerd.
1. U kunt de regel eventueel testen met behulp van de knop **simuleren** om te zien hoeveel waarschuwingen er zouden zijn genegeerd als deze regel actief was.
1. Sla de regel op. 


## <a name="edit-a-suppression-rule"></a>Een onderdrukkings regel bewerken

Als u een regel wilt bewerken die u hebt gemaakt, gebruikt u de pagina onderdrukkings regels.

1. Selecteer op de pagina beveiligings waarschuwingen van Security Center de koppeling **onderdrukt regels** aan de bovenkant van de pagina.
1. De pagina onderdrukkings regels wordt geopend met alle regels voor de geselecteerde abonnementen.

    [![Lijst met onderdrukkings regels](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Als u één regel wilt bewerken, opent u het menu met weglatings tekens (...) voor de regel en selecteert u **bewerken**.
1. Breng de benodigde wijzigingen aan en selecteer **Toep assen**. 

## <a name="delete-a-suppression-rule"></a>Een onderdrukkings regel verwijderen

Als u een of meer regels die u hebt gemaakt, wilt verwijderen, gebruikt u de pagina onderdrukkings regels.

1. Selecteer op de pagina beveiligings waarschuwingen van Security Center de koppeling **onderdrukt regels** aan de bovenkant van de pagina.
1. De pagina onderdrukkings regels wordt geopend met alle regels voor de geselecteerde abonnementen.
1. Als u één regel wilt verwijderen, opent u het menu met weglatings tekens (...) voor de regel en selecteert u **verwijderen**.
1. Als u meerdere regels wilt verwijderen, schakelt u de selectie vakjes in voor de regels die moeten worden verwijderd en selecteert u **verwijderen**.
    ![Een of meer onderdrukkings regels verwijderen](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="create-and-manage-suppression-rules-with-the-api"></a>Onderdrukkings regels maken en beheren met de API

U kunt regels voor het onderdrukken van waarschuwingen maken, weer geven of verwijderen via de REST API van Security Center. 

De relevante HTTP-methoden voor onderdrukkings regels in de REST API zijn:

- **Put**: voor het maken of bijwerken van een onderdrukkings regel in een opgegeven abonnement.

- **Ophalen**:

    - Alle regels weer geven die zijn geconfigureerd voor een opgegeven abonnement. Met deze methode wordt een matrix van de toepasselijke regels geretourneerd.

    - Om de details van een specifieke regel voor een opgegeven abonnement op te halen. Deze methode retourneert één onderdrukkings regel.

    - Om de impact van een onderdrukkings regel nog steeds in de ontwerp fase te simuleren. Deze aanroep geeft aan welke van uw bestaande waarschuwingen zouden zijn genegeerd als de regel actief was.

- **Verwijderen**: Hiermee verwijdert u een bestaande regel (maar wijzigt u de status van waarschuwingen die al zijn genegeerd).

Zie de [API-documentatie](/rest/api/securitycenter/)voor gedetailleerde informatie en voor beelden van gebruik. 


## <a name="next-steps"></a>Volgende stappen

In dit artikel worden de onderdrukkings regels in Azure Security Center beschreven waarmee automatisch ongewenste waarschuwingen worden genegeerd.

Zie de volgende pagina's voor meer informatie over beveiligings waarschuwingen van Azure Defender:

- [Beveiligings waarschuwingen en de Bewaar keten van de bedoeling](alerts-reference.md) : een referentie handleiding voor de beveiligings waarschuwingen die u kunt ontvangen van Azure Defender.