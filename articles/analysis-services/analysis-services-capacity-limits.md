---
title: Limieten voor resources en objecten Azure Analysis Services | Microsoft Docs
description: In dit artikel worden de limieten voor bronnen en objecten voor een Azure Analysis Services-server beschreven.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c2caab4e449f4299d00fff14b697887ec00f35e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83697489"
---
# <a name="analysis-services-resource-and-object-limits"></a>Limieten voor resources en objecten Analysis Services

In dit artikel worden de limieten voor resource-en model objecten beschreven.

## <a name="tier-limits"></a>Laag limieten

Zie de [pagina met Azure Analysis Services prijzen](https://azure.microsoft.com/pricing/details/analysis-services/)voor QPU-en geheugen limieten voor de lagen ontwikkelaar, basis en standaard.

## <a name="object-limits"></a>Object limieten

Deze limieten zijn theoretisch. Prestaties worden verminderd bij lagere getallen.

|Object|Maximale grootte/aantal|  
|------------|----------------------------|  
|Data bases in een exemplaar|16.000|  
|Gecombineerd aantal tabellen en kolommen in een Data Base|16.000|  
|Rijen in een tabel|Onbeperkt<br /><br /> **Waarschuwing:** Met de beperking dat geen enkele kolom in de tabel meer dan 1.999.999.997 afzonderlijke waarden kan hebben.|  
|Hiërarchieën in een tabel|15.999|  
|Niveaus in een hiërarchie|15.999|  
|Relaties|8,000|  
|Sleutel kolommen in alle tabellen|15.999|  
|Metingen in tabellen|2 ^ 31-1 = 2.147.483.647|  
|Cellen die door een query zijn geretourneerd|2 ^ 31-1 = 2.147.483.647|  
|Record grootte van de bron query|64 K|  
|Lengte van object namen|512 tekens|  


