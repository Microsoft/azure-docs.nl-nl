---
title: StorSimple 8000-serie oplossingen overzicht | Microsoft Docs
description: Hierin worden de StorSimple-lagen, het apparaat, het virtuele apparaat, de services en het opslag beheer beschreven en worden de belangrijkste termen in StorSimple geintroduceert.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/11/2021
ms.author: timlt
ms.openlocfilehash: 286f00318715764a5943387e6b362b3c8e69a82e
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98808149"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000-serie: een Hybrid Cloud Storage oplossing

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Overzicht
Welkom bij Microsoft Azure StorSimple, een geïntegreerde opslag oplossing voor het beheer van opslag taken tussen on-premises apparaten en Microsoft Azure Cloud opslag. StorSimple is een efficiënte, rendabele en eenvoudig te beheren Storage Area Network oplossing (SAN) waarmee veel van de problemen en kosten die zijn gekoppeld aan ondernemings opslag en gegevens bescherming, worden geëlimineerd. StorSimple gebruikt het eigen apparaat uit de StorSimple 8000-reeks, integreert met cloudservices en biedt een reeks hulpprogramma’s voor beheer voor een naadloze weergave van alle Enterprise-opslag, waaronder cloudopslag. (De StorSimple-implementatie gegevens die op de Microsoft Azure-website worden gepubliceerd, is alleen van toepassing op apparaten met StorSimple 8000-serie. Als u een StorSimple 5000/7000-serie apparaat gebruikt, gaat u naar [StorSimple Help](http://onlinehelp.storsimple.com/).)

StorSimple maakt gebruik van [opslag lagen](#automatic-storage-tiering) voor het beheren van opgeslagen gegevens op verschillende opslag media. De huidige werkset wordt lokaal opgeslagen op Ssd's (Solid state drives), gegevens die minder vaak worden gebruikt, worden opgeslagen op de harde schijven (Hdd's) en de archiverings gegevens worden naar de Cloud gepusht. Bovendien gebruikt StorSimple ontdubbeling en compressie om de hoeveelheid opslag ruimte die de gegevens verbruikt te verminderen. Ga voor meer informatie naar [ontdubbeling en compressie](#deduplication-and-compression). Voor definities van andere belang rijke termen en concepten die worden gebruikt in de StorSimple 8000 Series-documentatie, gaat u naar [StorSimple terminologie](#storsimple-terminology) aan het einde van dit artikel.

Naast opslag beheer kunt u met StorSimple-functies voor gegevens bescherming op aanvraag en geplande back-ups maken en deze vervolgens lokaal of in de Cloud opslaan. Back-ups worden gemaakt in de vorm van incrementele moment opnamen, wat betekent dat ze snel kunnen worden gecreëerd en teruggezet. Cloud momentopnamen kunnen zeer belang rijk zijn in scenario's voor herstel na nood gevallen, omdat ze secundaire opslag systemen (zoals tape back-ups) vervangen en u zo nodig gegevens kunt herstellen naar uw Data Center of naar alternatieve sites.

![Video pictogram](./media/storsimple-overview/video_icon.png) Bekijk de video voor een korte inleiding tot Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Waarom StorSimple gebruiken?
In de volgende tabel worden enkele van de belangrijkste voor delen beschreven die Microsoft Azure StorSimple biedt.

| Functie | Voordeel |
| --- | --- |
| Transparante integratie |Maakt gebruik van het iSCSI-protocol om onzichtbaar faciliteiten voor gegevens opslag te koppelen. Dit zorgt ervoor dat gegevens die zijn opgeslagen in de Cloud, op het Data Center of op externe servers, op één locatie worden opgeslagen. |
| Lagere opslag kosten |Wijst voldoende lokale of Cloud opslag toe om te voldoen aan de huidige vereisten en breidt de Cloud opslag alleen uit als dat nodig is. Het vermindert de opslag vereisten en-uitgaven door redundante versies van dezelfde gegevens (ontdubbeling) te elimineren en door compressie te gebruiken. |
| Vereenvoudigd opslag beheer |Biedt hulpprogram ma's voor systeem beheer voor het configureren en beheren van gegevens die zijn opgeslagen op locatie, op een externe server en in de Cloud. Daarnaast kunt u back-up-en herstel functies beheren vanuit een MMC-module (micro soft Management Console).|
| Verbeterd herstel na nood gevallen en naleving |Vereist geen uitgebreide herstel tijd. In plaats daarvan worden de gegevens hersteld wanneer dat nodig is, zodat normale bewerkingen met minimale onderbrekingen kunnen worden voortgezet. Daarnaast kunt u beleids regels configureren voor het opgeven van back-upscheman en het bewaren van gegevens. |
| Gegevens mobiliteit |Gegevens die zijn geüpload naar Microsoft Azure Cloud Services, kunnen worden geopend vanaf andere sites voor herstel-en migratie doeleinden. Daarnaast kunt u StorSimple gebruiken om StorSimple-Cloud apparaten te configureren op virtuele machines (Vm's) die worden uitgevoerd in Microsoft Azure. De Vm's kunnen vervolgens virtuele apparaten gebruiken om toegang te krijgen tot opgeslagen gegevens voor test-of herstel doeleinden. |
| Bedrijfscontinuïteit |Hiermee kunnen gebruikers van de StorSimple 5000-7000-serie hun gegevens migreren naar een StorSimple 8000 Series-apparaat. |
| Beschik baarheid in de Azure Government Portal |StorSimple is beschikbaar in de Azure Government Portal. Zie [uw on-premises StorSimple-apparaat implementeren in de overheids Portal](storsimple-8000-deployment-walkthrough-gov-u2.md)voor meer informatie. |
| Gegevens bescherming en-beschik baarheid |De StorSimple 8000-serie ondersteunt zone redundante opslag (ZRS), naast lokaal redundante opslag (LRS) en geografisch redundante opslag (GRS). Raadpleeg [dit artikel over Azure Storage redundantie opties](../storage/common/storage-redundancy.md) voor ZRS-gegevens. |
| Ondersteuning voor essentiële toepassingen |Met StorSimple kunt u de juiste volumes identificeren die lokaal zijn vastgemaakt. Dit zorgt ervoor dat de gegevens die door essentiële toepassingen worden vereist, niet in de cloud worden gelaagd. Lokaal vastgemaakte volumes zijn niet onderhevig aan Cloud latenties of verbindings problemen. Zie [de StorSimple-Apparaatbeheer-service gebruiken voor het beheren van volumes](storsimple-8000-manage-volumes-u2.md)voor meer informatie over lokaal vastgemaakte volumes. |
| Lage latentie en hoge prestaties |U kunt Cloud apparaten maken die gebruikmaken van de functies van hoge prestaties en lage latentie van Azure Premium Storage. Zie [een StorSimple Cloud Appliance in azure implementeren en beheren](storsimple-8000-cloud-appliance-u2.md)voor meer informatie over StorSimple Premium-Cloud apparaten. |


## <a name="storsimple-components"></a>StorSimple-onderdelen
De Microsoft Azure StorSimple oplossing omvat de volgende onderdelen:

* **Microsoft Azure StorSimple-apparaat** : een on-premises hybride opslag matrix die Ssd's en hdd's bevat, samen met redundante controllers en automatische failover-mogelijkheden. De controllers beheren opslag lagen, op dit moment gebruikte (of dynamische) gegevens op lokale opslag plaatsen (op het apparaat of op on-premises servers), terwijl er minder vaak gebruikte gegevens naar de cloud worden verplaatst.
* **StorSimple Cloud Appliance** , ook wel bekend als het virtuele StorSimple, is dit een software versie van het StorSimple-apparaat dat de architectuur en de meeste mogelijkheden van het fysieke Hybrid Storage-apparaat repliceert. De StorSimple Cloud Appliance wordt uitgevoerd op één knoop punt in een virtuele Azure-machine. Premium virtuele apparaten, die profiteren van Azure Premium Storage, zijn beschikbaar in update 2 en hoger.
* **StorSimple Apparaatbeheer-service** : een uitbrei ding van de Azure Portal waarmee u een StorSimple-apparaat of StorSimple Cloud Appliance kunt beheren vanuit één webinterface. U kunt de StorSimple Apparaatbeheer-service gebruiken voor het maken en beheren van services, het weer geven en beheren van apparaten, het weer geven van waarschuwingen, het beheren van volumes en het weer geven en beheren van back-upbeleid en de back-catalogus.
* **Windows PowerShell voor StorSimple** : een opdracht regel interface die u kunt gebruiken om het StorSimple-apparaat te beheren. Windows PowerShell voor StorSimple beschikt over functies waarmee u uw StorSimple-apparaat kunt registreren, de netwerk interface op uw apparaat kunt configureren, bepaalde soorten updates kunt installeren, het apparaat moet oplossen door toegang te krijgen tot de ondersteunings sessie en de apparaatstatus te wijzigen. U hebt toegang tot Windows PowerShell voor StorSimple door verbinding te maken met de seriële console of door Windows Power shell Remoting te gebruiken.
* **Azure PowerShell StorSimple-cmdlets** : een verzameling Windows Power shell-cmdlets waarmee u taken op service niveau en migratie kunt automatiseren vanaf de opdracht regel. Ga naar de [cmdlet-verwijzing](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0&preserve-view=true#azure)voor meer informatie over de Azure PowerShell-cmdlets voor StorSimple.
* **StorSimple Snapshot Manager** : een MMC-module die gebruikmaakt van volume groepen en de Windows-Volume Shadow Copy service voor het genereren van toepassings consistente back-ups. Daarnaast kunt u StorSimple Snapshot Manager gebruiken om back-upschemas te maken en volumes te klonen of te herstellen.
* **StorSimple-adapter voor share point** : een hulp programma waarmee Microsoft Azure StorSimple opslag en gegevens beveiliging op transparante wijze kan worden uitgebreid naar share Point server-farms, terwijl StorSimple-opslag kan worden weer gegeven en beheerd vanuit de portal voor Centraal beheer van share point.

In het onderstaande diagram ziet u een weer gave op hoog niveau van de Microsoft Azure StorSimple architectuur en-onderdelen.

![StorSimple-architectuur](./media/storsimple-overview/overview-big-picture.png)

In de volgende secties wordt elk van deze onderdelen uitvoeriger beschreven en wordt uitgelegd hoe de oplossing gegevens rangschikt, opslag ruimte en opslag beheer en gegevens beveiliging vereenvoudigt. De laatste sectie bevat definities voor een aantal belang rijke termen en concepten met betrekking tot StorSimple-onderdelen en het beheer ervan.

## <a name="storsimple-device"></a>StorSimple-apparaat
Het Microsoft Azure StorSimple-apparaat is een on-premises hybride opslag matrix die primaire opslag-en iSCSI-toegang biedt tot gegevens die zijn opgeslagen. Het beheert communicatie met Cloud opslag en helpt de veiligheid en vertrouwelijkheid te garanderen van alle gegevens die zijn opgeslagen op de Microsoft Azure StorSimple oplossing.

Het StorSimple-apparaat bevat Ssd's en harde schijven (Hdd's), evenals ondersteuning voor clustering en automatische failover. Het bevat een gedeelde processor, gedeelde opslag en twee gespiegelde controllers. Elke controller biedt het volgende:

* Verbinding met een hostcomputer
* Maxi maal zes netwerk poorten om verbinding te maken met het LAN (Local Area Network)
* Hardware controleren
* Niet-vluchtig Random Access Memory (NVRAM), waar informatie wordt bewaard, zelfs als de stroom wordt onderbroken
* Cluster bewust updaten voor het beheren van software-updates op servers in een failovercluster, zodat de updates mini maal of niet van invloed zijn op de beschik baarheid van de service
* Clusterservice, die fungeert als een back-end-cluster en een hoge Beschik baarheid bieden en eventuele nadelige effecten minimaliseren die kunnen optreden als een HDD of SSD uitvalt of offline wordt gezet

Er is slechts één controller actief op een bepaald moment. Als de actieve controller mislukt, wordt de tweede controller automatisch actief.

Ga voor meer informatie naar [StorSimple-hardwareonderdelen en-status](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
U kunt StorSimple gebruiken om een Cloud apparaat te maken dat de architectuur en mogelijkheden van het fysieke Hybrid Storage-apparaat repliceert. De StorSimple Cloud Appliance (ook wel bekend als het virtuele StorSimple-apparaat) wordt uitgevoerd op één knoop punt in een virtuele Azure-machine. (Een Cloud apparaat kan alleen worden gemaakt op een virtuele Azure-machine. U kunt er geen maken op een StorSimple-apparaat of een on-premises server.)

Het Cloud apparaat heeft de volgende functies:

* Het gedraagt zich als een fysiek apparaat en kan een iSCSI-interface bieden voor virtuele machines in de Cloud.
* U kunt een onbeperkt aantal Cloud apparaten in de Cloud maken en deze in-of uitschakelen als dat nodig is.
* Dit kan helpen bij het simuleren van on-premises omgevingen in nood herstel, ontwikkelings-en test scenario's en kan helpen bij het ophalen op item niveau van back-ups.

De StorSimple Cloud Appliance is beschikbaar in twee modellen: het 8010-apparaat (voorheen bekend als het 1100-model) en het 8020-apparaat. Het 8010-apparaat heeft een maximale capaciteit van 30 TB. Het 8020-apparaat, dat gebruikmaakt van Azure Premium Storage, heeft een maximale capaciteit van 64 TB. (In lokale lagen slaat Azure Premium Storage gegevens op Ssd's terwijl standaard opslag gegevens opslaat op Hdd's.) U moet een Azure Premium Storage-account hebben voor het gebruik van Premium Storage.

Ga voor meer informatie over de StorSimple Cloud Appliance naar [een StorSimple Cloud Appliance in azure implementeren en beheren](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>StorSimple-apparaatbeheerfunctie
Microsoft Azure StorSimple biedt een webinterface (de StorSimple Apparaatbeheer-service) waarmee u Data Center-en Cloud opslag centraal kunt beheren. U kunt de StorSimple Apparaatbeheer-service gebruiken om de volgende taken uit te voeren:

* Systeem instellingen voor StorSimple-apparaten configureren.
* Beveiligings instellingen voor StorSimple-apparaten configureren en beheren.
* Cloud referenties en-eigenschappen configureren.
* Volumes op een server configureren en beheren.
* Configureer volume groepen.
* Back-ups maken en gegevens herstellen.
* Prestaties bewaken.
* Controleer de systeem instellingen en Identificeer mogelijke problemen.

U kunt de StorSimple-Apparaatbeheer-service gebruiken om alle beheer taken uit te voeren, behalve die waarvoor systeem tijd nodig is, zoals de eerste installatie en installatie van updates.

Ga voor meer informatie naar [Gebruik de StorSimple Apparaatbeheer-service om uw StorSimple-apparaat te beheren](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell voor StorSimple
Windows PowerShell voor StorSimple biedt een opdracht regel interface die u kunt gebruiken om de Microsoft Azure StorSimple-service te maken en te beheren en StorSimple-apparaten in te stellen en te controleren. Het is een Windows Power shell-opdracht regel interface die speciale cmdlets bevat voor het beheren van uw StorSimple-apparaat. Windows PowerShell voor StorSimple heeft functies waarmee u het volgende kunt doen:

* Een apparaat registreren.
* Configureer de netwerk interface op een apparaat.
* Bepaalde typen updates installeren.
* Problemen met uw apparaat oplossen door de ondersteunings sessie te openen.
* De status van het apparaat wijzigen.

U hebt toegang tot Windows PowerShell voor StorSimple vanaf een seriële console (op een hostcomputer die rechtstreeks op het apparaat is aangesloten) of op afstand met behulp van externe communicatie met Windows Power shell. Sommige Windows PowerShell voor StorSimple-taken, zoals initiële apparaatregistratie, kunnen alleen worden uitgevoerd op de seriële console.

Ga voor meer informatie naar [gebruik Windows PowerShell voor StorSimple om uw apparaat te beheren](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple-cmdlets
De Azure PowerShell StorSimple-cmdlets zijn een verzameling Windows Power shell-cmdlets waarmee u taken op service niveau en migratie kunt automatiseren vanaf de opdracht regel. Ga naar de [cmdlet-verwijzing](/powershell/module/servicemanagement/azure.service/?view=azuresmps-3.7.0&preserve-view=true)voor meer informatie over de Azure PowerShell-cmdlets voor StorSimple.

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager is een MMC-module (micro soft Management Console) die u kunt gebruiken voor het maken van consistente, tijdgebonden back-upkopieën van lokale en Cloud gegevens. De module wordt uitgevoerd op een Windows Server-host. U kunt StorSimple Snapshot Manager gebruiken voor het volgende:

* Volumes configureren, back-ups maken en verwijderen.
* Configureer volume groepen om ervoor te zorgen dat back-upgegevens toepassings consistent zijn.
* Beheer het back-upbeleid zodat er een back-up wordt gemaakt van gegevens op basis van een vooraf bepaald schema en dat deze zijn opgeslagen op een aangewezen locatie (lokaal of in de Cloud).
* Volumes en afzonderlijke bestanden herstellen.

Back-ups worden vastgelegd als moment opnamen, waarbij alleen de wijzigingen worden opgenomen sinds de laatste moment opname is gemaakt en zo weinig opslag ruimte nodig heeft dan de volledige back-ups. U kunt back-upscheman maken of onmiddellijk back-ups nemen. Daarnaast kunt u StorSimple-Snapshot Manager gebruiken om Bewaar beleid te bepalen dat bepaalt hoeveel moment opnamen worden opgeslagen. Als u later gegevens van een back-up wilt herstellen, kunt u in StorSimple Snapshot Manager selecteren uit de catalogus met lokale of Cloud momentopnamen. 

Als er sprake is van een nood geval of als u gegevens om een andere reden moet herstellen, StorSimple Snapshot Manager deze incrementeel opnieuw op te slaan wanneer dat nodig is. Voor het herstellen van gegevens is het niet nodig om het hele systeem uit te scha kelen tijdens het terugzetten van een bestand, het vervangen van apparatuur of het verplaatsen van bewerkingen naar een andere site.

Ga voor meer informatie naar [Wat is StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple Adapter voor SharePoint
Microsoft Azure StorSimple bevat de StorSimple-adapter voor share point, een optioneel onderdeel waarmee StorSimple opslag-en gegevens beveiligings functies op transparante wijze worden uitgebreid naar share Point-server farms. De adapter werkt met een externe Blob Storage (RBS)-provider en de SQL Server Resource structuur functie, zodat u BLOBs kunt verplaatsen naar een server waarvan een back-up wordt gemaakt door het Microsoft Azure StorSimple systeem. Microsoft Azure StorSimple de BLOB-gegevens vervolgens lokaal of in de Cloud opslaat, op basis van het gebruik.

De StorSimple-adapter voor share point wordt beheerd vanuit de portal voor Centraal beheer van share point. Share point-beheer blijft centraal en alle opslag lijkt in de share point-Farm te zijn.

Ga naar de [StorSimple-adapter voor share point](storsimple-adapter-for-sharepoint.md)voor meer informatie. 

## <a name="storage-management-technologies"></a>Technologieën voor opslag beheer
Naast het toegewezen StorSimple-apparaat, het virtuele apparaat en andere onderdelen, Microsoft Azure StorSimple gebruikt de volgende software technologieën om snelle toegang tot gegevens te bieden en om het opslag verbruik te verminderen:

* [Automatische opslag lagen](#automatic-storage-tiering) 
* [Thin provisioning](#thin-provisioning) 
* [Ontdubbeling en compressie](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatische opslag lagen
Met Microsoft Azure StorSimple worden gegevens in logische lagen automatisch gerangschikt op basis van het huidige gebruik, de leeftijd en de relatie met andere gegevens. Gegevens die het meest actief zijn, worden lokaal opgeslagen, terwijl minder actieve en niet-actieve gegevens automatisch naar de cloud worden gemigreerd. Het volgende diagram illustreert deze opslag benadering.

![StorSimple-opslag lagen](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Om snelle toegang in te scha kelen, slaat StorSimple zeer actieve gegevens (warme gegevens) op Ssd's op in het StorSimple-apparaat. Hiermee worden gegevens opgeslagen die af en toe worden gebruikt (warme gegevens) op Hdd's op het apparaat of op servers in het Data Center. Er worden inactieve gegevens, back-upgegevens en gegevens die voor archivering of naleving in de cloud worden bewaard, verplaatst. 

> [!NOTE]
> In update 2 of hoger kunt u een volume opgeven dat lokaal is vastgemaakt. in dat geval blijven de gegevens op het lokale apparaat en worden ze niet in de Cloud gelaagd. 


StorSimple past de gegevens-en opslag toewijzingen aan en herordent de wijzigingen in de gebruiks patronen. Zo kan een deel van de gegevens in de loop van de tijd minder actief worden. Omdat deze geleidelijk minder actief wordt, wordt deze gemigreerd van Ssd's naar Hdd's en vervolgens naar de Cloud. Als dezelfde gegevens weer actief worden, wordt deze teruggedraaid naar het opslag apparaat.

De opslag lagen worden als volgt uitgevoerd:

1. Een systeem beheerder stelt een Microsoft Azure Cloud-opslag account in.
2. De beheerder gebruikt de seriële console en de StorSimple-Apparaatbeheer service (die in de Azure Portal wordt uitgevoerd) om het apparaat en de bestands server te configureren, volumes en gegevens beveiligings beleid te maken. On-premises machines (zoals bestands servers) gebruiken de Internet Small Computer System Interface (iSCSI) om toegang te krijgen tot het StorSimple-apparaat.
3. In eerste instantie slaat StorSimple gegevens op in de laag Fast SSD van het apparaat.
4. Naarmate de SSD-laag de capaciteit aanpakt, wordt de StorSimple gedupliceerd en worden de oudste gegevens blokken gecomprimeerd en verplaatst naar de laag van de harde schijf.
5. Omdat de HDD-laag capaciteit aanpakt, versleutelt StorSimple de oudste gegevens blokken en verzendt deze veilig naar het opslag account van Microsoft Azure via HTTPS.
6. Microsoft Azure maakt meerdere replica's van de gegevens in het Data Center en in een extern Data Center, zodat de gegevens kunnen worden hersteld als er sprake is van een nood geval.
7. Wanneer de bestands Server gegevens aanvraagt die in de Cloud zijn opgeslagen, retourneert StorSimple naadloos en wordt een kopie opgeslagen in de laag SSD van het StorSimple-apparaat.

#### <a name="how-storsimple-manages-cloud-data"></a>Hoe StorSimple Cloud gegevens beheert

StorSimple ontdubbelt klant gegevens over alle moment opnamen en de primaire gegevens (gegevens die door hosts zijn geschreven). Hoewel ontdubbeling ideaal is voor opslag efficiëntie, wordt de vraag ' wat is in de cloud ' gecompliceerd. De gelaagde primaire gegevens en de momentopname gegevens overlappen elkaar. Eén deel van de gegevens in de cloud kan worden gebruikt als gelaagde primaire gegevens en er ook naar wordt verwezen door verschillende moment opnamen. Elke Cloud momentopname zorgt ervoor dat een kopie van alle tijdgebonden gegevens in de Cloud wordt vergrendeld totdat de moment opname wordt verwijderd.

Er worden alleen gegevens uit de Cloud verwijderd wanneer er geen verwijzingen naar die gegevens zijn. Als we bijvoorbeeld een Cloud momentopname maken van alle gegevens die zich op het StorSimple-apparaat bevinden en vervolgens enkele primaire gegevens verwijderen, zien we dat de _primaire gegevens_ onmiddellijk worden teruggehaald. De _gegevens_ van de Cloud, die de gelaagde gegevens en de back-ups bevatten, blijven hetzelfde. De reden hiervoor is dat er nog steeds naar de Cloud gegevens wordt verwezen met een moment opname. Nadat de moment opname van de Cloud is verwijderd (en een andere moment opname die naar dezelfde gegevens verwijst), wordt het Cloud verbruik neergezet. Voordat we Cloud gegevens verwijderen, wordt gecontroleerd of er nog geen moment opnamen zijn die naar de gegevens verwijzen. Dit proces heet _Garbage Collection_ en is een achtergrond service die op het apparaat wordt uitgevoerd. Het verwijderen van Cloud gegevens is niet direct omdat de garbagecollection-service voor andere verwijzingen naar die gegevens controleert vóór de verwijdering. De snelheid van de garbagecollection is afhankelijk van het totale aantal moment opnamen en de totale gegevens. Normaal gesp roken worden de Cloud gegevens in minder dan een week opgeruimd.


### <a name="thin-provisioning"></a>Thin provisioning
Thin Provisioning is een virtualisatiesoftware waarin beschik bare opslag ruimte groter is dan fysieke resources. In plaats van voldoende opslag vooraf te reserveren, gebruikt StorSimple Thin Provisioning om net zoveel ruimte toe te wijzen om te voldoen aan de huidige vereisten. De elastische aard van Cloud opslag vereenvoudigt deze benadering omdat StorSimple de Cloud opslag kan verg Roten of verkleinen om te voldoen aan veranderende vereisten.

> [!NOTE]
> Lokaal vastgemaakte volumes zijn niet dun ingericht. Opslag die is toegewezen aan een lokaal volume, wordt in zijn geheel ingericht wanneer het volume wordt gemaakt.


### <a name="deduplication-and-compression"></a>Ontdubbeling en compressie
Microsoft Azure StorSimple maakt gebruik van ontdubbeling en gegevens compressie om de opslag vereisten verder te reduceren.

Ontdubbeling vermindert de totale hoeveelheid gegevens die is opgeslagen door redundantie in de opgeslagen gegevensset te elimineren. Als de gegevens worden gewijzigd, worden de ongewijzigde gegevens door StorSimple genegeerd en worden alleen de wijzigingen vastgelegd. Daarnaast vermindert StorSimple de hoeveelheid opgeslagen gegevens door overbodige gegevens te identificeren en te verwijderen. 

> [!NOTE]
> Gegevens op lokaal vastgemaakte volumes worden niet ontdubbeld of gecomprimeerd. Back-ups van lokaal vastgemaakte volumes worden echter ontdubbeld en gecomprimeerd.


## <a name="storsimple-workload-summary"></a>Samen vatting van StorSimple-werk belasting
Hieronder vindt u een overzicht van de ondersteunde StorSimple-workloads.

| Scenario | Workload | Ondersteund | Beperkingen | Versie |
| --- | --- | --- | --- | --- |
| Samenwerking |Bestanden delen |Ja | |Alle versies |
| Samenwerking |Distributed File Sharing |Ja | |Alle versies |
| Samenwerking |SharePoint |Ja* |Alleen ondersteund met lokaal vastgemaakte volumes |Update 2 en hoger |
| Archivering |Eenvoudige bestands archivering |Ja | |Alle versies |
| Virtualisatie |Virtuele machines |Ja* |Alleen ondersteund met lokaal vastgemaakte volumes |Update 2 en hoger |
| Database |SQL |Ja* |Alleen ondersteund met lokaal vastgemaakte volumes |Update 2 en hoger |
| Videobewaking |Videobewaking |Ja* |Ondersteund wanneer het StorSimple-apparaat alleen aan deze werk belasting is toegewezen |Update 2 en hoger |
| Backup |Primaire doel back-up |Ja* |Ondersteund wanneer het StorSimple-apparaat alleen aan deze werk belasting is toegewezen |Update 3 en hoger |
| Backup |Secundaire doel back-up |Ja* |Ondersteund wanneer het StorSimple-apparaat alleen aan deze werk belasting is toegewezen |Update 3 en hoger |

*Ja&#42;: de richt lijnen en beperkingen van de oplossing moeten worden toegepast.*

De volgende werk belastingen worden niet ondersteund door apparaten uit de StorSimple 8000-serie. Als deze werk belastingen zijn geïmplementeerd op StorSimple, resulteert dit in een niet-ondersteunde configuratie.

* Medische installatie kopieën
* Exchange
* VDI
* Oracle
* SAP
* Big Data
* Inhouds distributie
* Opstarten vanaf SCSI

Hier volgt een lijst van de StorSimple-ondersteunde infrastructuur onderdelen.

| Scenario | Workload | Ondersteund | Beperkingen | Versie |
| --- | --- | --- | --- | --- |
| Algemeen |ExpressRoute |Ja | |Alle versies |
| Algemeen |DataCore FC |Ja* |Ondersteund met DataCore SANsymphony |Alle versies |
| Algemeen |DFSR |Ja* |Alleen ondersteund met lokaal vastgemaakte volumes |Alle versies |
| Algemeen |Indexeren |Ja* |Voor gelaagde volumes wordt alleen het indexeren van meta gegevens ondersteund (geen gegevens).<br>Voor lokaal vastgemaakte volumes wordt volledige indexering ondersteund. |Alle versies |
| Algemeen |Antivirus |Ja* |Voor gelaagde volumes moet alleen scannen bij openen en sluiten worden ondersteund.<br> Voor lokaal vastgemaakte volumes wordt volledige scan ondersteund. |Alle versies |

*Ja&#42;: de richt lijnen en beperkingen van de oplossing moeten worden toegepast.*

Hieronder volgt een lijst met andere software die met StorSimple wordt gebruikt om oplossingen te bouwen.

| Type werk belasting | Software die wordt gebruikt met StorSimple | Ondersteunde versies|Koppeling naar de oplossings handleiding| 
| --- | --- | --- | --- |
| Back-updoel |Veeam |Veeam v 9 en hoger |[StorSimple als een back-updoel met Veaam](storsimple-configure-backup-target-veeam.md)|
| Back-updoel |Veritas Backup Exec |Backup Exec 16 en hoger |[StorSimple als back-updoel met Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Back-updoel |VERITAS NetBackup |NetBackup 7,7. x en hoger  |[StorSimple als back-updoel met NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Global File Sharing <br></br> Samenwerking |Talon  |[StorSimple met Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple terminologie
Voordat u uw Microsoft Azure StorSimple-oplossing implementeert, wordt u aangeraden de volgende voor waarden en definities te bekijken.

### <a name="key-terms-and-definitions"></a>Belangrijkste termen en definities
| Term (acroniem of afkorting) | Beschrijving |
| --- | --- |
| Access Control Record (ACR) |Een record die is gekoppeld aan een volume op uw Microsoft Azure StorSimple-apparaat dat bepaalt welke hosts er verbinding mee kunnen maken. De bepaling is gebaseerd op de iSCSI Qualified Name (IQN) van de hosts (opgenomen in de ACR) die verbinding maken met uw StorSimple-apparaat. |
| AES-256 |Een 256-bits Advanced Encryption Standard (AES)-algoritme voor het versleutelen van gegevens tijdens het verplaatsen van en naar de Cloud. |
| Allocation Unit Size (AUS) |De kleinste hoeveelheid schijf ruimte die kan worden toegewezen voor het opslaan van een bestand in uw Windows-bestands systemen. Als de grootte van een bestand niet even veelvoud is van de cluster grootte, moet er extra ruimte worden gebruikt om het bestand (tot het volgende veelvoud van de cluster grootte te bewaren), wat resulteert in verloren ruimte en fragmentatie van de harde schijf. <br>De aanbevolen AUS voor Azure StorSimple-volumes is 64 KB omdat deze goed werkt met de ontdubbelings algoritmen. |
| automatische opslag lagen |Verplaats minder actieve gegevens automatisch van Ssd's naar Hdd's en vervolgens naar een laag in de Cloud en schakel vervolgens beheer van alle opslag vanuit een centrale gebruikers interface in. |
| back-upcatalogus |Een verzameling back-ups, meestal gerelateerd aan het gebruikte toepassings type. Deze verzameling wordt weer gegeven op de Blade back-upcatalogus van de StorSimple Apparaatbeheer Service-gebruikers interface. |
| back-upcatalogusbestand |Een bestand met een lijst met beschik bare moment opnamen die momenteel zijn opgeslagen in de back-updatabase van StorSimple Snapshot Manager. |
| back-upbeleid |Een selectie van volumes, type back-ups en een tijd schema waarmee u back-ups kunt maken op een vooraf gedefinieerd schema. |
| binaire, grote objecten (BLOBs) |Een verzameling binaire gegevens die zijn opgeslagen als één entiteit in een Database Management System. BLOBs zijn doorgaans afbeeldingen, audio of andere multi media-objecten, hoewel binaire uitvoer bare code wordt opgeslagen als een BLOB. |
| Challenge Handshake Authentication Protocol (CHAP) |Een protocol dat wordt gebruikt voor de verificatie van de peer van een verbinding, op basis van de peer die een wacht woord of geheim deelt. CHAP kan één richting of wederzijds zijn. Met eenrichtings-CHAP verifieert het doel een initiator. Wederzijdse CHAP vereist dat het doel de initiator verifieert en dat de initiator het doel verifieert. |
| clone |Een duplicaat kopie van een volume. |
| Cloud as a tier (CaaT) |Cloud opslag is geïntegreerd als een laag binnen de opslag architectuur, zodat alle opslag lijkt deel te uitmaken van één ondernemings opslag netwerk. |
| Cloud serviceprovider (CSP) |Een provider van Cloud Computing Services. |
| Cloud momentopname |Een tijdgebonden kopie van de volume gegevens die in de Cloud zijn opgeslagen. Een Cloud momentopname is gelijk aan een moment opname die wordt gerepliceerd op een ander, off-site opslag systeem. Cloud momentopnamen zijn bijzonder handig in scenario's voor herstel na nood gevallen. |
| versleutelings sleutel voor Cloud opslag |Een wacht woord of sleutel die door uw StorSimple-apparaat wordt gebruikt voor toegang tot de versleutelde gegevens die door uw apparaat worden verzonden naar de Cloud. |
| cluster bewust updaten |Het beheren van software-updates op servers in een failovercluster zodat de updates mini maal of niet van invloed zijn op de beschik baarheid van de service. |
| datapath |Een verzameling functionele eenheden die met elkaar verbonden gegevens verwerkings bewerkingen uitvoeren. |
| deactiveren |Een permanente actie waarmee de verbinding tussen het StorSimple-apparaat en de bijbehorende Cloud service wordt verbroken. Cloud momentopnamen van het apparaat blijven na dit proces en kunnen worden gekloond of worden gebruikt voor herstel na nood gevallen. |
| schijf spiegeling |Replicatie van logische schijf volumes op afzonderlijke harde schijven in realtime om continue Beschik baarheid te garanderen. |
| mirroring van dynamische schijven |Replicatie van logische schijf volumes op dynamische schijven. |
| dynamische schijven |Een schijf volume-indeling die gebruikmaakt van LDM (Logical Disk Manager) om gegevens op meerdere fysieke schijven op te slaan en te beheren. Dynamische schijven kunnen worden uitgebreid om meer ruimte vrij te maken. |
| EBOD-behuizing (uitgebreid aantal schijven) |Een secundaire behuizing van uw Microsoft Azure StorSimple-apparaat dat extra harde schijven bevat voor extra opslag. |
| Fat-inrichting |Een conventionele opslag inrichting waarbij opslag ruimte wordt toegewezen op basis van de verwachte behoeften (en meestal buiten de huidige behoefte). Zie ook *Thin Provisioning*. |
| harde schijf (HDD) |Een station dat gebruikmaakt van roterende Platters om gegevens op te slaan. |
| Hybrid Cloud Storage |Een opslag architectuur die gebruikmaakt van lokale en off-site bronnen, waaronder Cloud opslag. |
| ISCSI (Internet Small Computer System Interface) |Een op Internet Protocol (IP) gebaseerde opslag netwerk standaard voor het koppelen van apparaten of faciliteiten voor gegevens opslag. |
| iSCSI-initiator |Een software component waarmee een hostcomputer met Windows verbinding kan maken met een extern op iSCSI gebaseerd opslag netwerk. |
| iSCSI Qualified Name (IQN) |Een unieke naam die een iSCSI-doel of initiator aanduidt. |
| iSCSI-doel |Een software onderdeel dat gecentraliseerde iSCSI-schijf subsystemen in Storage Area Networks biedt. |
| Live archiveren |Een opslag benadering waarin archiverings gegevens altijd toegankelijk zijn (deze is bijvoorbeeld niet op tape opgeslagen). Microsoft Azure StorSimple maakt gebruik van Live archiveren. |
| lokaal vastgemaakt volume |een volume dat zich op het apparaat bevindt en nooit is gelaagd in de Cloud. |
| lokale moment opname |Een tijdgebonden kopie van de volume gegevens die op het Microsoft Azure StorSimple apparaat is opgeslagen. |
| Microsoft Azure StorSimple |Een krachtige oplossing die bestaat uit een opslag apparaat en software voor data centers waarmee IT-organisaties Cloud opslag kunnen gebruiken alsof ze Data Center-opslag waren. StorSimple vereenvoudigt gegevens bescherming en gegevens beheer, terwijl de kosten worden verminderd. De oplossing consolideert primaire opslag, archivering, back-up en herstel na nood gevallen (DR) via naadloze integratie met de Cloud. Door SAN-opslag en Cloud gegevens beheer te combi neren op een platform op bedrijfs niveau, kunnen StorSimple-apparaten sneller, eenvoudig en betrouwbaar maken voor alle aan opslag gerelateerde behoeften. |
| Module voor voeding en koeling (PCM) |Hardwareonderdelen van uw StorSimple-apparaat, bestaande uit de voedingen en de koel ventilator; Daarom is de module name voeding en koeling. De primaire behuizing van het apparaat heeft twee 764W-PCMs, terwijl de EBOD Enclosure twee 580W PCMs heeft. |
| primaire behuizing |Hoofd behuizing van uw StorSimple-apparaat dat de toepassings platform controllers bevat. |
| beoogde herstel tijd (RTO) |De maximale hoeveelheid tijd die moet worden vrijgesteld voordat een bedrijfs proces of systeem volledig wordt hersteld na een nood geval. |
| Serial Attached SCSI (SAS) |Een type harde schijf (HDD). |
| versleutelings sleutel voor service gegevens |Een sleutel die beschikbaar is gemaakt voor een nieuw StorSimple-apparaat dat wordt geregistreerd bij de StorSimple-Apparaatbeheer service. De configuratie gegevens die worden overgebracht tussen de StorSimple-Apparaatbeheer service en het apparaat zijn versleuteld met een open bare sleutel en kunnen vervolgens alleen worden ontsleuteld op het apparaat met behulp van een persoonlijke sleutel. Met de versleutelings sleutel voor service gegevens kan de service deze persoonlijke sleutel voor ontsleuteling ophalen. |
| Service registratie sleutel |Een sleutel die helpt bij het registreren van het StorSimple-apparaat bij de StorSimple Apparaatbeheer-service, zodat deze wordt weer gegeven in de Azure Portal voor verdere beheer acties. |
| SCSI (Small Computer System Interface) |Een set normen voor het fysiek verbinden van computers en het door geven van gegevens ertussen. |
| Solid-State Drive (SSD) |Een schijf die geen bewegende onderdelen bevat; bijvoorbeeld een flash station. |
| opslagaccount |Een reeks toegangs referenties die zijn gekoppeld aan uw opslag account voor een bepaalde Cloud serviceprovider. |
| StorSimple Adapter voor SharePoint |Een Microsoft Azure StorSimple onderdeel dat StorSimple opslag en gegevens bescherming transparant uitbreidt tot share Point-server farms. |
| StorSimple-apparaatbeheerfunctie |Een uitbrei ding van de Azure Portal waarmee u uw Azure StorSimple on-premises en virtuele apparaten kunt beheren. |
| StorSimple Snapshot Manager |Een MMC-module (micro soft Management Console) voor het beheren van back-up-en herstel bewerkingen in Microsoft Azure StorSimple. |
| back-up maken |Een functie waarmee de gebruiker een interactieve back-up van een volume kan maken. Het is een alternatieve manier om een hand matige back-up te maken van een volume, in tegens telling tot het maken van een automatische back-up via een gedefinieerd beleid. |
| Thin Provisioning |Een methode voor het optimaliseren van de efficiëntie waarmee de beschik bare opslag ruimte wordt gebruikt in opslag systemen. Bij Thin Provisioning wordt de opslag toegewezen aan meerdere gebruikers op basis van de minimale ruimte die elke gebruiker op een bepaald moment nodig heeft. Zie ook *FAT-inrichting*. |
| lagen |Gegevens in logische groeperingen rangschikken op basis van huidige gebruik, leeftijd en relatie met andere gegevens. StorSimple rangschikt gegevens automatisch in lagen. |
| volume |Logische opslag gebieden worden weer gegeven in de vorm van stations. StorSimple-volumes komen overeen met de volumes die zijn gekoppeld door de host, met inbegrip van de volumes die zijn gedetecteerd via het gebruik van iSCSI en een StorSimple-apparaat. |
| volume container |Een groep volumes en de instellingen die hierop van toepassing zijn. Alle volumes op uw StorSimple-apparaat zijn gegroepeerd in volume containers. De instellingen voor de volume container omvatten opslag accounts, versleutelings instellingen voor gegevens die worden verzonden naar de Cloud met gekoppelde versleutelings sleutels en band breedte die wordt gebruikt voor bewerkingen met betrekking tot de Cloud. |
| volume groep |In StorSimple Snapshot Manager is een volume groep een verzameling van volumes die zijn geconfigureerd om de back-upverwerking te vergemakkelijken. |
| Volume Shadow Copy Service (VSS) |Een Windows Server-besturingssysteem service die de consistentie van toepassingen vereenvoudigt door te communiceren met VSS-bewuste toepassingen om het maken van incrementele moment opnamen te coördineren. VSS zorgt ervoor dat de toepassingen tijdelijk inactief zijn wanneer moment opnamen worden gemaakt. |
| Windows PowerShell voor StorSimple |Een Windows Power shell-opdracht regel interface die wordt gebruikt voor het uitvoeren en beheren van uw StorSimple-apparaat. Met het behoud van enkele van de basis mogelijkheden van Windows Power Shell heeft deze interface extra specifieke cmdlets die zijn gericht op het beheer van een StorSimple-apparaat. |

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [StorSimple-beveiliging](storsimple-8000-security.md).