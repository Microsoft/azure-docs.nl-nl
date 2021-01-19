---
title: Automatische AVS-evaluatie berekeningen in Azure Migrate | Microsoft Docs
description: Hierin wordt een overzicht gegeven van de AVS-evaluatie berekeningen in de Azure Migrate-service.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: f52c0296023098c755feb1bf0baba980f2988bd7
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567705"
---
# <a name="server-assessment-overview-migrate-to-azure-vmware-solution"></a>Overzicht van server evaluatie (migreren naar Azure VMware-oplossing)

[Azure migrate](migrate-services-overview.md) biedt een centrale hub voor het bijhouden van de detectie, beoordeling en migratie van uw on-premises apps en workloads. Ook worden uw persoonlijke en open bare Cloud exemplaren in azure bijgehouden. De hub biedt Azure Migrate-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV-aanbiedingen (Independent Software Vendor) van derden.

Server evaluatie is een hulp programma in Azure Migrate dat on-premises servers evalueert voor migratie naar Azure IaaS virtuele machines en de Azure VMware-oplossing (AVS). Dit artikel bevat informatie over hoe de evaluaties van de Azure VMware-oplossing (AVS) worden berekend.

> [!NOTE]
> De evaluatie versie van Azure VMware Solution (AVS) is momenteel in Preview en kan alleen worden gemaakt voor virtuele VMware-machines.

## <a name="types-of-assessments"></a>Typen evaluaties

Beoordelingen die u maakt met server evaluatie zijn een tijdgebonden moment opname van gegevens. Er zijn twee soorten evaluaties die u kunt maken met behulp van Azure Migrate: Server-evaluatie.

**Evaluatietype** | **Details**
--- | --- 
**Azure VM** | Evaluaties om uw on-premises servers te migreren naar virtuele Azure-machine. <br/><br/> U kunt uw on-premises [virtuele VMware-machines](how-to-set-up-appliance-vmware.md), [virtuele Hyper-V-machines](how-to-set-up-appliance-hyper-v.md)en [fysieke servers](how-to-set-up-appliance-physical.md) voor migratie naar Azure evalueren met dit beoordelings type. [Meer informatie](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | Evaluaties om uw on-premises servers te migreren naar [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> U kunt uw on-premises [VMware-VM’s](how-to-set-up-appliance-vmware.md) evalueren voor migratie naar Azure VMware Solution (AVS) met dit evaluatietype. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)

De evaluatie van de Azure VMware-oplossing (AVS) in Server beoordeling biedt twee opties voor het aanpassen van de grootte:

**Evaluatie** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties op basis van verzamelde prestatie gegevens van on-premises Vm's. | **Aanbevolen knooppunt grootte**: op basis van het CPU-en geheugen gebruik gegevens, samen met het knooppunt type, het opslag type en de FTT-instelling die u voor de evaluatie selecteert.
**Zoals on-premises** | Evaluaties op basis van on-premises grootte aanpassen. | **Aanbevolen knooppunt grootte**: op basis van de on-PREMISes VM-grootte samen met het knooppunt type, het opslag type en de FTT-instelling die u voor de evaluatie selecteert.

## <a name="how-do-i-run-an-assessment"></a>Hoe kan ik een evaluatie uit te voeren?

Er zijn een aantal manieren om een evaluatie uit te voeren.

- Computers beoordelen door gebruik te maken van meta gegevens van de server die zijn verzameld door een licht gewicht Azure Migrate apparaat. Het apparaat detecteert on-premises machines. Vervolgens worden de meta gegevens van de computer en de prestatie gegevens naar Azure Migrate verzonden.
- Computers beoordelen door gebruik te maken van meta gegevens van de server die worden geïmporteerd in een CSV-indeling (door komma's gescheiden waarden).

## <a name="how-do-i-assess-with-the-appliance"></a>Hoe kan ik evalueren met het apparaat?

Als u een Azure Migrate apparaat implementeert om on-premises servers te detecteren, voert u de volgende stappen uit:

1. Stel Azure en uw on-premises omgeving in om met server evaluatie te werken.
2. Voor uw eerste evaluatie maakt u een Azure-project en voegt u het hulp programma voor Server evaluatie toe.
3. Een licht gewicht Azure Migrate implementeren. Het apparaat detecteert voortdurend on-premises machines en verzendt meta gegevens en prestatie gegevens van de machine naar Azure Migrate. Implementeer het apparaat als een virtuele machine. U hoeft niets te installeren op computers die u wilt beoordelen.

Nadat het apparaat machine Discovery heeft gestart, kunt u computers die u wilt beoordelen in een groep verzamelen en een evaluatie van de groep uitvoeren met de evaluatie type **Azure VMware-oplossing (AVS)**.

Maak uw eerste evaluatie van de Azure VMware-oplossing (AVS) door de [volgende stappen te volgen.](how-to-create-azure-vmware-solution-assessment.md)

## <a name="how-do-i-assess-with-imported-data"></a>Hoe kan ik evalueren met geïmporteerde gegevens?

Als u servers met behulp van een CSV-bestand wilt beoordelen, hebt u geen apparaat nodig. Voer in plaats daarvan de volgende stappen uit:

1. Stel in dat Azure wordt gebruikt voor de evaluatie van de server.
2. Voor uw eerste evaluatie maakt u een Azure-project en voegt u het hulp programma voor Server evaluatie toe.
3. Een CSV-sjabloon downloaden en er Server gegevens aan toevoegen.
4. Importeer de sjabloon in de server evaluatie.
5. Ontdek servers die zijn toegevoegd met de import, verzamel ze in een groep en voer een evaluatie uit voor de groep met een evaluatie type van **Azure VMware-oplossing (AVS)**.

## <a name="what-data-does-the-appliance-collect"></a>Welke gegevens worden door het apparaat verzameld?

Als u het Azure Migrate-apparaat gebruikt voor evaluatie, kunt u meer informatie vinden over de meta gegevens en prestatie gegevens die voor [VMware](migrate-appliance.md#collected-data---vmware)worden verzameld.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hoe worden prestatie gegevens berekend op het apparaat?

Als u het apparaat voor detectie gebruikt, worden de prestatie gegevens voor de reken instellingen verzameld met de volgende stappen:

1. Het apparaat verzamelt een real-time voor beeld van een punt.

    - **VMware-vm's**: een voor beeld van een punt wordt elke 20 seconden verzameld.

2. Het apparaat combineert de voorbeeld punten om elke 10 minuten één gegevens punt te maken. Het apparaat selecteert de piek waarden van alle voor beelden om het gegevens punt te maken. Vervolgens wordt het gegevens punt naar Azure verzonden.
3. In Server beoordeling worden alle gegevens punten van 10 minuten voor de afgelopen maand opgeslagen.
4. Wanneer u een evaluatie maakt, identificeert server assessment het juiste gegevens punt dat moet worden gebruikt voor supportte. Identificatie is gebaseerd op de percentiel waarden voor de *prestatie geschiedenis* en het *percentiel gebruik*.

    - Als de prestatie geschiedenis bijvoorbeeld één week is en het percentiel gebruik het 95e percentiel is, sorteert server beoordeling de 10-minuten steekproef punten voor de afgelopen week. Ze worden in oplopende volg orde gesorteerd en de 95e percentiel waarde voor supportte wordt gekozen.
    - De 95e percentiel waarde zorgt ervoor dat u alle uitbijters negeert, die mogelijk worden opgenomen als u het 99e percentiel hebt gekozen.
    - Als u het piek gebruik voor de periode wilt kiezen en u geen uitbijters wilt missen, selecteert u het 99e percentiel voor percentiel gebruik.

5. Deze waarde wordt vermenigvuldigd met de comfort factor om de effectief prestatie gebruiks gegevens te verkrijgen voor de volgende meet waarden die het apparaat verzamelt:

    - CPU-gebruik
    - RAM-gebruik
    - Schijf-IOPS (lezen en schrijven)
    - Schijf doorvoer (lezen en schrijven)
    - Netwerk doorvoer (in-en uitgaand)

De volgende prestatie gegevens worden verzameld, maar niet gebruikt in aanbevelingen voor het aanpassen van de AVS-evaluaties:
  - De schijf-IOPS en doorvoer gegevens voor elke schijf die aan de VM is gekoppeld.
  - De netwerk-I/O voor het afhandelen van de grootte van de prestaties voor elke netwerk adapter die is gekoppeld aan een virtuele machine.

## <a name="how-are-avs-assessments-calculated"></a>Hoe worden AVS-evaluaties berekend?

Server assessment maakt gebruik van de meta gegevens en prestaties van on-premises machines om evaluaties te berekenen. Als u het Azure Migrate apparaat implementeert, gebruikt de evaluatie de gegevens die door het apparaat worden verzameld. Maar als u een evaluatie uitvoert die is geïmporteerd met behulp van een CSV-bestand, geeft u de meta gegevens voor de berekening op.

In deze drie fasen worden berekeningen uitgevoerd:

1. **Gereedheid voor de Azure VMware-oplossing (AVS) berekenen**: of de on-premises vm's geschikt zijn voor migratie naar de Azure VMware-oplossing (AVS).
2. **Bereken het aantal AVS-knoop punten en het gebruik van verschillende knoop punten**: het geschatte aantal AVS-knoop punten dat is vereist voor het uitvoeren van de vm's en geprojecteerde CPU, geheugen en opslag gebruik op alle knoop punten.
3. **Schatting van maandelijkse kosten**: de geschatte maandelijkse kosten voor alle knoop punten van de Azure VMware-oplossing (AVS) die de on-premises vm's uitvoeren.

Berekeningen worden in de voor gaande volg orde beschreven. Een machine server wordt alleen naar een latere fase verplaatst als deze de voor gaande wordt door gegeven. Als een server bijvoorbeeld uitvalt op de AVS-gereedheids fase, wordt deze gemarkeerd als niet geschikt voor Azure. De berekening van het formaat en de kosten voor die server wordt niet uitgevoerd

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Wat is een Azure VMware-oplossing (AVS)-evaluatie?

Dit is what's opgenomen in een AVS-evaluatie in Server evaluatie:


| **Eigenschap** | **Details** 
| - | - 
| **Doellocatie** | Hiermee geeft u de automatische AVS-Cloud locatie op waarnaar u wilt migreren.<br/><br/> AVS-evaluatie in Server evaluatie ondersteunt momenteel deze doel regio's: VS-Oost, Europa-west, VS-West. 
| **Opslagtype** | Hiermee geeft u de opslag engine moet worden gebruikt in AVS.<br/><br/> AVS-evaluaties ondersteunen alleen vSAN als een standaard type opslag. 
**Gereserveerde instanties (RIs)** | Met deze eigenschap kunt u gereserveerde instanties in AVS opgeven. RIs wordt momenteel niet ondersteund voor AVS-knoop punten. 
**Knooppunt type** | Hiermee geeft u het [AVS-knooppunt type](../azure-vmware/concepts-private-clouds-clusters.md) op dat wordt gebruikt om de on-premises vm's toe te wijzen. Het standaard knooppunttype is AV36. <br/><br/> Azure Migrate wordt een vereist aantal knoop punten aanbevolen voor de virtuele machines die moeten worden gemigreerd naar AVS. 
**FTT-instelling, RAID-niveau** | Hiermee geeft u de toepasselijke fout op voor verdragen en RAID-combi Naties. De geselecteerde FTT-optie in combi natie met de on-premises VM-schijf vereiste bepaalt de totale vSAN-opslag die is vereist in AVS. 
**Criterium voor het aanpassen van de grootte** | Hiermee stelt u de criteria in die moeten worden gebruikt voor de *juiste grootte* van VM'S voor AVS. U kunt kiezen voor een hoge grootte op *basis van prestaties* of *als on-premises,* zonder rekening te houden met de prestatie geschiedenis. 
**Prestatiegeschiedenis** | Hiermee stelt u de duur in waarmee rekening moet worden gehouden bij het evalueren van de prestatie gegevens van machines. Deze eigenschap is alleen van toepassing wanneer de grootte criteria *op basis van prestaties* zijn. 
**Percentiel gebruik** | Hiermee geeft u de percentiel waarde van de voorbereidings Voorbeeldset op die in aanmerking komt voor de juiste grootte. Deze eigenschap is alleen van toepassing wanneer de grootte van het formaat op basis van prestaties is gebaseerd.
**Comfortfactor** | Bij het evalueren van Azure Migrate-server wordt een buffer (comfort factor) in rekening gehouden tijdens de evaluatie. Deze buffer wordt toegepast boven op de gegevens over machinegebruik voor VM's (CPU, geheugen, schijf en netwerk). De comfortfactor houdt rekening met factoren zoals seizoensgebonden gebruik, een korte prestatiegeschiedenis en een mogelijke gebruikstoename in de toekomst.<br/><br/> Een VM met 10 kernen en een gebruik van 20% komt bijvoorbeeld gewoonlijk overeen met een VM met 2 kernen. Met een comfortfactor van 2,0x is het resultaat echter een VM met 4 kernen. 
**Aanbieding** | Hier wordt de [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) weer gegeven die u hebt Inge schreven. Azure Migrate maakt dienovereenkomstig een schatting van de kosten.
**Valuta** | Hier wordt de facturerings valuta voor uw account weer gegeven. 
**Korting (%)** | Een lijst met alle abonnements kortingen die boven op de Azure-aanbieding worden weer gegeven. De standaardinstelling is 0%. 
**Azure Hybrid Benefit** | Hiermee geeft u op of u Software Assurance hebt en in aanmerking komt voor [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Hoewel het geen invloed heeft op de prijzen van Azure VMware-oplossingen vanwege de prijs op basis van knoop punten, kunnen klanten nog steeds de on-premises OS-licenties (op basis van micro soft) in AVS Toep assen met behulp van hybride Azure-voor delen. Andere leveranciers van software-besturings systemen moeten hun eigen licentie voorwaarden opgeven, bijvoorbeeld RHEL. 
**vCPU overabonnement** | Hiermee geeft u de verhouding van het aantal virtuele kernen aan dat is gekoppeld aan één fysieke kern in het AVS-knoop punt. De standaard waarde in de berekeningen is 4 vCPU: 1 fysieke kern in AVS. <br/><br/> API-gebruikers kunnen deze waarde instellen als een geheel getal. Houd er rekening mee dat vCPU-overabonnement > 4:1 van invloed kan zijn op werk belastingen, afhankelijk van het CPU-gebruik. 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Analyse van Azure VMware-oplossing (AVS)

AVS-evaluaties in Server evaluatie evalueren elke on-premises Vm's voor de geschiktheid van de AVS door de computer eigenschappen te controleren. Er wordt ook elke geraamde machine toegewezen aan een van de volgende geschiktheids Categorieën:

- **Gereed voor AVS**: de machine kan worden gemigreerd naar Azure (AVS) zonder wijzigingen. Deze wordt gestart in AVS met volledige AVS-ondersteuning.
- **Klaar met voor waarden**: de virtuele machine heeft mogelijk compatibiliteits problemen met de huidige vSphere-versie en vereist mogelijk VMware-hulpprogram ma's en of andere instellingen voordat de volledige functionaliteit van de virtuele machine kan worden bereikt in AVS.
- **Niet gereed voor AVS**: de VM wordt niet gestart in AVS. Als de on-premises VMware VM bijvoorbeeld een extern apparaat heeft dat is aangesloten zoals een cd-rom, mislukt de VMware VMotion bewerking (als u VMware VMotion gebruikt).
- **Gereedheid onbekend**: Azure migrate kan de gereedheid van de machine niet bepalen vanwege onvoldoende meta gegevens die zijn verzameld uit de on-premises omgeving.

Server beoordeling controleert de computer eigenschappen om de Azure-gereedheid van de on-premises machine te bepalen.

### <a name="machine-properties"></a>Computer eigenschappen

Server beoordeling controleert de volgende eigenschap van de on-premises VM om te bepalen of deze kan worden uitgevoerd op de Azure VMware-oplossing (AVS).


| **Eigenschap** | **Details** | **Status van AVS-gereedheid** 
| - | - | - 
| **Internetprotocol** | AVS biedt momenteel geen ondersteuning voor IPv6-internetadressen.<br/><br/> Neem contact op met het lokale MSFT AVS GBB-team voor hulp bij herstelrichtlijnen als op uw machine IPv6 wordt gedetecteerd.| Voorwaardelijk gereed Internet Protocol


### <a name="guest-operating-system"></a>Gastbesturingssysteem

Op dit moment wordt het besturings systeem niet door AVS-evaluaties gecontroleerd als onderdeel van de geschiktheids analyse. Alle besturings systemen die worden uitgevoerd op on-premises Vm's, kunnen waarschijnlijk worden uitgevoerd op de Azure VMware-oplossing (AVS).

Naast de VM-eigenschappen controleert de server evaluatie op het gast besturingssysteem van de computers om te bepalen of het op Azure kan worden uitgevoerd.


## <a name="sizing"></a>Grootte aanpassen

Nadat een computer is gemarkeerd als gereed voor AVS, maakt AVS-evaluatie in Server evaluatie aanbevelingen voor het aanpassen van knoop punten, waarbij de juiste vereisten voor on-premises virtuele machines moeten worden geïdentificeerd en het totale aantal genummerde AVS-knoop punten moet worden gevonden. Deze aanbevelingen variëren, afhankelijk van de opgegeven evaluatie-eigenschappen.

- Als de evaluatie gebruikmaakt van *grootte op basis van prestaties*, wordt de prestatie geschiedenis van de machine door Azure migrate beschouwd als de juiste grootte aanbeveling voor AVS. Deze methode is vooral nuttig als u de on-premises VM hebt toegewezen, maar het gebruik is laag en u de grootte van de virtuele machine in AVS wilt besparen. Deze methode helpt u bij het optimaliseren van de grootten tijdens de migratie.
- Als u de prestatie gegevens voor de grootte van de virtuele machine niet wilt overwegen en de on-premises machines wilt laten geavs, kunt u de grootte criteria instellen op *als on-premises*. Vervolgens wordt de grootte van de virtuele machines gebaseerd op de on-premises configuratie zonder rekening te houden met de gebruiks gegevens. 


### <a name="ftt-sizing-parameters"></a>FTT-formaat parameters

De opslag-engine die in AVS wordt gebruikt, is vSAN. vSAN opslag beleid definieert opslag vereisten voor uw virtuele machines. Deze beleidsregels garanderen het vereiste serviceniveau voor VM’s, omdat ze bepalen hoe opslag wordt toegewezen aan de VM. De beschik bare FTT-Raid combinaties zijn: 

**FTT (te tolereren fouten)** | **RAID-configuratie** | **Minimumaantal vereiste hosts** | **Overwegingen voor de grootte**
--- | --- | --- | --- 
1 | RAID-1 (spiegelen) | 3 | Een VM van 100 GB verbruikt 200 GB.
1 | RAID-5 (verwijdering coderen) | 4 | Een VM van 100 GB verbruikt 133,33 GB
2 | RAID-1 (spiegelen) | 5 | Een VM van 100 GB verbruikt 300 GB.
2 | RAID-6 (verwijdering coderen) | 6 | Een VM van 100 GB verbruikt 150 GB.
3 | RAID-1 (spiegelen) | 7 | Een VM van 100 GB verbruikt 400 GB.

### <a name="performance-based-sizing"></a>Grootte op basis van prestaties

Voor een hoge grootte op basis van prestaties begint de server analyse met de schijven die zijn gekoppeld aan de virtuele machine, gevolgd door netwerk adapters. Vervolgens worden de vereisten voor de virtuele machine toegewezen aan een geschikte aantal knoop punten voor AVS. De Azure Migrate apparaat profielen de on-premises omgeving voor het verzamelen van prestatie gegevens voor CPU, geheugen, schijven en netwerk adapters.

**Stappen voor het verzamelen van prestatie gegevens:**

1. Voor virtuele VMware-machines verzamelt het Azure Migrate-apparaat een realtime-steekproef punt bij elke 20-seconden interval. 
2. Het apparaat rolt de sample punten die elke 10 minuten worden verzameld samen en verzendt de maximum waarde voor de laatste tien minuten naar server evaluatie.
3. In de server beoordeling worden de voorbeeld punten van 10 minuten voor de afgelopen maand opgeslagen. Afhankelijk van de evaluatie-eigenschappen die zijn opgegeven voor de *prestatie geschiedenis* en het *percentiel gebruik*, wordt het juiste gegevens punt aangegeven dat moet worden gebruikt voor de juiste grootte. Als de prestatie geschiedenis bijvoorbeeld is ingesteld op 1 dag en het percentiel gebruik het 95e percentiel is, gebruikt server assessment de 10-minuten steek proeven voor de afgelopen dag, worden deze in oplopende volg orde gesorteerd en wordt de waarde van het 95e percentiel voor rechts formaat gekozen.
4. Deze waarde wordt vermenigvuldigd met de comfort factor om de effectief prestatie gebruiks gegevens te verkrijgen voor elke metriek (CPU-gebruik, geheugen gebruik, schijf-IOPS (lezen en schrijven), schijf doorvoer (lezen en schrijven) en netwerk doorvoer (in en uit) die het apparaat verzamelt.

Nadat de waarde voor effectief gebruik is vastgesteld, worden de opslag, het netwerk en de computer grootte als volgt afgehandeld.

**Opslag grootte**: Azure migrate gebruikt de totale on-PREMISes VM-schijf ruimte als een berekenings parameter voor het bepalen van de vSAN-opslag vereisten voor AVS, naast de door de klant geselecteerde FTT-instelling. FTT: als u wilt verdragen en de optie minimum aantal knoop punten per FTT vereist, bepaalt u de totale hoeveelheid vSAN-opslag die is vereist in combi natie met de vereiste voor de VM-schijf.

**Netwerkgroottes aanpassen**: Bij Server-evaluatie worden momenteel geen netwerkinstellingen in overweging genomen voor AVS-evaluaties.

**Berekenings grootte**: nadat de opslag vereisten zijn berekend, beschouwt de server evaluatie aan de CPU-en geheugen vereisten om het aantal knoop punten te bepalen dat is vereist voor de AVS op basis van het knooppunt type.

- Op basis van de grootte criteria bekijkt de server evaluatie op de op prestaties gebaseerde VM-gegevens of de on-premises VM-configuratie. Met de instelling comfort factor kunt u de groei factor van het cluster opgeven. Momenteel is hyper-threading standaard ingeschakeld, en daarom bevatten 36 kernknooppunten 72 vCores. Er worden 4 vCores per fysiek knooppunt gebruikt om de CPU-drempelwaarden per cluster te bepalen, met behulp van de VMware-standaard voor een maximaal gebruik van 80%, voor onderhoud of fouten die moeten worden verwerkt zonder de beschikbaarheid van clusters te compromitteren. Er is momenteel geen onderdrukking beschikbaar voor het wijzigen van de waarden voor het aantal abonnementen. Dit kan in toekomstige versies.

### <a name="as-on-premises-sizing"></a>Als een on-premises grootte

Als u gebruikmaakt *van on-premises grootte*, wordt de prestatie geschiedenis van de vm's en schijven niet door de server bepaling beschouwd. In plaats daarvan wijst het de AVS-knoop punten toe op basis van de grootte die lokaal is toegewezen. Het standaard type opslag is vSAN in AVS.

Meer [informatie](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vmware-solution#review-an-assessment) over het controleren van de evaluatie van een Azure VMware-oplossing.

## <a name="confidence-ratings"></a>Betrouwbaarheids classificaties

Elke evaluatie op basis van prestaties in Azure Migrate is gekoppeld aan een betrouwbaarheids classificatie die van één (laagste) tot vijf sterren (hoogst) ligt.

- De betrouwbaarheidsclassificatie wordt aan een evaluatie toegewezen op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.
- De betrouwbaarheidsclassificatie van een evaluatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte die Azure Migrate geeft.
- Betrouwbaarheids classificaties zijn niet van toepassing *op de on-premises* evaluaties.
- Voor de grootte van op basis van prestaties heeft AVS-evaluaties in Server evaluatie de gebruiks gegevens voor CPU-en VM-geheugen nodig. De volgende gegevens worden verzameld, maar niet gebruikt in aanbevelingen voor het aanpassen van de AVS:
  - De schijf-IOPS en doorvoer gegevens voor elke schijf die aan de VM is gekoppeld.
  - De netwerk-I/O voor het afhandelen van de grootte van de prestaties voor elke netwerk adapter die is gekoppeld aan een virtuele machine.

  Als een van deze gebruiks nummers niet beschikbaar is in vCenter Server, is de aanbeveling voor de grootte mogelijk niet betrouwbaar.

Afhankelijk van het percentage beschik bare gegevens punten, gaat de betrouwbaarheids classificatie voor de evaluatie als volgt.


| **Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie** |
| - | - |
| 0-20% | 1 ster |
| 21-40% | 2 sterren |
| 41-60% | 3 sterren |
| 61-80% | 4 sterren |
| 81-100% | 5 sterren |

### <a name="low-confidence-ratings"></a>Classificaties met lage betrouw baarheid

Hier volgen enkele redenen waarom een evaluatie een lage betrouwbaarheids classificatie kan krijgen:

- U hebt uw omgeving niet in het profiel voor de duur waarvoor u de evaluatie maakt. Als u bijvoorbeeld de beoordeling met de prestatie duur hebt ingesteld op één dag, moet u ten minste één dag wachten nadat u de detectie hebt gestart voor alle gegevens punten die u wilt verzamelen.
- De evaluatie kan de prestatie gegevens voor sommige of alle virtuele machines in de evaluatie periode niet verzamelen. Voor een hoge betrouwbaarheids classificatie moet u het volgende doen: 
    - Vm's worden ingeschakeld voor de duur van de evaluatie
    - Uitgaande verbindingen op poort 443 zijn toegestaan
    - Voor virtuele Hyper-V-machines is dynamisch geheugen ingeschakeld 
    
    Bereken de evaluatie opnieuw om de meest recente wijzigingen in de betrouwbaarheidsclassificatie weer te geven.

- Sommige Vm's zijn gemaakt tijdens de periode waarin de evaluatie is berekend. Stel dat u een evaluatie hebt gemaakt voor de prestatie geschiedenis van de afgelopen maand, maar dat sommige Vm's slechts een week geleden zijn gemaakt. In dit geval zijn er voor de hele periode geen prestatiegegevens van de nieuwe VM’s beschikbaar, waardoor de betrouwbaarheidsclassificatie laag is.

> [!NOTE]
> Als de betrouwbaarheids classificatie van een evaluatie van minder dan vijf sterren is, raden we u aan om ten minste een dag te wachten op het apparaat om de omgeving in te stellen en de evaluatie vervolgens opnieuw te berekenen. Als dat niet het geval is, is de grootte van de prestaties mogelijk niet betrouwbaar. In dat geval raden wij u aan de evaluatie over te scha kelen naar de on-premises grootte.

## <a name="monthly-cost-estimation"></a>Schatting maandelijkse kosten

Nadat de aanbevelingen voor de grootte zijn voltooid, berekent Azure Migrate de totale kosten van het uitvoeren van de on-premises workloads in AVS door het aantal AVS-knoop punten te vermenigvuldigen dat is vereist voor de prijs van het knoop punt. De kosten per VM-kosten worden berekend door de totale kosten te delen door het aantal Vm's in de evaluatie. 
- De berekening heeft het aantal knoop punten dat is vereist, het type knoop punt en de locatie in het account.
- Hiermee worden de kosten op alle knoop punten geaggregeerd om de totale maandelijkse kosten te berekenen.
- De kosten worden weer gegeven in de valuta die is opgegeven in de instellingen voor evaluatie.

Naarmate de prijzen voor de Azure VMware-oplossing (AVS) per knoop punt zijn, heeft de totale kosten geen reken kosten en de distributie van de opslag kosten. [Meer informatie](../azure-vmware/introduction.md)

Houd er rekening mee dat als de preview-versie van Azure VMware wordt weer gegeven, de prijzen van het knoop punt in de evaluatie preview-prijzen zijn. Neem contact op met uw lokale GBB-team van MSFT AVS voor hulp.

## <a name="migration-tool-guidance"></a>Richt lijnen voor hulp programma voor migratie

In het Azure-gereedheidsrapport voor AVS-evaluatie (Azure VMware Solution) ziet u de volgende voorgestelde hulpprogramma’s: 
- **VMware HCX of ENTER prise**: voor VMware-machines is de VMware Hybrid Cloud extension (HCX)-oplossing het aanbevolen migratie programma voor het migreren van uw on-premises werk belasting naar uw Azure VMware-oplossing (AVS) Private Cloud. [Meer informatie](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Onbekend**: Voor machines die zijn geïmporteerd via een CSV-bestand, is het standaardhulpprogramma voor migratie onbekend. Hoewel voor VMware-machines, wordt aanbevolen de VMware Hybrid Cloud extension (HCX)-oplossing te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Een evaluatie maken voor [AVS VMware-vm's](how-to-create-azure-vmware-solution-assessment.md).
