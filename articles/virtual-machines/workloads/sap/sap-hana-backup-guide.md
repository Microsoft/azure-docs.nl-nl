---
title: Back-upgids voor SAP HANA op Azure Virtual Machines | Microsoft Docs
description: De back-upgids voor SAP HANA biedt twee belang rijke back-upmogelijkheden voor SAP HANA op virtuele machines van Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0004afb5895d7549e5db8ad5e53b52fa17991520
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667911"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Back-upgids voor SAP HANA op Azure Virtual Machines

## <a name="getting-started"></a>Aan de slag

In de back-upgids voor SAP HANA die op virtuele machines van Azure worden uitgevoerd, worden alleen specifieke onderwerpen van Azure beschreven. Raadpleeg de SAP HANA-documentatie voor algemene SAP HANA back-up van gerelateerde items. U verwacht dat u bekend bent met principe database back-upstrategieen, de redenen en motivaties een geluid en een geldige back-up te maken van de vereisten die uw bedrijf heeft voor de back-upprocedure, Bewaar periode van back-ups en herstel procedure.

SAP HANA wordt officieel ondersteund op verschillende typen virtuele Azure-machines, zoals Azure M-series. Voor een volledige lijst met SAP HANA gecertificeerde Azure-Vm's en HANA-eenheden voor grote instanties, raadpleegt u [gecertificeerde IaaS-platforms zoeken](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Microsoft Azure biedt een aantal eenheden waarin SAP HANA niet-gevirtualiseerd op fysieke servers wordt uitgevoerd. Deze service wordt [Hana grote instanties](hana-overview-architecture.md)genoemd. Deze hand leiding heeft geen betrekking op back-upprocesën en hulpprogram ma's voor HANA grote instanties. Maar is beperkt tot Azure virtual machines. Lees voor meer informatie over back-up-en herstel processen met HANA grote instanties het artikel [HLI back-up en herstellen](./hana-backup-restore.md).

De focus van dit artikel is op drie back-upmogelijkheden voor SAP HANA op virtuele machines van Azure:

- HANA-back-up via [Azure backup services](../../../backup/backup-overview.md) 
- HANA-back-up naar het bestands systeem in een virtuele machine van Azure Linux (Zie [SAP HANA Azure Backup op bestands niveau](sap-hana-backup-file-level.md))
- HANA-back-up op basis van moment opnamen van opslag met behulp van de Azure Storage BLOB snap shot-functie hand matig of Azure Backup Service


SAP HANA biedt een back-upapi waarmee back-uptools van derden rechtstreeks kunnen worden geïntegreerd met SAP HANA. Producten als Azure Backup-Service of [CommVault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) gebruiken deze eigen interface om SAP Hana-data base of logboek back-ups opnieuw te activeren. 


Informatie over hoe u kunt vinden welke SAP-software wordt ondersteund op Azure, vindt u in het artikel [wat SAP-software ondersteunt voor Azure-implementaties](./sap-supported-product-on-azure.md).

## <a name="azure-backup-service"></a>Azure Backup-Service

Het eerste scenario dat wordt weer gegeven, is een scenario waarbij Azure Backup service de SAP HANA `backint` -interface gebruikt voor het uitvoeren van een streaming-back-up met vanuit een SAP Hana-data base. Of u gebruikt een meer algemene mogelijkheid van Azure Backup service om een toepassings consistente schijf momentopname te maken en die ene naar de Azure Backup-service te laten overzetten.

Azure Backup integreert en is gecertificeerd als back-upoplossing voor SAP HANA met behulp van de eigen SAP HANA-interface met de naam [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5). Lees voor meer informatie over de oplossing, de mogelijkheden en de Azure-regio's waar deze beschikbaar is, het artikel [support matrix voor het maken van back-ups van SAP Hana-data bases op Azure-vm's](../../../backup/sap-hana-backup-support-matrix.md#scenario-support). Lees het artikel [over SAP Hana-database back-up in azure vm's](../../../backup/sap-hana-db-about.md)voor meer informatie en principes over Azure backup service voor Hana. 

De tweede mogelijkheid voor het gebruik van Azure Backup service is het maken van een toepassings consistente back-up met behulp van schijf momentopnamen van Azure Premium Storage. Andere HANA-gecertificeerde Azure-opslag, zoals [Azure Ultra Disk](../../disks-enable-ultra-ssd.md) en [Azure NetApp files](https://azure.microsoft.com/services/netapp/) , bieden geen ondersteuning voor dit soort moment opnamen via Azure backup-service. Lees deze artikelen:

- [De infrastructuur voor back-ups van virtuele Azure-machines plannen](../../../backup/backup-azure-vms-introduction.md)
- [Toepassingsconsistente back-up van Azure Linux-VM’s](../../../backup/backup-azure-linux-app-consistent.md) 

deze reeks activiteit komt voor:

- Azure Backup moet een script voorafgaand aan de moment opname uitvoeren waarmee de toepassing wordt geplaatst, in dit geval SAP HANA, in een consistente status
- Omdat deze consistente status wordt bevestigd, worden de moment opnamen van de schijf Azure Backup uitgevoerd
- Na het volt ooien van de moment opnamen, maakt Azure Backup de activiteit in het script dat voorafgaat aan moment opnamen ongedaan
- Wanneer de uitvoering is geslaagd, streamt Azure Backup de gegevens naar de back-upkluis

In het geval van SAP HANA gebruiken de meeste klanten Azure Write Accelerator voor de volumes die het SAP HANA logboek voor opnieuw uitvoeren bevatten. Azure Backup service worden deze volumes automatisch uitgesloten van de moment opnamen. Deze uitsluiting is niet van invloed op de mogelijkheid van HANA om te herstellen. Hoewel het niet mogelijk is om te herstellen met bijna alle andere met SAP ondersteunde DBMS.

Het nadeel van deze mogelijkheid is dat u uw eigen pre-en post-momentopname script moet ontwikkelen. Het script dat voorafgaat aan moment opnamen moet een HANA-moment opname maken en afdoende uitzonderings cases afhandelen. U moet de HANA-moment opname opnieuw verwijderen met het script dat volgt op de moment opname. Voor meer informatie over de vereiste logica, begint u met [SAP-ondersteunings opmerking #2039883](https://launchpad.support.sap.com/#/notes/2039883). De overwegingen van de sectie SAP HANA gegevens consistentie bij het maken van moment opnamen van opslag in dit artikel zijn volledig van toepassing op dit type back-up.

> [!NOTE]
> Back-ups op basis van schijf momentopnamen voor SAP HANA in implementaties waarbij meerdere database containers worden gebruikt, is een minimale versie van HANA 2,0 SP04
> 

Meer informatie over opslag momentopnamen vindt u verderop in dit document.

![In deze afbeelding ziet u twee mogelijkheden voor het opslaan van de huidige status van de virtuele machine](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Overige HANA-back-upmethoden
Er zijn drie andere back-upmethoden of-paden die kunnen worden overwogen:

- Back-ups maken op basis van een NFS-share die is gebaseerd op Azure NetApp Files (ANF). ANF is de mogelijkheid om moment opnamen te maken van de volumes waarop u back-ups opslaat. Gezien de door Voer die u uiteindelijk nodig hebt om de back-ups te schrijven, kan deze oplossing een dure methode worden. Hoewel de back-ups rechtstreeks naar de systeem eigen NFS-share kunnen worden geschreven, kan dit eenvoudig tot stand worden gebracht.
- De HANA-back-up uitvoeren op schijven die zijn gekoppeld aan de VM van Standard-SSD of Azure Premium Storage. Als volgende stap kunt u deze back-upbestanden kopiëren naar Azure Blob Storage. Deze strategie kan prijs aantrekkelijk zijn
- De HANA-back-up uitvoeren op schijven die zijn gekoppeld aan de VM van Standard-SSD of Azure Premium Storage. Als volgende stap wordt de schijf regel matig opgehaald. Na de eerste moment opname kunnen incrementele moment opnamen worden gebruikt om de kosten te verlagen

![In deze afbeelding ziet u de opties voor het maken van een back-up van een SAP HANA-bestand in de VM](media/sap-hana-backup-guide/other-hana-backup-paths.png)

In deze afbeelding ziet u opties voor het maken van een back-up van SAP HANA-bestanden in de virtuele machine en vervolgens het opslaan van de HANA-back-upbestanden op een andere manier. Alle oplossingen die geen back-upservice van derden of Azure Backup service hebben, hebben echter een aantal gemeen schappelijke obstakels. Sommige daarvan kunnen worden vermeld, zoals retentie beheer, automatisch herstel proces en het leveren van automatische herstel naar een bepaald tijdstip als Azure Backup Service of andere gespecialiseerde back-ups van derden en services. Veel van deze services van derden kunnen worden uitgevoerd op Azure. 


## <a name="sap-resources-for-hana-backup"></a>SAP-bronnen voor HANA-back-ups

### <a name="sap-hana-backup-documentation"></a>SAP HANA back-updocumentatie

- [Inleiding tot SAP HANA beheer](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [De strategie voor back-up en herstel plannen](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [HANA-back-ups plannen met ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Gegevens back-ups plannen (SAP HANA cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Veelgestelde vragen over SAP HANA back-up in [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Veelgestelde vragen over SAP HANA data base-en opslag momentopnamen in [SAP-notitie 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Niet-geschikte netwerk bestands systemen voor back-up en herstel in [SAP-notitie 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>De juistheid van SAP HANA back-up controleren
Onafhankelijk van uw back-upmethode is het uitvoeren van een test herstel op basis van een ander systeem een absolute nood zaak. Deze benadering biedt een manier om ervoor te zorgen dat een back-up correct is en dat interne processen voor back-up en herstel naar verwachting werken. Hoewel het herstellen van back-ups een obstakel op locatie kan zijn vanwege de vereisten van de infra structuur, is het veel eenvoudiger om in de cloud te zorgen door tijdelijk vereiste bronnen te bieden voor dit doel einde. Het is juist dat er hulp middelen zijn die zijn meegeleverd met HANA, waarmee back-upbestanden kunnen worden gecontroleerd op de mogelijkheid om te herstellen. Het doel van frequente herstel oefeningen is echter om het proces van het herstellen van een Data Base te testen en dit proces te trainen met de operations-mede werkers.

Houd er wel zeker van dat u een eenvoudige herstel bewerking uitvoert en controleert of HANA actief is en niet voldoende is. U moet een tabel consistentie controle uitvoeren om ervoor te zorgen dat de herstelde data base prima is. SAP HANA biedt verschillende soorten consistentie controles die worden beschreven in [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informatie over de consistentie controle van de tabel vindt u ook op de SAP-website op [consistentie controles van tabellen en catalogi](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Voor-en nadelen van HANA-back-ups versus opslag momentopname

SAP heeft&#39;t de voor keur geven aan HANA-back-ups versus een moment opname van de opslag. De lijst bevat de voor-en nadelen, waardoor er een kan worden bepaald welke gegevens moeten worden gebruikt, afhankelijk van de situatie en de beschik bare opslag technologie (Zie [uw strategie voor back-up en herstel plannen](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/ef085cd5949c40b788bba8fd3c65743e.html)).

Zorg er in azure voor dat de functie voor moment opnamen van Azure Blob&#39;t bestandssysteem consistentie biedt op meerdere schijven (Zie [voor het gebruik van BLOB-moment opnamen met Power shell](/archive/blogs/cie/using-blob-snapshots-with-powershell)). 

Daarnaast moet één inzicht hebben in de facturerings implicaties bij het werken met Blob-moment opnamen, zoals beschreven in dit artikel: inzicht in de [manier waarop moment opnamen toenemen](/rest/api/storageservices/understanding-how-snapshots-accrue-charges), het hebben&#39;t als duidelijk te maken met behulp van virtuele Azure-schijven.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA consistentie van gegevens bij het maken van moment opnamen van opslag

Zoals eerder is beschreven, is het beschrijven van de back-upfuncties voor moment opnamen van Azure Backup, de consistentie van bestands systemen en toepassingen verplicht bij het maken van moment opnamen van opslag. De eenvoudigste manier om problemen te voor komen, is om SAP HANA af te sluiten of misschien zelfs de hele virtuele machine. Iets wat niet haalbaar is voor een productie-exemplaar.

> [!NOTE]
> Back-ups op basis van schijf momentopnamen voor SAP HANA in implementaties waarbij meerdere database containers worden gebruikt, is een minimale versie van HANA 2,0 SP04
> 

Azure Storage biedt geen consistentie van bestands systemen op meerdere schijven of volumes die zijn gekoppeld aan een virtuele machine tijdens het momentopname proces. Dit betekent dat de toepassings consistentie tijdens de moment opname moet worden geleverd door de toepassing, in dit geval SAP HANA zichzelf. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) heeft belang rijke informatie over het SAP Hana van back-ups door opslag momentopnamen. Met XFS-bestands systemen is het bijvoorbeeld nodig **xfs- \_ blok kering** uit te voeren voordat u een moment opname van de opslag start om toepassings consistentie te bieden (Zie [xfs \_ bevriezen (8)-Linux-man pagina](https://linux.die.net/man/8/xfs_freeze) voor meer informatie over **xfs \_ blok keren**).

Ervan uitgaande dat er een XFS-bestands systeem is met vier virtuele schijven van Azure, bieden de volgende stappen een consistente moment opname die het HANA-gegevens gebied vertegenwoordigt:

1. Voor bereide HANA-gegevens momentopname maken
1. De bestands systemen van alle schijven/volumes blok keren (gebruik bijvoorbeeld **xfs \_ stilzetten**)
1. Alle benodigde BLOB-moment opnamen maken op Azure
1. De blok kering van het bestands systeem opheffen
1. De HANA-gegevens momentopname bevestigen (de moment opname wordt verwijderd)

Wanneer u de functionaliteit van de Azure Backup gebruikt voor het uitvoeren van toepassings consistente moment opnamen van back-ups, moeten de stappen #1 door u worden gecodeerd of door u in een script worden vastgelegd voor het script dat voorafgaat aan de moment opname. Azure Backup-service worden stappen uitgevoerd #2 en #3. De stappen #4 en #5 moeten worden weer gegeven door de code in het script dat volgt op de moment opname. Als u geen Azure backup-service gebruikt, moet u ook code/script-stap #2 en zelf #3.
Meer informatie over het maken van HANA-gegevens momentopnamen vindt u in de volgende artikelen:

- [HANA-gegevens momentopnamen] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Meer informatie over het uitvoeren van stap #1 vindt u in [het artikel een moment opname van gegevens maken (systeem eigen SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Details over het bevestigen/verwijderen van HANA-gegevens momentopnamen, zoals in stap #5, vindt u in het artikel [een moment opname van gegevens maken (systeem eigen SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 

Het is belang rijk om de HANA-moment opname te bevestigen. Als gevolg van het &quot; kopiëren naar schrijven, &quot; is voor SAP Hana mogelijk geen extra schijf ruimte nodig tijdens deze modus voor het voorbereiden van de moment opname. Het kan ook zijn dat er geen nieuwe back-ups&#39;worden gestart totdat de moment opname van SAP HANA is bevestigd.


### <a name="sap-hana-backup-scheduling-strategy"></a>Strategie voor het plannen van back-ups SAP HANA

In het artikel SAP HANA de [strategie voor back-up en herstel te plannen,](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) wordt een basis plan voor het uitvoeren van back-ups. Vertrouw op SAP-documentatie rond HANA en uw ervaringen met andere DBMS in het definiëren van de strategie voor back-up/herstel en het proces voor het maken van SAP HANA. De volg orde van de verschillende typen back-ups en de retentie periode zijn zeer afhankelijk van de Sla's die u moet opgeven.


### <a name="sap-hana-backup-encryption"></a>Back-upversleuteling SAP HANA

SAP HANA biedt versleuteling van gegevens en Logboeken. Als SAP HANA gegevens en logboek niet zijn versleuteld, worden de back-ups standaard niet versleuteld. SAP HANA biedt echter een afzonderlijke back-upcodering zoals beschreven in [SAP Hana back-up-versleuteling](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html). Als u oudere versies van SAP HANA gebruikt, moet u mogelijk controleren of de back-up-versleuteling onderdeel was van de functionaliteit die al is geleverd.  


## <a name="next-steps"></a>Volgende stappen
* [SAP HANA Azure Backup op bestands niveau](sap-hana-backup-file-level.md) wordt de back-upoptie op basis van bestanden beschreven.
* Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood gevallen op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het tot stand brengen van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure (grote exemplaren).
