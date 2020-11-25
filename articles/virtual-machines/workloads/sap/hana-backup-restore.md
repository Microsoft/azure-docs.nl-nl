---
title: HANA back-up en herstel op SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: HANA-back-up en herstel uitvoeren op SAP HANA op Azure (grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42333a3feae19b6c3c77494f7e843cac1d9bc078
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006316"
---
# <a name="backup-and-restore-of-sap-hana-on-hana-large-instances"></a>Back-up en herstel van SAP HANA in HANA grote instanties

>[!IMPORTANT]
>Dit artikel is geen vervanging voor de documentatie over SAP HANA beheer of SAP-opmerkingen. Het is belang rijk dat u beschikt over een solide kennis van en ervaring op het SAP HANA beheer en bewerkingen, met name voor back-up, herstel, hoge Beschik baarheid en herstel na nood gevallen. In dit artikel worden scherm afbeeldingen van SAP HANA Studio weer gegeven. De inhoud, de structuur en de aard van de schermen van SAP-beheer Programma's en de hulpprogram ma's zelf kunnen veranderen van SAP HANA release naar release.

Het is belang rijk dat u de stappen en processen die in uw omgeving worden uitgevoerd en met uw HANA-versies en-releases uitoefent. Sommige processen die in dit artikel worden beschreven, zijn vereenvoudigd voor een beter inzicht in het algemeen. Ze zijn niet bedoeld om te worden gebruikt als gedetailleerde stappen voor de uiteindelijke bewerkings handleidingen. Als u bewerkings handleidingen wilt maken voor uw configuraties, test en oefent u uw processen en documenteert u de processen die aan uw specifieke configuraties zijn gerelateerd. 

Een van de belangrijkste aspecten van het werken met data bases is om ze te beschermen tegen onherstelbare gebeurtenissen. De oorzaak van deze gebeurtenissen kan van invloed zijn op een natuur ramp op eenvoudige gebruikers fouten.

Het maken van een back-up van een Data Base, met de mogelijkheid om deze te herstellen naar een bepaald tijdstip, bijvoorbeeld voordat iemand kritieke gegevens heeft verwijderd, maakt het herstellen mogelijk in een staat die zo dicht mogelijk bij de weg ligt voordat de storing is opgetreden.

Er moeten twee typen back-ups worden uitgevoerd om de mogelijkheid te herstellen:

- Database back-ups: volledige, incrementele of differentiële back-ups
- Back-ups van transactie logboeken

Naast de volledige database back-ups die op toepassings niveau worden uitgevoerd, kunt u back-ups met opslag momentopnamen uitvoeren. Opslag momentopnamen vervangen geen back-ups van transactie Logboeken. Back-ups van transactie logboeken zijn belang rijk om de data base te herstellen naar een bepaald tijdstip of om de logboeken van al doorgevoerde trans acties leeg te maken. Met opslag momentopnamen kunt u het herstel versnellen door snel een roll-voorwaartse installatie kopie van de data base te leveren. 

SAP HANA op Azure (grote exemplaren) biedt twee opties voor back-up en herstel:

- **Doe het zelf (zelf).** Nadat u hebt gecontroleerd of er voldoende schijf ruimte beschikbaar is, voert u volledige data base-en logboek back-ups uit met behulp van een van de volgende schijf back-upmethoden. U kunt rechtstreeks een back-up maken van volumes die zijn gekoppeld aan de HANA grote instantie-eenheden of aan NFS-shares die zijn ingesteld op een virtuele Azure-machine (VM). In het laatste geval hebben klanten een virtuele Linux-machine in azure ingesteld, Azure Storage aan de virtuele machine gekoppeld en de opslag delen via een geconfigureerde NFS-server in die VM. Kopieer de back-ups naar een Azure Storage-account als u de back-up uitvoert op volumes die rechtstreeks aan HANA grote instantie-eenheden zijn gekoppeld. Doe dit nadat u een virtuele Azure-machine hebt ingesteld waarmee NFS-shares die zijn gebaseerd op Azure Storage worden geëxporteerd. U kunt ook een Azure Backup kluis of Azure-koude opslag gebruiken. 

   Een andere optie is het gebruik van een hulp programma voor gegevens beveiliging van derden om de back-ups op te slaan nadat ze zijn gekopieerd naar een Azure-opslag account. De zelf-back-upoptie kan ook nodig zijn voor gegevens die u gedurende langere tijd moet opslaan voor nalevings-en controle doeleinden. In alle gevallen worden de back-ups gekopieerd naar NFS-shares die worden weer gegeven via een VM en Azure Storage.

- **Functionaliteit voor back-up en herstel van infra structuur.** U kunt ook de functionaliteit voor back-up en herstel gebruiken die de onderliggende infra structuur van SAP HANA op Azure (grote instanties) biedt. Deze optie voldoet aan de nood zaak van back-ups en snelle herstel bewerkingen. De rest van deze sectie heeft betrekking op de functionaliteit voor back-up en herstel die wordt aangeboden met HANA grote instanties. In deze sectie wordt ook aandacht besteed aan de relatie die back-up en herstel heeft voor de functionaliteit voor herstel na nood gevallen van HANA grote instanties.

> [!NOTE]
>   De snap shot-technologie die wordt gebruikt door de onderliggende infra structuur van HANA grote instanties, is afhankelijk van SAP HANA moment opnamen. Op dit punt werken SAP HANA moment opnamen niet in combi natie met meerdere tenants van SAP HANA data base-containers voor multi tenants. Als er slechts één Tenant is geïmplementeerd, SAP HANA moment opnamen werken en kunt u deze methode gebruiken.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Opslag momentopnamen van SAP HANA gebruiken op Azure (grote exemplaren)

De onderliggende opslag infrastructuur SAP HANA op Azure (grote exemplaren) ondersteunt de opslag momentopnamen van volumes. Het maken van back-ups en het herstellen van volumes wordt ondersteund, met de volgende overwegingen:

- In plaats van volledige database back-ups worden moment opnamen van opslag volumes op een regel matige basis gemaakt.
- Wanneer een moment opname wordt geactiveerd via/Hana/data en/Hana/Shared, die/usr/sap, volumes bevat, initieert de snap shot-technologie een SAP HANA moment opname voordat de moment opname van de opslag wordt uitgevoerd. Deze SAP HANA moment opname is het installatie punt voor de uiteindelijke logboek herstel na herstel van de moment opname van de opslag. Een HANA-moment opname is alleen geslaagd als u een actief HANA-exemplaar hebt. In een HSR-scenario wordt een moment opname van de opslag niet ondersteund op een actief secundair knoop punt waarop een HANA-moment opname niet kan worden uitgevoerd.
- Nadat de opslag momentopname is uitgevoerd, wordt de moment opname van de SAP HANA verwijderd.
- Back-ups van transactie logboeken worden regel matig gemaakt en opgeslagen in het/Hana/logbackups-volume of in Azure. U kunt het/Hana/logbackups-volume dat de back-ups van het transactie logboek bevat, activeren om een moment opname afzonderlijk te maken. In dat geval hoeft u geen HANA-moment opname uit te voeren.
- Als u een Data Base op een bepaald moment moet herstellen voor een productie-uitval, vraagt u dat Microsoft Azure ondersteuning of SAP HANA in azure herstellen naar een bepaalde moment opname van de opslag. Een voor beeld is een geplande herstel bewerking van een sandbox-systeem naar de oorspronkelijke staat.
- De SAP HANA moment opname die is opgenomen in de moment opname van de opslag, is een offset punt voor het Toep assen van back-ups van transactie logboeken die zijn uitgevoerd en die zijn opgeslagen nadat de opslag momentopname is gemaakt.
- Deze back-ups van transactie logboeken worden opgehaald om de data base terug te zetten naar een bepaald punt in de tijd.

U kunt opslag momentopnamen uitvoeren die zijn gericht op drie soorten volumes:

- Een gecombineerde moment opname via/Hana/data en/Hana/Shared, inclusief/usr/sap. Voor deze moment opname is het maken van een moment opname van een SAP HANA vereist als voor bereiding voor de moment opname van de opslag. Met de moment opname van de SAP HANA zorgt u ervoor dat de data base zich in een consistente status bevindt vanuit een opslag punt van de weer gave. Voor het herstel proces is dit een punt om in te stellen.
- Een afzonderlijke moment opname via/Hana/logbackups.
- Een besturingssysteem partitie.

Zie [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de meest recente momentopname scripts en documentatie. Wanneer u het momentopname script pakket downloadt van [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md), krijgt u drie bestanden. Een van de bestanden wordt gedocumenteerd in een PDF-bestand voor de beschik bare functionaliteit. Nadat u de toolset hebt gedownload, volgt u de instructies in ' de hulp middelen voor moment opnamen ophalen '.

## <a name="storage-snapshot-considerations"></a>Overwegingen voor opslag momentopname

>[!NOTE]
>Opslag momentopnamen verbruiken opslag ruimte die is toegewezen aan de HANA grote instantie-eenheden. Houd rekening met de volgende aspecten van het plannen van moment opnamen van opslag en het aantal opslag momentopnamen dat moet worden bewaard. 

De specifieke mechanismen van opslag momentopnamen voor SAP HANA op Azure (grote exemplaren) zijn:

- Een specifieke opslag momentopname op het moment dat deze wordt uitgevoerd, verbruikt weinig opslag ruimte.
- Als de inhoud van de gegevens wordt gewijzigd en de inhoud van SAP HANA gegevens bestanden op het opslag volume wordt gewijzigd, moet de moment opname de oorspronkelijke blok inhoud en de gegevens wijzigingen opslaan.
- Als gevolg hiervan wordt de opslag momentopname groter. Hoe langer de moment opname bestaat, hoe groter de moment opname van de opslag wordt.
- Hoe meer wijzigingen worden aangebracht aan het SAP HANA database volume gedurende de levens duur van een opslag momentopname, hoe groter het ruimte verbruik van de moment opname van de opslag.

SAP HANA op Azure (grote exemplaren) wordt geleverd met vaste volume grootten voor de SAP HANA gegevens-en logboek volumes. Het uitvoeren van moment opnamen van deze volumes Eats in uw volume ruimte. U moet het volgende doen:

- Bepaal wanneer u opslag momentopnamen wilt plannen.
- Bewaak het ruimte verbruik van de opslag volumes. 
- Het aantal moment opnamen dat u opslaat beheren. 

U kunt de opslag momentopnamen uitschakelen wanneer u massa's van gegevens importeert of andere belang rijke wijzigingen doorvoert in de HANA-data base. 


De volgende secties bevatten informatie over het uitvoeren van deze moment opnamen en het toevoegen van algemene aanbevelingen:

- Hoewel de hardware 255 moment opnamen per volume kan hebben, kunt u het beste onder dit nummer blijven. De aanbeveling is 250 of minder.
- Voordat u opslag momentopnamen uitvoert, controleert u de beschik bare ruimte en houdt u deze bij.
- Verlaag het aantal opslag momentopnamen op basis van de beschik bare ruimte. U kunt het aantal moment opnamen verlagen dat u behoudt, of u kunt de volumes uitbreiden. U kunt extra opslag ruimte best Ellen in 1 TB eenheden.
- Tijdens activiteiten zoals het verplaatsen van gegevens naar SAP HANA met SAP platform Migration Tools (R3load) of het herstellen van SAP HANA-data bases van back-ups, moet u opslag momentopnamen op het/Hana/data-volume uitschakelen. 
- Vermijd indien mogelijk opslag momentopnamen tijdens grotere reorganisaties van SAP HANA tabellen.
- Opslag momentopnamen zijn een vereiste om te profiteren van de mogelijkheden voor nood herstel van SAP HANA op Azure (grote exemplaren).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Vereisten voor het gebruik van self-service voor opslag momentopnamen

Zorg ervoor dat perl is geïnstalleerd op het Linux-besturings systeem op de HANA-server voor grote instanties om ervoor te zorgen dat het momentopname script correct wordt uitgevoerd. Perl wordt vooraf geïnstalleerd op uw HANA-eenheid voor grote instanties. Als u de perl-versie wilt controleren, gebruikt u de volgende opdracht:

`perl -v`

![De open bare sleutel wordt gekopieerd door deze opdracht uit te voeren](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Opslag momentopnamen instellen

Voer de volgende stappen uit om moment opnamen van opslag met HANA grote instanties in te stellen.
1. Zorg ervoor dat perl is geïnstalleerd op het Linux-besturings systeem op de HANA-server voor grote instanties.
1. Wijzig de/etc/ssh/ssh \_ -configuratie om de regel _Macs HMAC-SHA1_ toe te voegen.
1. Maak een SAP HANA back-upaccount op het hoofd knooppunt voor elk SAP HANA exemplaar dat u uitvoert, indien van toepassing.
1. Installeer de SAP HANA HDB-client op alle SAP HANA Large Instances servers.
1. Maak op de eerste SAP HANA Large Instances server van elke regio een open bare sleutel voor toegang tot de onderliggende opslag infrastructuur die het maken van moment opnamen beheert.
1. Kopieer de scripts en het configuratie bestand van [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) naar de locatie van **hdbsql** in de SAP Hana-installatie.
1. Wijzig zo nodig het *HANABackupDetails.txt* -bestand voor de juiste klant specificaties.

Down load de nieuwste momentopname scripts en documentatie van [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Zie [micro soft snap shot tools for SAP Hana in azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de eerder vermelde stappen.

### <a name="consideration-for-mcod-scenarios"></a>Overwegingen voor MCOD-scenario's
Als u een [MCOD-scenario](https://launchpad.support.sap.com/#/notes/1681092) uitvoert met meerdere exemplaren van SAP Hana op één Hana grote instantie-eenheid, hebt u afzonderlijke opslag volumes ingericht voor elk van de SAP Hana exemplaren. Zie ' belang rijk te onthouden ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie over MDC en andere overwegingen.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Stap 1: Installeer de SAP HANA HDB-client

Het Linux-besturings systeem dat is geïnstalleerd op SAP HANA op Azure (grote exemplaren) bevat de mappen en scripts die nodig zijn om SAP HANA opslag momentopnamen uit te voeren voor back-ups en herstel na nood gevallen. Raadpleeg voor meer recente releases in [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

Het is uw verantwoordelijkheid om de SAP HANA HDB-client te installeren op de HANA grote instantie-eenheden tijdens de installatie van SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Stap 2: de/etc/ssh/ssh- \_ configuratie wijzigen

Deze stap wordt beschreven in ' communicatie inschakelen met opslag ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Stap 3: een open bare sleutel maken

Als u toegang tot de opslag momentopname-interfaces van uw HANA-Tenant voor grote instanties wilt inschakelen, stelt u een aanmeldings procedure in met een open bare sleutel. 

Maak op de eerste SAP HANA op de server van Azure (grote instanties) in uw Tenant een open bare sleutel voor toegang tot de opslag infrastructuur. Met een open bare sleutel is een wacht woord niet vereist om u aan te melden bij de opslag momentopname-interfaces. U hoeft ook geen wachtwoord referenties met een open bare sleutel te onderhouden. 

Zie ' communicatie inschakelen met opslag ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie over het genereren van een open bare sleutel.


### <a name="step-4-create-an-sap-hana-user-account"></a>Stap 4: een SAP HANA-gebruikers account maken

Als u het maken van SAP HANA moment opnamen wilt starten, maakt u een gebruikers account in SAP HANA dat door de scripts van de opslag momentopname kan worden gebruikt. Maak voor dit doel een SAP HANA gebruikers account in SAP HANA Studio. De gebruiker moet worden gemaakt onder de SYSTEMDB en *niet* onder de sid-Data Base voor MDC. In de omgeving met één container wordt de gebruiker gemaakt in de Tenant-data base. Dit account moet beschikken over een **back-upbeheer** en lees machtigingen voor de **catalogus** . 

Zie ' communicatie inschakelen met SAP HANA ' in [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)om een gebruikers account in te stellen en te gebruiken.


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Stap 5: het gebruikers account van de SAP HANA autoriseren

In deze stap machtigt u het SAP HANA gebruikers account dat u hebt gemaakt, zodat de scripts tijdens runtime geen wacht woorden hoeven in te dienen. Met de SAP HANA opdracht wordt `hdbuserstore` het maken van een SAP Hana gebruikers sleutel ingeschakeld. De sleutel wordt opgeslagen op een of meer SAP HANA knoop punten. Met de gebruikers sleutel krijgt de gebruiker toegang tot SAP HANA zonder dat er wacht woorden hoeven te worden beheerd in het script proces. Het script proces wordt verderop in dit artikel besproken.

>[!IMPORTANT]
>Voer deze configuratie opdrachten uit met dezelfde gebruikers context als waarop de momentopname opdrachten worden uitgevoerd. Anders werken de opdrachten van de moment opname niet goed.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Stap 6: de momentopname scripts ophalen, de moment opnamen configureren en de configuratie en connectiviteit testen

Down load de meest recente versie van de scripts van [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). De manier waarop de scripts worden geïnstalleerd, wordt gewijzigd met versie 4,1 van de scripts. Zie ' communicatie met SAP HANA inschakelen ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie.

Zie ' eenvoudige installatie van moment opname-hulpprogram ma's (standaard) ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de exacte volg orde van de opdrachten. We raden u aan de standaard installatie te gebruiken. 

Als u een upgrade wilt uitvoeren van versie 3. x naar 4,1, raadpleegt u ' een bestaande installatie bijwerken ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Zie "verwijderen van de momentopname hulpprogramma's" in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie over het installeren van het hulp programma 4,1.

Vergeet niet om de stappen uit te voeren die worden beschreven in ' de installatie van snap shot tools volt ooien ' in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Het doel van de verschillende scripts en bestanden die zijn geïnstalleerd, wordt beschreven in ' wat zijn deze momentopname hulpprogramma's? ' in de [micro soft snap shot-hulpprogram ma's voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Voordat u de momentopname hulpprogramma's configureert, moet u ervoor zorgen dat u ook de HANA-back-uplocaties en-instellingen correct hebt geconfigureerd. Zie "SAP HANA-configuratie" in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie.

De configuratie van het hulp programma set snap shots wordt beschreven in ' config file-HANABackupCustomerDetails.txt ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>De connectiviteit met SAP HANA testen

Nadat u alle configuratie gegevens in het *HANABackupCustomerDetails.txt* -bestand hebt geplaatst, controleert u of de configuraties correct zijn voor de Hana-exemplaar gegevens. Gebruik het script `testHANAConnection` , dat onafhankelijk is van een SAP Hana scale-up of schaal bare configuratie.

Zie ' connectiviteit controleren met SAP HANA-testHANAConnection ' in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie.

#### <a name="test-storage-connectivity"></a>Opslag connectiviteit testen

De volgende test stap is het controleren van de verbinding met de opslag op basis van de gegevens die u in het *HANABackupCustomerDetails.txt* configuratie bestand hebt geplaatst. Voer vervolgens een test momentopname uit. Voordat u de `azure_hana_backup` opdracht uitvoert, moet u deze test uitvoeren. Zie ' connectiviteit controleren met Storage-testStorageSnapshotConnection ' ' in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de volg orde van opdrachten voor deze test.

Na een geslaagde aanmelding bij de interfaces van de virtuele opslag machine wordt het script voortgezet met fase 2 en wordt een moment opname van de test gemaakt. De uitvoer wordt hier weer gegeven voor een scale-out configuratie met drie knoop punten van SAP HANA.

Als de test momentopname met het script wordt uitgevoerd, kunt u de werkelijke opslag momentopnamen plannen. Als dat niet lukt, onderzoekt u de problemen voordat u verdergaat. De test momentopname moet in de buurt blijven totdat de eerste echte moment opnamen worden uitgevoerd.


### <a name="step-7-perform-snapshots"></a>Stap 7: moment opnamen uitvoeren

Wanneer de voorbereidende stappen zijn voltooid, kunt u beginnen met het configureren en plannen van de werkelijke opslag momentopnamen. Het script dat moet worden gepland, werkt met SAP HANA scale-up-en scale-out-configuraties. Voor periodieke en periodieke uitvoering van het back-upscript moet u het script plannen met behulp van het hulp programma cron. 

Zie ' moment opname van de back-up uitvoeren-azure_hana_backup ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de exacte opdracht syntaxis en-functionaliteit. 

Wanneer het script `azure_hana_backup` wordt uitgevoerd, maakt het de moment opname van de opslag in de volgende drie fasen:

1. Er wordt een moment opname van SAP HANA uitgevoerd.
1. Er wordt een moment opname van de opslag uitgevoerd.
1. De SAP HANA moment opname die is gemaakt voordat de opslag momentopname werd uitgevoerd, wordt verwijderd.

Als u het script wilt uitvoeren, roept u het aan vanuit de uitvoer bare HDB-map waarnaar deze is gekopieerd. 

De retentie tijd wordt beheerd met het aantal moment opnamen dat als een para meter wordt verzonden wanneer u het script uitvoert. De hoeveelheid tijd die wordt gedekt door de moment opnamen van de opslag is een functie van de uitvoerings periode en van het aantal moment opnamen dat is verzonden als een para meter wanneer het script wordt uitgevoerd. 

Als het aantal moment opnamen dat wordt bewaard groter is dan het aantal dat wordt genoemd als een para meter in de aanroep van het script, wordt de oudste opslag momentopname van hetzelfde label verwijderd voordat een nieuwe moment opname wordt uitgevoerd. Het nummer dat u opgeeft als laatste para meter van de aanroep is het getal dat u kunt gebruiken om het aantal moment opnamen te bepalen dat wordt bewaard. Met dit aantal kunt u ook de schijf ruimte die wordt gebruikt voor moment opnamen beheren, indirect. 


## <a name="snapshot-strategies"></a>Momentopname strategieën
De frequentie van moment opnamen voor de verschillende typen is afhankelijk van het feit of u de functie voor herstel na nood gevallen in de HANA-versie gebruikt. Deze functionaliteit is afhankelijk van opslag momentopnamen, waarvoor speciale aanbevelingen kunnen worden vereist voor de frequentie-en uitvoerings perioden van de moment opnamen van de opslag. 

In de overwegingen en aanbevelingen die volgen, is de veronderstelling dat u *geen* gebruik maakt van de functionaliteit voor herstel na nood gevallen die door grote instanties wordt aangeboden. In plaats daarvan gebruikt u de opslag momentopnamen om back-ups te maken en kunt u voor de afgelopen 30 dagen herstel naar een bepaald tijdstip aanbieden. Gezien de beperkingen van het aantal moment opnamen en ruimte, moet u rekening houden met de volgende vereisten:

- De herstel tijd voor herstel naar een bepaald tijdstip.
- De gebruikte ruimte.
- De doel stellingen voor herstel punten en herstel tijd voor mogelijke herstel na een nood geval.
- De uiteindelijke uitvoering van HANA Full-Data Base-back-ups op schijven. Wanneer een back-up van de volledige data base wordt uitgevoerd op schijven of de **backint** -interface, mislukt de uitvoering van de opslag momentopnamen. Als u back-ups van de volledige data base boven op de opslag momentopnamen wilt uitvoeren, moet u ervoor zorgen dat de uitvoering van de opslag momentopnamen gedurende deze tijd is uitgeschakeld.
- Het aantal moment opnamen per volume, beperkt tot 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Als u geen gebruik maakt van de functionaliteit voor nood herstel van HANA grote instanties, is de momentopname periode minder vaak. In dergelijke gevallen voert u de gecombineerde moment opnamen op/Hana/data en/Hana/Shared uit, waaronder/usr/sap, in 12-uurs of 24-uurs Peri Oden. Bewaar de moment opnamen voor een maand. Hetzelfde geldt voor de moment opnamen van het back-upvolume van het logboek. Het uitvoeren van back-ups van SAP HANA transactie logboeken voor het back-upvolume van het logboek vindt plaats in peri Oden van vijf tot vijf tien minuten.

Geplande opslag momentopnamen worden het best uitgevoerd met behulp van cron. Gebruik hetzelfde script voor alle back-ups en nood herstel. Wijzig de script invoer zodat deze overeenkomt met de verschillende aangevraagde back-uptijden. Deze moment opnamen zijn allemaal anders gepland in cron, afhankelijk van de uitvoerings tijd. Dit kan elk uur, elke 12 uur, dagelijks of wekelijks zijn. 

In het volgende voor beeld ziet u een cron-schema in/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
In het vorige voor beeld wordt een samen voeging met een uur gecombineerd met de volumes die de/Hana/data en/hana/shared/SID bevatten, waaronder/usr/sap, locaties. Gebruik dit type moment opname voor een snellere herstel naar een bepaald tijdstip in de afgelopen twee dagen. Er is ook een dagelijkse moment opname op die volumes. U hebt dus twee dagen dekking per uur, plus vier weken aan dekking per dag moment opnamen. Er wordt dagelijks een back-up van het back-upvolume van het transactie logboek gemaakt. Deze back-ups worden vier weken bewaard. 

Zoals u in de derde regel van crontab ziet, wordt de back-up van het HANA-transactie logboek gepland om elke vijf minuten te worden uitgevoerd. De begin tijden van de verschillende cron-taken waarmee opslag momentopnamen worden uitgevoerd, worden gespreid. Op deze manier worden de moment opnamen niet allemaal tegelijk uitgevoerd op een bepaald moment. 

In het volgende voor beeld voert u een gecombineerde moment opname uit die de volumes bedekt die de/Hana/data en/hana/shared/SID bevatten, waaronder/usr/sap, locaties op basis van elk uur. U kunt deze moment opnamen twee dagen blijven gebruiken. De moment opnamen van de back-upvolumes van het transactie logboek worden op een periode van vijf minuten uitgevoerd en worden vier uur bewaard. Net als voorheen wordt de back-up van het HANA-transactie logboek bestand elke vijf minuten uitgevoerd. 

De moment opname van het back-upvolume van het transactie logboek wordt met een vertraging van 2 minuten uitgevoerd nadat de back-up van het transactie logboek is gestart. Onder normale omstandigheden wordt de back-up van het SAP HANA transactie logboek binnen die 2 minuten voltooid. Net als voorheen wordt een back-up van het volume met de opstart-LUN eenmaal per dag met een moment opname van de opslag gemaakt en gedurende vier weken bewaard.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

In de volgende afbeelding ziet u de volg orde van het vorige voor beeld. De opstart-LUN is uitgesloten.

![Relatie tussen back-ups en moment opnamen](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA voert regel matige schrijf bewerkingen uit op het/Hana/log-volume om de doorgevoerde wijzigingen in de data base te documenteren. SAP HANA schrijft regel matig een opslag punt naar het/Hana/data-volume. Zoals opgegeven in crontab, wordt een back-up van het transactie logboek van SAP HANA elke vijf minuten uitgevoerd. 

U ziet ook dat een moment opname van een SAP HANA elk uur wordt uitgevoerd als gevolg van het activeren van een gecombineerde opslag momentopname via de/Hana/data-en/hana/shared/SID-volumes. Nadat de HANA-moment opname is geslaagd, wordt de gecombineerde opslag momentopname uitgevoerd. Zoals een instructie in crontab, wordt de opslag momentopname op het/Hana/logbackup-volume elke vijf minuten uitgevoerd, ongeveer 2 minuten na de registratie van het HANA-transactie logboek.

> 

>[!IMPORTANT]
> Het gebruik van moment opnamen van opslag voor SAP HANA back-ups is alleen waardevol wanneer de moment opnamen in combi natie met SAP HANA transactie logboek back-ups worden uitgevoerd. Deze back-ups van transactie logboeken moeten de tijds perioden tussen de moment opnamen van de opslag hebben. 

Als u een toezeg ging hebt ingesteld voor gebruikers van een herstel naar een bepaald tijdstip van 30 dagen, moet u het volgende doen:

- Krijg in uitzonderlijke gevallen toegang tot een gecombineerde opslag momentopname via/Hana/data en/hana/shared/SID die 30 dagen oud zijn. 
- U hebt aaneengesloten back-ups van transactie logboeken die de tijd tussen een van de gecombineerde opslag momentopnamen beslaan. Daarom moet de oudste moment opname van het back-upvolume van het transactie logboek 30 dagen oud zijn. Dit is niet het geval als u de back-ups van het transactie logboek kopieert naar een andere NFS-share die zich op Azure Storage bevindt. In dat geval kunt u oude back-ups van transactie logboeken ophalen van die NFS-share.

Wijzig de locatie waarnaar SAP HANA de back-ups van transactie logboeken schrijft om te profiteren van back-ups van opslag en de uiteindelijke opslag replicatie van transactie Logboeken. U kunt deze wijziging aanbrengen in HANA Studio. 

Hoewel SAP HANA automatisch volledige logboek segmenten maakt, geeft u een logboek back-upinterval op die deterministisch moet zijn. Dit geldt met name wanneer u de optie voor nood herstel gebruikt, omdat u meestal logboek back-ups wilt uitvoeren met een deterministische periode. In het volgende geval wordt 15 minuten ingesteld als het back-upinterval van het logboek.

![SAP HANA back-uplogboeken plannen in SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

U kunt ook back-ups kiezen die vaker dan elke 15 minuten zijn. Een meer frequentere instelling wordt vaak gebruikt in combi natie met de functionaliteit voor herstel na nood gevallen van HANA grote instanties. Sommige klanten voeren elke vijf minuten back-ups van transactie Logboeken uit.

Als er nog geen back-up van de data base is gemaakt, is de laatste stap het uitvoeren van een database back-up op basis van een bestand om één back-upitem te maken dat in de back-upcatalogus moet bestaan. Anders kunnen SAP HANA de opgegeven logboek back-ups niet initialiseren.

![Maak een back-up op basis van een bestand om één back-upvermelding te maken](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Nadat de eerste geslaagde opslag momentopnamen zijn uitgevoerd, verwijdert u de moment opname van de test die u in stap 6 hebt uitgevoerd. Zie ' test moment opnamen verwijderen-removeTestStorageSnapshot ' in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie. 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Het aantal en de grootte van moment opnamen op het schijf volume bewaken

Op een specifiek opslag volume kunt u het aantal moment opnamen en het opslag verbruik van deze moment opnamen bewaken. Met de `ls` opdracht wordt de map met moment opnamen of bestanden niet weer gegeven. De Linux-besturingssysteem opdracht `du` bevat details over deze opslag momentopnamen, omdat deze zijn opgeslagen op dezelfde volumes. Gebruik de opdracht met de volgende opties:

- `du –sh .snapshot`: Deze optie biedt een totaal van alle moment opnamen in de map met moment opnamen.
- `du –sh --max-depth=1`: Met deze optie worden alle moment opnamen vermeld die zijn opgeslagen in de map **. snap shot** en de grootte van elke moment opname.
- `du –hc`: Deze optie biedt de totale grootte die wordt gebruikt door alle moment opnamen.

Gebruik deze opdrachten om ervoor te zorgen dat de moment opnamen die worden gemaakt en opgeslagen niet alle opslag op de volumes verbruiken.

>[!NOTE]
>De moment opnamen van de opstart-LUN worden niet weer gegeven met de vorige opdrachten.

### <a name="get-details-of-snapshots"></a>Details van moment opnamen ophalen
Gebruik het script om meer informatie over moment opnamen te krijgen `azure_hana_snapshot_details` . U kunt dit script uitvoeren op een van beide locaties als er een actieve server is op de locatie voor nood herstel. Het script biedt de volgende uitvoer, onderverdeeld op elk volume dat moment opnamen bevat: 
   * De grootte van het totale aantal moment opnamen in een volume
   * De volgende details in elke moment opname in dat volume: 
      - Naam van moment opname 
      - Tijd maken 
      - Grootte van de moment opname
      - Frequentie van de moment opname
      - HANA-back-up-ID die is gekoppeld aan deze moment opname, indien van toepassing

Zie voor de syntaxis van de opdracht en uitvoer ' snap shots-azure_hana_snapshot_details ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Het aantal moment opnamen op een server verminderen

Zoals eerder uitgelegd, kunt u het aantal specifieke labels van moment opnamen die u opslaat, beperken. De laatste twee para meters van de opdracht voor het initiëren van een moment opname zijn het label en het aantal moment opnamen dat u wilt behouden.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

In het vorige voor beeld is het momentopname label **dailyhana**. Het aantal moment opnamen met dit label dat moet worden bewaard, is **28**. Wanneer u reageert op het gebruik van schijf ruimte, wilt u mogelijk het aantal opgeslagen moment opnamen verlagen. Een eenvoudige manier om het aantal moment opnamen te verminderen tot 15, bijvoorbeeld om het script uit te voeren waarbij de laatste para meter is ingesteld op **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Als u het script uitvoert met deze instelling, is het aantal moment opnamen, dat de nieuwe opslag momentopname bevat, 15. De 15 meest recente moment opnamen worden bewaard en de 15 oudere moment opnamen worden verwijderd.

 >[!NOTE]
 > Dit script beperkt het aantal moment opnamen alleen als er moment opnamen zijn die meer dan één uur oud zijn. Het script verwijdert geen moment opnamen die kleiner zijn dan één uur oud. Deze beperkingen zijn gerelateerd aan de optionele functionaliteit voor herstel na nood gevallen.

Als u een set met moment opnamen niet meer wilt onderhouden met het back-upvoorvoegsel **dailyhana** in de syntaxis voorbeelden, voert u het script uit met **0** als het Bewaar nummer. Alle moment opnamen die overeenkomen met dit label worden vervolgens verwijderd. Het verwijderen van alle moment opnamen kan invloed hebben op de mogelijkheden van de functionaliteit voor herstel na nood gevallen van HANA grote instanties.

Een tweede optie voor het verwijderen van specifieke moment opnamen is het script te gebruiken `azure_hana_snapshot_delete` . Dit script is ontworpen om een moment opname of set met moment opnamen te verwijderen met behulp van de HANA-back-up-ID zoals gevonden in HANA Studio of via de naam van de moment opname zelf. Op dit moment is de back-upid alleen gekoppeld aan de moment opnamen die zijn gemaakt voor het **Hana** -momentopname type. Momentopname back-ups van het type **Logboeken** en **opstarten** voeren geen SAP Hana moment opname uit, dus er is geen back-upid voor deze moment opnamen. Als de naam van de moment opname wordt ingevoerd, zoekt deze naar alle moment opnamen op de verschillende volumes die overeenkomen met de opgegeven naam van de moment opname. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Zie ' een moment opname verwijderen azure_hana_snapshot_delete ' in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie over het script.

Voer het script uit als **hoofdmap** van de gebruiker.

>[!IMPORTANT]
>Als er gegevens zijn die alleen bestaan op de moment opname die u wilt verwijderen, gaan de gegevens na de moment opname verloren.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Herstel op bestands niveau vanuit een moment opname van een opslag

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Voor de momentopname typen **Hana** en **logs**, hebt u rechtstreeks toegang tot de moment opnamen op de volumes in de map **. snap shot** . Er is een submap voor elk van de moment opnamen. Kopieer elk bestand in de staat waarin het zich bevond in het punt van de moment opname van die submap in de daad werkelijke mapstructuur. 

In de huidige versie van het script is er *geen* herstel script voor het terugzetten van de moment opname als self-service. Het terugzetten van de moment opname kan worden uitgevoerd als onderdeel van de scripts voor herstel na nood geval tijdens een failover. Als u een gewenste moment opname van de bestaande beschik bare moment opnamen wilt herstellen, moet u contact opnemen met het micro soft Operations-team door een service aanvraag te openen.

>[!NOTE]
>Het herstellen van één bestand werkt niet voor moment opnamen van de opstart-LUN onafhankelijk van het type van de HANA grote instantie-eenheden. De map **. snap shot** wordt niet weer gegeven in de opstart-LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Herstellen naar de meest recente HANA-moment opname

In een scenario met productie kan het proces van het herstellen van een opslag momentopname worden gestart als een klant incident met Microsoft Azure ondersteuning. Het is zeer urgent als gegevens in een productie systeem zijn verwijderd en de enige manier om deze op te halen, het herstellen van de productie database.

In een andere situatie is het mogelijk dat herstel naar een bepaald tijdstip weinig urgentie en geplande dagen van tevoren zijn. U kunt dit herstel plannen met SAP HANA op Azure in plaats van een vlag met hoge prioriteit te verhogen. U kunt bijvoorbeeld plannen om de SAP-software bij te werken door een nieuw uitbreidings pakket toe te passen. Vervolgens moet u terugkeren naar een moment opname die de status voor de upgrade van het pakket aangeeft.

Voordat u de aanvraag verzendt, moet u de voor bereiding doen. De SAP HANA op het Azure-team kan vervolgens de aanvraag afhandelen en de herstelde volumes opgeven. Daarna herstelt u de HANA-Data Base op basis van de moment opnamen.

Voor de mogelijkheden van het ophalen van een moment opname met de nieuwe set hulp middelen raadpleegt u "een moment opname herstellen" in [hand matige herstel handleiding voor SAP Hana op Azure vanuit een moment opname van de opslag](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Voer de volgende stappen uit om de aanvraag voor te bereiden.

1. Bepaal welke moment opname moet worden hersteld. Alleen het Hana/gegevens volume wordt hersteld, tenzij u anders een instructie geeft. 

1. Sluit het HANA-exemplaar af.

   ![Het HANA-exemplaar afsluiten](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Ontkoppel de gegevens volumes op elk HANA-database knooppunt. Als de gegevens volumes nog steeds aan het besturings systeem zijn gekoppeld, mislukt het herstellen van de moment opname.

   ![De gegevens volumes op elk HANA-database knooppunt ontkoppelen](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Open een ondersteunings aanvraag voor Azure en neem instructies op over het herstel van een specifieke moment opname:

   - Tijdens de herstel bewerking: SAP HANA op de Azure-service wordt u mogelijk gevraagd om een telefonische oproep te nemen om te coördineren, te controleren en te bevestigen dat de juiste opslag momentopname is hersteld. 

   - Nadat de herstel bewerking is SAP HANA op de Azure-service, ontvangt u een melding wanneer de moment opname van de opslag is teruggezet.

1. Nadat het herstel proces is voltooid, koppelt u alle gegevens volumes opnieuw.

   ![Alle gegevens volumes opnieuw koppelen](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Een andere mogelijkheid om bijvoorbeeld SAP HANA gegevens bestanden te verkrijgen die zijn hersteld vanuit een moment opname van een opslag, wordt beschreven in stap 7 in [hand matige herstel handleiding voor SAP Hana op Azure vanuit een opslag momentopname](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Als u wilt herstellen vanuit een back-up van moment opnamen, raadpleegt u [hand matige herstel handleiding voor SAP Hana op Azure vanuit een opslag momentopname](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Als uw moment opname is hersteld door micro soft Operations, hoeft u stap 7 niet uit te voeren.


### <a name="recover-to-another-point-in-time"></a>Herstellen naar een ander tijdstip
Als u naar een bepaald tijdstip wilt herstellen, raadpleegt u ' de data base herstellen naar het volgende tijdstip ' in [hand matige herstel handleiding voor SAP Hana op Azure vanuit een moment opname van de opslag](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 





## <a name="snapcenter-integration-in-sap-hana-large-instances"></a>SnapCenter-integratie in SAP HANA grote instanties

In deze sectie wordt beschreven hoe klanten NetApp SnapCenter-software kunnen gebruiken om een moment opname, back-up en herstel te maken SAP HANA data bases die worden gehost op Microsoft Azure HANA grote instanties (HLI). 

SnapCenter biedt oplossingen voor scenario's zoals back-up/herstel, herstel na nood gevallen (DR) met asynchrone opslag replicatie, systeem replicatie en klonen van het systeem. Klanten die zijn geïntegreerd met SAP HANA Large Instances op Azure, kunnen nu gebruikmaken van SnapCenter voor back-up-en herstel bewerkingen.

Zie NetApp TR-4614 en TR-4646 op SnapCenter voor aanvullende informatie.

- [SAP HANA back-up/herstel met SnapCenter (TR-4614)](https://www.netapp.com/us/media/tr-4614.pdf)
- [SAP HANA herstel na nood gevallen met opslag replicatie (TR-4646)](https://www.netapp.com/us/media/tr-4646.pdf)
- [SAP HANA HSR met SnapCenter (TR-4719)](https://www.netapp.com/us/media/tr-4719.pdf)
- [SAP klonen vanuit SnapCenter (TR-4667)](https://www.netapp.com/us/media/tr-4667.pdf)

### <a name="system-requirements-and-prerequisites"></a>Systeem vereisten en-vereisten

Voor het uitvoeren van SnapCenter op Azure HLI, zijn de systeem vereisten:
* SnapCenter-server op Azure Windows 2016 of nieuwer met 4-vCPU, 16 GB RAM en mini maal 650 GB Managed Premium SSD-opslag.
* SAP HANA Large Instances systeem met 1,5 TB – 24 TB RAM-geheugen. Het is raadzaam om twee SAP HANA grote instantie systemen te gebruiken voor het klonen van bewerkingen en tests.

De stappen voor het integreren van SnapCenter in SAP HANA zijn: 

1. Verhoog een ondersteunings ticket aanvraag om de door de gebruiker gegenereerde open bare sleutel te communiceren met het micro soft ops-team. Dit is vereist om de SnapCenter-gebruiker in te stellen voor toegang tot het opslag systeem.
1. Maak een virtuele machine in uw VNET die toegang heeft tot HLI; deze VM wordt gebruikt voor SnapCenter. 
1. Down load en installeer SnapCenter. 
1. Back-up-en herstel bewerkingen. 

### <a name="create-a-support-ticket-for-user-role-storage-setup"></a>Een ondersteunings ticket maken voor het instellen van de opslag van gebruikers rollen

1. Open de Azure Portal en navigeer naar de pagina **abonnementen** . Selecteer uw SAP HANA-abonnement op de pagina Abonnementen, zoals hieronder rood wordt beschreven.

   :::image type="content" source="./media/snapcenter/create-support-case-for-user-role-storage-setup.png" alt-text="Ondersteunings Case voor het instellen van gebruikers opslag maken":::

1. Selecteer op de pagina SAP HANA abonnement de pagina **resource groepen** .

   :::image type="content" source="./media/snapcenter/solution-lab-subscription-resource-groups.png" alt-text="Resource groep voor Solution Lab-abonnement" lightbox="./media/snapcenter/solution-lab-subscription-resource-groups.png":::

1. Selecteer een geschikte resource groep in een regio.

   :::image type="content" source="./media/snapcenter/select-appropriate-resource-group-in-region.png" alt-text="Selecteer de juiste resource groep in de regio" lightbox="./media/snapcenter/select-appropriate-resource-group-in-region.png":::

1. Selecteer een SKU-vermelding die overeenkomt met SAP HANA in azure Storage.

   :::image type="content" source="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png" alt-text="Selecteer de SKU-vermelding die overeenkomt met SAP HANA" lightbox="./media/snapcenter/select-sku-entry-corresponding-to-sap-hana.png":::

1. Open een nieuwe aanvraag voor een **ondersteunings ticket** , in rood beschreven.

   :::image type="content" source="./media/snapcenter/open-new-support-ticket-request.png" alt-text="Nieuwe aanvraag voor ondersteunings ticket openen":::

1. Geef op het tabblad **basis beginselen** de volgende informatie op voor het ticket:

   * **Type probleem:** Documentatie
   * **Abonnement:** Uw abonnement
   * **Service:** SAP HANA grote instantie
   * **Resource:** De resource groep
   * **Samen vatting:** De door de gebruiker gegenereerde open bare sleutel opgeven
   * **Probleem type:** Configuratie en installatie
   * **Subtype van probleem:** SnapCenter instellen voor HLI


1. Geef in de **Beschrijving** van het ondersteunings ticket op het tabblad **Details** het volgende op: 
   
   * SnapCenter instellen voor HLI
   * Uw open bare sleutel voor SnapCenter gebruiker (SnapCenter. pem): Zie het volgende voor beeld van een open bare sleutel maken

     :::image type="content" source="./media/snapcenter/new-support-request-details.png" alt-text="Nieuwe ondersteunings aanvraag-tabblad Details" lightbox="./media/snapcenter/new-support-request-details.png":::

1. Selecteer **controleren + maken** om uw ondersteunings ticket te controleren. 

1. Genereer een certificaat voor de SnapCenter-gebruikers naam op de HANA grote instantie of een Linux-server.

   SnapCenter vereist een gebruikers naam en wacht woord voor toegang tot de virtuele opslag machine (SVM) en om moment opnamen van de HANA-data base te maken. Micro soft gebruikt de open bare sleutel om u (de klant) toe te staan om het wacht woord voor toegang tot het opslag systeem in te stellen.

   ```bash
   openssl req -x509 -nodes -days 1095 -newkey rsa:2048 -keyout snapcenter.key -out snapcenter.pem -subj "/C=US/ST=WA/L=BEL/O=NetApp/CN=snapcenter"
   Generating a 2048 bit RSA private key
   ................................................................................................................................................+++++
   ...............................+++++
   writing new private key to 'snapcenter.key'
   -----

   sollabsjct31:~ # ls -l cl25*
   -rw-r--r-- 1 root root 1704 Jul 22 09:59 snapcenter.key
   -rw-r--r-- 1 root root 1253 Jul 22 09:59 snapcenter.pem

   ```

1. Koppel het bestand snapcenter. pem aan het ondersteunings ticket en selecteer vervolgens **maken**

   Zodra het certificaat van de open bare sleutel is verzonden, stelt micro soft de SnapCenter-gebruikers naam in voor uw Tenant samen met het IP-adres van SVM.   

1. Nadat u het SVM IP-adres hebt ontvangen, moet u een wacht woord instellen voor toegang tot SVM, die u beheert.

   Hier volgt een voor beeld van de REST-AANROEP (documentatie) van HANA grote instantie of VM in het virtuele netwerk, die toegang heeft tot de HANA-omgeving voor grote instanties en die wordt gebruikt om het wacht woord in te stellen.

   ```bash
   curl --cert snapcenter.pem --key snapcenter.key -X POST -k "https://10.0.40.11/api/security/authentication/password" -d '{"name":"snapcenter","password":"test1234"}'
   ```

   Zorg ervoor dat er geen proxy variabele actief is op het HANA DB-systeem.

   ```bash
   sollabsjct31:/tmp # unset http_proxy
   sollabsjct31:/tmp # unset https_proxy
   ```

### <a name="download-and-install-snapcenter"></a>SnapCenter downloaden en installeren
Nu de gebruikers naam is ingesteld voor SnapCenter toegang tot het opslag systeem, gebruikt u de gebruikers naam van SnapCenter om de SnapCenter te configureren zodra deze is geïnstalleerd. 

Controleer [SAP Hana back-up/herstel met SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf) om uw back-upstrategie te definiëren voordat u SnapCenter installeert. 

1. Meld u aan bij [NetApp](https://mysupport.netapp.com) om de meest recente versie van SnapCenter te [downloaden](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fmysupport.netapp.com%2Fsite%2Fproducts%2Fall%2Fdetails%2Fsnapcenter%2Fdownloads-tab&data=02%7C01%7Cmadhukan%40microsoft.com%7Ca53f5e2f245a4e36933008d816efbb54%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637284566603265503&sdata=TOANWNYoAr1q5z1opu70%2FUDPHjluvovqR9AKplYpcpk%3D&reserved=0) .

1. Installeer SnapCenter op de virtuele Windows Azure-machine.

   Het installatie programma controleert de vereisten van de virtuele machine. 

   >[!IMPORTANT]
   >Let op de grootte van de virtuele machine, met name in grotere omgevingen.

1. Configureer de gebruikers referenties voor de SnapCenter. Standaard worden de Windows-gebruikers referenties ingevuld die worden gebruikt voor het installeren van de toepassing. 

   :::image type="content" source="media/snapcenter/installation-user-inputs-dialog.png" alt-text="Dialoog venster invoer van gebruiker voor installatie"::: 

1. Wanneer u de sessie start, slaat u de beveiligings uitzondering op en wordt de gebruikers interface gestart.

1. Meld u aan bij SnapCenter op de virtuele machine ( https://snapcenter-vm:8146) met behulp van de Windows-referenties voor het configureren van de omgeving.


### <a name="set-up-the-storage-system"></a>Het opslag systeem instellen

1. Selecteer in SnapCenter **opslag systeem** en selecteer **+ Nieuw**. 

   :::image type="content" source="./media/snapcenter/snapcenter-storage-connections-window.png" alt-text="SnapCenter-opslag verbindingen" lightbox="./media/snapcenter/snapcenter-storage-connections-window.png":::

   De standaard waarde is één SVM per Tenant. Als een klant meerdere tenants of HLIs in meerdere regio's heeft, is het aanbeveling om alle SVMs te configureren in SnapCenter

1. Geef in opslag systeem toevoegen de informatie op voor het opslag systeem dat u wilt toevoegen, de SnapCenter gebruikers naam en het wacht woord en selecteer vervolgens **verzenden**.

   :::image type="content" source="./media/snapcenter/new-storage-connection.png" alt-text="Nieuwe opslag verbinding":::

   >[!NOTE]
   >De standaard waarde is één SVM per Tenant.  Als er meerdere tenants zijn, is het aanbeveling om alle SVMs hier in SnapCenter te configureren. 

1. Selecteer in SnapCenter de optie **hosts** en selecteer **+ toevoegen** om de Hana-INVOEG toepassing en de Hana DB-hosts in te stellen.  De meest recente versie van SnapCenter detecteert de HANA-data base automatisch op de host.

   :::image type="content" source="media/snapcenter/managed-hosts-new-host.png" alt-text="Selecteer in SnapCenter de optie hosts en selecteer vervolgens toevoegen." lightbox="media/snapcenter/managed-hosts-new-host.png":::

1. Geef de gegevens voor de nieuwe host op:
   1. Selecteer het besturings systeem voor het type host.
   1. Voer de hostnaam van de SnapCenter-VM in.
   1. Geef de referenties op die u wilt gebruiken.
   1. Selecteer de opties **micro soft Windows** en **SAP Hana** en selecteer vervolgens **verzenden**.

   :::image type="content" source="media/snapcenter/add-new-host-operating-system-credentials.png" alt-text="Informatie voor nieuwe host":::

   >[!IMPORTANT]
   >Voordat u het eerste knoop punt kunt installeren, kan een niet-hoofd gebruiker SnapCenter invoeg toepassingen op de data base installeren.  Zie [een niet-hoofd gebruiker toevoegen en sudo-bevoegdheden configureren](https://library.netapp.com/ecmdocs/ECMLP2590889/html/GUID-A3EEB5FC-242B-4C2C-B407-510E48A8F131.html)voor meer informatie over het inschakelen van een niet-hoofd gebruiker.

1. Controleer de details van de host en selecteer **verzenden** om de invoeg toepassing op de SnapCenter-server te installeren.

1. Nadat de invoeg toepassing is geïnstalleerd, selecteert u in SnapCenter de optie **hosts** en selecteert u vervolgens **+ toevoegen** om een Hana-knoop punt toe te voegen.

   :::image type="content" source="media/snapcenter/add-hana-node.png" alt-text="Een HANA-knoop punt toevoegen" lightbox="media/snapcenter/add-hana-node.png":::

1. Geef de informatie op voor het HANA-knoop punt:
   1. Selecteer het besturings systeem voor het type host.
   1. Voer de hostnaam of het IP-adres van de HANA-data base in.
   1. Selecteer **+** deze optie om de referenties toe te voegen die zijn geconfigureerd op het besturings systeem Hana db-host en selecteer vervolgens **OK**.
   1. Selecteer **SAP Hana** en selecteer vervolgens **verzenden**.

   :::image type="content" source="media/snapcenter/add-hana-node-details.png" alt-text="Details van SAP HANA knoop punt":::

1. Bevestig de vinger afdruk en selecteer **bevestigen en verzenden**.

   :::image type="content" source="media/snapcenter/confirm-submit-fingerprint.png" alt-text="Bevestigen en verzenden van vinger afdruk":::

1. Selecteer op het Hana-knoop punt, onder de systeem database, de optie **beveiligings**  >  **gebruikers**  >  **SNAPCENTER** om de SNAPCENTER-gebruiker te maken.

   :::image type="content" source="media/snapcenter/create-snapcenter-user-hana-system-db.png" alt-text="De SnapCenter-gebruiker maken in HANA (systeem database)":::



### <a name="auto-discovery"></a>Automatische detectie
Met SnapCenter 4,3 wordt de functie Automatische detectie standaard ingeschakeld.  Automatische detectie wordt niet ondersteund voor HANA-instanties met HANA System Replication (HSR) geconfigureerd. U moet het exemplaar hand matig toevoegen aan de SnapCenter-server.


### <a name="hana-setup-manual"></a>HANA-instellingen (hand matig)
Als u HSR hebt geconfigureerd, moet u het systeem hand matig configureren.  

1. Selecteer in SnapCenter **resources** en **San Hana** (bovenaan) en selecteer vervolgens **+ SAP Hana data base toevoegen** (aan de rechter kant).

   :::image type="content" source="media/snapcenter/manual-hana-setup.png" alt-text="Hand matige HANA-instellingen" lightbox="media/snapcenter/manual-hana-setup.png":::

1. Geef de resource details op van de HANA-beheerder die door de gebruiker is geconfigureerd op de Linux-host of op de host waarop de invoeg toepassingen zijn geïnstalleerd. De back-up wordt beheerd vanuit de invoeg toepassing op het Linux-systeem.

   :::image type="content" source="media/snapcenter/provide-resource-details-sap-hana-database.png" alt-text="Geef de resource details op van de HANA-beheerder die door de gebruiker is geconfigureerd op de Linux-host.":::

1. Selecteer het gegevens volume waarvoor u moment opnamen wilt maken, selecteer **Opslaan** en selecteer vervolgens **volt ooien**.

   :::image type="content" source="media/snapcenter/provide-storage-footprint.png" alt-text="Selecteer het gegevens volume waarvoor u moment opnamen wilt maken, selecteer Opslaan en selecteer vervolgens volt ooien.":::

### <a name="create-a-snapshot-policy"></a>Een momentopname beleid maken

Voordat u SnapCenter gebruikt om een back-up van SAP HANA database resources te maken, moet u een back-upbeleid voor de resource of resource groep die u wilt back-ups. Tijdens het proces van het maken van een snap shot-beleid krijgt u de optie om pre/post-opdrachten en speciale SSL-sleutels te configureren. Zie [back-upbeleid maken voor SAP Hana-data bases](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html)voor meer informatie over het maken van een momentopname beleid.

1. Selecteer in SnapCenter **resources** en selecteer vervolgens een Data Base.

   :::image type="content" source="media/snapcenter/select-database-create-policy.png" alt-text="Selecteer in SnapCenter resources en selecteer vervolgens een Data Base.":::

1. Volg de werk stroom van de configuratie wizard om de snap shot scheduler te configureren.

   :::image type="content" source="media/snapcenter/follow-workflow-configuration-wizard.png" alt-text="Volg de werk stroom van de configuratie wizard om de snap shot scheduler te configureren." lightbox="media/snapcenter/follow-workflow-configuration-wizard.png":::

1. Geef de opties op voor het configureren van pre/post-opdrachten en speciale SSL-sleutels.  In dit voor beeld gebruiken we geen speciale instellingen.

   :::image type="content" source="media/snapcenter/configuration-options-pre-post-commands.png" alt-text="Geef de opties op voor het configureren van pre-post opdrachten en speciale SSL-sleutels." lightbox="media/snapcenter/configuration-options-pre-post-commands.png":::

1. Selecteer **toevoegen** om een momentopname beleid te maken, dat ook kan worden gebruikt voor andere Hana-data bases. 

   :::image type="content" source="media/snapcenter/select-one-or-more-policies.png" alt-text="Selecteer toevoegen om een momentopname beleid te maken, dat ook kan worden gebruikt voor andere HANA-data bases.":::

1. Voer de naam van het beleid en een beschrijving in.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy.png" alt-text="Voer de naam van het beleid en een beschrijving in.":::


1. Selecteer het type en de frequentie van de back-up.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-settings.png" alt-text="Selecteer het type en de frequentie van de back-up.":::

1. Configureer de **instellingen voor het bewaren van back-ups op aanvraag**.  In ons voor beeld stellen we de Bewaar periode in op drie momentopname kopieën die moeten worden bewaard.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-retention-settings.png" alt-text="Configureer de instellingen voor het bewaren van back-ups op aanvraag.":::

1. Configureer de instellingen voor het bewaren van het **hele uur**. 

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-hourly-retention-settings.png" alt-text="Configureer de instellingen voor het bewaren van het hele uur.":::

1. Als een SnapMirror-installatie is geconfigureerd, selecteert u **SnapMirror bijwerken na het maken van een kopie van een lokale moment opname**.

   :::image type="content" source="media/snapcenter/new-sap-hana-backup-policy-snapmirror.png" alt-text="Als een SnapMirror is vereist, selecteert u update SnapMirror na het maken van een lokale kopie van de moment opname.":::

1. Selecteer **volt ooien** om de samen vatting van het nieuwe back-upbeleid te bekijken. 
1. Selecteer onder **schema configureren** de optie **toevoegen**.

   :::image type="content" source="media/snapcenter/configure-schedules-for-selected-policies.png" alt-text="Selecteer onder schema configureren de optie toevoegen.":::

1. Selecteer de **begin datum**, **verloopt op** datum en de frequentie.

   :::image type="content" source="media/snapcenter/add-schedules-for-policy.png" alt-text="Selecteer de begin datum, verloopt op datum en de frequentie.":::

1. Geef de e-mail gegevens voor meldingen op.

   :::image type="content" source="media/snapcenter/backup-policy-notification-settings.png" alt-text="Geef de e-mail gegevens voor meldingen op.":::

1.  Selecteer **volt ooien** om het back-upbeleid te maken.

### <a name="disable-ems-message-to-netapp-autosupport"></a>EMS-bericht uitschakelen voor NetApp-ondersteuning
EMS-gegevens verzameling is standaard ingeschakeld en wordt elke zeven dagen na de installatie datum uitgevoerd.  U kunt gegevens verzameling uitschakelen met de Power shell-cmdlet `Disable-SmDataCollectionEms` .

1. Maak in Power shell een sessie met SnapCenter.

   ```powershell
   Open-SmConnection
   ```

1. Meld u aan met uw referenties.
1. Schakel de verzameling EMS-berichten uit.

   ```powershell
   Disable-SmCollectionEms
   ```

### <a name="restore-database-after-crash"></a>Data base herstellen na crash
U kunt SnapCenter gebruiken om de data base te herstellen.  In deze sectie worden de stappen op hoog niveau behandeld, maar Zie [SAP Hana back-up/herstel met SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf)voor meer informatie.


1. De data base stoppen en alle database bestanden verwijderen.

   ```
   su - h31adm
   > sapcontrol -nr 00 -function StopSystem
   StopSystem
   OK
   > sapcontrol -nr 00 -function GetProcessList
   OK
   name, description, dispstatus, textstatus, starttime, elapsedtime, pid
   hdbdaemon, HDB Daemon, GRAY, Stopped, , , 35902
 
   ```

1. Ontkoppel het database volume.

   ```bash
   unmount /hana/data/H31/mnt00001
   ```


1. Herstel de database bestanden via SnapCenter.  Selecteer de data base en selecteer vervolgens **herstellen**.  

   :::image type="content" source="media/snapcenter/restore-database-via-snapcenter.png" alt-text="Selecteer een Data Base en selecteer herstellen." lightbox="media/snapcenter/restore-database-via-snapcenter.png":::

1. Selecteer het type herstel.  In ons voor beeld herstellen we de volledige resource. 

   :::image type="content" source="media/snapcenter/restore-database-select-restore-type.png" alt-text="Selecteer het type herstel.":::

   >[!NOTE]
   >Met een standaard instelling hoeft u geen opdrachten op te geven om een lokale herstel bewerking uit te voeren vanaf de moment opname op de schijf. 

   >[!TIP]
   >Als u een bepaald LUN in het volume wilt herstellen, selecteert u **bestands niveau**.

1. Volg de werk stroom met behulp van de configuratie wizard.
   
   SnapCenter herstelt de oorspronkelijke locatie van de gegevens, zodat u het herstel proces in HANA kunt starten. Omdat SnapCenter de back-upcatalogus niet kan wijzigen (Data Base is niet beschikbaar), wordt er een waarschuwing weer gegeven.

   :::image type="content" source="media/snapcenter/restore-database-job-details-warning.png" alt-text="Omdat SnapCenter de back-upcatalogus niet kan wijzigen, wordt er een waarschuwing weer gegeven. ":::

1. Omdat alle database bestanden zijn hersteld, start u het herstel proces in HANA. Klik in Hana studio onder **systemen** met de rechter muisknop op de systeem database en selecteer **back-up en herstel**  >  **systeem database herstellen**.

   :::image type="content" source="media/snapcenter/hana-studio-backup-recovery.png" alt-text="Start het herstel proces in HANA.":::

1. Selecteer een herstel type.

   :::image type="content" source="media/snapcenter/restore-database-select-recovery-type.png" alt-text="Selecteer het herstel type.":::

1. Selecteer de locatie van de back-upcatalogus.

   :::image type="content" source="media/snapcenter/restore-database-select-location-backup-catalog.png" alt-text="Selecteer de locatie van de back-upcatalogus.":::

1. Selecteer een back-up om de SAP HANA-data base te herstellen.

   :::image type="content" source="media/snapcenter/restore-database-select-backup.png" alt-text="Selecteer een back-up om de SAP HANA-data base te herstellen.":::

   Zodra de data base is hersteld, wordt een bericht weer gegeven met een **hersteld naar tijd** en **teruggezet naar de logboek positie** stempel.

1. Onder **systemen** klikt u met de rechter muisknop op de systeem database en selecteert u **back-up en herstel**  >  **Tenant database herstellen**.
1. Volg de werk stroom van de wizard om het herstel van de Tenant database te volt ooien. 

Zie [SAP Hana backup/rerecovery with SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf)(Engelstalig) voor meer informatie over het herstellen van een Data Base.


### <a name="non-database-backups"></a>Back-ups zonder data base
U kunt niet-gegevens volumes herstellen, bijvoorbeeld een netwerk bestands share (/Hana/Shared) of een back-up van een besturings systeem.  Zie [SAP Hana backup/rerecovery with SnapCenter](https://www.netapp.com/us/media/tr-4614.pdf)(Engelstalig) voor meer informatie over het herstellen van een niet-gegevens volume.

### <a name="sap-hana-system-cloning"></a>SAP HANA systeem klonen

Voordat u kunt klonen, moet dezelfde HANA-versie zijn geïnstalleerd als de bron database. De SID en ID kunnen verschillen. 

:::image type="content" source="media/snapcenter/system-cloning-diagram.png" alt-text="SAP HANA systeem klonen" lightbox="media/snapcenter/system-cloning-diagram.png" border="false":::

1. Een HANA-database gebruikers archief maken voor de H34-data base van/usr/sap/H34/HDB40.

   ```
   hdbuserstore set H34KEY sollabsjct34:34013 system manager
   ```
 
1. Schakel de firewall uit.

   ```bash
   systemctl disable SuSEfirewall2
   systemctl stop  SuSEfirewall2
   ```

1. Installeer de Java-SDK.

   ```bash
   zypper in java-1_8_0-openjdk
   ```

1. Voeg in SnapCenter de doelhost toe waarop de kloon wordt gekoppeld. Zie [hosts toevoegen en invoeg toepassings pakketten installeren op externe hosts](http://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-sap-hana%2FGUID-246C0810-4F0B-4BF7-9A35-B729AD69954A.html)voor meer informatie.
   1. Geef de gegevens voor de run as-referenties op die u wilt toevoegen. 
   1. Selecteer het besturings systeem van de host en voer de gegevens van de host in.
   1. Onder **invoeg toepassingen die u wilt installeren**, selecteert u de versie, voert u het installatiepad in en selecteert u **SAP Hana**.
   1. Selecteer **valideren** om de controles voorafgaand aan de installatie uit te voeren.

1. Stop HANA en ontkoppel het oude gegevens volume.  U koppelt de kloon vanuit SnapCenter.  

   ```bash
   sapcontrol -nr 40 -function StopSystem
   umount /hana/data/H34/mnt00001

   ```
 1. Maak de configuratie-en shell script bestanden voor het doel.
 
    ```bash
    mkdir /NetApp
    chmod 777 /NetApp
    cd NetApp
    chmod 777 sc-system-refresh-H34.cfg
    chmod 777 sc-system-refresh.sh

    ```

    >[!TIP]
    >U kunt de scripts van [SAP klonen kopiëren vanuit SnapCenter](https://www.netapp.com/us/media/tr-4667.pdf).

1. Wijzig het configuratie bestand. 

   ```bash
   vi sc-system-refresh-H34.cfg
   ```

   * HANA_ARCHITECTURE = "MDC_single_tenant"
   * KEY = "H34KEY"
   * TIME_OUT_START = 18
   * TIME_OUT_STOP = 18
   * INSTANCENO = "40"
   * OPSLAG = "10.250.101.33"

1. Wijzig het shell script bestand.

   ```bash
   vi sc-system-refresh.sh
   ```  

   * UITGEBREID = Nee
   * MY_NAME = " `basename $0` "
   * BASE_SCRIPT_DIR = " `dirname $0` "
   * MOUNT_OPTIONS = "RW, vers = 4, hard, Timeo = 600, rsize = 1048576, wsize = 1048576, intr, noatime, NOLOCK"

1. Start de kloon vanuit een back-upproces. Selecteer de host om de kloon te maken. 

   >[!NOTE]
   >Zie [klonen vanuit een back-up](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html)voor meer informatie.

1. Onder **scripts**, geeft u het volgende op:

   * **Mount-opdracht:** /NetApp/SC-System-refresh.sh Mount H34% hana_data_h31_mnt00001_t250_vol_Clone
   * **Post-kloon opdracht:** /NetApp/SC-System-refresh.sh herstellen H34

1. De automatische koppeling in de bestand/etc/fstab uitschakelen (vergren delen) omdat het gegevens volume van de vooraf geïnstalleerde data base niet nodig is. 

   ```bash
   vi /etc/fstab
   ```

### <a name="delete-a-clone"></a>Een kloon verwijderen

U kunt een kloon verwijderen als deze niet langer nodig is. Zie [klonen verwijderen](https://docs.netapp.com/ocsc-43/index.jsp?topic=%2Fcom.netapp.doc.ocsc-dpg-cpi%2FGUID-F6E7FF73-0183-4B9F-8156-8D7DA17A8555.html)voor meer informatie.

De opdrachten die worden gebruikt voor het uitvoeren van een kloon, zijn:
* **Verwijdering vooraf klonen:** /NetApp/SC-System-refresh.sh H34 afsluiten
* **Ontkoppelen:** /NetApp/SC-System-refresh.sh umount H34

Met deze opdrachten staat u SnapCenter toe om de data base te Showdown, het volume te ontkoppelen en de fstab-vermelding te verwijderen.  Daarna wordt de FlexClone verwijderd. 

### <a name="cloning-database-logfile"></a>Database logboek klonen

```   
20190502025323###sollabsjct34###sc-system-refresh.sh: Adding entry in /etc/fstab.
20190502025323###sollabsjct34###sc-system-refresh.sh: 10.250.101.31:/Sc21186309-ee57-41a3-8584-8210297f791d /hana/data/H34/mnt00001 nfs rw,vers=4,hard,timeo=600,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
20190502025323###sollabsjct34###sc-system-refresh.sh: Mounting data volume.
20190502025323###sollabsjct34###sc-system-refresh.sh: mount /hana/data/H34/mnt00001
20190502025323###sollabsjct34###sc-system-refresh.sh: Data volume mounted successfully.
20190502025323###sollabsjct34###sc-system-refresh.sh: chown -R h34adm:sapsys /hana/data/H34/mnt00001
20190502025333###sollabsjct34###sc-system-refresh.sh: Recover system database.
20190502025333###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/HDB40/exe/Python/bin/python /usr/sap/H34/HDB40/exe/python_support/recoverSys.py --command "RECOVER DATA USING SNAPSHOT CLEAR LOG"
[140278542735104, 0.005] >> starting recoverSys (at Thu May  2 02:53:33 2019)
[140278542735104, 0.005] args: ()
[140278542735104, 0.005] keys: {'command': 'RECOVER DATA USING SNAPSHOT CLEAR LOG'}
recoverSys started: ============2019-05-02 02:53:33 ============
testing master: sollabsjct34
sollabsjct34 is master
shutdown database, timeout is 120
stop system
stop system: sollabsjct34
stopping system: 2019-05-02 02:53:33
stopped system: 2019-05-02 02:53:33
creating file recoverInstance.sql
restart database
restart master nameserver: 2019-05-02 02:53:38
start system: sollabsjct34
2019-05-02T02:53:59-07:00  P010976      16a77f6c8a2 INFO    RECOVERY state of service: nameserver, sollabsjct34:34001, volume: 1, RecoveryPrepared
recoverSys finished successfully: 2019-05-02 02:54:00
[140278542735104, 26.490] 0
[140278542735104, 26.490] << ending recoverSys, rc = 0 (RC_TEST_OK), after 26.485 secs
20190502025400###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is started ....
20190502025400###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025410###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025420###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025430###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025440###sollabsjct34###sc-system-refresh.sh: Status:  YELLOW
20190502025451###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502025451###sollabsjct34###sc-system-refresh.sh: SAP HANA database is started.
20190502025451###sollabsjct34###sc-system-refresh.sh: Recover tenant database H34.
20190502025451###sollabsjct34###sc-system-refresh.sh: /usr/sap/H34/SYS/exe/hdb/hdbsql -U H34KEY RECOVER DATA FOR H34 USING SNAPSHOT CLEAR LOG
0 rows affected (overall time 69.584135 sec; server time 69.582835 sec)
20190502025600###sollabsjct34###sc-system-refresh.sh: Checking availability of Indexserver for tenant H34.
20190502025601###sollabsjct34###sc-system-refresh.sh: Recovery of tenant database H34 succesfully finished.
20190502025601###sollabsjct34###sc-system-refresh.sh: Status: GREEN
Deleting the DB Clone – Logfile
20190502030312###sollabsjct34###sc-system-refresh.sh: Stopping HANA database.
20190502030312###sollabsjct34###sc-system-refresh.sh: sapcontrol -nr 40 -function StopSystem HDB

02.05.2019 03:03:12
StopSystem
OK
20190502030312###sollabsjct34###sc-system-refresh.sh: Wait until SAP HANA database is stopped ....
20190502030312###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030322###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030332###sollabsjct34###sc-system-refresh.sh: Status:  GREEN
20190502030342###sollabsjct34###sc-system-refresh.sh: Status:  GRAY
20190502030342###sollabsjct34###sc-system-refresh.sh: SAP HANA database is stopped.
20190502030347###sollabsjct34###sc-system-refresh.sh: Unmounting data volume.
20190502030347###sollabsjct34###sc-system-refresh.sh: Junction path: Sc21186309-ee57-41a3-8584-8210297f791d
20190502030347###sollabsjct34###sc-system-refresh.sh: umount /hana/data/H34/mnt00001
20190502030347###sollabsjct34###sc-system-refresh.sh: Deleting /etc/fstab entry.
20190502030347###sollabsjct34###sc-system-refresh.sh: Data volume unmounted successfully.

```

### <a name="uninstall-snapcenter-plug-ins-package-for-linux"></a>Pakket met SnapCenter-invoeg toepassingen voor Linux verwijderen

U kunt het pakket voor Linux-invoeg toepassingen verwijderen vanaf de opdracht regel. Omdat de automatische implementatie een nieuw systeem verwacht, is het eenvoudig om de invoeg toepassing te verwijderen.  

>[!NOTE]
>Mogelijk moet u een oudere versie van de invoeg toepassing hand matig verwijderen. 

Verwijder de invoeg toepassingen.

```bash
cd /opt/NetApp/snapcenter/spl/installation/plugins
./uninstall
```

U kunt nu de nieuwste HANA-invoeg toepassing installeren op het nieuwe knoop punt door **verzenden** te selecteren in SnapCenter. 




## <a name="next-steps"></a>Volgende stappen
- Zie [principes van herstel na nood gevallen en voor bereiding](hana-concept-preparation.md).
