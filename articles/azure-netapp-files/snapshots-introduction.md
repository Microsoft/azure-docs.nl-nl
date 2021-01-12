---
title: Hoe Azure NetApp Files moment opnamen werken | Microsoft Docs
description: Legt uit hoe Azure NetApp Files moment opnamen werken, met inbegrip van manieren om moment opnamen te maken, manieren om moment opnamen te herstellen, moment opnamen te gebruiken in replicatie-instellingen voor meerdere regio's.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/11/2021
ms.author: b-juche
ms.openlocfilehash: 4d21f7c4e74a87e409a73b22fc6b316e97e24a4e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122363"
---
# <a name="how-azure-netapp-files-snapshots-work"></a>Hoe Azure NetApp Files moment opnamen werken?

In dit artikel wordt uitgelegd hoe Azure NetApp Files moment opnamen werken. Azure NetApp Files snap shot-technologie levert stabiliteit, schaal baarheid en snellere herstel mogelijkheden, zonder dat dit van invloed is op de prestaties. Azure NetApp Files snap shot-technologie biedt de basis voor oplossingen voor gegevens beveiliging, waaronder het terugzetten van één bestand, het herstellen van volumes en klonen en replicatie tussen regio's. 

Zie [moment opnamen beheren met Azure NetApp files](azure-netapp-files-manage-snapshots.md)voor meer informatie over het gebruik van moment opnamen van volumes. Zie [vereisten en overwegingen voor het gebruik van replicatie tussen regio's](cross-region-replication-requirements-considerations.md)voor overwegingen met betrekking tot het beheer van moment opnamen in replicatie tussen regio's.

## <a name="what-volume-snapshots-are"></a>Welke volume momentopnamen worden  

Een moment opname van een Azure NetApp Files is een tijdgebonden bestandssysteem installatie kopie (volume System). Dit kan fungeren als ideale online back-up. U kunt een moment opname gebruiken om [een nieuw volume te maken](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume), [een bestand te herstellen](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client)of [een volume terug](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert)te zetten. In specifieke toepassings gegevens die zijn opgeslagen op Azure NetApp Files volumes, zijn mogelijk extra stappen vereist om de consistentie van toepassingen te garanderen.  

Moment opnamen met een lage overhead worden mogelijk gemaakt door de unieke functies van de volume Virtualization-technologie die deel uitmaakt van Azure NetApp Files. Net als bij een data base gebruikt deze laag verwijzingen naar de daad werkelijke gegevens blokken op de schijf. Maar in tegens telling tot een Data Base worden bestaande blokken niet opnieuw geschreven. het schrijft bijgewerkte gegevens naar een nieuw blok en wijzigt de aanwijzer. Met een moment opname van een Azure NetApp Files worden blok verwijzingen gewoon gemanipuleerd, wordt een ' bevroren ', alleen-lezen weer gave van een volume gemaakt waarmee toepassingen toegang hebben tot oudere versies van bestanden en Directory-hiërarchieën zonder speciale Program ma's. De werkelijke gegevens blokken worden niet gekopieerd. Als zodanig zijn moment opnamen efficiënt in de tijd die nodig is om ze te maken. ze zijn bijna onmiddellijk, ongeacht de grootte van het volume. Moment opnamen zijn ook efficiënt in opslag ruimte. Ze verbruiken zelf geen ruimte en alleen Delta blokken tussen moment opnamen en actief volume worden bewaard. 

De volgende diagrammen illustreren de concepten:  

![Diagrammen waarin de belangrijkste concepten van moment opnamen worden weer gegeven](../media/azure-netapp-files/snapshot-concepts.png)

In de bovenstaande diagrammen wordt een moment opname gemaakt in afbeelding 1a. In afbeelding 1b worden gewijzigde gegevens naar een *nieuw blok* geschreven en wordt de aanwijzer bijgewerkt. De snap shot-pointer wijst echter wel naar het *eerder geschreven blok*, waardoor u een live en een historisch overzicht van de gegevens krijgt. In afbeelding 1C wordt een andere moment opname gemaakt. U hebt nu toegang tot drie generaties gegevens (de dynamische gegevens, moment opname 2 en moment opnamen 1, in volg orde van leeftijd), zonder de volume ruimte te nemen die drie volledige kopieën nodig zouden hebben. 

Een moment opname heeft alleen een kopie van de meta gegevens van het volume (*inode-tabel*). Het duurt slechts enkele seconden om te maken, ongeacht de grootte van het volume, de gebruikte capaciteit of het niveau van de activiteit op het volume. Het maken van een moment opname van een 100-volume duurt dus hetzelfde (na nul) wanneer een moment opname van een 100-GiB-volume wordt gemaakt. Nadat een moment opname is gemaakt, worden wijzigingen in gegevens bestanden weer gegeven in de actieve versie van de bestanden, zoals normaal.

Ondertussen blijven de gegevens blokken die naar van een moment opname verwijzen, stabiel en onveranderbaar. Vanwege de aard ' omleiden bij schrijven ' van Azure NetApp Files moment opnamen, maakt een moment opname geen gebruik van eventuele ruimte op de prestatie overhead. U kunt Maxi maal 255 moment opnamen per volume in de loop van de tijd opslaan, die allemaal toegankelijk zijn als alleen-lezen en online versies van de gegevens, waarbij net zo weinig capaciteit wordt verbruikt als het aantal gewijzigde blokken tussen elke moment opname. Gewijzigde blokken worden opgeslagen in het actieve volume. Blokken naar in moment opnamen worden bewaard (als alleen-lezen) in het volume voor het bewaren van Bewaar doeleinden, zodat ze alleen kunnen worden gewijzigd wanneer alle moment opnamen (aanwijzers) zijn gewist. Daarom neemt het volume gebruik toe gedurende een periode, omdat er nieuwe gegevens blokken of (gewijzigde) gegevens blokken worden bewaard in moment opnamen.

 In het volgende diagram ziet u de moment opnamen van een volume en gebruikte ruimte gedurende een bepaalde periode: 

![Diagram waarin de moment opnamen van een volume en de gebruikte ruimte gedurende een periode worden weer gegeven](../media/azure-netapp-files/snapshots-used-space-over-time.png)

Omdat een volume momentopname alleen de wijzigingen van het blok heeft geregistreerd sinds de laatste moment opname, biedt dit de volgende belang rijke voor delen:

* Moment opnamen zijn ***opslag-efficiënt** _.   
    Moment opnamen nemen minimale opslag ruimte in beslag omdat hiermee niet de gegevens blokken van het hele volume worden gekopieerd. Twee moment opnamen die in volg orde zijn genomen, verschillen alleen door de blokken die zijn toegevoegd of gewijzigd in het tijds interval tussen de twee. Dit blok-incrementeel gedrag beperkt het gekoppelde opslag capaciteits verbruik. Veel alternatieve implementaties van moment opnamen maken gebruik van opslag volumes die gelijk zijn aan het actieve bestands systeem, waarbij de vereisten voor de opslag capaciteit worden verhoogd. Afhankelijk van de dagelijkse wijzigings tarieven van de toepassing _block-niveau worden er Azure NetApp Files moment opnamen meer of minder capaciteit verbruikt, maar alleen voor gewijzigde gegevens. Gemiddeld dagelijks moment opnamen zijn alleen van 1-5% van de gebruikte volume capaciteit voor veel toepassings volumes, of tot 20-30% voor volumes, zoals SAP HANA database volumes. Zorg ervoor dat u [uw volume-en momentopname gebruik bewaakt](azure-netapp-files-metrics.md#volumes) voor het capaciteits verbruik van de moment opname ten opzichte van het aantal gemaakte en beheerde moment opnamen.   

* Moment opnamen zijn ***snel om _ te maken, te repliceren, te herstellen of te klonen**.   
    Het duurt slechts enkele seconden om een moment opname te maken, te repliceren, te herstellen of te klonen, ongeacht de grootte van het volume en het niveau van de activiteiten. U kunt [op aanvraag](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume)een moment opname van een volume maken. U kunt ook een [momentopname beleid](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) gebruiken om op te geven wanneer Azure NetApp files automatisch een moment opname moet maken en hoeveel moment opnamen voor een volume moeten worden bewaard.  Toepassings consistentie kan worden bereikt door moment opnamen te organiseren met de toepassingslaag, bijvoorbeeld met behulp van het [hulp programma AzAcSnap](azacsnap-introduction.md) voor SAP Hana.

_ Moment opnamen hebben geen invloed op het volume ***prestaties** _.   
    Vanwege het ' omleiden van de aard van de onderhouds technologie, het opslaan of bewaren van Azure NetApp Files moment opnamen heeft geen invloed op de prestaties, zelfs met een zware gegevens activiteit. Het verwijderen van een moment opname heeft in veel gevallen ook weinig gevolgen voor de prestaties. 

_ Moment opnamen bieden ***schaal baarheid** _ omdat ze regel matig kunnen worden gemaakt en veel kunnen worden bewaard.   
    Azure NetApp Files-volumes bieden ondersteuning voor Maxi maal 255 moment opnamen. De mogelijkheid om een groot aantal, vaak gemaakte moment opnamen met weinig effect op te slaan, verhoogt de kans dat de gewenste versie van gegevens correct kan worden hersteld.

_ Moment opnamen bieden * de **zicht baarheid** van de gebruiker en bestanden kunnen worden _*_hersteld_*_.   
De hoge prestaties, schaal baarheid en stabiliteit van Azure NetApp Files snap shot-technologie betekent dat het een ideale online back-up voor door de gebruiker gestuurde herstel mogelijkheden biedt. U kunt moment opnamen van gebruikers toegankelijk maken voor bestanden, mappen of volume herstel doeleinden. Met extra oplossingen kunt u back-ups kopiëren naar offline opslag of [meerdere regio's repliceren](cross-region-replication-introduction.md) voor retentie of herstel na nood gevallen.

## <a name="ways-to-create-snapshots"></a>Manieren om moment opnamen te maken   

Azure NetApp Files moment opnamen zijn veelzijdig in gebruik. Zo zijn er meerdere methoden beschikbaar voor het maken en onderhouden van moment opnamen:

_ Hand matig (op aanvraag) door gebruik te maken van:   
    * De [Azure Portal](azure-netapp-files-manage-snapshots.md#create-an-on-demand-snapshot-for-a-volume), [rest API](/rest/api/netapp/snapshots), [Azure cli](/cli/azure/netappfiles/snapshot)of [Power shell](/powershell/module/az.netappfiles/new-aznetappfilessnapshot) -hulpprogram ma's
    * Scripts (Zie [voor beelden](azure-netapp-files-solution-architectures.md#sap-tech-community-and-blog-posts))

* Geautomatiseerd, met behulp van:
    * Snap shot-beleids regels, via de [Azure Portal](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies), [rest API](/rest/api/netapp/snapshotpolicies), [Azure cli](/cli/azure/netappfiles/snapshot/policy)of [Power shell](/powershell/module/az.netappfiles/new-aznetappfilessnapshotpolicy) -hulpprogram ma's
    * Toepassings consistente momentopname tool, zoals [AzAcSnap](azacsnap-introduction.md)

## <a name="how-volumes-and-snapshots-are-replicated-cross-region-for-dr"></a>Hoe volumes en moment opnamen worden gerepliceerd tussen regio's voor DR  

Azure NetApp Files ondersteunt [replicatie tussen regio's](cross-region-replication-introduction.md) voor nood herstel (Dr). Azure NetApp Files replicatie tussen regio's maakt gebruik van de SnapMirror-technologie. Alleen gewijzigde blokken worden via het netwerk verzonden met een gecomprimeerde, efficiënte indeling. Nadat een replicatie tussen de verschillende regio's tussen de volumes is gestart, worden de gehele volume-inhoud (dat wil zeggen, de daad werkelijke opgeslagen gegevens blokken) slechts één keer overgedragen. Deze bewerking wordt een *basislijn overdracht* genoemd. Na de eerste overdracht worden alleen gewijzigde blokken (zoals vastgelegd in moment opnamen) overgedragen. Er wordt een asynchrone 1:1-replica van het bron volume gemaakt (met inbegrip van alle moment opnamen).  Dit gedrag volgt een volledig en incrementeel replicatie mechanisme. Deze bedrijfseigen technologie minimaliseert de hoeveelheid gegevens die nodig zijn replicatie tussen regio's uit te voeren, waardoor kosten voor gegevensoverdracht worden bespaard. Ook wordt de replicatie tijd verkort. U kunt een kleinere RPO (Recovery Point objectief) bewezenlijken, omdat er meer moment opnamen kunnen worden gemaakt en vaker worden overgedragen met beperkte gegevens overdrachten.

Het volgende diagram toont het verkeer van de moment opname in scenario's voor replicatie tussen regio's: 

![Diagram waarin momentopname verkeer wordt weer gegeven in scenario's voor replicatie tussen regio's](../media/azure-netapp-files/snapshot-traffic-cross-region-replication.png)

## <a name="ways-to-restore-data-from-snapshots"></a>Manieren om gegevens van moment opnamen te herstellen  

De Azure NetApp Files snap shot-technologie verbetert de frequentie en de betrouw baarheid van back-ups aanzienlijk. Het leidt tot minimale prestatie overhead en kan veilig worden gemaakt op een actief volume. Azure NetApp Files moment opnamen bieden vrijwel onmiddellijk, veilig en optioneel door de gebruiker beheerde herstel bewerkingen. In deze sectie worden verschillende manieren beschreven waarop gegevens kunnen worden geopend of teruggezet vanuit Azure NetApp Files moment opnamen.

### <a name="restoring-files-or-directories-from-snapshots"></a>Bestanden of mappen terugzetten vanuit moment opnamen 

Als [zicht baarheid van moment opnamen](azure-netapp-files-manage-snapshots.md#edit-the-hide-snapshot-path-option) niet is verborgen, kunnen gebruikers rechtstreeks toegang krijgen tot moment opnamen om ze te herstellen tegen onbedoeld verwijderen, beschadiging of wijziging van hun gegevens. De beveiliging van bestanden en mappen wordt in de moment opname bewaard en moment opnamen zijn alleen-lezen. Als zodanig is de herstel bewerking veilig en eenvoudig. 

In het volgende diagram wordt weer gegeven dat het bestand of de map toegang heeft tot een moment opname: 

![Diagram waarin bestands-of Directory toegang tot een moment opname wordt weer gegeven](../media/azure-netapp-files/snapshot-file-directory-access.png)

In het diagram gebruikt moment opname 1 alleen de Delta blokken tussen het actieve volume en het moment waarop de moment opname wordt gemaakt. Maar wanneer u de moment opname opent via het pad naar de moment opname van het volume, worden de gegevens *weer gegeven* alsof het de volledige capaciteit van het volume is op het moment dat de moment opname wordt gemaakt. Door toegang te krijgen tot de mappen met moment opnamen kunnen gebruikers zelf gegevens herstellen door bestanden en mappen uit een moment opname van de keuze te kopiëren.

Op dezelfde manier kunnen moment opnamen in doel-replicatie volumes voor meerdere regio's alleen-lezen worden geopend voor gegevens herstel in de DR-regio.  

In het volgende diagram ziet u de toegang van moment opnamen in scenario's voor replicatie tussen regio's: 

![Diagram waarin de toegang tot de moment opname wordt weer gegeven in replicatie tussen regio's](../media/azure-netapp-files/snapshot-access-cross-region-replication.png)

Zie [een bestand herstellen vanuit een moment opname met een client over het](azure-netapp-files-manage-snapshots.md#restore-a-file-from-a-snapshot-using-a-client) herstellen van afzonderlijke bestanden of mappen van moment opnamen.

### <a name="restoring-cloning-a-snapshot-to-a-new-volume"></a>Een moment opname terugzetten (klonen) naar een nieuw volume

Azure NetApp Files moment opnamen kunnen worden hersteld naar een afzonderlijk, onafhankelijk volume. Deze bewerking bevindt zich bijna onmiddellijk, ongeacht de grootte van het volume en de verbruikte capaciteit. Het zojuist gemaakte volume is bijna onmiddellijk beschikbaar voor toegang, terwijl de werkelijke volume-en momentopname gegevens blokken worden gekopieerd. Afhankelijk van de grootte en capaciteit van het volume kan dit proces veel tijd in beslag nemen, waardoor het bovenliggende volume en de moment opname niet kunnen worden verwijderd. Het volume kan echter al worden geopend na het maken van de eerste keer dat het kopieer proces op de achtergrond wordt uitgevoerd. Met deze mogelijkheid maakt u snel een volume voor het maken van gegevens herstel of het klonen van volumes voor testen en ontwikkeling. Op basis van het proces van het kopiëren van gegevens wordt het verbruik van de opslag capaciteit verdubbeld wanneer de herstel bewerking is voltooid. het nieuwe volume toont de volledige actieve capaciteit van de oorspronkelijke moment opname. Nadat dit proces is voltooid, is het volume onafhankelijk en niet gekoppeld aan het oorspronkelijke volume en kunnen bron volumes en moment opnamen onafhankelijk van het nieuwe volume worden beheerd of verwijderd.

In het volgende diagram ziet u een nieuw volume dat is gemaakt door een moment opname te herstellen (te klonen):   

![Diagram waarin een nieuw volume wordt weer gegeven dat is gemaakt door een moment opname te herstellen](../media/azure-netapp-files/snapshot-restore-clone-new-volume.png)

Dezelfde bewerkingen kunnen worden uitgevoerd op gerepliceerde moment opnamen naar een nood herstel volume (DR). Elke moment opname kan worden teruggezet naar een nieuw volume, zelfs wanneer replicatie tussen regio's actief of in behandeling blijft. Deze mogelijkheid maakt het niet-storende maken van test-en ontwikkelings omgevingen in een DR-regio mogelijk, waarbij de gegevens worden gebruikt, terwijl de gerepliceerde volumes anders alleen worden gebruikt voor DR-doel einden. Met deze use-case kunnen test-en ontwikkelings omgevingen worden geïsoleerd van productie, waardoor er geen gevolgen zijn voor de productie omgeving.  

In het volgende diagram ziet u het herstellen van volumes (klonen) met behulp van de moment opname van DR-doel volumes, terwijl replicatie tussen regio's wordt uitgevoerd:  

![Diagram van het herstellen van het volume met behulp van de moment opname van DR-doel volumes](../media/azure-netapp-files/snapshot-restore-clone-target-volume.png)

Zie [een moment opname herstellen naar een nieuw volume](azure-netapp-files-manage-snapshots.md#restore-a-snapshot-to-a-new-volume) over het terugzetten van volume herstel bewerkingen.

### <a name="restoring-reverting-a-snapshot-in-place"></a>Een moment opname op locatie terugzetten (herstellen)

In sommige gevallen, omdat het nieuwe volume de opslag capaciteit gebruikt, is het maken van een nieuw volume van een moment opname mogelijk niet nodig of niet nodig. Als u gegevens beschadiging wilt herstellen (bijvoorbeeld beschadigingen van de data base of Ransomware-aanvallen), is het wellicht beter om een moment opname te herstellen binnen het volume zelf. Deze bewerking kan worden uitgevoerd met behulp van de functie voor het terugzetten van Azure NetApp Files moment opnamen. Deze functie stelt u in staat om snel een volume terug te zetten naar de status waarin deze zich bevond toen een bepaalde moment opname werd gemaakt. In de meeste gevallen is het herstellen van een volume veel sneller dan het herstellen van afzonderlijke bestanden van een moment opname naar het actieve bestands systeem, met name op grote volumes met meerdere TiB. 

Het terugdraaien van een volume momentopname is bijna onmiddellijk en duurt slechts enkele seconden, zelfs voor de grootste volumes. De meta gegevens van het actieve volume (*inode-tabel*) worden vervangen door de meta gegevens van de moment opname van de moment opname, waardoor het volume wordt teruggedraaid naar het specifieke punt in de tijd. Er hoeven geen gegevens blokken te worden gekopieerd om de wijziging door te voeren. Daarom is het meer ruimte efficiënt dan het herstellen van een moment opname naar een nieuw volume. 

In het volgende diagram ziet u een volume terug naar een eerdere moment opname:  

![Diagram waarin een volume wordt weer gegeven dat wordt teruggezet naar een eerdere moment opname](../media/azure-netapp-files/snapshot-volume-revert.png)

> [!IMPORTANT]
> Actieve bestandssysteem gegevens die zijn geschreven en moment opnamen die zijn gemaakt nadat de geselecteerde moment opname is gemaakt, gaan verloren. Met de bewerking voor het terugdraaien van moment opnamen worden alle gegevens in het doel volume vervangen door de gegevens in de geselecteerde moment opname. U moet aandacht best Eden aan de inhoud van de moment opname en de aanmaak datum wanneer u een moment opname selecteert. U kunt de bewerking voor het terugdraaien van moment opnamen niet ongedaan maken.  

Zie [een volume herstellen met behulp van moment opnamen reverteren](azure-netapp-files-manage-snapshots.md#revert-a-volume-using-snapshot-revert) over het gebruik van deze functie.

## <a name="how-snapshots-are-deleted"></a>Hoe moment opnamen worden verwijderd 

Moment opnamen gebruiken de opslag capaciteit. Daarom worden ze doorgaans niet voor onbepaalde tijd bewaard. Voor gegevens bescherming, retentie en herstel baarheid wordt een aantal moment opnamen (die op verschillende tijdstippen zijn gemaakt) doorgaans online gehouden voor een bepaalde duur, afhankelijk van de RPO-, RTO-en bewaar vereisten. Oudere moment opnamen hoeven echter vaak niet op de opslag service te worden bewaard en moeten mogelijk worden verwijderd om ruimte vrij te maken. Een moment opname kan op elk gewenst moment worden verwijderd (niet noodzakelijkerwijs om te worden gemaakt) door een beheerder. 

> [!IMPORTANT]
> De bewerking voor het verwijderen van de moment opname kan niet ongedaan worden gemaakt. 

Wanneer een moment opname wordt verwijderd, worden alle verwijzingen van die moment opname naar bestaande gegevens blokken verwijderd. Wanneer een gegevens blok geen aanwijzers meer aanwijst (door het actieve volume of andere moment opnamen in het volume), wordt het gegevens blok teruggestuurd naar de vrije ruimte op het volume voor toekomstig gebruik. Daarom vrijmaakt moment opnamen meestal meer capaciteit op een volume dan het verwijderen van gegevens van het actieve volume, omdat gegevens blokken vaak worden vastgelegd in eerder gemaakte moment opnamen. 

In het volgende diagram ziet u het effect van het opslag verbruik van moment opnamen verwijderen voor een volume:  

![Diagram dat het effect van het opslag verbruik van moment opnamen verwijderen weergeeft](../media/azure-netapp-files/snapshot-delete-storage-consumption.png)

Zorg ervoor dat u het [verbruik van volumes en moment opnamen bewaakt](azure-netapp-files-metrics.md#volumes) en weet hoe de toepassing, het actieve volume en het momentopname gebruik ermee kunnen werken. 

Zie [moment opnamen verwijderen](azure-netapp-files-manage-snapshots.md#delete-snapshots) over hoe u het verwijderen van moment opnamen kunt beheren. Zie [snap shot policies beheren](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies) over het automatiseren van dit proces.

## <a name="next-steps"></a>Volgende stappen

* [Momentopnamen beheren met behulp van Azure NetApp Files](azure-netapp-files-manage-snapshots.md)
* [Metrische gegevens van volumes en moment opnamen bewaken](azure-netapp-files-metrics.md#volumes)
* [Problemen met momentopnamebeleid oplossen](troubleshoot-snapshot-policies.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Video over Azure NetApp Files-moment opnamen 101](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [NetApp-moment opname-NetApp-video bibliotheek](https://tv.netapp.com/detail/video/2579133646001/snapshot)



