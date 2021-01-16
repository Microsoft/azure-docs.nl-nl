---
title: Probleem oplossing voor certificering van virtuele machines (VM) voor Azure Marketplace
description: Veelvoorkomende problemen oplossen met betrekking tot het testen en certificeren van virtuele machine-installatie kopieën voor Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: troubleshooting
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 921c05b76640935a1bd9e65d556933c23093e5b2
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251434"
---
# <a name="troubleshoot-virtual-machine-certification"></a>Problemen met certificering van virtuele machines oplossen

Wanneer u de installatie kopie van de virtuele machine (VM) publiceert naar Azure Marketplace, wordt deze door het Azure-team gevalideerd om ervoor te zorgen dat het opstart bare, veilige en compatibel met Azure is. Als uw VM-installatie kopie een van de tests van hoge kwaliteit mislukt, wordt deze niet gepubliceerd. U ontvangt een fout bericht waarin het probleem wordt beschreven.

In dit artikel worden veelvoorkomende fout berichten voor het publiceren van VM-installatie kopieën en gerelateerde oplossingen uitgelegd.

> [!NOTE]
> Als u vragen hebt over dit artikel of suggesties voor verbetering, kunt u contact opnemen met de [ondersteuning van partner Center](https://aka.ms/marketplacepublishersupport).


## <a name="vm-extension-failure"></a>VM-extensie fout

Controleer of de installatie kopie VM-extensies ondersteunt.

VM-extensies inschakelen:

1. Selecteer uw virtuele Linux-machine.
1. Ga naar **instellingen voor diagnostische gegevens**.
1. Schakel basis matrices in door het **opslag account** bij te werken.
1. Selecteer **Opslaan**.

   ![Scherm afbeelding waarin wordt weer gegeven hoe bewaking op gast niveau wordt ingeschakeld.](./media/create-vm/vm-certification-issues-solutions-1.png)

Controleren of de VM-extensies correct zijn geactiveerd:

1. Selecteer op de VM het tabblad **VM-extensies** en controleer de status van de **Linux Diagnostics-extensie**.
1. Controleer de inrichtings status.

   - Als de status is *ingericht*, wordt de test case door gegeven.  
   - Als de status van de *inrichting mislukt* is, is de uitbrei ding van de uitbreidings toepassing mislukt en moet u de optie voor de beveiligde vlag instellen.

   ![Scherm opname die laat zien dat het inrichten is geslaagd.](./media/create-vm/vm-certification-issues-solutions-2.png)

   Als de extensie van de virtuele machine mislukt, raadpleegt u de [Diagnostische Linux-extensie gebruiken om metrische gegevens en logboeken te controleren](../virtual-machines/extensions/diagnostics-linux.md) . Als u niet wilt dat de VM-extensie wordt ingeschakeld, neemt u contact op met het ondersteunings team en vraagt u deze uit te scha kelen.

## <a name="vm-provisioning-issue"></a>Probleem met het inrichten van de VM

Controleer of u het VM-inrichtings proces zorgvuldig hebt gevolgd voordat u uw aanbieding verzendt. Zie [een installatie kopie van een virtuele machine testen](azure-vm-image-test.md)om de JSON-indeling voor het inrichten van de VM weer te geven.

Het inrichtings probleem kan de volgende fout scenario's omvatten:

|Scenario|Fout|Reden|Oplossing|
|---|---|---|---|
|1|Ongeldige virtuele harde schijf (VHD)|Als de opgegeven cookie waarde in de voet tekst van de VHD onjuist is, wordt de VHD als ongeldig beschouwd.|Maak de installatie kopie opnieuw en verzend de aanvraag.|
|2|Ongeldig BLOB-type|Het inrichten van de VM is mislukt omdat het gebruikte blok een BLOB-type in plaats van een pagina Type is.|Maak de installatie kopie opnieuw en verzend de aanvraag.|
|3|Time-out voor inrichten of niet juist gegeneraliseerd|Er is een probleem met VM-generalisatie.|Maak de installatie kopie opnieuw met generalisatie en verzend de aanvraag.|

> [!NOTE]
> Zie voor meer informatie over VM-generalisatie:
> - [Linux-documentatie](azure-vm-create-using-approved-base.md#generalize-the-image)
> - [Windows-documentatie](../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)


## <a name="vhd-specifications"></a>VHD-specificaties

### <a name="conectix-cookie-and-other-vhd-specifications"></a>Conectix cookie en andere VHD-specificaties

De teken reeks ' conectix ' maakt deel uit van de VHD-specificatie. Het is gedefinieerd als de 8-bytes cookie in de VHD-voet tekst waarmee de maker van het bestand wordt geïdentificeerd. Alle VHD-bestanden die door micro soft zijn gemaakt, hebben deze cookie. 

Een blob met VHD-indeling moet een 512-byte-voet tekst met de volgende indeling hebben:

|Vaste-schijf voet tekst velden|Grootte (bytes)|
|---|---|
Cookies|8
Functies|4
Bestands indelings versie|4
Gegevens offset|8
Tijdstempel|4
Maker-toepassing|4
Creator-versie|4
Creator host-besturings systeem|4
Oorspronkelijke grootte|8
Huidige grootte|8
Schijf geometrie|4
Schijftype|4
Controlesom|4
Unieke id|16
Opgeslagen status|1
Gereserveerd|427


### <a name="vhd-specifications"></a>VHD-specificaties

Zorg ervoor dat uw VHD voldoet aan de volgende criteria om te zorgen voor een soepele publicatie:

- De cookie bevat de teken reeks ' conectix '.
- Het schijf type is vast.
- De virtuele harde schijf van de VHD is ten minste 20 MB groot.
- De VHD is uitgelijnd. De virtuele grootte moet een meervoud van 1 MB zijn.
- De lengte van de VHD-blob is gelijk aan de virtuele grootte plus de lengte van de VHD-voet tekst (512).

Down load de [VHD-specificatie](https://www.microsoft.com/download/details.aspx?id=23850).

## <a name="software-compliance-for-windows"></a>Software compatibiliteit voor Windows

Als uw Windows-installatie kopie wordt geweigerd vanwege een probleem met de software naleving, hebt u mogelijk een Windows-installatie kopie gemaakt met een geïnstalleerde SQL Server-exemplaar. In plaats daarvan moet u de relevante SQL Server versie basis installatie kopie van Azure Marketplace nemen.

Maak geen eigen Windows-installatie kopie waarop SQL Server geïnstalleerd. Gebruik de goedgekeurde SQL Server basis installatie kopieën (Enter prise/Standard/web) van Azure Marketplace.

Neem contact op met het ondersteunings team voor een eerdere goed keuring als u probeert Visual Studio of een product met Office-licenties te installeren.

Zie [een virtuele machine maken van een goedgekeurde basis](azure-vm-create-using-approved-base.md)voor meer informatie over het selecteren van een goedgekeurde basis.

## <a name="toolkit-test-case-execution-failed"></a>Uitvoering van Toolkit test case is mislukt

Met de micro soft-certificerings Toolkit kunt u test cases uitvoeren en controleren of uw VHD of installatie kopie compatibel is met de Azure-omgeving.

Down load de [micro soft-certificerings Toolkit](azure-vm-image-test.md).

### <a name="linux-test-cases"></a>Linux-test cases

De volgende tabel geeft een lijst van de Linux-test cases die door de Toolkit worden uitgevoerd. Test validatie wordt vermeld in de beschrijving.

|Scenario|Testcase|Beschrijving|
|---|---|---|
|1|Bash-geschiedenis|Bash-geschiedenis bestanden moeten worden gewist voordat u de VM-installatie kopie maakt.|
|2|Versie van Linux-agent|Azure Linux Agent 2.2.41 of hoger moet worden geïnstalleerd.|
|3|Vereiste kernel-para meters|Verifieert of de volgende kernel-para meters zijn ingesteld: <br>console = ttyS0<br>earlyprintk = ttyS0<br>rootdelay = 300 |
|4|Partitie wisselen op besturingssysteem schijf|Hiermee wordt gecontroleerd of wisselende partities niet zijn gemaakt op de besturingssysteem schijf.|
|5|Basis partitie op besturingssysteem schijf|Maak een enkele hoofd partitie voor de besturingssysteem schijf.|
|6|OpenSSL-versie|De OpenSSL-versie moet v 0.9.8 of hoger zijn.|
|7|Python-versie|Python-versie 2,6 of hoger wordt sterk aanbevolen.|
|8|Interval van client Alive|Stel ' ClientAliveInterval in op 180. Op basis van de toepassing kan deze worden ingesteld op 30 tot 235. Als u de SSH inschakelt voor uw eind gebruikers, moet deze waarde worden ingesteld zoals wordt uitgelegd.|
|9|Architectuur van besturingssysteem|Alleen 64-bits besturingssystemen worden ondersteund.|
|10|Automatisch bijwerken|Hiermee wordt aangegeven of automatisch bijwerken van de Linux-agent is ingeschakeld.|

### <a name="common-test-case-errors"></a>Veelvoorkomende test case fouten

Raadpleeg de volgende tabel voor veelvoorkomende fouten bij het uitvoeren van test cases:

| Scenario | Testcase | Fout | Oplossing |
| --- | --- | --- | --- |
| 1 | Test case voor Linux-agent versie | De minimale versie van de Linux-agent is 2.2.41 of hoger. Deze vereiste is verplicht sinds 1 mei 2020. | Werk de versie van de Linux-agent bij. Dit moet 2,241 of hoger zijn. Ga naar de [pagina Linux-agent versie bijwerken](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)voor meer informatie. |
| 2 | Test case voor bash geschiedenis | Er treedt een fout op als de grootte van de bash-geschiedenis in uw verzonden afbeelding meer dan 1 kilo byte (KB) is. De grootte is beperkt tot 1 KB om ervoor te zorgen dat uw bash-geschiedenis bestand geen mogelijk gevoelige informatie bevat. | Los het probleem op door de VHD te koppelen aan een andere werkende VM en wijzigingen aan te brengen om de grootte te verkleinen tot 1 KB of minder. Verwijder bijvoorbeeld de `.bash` geschiedenis bestanden. |
| 3 | Vereiste test case voor de kernel-para meter | U ontvangt deze fout melding als de waarde voor `console` niet is ingesteld op `ttyS0` . Controleer door de volgende opdracht uit te voeren: <br /> `cat /proc/cmdline` | Stel de waarde voor `console` in op `ttyS0` en verzend de aanvraag opnieuw. |
| 4 | Test case ClientAlive-interval | Als de Toolkit een mislukt resultaat voor deze test case krijgt, is er een onjuiste waarde voor `ClientAliveInterval` . | Stel de waarde `ClientAliveInterval` in op kleiner dan of gelijk aan 235 en verzend de aanvraag vervolgens opnieuw. |


### <a name="windows-test-cases"></a>Test cases van Windows

De volgende tabel geeft een lijst van de Windows-test cases die de Toolkit moet uitvoeren, samen met een beschrijving van de test validatie:

|Scenario |Testcases|Beschrijving|
|---|---|---|
|1|Architectuur van besturingssysteem|Azure ondersteunt alleen 64-bits besturings systemen.|
|2|Afhankelijkheid van gebruikers account|Uitvoering van toepassing mag niet afhankelijk zijn van het Administrator-account.|
|3|Failovercluster|De Windows Server failover clustering-functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|4|IPCONFIGURATION|IPv6 wordt nog niet ondersteund in de Azure-omgeving. De toepassing mag niet afhankelijk zijn van deze functie.|
|5|DHCP|Dynamic Host Configuration Protocol server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|6|Hyper-V|De Hyper-V-Server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|7|Externe toegang|De serverrol voor externe toegang (Direct Access) wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|8|Rights Management Services|Rights Management Services. De server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|9|Windows Deployment Services|Windows Deployment Services. De server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|10|BitLocker-stationsversleuteling|BitLocker-stationsversleuteling wordt niet ondersteund op de vaste schijf van het besturings systeem, maar kan worden gebruikt op gegevens schijven.|
|11|Naam server voor Internet opslag|De functie Internet Storage Name Server wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|12|Multipath I/O|Multipath I/O. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|13|Netwerktaakverdeling|Netwerk taakverdeling. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|14|Peer Name Resolution Protocol|Peer Name Resolution Protocol. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|15|SNMP-services|De functie Services van Simple Network Management Protocol (SNMP) wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|16|Windows Internet Name Service|Windows Internet Name Service. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|
|17|WLAN-service (Wireless LAN)|Draadloze LAN-service. Deze server functie wordt nog niet ondersteund. De toepassing mag niet afhankelijk zijn van deze functie.|

Als u problemen ondervindt met de voor gaande test cases, raadpleegt u de kolom **Beschrijving** in de tabel voor de oplossing. Neem contact op met het ondersteunings team voor meer informatie. 

## <a name="data-disk-size-verification"></a>Verificatie van de grootte van de gegevens schijf

Gegevens schijf aanvragen met een grootte groter dan 1023 gigabytes (GB) worden niet goedgekeurd. Deze regel is van toepassing op Linux en Windows.

Verzend de aanvraag opnieuw met een grootte die kleiner is dan of gelijk is aan 1023 GB.

## <a name="os-disk-size-validation"></a>Validatie van schijf grootte van besturings systeem

Raadpleeg de volgende regels voor beperkingen op de schijf grootte van het besturings systeem. Wanneer u een aanvraag indient, controleert u of de grootte van de besturingssysteem schijf binnen de limiet voor Linux of Windows valt.

|Besturingssysteem|Aanbevolen grootte voor VHD|
|---|---|
|Linux|1 GB tot 1023 GB|
|Windows|30 GB tot 250 GB|

Omdat Vm's toegang bieden tot het onderliggende besturings systeem, moet u ervoor zorgen dat de VHD groot genoeg is voor de VHD. Schijven zijn niet uitbreidbaar zonder uitval tijd. Gebruik een schijf grootte van 30 GB tot 50 GB.

|VHD-grootte|Werkelijke grootte|Oplossing|
|---|---|---|
|>500 tebibytes (TiB)|n.v.t.|Neem contact op met het ondersteunings team voor een uitzonderings goedkeuring.|
|250-500 TiB|>200 gibibytes (GiB) verschilt van de grootte van de BLOB|Neem contact op met het ondersteunings team voor een uitzonderings goedkeuring.|

> [!NOTE]
> Grotere schijf grootten leiden tot hogere kosten en veroorzaken een vertraging tijdens de installatie en het replicatie proces. Als gevolg van deze vertraging en kosten kan het ondersteunings team een reden voor de goed keuring van uitzonde ringen zoeken.

## <a name="wannacry-patch-verification-test-for-windows"></a>Verificatie test voor de WannaCry-patch voor Windows

Als u een mogelijke aanval met betrekking tot het WannaCry-virus wilt voor komen, moet u ervoor zorgen dat alle Windows-installatie kopie aanvragen worden bijgewerkt met de nieuwste patch.

U kunt de versie van het installatie kopie bestand controleren vanuit `C:\windows\system32\drivers\srv.sys` of `srv2.sys` .

De volgende tabel bevat de minimale versie van Windows Server met patches: 

|Besturingssysteem|Versie|
|---|---|
|Windows met 2008 R2|6.1.7601.23689|
|Windows Server 2012|6.2.9200.22099|
|Windows Server 2012 R2|6.3.9600.18604|
|Windows Server 2016|10.0.14393.953|
|Windows Server 2019|NA|

> [!NOTE]
> Windows Server 2019 heeft geen verplichte versie vereisten.

## <a name="sack-vulnerability-patch-verification"></a>Verificatie van beveiligings patch voor de Opzakken

Wanneer u een Linux-installatie kopie verzendt, wordt uw aanvraag mogelijk afgewezen vanwege problemen met de kernel-versie.

Werk de kernel bij met een goedgekeurde versie en verzend de aanvraag opnieuw. U kunt de goedgekeurde versie van de kernel vinden in de volgende tabel. Het versie nummer moet gelijk zijn aan of groter zijn dan het nummer dat hier wordt weer gegeven.

Als uw installatie kopie niet is geïnstalleerd met een van de volgende kernel-versies, werkt u deze bij met de juiste patches. De benodigde goed keuring van het ondersteunings team aanvragen nadat de installatie kopie is bijgewerkt met de volgende vereiste patches:

- CVE-2019-11477 
- CVE-2019-11478 
- CVE-2019-11479

|BESTURINGSSYSTEEM familie|Versie|Kernel|
|---|---|---|
|Ubuntu|14,04 LTS|4.4.0-151| 
||14,04 LTS|4.15.0-1049- \* -Azure|
||16,04 LTS|4.15.0-1049|
||18,04 LTS|4.18.0-1023|
||18,04 LTS|5.0.0-1025|
||18,10|4.18.0-1023|
||19,04|5.0.0-1010|
||19,04|5.3.0-1004|
|RHEL en Cent OS|6.10|2.6.32-754.15.3|
||7.2|3.10.0-327.79.2|
||7.3|3.10.0-514.66.2|
||7.4|3.10.0-693.50.3|
||7,5|3.10.0-862.34.2|
||7.6|3.10.0-957.21.3|
||7,7|3.10.0-1062.1.1|
||8.0|4.18.0-80.4.2|
||8.1|4.18.0-147|
||"7-RAW" (7,6)||
||"7-LVM" (7,6)|3.10.0-957.21.3|
||RHEL-SAP 7,4|NOG TE BEPALEN|
||RHEL-SAP 7,5|NOG TE BEPALEN|
|SLES|SLES11SP4 (inclusief SAP)|3.0.101-108.95.2|
||SLES12SP1 voor SAP|3.12.74-60.64.115.1|
||SLES12SP2 voor SAP|4.4.121-92.114.1|
||SLES12SP3|4.4180-4.31.1 (kernel-Azure)|
||SLES12SP3 voor SAP|4.4.180-94.97.1|
||SLES12SP4|4.12.14-6.15.2 (kernel-Azure)|
||SLES12SP4 voor SAP|4.12.14-95.19.1|
||SLES15|4.12.14-5.30.1 (kernel-Azure)|
||SLES15 voor SAP|4.12.14-5.30.1 (kernel-Azure)|
||SLES15SP1|4.12.14-5.30.1 (kernel-Azure)|
|Oracle|6.10|UEK2 2.6.39-400.312.2<br>UEK3 3.8.13-118.35.2<br>RHCK 2.6.32-754.15.3 
||7.0-7.5|UEK3 3.8.13-118.35.2<br>UEK4 4.1.12-124.28.3<br>RHCK volgt RHEL hierboven|
||7.6|RHCK 3.10.0-957.21.3<br>UEK5 4.14.35-1902.2.0|
|CoreOS stabiele 2079.6.0|4.19.43\*|
||Bèta 2135.3.1|4.19.50\*|
||Alpha-2163.2.1|4.19.50\*|
|Debian|Jessie (beveiliging)|3.16.68-2|
||Jessie backports|4.9.168-1 + deb9u3|
||uitrekken (beveiliging)|4.9.168-1 + deb9u3|
||Debian GNU/Linux 10 (Buster)|Debian 6.3.0-18 + deb9u1|
||Buster, sid (stretch backports)|4.19.37-5|

## <a name="image-size-should-be-in-multiples-of-megabytes"></a>De grootte van de installatie kopie moet in meerdere mega bytes staan

Alle Vhd's op Azure moeten een virtuele grootte hebben die is afgestemd op veelvouden van 1 Mega byte (MB). Als uw VHD niet aan de aanbevolen virtuele grootte voldoet, wordt uw aanvraag mogelijk geweigerd.

Volg de richt lijnen wanneer u van een onbewerkte schijf naar VHD converteert. Zorg ervoor dat de grootte van de RAW-schijf een meervoud van 1 MB is. Zie [informatie over niet-goedgekeurde distributies](../virtual-machines/linux/create-upload-generic.md)voor meer informatie.

## <a name="vm-access-denied"></a>VM-toegang geweigerd

Een probleem met _geweigerde toegang_ voor het uitvoeren van een test case op de VM wordt mogelijk veroorzaakt door onvoldoende bevoegdheden.

Controleer of u de juiste toegang hebt ingeschakeld voor het account waarop de self-test cases worden uitgevoerd. Schakel toegang voor het uitvoeren van test cases in als deze niet is ingeschakeld. Als u geen toegang wilt inschakelen, kunt u de test resultaten met het ondersteunings team delen.

Uw aanvraag met een door SSH uitgeschakelde afbeelding verzenden voor het certificerings proces:

1. Voer het [hulp programma voor de nieuwste certificerings test uit voor virtuele Azure-machines](https://aka.ms/AzureCertificationTestTool) in uw installatie kopie.

2. Een [ondersteunings ticket](https://aka.ms/marketplacepublishersupport)genereren. Zorg ervoor dat u het Toolkit rapport bijvoegt en Details van de aanbieding opgeeft:
   - Naam van aanbieding
   - Naam van de uitgever
   - Plan-ID/SKU en versie

3. Verzend uw certificerings aanvraag opnieuw.

## <a name="download-failure"></a>Fout bij het downloaden
    
Raadpleeg de volgende tabel voor problemen die zich voordoen wanneer u de VM-installatie kopie downloadt met een SAS-URL (Shared Access Signature).

|Scenario|Fout|Reden|Oplossing|
|---|---|---|---|
|1|De blob is niet gevonden|De VHD kan worden verwijderd of verplaatst van de opgegeven locatie.|| 
|2|BLOB in gebruik|De VHD wordt gebruikt door een ander intern proces.|De VHD moet een gebruikte status hebben wanneer u deze downloadt met een SAS-URL.|
|3|Ongeldige SAS-URL|De bijbehorende SAS-URL voor de VHD is onjuist.|Haal de juiste SAS-URL op.|
|4|Ongeldige hand tekening|De bijbehorende SAS-URL voor de VHD is onjuist.|Haal de juiste SAS-URL op.|
|6|Voorwaardelijke HTTP-header|De SAS-URL is ongeldig.|Haal de juiste SAS-URL op.|
|7|Ongeldige naam voor VHD|Controleer of er speciale tekens, zoals een procent teken `%` of aanhalings tekens `"` , aanwezig zijn in de naam van de virtuele harde schijf.|Wijzig de naam van het VHD-bestand door de speciale tekens te verwijderen.|

## <a name="first-1-mb-partition-2048-sectors-each-sector-of-512-bytes"></a>Eerste partitie van 1 MB (2.048 sectoren, elke sector van 512 bytes)

Als u [uw eigen installatie kopie bouwt](azure-vm-create-using-own-image.md), moet u ervoor zorgen dat de eerste 2.048 sectoren (1 MB) van de besturingssysteem schijf leeg zijn. Als dat niet het geval is, mislukt de publicatie. Deze vereiste is alleen van toepassing op de besturingssysteem schijf (niet op gegevens schijven). Als u een installatie kopie [van een goedgekeurde basis](azure-vm-create-using-approved-base.md)bouwt, kunt u deze vereiste overs Laan. 

### <a name="create-a-1-mb-partition-2048-sectors-each-sector-of-512-bytes-on-an-empty-vhd-linux-only-steps"></a>Een partitie van 1 MB (2.048 sectoren, elke sector van 512 bytes) maken op een lege VHD (alleen Linux-stappen)

Deze stappen zijn alleen van toepassing op Linux.

1. Maak een wille keurig type Linux-VM, zoals Ubuntu, Cent OS of andere. Vul de vereiste velden in en selecteer **volgende: schijven >**.

   ![Scherm opname van de pagina een virtuele machine maken met de knop volgende: schijven is gemarkeerd.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Maak een niet-beheerde schijf voor uw VM.

   Gebruik de standaard waarden of geef een waarde op voor velden als NIC, NSG en openbaar IP-adres.

   ![Scherm afbeelding van de pagina gegevens schijven in de stroom voor het maken van een virtuele machine.](./media/create-vm/vm-certification-issues-solutions-16.png)

1. Nadat u de virtuele machine hebt gemaakt, selecteert u **schijven** in het linkerdeel venster.

   ![Scherm afbeelding die laat zien hoe u schijven voor een virtuele machine selecteert.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Koppel uw VHD als gegevens schijf aan uw VM om een partitie tabel te maken.

   1. Selecteer **DataDisk**  >  **bestaande BLOB** toevoegen.

      ![Scherm afbeelding die laat zien hoe u een gegevens schijf kunt toevoegen aan uw VHD.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Zoek uw VHD Storage-account.
   1. Selecteer **container** en selecteer vervolgens uw VHD.
   1. Selecteer **OK**.

      ![Scherm afbeelding van de pagina niet-beheerde schijf koppelen.](./media/create-vm/vm-certification-issues-solutions-19.png)

      Uw VHD wordt toegevoegd als Data Disk LUN 0.

   1. Start de VM opnieuw op.

1. Nadat u de VM opnieuw hebt opgestart, meldt u zich aan bij de VM met behulp van Putty of een andere client en voert u de `sudo  -i` opdracht uit om toegang tot het hoofd te krijgen.

   ![Scherm opname van de opdracht regel van de putty-client met de sudo-i-opdracht.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Maak een partitie op uw VHD.

   1. Voer de `fdisk /dev/sdb` opdracht in.
   1. Als u de bestaande partitie lijst van uw VHD wilt weer geven, voert u in `p` .
   1. Voer `d` in om alle bestaande beschik bare partities in uw VHD te verwijderen. Als het niet nodig is, kunt u deze stap overs Laan.

      ![Scherm opname van de opdracht regel van de putty-client met de opdrachten voor het verwijderen van bestaande partities.](./media/create-vm/vm-certification-issues-solutions-21.png)

   1. Voer `n` in om een nieuwe partitie te maken en selecteer `p` voor (primaire partitie).

   1. Voer 2048 in als _eerste sector_ waarde. U kunt de _laatste sector_ als de standaard waarde laten staan.

      >[!IMPORTANT]
      >Alle bestaande gegevens worden gewist tot een hoeveelheid van 2048 sectoren (elke sector van 512 bytes). Maak een back-up van de VHD voordat u een nieuwe partitie maakt.

      ![Scherm opname van de opdracht regel van de putty-client met de opdrachten en uitvoer voor gegevens die zijn gewist.](./media/create-vm/vm-certification-issues-solutions-22.png)

   1. Typ `w` om te bevestigen dat de partitie wordt gemaakt. 

      ![Scherm opname van de opdracht regel van de putty-client met de opdrachten voor het maken van een partitie.](./media/create-vm/vm-certification-issues-solutions-23.png)

   1. U kunt de partitie tabel controleren door de opdracht uit te voeren `n fdisk /dev/sdb` en te typen `p` . U ziet dat de partitie wordt gemaakt met een offset waarde van 2048. 

      ![Scherm opname van de opdracht regel van de putty-client met de opdrachten voor het maken van de 2048-offset.](./media/create-vm/vm-certification-issues-solutions-24.png)

1. Ontkoppel de VHD van de VM en verwijder de virtuele machine.

### <a name="create-a-first-1-mb-partition-2048-sectors-each-sector-of-512-bytes-by-moving-existing-data-on-vhd"></a>Maak een eerste partitie van 1 MB (2.048 sectoren, elke sector van 512 bytes) door bestaande gegevens op de VHD te verplaatsen

Deze stappen zijn alleen van toepassing op Linux.

1. Maak een wille keurig type Linux-VM, zoals Ubuntu, Cent OS of andere. Vul de vereiste velden in en selecteer **volgende: schijven >**.

   ![Scherm opname van de pagina een virtuele machine maken met de knop volgende: schijven is gemarkeerd.](./media/create-vm/vm-certification-issues-solutions-15.png)

1. Maak een niet-beheerde schijf voor uw VM.

   ![Scherm afbeelding van de pagina gegevens schijven in de stroom voor het maken van een virtuele machine.](./media/create-vm/vm-certification-issues-solutions-16.png)

   Gebruik de standaard waarden of geef een waarde op voor velden als NIC, NSG en openbaar IP-adres.

1. Nadat u de virtuele machine hebt gemaakt, selecteert u **schijven** in het linkerdeel venster.

   ![Scherm afbeelding die laat zien hoe u schijven voor een virtuele machine selecteert.](./media/create-vm/vm-certification-issues-solutions-17.png)

1. Koppel uw VHD als gegevens schijf aan uw VM om een partitie tabel te maken.

   1. Koppel uw VHD als gegevens schijf aan uw VM om een partitie tabel te maken.

   1. Selecteer **DataDisk**  >  **bestaande BLOB** toevoegen.

      ![Scherm afbeelding die laat zien hoe u een gegevens schijf kunt toevoegen aan uw VHD.](./media/create-vm/vm-certification-issues-solutions-18.png)

   1. Zoek uw VHD Storage-account.
   1. Selecteer **container** en selecteer vervolgens uw VHD.
   1. Selecteer **OK**.

      ![Scherm afbeelding van de pagina niet-beheerde schijf koppelen.](./media/create-vm/vm-certification-issues-solutions-19.png)

      Uw VHD wordt toegevoegd als Data Disk LUN 0.

   1. Start de VM opnieuw op.

1. Meld u aan bij de VM met Putty of een andere client en voer de `sudo  -i` opdracht uit om toegang tot het hoofd te krijgen.

   ![Scherm opname van de opdracht regel van putty-client met de logboeken en de sudo-i-opdracht.](./media/create-vm/vm-certification-issues-solutions-20.png)

1. Voer de opdracht `echo '+1M,' | sfdisk --move-data /dev/sdc -N 1` uit.

   ![Scherm opname van de opdracht regel van de putty-client met de uitvoering van de opdrachten.](./media/create-vm/vm-certification-issues-solutions-25.png)

   >[!NOTE]
   >Het kan enige tijd duren voordat deze opdracht is voltooid, omdat deze afhankelijk is van de grootte van de schijf.

1. Ontkoppel de VHD van de VM en verwijder de virtuele machine.


## <a name="default-credentials"></a>Standaard referenties

Stuur nooit standaard referenties met de verzonden VHD. Het toevoegen van standaard referenties maakt de VHD kwetsbaarder voor beveiligings Risico's. Maak in plaats daarvan uw eigen referenties wanneer u de VHD verzendt.
  
## <a name="datadisk-mapped-incorrectly"></a>DataDisk onjuist toegewezen

Een toewijzings probleem kan optreden wanneer een aanvraag wordt ingediend met meerdere gegevens schijven die niet op volg orde staan. De Nummerings volgorde voor drie gegevens schijven moet bijvoorbeeld *0, 1, 2* zijn. Alle andere orders worden behandeld als een toewijzings probleem.

Verzend de aanvraag opnieuw met de juiste volg orde van de gegevens schijven.

## <a name="incorrect-os-mapping"></a>Onjuiste besturingssysteem toewijzing

Wanneer een installatie kopie wordt gemaakt, kan deze worden toegewezen aan of worden toegewezen aan het verkeerde besturingssysteem label. Wanneer u bijvoorbeeld **Windows** als onderdeel van de naam van het besturings systeem selecteert tijdens het maken van de installatie kopie, moet de besturingssysteem schijf alleen met Windows worden geïnstalleerd. Dezelfde vereiste is van toepassing op Linux.

## <a name="vm-not-generalized"></a>De VM is niet gegeneraliseerd

Als alle installatie kopieën die vanuit Azure Marketplace worden gemaakt, opnieuw moeten worden gebruikt, moet de virtuele harde schijf van het besturings systeem worden gegeneraliseerd.

* Voor **Linux** wordt met het volgende proces een virtuele Linux-machine gegeneraliseerd en opnieuw geïmplementeerd als een afzonderlijke VM.

  Voer in het SSH-venster de volgende opdracht in: `sudo waagent -deprovision+user` .

* Voor **Windows** kunt u Windows-installatie kopieën generaliseren met behulp van `sysreptool` .

  `sysreptool`Zie [overzicht van systeem voorbereiding (Sysprep)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)voor meer informatie over het hulp programma.

## <a name="datadisk-errors"></a>DataDisk-fouten

Gebruik de volgende tabel voor oplossingen voor fouten die betrekking hebben op de gegevens schijf:

|Fout|Reden|Oplossing|
|---|---|---|
|`DataDisk- InvalidUrl:`|Deze fout kan optreden vanwege een ongeldige Logical Unit Number (LUN) wanneer de aanbieding is verzonden.|Controleer of de LUN-nummer reeks voor de gegevens schijf zich in het partner centrum bevindt.|
|`DataDisk- NotFound:`|Deze fout kan optreden omdat een gegevens schijf zich niet bevindt op een opgegeven SAS-URL.|Controleer of de gegevens schijf zich op de opgegeven SAS-URL bevindt.|

## <a name="remote-access-issue"></a>Probleem met externe toegang

U ontvangt deze fout melding als de optie Remote Desktop Protocol (RDP) niet is ingeschakeld voor de Windows-installatie kopie.

Schakel RDP-toegang voor Windows-installatie kopieën in voordat u deze verzendt.

## <a name="bash-history-failed"></a>Bash-geschiedenis is mislukt

U ziet deze fout als de grootte van de bash-geschiedenis in uw verzonden afbeelding meer dan 1 kilo byte (KB) is. De grootte is beperkt tot 1 KB om het bestand te beperken tegen mogelijk gevoelige informatie.

De bash-geschiedenis verwijderen:

1. Implementeer de virtuele machine en selecteer de optie **opdracht uitvoeren** op de Azure Portal.

   ![Scherm afbeelding van de Azure Portal met de optie opdracht uitvoeren in het linkerdeel venster.](./media/create-vm/vm-certification-issues-solutions-3.png)

1. Selecteer eerste optie **RunShellScript** en voer de opdracht uit: `cat /dev/null > ~/.bash_history && history -c` .

   ![Scherm opname van de pagina opdracht script uitvoeren op de Azure Portal.](./media/create-vm/vm-certification-issues-solutions-4.png)

1. Nadat de opdracht is uitgevoerd, start u de VM opnieuw op.

1. Generaliseer de virtuele machine, maak de VHD met installatie kopieën en stop de virtuele machine.

1. Verzend de gegeneraliseerde installatie kopie opnieuw.

## <a name="request-an-exception-on-vm-images-for-select-tests"></a>Een uitzonde ring op VM-installatie kopieën aanvragen voor Select-tests

Uitgevers kunnen uitzonde ringen aanvragen voor een aantal tests die zijn uitgevoerd tijdens de VM-certificering. Uitzonde ringen worden in zeldzame gevallen gegeven wanneer een uitgever bewijs levert ter ondersteuning van de aanvraag. Het certificerings team behoudt zich het recht voor om uitzonde ringen op elk gewenst moment te weigeren of goed te keuren.

In deze sectie worden algemene scenario's beschreven waarin uitgevers een uitzonde ring aanvragen en een aanvraag indienen.

### <a name="scenarios-for-exception"></a>Scenario's voor uitzonde ring

Uitgevers vragen over het algemeen uitzonde ringen in de volgende gevallen:

- **Uitzonde ring voor een of meer test cases**. Neem contact op met de [ondersteuning van partners](https://aka.ms/marketplacepublishersupport) voor het aanvragen van uitzonde ringen voor test cases.

- **Vergrendelde vm's/geen hoofd toegang**. Enkele uitgevers hebben scenario's waarin Vm's moeten worden vergrendeld omdat ze software, zoals firewalls, hebben geïnstalleerd op de virtuele machine. In dit geval kunt u het [gecertificeerde test programma](https://aka.ms/AzureCertificationTestTool) downloaden en het rapport verzenden bij [Partner Center-ondersteuning](https://aka.ms/marketplacepublishersupport).

- **Aangepaste sjablonen**. Sommige uitgevers publiceren VM-installatie kopieën waarvoor een aangepaste Azure Resource Manager (ARM)-sjabloon vereist is om de virtuele machines te implementeren. In dit geval moet u de aangepaste sjablonen indienen bij [Partner Center-ondersteuning](https://aka.ms/marketplacepublishersupport) , zodat deze kan worden gebruikt door het certificerings team voor validatie.

### <a name="information-to-provide-for-exception-scenarios"></a>Informatie over uitzonderings scenario's

Neem contact op met het [partner centrum-ondersteuning](https://aka.ms/marketplacepublishersupport) om een uitzonde ring voor een van de scenario's aan te vragen en neem de volgende informatie op:

- **Uitgevers-id**. Typ de uitgevers-ID van uw partner Central-Portal.
- **Aanbiedings-id/naam**. Voer de aanbiedings-ID of-naam in.
- **SKU/plan-id**. Typ de abonnement-ID of SKU van de VM-aanbieding.
- **Versie**. Voer de versie van de VM-aanbieding in waarvoor een uitzonde ring is vereist.
- **Type uitzonde ring**. Kies uit tests, vergrendelde VM'S of aangepaste sjablonen.
- **Reden van aanvraag**. Neem de reden voor de uitzonderings aanvraag op, plus alle informatie over de uitzonde ringen op de test.
- **Tijd lijn**. Voer de eind datum voor uw uitzonde ring in.
- **Bijlage**. Bijgevoegd belang rijke bewijs documenten:

  - Koppel het test rapport voor vergrendelde Vm's.
  - Voor aangepaste sjablonen geeft u de aangepaste ARM-sjabloon op als bijlage.

  Als u deze bijlagen niet opneemt, wordt uw aanvraag geweigerd.

## <a name="address-a-vulnerability-or-an-exploit-in-a-vm-offer"></a>Een beveiligingslek of een aanval in een VM-aanbieding oplossen

In deze sectie wordt beschreven hoe u een nieuwe VM-installatie kopie kunt opgeven wanneer er een beveiligings probleem of misbruik wordt gedetecteerd met een van uw VM-installatie kopieën. Het is alleen van toepassing op Azure-VM'S die zijn gepubliceerd op Azure Marketplace.

> [!NOTE]
> U kunt de laatste VM-installatie kopie niet uit een plan verwijderen of het laatste abonnement voor een aanbieding stoppen.

Voer een van de volgende acties uit:

- Zie [een vaste VM-installatie kopie opgeven](#provide-a-fixed-vm-image)als u een nieuwe VM-installatie kopie hebt om de KWETS bare VM-installatie kopie te vervangen.
- Als u geen nieuwe VM-installatie kopie hebt om de enige VM-installatie kopie in een abonnement te vervangen of als u klaar bent met het abonnement, kunt u [het abonnement niet meer verkopen](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).
- Als u niet van plan bent om de enige VM-installatie kopie in de aanbieding te vervangen, raden we u aan om te [stoppen met het verkopen van de aanbieding](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="provide-a-fixed-vm-image"></a>Een vaste VM-installatie kopie opgeven

Om een installatie kopie van een virtuele machine te vervangen die een beveiligings probleem of misbruik heeft:

1. Geef een nieuwe VM-installatie kopie op om het beveiligings probleem op te lossen of misbruik te maken.
1. Verwijder de VM-installatie kopie met het beveiligings probleem of gebruik.
1. Publiceer de aanbieding opnieuw.

#### <a name="provide-a-new-vm-image-to-address-the-security-vulnerability-or-exploit"></a>Een nieuwe VM-installatie kopie opgeven om het beveiligings probleem op te lossen of misbruik te maken

Als u deze stappen wilt uitvoeren, moet u de technische assets voorbereiden voor de VM-installatie kopie die u wilt toevoegen. Zie [een virtuele machine maken met behulp van een goedgekeurde basis](azure-vm-create-using-approved-base.md)of [een virtuele machine maken met uw eigen installatie kopie](azure-vm-create-using-own-image.md) en [een SAS-URI voor uw VM-installatie kopie genereren](azure-vm-get-sas-uri.md)voor meer informatie.

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
1. Selecteer in het linkerdeel venster het gedeelte **Commercial Marketplace**-  >  **overzicht**.
1. Selecteer de aanbieding in de kolom **aanbiedings alias** .
1. Selecteer op het tabblad **plan overzicht** , in de kolom **naam** , het juiste abonnement.
1. Selecteer op het tabblad **technische configuratie** onder **VM-installatie kopieën** **+ installatie kopie van virtuele machine toevoegen**.

   > [!NOTE]
   > U kunt slechts één VM-installatie kopie toevoegen aan één abonnement per keer. Als u meerdere VM-installatie kopieën wilt toevoegen, moet u het eerste publiceren voordat u de volgende VM-installatie kopie toevoegt.

1. Geef in de weer gegeven vakken een nieuwe schijf versie en de installatie kopie van de virtuele machine op.
1. Selecteer **Concept opslaan**.

Verwijder vervolgens de VM-installatie kopie met behulp van het beveiligings probleem.

#### <a name="remove-the-vm-image-with-the-security-vulnerability-or-exploit"></a>De VM-installatie kopie verwijderen met het beveiligings probleem of de misbruik

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het linkerdeel venster het gedeelte **Commercial Marketplace**-  >  **overzicht**.
3. Selecteer de aanbieding in de kolom **aanbiedings alias** .
4. Selecteer op het tabblad **plan overzicht** , in de kolom **naam** , het juiste abonnement.
5. Op het tabblad **technische configuratie** onder **VM-installatie kopieën** naast de VM-installatie kopie die u wilt verwijderen, selecteert u VM- **installatie kopie verwijderen**.
6. Selecteer **door gaan** in het dialoog venster.
7. Selecteer **Concept opslaan**.

Publiceer vervolgens de aanbieding opnieuw.

#### <a name="republish-the-offer"></a>De aanbieding opnieuw publiceren

1. Selecteer **controleren en publiceren**.
2. Als u informatie moet verstrekken aan het certificerings team, voegt u deze toe aan het vak **notities voor certificering** .
3. Selecteer **Publiceren**.

Zie [aanbiedingen bekijken en publiceren](review-publish-offer.md)om het publicatie proces te volt ooien.

## <a name="next-steps"></a>Volgende stappen

- [Eigenschappen van een VM-aanbieding configureren](azure-vm-create-properties.md)
- [Beloonde actieve Marketplace](partner-center-portal/marketplace-rewards.md)
- Als u vragen of feedback over verbeteringen hebt, neemt u contact op met het [partner centrum-ondersteuning](https://aka.ms/marketplacepublishersupport).
