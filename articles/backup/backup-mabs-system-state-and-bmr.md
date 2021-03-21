---
title: Systeem status en Bare-Metal Recovery-beveiliging
description: Gebruik Azure Backup Server om een back-up te maken van uw systeem status en BMR-beveiliging (Bare-Metal Recovery) te bieden.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: c5096158ca0e76ca03577347d8dd3e1419a33ca0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021619"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Maak een back-up van de systeem status en herstel op bare-metal computers met behulp van Azure Backup Server

Azure Backup Server maakt een back-up van de systeem status en biedt BMR-beveiliging (bare-metal herstel).

* **Systeem status back-up**: maakt back-ups van bestanden van het besturings systeem. Met deze back-up kunt u herstellen wanneer een computer wordt opgestart, maar de systeem bestanden en het REGI ster gaan verloren. Een systeem status back-up bevat de volgende elementen:
  * Domeinlid: opstartbestanden, registratiedatabase van COM+-klasse, register
  * Domein controller: Windows Server Active Directory (NTDS), opstart bestanden, registratie database van COM+-klasse, REGI ster, systeem volume (SYSVOL)
  * Computer waarop Cluster Services worden uitgevoerd: meta gegevens van de Cluster Server
  * Computer waarop Certificate Services wordt uitgevoerd: certificaat gegevens
* **Bare-metal back-up**: maakt back-ups van besturingssysteem bestanden en alle gegevens op essentiële volumes, met uitzonde ring van gebruikers gegevens. Een BMR-back-up bevat per definitie een systeem status back-up. Het biedt beveiliging wanneer een computer niet start en u moet alles herstellen.

De volgende tabel bevat een overzicht van waar u een back-up kunt maken en herstellen. Voor informatie over app-versies die systeem status-en BMR kunnen beveiligen, Zie [waarover Azure backup server back-ups maken?](backup-mabs-protection-matrix.md).

|Backup|Probleem|Herstellen van Azure Backup Server back-up|Herstellen vanaf systeemstatusback-up|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Bestandsgegevens**<br /><br />Standaard gegevensback-up<br /><br />BMR/systeemstatusback-up|Verloren bestandsgegevens|J|N|N|
|**Bestandsgegevens**<br /><br />Azure Backup Server back-up van bestands gegevens<br /><br />BMR/systeemstatusback-up|Verloren of beschadigd besturingssysteem|N|J|J|
|**Bestandsgegevens**<br /><br />Azure Backup Server back-up van bestands gegevens<br /><br />BMR/systeemstatusback-up|Verloren server (gegevens volumes intact)|N|N|J|
|**Bestandsgegevens**<br /><br />Azure Backup Server back-up van bestands gegevens<br /><br />BMR/systeemstatusback-up|Verloren server (gegevensvolumes verloren)|J|N|J<br /><br />BMR, gevolgd door normaal herstel van back-ups van bestands gegevens|
|**Share point-gegevens**<br /><br />Azure Backup Server back-up van farm gegevens<br /><br />BMR/systeemstatusback-up|Verloren site, lijsten, lijst items, documenten|J|N|N|
|**Share point-gegevens**<br /><br />Azure Backup Server back-up van farm gegevens<br /><br />BMR/systeemstatusback-up|Verloren of beschadigd besturingssysteem|N|J|J|
|**Share point-gegevens**<br /><br />Azure Backup Server back-up van farm gegevens<br /><br />BMR/systeemstatusback-up|Herstel na noodgeval|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server back-up van Hyper-V-host of gast<br /><br />BMR/systeemstatusback-up van host|Verloren virtuele machine|J|N|N|
|Hyper-V<br /><br />Azure Backup Server back-up van Hyper-V-host of gast<br /><br />BMR/systeemstatusback-up van host|Verloren of beschadigd besturingssysteem|N|J|J|
|Hyper-V<br /><br />Azure Backup Server back-up van Hyper-V-host of gast<br /><br />BMR/systeemstatusback-up van host|Verloren Hyper-V-host (virtuele machines intact)|N|N|J|
|Hyper-V<br /><br />Azure Backup Server back-up van Hyper-V-host of gast<br /><br />BMR/systeemstatusback-up van host|Verloren Hyper-V-host (verloren virtuele machines)|N|N|J<br /><br />BMR, gevolgd door regulier Azure Backup Server herstel|
|SQL Server/Exchange<br /><br />Azure Backup Server app-back-up<br /><br />BMR/systeemstatusback-up|Verloren appgegevens|J|N|N|
|SQL Server/Exchange<br /><br />Azure Backup Server app-back-up<br /><br />BMR/systeemstatusback-up|Verloren of beschadigd besturingssysteem|N|J|J|
|SQL Server/Exchange<br /><br />Azure Backup Server app-back-up<br /><br />BMR/systeemstatusback-up|Verloren server (database/transactielogboekbestanden intact)|N|N|J|
|SQL Server/Exchange<br /><br />Azure Backup Server app-back-up<br /><br />BMR/systeemstatusback-up|Verloren server (verloren database/transactielogboekbestanden)|N|N|J<br /><br />BMR-herstel, gevolgd door het normale Azure Backup Server herstel|

## <a name="how-system-state-backup-works"></a>Hoe een systeemstatusback-up werkt

Wanneer een systeem status back-up wordt uitgevoerd, communiceert de back-upserver met Windows Server Back-up om een back-up van de systeem status van de server aan te vragen. Standaard maakt de back-upserver en Windows Server Back-up gebruik van het station met de meeste beschik bare vrije ruimte. Informatie over dit station wordt opgeslagen in het *PSDataSourceConfig.xml* -bestand.

U kunt het station dat door de back-upserver wordt gebruikt voor de systeem status back-up aanpassen:

1. Ga op de beveiligde server naar *C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources*.
1. Open het *PSDataSourceConfig.xml* bestand om het te bewerken.
1. Wijzig de waarde \<FilesToProtect\> voor de stationsletter.
1. Sla het bestand op en sluit het.

Als een beveiligings groep is ingesteld om de systeem status van de computer te beveiligen, voert u een consistentie controle uit. Als er een waarschuwing wordt gegenereerd, selecteert u **beveiligings groep wijzigen** in de waarschuwing en voltooit u de pagina's in de wizard. Voer vervolgens nog een consistentiecontrole uit.

Als de beveiligings server zich in een cluster bevindt, kan er een cluster station worden geselecteerd als het station met de meeste vrije ruimte. Als het eigendom van het station is overgeschakeld naar een ander knoop punt en een systeem status back-up wordt uitgevoerd, is het station niet beschikbaar en mislukt de back-up. In dit scenario wijzigt u *PSDataSourceConfig.xml* zodat deze verwijst naar een lokaal station.

Windows Server Back-up maakt vervolgens een map met de naam *WindowsImageBackup* in de hoofdmap van de map Restore. Als Windows Server Back-up de back-up maakt, worden alle gegevens in deze map geplaatst. Wanneer de back-up is voltooid, wordt het bestand overgebracht naar de back-upserver computer. Let op de volgende informatie:

* Deze map en de inhoud ervan worden niet opgeschoond wanneer de back-up of overdracht is voltooid. De beste manier om dit te zien is dat de ruimte wordt gereserveerd voor de volgende keer dat een back-up is voltooid.
* De map wordt voor elke back-up gemaakt. Het tijds tempel tijd en datum geeft de tijd weer van de laatste back-up van de systeem status.

## <a name="how-bmr-backup-works"></a>Hoe BMR Backup werkt

Voor BMR (inclusief een systeem status back-up) wordt de back-uptaak rechtstreeks opgeslagen in een share op de Server computer van de back-up. Deze wordt niet opgeslagen in een map op de beveiligde server.

De back-upserver roept Windows Server Back-up aan en deelt het replica volume voor die BMR-back-up. In dit geval is het niet nodig om Windows Server Back-up te gebruiken van het station met de meeste vrije ruimte. In plaats daarvan wordt de share gebruikt die voor de taak is gemaakt.

Wanneer de back-up is voltooid, wordt het bestand overgebracht naar de back-upserver computer. Logboeken worden opgeslagen in *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Vereisten en beperkingen

* BMR wordt niet ondersteund voor computers met Windows Server 2003 of voor computers waarop een client besturingssysteem wordt uitgevoerd.

* U kunt niet BMR en systeemstatus voor dezelfde computer in verschillende beveiligingsgroepen beveiligen.

* Een back-upserver computer kan zichzelf niet beveiligen voor BMR.

* Kortetermijnbeveiliging op tape (schijf naar tape of D2T) wordt niet ondersteund voor BMR. Lange termijn opslag op tape (schijf naar schijf naar tape of D2D2T) wordt ondersteund.

* Voor BMR-beveiliging moet Windows Server Back-up op de beveiligde computer zijn geïnstalleerd.

* Voor BMR-beveiliging, in tegens telling tot systeem status beveiliging, heeft de back-upserver geen ruimte vereisten op de beveiligde computer. Windows Server Back-up stuurt back-ups rechtstreeks naar de back-upserver computer. De taak back-upoverdracht wordt niet weer gegeven in de weer gave **taken** van de back-upserver.

* De back-upserver reserveert 30 GB aan ruimte op het replica volume voor BMR. U kunt deze Space-toewijzing wijzigen op de pagina **schijf toewijzing** in de wizard beveiligings groep wijzigen. U kunt ook de Power shell-cmdlets Get-DatasourceDiskAllocation en Set-DatasourceDiskAllocation gebruiken. Op het herstel punt volume vereist BMR-beveiliging ongeveer 6 GB voor een Bewaar periode van vijf dagen.
  * U kunt de grootte van het replica volume niet verkleinen tot minder dan 15 GB.
  * De back-upserver berekent niet de grootte van de BMR-gegevens bron. Hierbij wordt uitgegaan van 30 GB voor alle servers. Wijzig de waarde op basis van de grootte van de BMR-back-ups die u in uw omgeving verwacht. U kunt de grootte van een BMR-back-up berekenen als de som van de gebruikte ruimte op alle essentiële volumes. Essentiële volumes = opstart volume + systeem volume + volume dat als host fungeert voor systeem status gegevens, zoals Active Directory.

* Als u van systeem status beveiliging naar BMR-beveiliging overschakelt, is voor BMR-beveiliging minder ruimte op het *herstel punt volume* nodig. De extra ruimte op het volume wordt echter niet vrijgemaakt. U kunt de volume grootte hand matig verkleinen op de pagina **schijf toewijzing wijzigen** van de wizard beveiligings groep wijzigen. U kunt ook de Power shell-cmdlets Get-DatasourceDiskAllocation en Set-DatasourceDiskAllocation gebruiken.

    Als u van systeem status beveiliging naar BMR-beveiliging overschakelt, is voor BMR-beveiliging meer ruimte op het *replica volume* nodig. Het volume wordt automatisch uitgebreid. Als u de standaard ruimte toewijzingen wilt wijzigen, gebruikt u de Modify-DiskAllocation Power shell-cmdlet.

* Als u van BMR-beveiliging naar systeem status beveiliging overschakelt, hebt u meer ruimte nodig op het herstel punt volume. De back-upserver probeert het volume mogelijk automatisch te verg Roten. Als er onvoldoende ruimte beschikbaar is in de opslag groep, treedt er een fout op.

    Als u van BMR-beveiliging naar systeem status beveiliging overschakelt, hebt u ruimte op de beveiligde computer nodig. U hebt de ruimte nodig omdat de systeem status beveiliging de replica eerst naar de lokale computer schrijft en vervolgens de replica naar de back-upserver computer overdraagt.

## <a name="before-you-begin"></a>Voordat u begint

1. **Azure backup server implementeren**. Controleer of de back-upserver op de juiste wijze is geïmplementeerd. Zie voor meer informatie:
    * [Systeem vereisten voor Azure Backup Server](/system-center/dpm/install-dpm#setup-prerequisites)
    * [Beveiligings matrix van de back-upserver](backup-mabs-protection-matrix.md)

1. **Opslag instellen**. U kunt back-upgegevens opslaan op schijf, op tape en in de Cloud met Azure. Zie voor meer informatie [voorbereiden van gegevens opslag](/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Stel de beveiligings agent** in. Installeer de beveiligings agent op de computer waarvan u een back-up wilt maken. Zie [de DPM-beveiligings agent implementeren](/system-center/dpm/deploy-dpm-protection-agent)voor meer informatie.

## <a name="back-up-system-state-and-bare-metal"></a>Systeemstatus- en bare metalback-ups

Back-ups maken van de systeem status en Bare Metal:

1. Als u de wizard nieuwe beveiligingsgroep maken wilt openen, selecteert u in de Administrator-console van de back-upserver de optie **beveiligings**  >  **acties**  >  **beveiligings groep maken**.

1. Selecteer op de pagina **type beveiligings groep selecteren** de optie **servers** en selecteer vervolgens **volgende**.

1. Op de pagina **groeps leden selecteren** vouwt u de computer uit en selecteert u vervolgens **BMR** of **systeem status**.

    Houd er rekening mee dat u de BMR en de systeem status voor dezelfde computer in verschillende groepen niet kunt beveiligen. Wanneer u BMR selecteert, wordt de systeem status automatisch ingeschakeld. Zie [Deploying Protection groups](/system-center/dpm/create-dpm-protection-groups)(Engelstalig) voor meer informatie.

1. Kies op de pagina **methode voor gegevens beveiliging selecteren** hoe u back-ups voor de korte termijn en de back-up voor de lange termijn wilt afhandelen.

    Back-ups op korte termijn zijn altijd eerst op schijf, met de optie om een back-up van de schijf naar Azure te maken met behulp van Azure Backup (korte termijn of lange termijn). Een alternatief voor lange termijn back-up naar de Cloud is het instellen van back-ups op lange termijn naar een zelfstandig tape apparaat of een tape wisselaar die is verbonden met de back-upserver.

1. Kies op de pagina **Short-Term doelen selecteren** hoe u een back-up wilt maken naar kortetermijnbeveiliging op schijf:
    * Kies voor **Bewaar termijn** hoe lang de gegevens op de schijf bewaard moeten blijven.
    * Kies voor **synchronisatie frequentie** hoe vaak een incrementele back-up naar schijf moet worden uitgevoerd. Als u geen back-upinterval wilt instellen, kunt u **net voor een herstel punt** selecteren. De back-upserver voert een snelle volledige back-up uit, net voordat elk herstel punt is gepland.

1. Als u gegevens op tape wilt opslaan voor lange termijn opslag, kiest u op de pagina **Long-Term doelstellingen opgeven** hoe lang u tape gegevens wilt bewaren (1 tot 99 jaar).
    1. Kies voor **frequentie van back-ups** hoe vaak u een back-up op tape wilt uitvoeren. De frequentie is gebaseerd op de Bewaar termijn die u hebt geselecteerd:
        * Wanneer de Bewaar termijn 1 tot 99 jaar is, kunt u een back-up maken van dagelijks, wekelijks, twee wekelijks, maandelijks, elk kwar taal, halfjaarlijks of jaarlijks.
        * Wanneer de Bewaar termijn is ingesteld op 1 tot 11 maanden, kunt u een back-up maken van dagelijks, wekelijks, twee wekelijks of maandelijks.
        * Wanneer de Bewaar termijn 1 tot 4 weken is, kunt u dagelijks of wekelijks een back-up maken.

    1. Selecteer op de pagina **Details van tape en bibliotheek selecteren** de tape en bibliotheek die u wilt gebruiken. Kies ook of gegevens moeten worden gecomprimeerd en versleuteld.

1. Controleer op de pagina **schijf toewijzing controleren** de schijf ruimte van de opslag groep die beschikbaar is voor de beveiligings groep.

    * **Totale gegevens grootte** is de grootte van de gegevens waarvan u een back-up wilt maken.
    * **Schijf ruimte die moet worden ingericht op Azure backup server** is de ruimte die de back-upserver voor de beveiligings groep adviseert. De back-upserver gebruikt deze instellingen om het ideale back-upvolume te kiezen. U kunt de keuzes van het back-upvolume bewerken in **schijf toewijzings Details**.
    * Voor werk belastingen selecteert u in de vervolg keuzelijst de voorkeurs opslag. Met uw bewerkingen worden de waarden voor **Totale opslag** en **Vrije opslagruimte** in het deelvenster **Beschikbare schijfopslag** gewijzigd. Onderingerichte ruimte is de hoeveelheid opslag die door de back-upserver wordt voorgesteld om u toe te voegen aan het volume om soepele back-ups te garanderen.

1. Selecteer op de pagina **methode voor maken van replica** selecteren hoe u de initiële replicatie van volledige gegevens wilt afhandelen.

    Als u ervoor kiest om via het netwerk te repliceren, wordt u aangeraden een rustige tijd te kiezen. Voor grote hoeveel heden gegevens of voor netwerk omstandigheden die minder dan optimaal zijn, kunt u overwegen om de gegevens offline te repliceren met behulp van Verwissel bare media.

1. Selecteer op de pagina **Opties voor consistentie controle kiezen** hoe u consistentie controles wilt automatiseren.

    U kunt ervoor kiezen om een controle alleen uit te voeren als de replica gegevens inconsistent worden of volgens een planning. Als u geen automatische consistentie controle wilt configureren, kunt u op elk gewenst moment een hand matige controle uitvoeren. Als u een hand matige controle wilt uitvoeren, klikt u in het gebied **beveiliging** van de Administrator-console van de back-upserver met de rechter muisknop op de beveiligings groep en selecteert u vervolgens **consistentie controle uitvoeren**.

1. Als u een back-up naar de Cloud hebt gemaakt met behulp van Azure Backup, selecteert u op de pagina **online beveiligings gegevens opgeven** de werk belastingen waarvan u een back-up wilt maken naar Azure.

1. Selecteer op de pagina **online back-upschema opgeven** hoe vaak een incrementele back-up naar Azure moet worden gemaakt.

    U kunt plannen dat back-ups elke dag, week, maand en jaar worden uitgevoerd. U kunt ook de tijd en datum selecteren waarop back-ups moeten worden uitgevoerd. U kunt maximaal twee keer per dag back-ups uitvoeren. Telkens wanneer een back-up wordt uitgevoerd, wordt er een gegevens herstel punt in azure gemaakt op basis van de kopie van de back-upgegevens die zijn opgeslagen op de back-upserver schijf.

1. Selecteer op de pagina **online retentie beleid opgeven** hoe de herstel punten die zijn gemaakt op basis van de dagelijkse, wekelijkse, maandelijkse en jaarlijkse back-ups, worden bewaard in Azure.

1. Selecteer op de pagina **online replicatie kiezen** hoe de eerste volledige replicatie van gegevens wordt uitgevoerd.

    U kunt via het netwerk repliceren of een back-up maken van offline (offline seeding). Een offline back-up maakt gebruik van de Azure import-functie. Zie [offline back-upwerk stroom in azure backup](offline-backup-azure-data-box.md)voor meer informatie.

1. Controleer uw instellingen op de pagina  **samen vatting** . Nadat u **groep maken** hebt geselecteerd, vindt de initiële replicatie van de gegevens plaats. Wanneer de gegevens replicatie is voltooid, is de status van de beveiligings groep op de pagina **status** **OK**. Back-ups worden vervolgens uitgevoerd volgens de instellingen van de beveiligings groep.

## <a name="recover-system-state-or-bmr"></a>Systeemstatus of BMR herstellen

U kunt BMR of systeemstatus naar een netwerklocatie herstellen. Als u een back-up hebt gemaakt van BMR, gebruikt u Windows Recovery Environment (WinRE) om uw systeem te starten en verbinding te maken met het netwerk. Gebruik vervolgens Windows Server Back-up om vanaf de netwerklocatie te herstellen. Als u een back-up hebt gemaakt van de systeem status, gebruikt u Windows Server Back-up om de netwerk locatie te herstellen.

### <a name="restore-bmr"></a>BMR herstellen

Herstel uitvoeren op de back-upserver computer:

1. Zoek in het deel venster **herstel** de computer die u wilt herstellen. Selecteer vervolgens bare **Metal Recovery**.

1. Beschikbare herstelpunten worden vet weergegeven in de kalender. Selecteer de datum en tijd voor het herstel punt dat u wilt gebruiken.

1. Selecteer op de pagina  **type herstel bewerking selecteren** de optie **kopiëren naar een** netwerkmap.

1. Selecteer op de pagina **bestemming opgeven** de bestemming voor de gekopieerde gegevens.

    Houd er rekening mee dat het doel voldoende ruimte heeft voor de gegevens. U wordt aangeraden een nieuwe map voor de bestemming te maken.

1. Selecteer de beveiligings instellingen op de pagina **herstel opties opgeven** . Selecteer vervolgens of u moment opnamen op basis van Storage Area Network (SAN) wilt gebruiken voor sneller herstel. Deze optie is alleen beschikbaar als:
    * U hebt een SAN dat deze functionaliteit biedt.
    * U kunt een kloon maken en splitsen om deze schrijfbaar te maken.
    * De beveiligde computer en de back-upserver computer zijn verbonden met hetzelfde netwerk.

1. Stel meldings opties in.
1. Selecteer op de pagina **bevestiging** de optie **herstellen**.

De share locatie instellen:

1. Ga in de terugzet locatie naar de map met de back-up.

1. Deel de map die zich op één niveau boven *WindowsImageBackup* bevindt, zodat de hoofdmap van de gedeelde map de map *WindowsImageBackup* is.

    Als u deze map niet deelt, wordt de back-up niet gevonden. Als u verbinding wilt maken met behulp van WinRE, hebt u een share nodig die u kunt openen in WinRE met het juiste IP-adres en referenties.

Het systeem herstellen:

1. Start de computer waarop u de installatie kopie wilt herstellen met behulp van de Windows-DVD voor het systeem dat u wilt herstellen.

1. Controleer op de eerste pagina de instellingen voor taal en land instelling. Selecteer op de pagina **installeren** **de optie uw computer herstellen**.

1. Selecteer op de pagina **Opties voor systeem herstel** **de optie uw computer herstellen met een systeem installatie kopie die u eerder hebt gemaakt**.

1. Selecteer op de pagina **Selecteer een systeem kopie back-up** de optie **Selecteer een systeem** kopie  >  **Geavanceerd**  >  **zoeken naar een systeem installatie kopie op het netwerk**. Als er een waarschuwing wordt weer gegeven, selecteert u **Ja**. Ga naar het pad naar de share, voer de referenties in en selecteer het herstel punt. Het systeem zoekt naar specifieke back-ups die in dat herstel punt beschikbaar zijn. Selecteer het herstel punt dat u wilt gebruiken.

1. Selecteer op de pagina **Kies hoe u de back-upbewerking wilt herstellen de** optie **schijven Format teren en opnieuw partitioneren**. Controleer de instellingen op de volgende pagina.

1. Selecteer **volt ooien** om de herstel bewerking te starten. De computer moet opnieuw worden opgestart.

### <a name="restore-system-state"></a>Systeemstatus herstellen

Herstel uitvoeren op de back-upserver:

1. Zoek in het deel venster **herstel** de computer die u wilt herstellen en selecteer vervolgens bare **Metal Recovery**.

1. Beschikbare herstelpunten worden vet weergegeven in de kalender. Selecteer de datum en tijd voor het herstel punt dat u wilt gebruiken.

1. Selecteer op de pagina **type herstel bewerking selecteren** de optie **kopiëren naar een** netwerkmap.

1. Selecteer op de pagina **bestemming opgeven** waar u de gegevens wilt kopiëren.

    Houd er rekening mee dat de bestemming die u selecteert, voldoende ruimte heeft voor de gegevens. U wordt aangeraden een nieuwe map voor de bestemming te maken.

1. Selecteer de beveiligings instellingen op de pagina **herstel opties opgeven** . Selecteer vervolgens of u op SAN gebaseerde moment opnamen van de hardware wilt gebruiken om sneller te kunnen herstellen. Deze optie is alleen beschikbaar als:
    * U hebt een SAN dat deze functionaliteit biedt.
    * U kunt een kloon maken en splitsen om deze schrijfbaar te maken.
    * De beveiligde computer en de back-upserver server zijn verbonden met hetzelfde netwerk.

1. Stel meldings opties in.
1. Selecteer op de pagina **bevestiging** de optie **herstellen**.

Windows Server Back-up uitvoeren:

1. Selecteer **acties**  >    >  **deze server** herstellen  >  **volgende**.

1. Selecteer **een andere server**, selecteer de pagina **type locatie opgeven** en selecteer vervolgens **externe gedeelde map**. Geef het pad op naar de map die het herstel punt bevat.

1. Selecteer op de pagina **type herstel bewerking selecteren** de optie **systeem status**.

1. Selecteer op de pagina **locatie voor systeem status herstel selecteren** de optie  **oorspronkelijke locatie**.

1. Selecteer op de pagina **bevestiging** de optie **herstellen**.

1. Start de server opnieuw op nadat de herstel bewerking is gestart.

U kunt ook de systeem status herstellen uitvoeren vanaf een opdracht prompt:

1. Start Windows Server Back-up op de computer die u wilt herstellen.

1. Als u de versie-id wilt ophalen, voert u bij de opdracht prompt het volgende in:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Gebruik de versie-id om de systeem status herstellen te starten. Voer in de opdrachtregel in:

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Bevestig dat u het herstel wilt starten. U ziet het proces in het opdracht prompt venster. Er wordt een herstellogboek gemaakt.

1. Start de server opnieuw op nadat de herstel bewerking is gestart.
