---
title: Een bestaande toewijzing bijwerken vanuit de portal
description: Meer informatie over het mechanisme voor het bijwerken van een bestaande blauw druk-toewijzing vanuit de portal in azure-blauw drukken.
ms.date: 08/27/2020
ms.topic: how-to
ms.openlocfilehash: ddaac8110885d817bc96745965c1af7e522b1919
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007185"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Een bestaande blauw druk-toewijzing bijwerken

Wanneer een blauw druk is toegewezen, kan de toewijzing worden bijgewerkt. Er zijn verschillende redenen voor het bijwerken van een bestaande toewijzing, waaronder:

- [Resource vergrendeling](../concepts/resource-locking.md) toevoegen of verwijderen
- De waarde van [dynamische para meters](../concepts/parameters.md#dynamic-parameters) wijzigen
- De toewijzing upgraden naar een nieuwere, **gepubliceerde** versie van de blauw druk

## <a name="updating-assignments"></a>Toewijzingen bijwerken

1. Selecteer **Alle services** in het linkerdeelvenster. Zoek en selecteer **Blauwdrukken**.

1. Selecteer **Toegewezen blauwdrukken** op de pagina aan de linkerkant.

1. Selecteer in de lijst met blauw drukken de toewijzing van de blauw druk. Gebruik vervolgens de knop **toewijzing bijwerken** of klik met de rechter muisknop op de blauw druk toewijzing en selecteer **Update toewijzing**.

   :::image type="content" source="../media/update-existing-assignments/update-assignment.png" alt-text="Scherm afbeelding van de toewijzings pagina blauw drukken met de knop Update toewijzing gemarkeerd." border="false":::

1. De pagina **blauw drukken toewijzen** wordt vooraf gevuld met alle waarden van de oorspronkelijke toewijzing. U kunt de definitie van de **blauw druk**, de **vergrendelings toewijzings** status en de dynamische para meters van de definitie van de blauw druk wijzigen. Selecteer **toewijzen** wanneer u klaar bent met het maken van wijzigingen.

1. Bekijk op de pagina bijgewerkte toewijzings Details de nieuwe status. In dit voor beeld hebben we **vergren deling** toegevoegd aan de toewijzing.

   :::image type="content" source="../media/update-existing-assignments/updated-assignment.png" alt-text="Scherm opname van een bijgewerkte blauw druk-toewijzing waarin de vergrendelings modus is gewijzigd." border="false":::

1. Bekijk details over andere **toewijzings bewerkingen** met behulp van de vervolg keuzelijst. De tabel met **beheerde resources** die worden bijgewerkt op basis van de geselecteerde toewijzings bewerking.

   :::image type="content" source="../media/update-existing-assignments/assignment-operations.png" alt-text="Scherm afbeelding van een bijgewerkte blauw druk-toewijzing met de toewijzings bewerkingen en hun status." border="false":::

## <a name="rules-for-updating-assignments"></a>Regels voor het bijwerken van toewijzingen

De implementatie van de bijgewerkte toewijzingen volgt enkele belang rijke regels. Deze regels bepalen wat er gebeurt met resources die al zijn geïmplementeerd. De aangevraagde wijziging en het type artefact bron dat wordt geïmplementeerd of bijgewerkt, bepalen welke acties worden uitgevoerd.

- Roltoewijzingen
  - Als de rol of de toegewezen rol (gebruiker, groep of app) wordt gewijzigd, wordt een nieuwe roltoewijzing gemaakt. Roltoewijzingen die eerder zijn geïmplementeerd, blijven aanwezig.
- Beleidstoewijzingen
  - Als de para meters van de beleids toewijzing zijn gewijzigd, wordt de bestaande toewijzing bijgewerkt.
  - Als de definitie van de beleids toewijzing is gewijzigd, wordt er een nieuwe beleids toewijzing gemaakt.
    De eerder geïmplementeerde beleids toewijzingen blijven aanwezig.
  - Als het artefact voor beleids toewijzing van de blauw druk wordt verwijderd, blijven geïmplementeerde beleids toewijzingen aanwezig.
- Azure Resource Manager-sjablonen (ARM-sjablonen)
  - De sjabloon wordt als een **put** verwerkt door Resource Manager. Raadpleeg de documentatie voor elke opgenomen resource als elk resource type deze actie anders verwerkt, om de impact van deze actie te bepalen wanneer deze wordt uitgevoerd door blauw drukken.

## <a name="possible-errors-on-updating-assignments"></a>Mogelijke fouten bij het bijwerken van toewijzingen

Wanneer u toewijzingen bijwerkt, kunt u wijzigingen aanbrengen die onderbreekt wanneer ze worden uitgevoerd. Een voor beeld is het wijzigen van de locatie van een resource groep nadat deze al is geïmplementeerd. Alle wijzigingen die door [Resource Manager](../../../azure-resource-manager/management/overview.md) worden ondersteund, kunnen worden gemaakt, maar elke wijziging die zou leiden tot een fout door Resource Manager, leidt ook tot het mislukken van de toewijzing.

Er is geen limiet voor het aantal keren dat een toewijzing kan worden bijgewerkt. Als er een fout optreedt, controleert u de fout en maakt u een andere update voor de toewijzing.  Voor beelden van fout scenario's:

- Een ongeldige para meter
- Een al bestaand object
- Een wijziging die niet wordt ondersteund door Resource Manager

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van een blauwdruk](../concepts/lifecycle.md).
- Meer informatie over hoe u [statische en dynamische parameters](../concepts/parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](../concepts/sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](../concepts/resource-locking.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md).