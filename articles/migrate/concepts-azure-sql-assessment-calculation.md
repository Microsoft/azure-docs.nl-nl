---
title: Azure SQL-evaluaties in Azure Migrate hulp programma voor detectie en evaluatie
description: Meer informatie over Azure SQL-evaluaties in Azure Migrate hulp programma voor detectie en evaluatie
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: a22fa184f91cb409f7a4d7795a4bc34bdd83e598
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077802"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>Overzicht van evaluatie (migreren naar Azure SQL)

In dit artikel vindt u een overzicht van de evaluaties voor het migreren van on-premises SQL Server exemplaren van een VMware-omgeving naar Azure SQL-data bases of beheerde exemplaren met behulp van het [hulp programma Azure migrate: detectie en evaluatie](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

## <a name="whats-an-assessment"></a>Wat is een evaluatie?
Een evaluatie met het hulp programma detectie en evaluatie is een moment opname van gegevens en meet de gereedheid en schat het effect van het migreren van on-premises servers naar Azure.

## <a name="types-of-assessments"></a>Typen evaluaties

Er zijn drie soorten evaluaties die u kunt maken met behulp van de Azure Migrate: hulp programma voor detectie en evaluatie.

**Evaluatietype** | **Details**
--- | --- 
**Azure VM** | Evaluaties om uw on-premises servers te migreren naar virtuele Azure-machine. <br/><br/> U kunt uw on-premises servers in [VMware](how-to-set-up-appliance-vmware.md) en [Hyper-V-](how-to-set-up-appliance-hyper-v.md) omgeving, en [fysieke servers](how-to-set-up-appliance-physical.md) voor migratie naar virtuele Azure-machines evalueren met dit beoordelings type.
**Azure SQL** | Beoordelingen voor het migreren van uw on-premises SQL-servers vanuit uw VMware-omgeving naar Azure SQL Database of Azure SQL Managed instance.
**Azure VMware Solution (AVS)** | Evaluaties om uw on-premises servers te migreren naar [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> U kunt uw on-premises [VMware-VM’s](how-to-set-up-appliance-vmware.md) evalueren voor migratie naar Azure VMware Solution (AVS) met dit evaluatietype. [Meer informatie](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Als het aantal Azure VM-of AVS-evaluaties onjuist is in het hulp programma detectie en evaluatie, klikt u op het totale aantal evaluaties om naar alle evaluaties te gaan en de Azure VM-of AVS-evaluaties opnieuw te berekenen. In het hulp programma detectie en evaluatie wordt vervolgens het juiste aantal voor dat evaluatie type weer gegeven. 

Een Azure SQL-evaluatie biedt één formaat criterium:

**Criteria voor het aanpassen van de grootte** | **Details** | **Gegevens**
--- | --- | ---
**Op basis van prestaties** | Evaluaties die aanbevelingen doen op basis van verzamelde prestatiegegevens | De Azure SQL-configuratie is gebaseerd op prestatie gegevens van SQL-instanties en data bases, waaronder: CPU-gebruik, geheugen gebruik, IOPS (gegevens en logboek bestanden), door Voer en latentie van i/o-bewerkingen.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>Hoe kan ik evalueren van mijn on-premises SQL-servers?

U kunt uw on-premises SQL Server exemplaren evalueren door gebruik te maken van de configuratie en het gebruik van gegevens die zijn verzameld door een licht gewicht Azure Migrate apparaat. Het apparaat detecteert on-premises SQL Server-exemplaren en-data bases en verzendt de configuratie-en prestatie gegevens naar Azure Migrate. [Meer informatie](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Hoe kan ik evalueren met het apparaat?
Als u een Azure Migrate apparaat implementeert om on-premises servers te detecteren, voert u de volgende stappen uit:
1.  Stel Azure en uw on-premises omgeving in om met Azure Migrate te werken.
2.  Voor uw eerste evaluatie maakt u een Azure Migrate project en voegt u Azure Migrate het hulp programma voor detectie en evaluatie toe.
3.  Een licht gewicht Azure Migrate implementeren. Het apparaat detecteert voortdurend on-premises servers en verzendt configuratie-en prestatie gegevens naar Azure Migrate. Implementeer het apparaat als een virtuele machine of een fysieke server. U hoeft niets te installeren op servers die u wilt beoordelen.

Nadat het apparaat is gedetecteerd, kunt u servers die u wilt beoordelen in een groep verzamelen en een evaluatie uitvoeren voor de groep met het evaluatie type **Azure SQL**.

Volg de zelf studie voor het beoordelen van [SQL Server instanties](tutorial-assess-sql.md) om deze stappen uit te voeren.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>Hoe berekent het apparaat prestatie gegevens voor SQL-exemplaren en-data bases?

Het apparaat verzamelt prestatie gegevens voor reken instellingen met de volgende stappen:
1. Het apparaat verzamelt een real-time voor beeld van een punt. Voor SQL-servers wordt een voor beeld van een punt elke 30 seconden verzameld.
2. Het apparaat aggregeert de gegevens punten die elke 30 seconden worden verzameld over tien minuten. Het apparaat selecteert de piek waarden van alle voor beelden om het gegevens punt te maken. Hiermee wordt het maximum, gemiddelde en de variantie voor elk prestatie meter item naar Azure verzonden.
3. Azure Migrate worden alle gegevens punten van 10 minuten voor de afgelopen maand opgeslagen.
4. Wanneer u een evaluatie maakt, identificeert Azure Migrate het juiste gegevens punt dat moet worden gebruikt voor supportte. Identificatie is gebaseerd op de percentiel waarden voor de prestatie geschiedenis en het percentiel gebruik.
    - Als de prestatie geschiedenis bijvoorbeeld één week is en het percentiel gebruik het 95e percentiel is, sorteert de evaluatie de 10-minuten steekproef punten voor de afgelopen week. Ze worden in oplopende volg orde gesorteerd en de 95e percentiel waarde voor supportte wordt gekozen.
    - De 95e percentiel waarde zorgt ervoor dat u alle uitbijters negeert, die mogelijk worden opgenomen als u het 99e percentiel hebt gekozen.
    - Als u het piek gebruik voor de periode wilt kiezen en u geen uitbijters wilt missen, selecteert u het 99e percentiel voor percentiel gebruik.
5. Deze waarde wordt vermenigvuldigd met de comfort factor om de effectief prestatie gebruiks gegevens te verkrijgen voor de volgende meet waarden die het apparaat verzamelt:
    - CPU-gebruik (%)
    - Geheugen gebruik (%)
    - I/o van IO/s en schrijf-i/o (gegevens-en logboek bestanden)
    - Gelezen MB/s en schrijf-MB/s (door Voer)
    - Latentie van i/o-bewerkingen

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Welke eigenschappen worden gebruikt voor het maken en aanpassen van een Azure SQL-evaluatie?

Dit is what's opgenomen in de Azure SQL Assessment-eigenschappen:

**Eigenschap** | **Details**
--- | ---
**Doellocatie** | De Azure-regio waarnaar u wilt migreren. Azure SQL-configuratie en aanbevelingen voor kosten zijn gebaseerd op de locatie die u opgeeft.
**Type doel implementatie** | Het doel implementatie type waarvoor u de evaluatie wilt uitvoeren: <br/><br/> Selecteer **Aanbevolen** als u Azure migrate wilt beoordelen van de gereedheid van uw SQL-servers voor de migratie naar Azure SQL mi en Azure SQL DB, en aanbevolen de meest geschikte optie voor de doel implementatie, doellaag, Azure SQL-configuratie en maandelijkse schattingen te kiezen.<br/><br/>Selecteer **Azure SQL DB** als u wilt dat uw SQL-servers alleen worden gemigreerd naar Azure SQL-data bases en de doellaag, de Azure SQL DB-configuratie en maandelijkse schattingen bekijken.<br/><br/>Selecteer **Azure SQL mi** als u wilt dat uw SQL-servers alleen worden gemigreerd naar Azure SQL-data bases en de doel laag, de configuratie van Azure SQL mi en maandelijkse schattingen bekijken.
**Gereserveerde capaciteit** | Hiermee wordt de gereserveerde capaciteit opgegeven, zodat rekening wordt gehouden met kosten ramingen in de evaluatie.<br/><br/> Als u een gereserveerde capaciteits optie selecteert, kunt u geen korting (%) opgeven.
**Criteria voor het aanpassen van de grootte** | Deze eigenschap wordt gebruikt om de grootte van de Azure SQL-configuratie te wijzigen. <br/><br/> De standaard instelling is **gebaseerd op prestaties** , wat betekent dat de evaluatie de SQL Server instanties en data bases prestatie gegevens verzamelt om een optimaal formaat van Azure SQL Managed instance en/of Azure SQL database laag/configuratie aanbeveling aan te bevelen.
**Prestatiegeschiedenis** | Met de prestatie geschiedenis wordt de duur opgegeven die wordt gebruikt wanneer prestatie gegevens worden geëvalueerd.
**Percentiel gebruik** | Percentiel gebruik geeft de percentiel waarde van het voor beeld van de prestaties die wordt gebruikt voor supportte.
**Comfortfactor** | De buffer die wordt gebruikt tijdens de evaluatie. IT-accounts voor problemen zoals seizoen gebruik, korte prestatie geschiedenis en waarschijnlijk toename van toekomstig gebruik.<br/><br/> Zo resulteert een 10-kern exemplaar met 20% gebruik doorgaans in een exemplaar met twee kernen. Met een comfort factor van 2,0 is het resultaat een exemplaar van vier kernen.
**Aanbieding/licentie programma** | De [Azure-aanbieding](https://azure.microsoft.com/support/legal/offer-details/) waarin u bent Inge schreven. Op dit moment kunt u alleen kiezen uit betalen per gebruik en betalen naar gebruik-dev/test. Houd er rekening mee dat u extra korting kunt verkrijgen door gereserveerde capaciteit en Azure Hybrid Benefit boven op de aanbieding voor betalen naar gebruik toe te passen.
**Servicelaag** | De meest geschikte service tier-optie voor uw bedrijfs behoeften voor migratie naar Azure SQL Database en/of Azure SQL Managed instance:<br/><br/>**Aanbevolen** als u wilt dat Azure migrate de beste geschikte servicelaag voor uw servers aanbeveelt. Dit kan algemeen gebruik of bedrijfs kritiek zijn. <br/><br/> **Algemeen** Als u een Azure SQL-configuratie wilt die is ontworpen voor budget gerichte workloads. [Meer informatie](../azure-sql/database/service-tier-general-purpose.md) <br/><br/> **Bedrijfskritiek** Als u een Azure SQL-configuratie wilt die is ontworpen voor workloads met lage latentie met hoge tolerantie voor fouten en snelle failovers. [Meer informatie](../azure-sql/database/service-tier-business-critical.md)
**Valuta** | De facturerings valuta voor uw account.
**Korting (%)** | Alle abonnements kortingen die u boven op de Azure-aanbieding ontvangt. De standaardinstelling is 0%.
**Azure Hybrid Benefit** | Hiermee geeft u op of u al een SQL Server licentie hebt. <br/><br/> Als u dit wel doet en de actieve Software Assurance van SQL Server-abonnementen wordt gebruikt, kunt u voor de Azure Hybrid Benefit aanvragen wanneer u licenties naar Azure brengt.

[Bekijk de aanbevolen procedures voor het](best-practices-assessment.md) maken van een evaluatie met Azure Migrate.

## <a name="calculate-readiness"></a>Gereedheid berekenen

> [!NOTE]
De evaluatie bevat alleen data bases met de status online. Als de database zich in een andere status bevindt, worden de gereedheid, grootte en kostenberekening voor dergelijke databases genegeerd in de evaluatie. Als u dergelijke databases wilt evalueren, wijzigt u de status van de database, en berekent u de evaluatie op een later tijdstip opnieuw.

### <a name="azure-sql-readiness"></a>Gereedheid voor Azure SQL

Azure SQL-gereedheid voor SQL-exemplaren en-data bases is gebaseerd op een functie compatibiliteits controle met Azure SQL Database en Azure SQL Managed instance:
1. De Azure SQL-evaluatie beschouwt de SQL Server exemplaar functies die momenteel worden gebruikt door de bron SQL Server workloads (SQL-Agent taken, gekoppelde servers enz.) en de gebruikers databases schema's (tabellen, weer gaven, triggers, opgeslagen procedures enz.) om compatibiliteits problemen te identificeren.
1. Als er geen compatibiliteits problemen worden gevonden, wordt de gereedheid gemarkeerd als **gereed** voor het type doel implementatie (Azure SQL database of Azure SQL Managed instance)
1. Als er sprake is van niet-essentiële compatibiliteits problemen, zoals gedegradeerde of niet-ondersteunde functies die de migratie naar een specifiek type doel implementatie niet blok keren, wordt de gereedheid gemarkeerd als **gereed** (pictogram Hyper link en blauw) met informatie over **waarschuwingen** en aanbevolen herstel richtlijnen.
1. Als er compatibiliteits problemen zijn die de migratie naar een specifiek type doel implementatie kunnen blok keren, wordt de gereedheid gemarkeerd als **niet gereed** met **probleem** Details en aanbevolen richt lijnen voor herstel.
    - Als er zich zelfs één data base in een SQL-exemplaar bevindt die niet gereed is voor een bepaald type doel implementatie, wordt het exemplaar gemarkeerd als **niet gereed** voor dat implementatie type.
1. Als de detectie nog wordt uitgevoerd of er problemen zijn met de detectie van een SQL-exemplaar of-Data Base, is de gereedheid gemarkeerd als **onbekend** , omdat de evaluatie de gereedheid voor dat SQL-exemplaar niet kan berekenen.

### <a name="recommended-deployment-type"></a>Aanbevolen implementatie type

Als u het type doel implementatie selecteert zoals **Aanbevolen** in de Azure SQL Assessment-eigenschappen, raadt Azure migrate een Azure SQL-implementatie type aan dat compatibel is met uw SQL-exemplaar. Wanneer u migreert naar een door micro soft aanbevolen doel, vermindert u de algehele migratie. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Aanbevolen implementatie type op basis van de gereedheid van Azure SQL

 **Gereedheid voor Azure SQL DB** | **Azure SQL MI-gereedheid** | **Aanbevolen implementatie type** | **Berekende Azure SQL-configuratie en kosten ramingen?**
 --- | --- | --- | --- |
 Gereed | Gereed | Azure SQL DB of <br/>Azure SQL MI | Yes
 Gereed | Niet gereed of<br/> Onbekend | Azure SQL Database | Yes
 Niet gereed of<br/>Onbekend | Gereed | Azure SQL MI | Yes
 Niet gereed | Niet gereed | Mogelijk klaar voor Azure VM | No
 Niet gereed of<br/>Onbekend | Niet gereed of<br/>Onbekend | Onbekend | Nee

> [!NOTE]
> Als het aanbevolen implementatie type is geselecteerd als **Aanbevolen** in de evaluatie-eigenschappen en als de bron SQL server geschikt is voor zowel de Azure SQL DB-Data Base als een Azure SQL Managed instance, wordt door de evaluatie een specifieke optie aanbevolen waarmee u uw kosten optimaliseert en binnen de grenzen van de grootte en de prestaties past.

#### <a name="potentially-ready-for-azure-vm"></a>Mogelijk klaar voor Azure VM

Als het SQL-exemplaar niet gereed is voor Azure SQL Database en Azure SQL Managed instance, wordt het aanbevolen implementatie type gemarkeerd als *mogelijk gereed voor Azure VM*.
- De gebruiker wordt aanbevolen een evaluatie te maken in Azure Migrate met het evaluatie type ' Azure VM ' om te bepalen of de server waarop het exemplaar wordt uitgevoerd, gereed is om te migreren naar een virtuele Azure-machine. Opmerking:
    - Azure VM-evaluaties in Azure Migrate zijn momenteel liften en verschuivingen en worden geen rekening gehouden met de specifieke prestatie gegevens voor het uitvoeren van SQL-exemplaren en-data bases op de virtuele machine van Azure. 
    - Wanneer u een Azure VM-evaluatie uitvoert op een server, gelden de aanbevolen grootte en kostenramingen voor alle exemplaren die op de server worden uitgevoerd, en kunnen worden gemigreerd naar een Azure VM via het hulpprogramma voor servermigratie. Voordat u migreert, [raadpleegt u de richtlijnen voor prestaties](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) voor SQL Server op virtuele Azure-machines.


## <a name="calculate-sizing"></a>Grootte berekenen

### <a name="azure-sql-configuration"></a>Azure SQL-configuratie

Nadat de evaluatie de gereedheid en het aanbevolen Azure SQL-implementatie type heeft vastgesteld, worden er een specifieke servicelaag en Azure SQL-configuratie (SKU-grootte) berekend die de prestaties van het on-premises SQL-exemplaar kunnen voldoen of overschrijden:
1. Tijdens het detectie proces verzamelt Azure Migrate SQL-exemplaar configuratie en-prestaties, waaronder:
    - vCores (toegewezen) en CPU-gebruik (%)
        - CPU-gebruik voor een SQL-exemplaar is het percentage toegewezen CPU dat door het exemplaar op de SQL-Server wordt gebruikt
        - CPU-gebruik voor een Data Base is het percentage toegewezen CPU dat wordt gebruikt door de Data Base op het SQL-exemplaar
    - Geheugen (toegewezen) en geheugen gebruik (%)
    - I/o van IO/s en schrijf-i/o (gegevens-en logboek bestanden)
        - Lees-i/o-bewerkingen en schrijf-i/o op het niveau van een SQL-exemplaar worden berekend door het toevoegen van de lees-i/o-en schrijf-i/o van alle data bases die in dat exemplaar zijn gedetecteerd.
    - Gelezen MB/s en schrijf-MB/s (door Voer)
    - Latentie van i/o-bewerkingen
    - De totale grootte van de data base en organisaties van het database bestand
        - De grootte van de data base wordt berekend door alle gegevens en logboek bestanden toe te voegen.
1. De evaluatie aggregeert alle configuratie-en prestatie gegevens en probeert de beste overeenkomst te vinden tussen de verschillende Azure SQL-service lagen en-configuraties, en kiest een configuratie die de prestatie vereisten van het SQL-exemplaar kan overeenkomen of overschrijdt, waardoor de kosten worden geoptimaliseerd.

### <a name="confidence-ratings"></a>Betrouwbaarheids classificaties 
Elke Azure SQL-evaluatie is gekoppeld aan een betrouwbaarheids classificatie. De classificatie varieert van één (laagste) tot vijf (hoogste) sterren. De betrouwbaarheids classificatie helpt u bij het schatten van de betrouw baarheid van de aanbevelingen voor de grootte die Azure Migrate biedt.
- De betrouwbaarheids classificatie wordt toegewezen aan een evaluatie. De classificatie is gebaseerd op de beschik baarheid van gegevens punten die nodig zijn om de evaluatie te berekenen.
- Bij een grootte op basis van prestaties verzamelt de evaluatie prestatie gegevens van alle SQL-exemplaren en-data bases, zoals:
    - CPU-gebruik (%)
    - Geheugen gebruik (%)
    - I/o van IO/s en schrijf-i/o (gegevens-en logboek bestanden)
    - Gelezen MB/s en schrijf-MB/s (door Voer)
    - Latentie van i/o-bewerkingen
    
Als een van deze gebruiks nummers niet beschikbaar is, zijn de aanbevelingen voor de grootte mogelijk onbetrouwbaar.
Deze tabel bevat de beoordelings betrouwbaarheids classificaties die afhankelijk zijn van het percentage beschik bare gegevens punten:

**Beschikbaarheid van gegevenspunten** | **Betrouwbaarheidsclassificatie**
--- | ---
0%-20% | 1 ster
21%-40% | 2 sterren
41%-60% | 3 sterren
61%-80% | 4 sterren
81%-100% | 5 sterren

#### <a name="low-confidence-ratings"></a>Classificaties met lage betrouw baarheid
Hier volgen enkele redenen waarom een evaluatie een lage betrouwbaarheids classificatie kan krijgen:
- U hebt uw omgeving niet geprofileerd gedurende de periode waarvoor u de evaluatie maakt. Als u bijvoorbeeld de beoordeling met de prestatie duur hebt ingesteld op één dag, moet u ten minste één dag wachten nadat u de detectie hebt gestart voor alle gegevens punten die u wilt verzamelen.
- Bij de evaluatie kunnen de prestatiegegevens voor sommige of alle servers in de evaluatieperiode niet worden verzameld. Voor een hoge betrouwbaarheids classificatie moet u het volgende doen:
    - Servers zijn ingeschakeld voor de duur van de evaluatie
    - Uitgaande verbindingen op poort 443 zijn toegestaan
    - Als Azure Migrate verbindings status van de SQL-Agent in Azure Migrate is verbonden en de laatste heartbeat controleren 
    - Of de Azure Migrate-verbindingsstatus voor alle SQL-exemplaren op de blade voor het gedetecteerde SQL-exemplaar, is: Verbonden

    Bereken de evaluatie opnieuw om de meest recente wijzigingen in de betrouwbaarheidsclassificatie weer te geven.
- Sommige data bases of exemplaren zijn gemaakt tijdens de periode waarin de evaluatie is berekend. Stel dat u een evaluatie hebt gemaakt voor de prestatie geschiedenis van de afgelopen maand, maar dat sommige data bases of exemplaren slechts een week geleden zijn gemaakt. In dit geval zijn de prestatie gegevens voor de nieuwe servers niet beschikbaar voor de hele duur en is de betrouwbaarheids classificatie laag.

> [!NOTE]
> Als Azure SQL-evaluaties zijn gebaseerd op prestatie beoordelingen, is het raadzaam om ten minste een dag te wachten op het apparaat om de omgeving te profileren en de beoordeling opnieuw te berekenen als de betrouwbaarheids classificatie van een evaluatie minder dan vijf sterren is. Anders is de op prestaties gebaseerde grootte mogelijk onbetrouwbaar.

## <a name="calculate-monthly-costs"></a>Maandelijkse kosten berekenen
Nadat de aanbevelingen voor de grootte zijn voltooid, berekent Azure SQL assessment de reken-en opslag kosten voor de aanbevolen Azure SQL-configuraties met behulp van een interne prijs-API. De reken-en opslag kosten voor alle exemplaren worden geaggregeerd om de totale maandelijkse reken kosten te berekenen. 
### <a name="compute-cost"></a>Reken kosten
- Voor het berekenen van de reken kosten voor een Azure SQL-configuratie beschouwt de evaluatie de volgende eigenschappen:
    - Azure Hybrid Benefit voor SQL-licenties
    - Gereserveerde capaciteit
    - Azure-doel locatie
    - Valuta
    - Aanbieding/licentie programma
    - Korting (%)

### <a name="storage-cost"></a>Opslagkosten
- De schattingen voor de opslag kosten omvatten alleen gegevens bestanden en geen logboek bestanden. 
- Voor de berekening van de opslag kosten voor een Azure SQL-configuratie beschouwt de evaluatie de volgende eigenschappen:
    - Azure-doel locatie
    - Valuta
    - Aanbieding/licentie programma
    - Korting (%)
- De kosten voor back-upopslag zijn niet inbegrepen bij de evaluatie.
- **Azure SQL Database**
    - Er worden mini maal 5 GB opslag kosten toegevoegd aan de kosten raming en extra opslag kosten worden toegevoegd voor opslag in stappen van 1 GB. [Meer informatie](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Azure SQL Managed Instance**
    - Er zijn geen opslag kosten toegevoegd voor de eerste 32 GB/exemplaar/maand opslag en extra opslag kosten worden toegevoegd voor opslag in 32 GB. [Meer informatie](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>Volgende stappen
- [Bekijk](best-practices-assessment.md) de aanbevolen procedures voor het maken van evaluaties. 
- Meer informatie over het uitvoeren van een [Azure SQL-evaluatie](how-to-create-azure-sql-assessment.md).