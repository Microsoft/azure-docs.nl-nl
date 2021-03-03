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
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 375e8a748e8833e9483d92353ed04add287e90fb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705089"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Belangrijke aanstaande wijzigingen aan Azure Security Center

> [!IMPORTANT]
> De informatie op deze pagina heeft betrekking op producten of functies van voorlopige versies, die aanzienlijk kunnen worden gewijzigd voordat ze commercieel worden vrijgegeven, als dat ooit al gebeurt. Microsoft biedt geen verplichtingen of garanties, uitdrukkelijk of impliciet, met betrekking tot de informatie die hier wordt verstrekt.

Op deze pagina vindt u informatie over de wijzigingen die zijn gepland voor Security Center. Hierin worden de geplande wijzigingen in het product beschreven die van invloed kunnen zijn op zaken als uw beveiligde score of werkstromen.

Als u op zoek bent naar de nieuwste opmerkingen bij de release, vindt u deze in [Wat is er nieuw in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplande wijzigingen

- [Met twee oudere aanbevelingen worden gegevens niet meer rechtstreeks naar Azure-activiteiten logboek geschreven](#two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log)
- [Twee aanbevelingen van het beveiligings beheer ' systeem updates Toep assen ' worden afgeschaft](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Verbeteringen in aanbeveling voor SQL-gegevens classificatie](#enhancements-to-sql-data-classification-recommendation)
- [Afschaffing van 11 Azure Defender-waarschuwingen](#deprecation-of-11-azure-defender-alerts)


### <a name="two-legacy-recommendations-will-no-longer-write-data-directly-to-azure-activity-log"></a>Met twee oudere aanbevelingen worden gegevens niet meer rechtstreeks naar Azure-activiteiten logboek geschreven 

**Geschatte datum voor wijziging:** 2021 maart

Security Center geeft de gegevens voor bijna alle beveiligings aanbevelingen door aan Azure Advisor die op zijn beurt deze naar het [Azure-activiteiten logboek](../azure-monitor/essentials/activity-log.md)schrijven.

Voor twee aanbevelingen worden de gegevens tegelijkertijd rechtstreeks naar het Azure-activiteiten logboek geschreven. Met deze wijziging stopt Security Center het schrijven van gegevens voor deze verouderde beveiligings aanbevelingen rechtstreeks in het activiteiten logboek. In plaats daarvan exporteren we de gegevens naar Azure Advisor zoals we voor alle andere aanbevelingen doen. 

De twee oudere aanbevelingen zijn:
- Statusproblemen met eindpuntbescherming moeten worden opgelost voor uw machines
- Beveiligingsproblemen in de beveiligingsconfiguratie op uw computers moeten worden hersteld

Als u toegang hebt tot informatie voor deze twee aanbevelingen in de categorie ' aanbeveling van type TaskDiscovery ' van het activiteiten logboek, is dit niet langer beschikbaar.

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


### <a name="deprecation-of-11-azure-defender-alerts"></a>Afschaffing van 11 Azure Defender-waarschuwingen

**Geschatte datum voor wijziging:** 2021 maart

De volgende maand worden de elf Azure Defender-waarschuwingen die hieronder worden weer gegeven, afgeschaft.

- Nieuwe waarschuwingen vervangen deze twee waarschuwingen en bieden een betere dekking:

    | AlertType                | AlertDisplayName                                                         |
    |--------------------------|--------------------------------------------------------------------------|
    | ARM_MicroBurstDomainInfo | VOOR beeld-microburst Toolkit ' Get-AzureDomainInfo ' uitgevoerde functie gedetecteerd |
    | ARM_MicroBurstRunbook    | VOOR beeld-microburst Toolkit ' Get-AzurePasswords ' uitgevoerde functie gedetecteerd  |
    |                          |                                                                          |

- Deze negen waarschuwingen hebben betrekking op een Azure Active Directory Identity Protection connector die al is afgeschaft:

    | AlertType           | AlertDisplayName              |
    |---------------------|-------------------------------|
    | UnfamiliarLocation  | Onbekende aanmeldingseigenschappen |
    | AnonymousLogin      | Anoniem IP-adres          |
    | InfectedDeviceLogin | Aan malware gekoppeld IP-adres     |
    | ImpossibleTravel    | Ongewoon traject               |
    | MaliciousIP         | Schadelijk IP-adres          |
    | LeakedCredentials   | Gelekte referenties            |
    | PasswordSpray       | Wachtwoord spuit                |
    | LeakedCredentials   | Azure AD-bedreigingsinformatie  |
    | AADAI               | Azure AD AI                   |
    |                     |                               |
 



## <a name="next-steps"></a>Volgende stappen

Zie [Wat is er nieuw in Azure Security Center?](release-notes.md) voor alle recente wijzigingen aan het product.
