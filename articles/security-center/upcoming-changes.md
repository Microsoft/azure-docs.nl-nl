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
ms.openlocfilehash: 83c1d8ac316703d9d22d0716c86c5731c3db7133
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226468"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Belangrijke aanstaande wijzigingen aan Azure Security Center

> [!IMPORTANT]
> De informatie op deze pagina heeft betrekking op producten of functies van voorlopige versies, die aanzienlijk kunnen worden gewijzigd voordat ze commercieel worden vrijgegeven, als dat ooit al gebeurt. Microsoft biedt geen verplichtingen of garanties, uitdrukkelijk of impliciet, met betrekking tot de informatie die hier wordt verstrekt.

Op deze pagina vindt u informatie over de wijzigingen die zijn gepland voor Security Center. Hierin worden de geplande wijzigingen in het product beschreven die van invloed kunnen zijn op zaken als uw beveiligde score of werkstromen.

Als u op zoek bent naar de nieuwste opmerkingen bij de release, vindt u deze in [Wat is er nieuw in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplande wijzigingen

- [De aanbevelingen voor de beveiliging van Kubernetes-workloads worden binnenkort uitgebracht voor algemene Beschik baarheid (GA)](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [Twee aanbevelingen van het beveiligings beheer ' systeem updates Toep assen ' worden afgeschaft](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Verbeteringen in aanbeveling voor SQL-gegevens classificatie](#enhancements-to-sql-data-classification-recommendation)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>De aanbevelingen voor de beveiliging van Kubernetes-workloads worden binnenkort uitgebracht voor algemene Beschik baarheid (GA)

**Geschatte datum van wijziging:** Januari 2021

De aanbevelingen voor de beveiliging van Kubernetes-workloads die worden beschreven in [uw Kubernetes-workloads beveiligen](kubernetes-workload-protections.md) , zijn momenteel beschikbaar als preview-versie. Hoewel een aanbeveling een preview-versie is, wordt er geen resource weer gegeven en is deze niet opgenomen in de berekeningen van uw beveiligde Score.

Deze aanbevelingen worden binnenkort uitgebracht voor algemene Beschik baarheid (GA *), en worden dus opgenomen* in de score berekening. Als u deze nog niet hebt doorgevoerd, kan dit leiden tot een geringe invloed op uw beveiligde Score.

Herstel ze waar mogelijk (meer informatie over het [oplossen van aanbevelingen in azure Security Center](security-center-remediate-recommendations.md)).

De aanbevelingen voor de beveiliging van Kubernetes-werk belasting zijn:

- Azure Policy-invoeg toepassing voor Kubernetes moet zijn geïnstalleerd en ingeschakeld op uw clusters
- De CPU- en geheugenlimieten van containers moeten worden afgedwongen
- Bevoegde containers moeten worden vermeden
- Onveranderbaar (alleen-lezen) hoofdbestandssysteem moet worden afgedwongen voor containers
- Container met escalatie van bevoegdheden moet worden vermeden
- Het uitvoeren van containers als hoofdgebruiker moet worden vermeden
- Containers die gevoelige hostnaamruimten delen, moeten worden vermeden
- Minimaal bevoegde Linux-functies moeten worden afgedwongen voor containers
- Het gebruik van HostPath-volumekoppelingen voor pods moet worden beperkt tot een bekende lijst
- Containers mogen alleen op toegestane poorten luisteren
- Services mogen alleen op toegestane poorten luisteren
- Het gebruik van hostnetwerken en -poorten moet worden beperkt
- Het overschrijven of uitschakelen van het AppArmor-profiel voor containers moet worden beperkt
- Container installatie kopieën moeten alleen worden geïmplementeerd vanuit vertrouwde registers             

Meer informatie over deze aanbevelingen vindt [u in Beveilig uw Kubernetes-workloads](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Twee aanbevelingen van het beveiligings beheer ' systeem updates Toep assen ' worden afgeschaft 

**Geschatte datum voor wijziging:** Februari 2021

De volgende twee aanbevelingen zijn gepland om te worden afgeschaft in februari 2021:

- **Uw computers moeten opnieuw worden opgestart om systeem updates toe te passen**. Dit kan een geringe invloed op uw beveiligde Score opleveren.
- **Bewakings agent moet op uw computers zijn geïnstalleerd**. Deze aanbeveling is gekoppeld aan on-premises machines en sommige logica wordt overgezet naar een andere aanbeveling, **log Analytics problemen met de agent status moeten worden opgelost op uw computers**. Dit kan een geringe invloed op uw beveiligde Score opleveren.

We raden u aan om uw continue export-en werk stroom Automation-configuraties te controleren om te zien of deze aanbevelingen in hen zijn opgenomen. Daarnaast moeten alle Dash boards of andere bewakings hulpprogramma's die deze kunnen gebruiken, dienovereenkomstig worden bijgewerkt.

Meer informatie over deze aanbevelingen vindt u op de [overzichts pagina met aanbevelingen voor beveiliging](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Verbeteringen in aanbeveling voor SQL-gegevens classificatie

**Geschatte datum voor wijziging:** Q2 2021

De huidige versie van de gevoelige aanbevelings **gegevens in uw SQL-data bases moet worden geclassificeerd** in het beveiligings beheer **gegevens classificatie Toep assen** wordt vervangen door een nieuwe versie die beter is afgestemd op de strategie voor gegevens classificatie van micro soft. Als gevolg hiervan:

- De aanbeveling heeft geen invloed meer op uw beveiligde Score
- Het beveiligings beheer (' gegevens classificatie Toep assen ') heeft geen invloed meer op uw beveiligde Score
- De ID van de aanbeveling wordt ook gewijzigd (momenteel b0df6f56-862d-4730-8597-38c0fd4ebd59)



## <a name="next-steps"></a>Volgende stappen

Zie [Wat is er nieuw in Azure Security Center?](release-notes.md) voor alle recente wijzigingen aan het product.
