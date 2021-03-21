---
title: Levensduur van objecten en resources
description: Verklaart het levens duur beheer voor verschillende typen
author: jakrams
ms.author: jakras
ms.date: 02/06/2020
ms.topic: conceptual
ms.openlocfilehash: 0945b35f7aff8e93a1a3ba23b89db288db3d8efa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593888"
---
# <a name="object-and-resource-lifetime"></a>Levensduur van objecten en resources

Azure remote rendering onderscheidt zich van twee typen: **objecten** en **bronnen**.

## <a name="object-lifetime"></a>Levens duur van object

*Objecten* worden beschouwd als dingen die de gebruiker op eigen goeddunken kan maken, wijzigen en vernietigen. Objecten kunnen vrij worden gedupliceerd en elk exemplaar kan na verloop van tijd mutate. Daarom zijn [entiteiten](entities.md) en [onderdelen](components.md) objecten.

De levens duur van objecten is volledig onder gebruikers beheer. Het is niet gerelateerd aan de levens duur van de client-side-weer gave, maar. Klassen als `Entity` en `Component` hebben een `Destroy` functie die moet worden aangeroepen om het object op de externe rendering-host te detoewijzen. Daarnaast `Entity.Destroy()` worden de entiteit, de onderliggende items en alle onderdelen in die hiërarchie vernietigd.

## <a name="resource-lifetime"></a>Levens duur van resources

*Resources* zijn dingen waarvan de levens duur volledig wordt beheerd door de externe rendering-host. De verwijzingen worden intern geteld. De toewijzing wordt ongedaan gemaakt als niemand meer naar een verwijzing verwijst.

De meeste resources kunnen alleen indirect worden gemaakt, meestal door ze te laden vanuit een bestand. Wanneer hetzelfde bestand meerdere keren wordt geladen, retourneert de externe rendering van Azure dezelfde verwijzing en worden de gegevens niet opnieuw geladen.

Veel resources zijn onveranderbaar, zoals [netten](meshes.md) en [bitmappatronen](textures.md). Sommige resources zijn zo onveranderbaar, maar bijvoorbeeld [materialen](materials.md). Omdat resources vaak worden gedeeld, kan het wijzigen van een resource van invloed zijn op meerdere objecten. Als u bijvoorbeeld de kleur van een materiaal wijzigt, wordt de kleur gewijzigd van alle objecten die gebruikmaken van mazen, die op zijn beurt verwijzen naar dat materiaal.

### <a name="built-in-resources"></a>Ingebouwde resources

Azure remote rendering bevat een aantal ingebouwde resources, die kunnen worden geladen door hun respectieve id in behandeling te nemen `builtin://` tijdens het aanroepen van `RenderingSession.Connection.LoadXYZAsync()` . De beschik bare ingebouwde resources vindt u in de documentatie voor elk van de functies. In het [hoofd stuk](../overview/features/sky.md) worden bijvoorbeeld de ingebouwde lucht texturen vermeld.

## <a name="general-lifetime"></a>Algemene levens duur

De levens duur van alle objecten en resources is gebonden aan de verbinding. Bij verbinding met alles wordt verwijderd. Als u opnieuw verbinding maakt met dezelfde sessie, is de scène grafiek leeg en worden alle resources verwijderd.

In de praktijk is het laden van dezelfde bron in een-sessie doorgaans sneller dan de eerste keer. Dit is het geval omdat de meeste resources van Azure Storage de eerste keer moeten worden gedownload. Dit is niet nodig om de tweede keer veel tijd te besparen.

## <a name="next-steps"></a>Volgende stappen

* [Entiteiten](entities.md)
* [Onderdelen](components.md)
* [Modellen](models.md)
