---
title: Back-up maken van Hyper-V virtuele machines met MABS
description: Dit artikel bevat de procedures voor het maken van back-ups en het herstellen van virtuele machines met behulp van Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/18/2019
ms.openlocfilehash: c1c894df29659dce312d092f1aa3ea5d584e248a
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107207"
---
# <a name="back-up-hyper-v-virtual-machines-with-azure-backup-server"></a>Back-up maken van virtuele Hyper-V-machines met Azure Backup Server

In dit artikel wordt uitgelegd hoe u een back-up van virtuele Hyper-V-machines maakt met behulp van Microsoft Azure Backup Server (MABS).

## <a name="supported-scenarios"></a>Ondersteunde scenario's

MABS kan een back-up maken van virtuele machines die worden uitgevoerd op Hyper-V-hostservers in de volgende scenario's:

- **Virtuele machines met lokale of directe opslag**: maak een back-up van virtuele machines die op zelfstandige Hyper-V-servers worden gehost die lokaal of rechtstreeks gekoppelde opslag hebben. Bijvoorbeeld: een harde schijf, een Storage Area Network apparaat (SAN) of een NAS-apparaat (Network Attached Storage). De MABS-beveiligings agent moet op alle hosts zijn geïnstalleerd.

- **Virtuele machines in een cluster met CSV-opslag**: maak een back-up van virtuele machines die op een Hyper-V-cluster worden gehost met CSV-opslag (Cluster Shared Volume). De MABS-beveiligings agent wordt op elk cluster knooppunt geïnstalleerd.

## <a name="host-versus-guest-backup"></a>Host versus gast back-up

MABS kan een back-up van Hyper-V-Vm's op een host of gast niveau maken. Op hostniveau wordt de MABS-beveiligings agent geïnstalleerd op de Hyper-V-hostserver of het cluster en worden de volledige Vm's en gegevens bestanden op die host beveiligd.   Op gast niveau wordt de agent geïnstalleerd op elke virtuele machine en wordt de werk belasting die op die computer aanwezig is, beveiligd.

Beide methoden hebben voor- en nadelen:

- Back-ups op hostniveau zijn flexibel omdat ze werken, ongeacht het type besturings systeem dat wordt uitgevoerd op de gast computers en de installatie van de MABS-beveiligings agent op elke virtuele machine niet hoeft te worden geïnstalleerd. Als u back-up op hostniveau implementeert, kunt u een volledige virtuele machine, of bestanden en mappen (herstel op item niveau) herstellen.

- Back-ups op gast niveau zijn handig als u specifieke werk belastingen wilt beveiligen die op een virtuele machine worden uitgevoerd. Op hostniveau kunt u een volledige virtuele machine of specifieke bestanden herstellen, maar dit biedt geen herstel in de context van een bepaalde toepassing. Als u bijvoorbeeld specifieke share point-items van een back-up wilt herstellen, moet u een back-up op gast niveau van die virtuele machine uitvoeren. Gebruik back-up op gast niveau als u gegevens wilt beveiligen die op passthrough-schijven zijn opgeslagen. Met passthrough kan de virtuele machine rechtstreeks toegang krijgen tot het opslag apparaat en worden virtuele volume gegevens niet in een VHD-bestand opgeslagen.

## <a name="how-the-backup-process-works"></a>Hoe het back-upproces werkt

MABS voert een back-up met VSS als volgt uit. De stappen in deze beschrijving zijn voor de duidelijkheid genummerd.

1. De MABS-synchronisatie-engine op basis van blokken maakt een eerste kopie van de beveiligde virtuele machine en zorgt ervoor dat de kopie van de virtuele machine volledig en consistent is.

2. Nadat de eerste kopie is gemaakt en geverifieerd, gebruikt MABS het Hyper-V-VSS Writer voor het vastleggen van back-ups. De VSS Writer biedt een gegevens-consistente set schijf blokken die zijn gesynchroniseerd met de MABS-server. Deze benadering biedt het voor deel van een ' volledige back-up ' met de MABS-server, terwijl de back-upgegevens die over het netwerk moeten worden verzonden, worden geminimaliseerd.

3. De MABS-beveiligings agent op een server waarop Hyper-V wordt uitgevoerd, gebruikt de bestaande Hyper-V-Api's om te bepalen of een beveiligde virtuele machine ook VSS ondersteunt.

   - Als een virtuele machine voldoet aan de vereisten voor online back-ups en als de Hyper-V-integratieservicecomponent erop is geïnstalleerd, stuurt de Hyper-V VSS Writer de VVS-aanvraag recursief door naar alle VSS-processen op de virtuele machine. Deze bewerking wordt uitgevoerd zonder dat de MABS-beveiligings agent op de virtuele machine wordt geïnstalleerd. Met de recursieve VSS-aanvraag kan de VSS-writer voor Hyper-V ervoor zorgen dat schijfschrijfbewerkingen gesynchroniseerd zijn, zodat een VSS-momentopname wordt vastgelegd zonder gegevensverlies.

     De Hyper-V-integratieservicescomponent roept de Hyper-V VSS Writer aan in VSS (Volume Shadow Copy Services) op virtuele machines om ervoor te zorgen dat hun toepassingsgegevens zich in een consistente status bevinden.

   - Als de virtuele machine niet voldoet aan de vereisten voor online back-ups, gebruikt MABS automatisch de Hyper-V-Api's om de virtuele machine te onderbreken voordat de gegevens bestanden worden vastgelegd.

4. Nadat de oorspronkelijke basislijn kopie van de virtuele machine is gesynchroniseerd met de MABS-server, worden alle wijzigingen die worden aangebracht aan de resources van de virtuele machine vastgelegd in een nieuw herstel punt. Het herstelpunt vertegenwoordigt de consistente status van de virtuele machine op een specifiek tijdstip. De registratie van het herstelpunt kan minstens één keer per dag plaatsvinden. Wanneer een nieuw herstel punt wordt gemaakt, gebruikt MABS replicatie op blok niveau in combi natie met de Hyper-V-VSS Writer om te bepalen welke blokken zijn gewijzigd op de server waarop Hyper-V wordt uitgevoerd nadat het laatste herstel punt is gemaakt. Deze gegevens blokken worden vervolgens overgedragen naar de MABS-server en worden toegepast op de replica van de beveiligde gegevens.

5. De MABS-server gebruikt VSS op de volumes die herstel gegevens hosten, zodat er meerdere schaduw kopieën beschikbaar zijn. Elk van deze schaduwkopieën voorziet in een afzonderlijk herstel. VSS-herstel punten worden opgeslagen op de MABS-server. De tijdelijke kopie die wordt gemaakt op de server waarop Hyper-V wordt uitgevoerd, wordt alleen opgeslagen voor de duur van de MABS-synchronisatie.

>[!NOTE]
>
>Vanaf Windows Server 2016 zijn Hyper-V virtuele harde schijven ingebouwd voor het bijhouden van wijzigingen (RCT). MABS maakt gebruik van RCT (het systeem eigen bijhouden van wijzigingen in Hyper-V), waardoor er minder tijdrovende consistentie controles nodig zijn in scenario's zoals VM-crashes. RCT biedt betere tolerantie dan het bijhouden van wijzigingen door de VSS-back-ups op basis van momentopnamen. MABS v3 optimaliseert het netwerk-en opslag verbruik verder door alleen de gewijzigde gegevens tijdens de consistentie controles over te dragen.

## <a name="backup-prerequisites"></a>Back-upvereisten

Dit zijn de vereisten voor het maken van back-ups van virtuele Hyper-V-machines met MABS:

|Vereiste|Details|
|------------|-------|
|Vereisten voor MABS|-Als u herstel op item niveau voor virtuele machines wilt uitvoeren (bestanden, mappen, volumes), moet u de Hyper-V-functie installeren op de MABS-server.  Als u alleen de virtuele machine wilt herstellen en niet op item niveau, dan is de functie niet vereist.<br />-U kunt Maxi maal 800 virtuele machines van 100 GB elk op één MABS-server beveiligen en meerdere MABS-servers toestaan die grotere clusters ondersteunen.<br />-MABS sluit het wissel bestand van incrementele back-ups uit om de back-upprestaties van virtuele machines te verbeteren.<br />-MABS kan een back-up maken van een Hyper-V-Server of-cluster in hetzelfde domein als de MABS-server, of in een onderliggend of vertrouwd domein. Als u een back-up wilt maken van Hyper-V in een werk groep of een niet-vertrouwd domein, moet u verificatie instellen. U kunt voor één Hyper-V-server NTLM of certificaat verificatie gebruiken. Voor een cluster kunt u alleen certificaat verificatie gebruiken.<br />-   Het gebruik van back-ups op hostniveau om van gegevens van virtuele machine back-ups te maken op doorgangsschijven wordt niet ondersteund. In dit scenario raden we u aan om back-up op hostniveau te gebruiken om back-ups te maken van VHD-bestanden en back-ups op gast niveau om back-ups te maken van de andere gegevens die niet zichtbaar zijn op de host.<br />   -U kunt back-ups maken van virtuele machines die zijn opgeslagen op ontdubbelde volumes.|
|Vereisten voor Hyper-V-VM's|-De versie van integratie onderdelen die op de virtuele machine wordt uitgevoerd, moet hetzelfde zijn als de versie van de Hyper-V-host. <br />-   Voor elke back-up van een virtuele machine hebt u vrije ruimte nodig op het volume dat als host fungeert voor de virtuele hardeschijfbestanden, zodat Hyper-V voldoende ruimte heeft voor differentiërende schijven (AVHD's) tijdens de back-up. De ruimte moet minimaal gelijk zijn aan de venstertijdberekening **eerste schijfgrootte\*verloop\*back-up**. Als u meerdere back-ups uitvoert in een cluster, hebt u voldoende opslagcapaciteit nodig voor de AVHD's voor elk van de virtuele machines met deze berekening.<br />-Als u een back-up wilt maken van virtuele machines op Hyper-V-hostservers met Windows Server 2012 R2, moet op de virtuele machine een SCSI-controller zijn opgegeven, zelfs als deze geen verbinding heeft met iets. (In Windows Server 2012 R2-back-up koppelt de Hyper-V-host een nieuwe VHD in de virtuele machine en wordt deze vervolgens later ontkoppeld. Alleen de SCSI-controller kan dit ondersteunen en is daarom vereist voor online back-up van de virtuele machine.  Zonder deze instelling wordt gebeurtenis-ID 10103 uitgegeven wanneer u een back-up van de virtuele machine probeert te maken.)|
|Linux-vereisten|-U kunt back-ups maken van virtuele Linux-machines met behulp van MABS. Alleen bestandsconsistente momentopnamen worden ondersteund.|
|Een back-up maken van virtuele machines met CSV-opslag|-   Installeer voor CSV-opslag de VSS-hardwareprovider (Volume Shadow Copy Services) op de Hyper-V-server. Neem contact op met uw SAN-leverancier (storage area network) voor de VSS-hardwareprovider.<br />-Als één knoop punt onverwacht wordt uitgeschakeld in een CSV-cluster, wordt door MABS een consistentie controle uitgevoerd voor de virtuele machines die op het knoop punt worden uitgevoerd.<br />-   Als u een Hyper-V-server opnieuw moet opstarten waarvoor op het CSV-cluster BitLocker-stationsvergrendeling is ingeschakeld, moet u een consistentiecontrole uitvoeren voor virtuele Hyper-V-machines.|
|Een back-up maken van virtuele machines met SMB-opslag|-Schakel automatisch koppelen in op de server waarop Hyper-V wordt uitgevoerd om de beveiliging van virtuele machines in te scha kelen.<br />   -   Schakel TCP-chimney-offload uit.<br />-   Zorg ervoor dat voor alle Hyper-V-computeraccounts volledige machtigingen zijn toegewezen voor de specifieke externe SMB-bestandsshares.<br />-Zorg ervoor dat het bestandspad voor alle onderdelen van virtuele machines tijdens het herstel naar een alternatieve locatie minder dan 260 tekens is. Als dat niet het geval is, kan het herstel slagen, maar kan de virtuele machine niet worden gekoppeld met Hyper-V.<br />-De volgende scenario's worden niet ondersteund:<br />     Implementaties waarbij sommige onderdelen van de virtuele machine zich op lokale volumes bevinden en sommige onderdelen zich op externe volumes bevinden. een IPv4-of IPv6-adres voor de opslaglocatie Bestands server en het herstellen van een virtuele machine naar een computer die gebruikmaakt van externe SMB-shares.<br />-U moet de File Server VSS Agent-service op elke SMB-server inschakelen: Voeg deze toe in **functies en onderdelen toevoegen**  >  **Selecteer Server functies**  >  **Bestands-en opslag Services** bestands  >  **Services**  >  **Bestands**  >  **Server VSS Agent service**.|

## <a name="back-up-virtual-machines"></a>Een back-up maken van virtuele machines

1. Stel uw [MABS-server](backup-azure-microsoft-azure-backup.md) en [uw opslag](backup-mabs-add-storage.md)in. Bij het instellen van uw opslag gebruikt u de volgende richtlijnen voor de opslagcapaciteit.
   - Gemiddelde grootte van virtuele machine - 100 GB
   - Aantal virtuele machines per MABS-server-800
   - Totale grootte van 800 VM's - 80 TB
   - Vereiste ruimte voor back-upopslag - 80 TB

2. Stel de MABS-beveiligings agent in op de Hyper-V-Server of Hyper-V-cluster knooppunten.

3. Selecteer in de MABS Administrator-console **beveiliging beveiligings**  >  **groep maken** om de wizard **nieuwe beveiligings groep maken** te openen.

4. Selecteer op de pagina **Groepsleden selecteren** de virtuele machines die u wilt beveiligen op de Hyper-V-hostservers waarop deze zich bevinden. We raden u aan alle VM's die hetzelfde beveiligingsbeleid krijgen, in één beveiligingsgroep te plaatsen. U kunt co-locatie inschakelen voor efficiënt gebruik van de ruimte. Met co-locatie kunt u gegevens uit verschillende beveiligingsgroepen op dezelfde schijf of tapeopslag plaatsen, zodat meerdere gegevensbronnen één replica- en herstelpuntvolume hebben.

5. Geef op de pagina **Methode voor gegevensbeveiliging selecteren** een naam op voor de beveiligingsgroep. Selecteer **Ik wil kortetermijnbeveiliging met schijf** en selecteer **Ik wil online beveiliging** als u back-ups wilt maken van gegevens naar Azure met de Azure Backup-dienst.

6. Geef in **Short-Term**  >  **Bewaar periode** voor doelen op hoe lang u de schijf gegevens wilt bewaren. Geef bij **synchronisatie frequentie** op hoe vaak incrementele back-ups van de gegevens moeten worden uitgevoerd. In plaats van een interval voor incrementele back-ups kunt u ook **Net vóór een herstelpunt** inschakelen. Als deze instelling is ingeschakeld, voert MABS een snelle volledige back-up uit net vóór elk gepland herstel punt.

    > [!NOTE]
    >
    >Als u toepassingswerkbelastingen beveiligt, worden herstelpunten gemaakt volgens de synchronisatiefrequentie, mits de toepassing incrementele back-ups ondersteunt. Als dat niet het geval is, voert MABS een snelle volledige back-up uit in plaats van een incrementele back-up en worden er herstel punten gemaakt volgens de planning voor snelle back-ups.<br></br>Het back-upproces maakt geen back-up van de controle punten die zijn gekoppeld aan Vm's.

7. Controleer op de pagina **schijf toewijzing controleren** de schijf ruimte van de opslag groep die is toegewezen aan de beveiligings groep.

   **Totale gegevens grootte** is de grootte van de gegevens waarvan u een back-up wilt maken en **schijf ruimte die moet worden ingericht op MABS** is de ruimte die MABS raadt aan de beveiligings groep. MABS kiest het ideale back-upvolume, op basis van de instellingen. U kunt echter de keuzes voor het back-upvolume bewerken in **Schijftoewijzingsdetails**. Selecteer voor de werkbelastingen de gewenste opslag in het vervolgkeuzemenu. Met uw bewerkingen worden de waarden voor **Totale opslag** en **Vrije opslagruimte** in het deelvenster **Beschikbare schijfopslag** gewijzigd. Onderingerichte ruimte is de hoeveelheid opslag MABS die u toevoegt aan het volume, zodat u in de toekomst probleemloos kunt door gaan met het maken van back-ups.

8. Geef op de pagina **Methode voor maken van replica selecteren** op hoe de initiële replica van gegevens in de beveiligingsgroep wordt uitgevoerd. Als u ervoor kiest om **automatisch via het netwerk te repliceren**, raden we u aan een rustige tijd te kiezen. Voor grote hoeveel heden gegevens of minder dan optimale netwerk omstandigheden, kunt u overwegen om **hand matig** te selecteren. hiervoor moet u de gegevens offline repliceren met behulp van Verwissel bare media.

9. Selecteer op de pagina **Opties voor consistentiecontrole** hoe u consistentiecontroles wilt automatiseren. U kunt instellen dat er alleen een controle wordt uitgevoerd als de gerepliceerde gegevens inconsistent worden, of volgens een planning. Als u geen automatische consistentiecontroles wilt configureren, kunt u op elk gewenst moment een handmatige controle uitvoeren door met de rechtermuisknop op de beveiligingsgroep te klikken en **Consistentiecontrole uitvoeren** te selecteren.

    Wanneer u de beveiligingsgroep hebt gemaakt, wordt er een eerste replica van de gegevens gemaakt met de methode die u hebt geselecteerd. Na de eerste replicatie wordt elke back-up uitgevoerd op basis van de instellingen van de beveiligingsgroep. Als u een back-up van gegevens moet herstellen, let dan op het volgende:

## <a name="back-up-replica-virtual-machines"></a>Een back-up maken van virtuele replicamachines

Als MABS wordt uitgevoerd op Windows Server 2012 R2 of hoger, kunt u een back-up maken van virtuele replica machines. Dit is handig om verschillende redenen:

**Back-ups hebben minder invloed op werkbelastingen die worden uitgevoerd**: het maken van een back-up van een virtuele machine leidt tot enige overhead wanneer er een momentopname wordt gemaakt. Door het back-upproces te offloaden naar een secundaire externe site, wordt de back-upbewerking niet meer beïnvloed door de actieve werk belasting. Dit is alleen van toepassing op implementaties waarbij de back-up op een externe site wordt opgeslagen. U kunt bijvoorbeeld dagelijks back-ups maken en gegevens lokaal opslaan om voor snelle hersteltijden te zorgen, maar maandelijkse of driemaandelijkse back-ups maken van virtuele replicamachines die extern worden opgeslagen voor bewaren op de lange termijn.

**Besparing van bandbreedte**: in een gebruikelijke filiaal-/hoofdkantoorimplementatie hebt u een juiste hoeveelheid bandbreedte nodig die is ingericht om back-upgegevens tussen sites over te brengen. Als u naast uw gegevensback-upstrategie een replicatie- en failoverstrategie maakt, kunt u de hoeveelheid redundante gegevens die wordt verzonden via het netwerk, verminderen. Als u een back-up maakt van de gegevens van de replica virtuele machine in plaats van de primaire, bespaart u de overhead van het verzenden van de gegevens waarvan een back-up is gemaakt via het netwerk.

**Mogelijkheid van hosterback-ups** -u kunt een gehoste datacenter gebruiken als replicasite zonder dat een secundaire datacenter nodig is. In dit geval vereist de SLA voor de Hoster een consistente back-up van virtuele replica machines.

Een replica-VM is altijd uitgeschakeld totdat een failover wordt gestart en VSS geen toepassingsconsistente back-up voor een replica-VM kan garanderen. De back-up van een virtuele replica machine wordt dus alleen crash-consistent. Als er geen crash-consistentie kan worden gegarandeerd, mislukt de back-up. Dit kan verschillende redenen hebben:

- De virtuele replicamachine heeft een kritieke status.

- De virtuele replicamachine synchroniseert opnieuw (heeft de status Hersynchronisatie wordt uitgevoerd of Hersynchronisatie vereist).

- Initiële replicatie tussen de primaire en secundaire site wordt uitgevoerd of is in behandeling voor de virtuele machine.

- . HRL-logboeken worden toegepast op de replica virtuele machine, of een eerdere actie om de. HRL-logboeken op de virtuele schijf toe te passen, is mislukt of is geannuleerd of onderbroken.

- Migratie of failover van de virtuele replicamachine wordt uitgevoerd

## <a name="recover-backed-up-virtual-machines"></a>Back-ups van virtuele machines herstellen

Wanneer u back-ups van virtuele machines wilt herstellen, gebruikt u de wizard Herstellen om de virtuele machine en het specifieke herstelpunt te selecteren. Ga als volg te werk om de wizard Herstellen te openen en een virtuele machine te herstellen:

1. Typ in de MABS Administrator-console de naam van de virtuele machine of vouw de lijst met beveiligde items uit en selecteer de virtuele machine die u wilt herstellen.

2. Selecteer in het deel venster **herstel punten voor** in de agenda een wille keurige datum om de beschik bare herstel punten weer te geven. Selecteer vervolgens in het deelvenster **Pad** het herstelpunt dat u wilt gebruiken in de wizard Herstellen.

3. Klik in het menu **acties** op **herstellen** om de wizard herstellen te openen.

    De VM en het herstelpunt die u hebt geselecteerd, worden weergegeven in het scherm **Selectie voor herstel controleren**. Selecteer **Next**.

4. Selecteer op het scherm **type herstel bewerking selecteren** waar u de gegevens wilt herstellen en selecteer vervolgens **volgende**.

    - **Herstellen naar oorspronkelijk exemplaar**: wanneer u herstelt naar het oorspronkelijke exemplaar, worden de oorspronkelijke VHD en alle bijbehorende controle punten verwijderd. MABS herstelt de VHD en andere configuratie bestanden op de oorspronkelijke locatie met behulp van Hyper-V-VSS Writer. Aan het einde van het herstelproces zijn virtuele machines nog steeds maximaal beschikbaar.
        De brongroep moet aanwezig zijn voor het herstel. Als de groep niet beschikbaar is, herstelt u naar een alternatieve locatie en stelt u de virtuele machine maximaal beschikbaar.

    - **Herstellen als virtuele machine op een wille keurige host**: MABS ondersteunt een probleemloze herstel bewerking van een beveiligde hyper-v-virtuele machine naar een andere hyper-v-host, onafhankelijk van de processor architectuur. Virtuele Hyper-V-machines die worden hersteld naar een cluster knooppunt zijn niet Maxi maal beschikbaar. Als u deze optie kiest, biedt de wizard Herstellen u een extra scherm voor het identificeren van het doel en doelpad.
    
        >[!NOTE]
        >Als u de oorspronkelijke host selecteert, is het gedrag hetzelfde als het **oorspronkelijke exemplaar herstellen**. De oorspronkelijke VHD en alle bijbehorende controle punten worden verwijderd.

    - **Kopiëren naar een netwerkmap**: MABS ondersteunt herstel op item niveau (ILR), waarmee u op item niveau bestanden, mappen, volumes en virtuele harde schijven (vhd's) kunt herstellen vanaf een back-up op hostniveau van virtuele Hyper-V-machines naar een netwerk share of een volume op een MABS beveiligde server. De MABS-beveiligings agent hoeft niet in de gast te worden geïnstalleerd om herstel op item niveau uit te voeren. Als u deze optie kiest, biedt de wizard Herstellen u een extra scherm voor het identificeren van het doel en doelpad.

5. Configureer in **herstel opties opgeven** de herstel opties en selecteer **volgende**:

    - Als u een virtuele machine wilt herstellen over een lage band breedte, selecteert u **wijzigen** om **beperking van netwerk bandbreedte gebruik** in te scha kelen. Nadat u de optie voor beperking hebt ingeschakeld, kunt u de hoeveelheid bandbreedte opgeven die u beschikbaar wilt maken en het tijdstip waarop die bandbreedte beschikbaar is.
    - Selecteer **op San gebaseerd herstel inschakelen met behulp van een moment opname van de hardware** als u uw netwerk hebt geconfigureerd.
    - Selecteer **Een e-mail verzenden wanneer het herstel is voltooid** en geef vervolgens de e-mailadressen op, als u e-mailmeldingen wilt verzenden zodra het herstelproces is voltooid.

6. Controleer in het scherm Samenvatting of alle gegevens juist zijn. Als de details niet juist zijn of als u een wijziging wilt aanbrengen, selecteert u **vorige**. Als u tevreden bent met de instellingen, selecteert u **herstellen** om het herstel proces te starten.

7. Het scherm **Herstelstatus** bevat informatie over de hersteltaak.

## <a name="next-steps"></a>Volgende stappen

[Gegevens herstellen vanaf Azure Backup Server](./backup-azure-alternate-dpm-server.md)
