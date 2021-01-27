---
title: Inzicht in de levens cyclus van een blauw druk
description: Meer informatie over de levens cyclus van de definitie van een blauw druk en informatie over elke fase, inclusief het bijwerken en verwijderen van blauw druk-toewijzingen.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 721a198b5732af01c6712e86bc0c8e8ef543b404
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918514"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Inzicht in de levens cyclus van een Azure Blueprint

Net als bij veel resources binnen Azure heeft een blauw druk in azure-blauw drukken een typische en natuurlijke levens cyclus. Ze worden gemaakt, geïmplementeerd en definitief verwijderd wanneer ze niet meer nodig of relevant zijn. Azure-blauw drukken ondersteunt standaard levenscyclus bewerkingen. Vervolgens wordt er een build gemaakt om aanvullende status niveaus te bieden voor de ondersteuning van gang bare, continue integratie en doorlopende implementatie pijplijnen voor organisaties die hun infra structuur als code beheren: een belang rijk element in DevOps.

We bieden een standaard levenscyclus voor het volledig begrijpen van een blauw druk en de stadia:

> [!div class="checklist"]
> - Een blauw druk maken en bewerken
> - De blauw druk publiceren
> - Een nieuwe versie van de blauw druk maken en bewerken
> - Een nieuwe versie van de blauw druk publiceren
> - Een specifieke versie van de blauw druk verwijderen
> - De blauw druk verwijderen

## <a name="creating-and-editing-a-blueprint"></a>Een blauw druk maken en bewerken

Wanneer u een blauw druk maakt, voegt u er artefacten aan toe, slaat u deze op in een beheer groep of abonnement en hebt u een unieke naam en een unieke versie gegeven. De blauw druk is nu in een **concept** modus en kan nog niet worden toegewezen. In de **concept** modus kan het blijven worden bijgewerkt en gewijzigd.

Een niet-gepubliceerde blauw druk in de **concept** modus bevat een ander pictogram op de pagina met sjablonen voor **blauw** drukken dan de pagina's die zijn **gepubliceerd**. De **meest recente versie** wordt weer gegeven als **concept** voor deze nooit gepubliceerde blauw drukken.

Een blauw druk maken en bewerken met de [Azure Portal](../create-blueprint-portal.md#create-a-blueprint) of [rest API](../create-blueprint-rest-api.md#create-a-blueprint).

## <a name="publishing-a-blueprint"></a>Een blauw druk publiceren

Zodra alle geplande wijzigingen zijn aangebracht in een blauw druk in de **concept** modus, kan deze worden **gepubliceerd** en beschikbaar worden gemaakt voor toewijzing. De **gepubliceerde** versie van de blauw druk kan niet worden gewijzigd. Na **publicatie** wordt de blauw druk weer gegeven met een ander pictogram dan **concept** blauw drukken en wordt het gegeven versie nummer weer gegeven in de kolom **meest recente versie** .

Een blauw druk publiceren met de [Azure Portal](../create-blueprint-portal.md#publish-a-blueprint) of [rest API](../create-blueprint-rest-api.md#publish-a-blueprint).

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>Een nieuwe versie van de blauw druk maken en bewerken

Een **gepubliceerde** versie van een blauw druk kan niet worden gewijzigd. Een nieuwe versie van de blauw druk kan echter worden toegevoegd aan de bestaande blauw druk en zo nodig worden gewijzigd. Wijzigingen aanbrengen in een bestaande blauw druk door deze te bewerken. Wanneer de nieuwe wijzigingen worden opgeslagen, heeft de blauw druk nu niet- **gepubliceerde wijzigingen**. Deze wijzigingen zijn een nieuwe **concept** versie van de blauw druk.

Een blauw druk bewerken met de [Azure Portal](../create-blueprint-portal.md#edit-a-blueprint).

## <a name="publishing-a-new-version-of-the-blueprint"></a>Een nieuwe versie van de blauw druk publiceren

Elke bewerkte versie van een blauw druk moet worden **gepubliceerd** voordat deze kan worden toegewezen. Wanneer niet- **gepubliceerde wijzigingen** zijn aangebracht in een blauw druk, maar niet zijn **gepubliceerd**, is de knop **blauw** drukken beschikbaar op de pagina blauw drukken bewerken. Als de knop niet zichtbaar is, is de blauw druk al **gepubliceerd** en heeft deze geen niet- **gepubliceerde wijzigingen**.

> [!NOTE]
> Eén blauw druk kan meerdere **gepubliceerde** versies hebben die elk aan abonnementen kunnen worden toegewezen.

Als u een blauw druk wilt publiceren met niet- **gepubliceerde wijzigingen**, gebruikt u dezelfde stappen voor het publiceren van een nieuwe blauw druk.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>Een specifieke versie van de blauw druk verwijderen

Elke versie van een blauw druk is een uniek object dat afzonderlijk kan worden **gepubliceerd**. Als zodanig kan elke versie van een blauw druk ook worden verwijderd. Het verwijderen van een versie van een blauw druk heeft geen invloed op andere versies van die blauw drukken.

> [!NOTE]
> Het is niet mogelijk om een blauw druk met actieve toewijzingen te verwijderen. Verwijder eerst de toewijzingen en verwijder vervolgens de versie die u wilt verwijderen.

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer op de pagina links **blauw** drukken en gebruik de filter opties om de blauw druk te vinden waarvan u een versie van wilt verwijderen. Selecteer deze optie om de pagina bewerken te openen.

1. Selecteer het tabblad **gepubliceerde versies** en zoek de versie die u wilt verwijderen.

1. Klik met de rechter muisknop op de versie die u wilt verwijderen en selecteer **deze versie verwijderen**.

## <a name="deleting-the-blueprint"></a>De blauw druk verwijderen

De basis blauw druk kan ook worden verwijderd. Als u de primaire blauw druk verwijdert, worden ook alle blauw drukken-versies van deze blauw druk verwijderd, inclusief het **concept** en de **publicatie** . Net als bij het verwijderen van een versie van een blauw druk wordt met het verwijderen van de primaire blauw druk de bestaande toewijzingen van de versies van de blauw druk niet verwijderd.

> [!NOTE]
> Het is niet mogelijk om een blauw druk met actieve toewijzingen te verwijderen. Verwijder eerst de toewijzingen en verwijder vervolgens de versie die u wilt verwijderen.

Een blauw druk verwijderen met de [Azure Portal](../create-blueprint-portal.md#delete-a-blueprint) of [rest API](../create-blueprint-rest-api.md#delete-a-blueprint).

## <a name="assignments"></a>Toewijzingen

Er zijn verschillende punten tijdens de levens cyclus die een blauw druk kan worden toegewezen aan een abonnement. Wanneer de modus van een blauw druk wordt **gepubliceerd**, kan die versie worden toegewezen aan een abonnement. Met deze levens cyclus kunnen versies van een blauw druk worden gebruikt en actief worden toegewezen terwijl er een nieuwere versie wordt ontwikkeld.

Als versies van blauw drukken worden toegewezen, is het belang rijk om te begrijpen waar ze zijn toegewezen en met welke para meters ze zijn toegewezen. De para meters kunnen statisch of dynamisch zijn. Zie [statische en dynamische para meters](./parameters.md)voor meer informatie.

### <a name="updating-assignments"></a>Toewijzingen bijwerken

Wanneer een blauw druk is toegewezen, kan de toewijzing worden bijgewerkt. Er zijn verschillende redenen voor het bijwerken van een bestaande toewijzing, waaronder:

- [Resource vergrendeling](./resource-locking.md) toevoegen of verwijderen
- De waarde van [dynamische para meters](./parameters.md#dynamic-parameters) wijzigen
- De toewijzing upgraden naar een nieuwere, **gepubliceerde** versie van de blauw druk

Zie [bestaande toewijzingen bijwerken](../how-to/update-existing-assignments.md)voor meer informatie.

### <a name="unassigning-assignments"></a>Toewijzing van toewijzingen opheffen

Als de blauw druk niet meer nodig is, kan deze niet meer worden toegewezen aan de beheer groep of het abonnement. Tijdens de niet-toewijzing van de blauw druk gebeurt het volgende:

- Verwijderen van [blauw druk-resource vergrendeling](./resource-locking.md)
- Het verwijderen van de blauw druk toewijzings object
- Waarden Als een door het **systeem toegewezen beheerde identiteit** is gebruikt, wordt deze ook verwijderd

> [!NOTE]
> Alle resources die worden geïmplementeerd door de blauw druk-toewijzing, blijven aanwezig, maar worden niet langer beveiligd door Azure-blauw drukken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe u [statische en dynamische parameters](./parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](./sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](./resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md).