---
title: Automatische AVS-evaluatie berekeningen in Azure Migrate | Microsoft Docs
description: Hierin wordt een overzicht gegeven van de AVS-evaluatie berekeningen in de Azure Migrate-service.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 1d9918786b22faddaeb07a12f0840b36a11ffe4d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104778378"
---
# <a name="assessment-overview-migrate-to-azure-vmware-solution"></a>Overzicht van evaluatie (migreren naar Azure VMware-oplossing)

[Azure migrate](migrate-services-overview.md) biedt een centrale hub voor het bijhouden van de detectie, beoordeling en migratie van uw on-premises apps en workloads. Ook worden uw persoonlijke en open bare Cloud exemplaren in azure bijgehouden. De hub biedt Azure Migrate-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV-aanbiedingen (Independent Software Vendor) van derden.

Het hulp programma detectie en evaluatie in Azure Migrate evalueert on-premises servers voor migratie naar Azure virtual machines en de Azure VMware-oplossing (AVS). Dit artikel bevat informatie over hoe de evaluaties van de Azure VMware-oplossing (AVS) worden berekend.

> [!NOTE]
> De evaluatie van de Azure VMware-oplossing (AVS) kan alleen worden gemaakt voor virtuele VMware-machines.

## <a name="types-of-assessments"></a>Typen evaluaties

Evaluaties die u met Azure Migrate maakt, zijn een tijdgebonden moment opname van gegevens. Er zijn twee soorten evaluaties die u kunt maken met behulp van Azure Migrate:

**Evaluatietype** | **Details**
--- | --- 
**Azure VM** | Evaluaties om uw on-premises servers te migreren naar virtuele Azure-machine. U kunt uw on-premises servers in [VMware](how-to-set-up-appliance-vmware.md) en [Hyper-V-](how-to-set-up-appliance-hyper-v.md) omgeving, en [fysieke servers](how-to-set-up-appliance-physical.md) voor migratie naar virtuele Azure-machines evalueren met dit beoordelings type.
**Azure SQL** | Beoordelingen voor het migreren van uw on-premises SQL-servers vanuit uw VMware-omgeving naar Azure SQL Database of Azure SQL Managed instance.
**Azure VMware Solution (AVS)** | Evaluaties om uw on-premises servers te migreren naar [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). U kunt uw on-premises [VMware-VM’s](how-to-set-up-appliance-vmware.md) evalueren voor migratie naar Azure VMware Solution (AVS) met dit evaluatietype. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)

De evaluatie van de Azure VMware-oplossing (AVS) biedt twee opties voor het aanpassen van de grootte:

| **Evaluatie** | **Details** | **Gegevens** |
| - | - | - |
| **Op basis van prestaties** | Evaluaties op basis van verzamelde prestatie gegevens van on-premises Vm's. | **Aanbevolen knooppunt grootte**: op basis van het CPU-en geheugen gebruik gegevens, samen met het knooppunt type, het opslag type en de FTT-instelling die u voor de evaluatie selecteert. |
| **Zoals on-premises** | Evaluaties op basis van on-premises grootte aanpassen. | **Aanbevolen knooppunt grootte**: op basis van de on-PREMISes VM-grootte samen met het knooppunt type, het opslag type en de FTT-instelling die u voor de evaluatie selecteert. |

## <a name="how-do-i-run-an-assessment"></a>Hoe kan ik een evaluatie uit te voeren?

Er zijn een aantal manieren om een evaluatie uit te voeren.

- Evalueer servers met behulp van server meta gegevens die zijn verzameld door een licht gewicht Azure Migrate apparaat. Het apparaat detecteert on-premises servers. Vervolgens worden de meta gegevens en prestatie gegevens van de server naar Azure Migrate verzonden. Dit maakt nauw keuriger.
- Evalueer servers met behulp van server meta gegevens die worden geïmporteerd in een CSV-indeling (door lijst scheidings tekens gescheiden waarden).

## <a name="how-do-i-assess-with-the-appliance"></a>Hoe kan ik evalueren met het apparaat?

Als u een Azure Migrate apparaat implementeert om on-premises servers te detecteren, voert u de volgende stappen uit:

1. Stel Azure en uw on-premises omgeving in om met Azure Migrate te werken.
2. Voor uw eerste evaluatie maakt u een Azure-project en voegt u het hulp programma voor detectie en evaluatie toe.
3. Een licht gewicht Azure Migrate implementeren. Het apparaat detecteert voortdurend on-premises servers en verzendt meta gegevens en prestatie gegevens van de server naar Azure Migrate. Implementeer het apparaat als een virtuele machine. U hoeft niets te installeren op servers die u wilt beoordelen.

Nadat het apparaat de detectie van de server heeft gestart, kunt u de servers die u wilt beoordelen in een groep verzamelen en een evaluatie van de groep met de evaluatie typen **Azure VMware-oplossing (AVS)** uitvoeren.

Maak uw eerste evaluatie van de Azure VMware-oplossing (AVS) door de [volgende stappen te volgen.](how-to-create-azure-vmware-solution-assessment.md)

## <a name="how-do-i-assess-with-imported-data"></a>Hoe kan ik evalueren met geïmporteerde gegevens?

Als u servers met behulp van een CSV-bestand wilt beoordelen, hebt u geen apparaat nodig. Voer in plaats daarvan de volgende stappen uit:

1. Stel Azure in om te werken met Azure Migrate.
2. Voor uw eerste evaluatie maakt u een Azure-project en voegt u het hulp programma voor detectie en evaluatie toe.
3. Een CSV-sjabloon downloaden en er Server gegevens aan toevoegen.
4. Importeer de sjabloon in Azure Migrate.
5. Ontdek servers die zijn toegevoegd met de import, verzamel ze in een groep en voer een evaluatie uit voor de groep met een evaluatie type van **Azure VMware-oplossing (AVS)**.

## <a name="what-data-does-the-appliance-collect"></a>Welke gegevens worden door het apparaat verzameld?

Als u het Azure Migrate-apparaat gebruikt voor evaluatie, kunt u meer informatie vinden over de meta gegevens en prestatie gegevens die voor [VMware](migrate-appliance.md#collected-data---vmware)worden verzameld.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Hoe worden prestatie gegevens berekend op het apparaat?

Als u het apparaat voor detectie gebruikt, worden de prestatie gegevens voor de reken instellingen verzameld met de volgende stappen:

1. Het apparaat verzamelt een real-time voor beeld van een punt.

   - **VMware-vm's**: een voor beeld van een punt wordt elke 20 seconden verzameld.
2. Het apparaat combineert de voorbeeld punten om elke 10 minuten één gegevens punt te maken. Het apparaat selecteert de piek waarden van alle voor beelden om het gegevens punt te maken. Vervolgens wordt het gegevens punt naar Azure verzonden.
3. Azure Migrate worden alle gegevens punten van 10 minuten voor de afgelopen maand opgeslagen.
4. Wanneer u een evaluatie maakt, identificeert de evaluatie het juiste gegevens punt dat moet worden gebruikt voor supportte. Identificatie is gebaseerd op de percentiel waarden voor de *prestatie geschiedenis* en het *percentiel gebruik*.

   - Als de prestatie geschiedenis bijvoorbeeld één week is en het percentiel gebruik het 95e percentiel is, sorteert de evaluatie de 10-minuten steekproef punten voor de afgelopen week. Ze worden in oplopende volg orde gesorteerd en de 95e percentiel waarde voor supportte wordt gekozen.
   - De 95e percentiel waarde zorgt ervoor dat u alle uitbijters negeert, die mogelijk worden opgenomen als u het 99e percentiel hebt gekozen.
   - Als u het piek gebruik voor de periode wilt kiezen en u geen uitbijters wilt missen, selecteert u het 99e percentiel voor percentiel gebruik.
5. Deze waarde wordt vermenigvuldigd met de comfort factor om de effectief prestatie gebruiks gegevens te verkrijgen voor de volgende meet waarden die het apparaat verzamelt:

   - CPU-gebruik
   - RAM-gebruik

De volgende prestatie gegevens worden verzameld, maar niet gebruikt in aanbevelingen voor het aanpassen van de AVS-evaluaties:

- De schijf-IOPS en doorvoer gegevens voor elke schijf die aan de VM is gekoppeld.
- De netwerk-I/O voor het afhandelen van de grootte van de prestaties voor elke netwerk adapter die is gekoppeld aan een virtuele machine.

## <a name="how-are-avs-assessments-calculated"></a>Hoe worden AVS-evaluaties berekend?

AVS-evaluatie maakt gebruik van de meta gegevens en prestaties van de on-premises servers om evaluaties te berekenen. Als u het Azure Migrate apparaat implementeert, gebruikt de evaluatie de gegevens die door het apparaat worden verzameld. Maar als u een evaluatie uitvoert die is geïmporteerd met behulp van een CSV-bestand, geeft u de meta gegevens voor de berekening op.

In deze drie fasen worden berekeningen uitgevoerd:

1. **Gereedheid voor de Azure VMware-oplossing (AVS) berekenen**: of de on-premises vm's geschikt zijn voor migratie naar de Azure VMware-oplossing (AVS).
2. Het **aantal AVS-knoop punten en het gebruik tussen knoop punten berekenen**: het geschatte aantal AVS-knoop punten dat is vereist voor het uitvoeren van de virtuele VMware-machines en geprojecteerde CPU, geheugen en opslag gebruik op alle knoop punten.
3. **Schatting van maandelijkse kosten**: de geschatte maandelijkse kosten voor alle knoop punten van de Azure VMware-oplossing (AVS) die de on-premises vm's uitvoeren.

Berekeningen worden in de voor gaande volg orde beschreven. Een server wordt alleen naar een latere fase verplaatst als deze de voor gaande wordt door gegeven. Als een server bijvoorbeeld uitvalt op de AVS-gereedheids fase, wordt deze gemarkeerd als niet geschikt voor Azure. De berekening van het formaat en de kosten voor die server wordt niet uitgevoerd

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Wat is een Azure VMware-oplossing (AVS)-evaluatie?

Dit is what's opgenomen in een AVS-evaluatie:

| **Eigenschap** | **Details** |
| - | - |
| **Doellocatie** | Hiermee geeft u de automatische AVS-Cloud locatie op waarnaar u wilt migreren. |
| **Opslagtype** | Hiermee geeft u de opslag engine moet worden gebruikt in AVS. AVS biedt momenteel alleen ondersteuning voor vSAN als een standaard-opslag type, maar er zijn meer opslag opties beschikbaar volgens het schema. |
| **Gereserveerde instanties (RIs)** | Met deze eigenschap kunt u gereserveerde instanties in AVS opgeven als deze zijn gekocht en de voor waarde van de gereserveerde instantie. Wordt gebruikt om de kosten te berekenen. |
| **Knooppunt type** | Hiermee geeft u het [AVS-knooppunt type](../azure-vmware/concepts-private-clouds-clusters.md) op dat wordt gebruikt in Azure. Het standaard knooppunttype is AV36. Meer knooppunt typen zijn mogelijk in de toekomst beschikbaar.  Azure Migrate wordt een vereist aantal knoop punten aanbevolen voor de virtuele machines die moeten worden gemigreerd naar AVS. |
| **FTT-instelling, RAID-niveau** | Geeft de geldige combi natie van fouten aan om te verdragen en RAID-combi Naties. De geselecteerde FTT-optie in combi natie met RAID-niveau en de on-premises VM-schijf vereiste bepalen de totale vSAN-opslag die is vereist in AVS. De totale hoeveelheid beschik bare opslag na berekeningen omvat ook een) ruimte die is gereserveerd voor beheer objecten, zoals vCenter en b), 25% toegestane vertraging van de opslag voor vSAN-bewerkingen. |
| **Criterium voor het aanpassen van de grootte** | Hiermee stelt u de criteria in die moeten worden gebruikt om geheugen-, CPU-en opslag vereisten voor AVS-knoop punten te bepalen. U kunt kiezen voor een hoge grootte op *basis van prestaties* of *als on-premises,* zonder rekening te houden met de prestatie geschiedenis. Als u de optie alleen on-premises wilt selecteren en SHIFT ingedrukt wilt houden. Kies prestaties gebaseerd om de grootte van op gebruik gebaseerd formaat te verkrijgen. |
| **Prestatiegeschiedenis** | Hiermee stelt u de duur in waarmee rekening moet worden gehouden bij het evalueren van de prestatie gegevens van servers. Deze eigenschap is alleen van toepassing wanneer de grootte criteria *op basis van prestaties* zijn. |
| **Percentiel gebruik** | Hiermee geeft u de percentiel waarde van de voorbereidings Voorbeeldset op die in aanmerking komt voor de juiste grootte. Deze eigenschap is alleen van toepassing wanneer de grootte van het formaat op basis van prestaties is gebaseerd. |
| **Comfortfactor** | Tijdens de evaluatie houdt Azure Migrate rekening met een buffer (comfortfactor). Deze buffer wordt toegepast boven op server gebruiks gegevens voor Vm's (CPU, geheugen en schijf). De comfortfactor houdt rekening met factoren zoals seizoensgebonden gebruik, een korte prestatiegeschiedenis en een mogelijke gebruikstoename in de toekomst. Een VM met 10 kernen en een gebruik van 20% komt bijvoorbeeld gewoonlijk overeen met een VM met 2 kernen. Met een comfortfactor van 2,0x is het resultaat echter een VM met 4 kernen. |
| **Aanbieding** | Hier wordt de [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) weer gegeven die u hebt Inge schreven. Azure Migrate maakt dienovereenkomstig een schatting van de kosten. |
| **Valuta** | Hier wordt de facturerings valuta voor uw account weer gegeven. |
| **Korting (%)** | Een lijst met alle abonnements kortingen die boven op de Azure-aanbieding worden weer gegeven. De standaardinstelling is 0%. |
| **Azure Hybrid Benefit** | Hiermee geeft u op of u Software Assurance hebt en in aanmerking komt voor [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/). Hoewel het geen invloed heeft op de prijzen van Azure VMware-oplossingen vanwege de prijs op basis van knoop punten, kunnen klanten nog steeds het on-premises besturings systeem of SQL-licenties (op basis van micro soft) in AVS Toep assen met behulp van hybride Azure-voor delen. Andere leveranciers van software-besturings systemen moeten hun eigen licentie voorwaarden opgeven, bijvoorbeeld RHEL. |
| **vCPU overabonnement** | Hiermee geeft u de verhouding van het aantal virtuele kernen aan dat is gekoppeld aan één fysieke kern in het AVS-knoop punt. De standaard waarde in de berekeningen is 4 vCPU: 1 fysieke kern in AVS. API-gebruikers kunnen deze waarde instellen als een geheel getal. Houd er rekening mee dat vCPU-overabonnement > 4:1 van invloed kan zijn op werk belastingen, afhankelijk van het CPU-gebruik. Bij het verg Roten/verkleinen wordt altijd gebruikgemaakt van 100% gebruik van de gekozen kernen. |
| **Factor geheugen overdoorvoer** | Hiermee geeft u de verhouding van geheugen over door Voer op het cluster. Een waarde van 1 betekent 100% geheugen gebruik, 0,5 bijvoorbeeld 50%, en 2 maakt gebruik van 200% van het beschik bare geheugen. U kunt alleen waarden van 0,5 tot en met 10 tot één decimaal positie toevoegen. |
| **Ontdubbeling en compressie factor** | Hiermee geeft u de verwachte ontdubbeling en compressie factor voor uw workloads. De werkelijke waarde kan worden verkregen via on-premises vSAN of opslag configuratie. Deze zijn afhankelijk van de werk belasting. Een waarde van 3 zou betekenen dat er voor 300 GB schijf ruimte alleen 100 GB mag worden gebruikt. Een waarde van 1 betekent dat u geen duplicaten of compressie ontdubbelt. U kunt alleen waarden van 1 tot en met 10 tot één decimaal positie toevoegen. |

## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Analyse van Azure VMware-oplossing (AVS)

AVS-evaluaties evalueren elke on-premises Vm's voor de geschiktheid van de AVS door de server eigenschappen te controleren. Ook wordt elke geraamde server toegewezen aan een van de volgende geschiktheids Categorieën:

- **Gereed voor AVS**: de server kan zonder wijzigingen worden gemigreerd naar Azure (AVS). Deze wordt gestart in AVS met volledige AVS-ondersteuning.
- **Klaar met voor waarden**: de virtuele machine heeft mogelijk compatibiliteits problemen met de huidige vSphere-versie en vereist mogelijk VMware-hulpprogram ma's en of andere instellingen voordat de volledige functionaliteit van de virtuele machine kan worden bereikt in AVS.
- **Niet gereed voor AVS**: de VM wordt niet gestart in AVS. Als de on-premises VMware VM bijvoorbeeld een extern apparaat heeft dat is aangesloten zoals een cd-rom, mislukt de VMware VMotion bewerking (als u VMware VMotion gebruikt).
- **Gereedheid onbekend**: Azure migrate kan de gereedheid van de server niet bepalen vanwege onvoldoende meta gegevens die zijn verzameld uit de on-premises omgeving.

De evaluatie controleert de eigenschappen van de server om de Azure-gereedheid van de on-premises server vast te stellen.

### <a name="server-properties"></a>Servereigenschappen

De evaluatie controleert de volgende eigenschap van de on-premises VM om te bepalen of deze kan worden uitgevoerd op de Azure VMware-oplossing (AVS).

| **Eigenschap** | **Details** | **Status van AVS-gereedheid** |
| - | - | - |
| **Internetprotocol** | Azure biedt momenteel geen ondersteuning voor end-to-end IPv6-Internet adressen. Neem contact op met uw lokale MSFT AVS GBB-team voor hulp bij herstel richtlijnen als uw server wordt gedetecteerd met IPv6. | Voorwaardelijk gereed Internet Protocol |

### <a name="guest-operating-system"></a>Gastbesturingssysteem

Op dit moment wordt het besturings systeem niet door AVS-evaluaties gecontroleerd als onderdeel van de geschiktheids analyse. Alle besturings systemen die worden uitgevoerd op on-premises Vm's, kunnen waarschijnlijk worden uitgevoerd op de Azure VMware-oplossing (AVS).

Naast de VM-eigenschappen controleert de evaluatie het gast besturingssysteem van de servers om te bepalen of het op Azure kan worden uitgevoerd.

## <a name="sizing"></a>Grootte aanpassen

Nadat een server is gemarkeerd als gereed voor AVS, maakt AVS-evaluatie aanbevelingen voor het aanpassen van knoop punten, waarbij de juiste vereisten voor on-premises virtuele machines moeten worden geïdentificeerd en het totale aantal genummerde AVS-knoop punten moet worden gevonden. Deze aanbevelingen variëren, afhankelijk van de opgegeven evaluatie-eigenschappen.

- Als de evaluatie gebruikmaakt van *grootte op basis van prestaties*, neemt Azure migrate de prestatie geschiedenis van de server af om de juiste grootte aanbeveling voor AVS te maken. Deze methode is vooral nuttig als u de on-premises VM hebt toegewezen, maar het gebruik is laag en u de grootte van de virtuele machine in AVS wilt besparen. Deze methode helpt u bij het optimaliseren van de grootten tijdens de migratie.
- Als u de prestatie gegevens voor de VM-grootte niet wilt overwegen en de on-premises servers wilt laten door lopen, kunt u de grootte criteria instellen op * als on-premises *. Vervolgens wordt de omvang van de virtuele machines gebaseerd op de on-premises configuratie zonder rekening te houden met de gebruiks gegevens.

### <a name="ftt-sizing-parameters"></a>FTT-formaat parameters

De opslag-engine die in AVS wordt gebruikt, is vSAN. vSAN opslag beleid definieert opslag vereisten voor uw servers. Deze beleidsregels garanderen het vereiste serviceniveau voor VM’s, omdat ze bepalen hoe opslag wordt toegewezen aan de VM. De beschik bare FTT-Raid combinaties zijn:

| **FTT (te tolereren fouten)** | **RAID-configuratie** | **Minimumaantal vereiste hosts** | **Overwegingen voor de grootte** |
| - | - | - | - |
| 1 | RAID-1 (spiegelen) | 3 | Een VM van 100 GB verbruikt 200 GB. |
| 1 | RAID-5 (verwijdering coderen) | 4 | Een VM van 100 GB verbruikt 133,33 GB |
| 2 | RAID-1 (spiegelen) | 5 | Een VM van 100 GB verbruikt 300 GB. |
| 2 | RAID-6 (verwijdering coderen) | 6 | Een VM van 100 GB verbruikt 150 GB. |
| 3 | RAID-1 (spiegelen) | 7 | Een VM van 100 GB verbruikt 400 GB. |

### <a name="performance-based-sizing"></a>Grootte op basis van prestaties

Voor een hoge grootte kunt u met Azure Migrate apparaat de on-premises omgeving profileren voor het verzamelen van prestatie gegevens voor CPU, geheugen en schijf. Op basis van de grootte van op prestaties gebaseerd op de AVS wordt de toegewezen schijf ruimte in aanmerking genomen en wordt het gekozen percentiel gebruik van geheugen en CPU gebruikt. Als een virtuele machine bijvoorbeeld 4vCores heeft toegewezen, maar alleen 25% gebruikt, wordt de grootte van AVS voor 1vCore voor die VM.

**Stappen voor het verzamelen van prestatie gegevens:**

1. Voor virtuele VMware-machines verzamelt het Azure Migrate-apparaat een realtime-steekproef punt bij elke 20-seconden interval.
2. Het apparaat rolt de sample punten samen die elke 10 minuten worden verzameld en verstuurt de maximum waarde voor de laatste tien minuten naar Azure Migrate.
3. Azure Migrate worden alle voorbeeld punten van 10 minuten voor de afgelopen maand opgeslagen. Afhankelijk van de evaluatie-eigenschappen die zijn opgegeven voor de *prestatie geschiedenis* en het *percentiel gebruik*, wordt het juiste gegevens punt aangegeven dat moet worden gebruikt voor de juiste grootte. Als de prestatie geschiedenis bijvoorbeeld is ingesteld op 1 dag en het percentiel gebruik het 95e percentiel is, gebruikt Azure Migrate de 10-minuten steek proeven voor de afgelopen dag, worden deze in oplopende volg orde gesorteerd en wordt de waarde van het 95e percentiel voor de juiste grootte gekozen.
4. Deze waarde wordt vermenigvuldigd met de comfort factor om de effectief prestatie gebruiks gegevens te verkrijgen voor elke metriek (CPU-gebruik en geheugen gebruik) die het apparaat verzamelt.

Nadat de waarde voor effectief gebruik is vastgesteld, worden de opslag, het netwerk en de computer grootte als volgt afgehandeld.

**Opslag grootte**: Azure migrate gebruikt de totale on-PREMISes VM-schijf ruimte als een berekenings parameter voor het bepalen van de vSAN-opslag vereisten voor AVS, naast de door de klant geselecteerde FTT-instelling. FTT: als u wilt verdragen en de optie minimum aantal knoop punten per FTT vereist, bepaalt u de totale hoeveelheid vSAN-opslag die is vereist in combi natie met de vereiste voor de VM-schijf. Voor het gebruik van de opslag wordt momenteel geen rekening gehouden en de logica controleert alleen op toegewezen opslag per VM.

**Netwerk grootte**: AVS-analyses maken momenteel geen rekening met de netwerk instellingen.

**Berekenings grootte**: nadat de opslag vereisten zijn berekend, houdt de AVS-evaluatie rekening met de vereisten voor de CPU en het geheugen om het aantal knoop punten te bepalen dat is vereist voor de AVS op basis van het knooppunt type.

- Op basis van de grootte criteria bekijkt de AVS-evaluatie op de op prestaties gebaseerde VM-gegevens of de on-premises VM-configuratie. Met de instelling comfort factor kunt u de groei factor van het cluster opgeven. Momenteel is hyper-threading standaard ingeschakeld, en daarom bevatten 36 kernknooppunten 72 vCores. Er worden 4 vCores per fysiek knooppunt gebruikt om de CPU-drempelwaarden per cluster te bepalen, met behulp van de VMware-standaard voor een maximaal gebruik van 80%, voor onderhoud of fouten die moeten worden verwerkt zonder de beschikbaarheid van clusters te compromitteren. Er is momenteel geen onderdrukking beschikbaar voor het wijzigen van de waarden voor het aantal abonnementen. Dit kan in toekomstige versies.

### <a name="as-on-premises-sizing"></a>Als een on-premises grootte

Als u gebruikt *als on-premises grootte*, houdt AVS-evaluatie geen rekening met de prestatie geschiedenis van de vm's en schijven. In plaats daarvan wijst het de AVS-knoop punten toe op basis van de grootte die lokaal is toegewezen. Het standaard type opslag is vSAN in AVS.

Meer [informatie](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment) over het controleren van de evaluatie van een Azure VMware-oplossing.

### <a name="cpu-utilization-on-avs-nodes"></a>CPU-gebruik op AVS-knoop punten

CPU-gebruik veronderstelt 100% van de beschik bare kern geheugens. Om het vereiste aantal knoop punten te verminderen, kan het overzetten van 4:1 worden verhoogd tot 6:1 op basis van de kenmerken van de werk belasting en de on-premises ervaring. In tegens telling tot de schijf, biedt AVS geen limieten voor het CPU-gebruik. het is aan klanten om ervoor te zorgen dat het cluster optimaal presteert, dus als ' dynamisch uitvoeren ' vereist is, moet u dit dienovereenkomstig aanpassen. Als u meer groei ruimte wilt toestaan, kunt u het abonnement verlagen of de waarde voor groei factor verhogen.

CPU-gebruik is ook al accounts voor beheer overhead van vCenter, NSX Manager en andere kleinere bronnen.

### <a name="memory-utilization-on-avs-nodes"></a>Geheugen gebruik op AVS-knoop punten

Geheugen gebruik toont het totale geheugen van alle knoop punten versus vereisten van server of workloads. Het geheugen kan worden geabonneerd en opnieuw door de AVS worden geen limieten ingesteld en de klant kan de optimale cluster prestaties voor hun werk belastingen uitvoeren.

Het geheugen gebruik is ook al accounts voor beheer overhead van vCenter, NSX Manager en andere kleinere bronnen.

### <a name="storage-utilization-on-avs-nodes"></a>Opslag gebruik op AVS-knoop punten

Opslag gebruik wordt berekend op basis van de volgende volg orde:

1. Grootte die vereist is voor virtuele machines (toegewezen als of op basis van gebruikte ruimte)
2. Groei factor Toep assen indien van toepassing
3. Beheer overhead toevoegen en FTT-verhouding Toep assen
4. Ontdubbeling en compressie factor Toep assen
5. Een toegestane vertraging van 25% voor vSAN Toep assen
6. Resulterende beschik bare opslag voor virtuele machines met een totale opslag capaciteit, inclusief beheer overhead.

De beschik bare opslag ruimte op een cluster met drie knoop punten is gebaseerd op het standaard beleid voor opslag dat is RAID-1 en maakt gebruik van een brede inrichting. Bij het berekenen van code ring of RAID-5 bijvoorbeeld, is mini maal 4 knoop punten vereist. Houd er rekening mee dat het opslag beleid door de beheerder moet worden gewijzigd om meer ruimte vrij te maken in AVS.

## <a name="confidence-ratings"></a>Betrouwbaarheids classificaties

Elke evaluatie op basis van prestaties in Azure Migrate is gekoppeld aan een betrouwbaarheids classificatie die van één (laagste) tot vijf sterren (hoogst) ligt.

- De betrouwbaarheidsclassificatie wordt aan een evaluatie toegewezen op basis van de beschikbaarheid van de gegevenspunten die nodig zijn om de evaluatie te berekenen.
- De betrouwbaarheidsclassificatie van een evaluatie helpt u om de betrouwbaarheid in te schatten van de aanbevelingen voor de grootte die Azure Migrate geeft.
- Betrouwbaarheids classificaties zijn niet van toepassing *op de on-premises* evaluaties.
- Voor de grootte van op basis van prestaties hebben AVS-evaluaties de gebruiks gegevens voor CPU-en VM-geheugen nodig. De volgende gegevens worden verzameld, maar niet gebruikt in aanbevelingen voor het aanpassen van de AVS:

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

- U hebt uw omgeving niet geprofileerd gedurende de periode waarvoor u de evaluatie maakt. Als u bijvoorbeeld de beoordeling met de prestatie duur hebt ingesteld op één dag, moet u ten minste één dag wachten nadat u de detectie hebt gestart voor alle gegevens punten die u wilt verzamelen.
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

## <a name="migration-tool-guidance"></a>Richt lijnen voor hulp programma voor migratie

In het Azure-gereedheidsrapport voor AVS-evaluatie (Azure VMware Solution) ziet u de volgende voorgestelde hulpprogramma’s:

- **VMware HCX of ENTER prise**: voor VMware-servers is de VMware Hybrid Cloud extension (HCX)-oplossing het aanbevolen migratie programma voor het migreren van uw on-premises werk belasting naar uw Azure VMware-oplossing (AVS) Private Cloud. [Meer informatie](../azure-vmware/tutorial-deploy-vmware-hcx.md).
- **Onbekend**: voor servers die worden geïmporteerd via een CSV-bestand, is het standaard hulp programma voor migratie onbekend. Voor VMware-servers is het echter raadzaam de VMware Hybrid Cloud extension (HCX)-oplossing te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Een evaluatie maken voor [AVS VMware-vm's](how-to-create-azure-vmware-solution-assessment.md).
