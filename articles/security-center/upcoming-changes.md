---
title: Belangrijke wijzigingen afkomstig van Azure Security Center
description: Aanstaande wijzigingen in Azure Security Center waarvan u mogelijk op de hoogte moet zijn en waarmee u mogelijk rekening moet houden
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/08/2021
ms.author: memildin
ms.openlocfilehash: f9e1b5d19acbc9bcee86c374a3f843530b8adc61
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103756"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Belangrijke aanstaande wijzigingen aan Azure Security Center

> [!IMPORTANT]
> De informatie op deze pagina heeft betrekking op producten of functies van voorlopige versies, die aanzienlijk kunnen worden gewijzigd voordat ze commercieel worden vrijgegeven, als dat ooit al gebeurt. Microsoft biedt geen verplichtingen of garanties, uitdrukkelijk of impliciet, met betrekking tot de informatie die hier wordt verstrekt.

Op deze pagina vindt u informatie over de wijzigingen die zijn gepland voor Security Center. Hierin worden de geplande wijzigingen in het product beschreven die van invloed kunnen zijn op zaken als uw beveiligde score of werkstromen.

Als u op zoek bent naar de nieuwste opmerkingen bij de release, vindt u deze in [Wat is er nieuw in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplande wijzigingen

| Geplande wijziging                                                                                                                                                        | Geschatte datum voor wijziging |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [21 aanbevelingen die tussen beveiligings controles worden verplaatst](#21-recommendations-moving-between-security-controls)                                                           | April 2021                |
| [Twee aanbevelingen van het beveiligings beheer ' systeem updates Toep assen ' worden afgeschaft](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | April 2021                |
| [Aanbevelingen van AWS worden uitgebracht voor algemene Beschik baarheid (GA)](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | 2021 **augustus**             |
| [Verbeteringen in aanbeveling voor SQL-gegevens classificatie](#enhancements-to-sql-data-classification-recommendation)                                                     | Q2 2021                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>21 aanbevelingen die tussen beveiligings controles worden verplaatst 

**Geschatte datum voor wijziging:** April 2021

De volgende aanbevelingen worden verplaatst naar een ander beveiligings beheer. Beveiligings controles zijn logische groepen van gerelateerde beveiligings aanbevelingen en zijn afgespiegeld op uw kwets bare aanvals oppervlakken. Op deze manier zorgt u ervoor dat elk van deze aanbevelingen in het meest geschikte besturings element staat om aan het doel te voldoen. 

Voor meer informatie over welke aanbevelingen zich in elk beveiligingsbeheer bevinden, raadpleegt u Beveiligingscontroles en de bijbehorende aanbevelingen.

|Aanbeveling |Wijziging en impact  |
|---------|---------|
|De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers<br>De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren<br>Beveiligings problemen voor uw SQL-data bases moeten worden hersteld<br>Beveiligings problemen voor uw SQL-data bases in Vm's moeten worden hersteld     |Verplaatsen van beveiligings problemen oplossen (6 punten waard)<br>beveiligings configuraties herstellen (4 punten waard).<br>Afhankelijk van uw omgeving heeft deze aanbevelingen een geringe invloed op uw score.|
|Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement<br>Automation-accountvariabelen moeten worden versleuteld<br>IoT-apparaten-controle proces gestopt gebeurtenissen verzenden<br>IoT-apparaten-validatie van basis lijn van besturings systeem<br>IoT-apparaten-upgrade van TLS-coderings suite vereist<br>IoT-apparaten: poorten openen op apparaat<br>IoT-apparaten: beleid voor strikte firewall in een van de ketens is gevonden<br>IoT-apparaten-strikte firewall regel in de invoer keten is gevonden<br>IoT-apparaten-strikte firewall regel in de uitvoer keten is gevonden<br>Diagnostische logboeken in IoT Hub moeten zijn ingeschakeld<br>IoT-apparaten: agent die onderbezette berichten verzendt<br>IoT-apparaten-standaard IP-filter beleid moet worden geweigerd<br>IoT-apparaten-IP-filter regel grote IP-bereik<br>IoT-apparaten-intervallen en grootte van agent berichten moeten worden aangepast<br>IoT-apparaten-identieke verificatie referenties<br>IoT-apparaten-gecontroleerd proces heeft het verzenden van gebeurtenissen gestopt<br>IoT-apparaten-de basislijn configuratie van het besturings systeem (OS) moet worden hersteld|Verplaatsen naar **aanbevolen beveiligings procedures implementeren**.<br>Wanneer een aanbeveling wordt verplaatst naar het beveiligings beheer van best practices voor beveiliging, wat geen punten is, is de aanbeveling niet langer van invloed op uw beveiligde Score.|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Twee aanbevelingen van het beveiligings beheer ' systeem updates Toep assen ' worden afgeschaft

**Geschatte datum voor wijziging:** April 2021

De volgende twee aanbevelingen worden afgeschaft:

- **De versie van het besturings systeem moet worden bijgewerkt voor uw Cloud service-rollen** : standaard werkt Azure uw gast besturingssysteem regel matig bij naar de meest recente ondersteunde installatie kopie in de besturingssysteem familie die u hebt opgegeven in uw service configuratie (. cscfg), zoals Windows Server 2016.
- **Kubernetes Services moet worden bijgewerkt naar een niet-kwets bare Kubernetes-versie** . de evaluaties van deze aanbeveling zijn niet zo breed als we willen. De huidige versie van deze aanbeveling wordt uiteindelijk vervangen door een verbeterde versie die beter is afgestemd op de beveiligings behoeften van onze klant.


### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Aanbevelingen van AWS worden uitgebracht voor algemene Beschik baarheid (GA)

**Geschatte datum voor wijziging:** 2021 augustus

Azure Security Center biedt bescherming voor workloads in Azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

De aanbevelingen die afkomstig zijn van de AWS Security hub, zijn in de preview-periode geweest sinds de introductie van de Cloud connectors. Aanbevelingen die als **Preview** zijn gemarkeerd, zijn niet opgenomen in de berekeningen van uw beveiligde Score, maar moeten nog steeds worden hersteld, zodat de evaluatie periode afloopt.

Met deze wijziging worden twee sets van AWS-aanbevelingen verplaatst naar GA:

- [PCI DSS besturings elementen van de Security hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Security hub CIS AWS Stichtings benchmarks Controls](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Wanneer dit GA en de evaluaties worden uitgevoerd op uw AWS-resources, zijn de resultaten van invloed op uw gecombineerde beveiligde score voor al uw multi-en hybride cloud resources.



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Verbeteringen in aanbeveling voor SQL-gegevens classificatie

**Geschatte datum voor wijziging:** Q2 2021

De gevoelige aanbevelings **gegevens in uw SQL-data bases moeten worden geclassificeerd** in het beveiligings beheer **gegevens classificatie Toep assen** wordt vervangen door een nieuwe versie die beter is afgestemd op de strategie voor gegevens classificatie van micro soft. Als gevolg hiervan wordt de ID van de aanbeveling ook gewijzigd (momenteel b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Volgende stappen

Zie [Wat is er nieuw in Azure Security Center?](release-notes.md) voor alle recente wijzigingen aan het product.