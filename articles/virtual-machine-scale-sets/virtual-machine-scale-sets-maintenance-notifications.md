---
title: Onderhouds meldingen voor virtuele-machine schaal sets in azure
description: Bekijk onderhouds meldingen en start self-service onderhoud voor schaal sets voor virtuele machines in Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: b4c705e65b5b8c4f9e34dc5ff1474e87da5bee02
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209781"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Meldingen voor gepland onderhoud voor virtuele-machineschaalsets


Azure voert regel matig updates uit om de betrouw baarheid, prestaties en beveiliging van de host-infra structuur voor virtuele machines (Vm's) te verbeteren. Updates bevatten mogelijk patches voor de hosting omgeving of het upgraden en buiten gebruik stellen van hardware. De meeste updates hebben geen invloed op de gehoste Vm's. Updates zijn echter van invloed op Vm's in deze scenario's:

- Als voor het onderhoud niet opnieuw moet worden opgestart, wordt de virtuele machine gedurende enkele seconden onderbroken tijdens het bijwerken van de host. Dit type onderhouds bewerkingen worden een fout domein toegepast op het fout domein. De voortgang wordt gestopt als er waarschuwings status signalen worden ontvangen.

- Als de computer opnieuw moet worden opgestart, wordt een melding weer gegeven wanneer het onderhoud is gepland. In deze gevallen krijgt u een tijd venster met meestal 35 dagen waarop u het onderhoud zelf kunt starten, wanneer dit voor u geschikt is.


Gepland onderhoud waarvoor opnieuw opstarten is vereist, wordt gepland in golven. Elke golf heeft een ander bereik (regio's):

- Een golf begint met een melding aan klanten. Standaard wordt er een melding verzonden naar de eigenaar van het abonnement en mede-eigen aars. U kunt ontvangers en bericht opties, zoals e-mail, SMS en webhooks, toevoegen aan de meldingen door Azure- [activiteiten logboek waarschuwingen](../azure-monitor/essentials/platform-logs-overview.md)te gebruiken.  
- Met een melding wordt een *self-service venster* beschikbaar gesteld. In dit venster met meestal 35 dagen kunt u zien welke van uw Vm's zijn opgenomen in de Wave. U kunt onderhoud proactief starten volgens uw eigen plannings behoeften.
- Na het self-service venster begint een *gepland onderhouds venster* . Op een bepaald moment tijdens dit venster plant Azure en past het vereiste onderhoud toe op uw VM. 

Het doel van het hebben van twee vensters is dat u voldoende tijd hebt om onderhoud te starten en uw VM opnieuw op te opstarten, terwijl u weet wanneer Azure automatisch onderhoud start.

U kunt de Azure Portal, Power shell, de REST API en de Azure CLI gebruiken om een onderhouds venster voor virtuele-machine schaal sets Vm's te zoeken en om selfservice onderhoud te starten.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Moet u onderhoud starten tijdens het selfservice venster?  

De volgende richt lijnen kunnen u helpen beslissen of u het onderhoud op een door u gekozen tijdstip wilt starten.

> [!NOTE] 
> Het onderhoud van self-service is mogelijk niet beschikbaar voor al uw virtuele machines. Als u wilt bepalen of proactieve herimplementatie beschikbaar is voor uw virtuele machine, zoekt u naar **nu starten** in de onderhouds status. Op dit moment is self-service onderhoud niet beschikbaar voor Azure Cloud Services (Web/Worker) en Azure Service Fabric.


Self-service onderhoud wordt niet aanbevolen voor implementaties die gebruikmaken van *beschikbaarheids sets*. Beschikbaarheids sets zijn Maxi maal beschik bare instellingen waarin slechts één update domein op elk gewenst moment wordt beïnvloed. Voor beschikbaarheids sets:

- Laat Azure het onderhoud activeren. Voor onderhoud waarvoor opnieuw opstarten is vereist, wordt onderhoud uitgevoerd op het update domein. Update domeinen hoeven het onderhoud niet opeenvolgend te ontvangen. Er is een onderbreking van 30 minuten tussen de update domeinen.
- Als een tijdelijk verlies van een deel van uw capaciteit (1/update domein) een probleem is, kunt u eenvoudig het verlies compenseren door extra instanties toe te wijzen tijdens de onderhouds periode.
- Voor onderhoud waarbij niet opnieuw moet worden opgestart, worden updates toegepast op het niveau van het fout domein. 
    
Gebruik self-service onderhoud **niet** in de volgende scenario's: 

- Als u uw virtuele machines regel matig afsluit, gebruikt u DevTest Labs met behulp van automatisch afsluiten of volgt u een schema. Bij self-service onderhoud in deze scenario's kan de onderhouds status worden hersteld en wordt er extra uitval tijd in beslag.
- Op korte en gelaagde Vm's die u weet, worden vóór het einde van de onderhouds Golf verwijderd. 
- Voor werk belastingen met een grote status die is opgeslagen in de lokale (tijdelijke) schijf die u na het bijwerken wilt behouden. 
- Als u de grootte van uw virtuele machine vaak wijzigt. Dit scenario kan de onderhouds status herstellen. 
- Als u geplande gebeurtenissen hebt aangenomen die proactieve failover mogelijk maken of als u de werk belasting 15 minuten zonder problemen hebt afgesloten, wordt het afsluiten van het onderhoud gestart.

**Gebruik Self** -service onderhoud als u van plan bent om uw virtuele machine tijdens de geplande onderhouds fase zonder onderbreking uit te voeren en geen van de voor gaande counterindications van toepassing is. 

Het is raadzaam selfservice onderhoud in de volgende gevallen te gebruiken:

- U moet een exact onderhouds venster met beheer of uw klant communiceren. 
- U moet het onderhoud op een specifieke datum volt ooien. 
- U moet de volg orde van onderhoud, bijvoorbeeld in een toepassing met meerdere lagen, beheren om veilig herstel te garanderen.
- U hebt meer dan 30 minuten aan VM-herstel tijd tussen twee update domeinen nodig. Als u de tijd tussen de update domeinen wilt bepalen, moet u onderhoud voor uw virtuele machines één update domein per keer activeren.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Schaal sets voor virtuele machines weer geven die worden beïnvloed door onderhoud in de portal

Wanneer een geplande onderhouds Golf is gepland, kunt u de lijst met virtuele-machine schaal sets weer geven die worden beïnvloed door de aanstaande onderhouds Golf door gebruik te maken van de Azure Portal. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links **alle services** en selecteer vervolgens virtuele- **machine schaal sets**.
3. Selecteer onder **schaal sets voor virtuele machines** de optie **kolommen bewerken** om de lijst met beschik bare kolommen te openen.
4. Selecteer in de sectie **beschik bare kolommen** de optie **self-service onderhoud** en verplaats deze naar de lijst met **geselecteerde kolommen** . Selecteer **Toepassen**.  

    Als u het **selfservice-onderhouds** item eenvoudiger wilt kunnen vinden, kunt u de vervolg keuzelijst in de sectie **beschik bare kolommen** wijzigen van **alle** in **Eigenschappen**.

De kolom **self-service onderhoud** wordt nu weer gegeven in de lijst met virtuele-machine schaal sets. Elke schaalset voor virtuele machines kan een van de volgende waarden hebben voor de kolom selfservice onderhoud:

| Waarde | Beschrijving |
|-------|-------------|
| Ja | Ten minste één VM in de schaalset voor virtuele machines bevindt zich in een self-service venster. U kunt onderhoud starten op elk gewenst moment tijdens dit selfservice venster. | 
| No | Er bevinden zich geen Vm's in een self-service venster in de desbetreffende schaalset voor virtuele machines. | 
| - | Uw schaal sets voor virtuele machines maken geen deel uit van een geplande onderhouds Golf.| 

## <a name="notification-and-alerts-in-the-portal"></a>Meldingen en waarschuwingen in de portal

Azure communiceert een planning voor gepland onderhoud door een e-mail bericht te verzenden naar de eigenaar van het abonnement en de groep mede-eigen aars. U kunt ontvangers en kanalen aan deze communicatie toevoegen door waarschuwingen voor activiteiten logboeken te maken. Zie [abonnements activiteiten bewaken met het Azure-activiteiten logboek](../azure-monitor/essentials/platform-logs-overview.md)voor meer informatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links de optie **monitor**. 
3. Selecteer in het deel venster **monitor-waarschuwingen (klassiek)** **+ waarschuwing voor activiteiten logboek toevoegen**.
4. Selecteer op de pagina **waarschuwing voor activiteiten logboek toevoegen** de gevraagde informatie of voer deze in. Zorg er in **criteria** voor dat u de volgende waarden instelt:
   - **Gebeurtenis categorie**: Selecteer **service Health**.
   - **Services**: Selecteer **Virtual Machine Scale sets en virtual machines**.
   - **Type**: Selecteer **gepland onderhoud**. 
    
Zie [waarschuwingen voor activiteiten](../azure-monitor/alerts/activity-log-alerts.md) logboeken maken voor meer informatie over het configureren van waarschuwingen voor activiteiten Logboeken.
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Onderhoud starten op de schaalset voor virtuele machines vanuit de portal

Meer informatie over onderhoud vindt u in het overzicht van virtuele-machine schaal sets. Als ten minste één virtuele machine in de schaalset voor virtuele machines is opgenomen in de geplande onderhoud Golf, wordt er een nieuw lint aan de bovenkant van de pagina toegevoegd. Selecteer het lint met meldingen om naar de **onderhouds** pagina te gaan. 

Op de pagina **onderhoud** kunt u zien welke VM-instantie wordt beïnvloed door het geplande onderhoud. Als u onderhoud wilt starten, schakelt u het selectie vakje in dat overeenkomt met de betreffende VM. Selecteer vervolgens  **Onderhoud starten**.

Nadat u het onderhoud hebt gestart, onderneemt de betrokken Vm's in de schaalset voor virtuele machines onderhoud en zijn ze tijdelijk niet beschikbaar. Als u het self-service venster hebt gemist, kunt u nog steeds het tijd venster zien wanneer de schaalset van de virtuele machine wordt onderhouden door Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>Onderhouds status controleren met behulp van Power shell

U kunt Azure PowerShell gebruiken om te zien wanneer Vm's in uw schaal sets voor virtuele machines zijn gepland voor onderhoud. Informatie over gepland onderhoud is beschikbaar met behulp van de cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) wanneer u de `-InstanceView` para meter gebruikt.
 
Onderhouds informatie wordt alleen geretourneerd als het onderhoud is gepland. Als er geen onderhoud wordt gepland die van invloed is op het VM-exemplaar, retourneert de cmdlet geen onderhouds informatie. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

De volgende eigenschappen worden geretourneerd onder **MaintenanceRedeployStatus**: 

| Waarde | Beschrijving   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Hiermee wordt aangegeven of u op dit moment onderhoud kunt starten op de virtuele machine. |
| PreMaintenanceWindowStartTime         | Het begin van het onderhouds self-service venster wanneer u onderhoud op uw virtuele machine kunt starten. |
| PreMaintenanceWindowEndTime           | Het einde van het onderhouds self-service venster wanneer u onderhoud op uw virtuele machine kunt starten. |
| MaintenanceWindowStartTime            | Het begin van het onderhoud waarbij Azure onderhoud initieert op uw virtuele machine. |
| MaintenanceWindowEndTime              | Het einde van het geplande onderhouds venster waarin Azure onderhoud initieert op uw virtuele machine. |
| LastOperationResultCode               | Het resultaat van de laatste poging om onderhoud op de virtuele machine te initiëren. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Onderhoud starten in uw VM-exemplaar met behulp van Power shell

U kunt onderhoud starten op een VM als **IsCustomerInitiatedMaintenanceAllowed** is ingesteld op **True**. Gebruik de cmdlet [set-AzVmss](/powershell/module/az.compute/set-azvmss) met de `-PerformMaintenance` para meter.

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>Onderhouds status controleren met behulp van de CLI

U kunt geplande onderhouds informatie weer geven met [AZ vmss list-instances](/cli/azure/vmss#az-vmss-list-instances).
 
Onderhouds informatie wordt alleen geretourneerd als het onderhoud is gepland. Als er geen onderhoud wordt uitgevoerd die van invloed is op het VM-exemplaar, wordt er geen onderhouds informatie weer gegeven. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

De volgende eigenschappen worden geretourneerd onder **MaintenanceRedeployStatus** voor elk VM-exemplaar: 

| Waarde | Beschrijving   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Hiermee wordt aangegeven of u op dit moment onderhoud kunt starten op de virtuele machine. |
| PreMaintenanceWindowStartTime         | Het begin van het onderhouds self-service venster wanneer u onderhoud op uw virtuele machine kunt starten. |
| PreMaintenanceWindowEndTime           | Het einde van het onderhouds self-service venster wanneer u onderhoud op uw virtuele machine kunt starten. |
| MaintenanceWindowStartTime            | Het begin van het onderhoud waarbij Azure onderhoud initieert op uw virtuele machine. |
| MaintenanceWindowEndTime              | Het einde van het geplande onderhouds venster waarin Azure onderhoud initieert op uw virtuele machine. |
| LastOperationResultCode               | Het resultaat van de laatste poging om onderhoud op de virtuele machine te initiëren. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Onderhoud starten op uw VM-exemplaar met behulp van de CLI

De volgende aanroep initieert onderhoud voor een VM-exemplaar als deze `IsCustomerInitiatedMaintenanceAllowed` is ingesteld op **waar**:

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Veelgestelde vragen

**V: Waarom moet u nu mijn Vm's opnieuw opstarten?**

**A:** Hoewel de meeste updates en upgrades naar het Azure-platform niet van invloed zijn op de beschik baarheid van virtuele machines, kunnen we in sommige gevallen de Vm's die worden gehost in azure, niet voor komen. We hebben verschillende wijzigingen verzameld die ons nodig hebben om de servers opnieuw op te starten. Dit zal resulteren in het opnieuw opstarten van de VM.

**V: als ik de aanbevelingen voor hoge Beschik baarheid volg met een beschikbaarheidsset, ben ik dan veilig?**

**A:** Virtuele machines die zijn geïmplementeerd in een beschikbaarheidsset of in virtuele-machine schaal sets gebruiken update domeinen. Bij het uitvoeren van onderhoud voldoet Azure aan de beperking van het update domein en worden de Vm's van een ander update domein (binnen dezelfde beschikbaarheidsset) niet opnieuw opgestart. Azure wacht nog ten minste 30 minuten voordat u overstapt op de volgende groep Vm's. 

Zie [regio's en beschik baarheid voor virtuele machines in azure](../virtual-machines/availability.md)voor meer informatie over maximale Beschik baarheid.

**V: hoe kan ik een melding ontvangen over gepland onderhoud?**

**A:** Een geplande onderhouds Golf wordt gestart door een planning in te stellen voor een of meer Azure-regio's. Binnenkort nadat wordt een e-mail melding verzonden naar de abonnements beheerders, mede beheerders, eigen aren en mede werkers (één e-mail per abonnement). Aanvullende kanalen en ontvangers voor deze melding kunnen worden geconfigureerd met behulp van waarschuwingen voor activiteiten Logboeken. Als u een virtuele machine implementeert in een regio waar gepland onderhoud al is gepland, ontvangt u de melding niet. Controleer in plaats daarvan de onderhouds status van de virtuele machine.

**V: Ik zie geen indicatie van gepland onderhoud in de portal, Power shell of de CLI. Wat is er aan de hand?**

**A:** Informatie met betrekking tot gepland onderhoud is alleen beschikbaar tijdens een geplande onderhouds Golf voor de virtuele machines die door het geplande onderhoud worden beïnvloed. Als u geen gegevens ziet, is het mogelijk dat de Wave van het onderhoud al is voltooid (of niet is gestart) of dat uw virtuele machine al is gehost op een bijgewerkte server.

**V: is er een manier om precies te weten wanneer mijn virtuele machine wordt beïnvloed?**

**A:** Wanneer we het schema instellen, definiëren we een tijd venster van enkele dagen. De exacte volgorde van servers (en virtuele machines) binnen dit venster is onbekend. Als u de exacte tijd wilt weten dat uw Vm's worden bijgewerkt, kunt u [geplande gebeurtenissen](../virtual-machines/windows/scheduled-events.md)gebruiken. Wanneer u geplande gebeurtenissen gebruikt, kunt u een query uitvoeren vanuit de virtuele machine en een melding van 15 minuten ontvangen voordat een VM opnieuw wordt opgestart.

**V: hoe lang duurt het om mijn VM opnieuw op te starten?**

**A:**  Afhankelijk van de grootte van uw virtuele machine kan het enkele minuten duren voordat de computer opnieuw wordt opgestart. Tijdens het opnieuw opstarten van Azure in het geplande onderhouds venster duurt het normaal gesp roken ongeveer 25 minuten. Als u Cloud Services (Web/Worker-rol), schaal sets voor virtuele machines of beschikbaarheids sets gebruikt, krijgt u 30 minuten tussen elke groep Vm's (update domein) tijdens het geplande onderhouds venster. 

**V: Ik zie geen onderhouds informatie op mijn Vm's. Wat is er verkeerd gegaan?**

**A:** Er zijn verschillende redenen waarom u mogelijk geen onderhouds informatie ziet op uw Vm's:
   - U gebruikt een abonnement dat is gemarkeerd als *interne micro soft*.
   - Uw Vm's zijn niet gepland voor onderhoud. Het kan zijn dat de onderhouds Golf is beëindigd, is geannuleerd of is gewijzigd, zodat de virtuele machines niet meer worden beïnvloed door de toepassing.
   - U beschikt niet over de kolom **onderhoud** die is toegevoegd aan de lijst weergave van de virtuele machine. Hoewel deze kolom aan de standaard weergave is toegevoegd, moet u de kolom **onderhoud** hand matig toevoegen aan de lijst weergave van de virtuele machine als u uw weer gave zo configureert dat niet-standaard kolommen worden weer gegeven.

**V: mijn VM is voor de tweede keer gepland voor onderhoud. Waarom?**

**A:** In verschillende use cases is uw VM gepland voor onderhoud nadat u uw onderhoud al hebt voltooid en opnieuw hebt geïmplementeerd:
   - De onderhouds Golf is geannuleerd en opnieuw gestart met een andere nettolading. Het kan zijn dat er een beschadigde Payload is gedetecteerd en dat er alleen een extra Payload moet worden geïmplementeerd.
   - De virtuele machine is op een ander knoop punt *retoucheerd* vanwege een hardwarefout.
   - U hebt ervoor gekozen om te stoppen (toewijzing ongedaan te maken) en de virtuele machine opnieuw op te starten.
   - U hebt **automatisch afsluiten** ingeschakeld voor de virtuele machine.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u zich kunt registreren voor onderhouds gebeurtenissen vanuit de VM met behulp van [geplande gebeurtenissen](../virtual-machines/windows/scheduled-events.md).
