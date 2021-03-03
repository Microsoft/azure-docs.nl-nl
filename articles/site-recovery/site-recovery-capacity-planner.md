---
title: De capaciteit plannen voor herstel na nood geval met Azure Site Recovery
description: Gebruik dit artikel voor het schatten van de capaciteit bij het instellen van herstel na nood gevallen met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: aa96845a2bd2f4659da1155c4f75281268dce76d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737712"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Capaciteit plannen voor herstel na noodgevallen van Hyper-V-VM's 

De implementatie van [Azure Site Recovery Deployment Planner] (site-recovery-hyper-v-deployment-planner.md) voor Hyper-V naar Azure biedt het volgende:

* Beoordeling van de geschiktheid van de VM, op basis van het aantal schijven, schijf grootte, IOPS, verloop en enkele VM-kenmerken
* Vereiste netwerkbandbreedte versus RPO-evaluatie
* Vereisten voor Azure-infrastructuur
* On-premises infrastructuurvereisten
* Hulp bij eerste replicatie via batch
* Geschatte totale kosten voor herstel na nood geval naar Azure


Azure Site Recovery Capacity Planner helpt u bij het bepalen van de capaciteits vereisten wanneer u virtuele Hyper-V-machines repliceert met Azure Site Recovery.

Gebruik Site Recovery Capacity Planner om uw bron omgeving en workloads te analyseren. Het helpt u bij het schatten van bandbreedte behoeften, de Server bronnen die u nodig hebt voor de bron locatie en de resources (zoals Vm's en opslag) die u nodig hebt op de doel locatie.

U kunt het hulp programma uitvoeren in twee modi:

* **Snelle planning**: voorziet in netwerk-en server projectie op basis van een gemiddeld aantal vm's, schijven, opslag en wijzigings frequentie.
* **Gedetailleerde planning**: bevat details over elke werk belasting op het niveau van de virtuele machine. Analyseer de compatibiliteit van de virtuele machine en haal netwerk-en server projecties op.

## <a name="before-you-start"></a>Voordat u begint

* Verzamel informatie over uw omgeving, met inbegrip van Vm's, schijven per VM, opslag per schijf.
* Bepaal uw dagelijkse wijzigings frequentie (verloop) voor gerepliceerde gegevens. Down load het [hulp programma voor capaciteits planning voor Hyper-V](https://www.microsoft.com/download/details.aspx?id=39057) om de wijzigings frequentie te verkrijgen. [Meer informatie](./hyper-v-deployment-planner-overview.md) over dit hulpprogramma. U wordt aangeraden dit hulp programma over een week uit te voeren om de gemiddelden vast te leggen.


## <a name="run-the-quick-planner"></a>De snelle planner uitvoeren
1. Down load en open [Site Recovery capacity planner](/samples/browse/?redirectedfrom=TechNet-Gallery). U moet macro's uitvoeren. Wanneer u hierom wordt gevraagd, selecteert u selecties om bewerken en inhoud in te scha kelen.

2. Selecteer in de keuze lijst **een planner type selecteren** de optie **snelle planner**.

   ![Scherm afbeelding van de optie type van planner selecteren, waarbij snelle planner is geselecteerd.](./media/site-recovery-capacity-planner/getting-started.png)

3. Voer de vereiste gegevens in op het werk blad **capacity planner** . Vul alle velden in die rood in de volgende scherm afbeelding worden omcirkeld:

   a. In **Selecteer uw scenario** kiest u **Hyper-V naar Azure** of **VMware/fysiek naar Azure**.

   b. Geef de **gemiddelde dagelijkse gegevens wijzigings frequentie (%)** op door de gegevens die u verzamelt met het [hulp programma voor capaciteits planning voor Hyper-V](./hyper-v-deployment-planner-overview.md) of [site Recovery Deployment planner](./site-recovery-deployment-planner.md).

   c. De **compressie** -instelling wordt niet gebruikt wanneer u virtuele Hyper-V-machines naar Azure repliceert. Gebruik voor compressie een apparaat van derden, zoals Riverbed.

   d. In **dagen** bewaren geeft u in dagen op hoe lang replica's moeten worden bewaard.

   e. In het **aantal uren waarin de initiële replicatie voor de batch van virtuele machines moet worden voltooid** en het **aantal virtuele machines per initiële replicatie batch**, voert u de instellingen in die worden gebruikt voor het berekenen van de initiële replicatie vereisten. Wanneer Site Recovery is geïmplementeerd, wordt de volledige initiële gegevensset geüpload.

   ![Scherm afbeelding van het Capacity Planner werk blad, met daarin de vereiste invoer gegevens.](./media/site-recovery-capacity-planner/inputs.png)

4. Nadat u de waarden voor de bron omgeving hebt opgegeven, bevat de weer gegeven uitvoer:

   * **Band breedte vereist voor replicatie van verschillen (in megabits/sec.)**: de netwerk bandbreedte voor Delta replicatie wordt berekend op basis van de gemiddelde dagelijkse gegevens wijzigings frequentie.
   * **Band breedte die is vereist voor de initiële replicatie (in megabits/sec.)**: de netwerk bandbreedte voor de eerste replicatie wordt berekend op basis van de initiële replicatie waarden die u invoert.
   * **Opslag vereist (in GB)**: de totale Azure-opslag vereist.
   * **Totaal aantal IOPS op standaard opslag**: het getal wordt berekend op basis van de 8K IOPS-eenheids grootte op de totale standaard opslag accounts. Voor de snelle planner wordt het getal berekend op basis van alle schijven van de bron-VM en de wijzigings frequentie voor dagelijkse gegevens. Voor de gedetailleerde planner wordt het getal berekend op basis van het totale aantal virtuele machines dat is toegewezen aan standaard virtuele machines van Azure en de gegevens wijzigings frequentie voor deze Vm's.
   * **Aantal vereiste standaard opslag accounts**: het totale aantal standaard opslag accounts dat nodig is om de virtuele machines te beveiligen. Een standaard opslag account kan Maxi maal 20.000 IOPS bevatten voor alle virtuele machines in de standaard opslag. Er wordt Maxi maal 500 IOPS per schijf ondersteund.
   * **Aantal vereiste BLOB-schijven**: het aantal schijven dat is gemaakt in azure Storage.
   * **Aantal vereiste Premium-accounts**: het totale aantal Premium-opslag accounts dat nodig is om de virtuele machines te beveiligen. Voor een bron-VM met hoge IOPS (groter dan 20.000) is een Premium-opslag account vereist. Een Premium-opslag account kan Maxi maal 80.000 IOPS bevatten.
   * **Totaal aantal IOPS op Premium Storage**: het getal wordt berekend op basis van de grootte van de 256-IOPS-eenheid op de totale Premium-opslag accounts. Voor de snelle planner wordt het getal berekend op basis van alle schijven van de bron-VM en de wijzigings frequentie voor dagelijkse gegevens. Voor de gedetailleerde planner wordt het getal berekend op basis van het totale aantal virtuele machines dat is toegewezen aan de Premium Azure-Vm's (DS-en GS-serie) en de gegevens wijzigings frequentie voor deze Vm's.
   * **Aantal vereiste configuratie servers**: hier ziet u hoeveel configuratie servers vereist zijn voor de implementatie.
   * **Aantal vereiste extra proces servers**: geeft aan of extra proces servers vereist zijn, naast de proces server die standaard op de configuratie server wordt uitgevoerd.
   * **100% extra opslag ruimte op de bron**: geeft aan of extra opslag ruimte vereist is op de bron locatie.

      ![Scherm opname van de weer gegeven uitvoer op basis van de opgegeven invoer.](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>De gedetailleerde planner uitvoeren

1. Down load en open [Site Recovery capacity planner](/samples/browse/?redirectedfrom=TechNet-Gallery). U moet macro's uitvoeren. Wanneer u hierom wordt gevraagd, selecteert u selecties om bewerken en inhoud in te scha kelen.

2. Selecteer in **Selecteer een planner type** **gedetailleerde planner** in de keuze lijst.

   ![Scherm afbeelding van de optie type van planner selecteren, waarbij gedetailleerde planner is geselecteerd.](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Voer de vereiste gegevens in op het werk blad **kwalificaties werk belasting** . U moet alle velden die zijn gemarkeerd invullen.

   a. Geef in **processor cores** het totale aantal kernen op een bron server op.

   b. Geef in **geheugen toewijzing (in MB)** de RAM-grootte op van een bron server.

   c. Geef in **aantal nic's** het aantal netwerk adapters op een bron server op.

   d. Geef in **totale opslag (in GB)** de totale grootte van de VM-opslag op. Als de bron server bijvoorbeeld drie schijven heeft met elk 500 GB, is de totale opslag grootte 1.500 GB.

   e. Geef bij **aantal gekoppelde schijven** het totale aantal schijven van een bron server op.

   f. Geef het gemiddelde gebruik op in **schijf capaciteits gebruik (%)**.

   g. In **dagelijkse gegevens wijzigings percentage (%)** geeft u de dagelijkse gegevens wijzigings frequentie van een bron server op.

   h. Voer in **Azure VM-grootte toewijzen** de Azure VM-grootte in die u wilt toewijzen. Als u dit niet hand matig wilt doen, selecteert u **Compute IaaS vm's**. Als u een hand matige instelling invoert en vervolgens **Compute IaaS vm's** selecteert, kan de hand matige instelling worden overschreven. Het reken proces identificeert automatisch de beste overeenkomst op de Azure VM-grootte.

   ![Scherm afbeelding van het werk blad werk belasting kwalificatie, met daarin de vereiste invoer gegevens.](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Als u **Compute IaaS vm's** selecteert, doet u het volgende:

   * Valideert de verplichte invoer.
   * Berekent IOPS en stelt de beste overeenkomst van de Azure VM-grootte voor elke VM die in aanmerking komt voor replicatie naar Azure. Als er geen geschikte grootte voor Azure VM kan worden gedetecteerd, wordt een fout weer gegeven. Als bijvoorbeeld het aantal gekoppelde schijven 65 is, wordt er een fout weer gegeven omdat de hoogste grootte voor een virtuele machine van Azure 64 is.
   * Hiermee wordt een opslag account voorgesteld dat kan worden gebruikt voor een virtuele Azure-machine.
   * Berekent het totale aantal standaard opslag accounts en Premium Storage-accounts die zijn vereist voor de werk belasting. Schuif omlaag om het Azure-opslag type en het opslag account dat kan worden gebruikt voor een bron server weer te geven.
   * Hiermee wordt de rest van de tabel voltooid en gesorteerd op basis van het vereiste opslag type (Standard of Premium) dat is toegewezen aan een virtuele machine en het aantal gekoppelde schijven. Voor alle virtuele machines die voldoen aan de vereisten voor Azure, wordt de kolom aangeduid als een **virtuele machine?** bevat **Ja**. Als er geen back-up van een virtuele machine kan worden gemaakt naar Azure, wordt een fout weer gegeven.

Kolommen AA tot en met AE zijn uitvoer en geven informatie voor elke VM.

![Scherm opname van uitvoer kolommen AA tot en met AE.](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Voorbeeld
Als voor beeld: voor zes Vm's met de waarden die in de tabel worden weer gegeven, worden met het hulp programma de beste overeenkomst voor Azure-VM'S en de vereisten voor Azure Storage berekend en toegewezen.

![Scherm afbeelding met toewijzing van de werk belasting kwalificatie.](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Let op het volgende in de voorbeeld uitvoer:

  * De eerste kolom is een validatie kolom voor de Vm's, schijven en het verloop.
  * Er zijn twee standaard opslag accounts en één Premium-opslag account nodig voor vijf Vm's.
  * VM3 komt niet in aanmerking voor beveiliging omdat een of meer schijven meer dan 1 TB zijn.
  * VM1 en VM2 kunnen het eerste standaard opslag account gebruiken
  * VM4 kan gebruikmaken van het tweede standaard opslag account.
  * VM5 en VM6 hebben een Premium-opslag account nodig en beide kunnen één account gebruiken.

    > [!NOTE]
    > IOPS op standaard-en Premium-opslag wordt berekend op het niveau van de virtuele machine en niet op schijf niveau. Een standaard-VM kan Maxi maal 500 IOPS per schijf verwerken. Als IOPS voor een schijf groter is dan 500, hebt u Premium Storage nodig. Als IOPS voor een schijf groter is dan 500, maar IOPS voor de totale VM-schijven binnen de ondersteunde limieten voor Azure-VM'S liggen, kiest de planner een standaard-VM en niet de DS-of GS-serie. (De limieten voor Azure VM zijn VM-grootte, het aantal schijven, het aantal adapters, de CPU en het geheugen.) U moet de cel toewijzing Azure size hand matig bijwerken met de juiste VM van de DS-of GS-serie.


Nadat alle gegevens zijn ingevoerd, selecteert **u gegevens verzenden naar het hulp programma planner** om capacity planner te openen. Werk belastingen zijn gemarkeerd om aan te tonen of ze in aanmerking komen voor beveiliging.

### <a name="submit-data-in-capacity-planner"></a>Gegevens verzenden in Capacity Planner
1. Wanneer u het werk blad **capacity planner** opent, wordt het ingevuld op basis van de instellingen die u hebt opgegeven. Het woord ' workload ' wordt weer gegeven in de bron cel voor **infra structuur invoer** om aan te geven dat de invoer het werk blad voor **werk belasting kwalificaties** is.

2. Als u wijzigingen wilt aanbrengen, moet u het werk blad **kwalificaties werk belasting** wijzigen. Selecteer vervolgens opnieuw **gegevens verzenden naar het hulp programma planner** .

   ![Scherm opname van de gewijzigde invoer en resulterende uitvoer in het Capacity Planner werk blad.](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het uitvoeren](./hyper-v-deployment-planner-overview.md) van het hulp programma capaciteits planning.