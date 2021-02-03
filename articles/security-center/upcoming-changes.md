---
title: Belangrijke wijzigingen afkomstig van Azure Security Center
description: Aanstaande wijzigingen in Azure Security Center waarvan u mogelijk op de hoogte moet zijn en waarmee u mogelijk rekening moet houden
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: 8ef0fedcbf7ce06063db22fb345a0711acddcb4d
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526378"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Belangrijke aanstaande wijzigingen aan Azure Security Center

> [!IMPORTANT]
> De informatie op deze pagina heeft betrekking op producten of functies van voorlopige versies, die aanzienlijk kunnen worden gewijzigd voordat ze commercieel worden vrijgegeven, als dat ooit al gebeurt. Microsoft biedt geen verplichtingen of garanties, uitdrukkelijk of impliciet, met betrekking tot de informatie die hier wordt verstrekt.

Op deze pagina vindt u informatie over de wijzigingen die zijn gepland voor Security Center. Hierin worden de geplande wijzigingen in het product beschreven die van invloed kunnen zijn op zaken als uw beveiligde score of werkstromen.

Als u op zoek bent naar de nieuwste opmerkingen bij de release, vindt u deze in [Wat is er nieuw in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplande wijzigingen

- [Twee aanbevelingen van het beveiligings beheer ' systeem updates Toep assen ' worden afgeschaft](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Verbeteringen in aanbeveling voor SQL-gegevens classificatie](#enhancements-to-sql-data-classification-recommendation)

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Twee aanbevelingen van het beveiligings beheer ' systeem updates Toep assen ' worden afgeschaft 

**Geschatte datum voor wijziging:** Februari 2021

De volgende twee aanbevelingen zijn gepland om te worden afgeschaft in februari 2021:

- **Uw computers moeten opnieuw worden opgestart om systeem updates toe te passen**. Dit kan een geringe invloed op uw beveiligde Score opleveren.
- **Bewakings agent moet op uw computers zijn geïnstalleerd**. Deze aanbeveling is gekoppeld aan on-premises machines en sommige logica wordt overgezet naar een andere aanbeveling, **log Analytics problemen met de agent status moeten worden opgelost op uw computers**. Dit kan een geringe invloed op uw beveiligde Score opleveren.

We raden u aan om uw continue export-en werk stroom Automation-configuraties te controleren om te zien of deze aanbevelingen in hen zijn opgenomen. Daarnaast moeten alle Dash boards of andere bewakings hulpprogramma's die deze kunnen gebruiken, dienovereenkomstig worden bijgewerkt.

Meer informatie over deze aanbevelingen vindt u op de [overzichts pagina met aanbevelingen voor beveiliging](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Verbeteringen in aanbeveling voor SQL-gegevens classificatie

**Geschatte datum voor wijziging:** Q2 2021

De gevoelige aanbevelings **gegevens in uw SQL-data bases moeten worden geclassificeerd** in het beveiligings beheer **gegevens classificatie Toep assen** wordt vervangen door een nieuwe versie die beter is afgestemd op de strategie voor gegevens classificatie van micro soft. Als gevolg hiervan wordt de ID van de aanbeveling ook gewijzigd (momenteel b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Volgende stappen

Zie [Wat is er nieuw in Azure Security Center?](release-notes.md) voor alle recente wijzigingen aan het product.
