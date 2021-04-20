---
title: Belangrijke wijzigingen afkomstig van Azure Security Center
description: Aanstaande wijzigingen in Azure Security Center waarvan u mogelijk op de hoogte moet zijn en waarmee u mogelijk rekening moet houden
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/20/2021
ms.author: memildin
ms.openlocfilehash: 3307d3aed422c3eab63412388244ef14ef3be699
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750986"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Belangrijke aanstaande wijzigingen aan Azure Security Center

> [!IMPORTANT]
> De informatie op deze pagina heeft betrekking op producten of functies van voorlopige versies, die aanzienlijk kunnen worden gewijzigd voordat ze commercieel worden vrijgegeven, als dat ooit al gebeurt. Microsoft biedt geen verplichtingen of garanties, uitdrukkelijk of impliciet, met betrekking tot de informatie die hier wordt verstrekt.

Op deze pagina vindt u informatie over de wijzigingen die zijn gepland voor Security Center. Hierin worden de geplande wijzigingen in het product beschreven die van invloed kunnen zijn op zaken als uw beveiligde score of werkstromen.

Als u op zoek bent naar de nieuwste opmerkingen bij de release, vindt u deze in [Wat is er nieuw in Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Geplande wijzigingen

| Geplande wijziging                                                                                                                                                        | Geschatte datum voor wijziging |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------|
| [21 aanbevelingen voor het verplaatsen tussen beveiligingscontroles](#21-recommendations-moving-between-security-controls)                                                           | April 2021                |
| [Twee aanbevelingen van beveiligingsbeheer 'Systeemupdates toepassen' worden afgeschaft](#two-recommendations-from-apply-system-updates-security-control-being-deprecated) | April 2021                |
| [Verouderde implementatie van ISO 27001 wordt vervangen door nieuwe ISO 27001:2013](#legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013)          | Juni 2021                 |
| [Aanbevelingen van AWS worden uitgebracht voor algemene beschikbaarheid](#recommendations-from-aws-will-be-released-for-general-availability-ga)                     | **Augustus** 2021           |
| [Verbeteringen in aanbeveling voor SQL-gegevensclassificatie](#enhancements-to-sql-data-classification-recommendation)                                                     | Tweede kwartaal van 2021                   |
|                                                                                                                                                                       |                           |


### <a name="21-recommendations-moving-between-security-controls"></a>21 aanbevelingen voor het verplaatsen tussen beveiligingscontroles 

**Geschatte datum voor wijziging:** April 2021

De volgende aanbevelingen worden verplaatst naar een ander besturingselement voor beveiliging. Beveiligingscontroles zijn logische groepen gerelateerde beveiligingsaanbevelingen en weerspiegelen uw kwetsbare aanvalsoppervlakken. Deze overstap zorgt ervoor dat elk van deze aanbevelingen de meest geschikte controle heeft om het doel te bereiken. 

Voor meer informatie over welke aanbevelingen zich in elk beveiligingsbeheer bevinden, raadpleegt u Beveiligingscontroles en de bijbehorende aanbevelingen.

|Aanbeveling |Wijziging en impact  |
|---------|---------|
|De evaluatie van beveiligingsproblemen moet worden ingeschakeld op uw SQL-servers<br>De evaluatie van beveiligingsproblemen moet worden ingeschakeld voor uw beheerde SQL-exemplaren<br>Beveiligingsproblemen in uw SQL-databases moeten worden vereengeld<br>Beveiligingsproblemen in uw SQL-databases in VM's moeten worden ver verhelpen     |Over te gaan van Beveiligingsproblemen herstellen (6 punten waard)<br>om beveiligingsconfiguraties te herstellen (ter waarde van 4 punten).<br>Afhankelijk van uw omgeving hebben deze aanbevelingen een verminderde invloed op uw score.|
|Er moet meer dan één eigenaar zijn toegewezen aan uw abonnement<br>Automation-accountvariabelen moeten worden versleuteld<br>IoT-apparaten: controleproces is gestopt met het verzenden van gebeurtenissen<br>IoT-apparaten- Validatiefout van besturingssysteembasislijn<br>IoT-apparaten - upgrade van TLS-coderingssuite vereist<br>IoT-apparaten - Poorten openen op apparaat<br>IoT-apparaten: er is een permissief firewallbeleid in een van de ketens gevonden<br>IoT-apparaten: er is een permissieve firewallregel in de invoerketen gevonden<br>IoT-apparaten: er is een permissieve firewallregel in de uitvoerketen gevonden<br>Diagnostische logboeken in IoT Hub moeten zijn ingeschakeld<br>IoT-apparaten - Agent die onderbelegde berichten verstuurt<br>IoT-apparaten: standaard IP-filterbeleid moet Weigeren zijn<br>IoT-apparaten - IP-filterregel groot IP-bereik<br>IoT-apparaten: agentberichtintervallen en grootte moeten worden aangepast<br>IoT-apparaten - Identieke verificatiereferenties<br>IoT-apparaten: gecontroleerd proces is gestopt met het verzenden van gebeurtenissen<br>IoT-apparaten: de basislijnconfiguratie van het besturingssysteem moet zijn hersteld|Over te gaan **op Best practices voor beveiliging implementeren.**<br>Wanneer een aanbeveling wordt verplaatst naar Beveiligingsbeheer voor best practices voor beveiliging implementeren, wat geen punten waard is, is de aanbeveling niet langer van invloed op uw beveiligingsscore.|
|||


### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Twee aanbevelingen van het beveiligingsbeheer Systeemupdates toepassen worden afgeschaft

**Geschatte wijzigingsdatum:** April 2021

De volgende twee aanbevelingen worden afgeschaft:

-  De versie van het besturingssysteem moet worden bijgewerkt voor uw cloudservicerollen: standaard werkt Azure uw gastbesturingssysteem periodiek bij naar de meest recente ondersteunde installatie afbeelding binnen de besturingssysteemfamilie die u hebt opgegeven in uw serviceconfiguratie (.cscfg), zoals Windows Server 2016.
- Kubernetes Services moet worden geüpgraded naar een **niet-kwetsbare Kubernetes-versie.** De evaluaties van deze aanbeveling zijn niet zo breed als we willen. De huidige versie van deze aanbeveling wordt uiteindelijk vervangen door een verbeterde versie die beter is afgestemd op de beveiligingsbehoeften van onze klant.


### <a name="legacy-implementation-of-iso-27001-is-being-replaced-with-new-iso-270012013"></a>Verouderde implementatie van ISO 27001 wordt vervangen door nieuwe ISO 27001:2013

De verouderde implementatie van ISO 27001 wordt verwijderd uit Security Center dashboard voor naleving van regelgeving. Als u uw ISO 27001-naleving met Security Center bij houdt, onboardt u de nieuwe ISO 27001:2013-standaard voor alle relevante beheergroepen of abonnementen en wordt de huidige verouderde ISO 27001 binnenkort verwijderd uit het dashboard.

:::image type="content" source="media/upcoming-changes/removing-iso-27001-legacy-implementation.png" alt-text="Security Center dashboard voor naleving van regelgeving met het bericht over het verwijderen van de verouderde implementatie van ISO 27001." lightbox="media/upcoming-changes/removing-iso-27001-legacy-implementation.png":::

### <a name="recommendations-from-aws-will-be-released-for-general-availability-ga"></a>Aanbevelingen van AWS worden uitgebracht voor algemene beschikbaarheid

**Geschatte wijzigingsdatum:** Augustus 2021

Azure Security Center biedt bescherming voor workloads in Azure, Amazon Web Services (AWS) en Google Cloud Platform (GCP).

De aanbevelingen van AWS Security Hub zijn in preview sinds de introductie van de cloudconnectoren. Aanbevelingen die worden  gemarkeerd als preview-versie, worden niet opgenomen in de berekeningen van uw beveiligde score, maar moeten waar mogelijk nog steeds worden herstelt, zodat wanneer de preview-periode wordt beëindigd, ze bijdragen aan uw score.

Met deze wijziging worden twee sets AWS-aanbevelingen verplaatst naar ga:

- [De besturingselementen voor PCI DSS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-pci-controls.html)
- [Besturingselementen voor CIS AWS Foundations Benchmark van Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-cis-controls.html)

Wanneer deze ga-en-evaluaties worden uitgevoerd op uw AWS-resources, zijn de resultaten van invloed op uw gecombineerde beveiligde score voor al uw multi- en hybride cloudresources.



### <a name="enhancements-to-sql-data-classification-recommendation"></a>Verbeteringen in aanbeveling voor SQL-gegevensclassificatie

**Geschatte wijzigingsdatum:** Tweede kwartaal van 2021

De aanbeveling Gevoelige gegevens in uw **SQL-databases** moeten worden geclassificeerd in het besturingselement Beveiliging voor gegevensclassificatie toepassen wordt vervangen door een nieuwe versie die beter is afgestemd op de strategie van Microsoft voor gegevensclassificatie.  Als gevolg hiervan wordt de id van de aanbeveling ook gewijzigd (momenteel is dit b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Volgende stappen

Zie [Wat is er nieuw in Azure Security Center?](release-notes.md) voor alle recente wijzigingen aan het product.