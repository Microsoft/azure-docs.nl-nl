---
title: Gegevensassets beheren in Azure Data Catalog
description: In het artikel wordt uitgelegd hoe u de zicht baarheid en het eigendom van geregistreerde gegevensassets in Azure Data Catalog kunt beheren.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 9d035417b613c81487c493bde90cdcc461f04412
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017284"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Gegevensassets beheren in Azure Data Catalog
## <a name="introduction"></a>Introductie
Azure Data Catalog is ontworpen voor detectie van gegevens bronnen, zodat u de gegevens bronnen die u nodig hebt om analyses uit te voeren en beslissingen te nemen, eenvoudig kunt ontdekken en begrijpen. Deze detectie mogelijkheden maken de grootste invloed op het moment dat u en andere gebruikers het breedste bereik aan beschik bare gegevens bronnen kunnen vinden en begrijpen. Met deze elementen in dit geval is het standaard gedrag van Data Catalog voor alle geregistreerde gegevens bronnen voor weer gave en detectie door alle catalogus gebruikers.

Data Catalog geeft u geen toegang tot de gegevens zelf. De gegevens toegang wordt bepaald door de eigenaar van de gegevens bron. Met Data Catalog kunt u gegevens bronnen detecteren en de meta gegevens bekijken die gerelateerd zijn aan de bronnen die in de catalogus zijn geregistreerd.

Er kunnen echter situaties zijn waarin gegevens bronnen alleen zichtbaar moeten zijn voor specifieke gebruikers of voor leden van specifieke groepen. In dergelijke scenario's kunnen gebruikers het eigendom van geregistreerde gegevensassets in de catalogus nemen en vervolgens de zicht baarheid van de assets bepalen.

> [!NOTE]
> De functionaliteit die in dit artikel wordt beschreven, is alleen beschikbaar in de Standard-editie van Azure Data Catalog. De gratis editie biedt geen mogelijkheden voor eigendom en het beperken van de zicht baarheid van gegevens elementen.
>
>

## <a name="manage-ownership-of-data-assets"></a>Eigendom van gegevensassets beheren
Standaard hebben gegevensassets die zijn geregistreerd in Data Catalog geen eigendom van de gegevens. Elke gebruiker met machtigingen voor toegang tot de catalogus kan deze assets detecteren en aantekeningen maken. Gebruikers kunnen eigenaar worden van niet-eigendoms gegevensassets en vervolgens de zicht baarheid van de assets beperken.

Wanneer een gegevensasset in Data Catalog eigendom is, kunnen alleen gebruikers die zijn geautoriseerd door de eigen aren de Asset detecteren en de meta gegevens weer geven, en kunnen alleen de eigen aren de Asset uit de catalogus verwijderen.

> [!NOTE]
> Eigendom in Data Catalog is alleen van invloed op de meta gegevens die zijn opgeslagen in de catalogus. Eigendom verleent geen machtigingen voor de onderliggende gegevens bron.
>
>

### <a name="take-ownership"></a>Eigenaar worden
Gebruikers kunnen eigenaar worden van gegevensassets door de optie **eigendom nemen** te selecteren in de portal van Data Catalog. Er zijn geen speciale machtigingen vereist om eigenaar te worden van een niet-eigendom gegevens Asset. Elke gebruiker kan eigenaar worden van een niet-eigendom gegevens Asset.

### <a name="add-owners-and-co-owners"></a>Eigen aren en mede-eigen aars toevoegen
Als een gegevensasset al eigendom is, kunnen andere gebruikers niet gewoon eigenaar worden. Ze moeten worden toegevoegd als mede-eigen aren door een bestaande eigenaar. Elke eigenaar kan aanvullende gebruikers of beveiligings groepen toevoegen als mede-eigen aars.

> [!NOTE]
> Het is een best practice om ten minste twee personen als eigen aren te hebben voor de gegevens activa van de eigenaar.
>
>

### <a name="remove-owners"></a>Eigen aars verwijderen
Net zoals een eigenaar van een activum mede-eigen aars kan toevoegen, kan elke eigenaar van het activum elke mede-eigenaar verwijderen.

Een eigenaar van een activum die zichzelf verwijdert als eigenaar, kan de Asset niet meer beheren. Als de eigenaar van de activa zichzelf verwijdert als eigenaar en er geen andere mede-eigen aars zijn, wordt de Asset opnieuw ingesteld op een niet-eigendoms status.

## <a name="control-visibility"></a>Zicht baarheid bepalen
Eigen aren van gegevens elementen kunnen de zicht baarheid van de gegevensassets bepalen. Als u de zicht baarheid wilt beperken als de standaard, waarbij alle Data Catalog gebruikers de gegevensasset kunnen detecteren en weer geven, kan de eigenaar van het activum de zichtbaarheids instelling van **iedereen** naar **eigen aren & deze gebruikers** in de eigenschappen voor de asset in-of uitschakelen. Eigen aren kunnen vervolgens specifieke gebruikers en beveiligings groepen toevoegen.

> [!NOTE]
> Als dat mogelijk is, moeten machtigingen voor eigendom en zicht baarheid van assets worden toegewezen aan beveiligings groepen en niet aan afzonderlijke gebruikers.
>
>

## <a name="catalog-administrators"></a>Catalogus beheerders
Data Catalog beheerders zijn impliciet mede-eigen aren van alle assets in de catalogus. Eigen aars van activa kunnen geen zicht baarheid van beheerders verwijderen en beheerders kunnen het eigendom en de zicht baarheid voor alle gegevensassets in de catalogus beheren.

## <a name="summary"></a>Samenvatting
Met het Data Catalog crowdsourcing-model naar meta gegevens en gegevens Asset Discovery kunnen alle catalogus gebruikers bijdragen en detectie. De Standard-editie van Data Catalog is ontworpen voor eigendom en beheer om de zicht baarheid en het gebruik van specifieke gegevensassets te beperken.
