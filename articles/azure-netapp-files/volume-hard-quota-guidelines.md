---
title: Wat het wijzigen van het volumeharde quotum betekent voor uw Azure NetApp Files service | Microsoft Docs
description: Beschrijft de wijziging in het gebruik van volumeharde quota, hoe u de wijziging kunt plannen en hoe u capaciteiten kunt bewaken en beheren.
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
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: 5e7f71f91e5778b4f096bb760bfe5a0a89b5cbcb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764275"
---
# <a name="what-changing-to-volume-hard-quota-means-for-your-azure-netapp-files-service"></a>Wat het wijzigen van het harde quotum voor volumes betekent voor uw Azure NetApp Files service

Vanaf het begin van de service maakt Azure NetApp Files gebruik van een mechanisme voor het inrichten van capaciteitspools en automatische groei. Azure NetApp Files-volumes worden thin provisioned op een onderlaag, door de klant inrichtende capaciteitspool van een geselecteerde laag en grootte. Volumegrootten (quota) worden gebruikt om prestaties en capaciteit te bieden en de quota kunnen op elk moment op elk moment worden aangepast. Dit gedrag betekent dat het volumequotum momenteel een prestatieknop is die wordt gebruikt om de bandbreedte van het volume te bepalen. Op dit moment nemen de onderlaagcapaciteitspools automatisch toe wanneer de capaciteit vol is.   

> [!IMPORTANT] 
> Het Azure NetApp Files gedrag van het inrichten van volumes en capaciteitspools wordt gewijzigd in een handmatig *en* *beheersbaar* mechanisme. **Vanaf 30 april 2021 (bijgewerkt) beheren volumegrootten (quotum) de bandbreedteprestaties, evenals de inrichtende capaciteit, en worden onderliggende capaciteitspools niet meer automatisch groter.** 

## <a name="reasons-for-the-change-to-volume-hard-quota"></a>Redenen voor de wijziging van het harde quotum voor volumes

Veel klanten hebben drie belangrijke uitdagingen met het eerste *gedrag* aangegeven:
* VM-clients zien de capaciteit met thin provisioning (100 TiB) van een bepaald volume bij gebruik van besturingssysteemruimte of hulpprogramma's voor capaciteitsbewaking, waardoor de capaciteit aan de client- of toepassingszijde onnauwkeurig is.
* Toepassingseigenaren hebben geen controle over de inrichtende capaciteitspoolruimte (en de bijbehorende kosten), vanwege het gedrag van automatisch vergroten van de capaciteitspool. Deze situatie is omslachtig in omgevingen waarin 'run-away processen' snel kunnen worden gevuld en de inrichtende capaciteit en kosten kunnen toenemen.
* Klanten willen een rechtstreekse correlatie tussen volumegrootte (quotum) en prestaties zien en onderhouden. Met het huidige gedrag van (impliciet) oversubsubribing van een volume (capaciteitsgewende) en automatische groei van de pool, hebben klanten geen directe correlatie, totdat het volumequotum actief is ingesteld of opnieuw is ingesteld. 

Veel klanten hebben directe controle over de inrichtende capaciteit aangevraagd. Ze willen de opslagcapaciteit en het gebruik controleren en in balans brengen. Ze willen ook de kosten beheersen, samen met de zichtbaarheid aan de toepassingszijde en clientzijde van de beschikbare, gebruikte en inrichtende capaciteit en prestaties van hun toepassingsvolumes. 

## <a name="what-is-the-volume-hard-quota-change"></a>Wat is de wijziging van het harde quotum voor het volume?   

Als het harde quotum voor het volume is gewijzigd, Azure NetApp Files volumes niet langer thin provisioning op (maximaal) 100 TiB. De volumes worden ingericht met de werkelijke geconfigureerde grootte (quotum). Bovendien zullen de onderlaagse capaciteitspools niet meer automatisch groeien bij het bereiken van het verbruik van de volledige capaciteit. Deze wijziging weerspiegelt het gedrag zoals beheerde Azure-schijven, die ook zonder automatische capaciteitsverhoging worden ingericht.

Denk bijvoorbeeld aan een Azure NetApp Files volume dat is geconfigureerd met een grootte van 1 TiB (quotum) voor een capaciteitspool van 4 TiB Ultra Service Level. Een toepassing schrijft continu gegevens naar het volume.

Het *eerste* gedrag:  
* Verwachte bandbreedte: 128 MiB/s
* Totale bruikbaar (en client zichtbaar) capaciteit: 100 TiB   
    U kunt niet meer gegevens op het volume schrijven dan deze grootte.
* Capaciteitspool: Neemt automatisch toe met een toename van 1 TiB wanneer deze vol is.
* Volumequotumwijziging: alleen de prestaties (bandbreedte) van het volume worden gewijzigd. De zichtbare of bruikbaar capaciteit van de client wordt niet gewijzigd.

Het *gewijzigde gedrag:*  
* Verwachte bandbreedte: 128 MiB/s
* Totale bruikbaar (en client zichtbaar) capaciteit: 1 TiB U kunt niet meer gegevens schrijven op het volume buiten deze grootte.
* Capaciteitspool: blijft 4 TiB groot en groeit niet automatisch. 
* Volumequotumwijziging: wijzigt de prestaties (bandbreedte) en de zichtbare of gebruikscapaciteit van de client van het volume.

U moet het gebruik van volumes en capaciteitspools proactief Azure NetApp Files bewaken. U moet het volume- en poolgebruik doelbewust wijzigen voor bijna volledig gebruik. Azure NetApp Files blijven [on-the-fly volume- en capaciteitsgroepsbewerkingen toestaan.](azure-netapp-files-resize-capacity-pools-or-volumes.md)

## <a name="how-to-operationalize-the-volume-hard-quota-change"></a>De wijziging van het harde quotum voor volumes operationeel maken

Deze sectie bevat richtlijnen voor het operationeel maken van de wijziging in het harde quotum voor volumes voor een soepele overgang. Het biedt ook inzichten voor het verwerken van momenteel inrichtende volumes en capaciteitspools, door te gaan bewaking, en opties voor waarschuwingen en capaciteitsbeheer.

### <a name="currently-provisioned-volumes-and-capacity-pools"></a>Momenteel inrichtende volumes en capaciteitspools

Vanwege de wijziging van het harde quotum voor het volume, moet u uw bedrijfsmodel wijzigen. Voor de inrichtende volumes en capaciteitspools is doorlopend capaciteitsbeheer vereist.  Omdat het gewijzigde gedrag direct zal plaatsvinden, raadt het Azure NetApp Files-team een reeks een time-corrective measures aan voor bestaande, eerder inrichtende volumes en capaciteitspools, zoals beschreven in deze sectie.

#### <a name="one-time-corrective-or-preventative-measures-recommendations"></a>Aanbevelingen voor een time-corrective of preventative measures  

De wijziging van het harde quotum voor volumes leidt tot wijzigingen in de inrichtende en beschikbare capaciteit voor eerder inrichtende volumes en pools. Als gevolg hiervan kunnen er enkele uitdagingen voor capaciteitstoewijzing ontstaan. Om situaties met weinig ruimte op de korte termijn voor klanten te voorkomen, raadt het Azure NetApp Files team de volgende een time-corrective/preventatieve maatregelen aan: 

* **Inrichten van volumegrootten:**   
    Wijzig de grootte van elk ingerichte volume om de juiste buffer te hebben op basis van de wijzigingssnelheid en waarschuwingen of om de doorlooptijd (bijvoorbeeld 20% op basis van typische workloadoverwegingen) te wijzigen, met een maximum van 100 TiB (de limiet voor [de volumegrootte).](azure-netapp-files-resource-limits.md#resource-limits) Deze nieuwe volumegrootte, inclusief buffercapaciteit, moet worden gebaseerd op de volgende factoren:
    * **Inrichten van** volumecapaciteit, voor het geval de gebruikte capaciteit kleiner is dan het inrichtende volumequotum.
    * **Gebruikte** volumecapaciteit, voor het geval de gebruikte capaciteit groter is dan het inrichtende volumequotum.  
    Er zijn geen extra kosten voor capaciteitsverhoging op volumeniveau als de onderlaagcapaciteitspool niet hoeft te worden vergroot. Als gevolg van deze wijziging ziet u  mogelijk een hogere bandbreedtelimiet voor het volume (voor het geval het type automatische [QoS-capaciteitspool](azure-netapp-files-understand-storage-hierarchy.md#qos_types) wordt gebruikt).

* **Inrichten van capaciteitspoolgrootten:**   
    Als de som van volumes groter wordt dan de grootte van de hostcapaciteitspool nadat de volumegrootte is aangepast, moet de capaciteitspool worden verhoogd tot een grootte die [](azure-netapp-files-resource-limits.md#resource-limits)gelijk is aan of groter is dan de som van de volumes, met een maximum van 500 TiB (de maximale capaciteitspool). Voor extra capaciteitspoolcapaciteit gelden zoals gebruikelijk ACR-kosten.

Werk samen met uw Azure NetApp Files om uw omgeving te valideren, als u hulp nodig hebt bij het instellen van bewaking of waarschuwingen, zoals beschreven in de onderstaande secties.

### <a name="ongoing-capacity-management"></a>Doorlopend capaciteitsbeheer  

Na het uitvoeren van de een time-corrective measures moet u lopende processen samenbrengen om de capaciteit te bewaken en te beheren. De volgende secties bieden suggesties en alternatieven voor capaciteitsbewaking en -beheer.

### <a name="monitor-capacity-utilization"></a>Capaciteitsgebruik bewaken

U kunt het capaciteitsgebruik op verschillende niveaus bewaken. 

#### <a name="vm-level-monitoring"></a>Bewaking op VM-niveau 

Het hoogste bewakingsniveau (het dichtst bij de toepassing) is vanuit de virtuele machine van de toepassing. U ziet een wijziging in het gedrag in capaciteitsrapportage vanuit het besturingssysteem van de VM-client.

In de volgende twee scenario's kunt u een Azure NetApp Files-volume overwegen dat is geconfigureerd met een grootte van 1 TiB (quotum) voor een capaciteitspool van 4 TiB, ultraserviceniveau. 

##### <a name="windows"></a>Windows

Windows-clients kunnen de gebruikte en beschikbare capaciteit van een volume controleren met behulp van de eigenschappen van het netwerkstation. U kunt de optie **Eigenschappen van**  ->    ->  **Verkenner-station** gebruiken.  

De volgende voorbeelden tonen de volumecapaciteitsrapportage in Windows *vóór* het gewijzigde gedrag:

![Schermopnamen met een voorbeeld van een opslagcapaciteit van een volume voordat het gedrag verandert.](../media/azure-netapp-files/hard-quota-windows-capacity-before.png)

U kunt ook de opdracht `dir` bij de opdrachtprompt gebruiken, zoals hieronder wordt weergegeven:

![Schermopname van het gebruik van een opdracht om de opslagcapaciteit voor een volume weer te geven voordat het gedrag wordt gewijzigd.](../media/azure-netapp-files/hard-quota-command-capacity-before.png)

De volgende voorbeelden tonen de volumecapaciteitsrapportage in Windows *na* het gewijzigde gedrag:

![Schermopnamen met een voorbeeld van een opslagcapaciteit van een volume na een wijziging in het gedrag.](../media/azure-netapp-files/hard-quota-windows-capacity-after.png)

In het volgende voorbeeld ziet u `dir` de uitvoer van de opdracht:  

![Schermopname van het gebruik van een opdracht om de opslagcapaciteit voor een volume weer te geven na een gedragswijziging.](../media/azure-netapp-files/hard-quota-command-capacity-after.png)

##### <a name="linux"></a>Linux 

Linux-clients kunnen de gebruikte en beschikbare capaciteit van een volume controleren met behulp van de [ `df` opdracht](https://linux.die.net/man/1/df). Met de optie worden de grootte, de gebruikte ruimte en de beschikbare ruimte in een door mensen leesbare indeling met behulp van de eenheden M, G en `-h` T.

In het volgende voorbeeld ziet u de volumecapaciteitsrapportage in Linux *vóór* het gewijzigde gedrag:  

![Schermopname van het gebruik van Linux om de opslagcapaciteit voor een volume weer te geven voordat het gedrag wordt gewijzigd.](../media/azure-netapp-files/hard-quota-linux-capacity-before.png)

In het volgende voorbeeld ziet u de volumecapaciteitsrapportage in Linux *na* het gewijzigde gedrag:  

![Schermopname van het gebruik van Linux om de opslagcapaciteit voor een volume weer te geven na gedragswijziging.](../media/azure-netapp-files/hard-quota-linux-capacity-after.png)


### <a name="configure-alerts-using-anfcapacitymanager"></a>Waarschuwingen configureren met ANFCapacityManager

U kunt het door de community ondersteunde Logic Apps ANFCapacityManager gebruiken om uw Azure NetApp Files te bewaken en op maat gemaakte waarschuwingen te ontvangen. Het hulpprogramma ANFCapacityManager is beschikbaar op de [GitHub-pagina ANFCapacityManager.](https://github.com/ANFTechTeam/ANFCapacityManager)

ANFCapacityManager is een logische Azure-app die waarschuwingsregels op basis van capaciteit beheert. De volumegrootte wordt automatisch verhoogd om te voorkomen dat Azure NetApp Files volumes te weinig ruimte hebben. Het is eenvoudig te implementeren en biedt de volgende Waarschuwingenbeheer mogelijkheden:

* Wanneer een Azure NetApp Files capaciteitspool of volume wordt gemaakt, maakt ANFCapacityManager een waarschuwingsregel voor metrische gegevens op basis van de opgegeven percentage verbruikte drempelwaarde.
* Wanneer een Azure NetApp Files capaciteitspool of volume wordt ge wijzigt, wijzigt ANFCapacityManager de waarschuwingsregel voor metrische gegevens op basis van de drempelwaarde voor het opgegeven percentage verbruikte capaciteit. Als de waarschuwingsregel niet bestaat, wordt deze gemaakt.
* Wanneer een Azure NetApp Files capaciteitspool of volume wordt verwijderd, wordt de bijbehorende waarschuwingsregel voor metrische gegevens verwijderd.

U kunt de volgende belangrijke waarschuwingsinstellingen configureren:  

* **Volledige drempelwaarde voor capaciteitspool:** met deze instelling wordt de verbruikte drempelwaarde bepaald die een waarschuwing voor capaciteitspools activeert. Een waarde van 90 zou ertoe leiden dat een waarschuwing wordt geactiveerd wanneer de capaciteitspool 90% verbruikt heeft bereikt.
* **Volume % volledige drempelwaarde:** deze instelling bepaalt de verbruikte drempelwaarde die een waarschuwing voor volumes activeert. Een waarde van 80 zou ertoe leiden dat een waarschuwing wordt geactiveerd wanneer het volume 80% verbruikt bereikt.
* **Bestaande actiegroep voor capaciteitsmeldingen:** deze instelling is de actiegroep die wordt geactiveerd voor waarschuwingen op basis van capaciteit. Deze instelling moet vooraf door u zijn gemaakt. De actiegroep kan e-mail, sms of andere indelingen verzenden.

In de volgende afbeelding ziet u de configuratie van de waarschuwing:  

![Afbeelding van de configuratie van waarschuwingen met behulp van ANFCapacityManager.](../media/azure-netapp-files/hard-quota-anfcapacitymanager-configuration.png)

Nadat u ANFCapacityManager hebt geïnstalleerd, kunt u het volgende gedrag verwachten: Wanneer een Azure NetApp Files-capaciteitspool of -volume wordt gemaakt, gewijzigd of verwijderd, maakt, wijzigt of verwijdert de logische app automatisch een metrische waarschuwingsregel op basis van capaciteit met de naam `ANF_Pool_poolname` of `ANF_Volume_poolname_volname` . 

### <a name="manage-capacity"></a>Capaciteit beheren

Naast bewaking en waarschuwingen moet u ook een toepassingscapaciteitsbeheerprocedure gebruiken voor het beheren van Azure NetApp Files (verhoogd) capaciteitsverbruik. Wanneer een Azure NetApp Files of capaciteitspool vol is, kan er [on-the-fly](azure-netapp-files-resize-capacity-pools-or-volumes.md)extra capaciteit worden geleverd zonder onderbreking van de toepassing. In deze sectie worden verschillende handmatige en geautomatiseerde manieren beschreven om het volume en de in de capaciteitspool inrichten naar behoefte te vergroten.
 
#### <a name="manual"></a>Handmatig 

U kunt de portal of de CLI gebruiken om de grootte van het volume of de capaciteitspool handmatig te vergroten. 

##### <a name="portal"></a>Portal 

U kunt [de grootte van een volume indien nodig](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-a-volume) wijzigen. Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool.

1. Klik op de blade NetApp-account beheren op **Volumes.**  
2. Klik met de rechtermuisknop op de naam van het volume dat u wilt aanpassen of klik op het pictogram aan het einde van de rij van het volume om het `…` contextmenu weer te geven. 
3. Gebruik de opties in het contextmenu om het volume te wijzigen of te verwijderen.   

   ![Schermopname van de opties in het contextmenu voor een volume.](../media/azure-netapp-files/hard-quota-volume-options.png) 

   ![Schermopname van het venster Volumequotum bijwerken.](../media/azure-netapp-files/hard-quota-update-volume-quota.png) 

In sommige gevallen heeft de hostcapaciteitspool niet voldoende capaciteit om de volumes te vergroten of te vergroten of te vergroten. U kunt de grootte [van de capaciteitspool echter](azure-netapp-files-resize-capacity-pools-or-volumes.md#resize-the-capacity-pool) wijzigen in stappen of in aflopende stappen van 1 TiB. De grootte van de capaciteitspool mag niet kleiner zijn dan 4 TiB. *Als u de capaciteitspool wijzigt, wordt de aangeschafte Azure NetApp Files gewijzigd.*

1. Klik op de blade NetApp-account beheren op de capaciteitspool die u wilt vergroten of vergroten of haar.
2. Klik met de rechtermuisknop op de naam van de capaciteitspool of klik op het pictogram aan het einde van de rij van de capaciteitspool `…` om het contextmenu weer te geven.
3. Gebruik de opties in het contextmenu om de capaciteitspool te vergroten of te verwijderen.    

   ![Schermopname van de opties in het contextmenu voor een capaciteitspool.](../media/azure-netapp-files/hard-quota-pool-options.png) 

   ![Schermopname van het venster Pool van hetize-schermafbeelding.](../media/azure-netapp-files/hard-quota-update-resize-pool.png) 


##### <a name="cli-or-powershell"></a>CLI of PowerShell

U kunt de Azure NetApp Files CLI-hulpprogramma's , inclusief de Azure CLI en Azure PowerShell, gebruiken om handmatig de grootte van het volume of de capaciteitspool te wijzigen. [](azure-netapp-files-sdk-cli.md#cli-tools)  De volgende twee opdrachten kunnen worden gebruikt voor het beheren Azure NetApp Files volume- en poolbronnen:  

* [`az netappfiles pool`](/cli/azure/netappfiles/pool)
* [`az netappfiles volume`](/cli/azure/netappfiles/volume)

Als u Azure NetApp Files azure CLI wilt beheren, kunt u de Azure Portal openen en de koppeling Azure **Cloud Shell** bovenaan de menubalk selecteren: 

[![Schermopname die laat zien hoe u toegang krijgt Cloud Shell koppeling. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-link.png#lightbox)

Met deze actie wordt de Azure Cloud Shell:

[![Schermopname van Cloud Shell venster. ](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png)](../media/azure-netapp-files/hard-quota-update-cloud-shell-window.png#lightbox)

In de volgende voorbeelden worden de opdrachten gebruikt om [de grootte](/cli/azure/netappfiles/volume#az_netappfiles_volume_show) [van](/cli/azure/netappfiles/volume#az_netappfiles_volume_update) een volume weer te geven en bij te werken:
 
[![Schermopname van het gebruik van PowerShell om de volumegrootte weer te geven. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-show.png#lightbox)

[![Schermopname van het gebruik van PowerShell voor het bijwerken van de volumegrootte. ](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-volume-update.png#lightbox)

In de volgende voorbeelden worden de opdrachten gebruikt om [de grootte](/cli/azure/netappfiles/pool#az_netappfiles_pool_show) [van](/cli/azure/netappfiles/pool#az_netappfiles_pool_update) een capaciteitspool weer te geven en bij te werken:

[![Schermopname van het gebruik van PowerShell om de grootte van de capaciteitspool weer te geven. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-show.png#lightbox) 

[![Schermopname van het gebruik van PowerShell om de grootte van de capaciteitspool bij te werken. ](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png)](../media/azure-netapp-files/hard-quota-update-powershell-pool-update.png#lightbox)

#### <a name="automated"></a>Geautomatiseerd  

U kunt een geautomatiseerd proces bouwen om het gewijzigde gedrag te beheren.

##### <a name="rest-api"></a>REST-API   

De REST API voor de Azure NetApp Files-service definieert HTTP-bewerkingen voor resources zoals het NetApp-account, de capaciteitspool, de volumes en momentopnamen. De REST API-specificatie voor Azure NetApp Files wordt gepubliceerd via Azure NetApp Files Resource Manager [GitHub-pagina](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)]. U vindt [voorbeeldcode voor gebruik met REST API's](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager/Microsoft.NetApp/stable/2020-06-01/examples) in GitHub.

Zie [Ontwikkelen voor Azure NetApp Files met REST API](azure-netapp-files-develop-with-rest-api.md). 

##### <a name="rest-api-using-powershell"></a>REST API met PowerShell  

De REST API voor de Azure NetApp Files-service definieert HTTP-bewerkingen voor resources zoals het NetApp-account, de capaciteitspool, de volumes en momentopnamen. De [REST API specificatie voor Azure NetApp Files](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager) wordt gepubliceerd via GitHub.

Zie [Ontwikkelen voor Azure NetApp Files met REST API met behulp van PowerShell.](develop-rest-api-powershell.md)

##### <a name="capacity-management-using-anfcapacitymanager"></a>Capaciteitsbeheer met ANFCapacityManager

ANFCapacityManager is een logische Azure-app die op capaciteit gebaseerde waarschuwingsregels beheert. Het vergroot automatisch de volumegrootten om te voorkomen dat Azure NetApp Files volumes te weinig ruimte hebben. Naast het verzenden van waarschuwingen, kan de automatische toename van de grootte van volumes en capaciteitspools worden ingeschakeld om te voorkomen dat uw Azure NetApp Files volumes te weinig ruimte hebben: 

* Wanneer een volume Azure NetApp Files het opgegeven percentage verbruikte drempel bereikt, wordt het volumequotum (grootte) verhoogd met het percentage dat is opgegeven tussen 10 en 100.  
* Als het volume groter wordt dan de capaciteit van de capaciteitspool, wordt de grootte van de capaciteitspool ook verhoogd om ruimte te bieden aan de nieuwe volumegrootte.

U kunt de volgende belangrijke instelling voor capaciteitsbeheer configureren:  

* **Toename van het percentage automatische** groei: het percentage van de bestaande volumegrootte om automatisch een volume te laten groeien als het opgegeven percentage volledige **drempelwaarde bereikt.** Met de waarde 0 (nul) wordt de functie AutoGrow uitgeschakeld. Een waarde tussen 10 en 100 wordt aanbevolen.

    ![Schermopname van het venster Set Volume Auto Growth Percent.](../media/azure-netapp-files/hard-quota-volume-anfcapacitymanager-auto-grow-percent.png) 

## <a name="faq"></a>Veelgestelde vragen 

In deze sectie vindt u antwoorden op enkele vragen over de wijziging van het harde quotum voor het volume. 

### <a name="does-snapshot-space-count-towards-the-usable-or-provisioned-capacity-of-a-volume"></a>Telt de momentopnameruimte mee voor de bruikbaar of inrichtende capaciteit van een volume?

Ja, de verbruikte momentopnamecapaciteit telt mee voor de inrichtende ruimte in het volume. Als het volume vol is, kunt u twee herstelopties overwegen:

* Het volume aanpassen zoals beschreven in dit artikel.
* Verwijder oudere momentopnamen om ruimte vrij te maken in het hostingvolume.

### <a name="does-this-change-mean-the-volume-auto-grow-behavior-will-disappear-from-azure-netapp-files"></a>Betekent deze wijziging dat het gedrag voor automatisch groeien van het volume verdwijnt Azure NetApp Files?

Een veelvoorkomend misvatting is dat Azure NetApp Files *volumes* automatisch toenemen bij het invullen. Volumes werden thin *provisioned* met een grootte van 100 TiB, ongeacht het werkelijke ingesteld quotum, terwijl de onderlaying capaciteitspool automatisch zou groeien met stappen van 1 TiB. Door deze wijziging wordt de volumegrootte (zichtbaar en *bruikbaar)* in het stellen quotum bereikt, en worden de capaciteitspools niet meer automatisch groter.  Deze wijziging resulteert in doorgaans gewenste nauwkeurige ruimte- en capaciteitsrapportage aan de clientzijde. Dit voorkomt 'runaway' capaciteitsverbruik.

### <a name="does-this-change-have-any-effect-on-volumes-replicated-with-cross-region-replication-preview"></a>Heeft deze wijziging gevolgen voor volumes die worden gerepliceerd met replicatie tussen regio's (preview)? 

Het quotum voor het harde volume wordt niet afgedwongen op replicatiedoelvolumes.

### <a name="does-this-change-have-any-effect-on-metrics-currently-available-in-azure-monitor"></a>Heeft deze wijziging invloed op de metrische gegevens die momenteel beschikbaar zijn in Azure Monitor?

Metrische gegevens en Azure Monitor portal geven nauwkeurig het nieuwe toewijzings- en gebruiksmodel weer.

### <a name="does-this-change-have-any-effect-on-the-resource-limits-for-azure-netapp-files"></a>Heeft deze wijziging invloed op de resourcelimieten voor Azure NetApp Files?

Er zijn geen wijzigingen in resourcelimieten voor Azure NetApp Files de quotumwijzigingen die in dit artikel worden beschreven.

### <a name="is-there-an-example-anfcapacitymanager-workflow"></a>Is er een voorbeeld van een ANFCapacityManager-werkstroom?  

Ja. Zie de [GitHub-pagina Voorbeeld van Volume AutoGrow Workflow.](https://github.com/ANFTechTeam/ANFCapacityManager/blob/master/ResizeWorkflow.md)

### <a name="is-anfcapacitymanager-microsoft-supported"></a>Wordt ANFCapacityManager Microsoft ondersteund?  

[De logische app ANFCapacityManager wordt geleverd zoals ze is en wordt niet ondersteund door NetApp of Microsoft](https://github.com/ANFTechTeam/ANFCapacityManager#disclaimer). U wordt aangeraden om deze aan te passen aan uw specifieke omgeving of vereisten. U moet de functionaliteit testen voordat u deze implementeert in bedrijfskritieke of productieomgevingen.

### <a name="how-can-i-report-a-bug-or-submit-a-feature-request-for-anfcapacitymanger"></a>Hoe kan ik een fout melden of een functieaanvraag indienen voor ANFCapacityManger?
U kunt fouten en functieaanvragen indienen door op Nieuw probleem **te klikken** op de [GitHub-pagina ANFCapacityManager.](https://github.com/ANFTechTeam/ANFCapacityManager/issues)

## <a name="next-steps"></a>Volgende stappen
* [Het formaat van een capaciteitspool of volume wijzigen](azure-netapp-files-resize-capacity-pools-or-volumes.md) 
* [Metrische gegevens voor Azure NetApp Files](azure-netapp-files-metrics.md)
