---
title: Een back-up maken van virtuele Hyper-V-machines met MABS
description: Dit artikel bevat de procedures voor het maken van back-up en herstel van virtuele machines met behulp Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: b4de791269161b477fc07d6539feaa975fdd72ad
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739994"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Back-up maken van virtuele Hyper-V-machines met Azure Backup Server

In dit artikel wordt uitgelegd hoe u een back-up maakt van virtuele Hyper-V-machines met Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Ondersteunde scenario's

MABS kan in de volgende scenario's een back-up maken van virtuele machines die worden uitgevoerd op Hyper-V-hostservers:

- **Virtuele machines met lokale of directe opslag**: maak een back-up van virtuele machines die op zelfstandige Hyper-V-servers worden gehost die lokaal of rechtstreeks gekoppelde opslag hebben. Bijvoorbeeld: een harde schijf, een SAN-apparaat (Storage Area Network) of een NAS-apparaat (Network Attached Storage). De MABS-beveiligingsagent moet op alle hosts zijn geïnstalleerd.

- **Virtuele machines in een cluster met CSV-opslag**: maak een back-up van virtuele machines die op een Hyper-V-cluster worden gehost met CSV-opslag (Cluster Shared Volume). De MABS-beveiligingsagent wordt geïnstalleerd op elk clusterknooppunt.

## <a name="host-versus-guest-backup"></a>Host versus gastback-up

MABS kan een back-up op host- of gastniveau maken van Hyper-V-VM's. Op hostniveau wordt de MABS-beveiligingsagent geïnstalleerd op de Hyper-V-hostserver of het hyper-V-cluster en worden de volledige VM's en gegevensbestanden die op die host worden uitgevoerd, beschermd.   Op gastniveau wordt de agent geïnstalleerd op elke virtuele machine en wordt de werkbelasting op die machine beschermd.

Beide methoden hebben voor- en nadelen:

- Back-ups op hostniveau zijn flexibel omdat ze werken ongeacht het type besturingssysteem dat wordt uitgevoerd op de gastmachines en niet de installatie van de MABS-beveiligingsagent op elke virtuele machine vereisen. Als u back-ups op hostniveau implementeert, kunt u een volledige virtuele machine of bestanden en mappen herstellen (herstel op itemniveau).

- Back-ups op gastniveau zijn handig als u specifieke workloads wilt beveiligen die op een virtuele machine worden uitgevoerd. Op hostniveau kunt u een volledige VM of specifieke bestanden herstellen, maar dit biedt geen herstel in de context van een specifieke toepassing. Als u bijvoorbeeld specifieke SharePoint-items wilt herstellen vanaf een back-up van een VM, moet u een back-up op gastniveau van die VM maken. Gebruik back-ups op gastniveau als u gegevens wilt beveiligen die zijn opgeslagen op passthrough-schijven. Met Passthrough heeft de virtuele machine rechtstreeks toegang tot het opslagapparaat en worden virtuele volumegegevens niet opgeslagen in een VHD-bestand.

## <a name="how-the-backup-process-works"></a>Hoe het back-upproces werkt

MABS voert als volgt een back-up uit met VSS. De stappen in deze beschrijving zijn voor de duidelijkheid genummerd.

1. De MABS-engine voor bloksynchronisatie maakt een eerste kopie van de beveiligde virtuele machine en zorgt ervoor dat de kopie van de virtuele machine volledig en consistent is.

2. Nadat de eerste kopie is gemaakt en geverifieerd, gebruikt MABS de VsS Writer van Hyper-V om back-ups vast te leggen. De VSS Writer biedt een gegevens-consistente set schijfblokken die worden gesynchroniseerd met de MABS-server. Deze aanpak biedt het voordeel van een 'volledige back-up' met de MABS-server, terwijl de back-upgegevens die over het netwerk moeten worden overgedragen, worden geminimimeerd.

3. De MABS-beveiligingsagent op een server met Hyper-V gebruikt de bestaande Hyper-V-API's om te bepalen of een beveiligde virtuele machine ook VSS ondersteunt.

   - Als een virtuele machine voldoet aan de vereisten voor online back-ups en als de Hyper-V-integratieservicecomponent erop is geïnstalleerd, stuurt de Hyper-V VSS Writer de VVS-aanvraag recursief door naar alle VSS-processen op de virtuele machine. Deze bewerking treedt op zonder dat de MABS-beveiligingsagent op de virtuele machine is geïnstalleerd. Met de recursieve VSS-aanvraag kan de VSS-writer voor Hyper-V ervoor zorgen dat schijfschrijfbewerkingen gesynchroniseerd zijn, zodat een VSS-momentopname wordt vastgelegd zonder gegevensverlies.

     De Hyper-V-integratieservicescomponent roept de Hyper-V VSS Writer aan in VSS (Volume Shadow Copy Services) op virtuele machines om ervoor te zorgen dat hun toepassingsgegevens zich in een consistente status bevinden.

   - Als de virtuele machine niet voldoet aan online back-upvereisten, gebruikt MABS automatisch de Hyper-V-API's om de virtuele machine te onderbreken voordat ze gegevensbestanden vastleggen.

4. Nadat de eerste basislijnkopie van de virtuele machine wordt gesynchroniseerd met de MABS-server, worden alle wijzigingen die zijn aangebracht in de resources van de virtuele machine vastgelegd in een nieuw herstelpunt. Het herstelpunt vertegenwoordigt de consistente status van de virtuele machine op een specifiek tijdstip. De registratie van het herstelpunt kan minstens één keer per dag plaatsvinden. Wanneer er een nieuw herstelpunt wordt gemaakt, gebruikt MABS replicatie op blokniveau in combinatie met de Hyper-V VSS Writer om te bepalen welke blokken zijn gewijzigd op de server waarop Hyper-V wordt uitgevoerd nadat het laatste herstelpunt is gemaakt. Deze gegevensblokken worden vervolgens overgedragen naar de MABS-server en worden toegepast op de replica van de beveiligde gegevens.

5. De MABS-server gebruikt VSS op de volumes die herstelgegevens hosten, zodat er meerdere schaduwkopieen beschikbaar zijn. Elk van deze schaduwkopieën voorziet in een afzonderlijk herstel. VSS-herstelpunten worden opgeslagen op de MABS-server. De tijdelijke kopie die is gemaakt op de server met Hyper-V wordt alleen opgeslagen voor de duur van de MABS-synchronisatie.

>[!NOTE]
>
>Vanaf Windows Server 2016 hebben virtuele Hyper-V-harde schijven ingebouwde tracering van veranderingen, ook wel resilient change tracking (RCT) genoemd. MABS maakt gebruik van RCT (het systeemeigen bijhouden van veranderingen in Hyper-V), waardoor er minder tijdrovende consistentiecontroles nodig zijn in scenario's zoals VM-crashes. RCT biedt betere tolerantie dan het bijhouden van wijzigingen door de VSS-back-ups op basis van momentopnamen. MABS V3 optimaliseert het netwerk- en opslagverbruik verder door alleen de gewijzigde gegevens over te dragen tijdens eventuele consistentiecontroles.

## <a name="backup-prerequisites"></a>Back-upvereisten

Dit zijn de vereisten voor het maken van back-up van virtuele Hyper-V-machines met MABS:

|Vereiste|Details|
|------------|-------|
|MABS-vereisten|- Als u herstel op itemniveau wilt uitvoeren voor virtuele machines (bestanden, mappen, volumes herstellen), moet u de Hyper-V-functie installeren op de MABS-server.  Als u alleen de virtuele machine wilt herstellen en niet het itemniveau, is de rol niet vereist.<br />- U kunt maximaal 800 virtuele machines van elk 100 GB op één MABS-server beveiligen en meerdere MABS-servers toestaan die ondersteuning bieden voor grotere clusters.<br />- MABS sluit het paginabestand uit van incrementele back-ups om de back-upprestaties van virtuele machines te verbeteren.<br />- MABS kan een back-up maken van een Hyper-V-server of -cluster in hetzelfde domein als de MABS-server, of in een onderliggend of vertrouwd domein. Als u een back-up wilt maken van Hyper-V in een werkgroep of een niet-vertrouwd domein, moet u verificatie instellen. Voor één Hyper-V-server kunt u NTLM of certificaatverificatie gebruiken. Voor een cluster kunt u alleen certificaatverificatie gebruiken.<br />-   Het gebruik van back-ups op hostniveau om van gegevens van virtuele machine back-ups te maken op doorgangsschijven wordt niet ondersteund. In dit scenario raden we u aan om back-ups op hostniveau te gebruiken om een back-up te maken van VHD-bestanden en back-ups op gastniveau om een back-up te maken van de andere gegevens die niet zichtbaar zijn op de host.<br />   -U kunt een back-up maken van VM's die zijn opgeslagen op ontdubbelde volumes.|
|Vereisten voor Hyper-V-VM's|- De versie van Integratieonderdelen die wordt uitgevoerd op de virtuele machine moet gelijk zijn aan de versie van de Hyper-V-host. <br />-   Voor elke back-up van een virtuele machine hebt u vrije ruimte nodig op het volume dat als host fungeert voor de virtuele hardeschijfbestanden, zodat Hyper-V voldoende ruimte heeft voor differentiërende schijven (AVHD's) tijdens de back-up. De ruimte moet minimaal gelijk zijn aan de venstertijdberekening **eerste schijfgrootte\*verloop\*back-up**. Als u meerdere back-ups uitvoert in een cluster, hebt u voldoende opslagcapaciteit nodig voor de AVHD's voor elk van de virtuele machines met deze berekening.<br />- Als u een back-up wilt maken van virtuele machines op Hyper-V-hostservers met Windows Server 2012 R2, moet op de virtuele machine een SCSI-controller zijn opgegeven, zelfs als deze niet is verbonden met iets. (In Windows Server 2012 R2 back-up, de Hyper-V-host een nieuwe VHD in de VM wordt vervolgens ontkoppeld. Alleen de SCSI-controller kan dit ondersteunen en is daarom vereist voor online back-ups van de virtuele machine.  Zonder deze instelling wordt gebeurtenis-id 10103 uitgegeven wanneer u een back-up van de virtuele machine probeert te maken.)|
|Linux-vereisten|- U kunt een back-up maken van virtuele Linux-machines met behulp van MABS. Alleen bestandsconsistente momentopnamen worden ondersteund.|
|Een back-up maken van virtuele machines met CSV-opslag|-   Installeer voor CSV-opslag de VSS-hardwareprovider (Volume Shadow Copy Services) op de Hyper-V-server. Neem contact op met uw SAN-leverancier (storage area network) voor de VSS-hardwareprovider.<br />- Als één knooppunt onverwacht wordt afgesloten in een CSV-cluster, voert MABS een consistentiecontrole uit op de virtuele machines die op dat knooppunt werden uitgevoerd.<br />-   Als u een Hyper-V-server opnieuw moet opstarten waarvoor op het CSV-cluster BitLocker-stationsvergrendeling is ingeschakeld, moet u een consistentiecontrole uitvoeren voor virtuele Hyper-V-machines.|
|Een back-up maken van virtuele machines met SMB-opslag|- Schakel automatisch bevestiging in op de server met Hyper-V om de beveiliging van virtuele machines in teschakelen.<br />   -   Schakel TCP-chimney-offload uit.<br />-   Zorg ervoor dat voor alle Hyper-V-computeraccounts volledige machtigingen zijn toegewezen voor de specifieke externe SMB-bestandsshares.<br />- Zorg ervoor dat het bestandspad voor alle onderdelen van de virtuele machine tijdens het herstellen naar een alternatieve locatie minder is dan 260 tekens. Als dat niet het beste is, slaagt het herstel mogelijk, maar kan hyper-V de virtuele machine niet aan elkaar worden gemonteerd.<br />- De volgende scenario's worden niet ondersteund:<br />     Implementaties waarbij sommige onderdelen van de virtuele machine zich op lokale volumes en sommige onderdelen op externe volumes; een IPv4- of IPv6-adres voor opslaglocatiebestandsserver en herstel van een virtuele machine naar een computer die externe SMB-shares gebruikt.<br />- U moet de VSS-agentservice voor bestandsservers op elke SMB-server inschakelen: voeg deze toe **in** Functies en onderdelen toevoegen Serverfuncties selecteren Bestands- en opslagservices  >    >    >    >    >  **Bestandsservicebestandsserver VSS Agent-service**.|

## <a name="back-up-virtual-machines"></a>Een back-up maken van virtuele machines

1. Stel uw [MABS-server en](backup-azure-microsoft-azure-backup.md) [uw opslag in.](backup-mabs-add-storage.md) Bij het instellen van uw opslag gebruikt u de volgende richtlijnen voor de opslagcapaciteit.
   - Gemiddelde grootte van virtuele machine - 100 GB
   - Aantal virtuele machines per MABS-server - 800
   - Totale grootte van 800 VM's - 80 TB
   - Vereiste ruimte voor back-upopslag - 80 TB

2. De MABS-beveiligingsagent instellen op de Hyper-V-server of Hyper-V-clusterknooppunten.

3. Selecteer in de MABS Administrator-console  >  **Beveiligingsgroep maken om** de wizard Nieuwe **beveiligingsgroep maken te** openen.

4. Selecteer op de pagina **Groepsleden selecteren** de virtuele machines die u wilt beveiligen op de Hyper-V-hostservers waarop deze zich bevinden. We raden u aan alle VM's die hetzelfde beveiligingsbeleid krijgen, in één beveiligingsgroep te plaatsen. U kunt co-locatie inschakelen voor efficiënt gebruik van de ruimte. Met co-locatie kunt u gegevens uit verschillende beveiligingsgroepen op dezelfde schijf of tapeopslag plaatsen, zodat meerdere gegevensbronnen één replica- en herstelpuntvolume hebben.

5. Geef op de pagina **Methode voor gegevensbeveiliging selecteren** een naam op voor de beveiligingsgroep. Selecteer **Ik wil kortetermijnbeveiliging met schijf** en selecteer **Ik wil online beveiliging** als u back-ups wilt maken van gegevens naar Azure met de Azure Backup-dienst.

6. Geef **in Short-Term Bewaartermijn** voor doelstellingen op hoelang u  >  schijfgegevens wilt bewaren. Geef **in Synchronisatiefrequentie** op hoe vaak incrementele back-ups van de gegevens moeten worden uitgevoerd. In plaats van een interval voor incrementele back-ups kunt u ook **Net vóór een herstelpunt** inschakelen. Als deze instelling is ingeschakeld, zal MABS een volledige back-up uitvoeren net vóór elk gepland herstelpunt.

    > [!NOTE]
    >
    >Als u toepassingswerkbelastingen beveiligt, worden herstelpunten gemaakt volgens de synchronisatiefrequentie, mits de toepassing incrementele back-ups ondersteunt. Als dat niet zo is, voert MABS een volledige back-up uit in plaats van een incrementele back-up, en worden er herstelpunten gemaakt volgens het schema voor expressback-ups.<br></br>Het back-upproces maakt geen back-up van de controlepunten die zijn gekoppeld aan VM's.

7. Controleer op **de pagina Schijftoewijzing** controleren de schijfruimte van de opslaggroep die is toegewezen voor de beveiligingsgroep.

   **Totale gegevensgrootte** is de grootte van de gegevens waar u een back-up van wilt maken en schijfruimte die moet worden ingericht op **MABS** is de ruimte die MABS aanbeveelt voor de beveiligingsgroep. MABS kiest het ideale back-upvolume op basis van de instellingen. U kunt echter de keuzes voor het back-upvolume bewerken in **Schijftoewijzingsdetails**. Selecteer voor de werkbelastingen de gewenste opslag in het vervolgkeuzemenu. Met uw bewerkingen worden de waarden voor **Totale opslag** en **Vrije opslagruimte** in het deelvenster **Beschikbare schijfopslag** gewijzigd. Te weinig in te stellen ruimte is de hoeveelheid opslagruimte die MABS adviseert om toe te voegen aan het volume, om in de toekomst probleemloos door te gaan met back-ups.

8. Geef op de pagina **Methode voor maken van replica selecteren** op hoe de initiële replica van gegevens in de beveiligingsgroep wordt uitgevoerd. Als u automatisch **repliceren via het netwerk selecteert,** wordt u aangeraden een daltijd te kiezen. Voor grote hoeveelheden gegevens of minder dan optimale netwerkomstandigheden kunt u handmatig **selecteren.** Hiervoor moet u de gegevens offline repliceren met verwisselbare media.

9. Selecteer op de pagina **Opties voor consistentiecontrole** hoe u consistentiecontroles wilt automatiseren. U kunt instellen dat er alleen een controle wordt uitgevoerd als de gerepliceerde gegevens inconsistent worden, of volgens een planning. Als u geen automatische consistentiecontroles wilt configureren, kunt u op elk gewenst moment een handmatige controle uitvoeren door met de rechtermuisknop op de beveiligingsgroep te klikken en **Consistentiecontrole uitvoeren** te selecteren.

    Wanneer u de beveiligingsgroep hebt gemaakt, wordt er een eerste replica van de gegevens gemaakt met de methode die u hebt geselecteerd. Na de eerste replicatie wordt elke back-up uitgevoerd op basis van de instellingen van de beveiligingsgroep. Als u back-upgegevens wilt herstellen, moet u rekening houden met het volgende:

## <a name="back-up-replica-virtual-machines"></a>Een back-up maken van virtuele replicamachines

Als MABS wordt uitgevoerd op Windows Server 2012 R2 of hoger, kunt u een back-up maken van virtuele replicamachines. Dit is handig om verschillende redenen:

**Back-ups hebben minder invloed op werkbelastingen die worden uitgevoerd**: het maken van een back-up van een virtuele machine leidt tot enige overhead wanneer er een momentopname wordt gemaakt. Door het back-upproces naar een secundaire externe site te offloaden, wordt de werkbelasting die wordt uitgevoerd niet meer beïnvloed door de back-upbewerking. Dit is alleen van toepassing op implementaties waarbij de back-up op een externe site wordt opgeslagen. U kunt bijvoorbeeld dagelijks back-ups maken en gegevens lokaal opslaan om voor snelle hersteltijden te zorgen, maar maandelijkse of driemaandelijkse back-ups maken van virtuele replicamachines die extern worden opgeslagen voor bewaren op de lange termijn.

**Besparing van bandbreedte**: in een gebruikelijke filiaal-/hoofdkantoorimplementatie hebt u een juiste hoeveelheid bandbreedte nodig die is ingericht om back-upgegevens tussen sites over te brengen. Als u naast uw gegevensback-upstrategie een replicatie- en failoverstrategie maakt, kunt u de hoeveelheid redundante gegevens die wordt verzonden via het netwerk, verminderen. Door een back-up te maken van de gegevens van de replica-virtuele machine in plaats van de primaire machine, bespaart u de overhead van het verzenden van back-upgegevens via het netwerk.

**Mogelijkheid van hosterback-ups** -u kunt een gehoste datacenter gebruiken als replicasite zonder dat een secundaire datacenter nodig is. In dit geval vereist de HOSTER-SLA een consistente back-up van virtuele replicamachines.

Een replica-VM is altijd uitgeschakeld totdat een failover wordt gestart en VSS geen toepassingsconsistente back-up voor een replica-VM kan garanderen. De back-up van een virtuele replicamachine is dus alleen crash-consistent. Als er geen crash-consistentie kan worden gegarandeerd, mislukt de back-up. Dit kan verschillende redenen hebben:

- De virtuele replicamachine heeft een kritieke status.

- De virtuele replicamachine synchroniseert opnieuw (heeft de status Hersynchronisatie wordt uitgevoerd of Hersynchronisatie vereist).

- Initiële replicatie tussen de primaire en secundaire site wordt uitgevoerd of is in behandeling voor de virtuele machine.

- .hrl-logboeken worden toegepast op de virtuele replicamachine of een eerdere actie voor het toepassen van de HRL-logboeken op de virtuele schijf is mislukt of is geannuleerd of onderbroken.

- Migratie of failover van de virtuele replicamachine wordt uitgevoerd

## <a name="recover-backed-up-virtual-machines"></a>Back-ups van virtuele machines herstellen

Wanneer u back-ups van virtuele machines wilt herstellen, gebruikt u de wizard Herstellen om de virtuele machine en het specifieke herstelpunt te selecteren. Ga als volg te werk om de wizard Herstellen te openen en een virtuele machine te herstellen:

1. Typ in de MABS Administrator-console de naam van de VM of vouw de lijst met beveiligde items uit, navigeer naar Alle beveiligde **HyperV-gegevens** en selecteer de VM die u wilt herstellen.

2. Selecteer in **het deelvenster Herstelpunten** voor op de kalender een datum om de beschikbare herstelpunten weer te geven. Selecteer vervolgens in het deelvenster **Pad** het herstelpunt dat u wilt gebruiken in de wizard Herstellen.

3. Selecteer herstellen **in het** menu Acties **om** de wizard Herstel te openen.

    De VM en het herstelpunt die u hebt geselecteerd, worden weergegeven in het scherm **Selectie voor herstel controleren**. Selecteer **Next**.

4. Selecteer in **het scherm Hersteltype** selecteren waar u de gegevens wilt herstellen en selecteer **vervolgens Volgende.**

    - **Herstellen naar het oorspronkelijke exemplaar:** wanneer u herstelt naar het oorspronkelijke exemplaar, worden de oorspronkelijke VHD en alle bijbehorende controlepunten verwijderd. MABS herstelt de VHD en andere configuratiebestanden naar de oorspronkelijke locatie met behulp van Hyper-V VSS Writer. Aan het einde van het herstelproces zijn virtuele machines nog steeds maximaal beschikbaar.
        De brongroep moet aanwezig zijn voor het herstel. Als de groep niet beschikbaar is, herstelt u naar een alternatieve locatie en stelt u de virtuele machine maximaal beschikbaar.

    - **Herstellen als virtuele machine** op elke host: MABS ondersteunt herstel van alternatieve locaties (ALR), dat een naadloos herstel van een beveiligde virtuele Hyper-V-machine naar een andere Hyper-V-host biedt, onafhankelijk van de processorarchitectuur. Virtuele Hyper-V-machines die worden hersteld naar een clusterknooppunt, zijn niet zeer beschikbaar. Als u deze optie kiest, biedt de wizard Herstellen u een extra scherm voor het identificeren van het doel en doelpad.
    
        >[!NOTE]
        >Als u de oorspronkelijke host selecteert, is het gedrag hetzelfde als bij **Herstellen naar het oorspronkelijke exemplaar.** De oorspronkelijke VHD en alle bijbehorende controlepunten worden verwijderd.

    - Kopiëren **naar** een netwerkmap: MABS ondersteunt herstel op itemniveau (ILR), waarmee u bestanden, mappen, volumes en virtuele harde schijven (VHD's) kunt herstellen vanaf een back-up op hostniveau van virtuele Hyper-V-machines naar een netwerk share of een volume op een met MABS beveiligde server. De MABS-beveiligingsagent hoeft niet in de gast te worden geïnstalleerd om herstel op itemniveau uit te voeren. Als u deze optie kiest, biedt de wizard Herstellen u een extra scherm voor het identificeren van het doel en doelpad.

5. Configureer **in Herstelopties** opgeven de herstelopties en selecteer **Volgende:**

    - Als u een VM herstelt met een lage bandbreedte, selecteert u **Wijzigen om** beperking van **netwerkbandbreedtegebruik in te stellen.** Nadat u de optie voor beperking hebt ingeschakeld, kunt u de hoeveelheid bandbreedte opgeven die u beschikbaar wilt maken en het tijdstip waarop die bandbreedte beschikbaar is.
    - Selecteer Herstel op basis van SAN inschakelen met behulp van **hardwaremomentopnamen** als u uw netwerk hebt geconfigureerd.
    - Selecteer **Een e-mail verzenden wanneer het herstel is voltooid** en geef vervolgens de e-mailadressen op, als u e-mailmeldingen wilt verzenden zodra het herstelproces is voltooid.

6. Controleer in het scherm Samenvatting of alle gegevens juist zijn. Als de details niet juist zijn of als u een wijziging wilt maken, selecteert u **Terug.** Als u tevreden bent met de instellingen, selecteert u **Herstellen om** het herstelproces te starten.

7. Het scherm **Herstelstatus** bevat informatie over de hersteltaak.

## <a name="next-steps"></a>Volgende stappen

[Gegevens herstellen vanaf Azure Backup Server](./backup-azure-alternate-dpm-server.md)
