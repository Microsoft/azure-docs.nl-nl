---
title: Resources en quota's beheren
titleSuffix: Azure Purview
description: Meer informatie over de quota's en limieten voor resources voor Azure controle sfeer liggen en hoe u quotum verhogingen kunt aanvragen.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 3b0a413db304b4f9d2c50a3d221c480f1e9dc37a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552532"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Quota's voor resources beheren en verg Roten met Azure controle sfeer liggen
 
Azure controle sfeer liggen is een Cloud service die door gegevens gebruikers kan worden gebruikt. U gebruikt Azure controle sfeer liggen voor het centraal beheren van gegevens beheer over uw gegevens en het gebruik van Cloud-en on-premises omgevingen. Met de service kunnen bedrijfs analisten zoeken naar relevante gegevens met behulp van zinvolle zakelijke voor waarden. Neem contact op met de ondersteuning als u de limieten wilt verhogen tot de maximumwaarde voor uw abonnement.
 
## <a name="azure-purview-limits"></a>Limieten voor Azure controle sfeer liggen
 
|**Resource**|  **Standaard limiet**  |**Maximum limiet**|
|---|---|---|
|Controle sfeer liggen accounts per Tenant (alle abonnementen gecombineerd)|3|Contact met ondersteuning|
|vCores beschikbaar voor scannen, per account *|160|160|
|Gelijktijdige scans, per account op een bepaald moment. De limiet is gebaseerd op het type gescande gegevens bronnen *|5 | 10 |
|Maximale tijd waarop een scan kan worden uitgevoerd|7 dagen|7 dagen|
|API-aanroepen, per account|10 miljoen Api's per maand voor de platform grootte van 4 capaciteits eenheden. <br>40M Api's/maand voor 16-capaciteits eenheden platform grootte |10 miljoen Api's per maand voor de platform grootte van 4 capaciteits eenheden. <br>40M Api's/maand voor 16-capaciteits eenheden platform grootte|
|Opslag, per account|10 GB voor de platform grootte van 4 capaciteits eenheden. <br>40 GB voor de platform grootte van 16 capaciteits eenheden |10 GB voor de platform grootte van 4 capaciteits eenheden. <br> 40 GB voor de platform grootte van 16 capaciteits eenheden |
|Grootte van assets per account|100 miljoen fysieke activa |Contact met ondersteuning|
|Maximale grootte van een asset in een catalogus|2 MB|2 MB|
|Maximale lengte van de naam van een Asset en classificatie|4 kB|4 kB|
|Maximale lengte van de naam en waarde van de Asset-eigenschap|32 kB|32 kB|
|Maximale lengte van de naam en waarde van het classificatie kenmerk|32 kB|32 kB|
|Maximum aantal woordenlijst termen, per account|100K|100K|
 
* Scenario's voor zelf-hostende integratie-runtime vallen buiten het bereik van de limieten die in de bovenstaande tabel zijn gedefinieerd. 
 
## <a name="next-steps"></a>Volgende stappen
 
> [!div class="nextstepaction"]
>[Zelf studie: gegevens scannen met Azure controle sfeer liggen](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Zelf studie: door de start pagina navigeren en naar een Asset zoeken](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Zelf studie: door assets te bladeren en hun afkomst weer te geven](tutorial-browse-and-view-lineage.md)
