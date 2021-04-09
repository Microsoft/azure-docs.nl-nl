---
title: Problemen met Azure Internet Analyzer oplossen | Microsoft Docs
description: De naslag informatie voor probleem oplossing voor Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: troubleshooting
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: fe173ec4d4f28444d43739ea2ed51e43021916b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84744354"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Problemen met Azure Internet Analyzer oplossen

Dit artikel bevat probleemoplossings stappen voor veelvoorkomende problemen met Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Zaken om rekening mee te houden
- Het client script moet worden inge sloten in een **https** -website. Metingen worden niet verzameld als het script wordt uitgevoerd op een **http://**-of lokale (**File://**)-website.
- Meet gegevens worden alleen verzameld als het client script van het Internet Analyzer-profiel is inge sloten in een toepassing die werkelijk gebruikers verkeer ontvangt. Synthetische verkeer (bijvoorbeeld prestatie tests van Azure WebApp) voert doorgaans geen Inge sloten java script-code uit, dus er worden geen metingen gegenereerd door dat type verkeer.

## <a name="azure-portal"></a>Azure Portal
**' Er is geen Score Card gegenereerd voor de geselecteerde filter combinatie ' in de sectie Score cards**
- Score cards worden dagelijks gegenereerd (aan het einde van elke dag, UTC-tijd).
- Score cards worden alleen gegenereerd als er meer dan 100 metingen zijn verzameld voor de geselecteerde filter combinatie (test, tijds periode, land/regio enzovoort).

**"Totaal aantal metingen" is nul voor een of beide eind punten in een test**
- Tijds reeksen en meet aantallen worden één keer per uur berekend. u moet er dus voor zorgen dat er ten minste een hoeveelheid tijd nodig is om nieuwe meet gegevens weer te geven.
- Internet Analyzer omvat alleen geslaagde metingen (zoals HTTP 200-antwoorden) voor de analyse. Als een of beide eind punten in een test onbereikbaar zijn of een niet-200 HTTP-code retour neren, worden ze weer gegeven met een totaal van nul metingen.

## <a name="next-steps"></a>Volgende stappen
Lees de [Veelgestelde vragen over Internet Analyzer](internet-analyzer-faq.md)
