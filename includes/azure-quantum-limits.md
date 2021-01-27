---
title: bestand opnemen
description: bestand opnemen
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: c312ee63f9f00e4eef726924fc01f2862ba2884f
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920462"
---
### <a name="provider-limits--quota"></a>Limieten voor provider & quotum

De Azure Quantum service ondersteunt zowel de service providers van de eerste als van derden. Leveranciers van derden hebben hun limieten en quota. Gebruikers kunnen aanbiedingen en limieten weer geven in de Azure Portal bij het configureren van providers van derden op de Blade van de provider. 

Hieronder vindt u de gepubliceerde quotum limieten voor de eerste leverancier optimalisatie oplossingen van micro soft. 

#### <a name="learn--develop-sku"></a>Meer informatie & het ontwikkelen van SKU

| Resource | Limiet |
| --- | --- |
| Gelijktijdige taken op basis van CPU | Maxi maal 5 gelijktijdige taken |
| Gelijktijdige taken op basis van FPGA | Maxi maal 2 gelijktijdige taken |
| Op CPU gebaseerde solver-uren | 20 uur per maand  |
| Op FPGA gebaseerde solver-uren | 1 uur per maand  |

Als u de leren & het ontwikkelen van SKU gebruikt, **kunt u geen** verhoging van uw quotum limieten aanvragen. In plaats daarvan moet u overstappen op de SKU prestaties op schaal.

#### <a name="performance-at-scale-sku"></a>SKU voor prestaties op schaal

| Resource | Limiet |
| --- | --- |
| Gelijktijdige taken op basis van CPU | Maxi maal 100 gelijktijdige taken |
| Gelijktijdige taken op basis van FPGA | Maxi maal 10 gelijktijdige taken |
| Oplosser-uren | 50.000 uur per maand  |

Neem contact op met de ondersteuning van Azure als u een limiet wilt verhogen. 

Raadpleeg de [pagina met prijzen voor Azure Quantum](https://aka.ms/AQ/Pricing)voor meer informatie.
Raadpleeg de relevante provider pagina in het Azure Portal voor informatie over aanbiedingen van derden.
