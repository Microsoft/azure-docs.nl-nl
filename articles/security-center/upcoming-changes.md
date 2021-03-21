---
title: Belangrijke wijzigingen afkomstig van Azure Security Center
description: Aanstaande wijzigingen in Azure Security Center waarvan u mogelijk op de hoogte moet zijn en waarmee u mogelijk rekening moet houden
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/10/2021
ms.author: memildin
ms.openlocfilehash: 49141f7f11c0e8ead090459238e15b56f57b990b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633713"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Belangrijke aanstaande wijzigingen aan Azure Security Center

> [!IMPORTANT]
> De informatie op deze pagina heeft betrekking op producten of functies van voorlopige versies, die aanzienlijk kunnen worden gewijzigd voordat ze commercieel worden vrijgegeven, als dat ooit al gebeurt. Microsoft biedt geen verplichtingen of garanties, uitdrukkelijk of impliciet, met betrekking tot de informatie die hier wordt verstrekt.

Op deze pagina vindt u informatie over de wijzigingen die zijn gepland voor Security Center. Hierin worden de geplande wijzigingen in het product beschreven die van invloed kunnen zijn op zaken als uw beveiligde score of werkstromen.

Als u op zoek bent naar de nieuwste opmerkingen bij de release, vindt u deze in [Wat is er nieuw in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplande wijzigingen

- [Aanbevelingen van AWS worden uitgebracht voor algemene Beschik baarheid (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)
- [Twee aanbevelingen van het beveiligings beheer ' systeem updates Toep assen ' worden afgeschaft](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Verbeteringen in aanbeveling voor SQL-gegevens classificatie](#enhancements-to-sql-data-classification-recommendation)
- [Afschaffing van 11 Azure Defender-waarschuwingen](#deprecation-of-11-azure-defender-alerts)


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Aanbevelingen van AWS worden uitgebracht voor algemene Beschik baarheid (GA)

**Geschatte datum voor wijziging:** April 2021

Azure Security Center biedt bescherming voor workloads in Azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

De aanbevelingen die afkomstig zijn van de AWS Security hub, zijn in de preview-periode geweest sinds de introductie van de Cloud connectors. Aanbevelingen die als **Preview** zijn gemarkeerd, zijn niet opgenomen in de berekeningen van uw beveiligde Score, maar moeten nog steeds worden hersteld, zodat de evaluatie periode afloopt.

Met deze wijziging worden twee sets van AWS-aanbevelingen verplaatst naar GA:

- [PCI DSS besturings elementen van de Security hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Security hub CIS AWS Stichtings benchmarks Controls](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Wanneer dit GA en de evaluaties worden uitgevoerd op uw AWS-resources, zijn de resultaten van invloed op uw gecombineerde beveiligde score voor al uw multi-en hybride cloud resources. 


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Twee aanbevelingen van het beveiligings beheer ' systeem updates Toep assen ' worden afgeschaft 

**Geschatte datum voor wijziging:** 2021 maart

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
