---
title: Aanbevolen procedures voor de virtuele StorSimple-matrix | Microsoft Docs
description: Hierin worden de aanbevolen procedures beschreven voor het implementeren en beheren van de virtuele StorSimple-matrix.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 71b018da6b54ebf2b45a261378ea521a397159e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94964980"
---
# <a name="storsimple-virtual-array-best-practices"></a>Best practices virtuele array van StorSimple

## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Microsoft Azure StorSimple Virtual array is een geïntegreerde opslag oplossing voor het beheer van opslag taken tussen een on-premises virtueel apparaat dat wordt uitgevoerd in een Hyper Visor en Microsoft Azure Cloud opslag. StorSimple Virtual array is een efficiënt, rendabel alternatief voor de fysieke matrix van de 8000-serie. De virtuele matrix kan worden uitgevoerd op uw bestaande Hyper Visor infrastructuur, ondersteunt zowel de iSCSI-als de SMB-protocollen en is geschikt voor externe Office-en filiaal scenario's. Ga naar [Microsoft Azure StorSimple-overzicht](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx)voor meer informatie over de StorSimple-oplossingen.

In dit artikel worden de aanbevolen procedures besproken die zijn geïmplementeerd tijdens de eerste installatie, implementatie en het beheer van de virtuele StorSimple-matrix. Deze aanbevolen procedures bieden gevalideerde richt lijnen voor het instellen en beheren van uw virtuele matrix. Dit artikel is gericht op de IT-beheerders die de virtuele matrices in hun data centers implementeren en beheren.

We raden u aan een periodieke beoordeling van de best practices uit te voeren om ervoor te zorgen dat uw apparaat nog steeds compatibel is wanneer er wijzigingen worden aangebracht in de installatie-of bewerkings stroom. Als u problemen ondervindt bij het implementeren van deze aanbevolen procedures voor uw virtuele array, [neemt u contact op met Microsoft ondersteuning](storsimple-virtual-array-log-support-ticket.md) voor hulp.

## <a name="configuration-best-practices"></a>Aanbevolen procedures voor configuratie
Deze aanbevolen procedures gelden voor de richt lijnen die moeten worden gevolgd tijdens de eerste installatie en implementatie van de virtuele matrices. Dit zijn de aanbevolen procedures voor het inrichten van de virtuele machine, groeps beleids instellingen, het instellen van het netwerk, het configureren van opslag accounts en het maken van shares en volumes voor de virtuele matrix. 

### <a name="provisioning"></a>Inrichten
StorSimple Virtual array is een virtuele machine (VM) die is ingericht op de Hyper Visor (Hyper-V of VMware) van uw hostserver. Wanneer u de virtuele machine inricht, moet u ervoor zorgen dat uw host voldoende bronnen kan toewijzen. Voor meer informatie gaat u naar [minimale resource vereisten](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) om een matrix in te richten.

Implementeer de volgende aanbevolen procedures bij het inrichten van de virtuele matrix:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Type virtuele machine** |**Generatie 2** VM voor gebruik met Windows Server 2012 of hoger en een *. vhdx* -installatie kopie. <br></br> **Generatie 1** VM voor gebruik met een Windows Server 2008 of hoger en een *VHD* -installatie kopie. |Gebruik de versie 8 van de virtuele machine wanneer u een *VMDK* -afbeelding gebruikt. |
| **Type geheugen** |Configureer als **statisch geheugen**. <br></br> Gebruik niet de optie **dynamisch geheugen** . | |
| **Type gegevens schijf** |Inrichten als **dynamisch uitbreiden**.<br></br> De **vaste grootte** neemt veel tijd in beslag. <br></br> Gebruik niet de optie **differentiëring** . |Gebruik de optie voor **Thin Provisioning** . |
| **Wijziging van de gegevens schijf** |Uitbrei ding of krimpen is niet toegestaan. Als u dit toch doet, worden alle lokale gegevens op het apparaat verloren. |Uitbrei ding of krimpen is niet toegestaan. Als u dit toch doet, worden alle lokale gegevens op het apparaat verloren. |

### <a name="sizing"></a>Grootte aanpassen
Houd bij het aanpassen van de grootte van de virtuele StorSimple-matrix rekening met de volgende factoren:

* Lokale reserve ring voor volumes of shares. Ongeveer 12% van de ruimte wordt gereserveerd op de lokale laag voor elk ingericht volume of een gelaagde share. Ongeveer 10% van de ruimte wordt ook gereserveerd voor een lokaal vastgemaakt volume voor het bestands systeem.
* Overhead van moment opnamen. Ongeveer 15% ruimte op de lokale laag is gereserveerd voor moment opnamen.
* Moet worden hersteld. Als u herstellen als een nieuwe bewerking uitvoert, moet u de grootte van de ruimte die nodig is voor herstel wijzigen. De herstel bewerking wordt uitgevoerd op een share of een volume met dezelfde grootte.
* Voor elke onverwachte groei moet een buffer worden toegewezen.

Op basis van de voor gaande factoren kunnen de vereisten voor de grootte worden weer gegeven met de volgende vergelijking:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

In de volgende voor beelden ziet u hoe u het formaat van een virtuele matrix kunt aanpassen op basis van uw vereisten.

#### <a name="example-1"></a>Voorbeeld 1:
U kunt in uw virtuele matrix het beste

* richt een gelaagd volume of een share van 2 TB in.
* richt een volume of share van 1 TB in de laag in.
* richt een lokaal vastgemaakt volume of share in van 300 GB.

Voor de voor gaande volumes of shares, laten we de ruimte vereisten op de lokale laag berekenen.

Voor elke gelaagde volume/share is lokale reserve ring gelijk aan 12% van de grootte van het volume/de share. Voor de lokaal vastgemaakte volume/share is de lokale reserve ring 10% van de lokaal vastgemaakte volume/share grootte (naast de ingerichte grootte). In dit voor beeld hebt u het volgende nodig:

* 240-GB lokale reserve ring (voor een gelaagd volume/share van 2 TB)
* 120-GB lokale reserve ring (voor een gelaagd volume/share van 1 TB)
* 330-GB voor lokaal vastgemaakt volume of share (10% van lokale reserve ring toevoegen aan de ingerichte grootte van 300 GB)

De totale ruimte die is vereist voor de lokale laag is tot nu toe: 240 GB + 120 GB + 330 GB = 690 GB.

Ten tweede hebben we ten minste zoveel ruimte op de lokale laag als grootste enkele reserve ring nodig. Dit extra bedrag wordt gebruikt wanneer u een Cloud momentopname wilt herstellen. In dit voor beeld is de grootste lokale reserve ring 330 GB (inclusief reserve ring voor bestands systeem), zodat u deze toevoegt aan de 690 GB: 690 GB + 330 GB = 1020 GB.
Als we verdere herstel bewerkingen hebben uitgevoerd, kunnen we de ruimte van de vorige terugzet bewerking altijd vrijmaken.

Ten derde hebben we 15% van uw totale lokale ruimte tot nu toe nodig voor het opslaan van lokale moment opnamen, zodat er slechts 85% van de schijf beschikbaar is. In dit voor beeld zou dat ongeveer 1020 GB = 0,85 &ast; ingerichte gegevens schijf TB zijn. Daarom zou de ingerichte gegevens schijf (1020 &ast; (1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (afronden op het dichtstbijzijnde kwartiel)

Voor de factor in onverwachte groei en nieuwe herstel bewerkingen moet u een lokale schijf van circa 1,25-1,5 TB inrichten.

> [!NOTE]
> We raden u ook aan om de lokale schijf Thin provisioned. Deze aanbeveling is omdat de herstel ruimte alleen nodig is wanneer u gegevens wilt herstellen die ouder zijn dan vijf dagen. Met herstel op item niveau kunt u gegevens voor de afgelopen vijf dagen herstellen zonder dat de extra ruimte nodig is om te herstellen.


#### <a name="example-2"></a>Voorbeeld 2:
U kunt in uw virtuele matrix het beste

* een gelaagd volume van 2 TB inrichten
* een lokaal vastgemaakt volume van 300 GB inrichten

Op basis van 12% van lokale ruimte reservering voor gelaagde volumes/shares en 10% voor lokaal vastgemaakte volumes/shares, moeten we

* 240-GB lokale reserve ring (voor 2 TB gelaagd volume/share)
* 330-GB voor lokaal vastgemaakt volume of share (10% van lokale reserve ring toevoegen aan de ingerichte ruimte van 300 GB)

De totale benodigde ruimte voor de lokale laag is: 240 GB + 330 GB = 570 GB

De minimale lokale schijf ruimte die nodig is voor herstel is 330 GB.

15% van de totale schijf wordt gebruikt om moment opnamen op te slaan, zodat er slechts 0,85 beschikbaar is. De schijf grootte is dus (900 &ast; (1/0.85)) = 1,06 TB ~ 1,25 TB (afronden op het dichtstbijzijnde kwartiel)

Factor in een onverwachte groei kunt u een lokale schijf van 1,25-1,5 TB inrichten.

### <a name="group-policy"></a>Groepsbeleid
Groepsbeleid is een infra structuur waarmee u specifieke configuraties kunt implementeren voor gebruikers en computers. Groepsbeleid-instellingen zijn opgenomen in groepsbeleid objecten (Gpo's) die zijn gekoppeld aan de volgende Active Directory Domain Services (AD DS) containers: sites, domeinen of organisatie-eenheden (Ou's). 

Als uw virtuele matrix lid is van een domein, kunnen groeps beleidsobjecten hierop worden toegepast. Met deze groeps beleidsobjecten kunnen toepassingen worden geïnstalleerd, zoals een antivirus software die de werking van de virtuele StorSimple-matrix kan beïnvloeden.

Daarom wordt u aangeraden:

* Zorg ervoor dat uw virtuele matrix zich in een eigen organisatie-eenheid (OE) bevindt voor Active Directory.
* Zorg ervoor dat er geen groeps beleidsobjecten (Gpo's) worden toegepast op uw virtuele matrix. U kunt overname blok keren om ervoor te zorgen dat de virtuele matrix (onderliggend knoop punt) geen Gpo's automatisch overneemt van het bovenliggende item. Ga voor meer informatie naar de [overname blok keren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11)).

### <a name="networking"></a>Netwerken
De netwerk configuratie voor uw virtuele matrix wordt uitgevoerd via de lokale webgebruikersinterface. Een virtuele netwerk interface wordt ingeschakeld via de Hyper Visor waarin de virtuele matrix is ingericht. Gebruik de pagina [netwerk instellingen](storsimple-virtual-array-deploy3-fs-setup.md) om het IP-adres, subnet en de gateway van de virtuele netwerk interface te configureren.  U kunt ook de primaire en secundaire DNS-server, tijd instellingen en optionele proxy-instellingen voor uw apparaat configureren. De meeste netwerk configuratie is een eenmalige installatie. Bekijk de [StorSimple-netwerk vereisten](storsimple-ova-system-requirements.md#networking-requirements) voordat u de virtuele matrix implementeert.

Wanneer u uw virtuele matrix implementeert, wordt u aangeraden deze aanbevolen procedures te volgen:

* Zorg ervoor dat het netwerk waarin de virtuele matrix wordt geïmplementeerd, altijd de capaciteit heeft voor het toewijzen van 5 Mbps internet bandbreedte (of meer).
  
  * De Internet bandbreedte moet variëren, afhankelijk van de kenmerken van de werk belasting en de frequentie waarmee gegevens worden gewijzigd.
  * De gegevens wijziging die kan worden verwerkt, is rechtstreeks evenredig met uw Internet bandbreedte. Een voor beeld van het maken van een back-up is een 5 Mbps-band breedte die een gegevens wijziging van circa 18 GB in acht uur kan bevatten. Met vier maal meer band breedte (20 Mbps) kunt u vier keer zoveel gegevens wijziging afhandelen (72 GB).
* Zorg ervoor dat de verbinding met Internet altijd beschikbaar is. Sporadische of onbetrouwbare Internet verbindingen met de apparaten kunnen leiden tot verlies van toegang tot gegevens in de Cloud. Dit kan leiden tot een niet-ondersteunde configuratie.
* Als u van plan bent om uw apparaat te implementeren als een iSCSI-server:
  
  * We raden u aan de optie **automatisch IP-adres ophalen** (DHCP) uit te scha kelen.
  * Statische IP-adressen configureren. U moet een primaire en een secundaire DNS-server configureren.
  * Als u meerdere netwerk interfaces in uw virtuele matrix definieert, kan alleen de eerste netwerk interface (standaard deze interface is **Ethernet**) de Cloud bereiken. Als u het type verkeer wilt beheren, kunt u meerdere virtuele netwerk interfaces maken op uw virtuele array (geconfigureerd als een iSCSI-server) en die interfaces verbinden met verschillende subnetten.
* Als u de Cloud bandbreedte alleen wilt beperken (gebruikt door de virtuele matrix), configureert u beperking op de router of de firewall. Als u een beperking in uw Hyper Visor definieert, worden alle protocollen met inbegrip van iSCSI en SMB beperkt in plaats van alleen de Cloud bandbreedte.
* Zorg ervoor dat tijd synchronisatie voor Hyper Visor is ingeschakeld. Als u Hyper-V gebruikt, selecteert u uw virtuele matrix in de Hyper-V-beheer, gaat u naar **instellingen &gt; integratie Services** en zorgt u ervoor dat de **tijd synchronisatie** wordt gecontroleerd.

### <a name="storage-accounts"></a>Opslagaccounts
De virtuele StorSimple-matrix kan worden gekoppeld aan één opslag account. Dit opslag account kan een automatisch gegenereerd opslag account zijn, een account in hetzelfde abonnement als de service of een opslag account dat is gerelateerd aan een ander abonnement. Zie [opslag accounts beheren voor uw virtuele matrix](storsimple-virtual-array-manage-storage-accounts.md)voor meer informatie.

Gebruik de volgende aanbevelingen voor opslag accounts die zijn gekoppeld aan uw virtuele matrix.

* Wanneer u meerdere virtuele matrices met één opslag account koppelt, wordt de factor in de maximum capaciteit (64 TB) voor een virtuele matrix en de maximale grootte (500 TB) voor een opslag account. Hiermee wordt het aantal virtuele matrices met volledige grootte die aan dat opslag account kunnen worden gekoppeld, beperkt tot ongeveer 7.
* Bij het maken van een nieuw opslag account
  
  * We raden u aan deze te maken in de regio die het dichtst bij het externe kantoor/filiaal waar uw virtuele StorSimple-matrix wordt geïmplementeerd om latentie te minimaliseren.
  * Houd er rekening mee dat u een opslag account niet over verschillende regio's kunt verplaatsen. U kunt ook geen service verplaatsen tussen abonnementen.
  * Gebruik een opslag account dat redundantie tussen de data centers implementeert. Geo-Redundant Storage (GRS), zone redundante opslag (ZRS) en lokaal redundante opslag (LRS) worden ondersteund voor gebruik met uw virtuele matrix. Ga naar [Azure storage-replicatie](../storage/common/storage-redundancy.md)voor meer informatie over de verschillende soorten opslag accounts.

### <a name="shares-and-volumes"></a>Shares en volumes
U kunt voor uw virtuele StorSimple-matrix shares inrichten wanneer deze is geconfigureerd als een bestands server en volumes wanneer deze zijn geconfigureerd als een iSCSI-server. De aanbevolen procedures voor het maken van shares en volumes zijn gerelateerd aan de grootte en het type dat is geconfigureerd.

#### <a name="volumeshare-size"></a>Grootte van volume/share
U kunt in uw virtuele matrix shares inrichten wanneer deze is geconfigureerd als een bestands server en volumes wanneer deze zijn geconfigureerd als een iSCSI-server. De aanbevolen procedures voor het maken van shares en volumes zijn gerelateerd aan de grootte en het type dat is geconfigureerd. 

Let op de volgende aanbevolen procedures bij het inrichten van shares of volumes op het virtuele apparaat.

* De bestands grootte ten opzichte van de ingerichte grootte van een gelaagde share kan invloed hebben op de prestaties van de lagen. Het werken met grote bestanden kan resulteren in een langzame laag. Wanneer u werkt met grote bestanden, raden we aan dat het grootste bestand kleiner is dan 3% van de share grootte.
* Er kunnen Maxi maal 16 volumes/shares worden gemaakt op de virtuele matrix. Voor de maximale grootte van de lokaal vastgemaakte en gelaagde volumes/shares raadpleegt u altijd de [limieten voor de virtuele StorSimple-matrix](storsimple-ova-limits.md).
* Bij het maken van een volume moet u rekening houden met het verwachte gegevens verbruik en de toekomstige groei. Het volume of de share kan later niet worden uitgebreid.
* Zodra het volume of de share is gemaakt, kunt u de grootte van het volume of de share op StorSimple niet verkleinen.
* Wanneer u naar een gelaagd volume op StorSimple schrijft en de volume gegevens een bepaalde drempel waarde bereiken (ten opzichte van de lokale ruimte die voor het volume is gereserveerd), wordt de i/o beperkt. Als u doorgaat met schrijven naar dit volume, wordt de IO aanzienlijk vertraagd. Hoewel u kunt schrijven naar een gelaagd volume dat groter is dan de ingerichte capaciteit (de gebruiker niet actief stopt met het schrijven van de ingerichte capaciteit), ziet u een waarschuwings melding over het effect dat u hebt geabonneerd. Zodra u de waarschuwing ziet, is het van cruciaal belang dat u de herstel maatregelen uitvoert, zoals het verwijderen van de volume gegevens (volume uitbreiding wordt momenteel niet ondersteund).
* Voor use cases voor herstel na nood gevallen, omdat het aantal toegestane shares/volumes 16 is en het maximum aantal shares/volumes dat parallel kan worden verwerkt, ook 16 is, is het aantal shares/volumes geen invloed op uw RPO en Rto's.

#### <a name="volumeshare-type"></a>Type volume/share
StorSimple ondersteunt twee typen volume/share op basis van het gebruik: lokaal vastgemaakt en gelaagd. Lokaal vastgemaakte volumes/shares zijn dik ingericht, terwijl gelaagde volumes/shares Thin-provisioned zijn. U kunt een lokaal vastgemaakt volume/gedeelde map niet converteren naar gelaagd of *vice versa* .

We raden u aan de volgende aanbevolen procedures te implementeren bij het configureren van StorSimple-volumes/-shares:

* Bepaal het volume type op basis van de werk belastingen die u wilt implementeren voordat u een volume maakt. Lokaal vastgemaakte volumes gebruiken voor werk belastingen die lokale garanties van gegevens vereisen (zelfs tijdens een storing in de Cloud) en waarvoor lage Cloud latenties zijn vereist. Zodra u een volume op uw virtuele matrix hebt gemaakt, kunt u het volume type niet meer wijzigen van lokaal vastgemaakt naar gelaagd of *vice versa*. Maak een voor beeld van lokaal vastgemaakte volumes bij het implementeren van SQL-workloads of workloads die als host fungeren voor virtuele machines (Vm's). gebruik gelaagde volumes voor workloads voor bestands shares.


#### <a name="volume-format"></a>Volume-indeling
Nadat u StorSimple-volumes op uw iSCSI-server hebt gemaakt, moet u de volumes initialiseren, koppelen en Format teren. Deze bewerking wordt uitgevoerd op de host die is verbonden met uw StorSimple-apparaat. De volgende aanbevolen procedures worden aanbevolen bij het koppelen en Format teren van volumes op de StorSimple-host.

* Voer een snelle indeling uit op alle StorSimple-volumes.
* Gebruik bij het format teren van een StorSimple-volume een Allocation Unit Size (AUS) van 64 KB (de standaard waarde is 4 KB). De 64 KB AUS is gebaseerd op het uitvoeren van tests die intern worden uitgevoerd voor veelvoorkomende StorSimple-workloads en andere workloads.
* Wanneer u de virtuele StorSimple-matrix gebruikt die is geconfigureerd als een iSCSI-server, mag u geen spanned volumes of dynamische schijven gebruiken omdat deze volumes of schijven niet worden ondersteund door StorSimple.

#### <a name="share-access"></a>Toegang delen
Volg de volgende richt lijnen bij het maken van shares op de bestands server van de virtuele matrix:

* Wanneer u een share maakt, moet u een gebruikers groep toewijzen als share beheerder in plaats van één gebruiker.
* U kunt de NTFS-machtigingen beheren nadat de share is gemaakt door de shares te bewerken via Windows Verkenner.

#### <a name="volume-access"></a>Volume toegang
Bij het configureren van de iSCSI-volumes op uw virtuele StorSimple-matrix, is het belang rijk om de toegang te beheren waar nodig. Om te bepalen welke hostservers toegang hebben tot volumes, maakt en koppelt u Access Control records (ACRs) met StorSimple-volumes.

Gebruik de volgende aanbevolen procedures bij het configureren van ACRs voor StorSimple-volumes:

* Koppel altijd ten minste één ACR aan een volume.

* Wanneer u meer dan een ACR aan een volume toewijst, moet u ervoor zorgen dat het volume niet wordt weer gegeven op een manier waar het gelijktijdig kan worden geopend door meer dan één niet-geclusterde host. Als u meerdere ACRs hebt toegewezen aan een volume, wordt er een waarschuwings bericht weer gegeven waarin u uw configuratie kunt controleren.

### <a name="data-security-and-encryption"></a>Gegevensbeveiliging en -versleuteling
Uw virtuele StorSimple-matrix bevat functies voor gegevens beveiliging en-versleuteling die ervoor zorgen dat de vertrouwelijkheid en integriteit van uw gegevens worden gegarandeerd. Wanneer u deze functies gebruikt, is het raadzaam deze aanbevolen procedures te volgen: 

* Definieer een versleutelings sleutel voor de Cloud opslag om AES-256-versleuteling te genereren voordat de gegevens vanuit uw virtuele matrix naar de cloud worden verzonden. Deze sleutel is niet vereist als uw gegevens zijn versleuteld om te beginnen met. U kunt de sleutel genereren en veilig houden met behulp van een sleutel beheersysteem, zoals [Azure Key kluis](../key-vault/general/overview.md).
* Wanneer u het opslag account via de StorSimple Manager-service configureert, moet u ervoor zorgen dat u de TLS-modus inschakelt om een beveiligd kanaal te maken voor netwerk communicatie tussen uw StorSimple-apparaat en de Cloud.
* Genereer regel matig de sleutels voor uw opslag accounts (door de Azure Storage-service opnieuw te openen) om eventuele wijzigingen in toegang te krijgen op basis van de gewijzigde lijst met beheerders.
* Gegevens in uw virtuele matrix worden gecomprimeerd en ontdubbeld voordat deze naar Azure worden verzonden. Het wordt niet aangeraden de functie Service Gegevensontdubbeling te gebruiken op uw Windows Server-host.

## <a name="operational-best-practices"></a>Aanbevolen operationele procedures
De aanbevolen procedures voor het werk zijn richt lijnen die moeten worden gevolgd tijdens het dagelijks beheer of de werking van de virtuele matrix. Deze procedures beslaan specifieke beheer taken, zoals het maken van back-ups, het herstellen van een back-upset, het uitvoeren van een failover, het deactiveren en verwijderen van de matrix, het bewaken van het systeem gebruik en de status en het uitvoeren van virus scans op uw virtuele matrix.

### <a name="backups"></a>Back-ups
De gegevens in uw virtuele matrix worden op twee manieren in de cloud opgeslagen: een standaard geautomatiseerde dagelijkse back-up van het hele apparaat vanaf 22:30 of via een hand matige back-up op aanvraag. Standaard maakt het apparaat automatisch dagelijkse Cloud momentopnamen van alle gegevens die zich hierop bevinden. Ga voor meer informatie naar [een back-up van de virtuele StorSimple-matrix maken](storsimple-virtual-array-backup.md).

De frequentie en de retentie die zijn gekoppeld aan de standaard back-ups kunnen niet worden gewijzigd, maar u kunt de tijd configureren waarop de dagelijkse back-ups elke dag worden geïnitieerd. Bij het configureren van de begin tijd voor de automatische back-ups wordt aangeraden:

* Plan uw back-ups voor daluren. De begin tijd van de back-up mag niet samen vallen met een groot aantal IO-bewerkingen.
* Start een hand matige back-up op aanvraag wanneer u een failover van een apparaat of voorafgaand aan het onderhouds venster wilt uitvoeren om de gegevens op uw virtuele matrix te beveiligen.

### <a name="restore"></a>Herstellen
U kunt op twee manieren een back-upset herstellen: herstel op een ander volume of deel of voer een herstel op item niveau uit (alleen beschikbaar voor een virtuele matrix die is geconfigureerd als een bestands server). Met herstel op item niveau kunt u een gedetailleerd herstel van bestanden en mappen uitvoeren vanuit een Cloud back-up van alle shares op het StorSimple-apparaat. Ga voor meer informatie naar [herstellen vanuit een back-up](storsimple-virtual-array-clone.md).

Houd bij het uitvoeren van een herstel bewerking de volgende richt lijnen in acht:

* De virtuele StorSimple-matrix biedt geen ondersteuning voor in-place herstel bewerkingen. Dit kan echter worden gerealiseerd door een proces met twee stappen: Maak ruimte vrij op de virtuele matrix en herstel vervolgens naar een ander volume of een andere share.
* Houd er rekening mee dat bij het herstellen van een lokaal volume een langlopende bewerking wordt uitgevoerd. Hoewel het volume snel online kan worden gebracht, worden de gegevens op de achtergrond blijvend gehydrateerd.
* Het volume type blijft hetzelfde tijdens het herstel proces. Een gelaagd volume wordt hersteld naar een ander gelaagd volume en een lokaal vastgemaakt volume op een lokaal vastgemaakt volume.
* Wanneer u probeert een volume of een share te herstellen vanuit een back-upset, kan het zijn dat er nog steeds een doel volume of-share in de portal is gemaakt als de herstel taak mislukt. Het is belang rijk dat u het ongebruikte doel volume of de share in de portal verwijdert om toekomstige problemen die voortvloeien uit dit element te minimaliseren.

### <a name="failover-and-disaster-recovery"></a>Failover en herstel na nood gevallen
Met een failover van een apparaat kunt u uw gegevens van een *bron* apparaat in het Data Center migreren naar een ander *doel* apparaat dat zich op dezelfde of een andere geografische locatie bevindt. De failover van het apparaat is voor het hele apparaat. Tijdens de failover wijzigt de Cloud gegevens voor het bron apparaat het eigendom van het doel apparaat.

Voor uw virtuele StorSimple-matrix kunt u alleen een failover uitvoeren naar een andere virtuele matrix die wordt beheerd door dezelfde StorSimple Manager-service. Een failover naar een 8000 serie-apparaat of een matrix die wordt beheerd door een andere StorSimple Manager-service (dan die van het bron apparaat) is niet toegestaan. Ga naar [vereisten voor de failover van het apparaat](storsimple-virtual-array-failover-dr.md)voor meer informatie over de stappen voor de failover.

Houd bij het uitvoeren van een failover voor uw virtuele matrix het volgende in acht:

* Voor een geplande failover is het een aanbevolen best practice om alle volumes/shares offline te halen voordat de failover wordt gestart. Volg de specifieke instructies voor het besturings systeem om de volumes/shares eerst op de host offline te halen en deze vervolgens offline te zetten op het virtuele apparaat.
* Voor een herstel na nood geval op een bestands server (DR) wordt u aangeraden het doel apparaat als bron toe te voegen aan hetzelfde domein, zodat de share machtigingen automatisch worden omgezet. In deze release wordt alleen de failover naar een doel apparaat in hetzelfde domein ondersteund.
* Zodra de DR is voltooid, wordt het bron apparaat automatisch verwijderd. Hoewel het apparaat niet meer beschikbaar is, neemt de virtuele machine die u op het hostsysteem hebt ingericht nog steeds resources in beslag. We raden u aan deze virtuele machine te verwijderen van uw hostsysteem om te voor komen dat er kosten in rekening worden gebracht.
* Houd er rekening mee dat zelfs als de failover is mislukt, **de gegevens altijd veilig zijn in de Cloud**. Houd rekening met de volgende drie scenario's waarin de failover niet is voltooid:
  
  * Er is een fout opgetreden in de eerste fasen van de failover, zoals wanneer de pre-controles van DR worden uitgevoerd. In deze situatie is uw doel apparaat nog steeds bruikbaar. U kunt de failover opnieuw uitvoeren op hetzelfde doel apparaat.
  * Er is een fout opgetreden tijdens het feitelijke failoverproces. In dit geval is het doel apparaat gemarkeerd als onbruikbaar. U moet een andere virtuele doel matrix inrichten en configureren en gebruiken voor failover.
  * De failover is voltooid toen het bron apparaat werd verwijderd, maar het doel apparaat heeft problemen en u hebt geen toegang tot gegevens. De gegevens zijn nog steeds veilig in de Cloud en kunnen eenvoudig worden opgehaald door een andere virtuele matrix te maken en deze vervolgens als doel apparaat te gebruiken voor de DR.

### <a name="deactivate"></a>Deactivate
Wanneer u een virtuele StorSimple-matrix deactiveert, verbreekt u de verbinding tussen het apparaat en de bijbehorende StorSimple Manager service. Deactivering is een **permanente** bewerking en kan niet ongedaan worden gemaakt. Een gedeactiveerd apparaat kan niet opnieuw worden geregistreerd bij de StorSimple Manager-service. Ga voor meer informatie naar [deactiveren en verwijder de virtuele StorSimple-matrix](storsimple-virtual-array-deactivate-and-delete-device.md).

Houd bij het deactiveren van uw virtuele array de volgende aanbevolen procedures in acht:

* Maak een Cloud momentopname van alle gegevens voordat u een virtueel apparaat deactiveert. Wanneer u een virtuele matrix deactiveert, gaan alle gegevens van het lokale apparaat verloren. Door een Cloud momentopname te maken, kunt u gegevens in een later stadium herstellen.
* Voordat u een virtuele StorSimple-matrix deactiveert, moet u ervoor zorgen dat clients en hosts die afhankelijk zijn van dat apparaat, worden gestopt of verwijderd.
* Een gedeactiveerd apparaat verwijderen als u niet meer gebruikt, zodat er geen kosten in rekening worden gebracht.

### <a name="monitoring"></a>Bewaking
Om ervoor te zorgen dat uw virtuele StorSimple-matrix een voortdurende status heeft, moet u de matrix controleren en ervoor zorgen dat u informatie ontvangt van het systeem, inclusief waarschuwingen. Als u de algehele status van de virtuele matrix wilt bewaken, voert u de volgende aanbevolen procedures uit:

* Configureer bewaking om het schijf gebruik van de gegevens schijf van uw virtuele matrix en de besturingssysteem schijf bij te houden. Als Hyper-V wordt uitgevoerd, kunt u een combi natie van System Center Virtual Machine Manager (SCVMM) en System Center Operations Manager gebruiken om de virtualisatiehost te bewaken.
* E-mail meldingen configureren voor uw virtuele matrix om waarschuwingen op bepaalde gebruiks niveaus te verzenden.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Index zoeken en virus scan toepassingen
Met een virtuele StorSimple-matrix kunnen gegevens van de lokale laag automatisch worden gelaagd naar de Microsoft Azure Cloud. Wanneer een toepassing, zoals een index zoeken of een virus scan, wordt gebruikt voor het scannen van de gegevens die zijn opgeslagen op StorSimple, moet u ervoor zorgen dat de Cloud gegevens niet toegankelijk zijn en terugvallen op de lokale laag.

U wordt aangeraden de volgende aanbevolen procedures te implementeren bij het configureren van de index zoekopdracht of virus scan in uw virtuele array:

* Schakel alle automatisch geconfigureerde volledige scan bewerkingen uit.
* Configureer voor gelaagde volumes de index-Zoek opdracht of virus scan toepassing om een incrementele scan uit te voeren. Hiermee worden alleen de nieuwe gegevens gescand die zich waarschijnlijk op de lokale laag bevinden. De gegevens die in de cloud worden gelaagd, zijn niet toegankelijk tijdens een incrementele bewerking.
* Zorg ervoor dat de juiste Zoek filters en-instellingen zo zijn geconfigureerd dat alleen de beoogde bestands typen worden gescand. Afbeeldings bestanden (JPEG, GIF en TIFF) en technische tekeningen moeten bijvoorbeeld niet worden gescand tijdens het opnieuw opbouwen van de incrementele of volledige index.

Als u Windows indexerings proces gebruikt, volgt u deze richt lijnen:

* Gebruik de Windows-Indexeer functie voor gelaagde volumes niet als er grote hoeveel heden gegevens (TBs) worden teruggehaald uit de Cloud als de index vaak opnieuw moet worden opgebouwd. Als u de index opnieuw bouwt, worden alle bestands typen opgehaald om de inhoud ervan te indexeren.
* Gebruik het Windows-indexerings proces voor lokaal vastgemaakte volumes, omdat hiermee alleen toegang wordt verkregen tot gegevens op de lokale lagen om de index te maken (de Cloud gegevens zijn niet toegankelijk).

### <a name="byte-range-locking"></a>Vergren deling van byte bereik
Toepassingen kunnen een opgegeven bereik aan bytes binnen de bestanden vergren delen. Als het vergren delen van het byte bereik is ingeschakeld voor de toepassingen die naar uw StorSimple schrijven, werkt de lagen niet in uw virtuele matrix. Als u wilt dat de lagen werken, moeten alle delen van de bestanden worden ontgrendeld. Het vergren delen van het bereik van bytes wordt niet ondersteund bij gelaagde volumes in uw virtuele matrix.

De aanbevolen maat regelen om de vergrendeling van het bereik van bytes te beperken, zijn:

* Schakel de vergren deling van het byte bereik uit in uw toepassings logica.
* Lokaal vastgemaakte volumes gebruiken (in plaats van gelaagd) voor de gegevens die zijn gekoppeld aan deze toepassing. Lokaal vastgemaakte volumes maken geen deel uit van de Cloud.
* Bij het gebruik van lokaal vastgemaakte volumes met het vergren delen van het byte bereik is ingeschakeld, kan het volume online worden gezet voordat het herstel is voltooid. In deze gevallen moet u wachten tot de herstel bewerking is voltooid.

## <a name="multiple-arrays"></a>Meerdere matrices
Er moeten mogelijk meerdere virtuele matrices worden geïmplementeerd voor een groeiende werkset gegevens die de prestaties van het apparaat kunnen overlopen op de Cloud. In deze gevallen kunt u het beste apparaten schalen naarmate de werkset groeit. Hiervoor moeten een of meer apparaten worden toegevoegd in het on-premises Data Center. Wanneer u de apparaten toevoegt, kunt u het volgende doen:

* De huidige gegevens verzameling splitsen.
* Nieuwe werk belastingen implementeren op nieuwe apparaten.
* Als u meerdere virtuele arrays implementeert, raden we u aan om de matrix te verdelen over verschillende Hyper Visor-hosts vanuit het perspectief voor taak verdeling.
* Meerdere virtuele matrices (indien geconfigureerd als een bestands server of een iSCSI-server) kunnen worden geïmplementeerd in een Distributed File System naam ruimte. Ga voor gedetailleerde stappen naar [Distributed File System naam ruimte oplossing met de implementatie handleiding voor hybride Cloud opslag](https://www.microsoft.com/download/details.aspx?id=45507). Distributed File System replicatie wordt op dit moment niet aanbevolen voor gebruik met de virtuele matrix. 

## <a name="see-also"></a>Zie ook
Meer informatie over het [beheren van uw StorSimple Virtual array](storsimple-virtual-array-manager-service-administration.md) via de StorSimple Manager-service.