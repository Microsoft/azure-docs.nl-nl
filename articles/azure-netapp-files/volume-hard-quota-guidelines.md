---
title: Wat wordt overgeschakeld naar het volume vaste quotum voor uw Azure NetApp Files-service | Microsoft Docs
description: Beschrijft de wijziging van het gebruik van vaste quota voor volumes, het plannen van de wijziging en het bewaken en beheren van capaciteit.
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
ms.date: 02/05/2021
ms.author: b-juche
ms.openlocfilehash: 12807e83f7841bc67999ce385d0cb82bf15f4c71
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175988"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Wat wordt overgeschakeld naar het volume vaste quotum voor uw Azure NetApp Files-service

Vanaf het begin van de service heeft Azure NetApp Files een capaciteits pool inrichten en een mechanisme voor automatische groei gebruikt. Azure NetApp Files volumes worden dun ingericht op een door de klant ingerichte capaciteits groep van een geselecteerde laag en grootte. Volume grootten (quota's) worden gebruikt voor prestaties en capaciteit en de quota's kunnen op elk gewenst moment worden aangepast. Dit gedrag houdt in dat het volume quotum op dit moment een prestatie hendel is die wordt gebruikt om de band breedte naar het volume te beheren. Op dit moment groeien de capaciteits Pools automatisch wanneer de capaciteit omhoog wordt gevuld.   

> [!IMPORTANT] 
> Het Azure NetApp Files gedrag van het inrichten van het volume en de capaciteits pool wordt gewijzigd in een *hand matig* *en te* bepalen mechanisme. **Vanaf 1 april 2021 (bijgewerkt) worden de bandbreedte prestaties, evenals de ingerichte capaciteit en de onderliggende capaciteits groepen, niet meer automatisch uitgebreid wanneer de volume grootten (quotum) worden beheerd.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Redenen voor de wijziging van het volume vaste quotum

Veel klanten hebben drie grote uitdagingen gesignaleerd met het *oorspronkelijke* gedrag:
* VM-clients zien de TiB-capaciteit 100 (Thin provisioned) van een bepaald volume bij gebruik van besturingssysteem ruimte of hulpprogram ma's voor het controleren van de capaciteit, waardoor de zicht baarheid van de client of toepassings zijde onnauwkeurig is.
* Eigen aren van toepassingen zouden geen controle kunnen hebben over de ingerichte capaciteits ruimte (en de gekoppelde kosten), omdat het gedrag van de capaciteits groep automatisch groeit. Deze situatie is handig in omgevingen waar "uitvoer processen" snel de ingerichte capaciteit en kosten kunnen opvullen en verg Roten.
* Klanten willen een directe correlatie tussen volume grootte (quota) en prestaties zien en onderhouden. Met het huidige gedrag van (impliciet) het overzetten van een volume (capaciteit) en het automatisch verg Roten van de pool, hebben klanten geen rechtstreekse correlatie, totdat het volume quotum actief is geweest of opnieuw is ingesteld. 

Veel klanten hebben direct controle aangevraagd over de ingerichte capaciteit. Ze willen de opslag capaciteit en het gebruik kunnen bepalen en verdelen. Ze willen ook de kosten bepalen aan de hand van de beschik baarheid van de toepassing en de prestaties van de client en de capaciteit van hun toepassings volumes. 

## <a name="what-is-the-volume-hard-quota-change"></a>Wat is het volume van de harde quota wijzigen   

Wanneer het volume vaste quotum is gewijzigd, zijn Azure NetApp Files volumes niet meer thin-provisioned op (Maxi maal) 100 TiB. De volumes worden ingericht op de werkelijke geconfigureerde grootte (quotum). Ook worden de groepen met de capaciteit van de groep niet langer automatisch verhoogd wanneer het verbruik van de volledige capaciteit wordt bereikt. Deze wijziging heeft betrekking op het gedrag zoals Azure Managed disks, die ook worden ingericht, zonder automatische capaciteits verhoging.

Denk bijvoorbeeld aan een Azure NetApp Files-volume dat is geconfigureerd met TiB-grootte (quotum) op een 4-TiB Ultra service niveau-capaciteits groep. Een toepassing schrijft voortdurend gegevens naar het volume.

Het *eerste* gedrag:  
* Verwachte band breedte: 128 MiB/s
* Totale capaciteit van bruikbaar (en client zichtbaar): 100 TiB   
    U kunt niet meer dan deze grootte meer gegevens op het volume schrijven.
* Capaciteits groep: neemt automatisch toe met 1 TiBe stappen wanneer deze vol is.
* Wijziging van volume quota: alleen de prestaties (band breedte) van het volume worden gewijzigd. De client heeft geen zicht bare of bruikbare capaciteit.

Het *gewijzigde* gedrag:  
* Verwachte band breedte: 128 MiB/s
* Totale capaciteit van bruikbare (en client zichtbaar): 1 TiB u kunt niet meer dan deze omvang meer gegevens op het volume meer schrijven.
* Capaciteits pool: blijft 4 TiB groot en wordt niet automatisch uitgebreid. 
* Wijziging van volume quota: de prestaties (band breedte) en de zicht bare of bruikbare capaciteit van het volume worden gewijzigd.

U moet proactief het gebruik van Azure NetApp Files volumes en capaciteits Pools bewaken. U moet het volume en de pool van het geheugen voor het gebruik van close-to-Full het doel wijzigen. Azure NetApp Files blijven toestaan dat [het volume en de grootte van de capaciteits groep in de vlucht](azure-netapp-files-resize-capacity-pools-or-volumes.md)worden gewijzigd.

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>De operationeel maken van het harde volume wijzigen

Deze sectie bevat richt lijnen voor het operationeel maken van de wijziging aan het volume vaste quotum voor een soepele overgang. Ook vindt u hier informatie over het verwerken van momenteel ingerichte volumes en capaciteits Pools, doorlopende bewaking en opties voor het beheer van waarschuwingen en capaciteit.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Momenteel ingerichte volumes en capaciteits Pools

Vanwege de wijziging van het volume vaste quotum moet u het besturings model wijzigen. Voor de ingerichte volumes en capaciteits Pools is het beheer van de capaciteit vereist.  Omdat het gewijzigde gedrag onmiddellijk plaatsvindt, raadt het Azure NetApp Files-team een reeks van eenmalig corrigerende maat regelen aan voor bestaande, eerder ingerichte volumes en capaciteits groepen, zoals beschreven in deze sectie.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Aanbevelingen voor eenmalige corrigerende of preventieve maat regelen  

De wijziging van het volume vaste quota leidt tot wijzigingen in de ingerichte en beschik bare capaciteit voor eerder ingerichte volumes en groepen. Als gevolg hiervan kunnen er problemen met capaciteits toewijzing optreden. Ter voorkoming van problemen met de korte termijn voor klanten, raadt het Azure NetApp Files team de volgende, eenmalige corrigerende en preventieve maat regelen aan: 

* **Grootte van ingericht volume**:   
    Wijzig het formaat van elk ingericht volume zodat het geschikt is voor de juiste buffer op basis van het wijzigings percentage en de grootte van de verwerkings tijd (bijvoorbeeld 20% op basis van typische werkbelasting overwegingen), met een maximum van 100 TiB (de maximale [volume grootte](azure-netapp-files-resource-limits.md#resource-limits)). Deze nieuwe grootte van het volume, inclusief buffer capaciteit, moet zijn gebaseerd op de volgende factoren:
    * De capaciteit van het **ingerichte** volume voor het geval dat de gebruikte capaciteit kleiner is dan het ingerichte volume quotum.
    * **Gebruikte** volume capaciteit als de gebruikte capaciteit meer is dan het ingerichte volume quotum.  
    Er worden geen extra kosten in rekening gebracht voor de verhoging van de capaciteit op volume niveau als de functie voor de groeps capaciteit niet hoeft te worden gegroeid. Als gevolg van deze wijziging ziet u mogelijk een *toename* van de bandbreedte limiet voor het volume (voor het geval het [type automatische QoS-capaciteits groep](azure-netapp-files-understand-storage-hierarchy.md#qos_types) wordt gebruikt).

* **Grootte van ingerichte capaciteits groep**:   
    Wanneer de grootte van de volume grootten is gewijzigd en de som van de volumes groter is dan de grootte van de hosting-capaciteits groep, moet de capaciteits groep worden verhoogd tot een grootte die gelijk is aan of groter is dan de som van de volumes, met een maximum van 500 TiB (de maximale grootte van de [capaciteits groep](azure-netapp-files-resource-limits.md#resource-limits)). Voor de capaciteit van een extra capaciteits groep gelden de kosten voor de ACR.

U moet samen werken met uw Azure NetApp Files specialisten om uw omgeving te valideren als u hulp nodig hebt bij het instellen van bewaking of waarschuwingen zoals beschreven in de volgende secties.

### <a name="ongoing-capacity-management"></a>Doorlopend capaciteits beheer  

Nadat u de eenmalige corrigerende maat regelen hebt uitgevoerd, moet u doorlopende processen samen stellen om de capaciteit te controleren en te beheren. De volgende secties bieden suggesties en alternatieven voor het bewaken en beheren van de capaciteit.

### <a name="monitor-capacity-utilization"></a>Capaciteits gebruik bewaken

U kunt het capaciteits gebruik op verschillende niveaus bewaken. 

#### <a name="vm-level-monitoring"></a>Bewaking op VM-niveau 

Het hoogste bewakings niveau (het meest in de buurt van de toepassing) bevindt zich in de virtuele machine van de toepassing. U ziet een wijziging in het gedrag van capaciteits rapportage vanuit het VM-client besturingssysteem.

In de volgende twee scenario's moet u rekening houden met een Azure NetApp Files-volume dat is geconfigureerd met een grootte van 1 TiB (quotum) op een 4-TiB, zeer grote capaciteits groep op service niveau. 

##### <a name="windows"></a>Windows

Windows-clients kunnen de gebruikte en beschik bare capaciteit van een volume controleren met behulp van de eigenschappen van toegewezen netwerk station. U kunt de   ->    ->  optie **Eigenschappen** van het Explorer-station gebruiken.  

In de volgende voor beelden ziet u de rapportage over volume capaciteit in Windows *vóór* het gewijzigde gedrag:

![Scherm afbeeldingen met een voor beeld van een opslag capaciteit van een volume voordat het gedrag verandert.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

U kunt ook de `dir` opdracht achter de opdracht prompt gebruiken, zoals hieronder wordt weer gegeven:

![Scherm opname van het gebruik van een opdracht voor het weer geven van de opslag capaciteit voor een volume voordat het gedrag wordt gewijzigd.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

In de volgende voor beelden ziet u de rapportage over volume capaciteit in Windows *na* het gewijzigde gedrag:

![Scherm afbeeldingen die een voor beeld van een opslag capaciteit van een volume weer geven nadat het gedrag is gewijzigd.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

In het volgende voor beeld ziet u de uitvoer van de `dir` opdracht:  

![Scherm afbeelding met een opdracht voor het weer geven van de opslag capaciteit van een volume nadat het gedrag is gewijzigd.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Linux-clients kunnen de gebruikte en beschik bare capaciteit van een volume controleren met behulp van de [ `df` opdracht](https://linux.die.net/man/1/df). `-h`Met de optie worden de grootte, gebruikte ruimte en de beschik bare ruimte in een lees bare indeling weer gegeven met de grootten M, G en T-eenheid.

In het volgende voor beeld ziet u de rapportage van volume capaciteit in Linux *vóór* het gewijzigde gedrag:  

![Scherm opname van het gebruik van Linux om de opslag capaciteit voor een volume weer te geven voordat het gedrag wordt gewijzigd.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

In het volgende voor beeld ziet u de rapportage van volume capaciteit in Linux *na* het gewijzigde gedrag:  

![Scherm opname van het gebruik van Linux voor het weer geven van de opslag capaciteit van een volume na wijziging van het gedrag.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Waarschuwingen configureren met ANFCapacityManager

U kunt het door de Community ondersteunde Logic Apps ANFCapacityManager-hulp programma gebruiken om Azure NetApp Files capaciteit te bewaken en waarschuwingen op maat te ontvangen. Het hulp programma ANFCapacityManager is beschikbaar op de [pagina ANFCapacityManager github](https://github.com/ANFTechTeam/ANFCapacityManager).

ANFCapacityManager is een Azure Logic-app die op capaciteit gebaseerde waarschuwings regels beheert. De grootte van het volume wordt automatisch verhoogd om te voor komen dat uw Azure NetApp Files-volumes bijna vol zijn. Het is eenvoudig te implementeren en biedt de volgende Waarschuwingenbeheer mogelijkheden:

* Wanneer een Azure NetApp Files capaciteits groep of-volume wordt gemaakt, wordt in ANFCapacityManager een metrische waarschuwings regel gemaakt op basis van de opgegeven drempel waarde voor percentage verbruikt.
* Wanneer de grootte van een Azure NetApp Files-capaciteits groep of-volume wordt gewijzigd, wijzigt ANFCapacityManager de metrische waarschuwings regel op basis van de opgegeven drempel waarde voor percentage capaciteit. Als de waarschuwings regel niet bestaat, wordt deze gemaakt.
* Wanneer een Azure NetApp Files capaciteits groep of-volume wordt verwijderd, wordt de bijbehorende waarschuwings regel voor metrische gegevens verwijderd.

U kunt de volgende belang rijke waarschuwings instellingen configureren:  

* **Maximale drempel waarde voor het percentage van de capaciteits groep** : deze instelling bepaalt de verbruikte drempel die een waarschuwing voor capaciteits Pools activeert. Een waarde van 90 zou ertoe leiden dat een waarschuwing wordt geactiveerd wanneer de capaciteits pool 90% verbruikt.
* **Volume% volledige drempel waarde** -deze instelling bepaalt de verbruikte drempel die een waarschuwing voor volumes activeert. Een waarde van 80 zou ertoe leiden dat een waarschuwing wordt geactiveerd wanneer het volume 80% verbruikt.
* **Bestaande actie groep voor capaciteits meldingen** : deze instelling is de actie groep die wordt geactiveerd voor waarschuwingen op basis van capaciteit. Deze instelling moet vooraf door u worden gemaakt. De actie groep kan e-mail, SMS of andere indelingen verzenden.

In de volgende afbeelding ziet u de configuratie van waarschuwingen:  

![Afbeelding waarin de configuratie van waarschuwingen wordt weer gegeven met behulp van ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Nadat u ANFCapacityManager hebt geïnstalleerd, kunt u het volgende gedrag verwachten: wanneer een Azure NetApp Files capaciteits groep of-volume wordt gemaakt, gewijzigd of verwijderd, wordt door de logische app automatisch een metrische waarschuwings regel voor op basis van capaciteit gemaakt, gewijzigd of gewist met de naam `ANF_Pool_poolname` of `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Capaciteit beheren

Naast bewaking en waarschuwingen, moet u ook een praktijk beheer voor toepassings capaciteit opnemen om Azure NetApp Files (toegenomen) capaciteits verbruik te beheren. Wanneer een Azure NetApp Files-volume of-capaciteits pool vol raakt, [kan extra capaciteit aan de vlucht worden gegeven zonder dat de toepassing wordt onderbroken](azure-netapp-files-resize-capacity-pools-or-volumes.md). In deze sectie worden verschillende hand matige en geautomatiseerde manieren beschreven om de ingerichte ruimte voor het volume en de capaciteit te verg Roten.
 
#### <a name="manual"></a>Handmatig 

U kunt de portal of de CLI gebruiken om de grootte van het volume of de capaciteits groep hand matig te verhogen. 

##### <a name="portal"></a>Portal 

U kunt [de grootte van een volume](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) indien nodig wijzigen. Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool.

1. Klik op de Blade NetApp-account beheren op **volumes**.  
2. Klik met de rechter muisknop op de naam van het volume dat u wilt verg Roten of verkleinen of klik op het `…` pictogram aan het einde van de rij van het volume om het context menu weer te geven. 
3. Gebruik de opties in het context menu om het volume te verg Roten of verkleinen of verwijderen.   

   ![Scherm opname van context menu opties voor een volume.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Scherm opname van het venster volume quotum bijwerken.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

In sommige gevallen beschikt de hosting-capaciteits groep niet over voldoende capaciteit om de grootte van de volumes te wijzigen. U kunt [de grootte van de capaciteits groep echter wijzigen](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) in 1-TIB of verlaagt. De grootte van de capaciteits groep mag niet kleiner zijn dan 4 TiB. *Het wijzigen van de grootte van de capaciteits groep wijzigt de gekochte Azure NetApp Files capaciteit.*

1. Klik op de Blade NetApp-account beheren op de capaciteits groep waarvan u de grootte wilt wijzigen.
2. Klik met de rechter muisknop op de naam van de capaciteits groep of klik op het `…` pictogram aan het einde van de rij van de capaciteits groep om het context menu weer te geven.
3. Gebruik de opties in het context menu om het formaat van de capaciteits groep te wijzigen of te verwijderen.    

   ![Scherm opname van context menu opties voor een capaciteits pool.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Scherm afbeelding met het venster formaat van groep wijzigen.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI of PowerShell

U kunt de [Azure NETAPP files cli-hulpprogram ma's](azure-netapp-files-sdk-cli.md#cli-tools), inclusief de Azure CLI en Azure PowerShell, gebruiken om de grootte van het volume of de capaciteits groep hand matig te wijzigen.  De volgende twee opdrachten kunnen worden gebruikt voor het beheren van Azure NetApp Files volume-en pool bronnen:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Als u Azure NetApp Files resources wilt beheren met behulp van Azure CLI, opent u de Azure Portal en selecteert u de koppeling Azure **Cloud shell** boven aan de menu balk: 

[![Scherm afbeelding die laat zien hoe u Cloud shell koppeling kunt openen. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Met deze actie wordt de Azure Cloud Shell geopend:

[![Scherm opname van Cloud shell venster. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

De volgende voor beelden gebruiken de opdrachten om de grootte van een volume [weer te geven](/cli/azure/netappfiles/volume#az-netappfiles-volume-show) en bij te [werken](/cli/azure/netappfiles/volume#az-netappfiles-volume-update) :
 
[![Scherm opname van de weer gave van de volume grootte met Power shell. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Scherm opname van het gebruik van Power shell om de volume grootte bij te werken. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

De volgende voor beelden gebruiken de opdrachten om de grootte van een capaciteits groep [weer te geven](/cli/azure/netappfiles/pool#az-netappfiles-pool-show) en bij te [werken](/cli/azure/netappfiles/pool#az-netappfiles-pool-update) :

[![Scherm opname van de weer gave van de grootte van de capaciteits pool met Power shell. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Scherm opname van het gebruik van Power shell om de grootte van de capaciteits pool bij te werken. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Geautomatiseerd  

U kunt een geautomatiseerd proces bouwen om het gewijzigde gedrag te beheren.

##### <a name="rest-api"></a>REST-API   

De REST API voor de Azure NetApp Files-service definieert HTTP-bewerkingen op basis van resources, zoals het NetApp-account, de capaciteits pool, de volumes en moment opnamen. De REST API-specificatie voor Azure NetApp Files wordt gepubliceerd via de [pagina Azure NetApp Files Resource Manager-github](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)]. U kunt [voorbeeld code vinden voor gebruik met rest-api's](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) in github.

Zie [ontwikkelen voor Azure NetApp files met rest API](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>REST API met PowerShell  

De REST API voor de Azure NetApp Files-service definieert HTTP-bewerkingen op basis van resources, zoals het NetApp-account, de capaciteits pool, de volumes en moment opnamen. De [rest API-specificatie voor Azure NetApp files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) wordt gepubliceerd via github.

Zie [ontwikkelen voor Azure NetApp files met rest API met behulp van Power shell](develop-rest-api-powershell.md).

##### <a name="capacity-management-using-anfcapacitymanager"></a>Capaciteits beheer met ANFCapacityManager

ANFCapacityManager is een Azure Logic-app die op capaciteit gebaseerde waarschuwings regels beheert. De grootte van het volume wordt automatisch verhoogd om te voor komen dat uw Azure NetApp Files-volumes bijna vol zijn. Naast het verzenden van waarschuwingen kan het de automatische toename van de grootte van het volume en de capaciteits groep inschakelen om te voor komen dat uw Azure NetApp Files volumes bijna vol zijn: 

* Wanneer een Azure NetApp Files volume de opgegeven drempel waarde voor percentage verbruikt, wordt het volume quotum (grootte) eventueel verhoogd met het percentage dat is opgegeven tussen 10-100.  
* Als het verg Roten van de grootte van het volume groter is dan de capaciteit van de groep met capaciteit, wordt de grootte van de capaciteits groep ook verhoogd om de nieuwe grootte van het volume aan te passen.

U kunt de volgende instelling voor sleutel capaciteits beheer configureren:  

* Automatisch verg Roten: percentage **verhogen** -percentage van de bestaande volume grootte om een volume te verg Roten als het de opgegeven **waarde voor percentage volle drempel** bereikt. Met de waarde 0 (nul) wordt de functie autogrow uitgeschakeld. Een waarde tussen 10 en 100 wordt aanbevolen.

    ![Scherm opname van het venster volume automatische groei percentage instellen.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Veelgestelde vragen 

In deze sectie vindt u antwoorden op enkele vragen over de wijziging van het volume vaste quota. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Telt het aantal snap shot-ruimte naar de bruikbare of ingerichte capaciteit van een volume?

Ja, het aantal verbruikte moment opnamen ligt op de ingerichte ruimte in het volume. Als het volume vol is, moet u rekening houden met twee opties voor herstel:

* Wijzig de grootte van het volume zoals beschreven in dit artikel.
* Verwijder oudere moment opnamen om ruimte vrij te maken in het hosting volume.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Betekent dit dat het automatische groei gedrag van het volume wordt verwijderd uit Azure NetApp Files?

Een veelvoorkomende uitbrei ding is dat Azure NetApp Files *volumes* bij het opvullen automatisch groeien. Volumes zijn dun ingericht met een grootte van 100 TiB, ongeacht het daad werkelijke set-quotum, terwijl de groep met de *capaciteit* van de onderdrukbaar in stappen van 1-TIB automatisch groeit. Deze wijziging is van invloed op de grootte van het (zicht bare en bruikbare) *volume* voor het ingestelde quotum en de *capaciteits groepen* worden niet langer automatisch verg root. Deze wijziging resulteert in een regel matig juiste nauw keurige beschik bare ruimte op de client en capaciteits rapportage. Het voor komt ' overmatig ' capaciteits verbruik.

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Heeft deze wijziging invloed op volumes die worden gerepliceerd met cross-Region-Replication (preview)? 

Het volume quotum van de harde schijf wordt niet afgedwongen op de replicatie doel volumes.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Heeft deze wijziging gevolgen voor de metrische gegevens die momenteel beschikbaar zijn in Azure Monitor?

De metrische gegevens van de portal en de statistieken van Azure Monitor worden nauw keurig weer gegeven in het nieuwe toewijzings-en gebruiks model.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Heeft deze wijziging gevolgen voor de resource limieten voor Azure NetApp Files?

Er zijn geen wijzigingen in de resource limieten voor Azure NetApp Files buiten de quota wijzigingen die in dit artikel worden beschreven.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Is er een voor beeld van een ANFCapacityManager werk stroom?  

Ja. Zie de [pagina voor beeld van de GitHub-werk stroom volume automatisch verg Roten](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md).

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Wordt ANFCapacityManager micro soft ondersteund?  

[De ANFCapacityManager Logic-app wordt geboden als-is en wordt niet ondersteund door NetApp of micro soft](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). U wordt aangeraden uw specifieke omgeving of vereisten aan te passen. U moet de functionaliteit testen voordat u deze implementeert in een bedrijfs kritieke of productie omgeving.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Hoe kan ik een fout melden of een functie aanvraag indienen voor ANFCapacityManger?
U kunt fouten en functie aanvragen indienen door te klikken op **nieuw probleem** op de [pagina ANFCapacityManager github](https://github.com/ANFTechTeam/ANFCapacityManager/issues).

## <a name="next-steps"></a>Volgende stappen
* [Het formaat van een capaciteitspool of volume wijzigen](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Metrische gegevens voor Azure NetApp Files](azure-netapp-files-metrics.md)