---
title: Overzicht van de Azure Security Bench Mark v2
description: Overzicht van Azure Security Bench Mark v2
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3bc97a932e622838315f717cc7b036a559787805
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369154"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Overzicht van de Azure Security Bench Mark (v2)

De Azure Security Bench Mark (ASB) biedt prescriptieve aanbevolen procedures en aanbevelingen voor het verbeteren van de beveiliging van werk belastingen, gegevens en services in Azure.

Deze bench Mark maakt deel uit van een aantal holistische beveiligings richtlijnen die ook het volgende bevatten:

- **Cloud-acceptatie raamwerk** : richt lijnen voor beveiliging, inclusief [strategie](/azure/cloud-adoption-framework/strategy/define-security-strategy), [rollen en verantwoordelijkheden](/azure/cloud-adoption-framework/organize/cloud-security), [Aanbevolen Azure Top 10-beveiligings procedures](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)en [referentie-implementatie](/azure/cloud-adoption-framework/ready/enterprise-scale/).
- **Azure Well-Architected Framework** : richt lijnen voor [het beveiligen van uw workloads](/assessments/?mode=pre-assessment&session=local) in Azure.
- **Aanbevolen procedures voor micro soft-beveiliging** : [aanbevelingen](/security/compass/microsoft-security-compass-introduction) met voor beelden op Azure.

 De Azure Security-Bench Mark is gericht op Cloud gerichte controle gebieden. Deze besturings elementen zijn consistent met bekende beveiligings benchmarks, zoals die worden beschreven in het Center for Internet Security (CIS) Controls versie 7,1 en National Institute of Standards and Technology (NIST) SP 800-53.
De volgende besturings elementen zijn opgenomen in de Azure Security-Bench Mark:

| Beheer domeinen ASB | Description 
|--|--|
| [Netwerk &nbsp; beveiliging &nbsp; (NS)](security-controls-v2-network-security.md) | Netwerk beveiliging heeft betrekking op besturings elementen voor het beveiligen en beveiligen van Azure-netwerken, waaronder het beveiligen van virtuele netwerken, het tot stand brengen van particuliere verbindingen, het voor komen en beperken van externe aanvallen en het beveiligen van DNS. |
| [Identiteits &nbsp; beheer &nbsp; (IM)](security-controls-v2-identity-management.md) | Identiteits beheer heeft betrekking op besturings elementen voor het instellen van een beveiligde identiteits-en toegangs beheer met behulp van Azure Active Directory, waaronder het gebruik van eenmalige aanmelding, sterke verificaties, beheerde identiteiten (en service principes) voor toepassingen, voorwaardelijke toegang en het controleren van afwijkingen van accounts. |
| [Privileged &nbsp; Access &nbsp; (PA)](security-controls-v2-privileged-access.md) | Uitgebreide toegang heeft betrekking op besturings elementen voor het beveiligen van bevoegde toegang tot uw Azure-Tenant en-resources, waaronder een reeks besturings elementen voor het beveiligen van uw beheer model, beheerders accounts en geprivilegieerde toegang tegen opzettelijke en onbedoelde Risico's. |
| [Gegevens &nbsp; bescherming &nbsp; (DP)](security-controls-v2-data-protection.md) | Gegevens bescherming is van toepassing op de controle van gegevens beveiliging op rest, onderweg en via geautoriseerde toegangs mechanismen, waaronder het detecteren, classificeren, beveiligen en bewaken van gevoelige gegevens assets met behulp van toegangs beheer, versleuteling en logboek registratie in Azure. |
| [Asset &nbsp; Management &nbsp; (am)](security-controls-v2-asset-management.md) | Asset Management bestrijkt de controles om inzicht te krijgen in de beveiliging en het beheer van Azure-resources, waaronder aanbevelingen over machtigingen voor beveiligings personeel, de beveiliging van de inventaris en het beheren van goed keuringen voor services en resources (inventaris, tracering en juistheid). |
| [Logboek registratie &nbsp; en &nbsp; detectie van bedreigingen &nbsp; (lt)](security-controls-v2-logging-threat-detection.md) | Logboek registratie en detectie van bedreigingen omvatten besturings elementen voor het detecteren van bedreigingen op Azure en het inschakelen, verzamelen en opslaan van audit logboeken voor Azure-Services, waaronder het inschakelen van detectie-, onderzoek-en herstel processen met besturings elementen voor het genereren van waarschuwingen van hoge kwaliteit met systeem eigen bedreigingen detectie in Azure-Services. Het omvat ook het verzamelen van logboeken met Azure Monitor, het centraliseren van beveiligings analyse met Azure Sentinel, tijd synchronisatie en logboek registratie. |
| [Reactie op incidenten &nbsp; &nbsp; (IR)](security-controls-v2-incident-response.md) | Reactie op incidenten betreft controles in de levens cyclus van incidenten: voor bereiding, detectie en analyse, insluiting en activiteiten na incidenten, inclusief het gebruik van Azure-Services, zoals Azure Security Center en Sentinel, om het respons proces van het incident te automatiseren. |
| [Postuur &nbsp; en &nbsp; beveiligings &nbsp; beheer &nbsp; (HW)](security-controls-v2-posture-vulnerability-management.md) | Postuur en beveiligings beheer richt zich op besturings elementen voor het beoordelen en verbeteren van de postuur van Azure, waaronder het scannen van beveiligings problemen, het testen van indringing en herstel, alsmede het bijhouden van de beveiligings configuratie, rapportage en correctie in azure-resources. |
| [Endpoint &nbsp; Security &nbsp; (ES)](security-controls-v2-endpoint-security.md) | Endpoint Security heeft betrekking op besturings elementen in eindpunt detectie en-antwoord, met inbegrip van het gebruik van eindpunt detectie en-antwoorden (EDR) en anti-malware-service voor eind punten in azure-omgevingen. |
| [Back-up &nbsp; en &nbsp; herstel &nbsp; (BR)](security-controls-v2-backup-recovery.md) | Back-up en herstel bestrijkt de controles om ervoor te zorgen dat de gegevens en configuratie back-ups in de verschillende service lagen worden uitgevoerd, gevalideerd en beveiligd. |
| [Governance &nbsp; en &nbsp; strategie &nbsp; (GS)](security-controls-v2-governance-strategy.md) | Governance en strategie biedt richt lijnen voor het garanderen van een samenhangende beveiligings strategie en een gedocumenteerde governance aanpak om beveiligings verzekeringen te hand haven, inclusief het instellen van rollen en verantwoordelijkheden voor de verschillende Cloud beveiligings functies, uniforme technische strategie en ondersteunings beleid en-standaarden. |

## <a name="azure-security-benchmark-recommendations"></a>Aanbevelingen voor Azure Security Bench Mark

Elke aanbeveling bevat de volgende informatie:

- **Azure-id**: de Azure Security Bench Mark-id die overeenkomt met de aanbeveling.
- **CIS controleert v 7.1-id ('s)**: het CIS besturings element (en) die overeenkomen met deze aanbeveling.
- **NIST sp 800-53 R4-id ('s)**: de Control (s) van het nist SP 800-53 R4 (gemiddeld) die overeenkomen met deze aanbeveling.
- **Details**: de motivering van de aanbeveling en koppelingen naar richt lijnen voor het implementeren ervan. Als de aanbeveling wordt ondersteund door Azure Security Center, wordt die informatie ook weer gegeven.
- **Verantwoordelijkheid**: of de klant, de service provider of beide verantwoordelijk zijn voor de implementatie van deze aanbeveling. Beveiligings verantwoordelijkheden worden gedeeld in de open bare Cloud. Sommige beveiligings mechanismen zijn alleen beschikbaar voor de Cloud serviceprovider en daarom is de provider verantwoordelijk voor het adresseren van deze. Dit zijn algemene waarnemingen: voor sommige afzonderlijke services is de verantwoordelijkheid afwijkend van wat wordt vermeld in de Azure Security-benchmark waarde. Deze verschillen worden beschreven in de aanbevelingen van de basis lijn voor de afzonderlijke service.
- **Beveiligings belanghebbenden** van de klant: [de beveiligings functies](/azure/cloud-adoption-framework/organize/cloud-security#security-functions) bij de organisatie van de klant die kunnen worden beheerbaar, verantwoordelijk of geraadpleegd voor de respectieve controle. Het kan afwijken van de organisatie naar de organisatie, afhankelijk van de organisatie structuur van uw bedrijf en de rollen en verantwoordelijkheden die u hebt ingesteld voor de beveiliging van Azure.

> [!NOTE]
> De controle toewijzingen tussen de ASB en de branche benchmarks (zoals NIST en CIS) geven alleen aan dat een specifieke functie van Azure kan worden gebruikt voor het volledig of gedeeltelijk oplossen van een controle vereiste die is gedefinieerd in het NIST of CIS. Houd er rekening mee dat deze implementatie niet noodzakelijkerwijs moet worden vertaald naar de volledige naleving van het overeenkomstige besturings element in CIS of NIST.

We stellen uw gedetailleerde feedback en actieve deelname aan de benchmark inspanningen van Azure Security toe. Als u de directe invoer van het Azure Security Bench Mark-team wilt opgeven, vult u het formulier in op https://aka.ms/AzSecBenchmark

## <a name="download"></a>Downloaden

U kunt de Azure Security-Bench Mark in een [Spreadsheet indeling](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark)downloaden.

## <a name="next-steps"></a>Volgende stappen 
- Zie het eerste beveiligings beheer: [netwerk beveiliging](security-control-network-security.md)
- Lees de [Inleiding voor Azure Security Bench Mark](introduction.md)
- Meer informatie over de [basis beginselen van Azure-beveiliging](../fundamentals/index.yml)