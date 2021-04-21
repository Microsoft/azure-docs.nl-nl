---
title: Onderhoudsmeldingen voor virtuele-machineschaalsets in Azure
description: Bekijk onderhoudsmeldingen en start selfserviceonderhoud voor virtuele-machineschaalsets in Azure.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: maintenance-control
ms.date: 11/12/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: ec8d211bd25eb04f9e000af950cea9a28a0d1874
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762835"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>Meldingen voor gepland onderhoud voor virtuele-machineschaalsets


Azure voert regelmatig updates uit om de betrouwbaarheid, prestaties en beveiliging van de hostinfrastructuur voor virtuele machines (VM's) te verbeteren. Updates kunnen bestaan uit het patchen van de hostingomgeving of het upgraden en buiten gebruik stellen van hardware. De meeste updates hebben geen invloed op de gehoste VM's. Updates zijn echter van invloed op VM's in deze scenario's:

- Als voor het onderhoud niet opnieuw moet worden opgestart, wordt de VM enkele seconden onderbroken terwijl de host wordt bijgewerkt. Deze typen onderhoudsbewerkingen worden op foutdomein toegepast op foutdomein. De voortgang wordt gestopt als er waarschuwingswaarschuwingen worden ontvangen.

- Als voor onderhoud opnieuw moet worden opgestart, krijgt u een melding wanneer het onderhoud is gepland. In dergelijke gevallen krijgt u een tijdvenster dat doorgaans 35 dagen is waarin u het onderhoud zelf kunt starten, wanneer het voor u werkt.


Gepland onderhoud dat opnieuw moet worden opgestart, wordt gepland in golven. Elke golf heeft een ander bereik (regio's):

- Een golf begint met een melding aan klanten. Standaard wordt een melding verzonden naar de eigenaar en mede-eigenaren van het abonnement. U kunt ontvangers en berichtenopties, zoals e-mail, sms en webhooks, toevoegen aan de meldingen met behulp van Waarschuwingen voor [Azure-activiteitenlogboek.](../azure-monitor/essentials/platform-logs-overview.md)  
- Met de melding wordt *een selfservicevenster* beschikbaar gesteld. Tijdens dit venster, dat doorgaans 35 dagen duurt, kunt u zien welke van uw VM's zijn opgenomen in de golf. U kunt het onderhoud proactief starten op basis van uw eigen planningsbehoeften.
- Na het selfservicevenster wordt *een gepland onderhoudsvenster* gestart. Op een bepaald moment in dit venster wordt het vereiste onderhoud door Azure gepland en toegepast op uw VM. 

Het doel van twee vensters is om u voldoende tijd te geven om onderhoud te starten en uw VM opnieuw op te starten terwijl u weet wanneer het onderhoud automatisch wordt uitgevoerd in Azure.

U kunt de Azure Portal, PowerShell, de REST API en de Azure CLI gebruiken om onderhoudsvensters op te vragen voor uw virtuele-machineschaalset-VM's en om selfserviceonderhoud te starten.

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>Moet u onderhoud starten tijdens het selfservicevenster?  

Aan de hand van de volgende richtlijnen kunt u bepalen of u onderhoud wilt starten op een tijdstip dat u kiest.

> [!NOTE] 
> Selfserviceonderhoud is mogelijk niet beschikbaar voor al uw VM's. Als u wilt bepalen of proactief opnieuw uitvoeren beschikbaar is voor uw VM, gaat u naar **Nu starten** in de onderhoudsstatus. Op dit moment is selfserviceonderhoud niet beschikbaar voor Azure Cloud Services (web-/werkrol) en Azure Service Fabric.


Selfserviceonderhoud wordt niet aanbevolen voor implementaties die gebruikmaken van *beschikbaarheidssets.* Beschikbaarheidssets zijn instellingen met hoge beschikbaarheid waarin slechts één updatedomein op elk moment wordt beïnvloed. Voor beschikbaarheidssets:

- Laat Azure het onderhoud activeren. Voor onderhoud dat opnieuw moet worden opgestart, wordt onderhoud uitgevoerd op updatedomein per updatedomein. Updatedomeinen ontvangen het onderhoud niet per se opeenvolgend. Er is een onderbreking van 30 minuten tussen updatedomeinen.
- Als een tijdelijk verlies van een deel van uw capaciteit (aantal 1/updatedomein) een probleem is, kunt u het verlies eenvoudig compenseren door extra instanties toe tewijsen tijdens de onderhoudsperiode.
- Voor onderhoud waarvoor niet opnieuw hoeft te worden opgestart, worden updates toegepast op het niveau van het foutdomein. 
    
**Gebruik in de volgende** scenario's geen selfserviceonderhoud: 

- Als u uw VM's regelmatig afsluit, hetzij handmatig, met behulp van DevTest Labs, met behulp van automatisch afsluiten of volgens een schema. Selfserviceonderhoud in deze scenario's kan de onderhoudsstatus terugdraaien en leiden tot extra downtime.
- Op kortstondige VM's die u kent, worden vóór het einde van de onderhoudsgolf verwijderd. 
- Voor werkbelastingen met een grote status die is opgeslagen op de lokale (kortstondige) schijf die u na de update wilt onderhouden. 
- Als u de VM vaak het 100-00-30-2018-2018- In dit scenario kan de onderhoudsstatus worden terugdraaien. 
- Als u geplande gebeurtenissen hebt aangenomen die proactieve failover of een goede afsluiting van uw werkbelasting mogelijk maken, 15 minuten voordat het onderhoud wordt afgesloten.

**Gebruik** selfserviceonderhoud als u van plan bent om uw VM ononderbroken uit te voeren tijdens de fase gepland onderhoud en geen van de voorgaande counterindicaties van toepassing is. 

In de volgende gevallen kunt u het beste selfserviceonderhoud gebruiken:

- U moet een exact onderhoudsvenster communiceren met het management of uw klant. 
- U moet het onderhoud op een specifieke datum voltooien. 
- U moet de volgorde van onderhoud bepalen, bijvoorbeeld in een toepassing met meerdere lagen, om veilig herstel te garanderen.
- U hebt meer dan 30 minuten hersteltijd nodig tussen twee updatedomeinen. Als u de tijd tussen updatedomeinen wilt beheren, moet u onderhoud in uw VM's met één updatedomein tegelijk activeren.

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>Virtuele-machineschaalsets weergeven die worden beïnvloed door onderhoud in de portal

Wanneer een geplande onderhoudsgolf is gepland, kunt u de lijst weergeven met virtuele-machineschaalsets die worden beïnvloed door de aanstaande onderhoudsgolf met behulp van de Azure Portal. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu links **Alle services en** selecteer vervolgens **Virtuele-machineschaalsets.**
3. Selecteer **onder Virtuele-machineschaalsets** **de optie Kolommen bewerken** om de lijst met beschikbare kolommen te openen.
4. Selecteer in **de sectie Beschikbare** kolommen de optie **Selfserviceonderhoud** en verplaats deze vervolgens naar de **lijst Geselecteerde** kolommen. Selecteer **Toepassen**.  

    Als u het **selfserviceonderhoudsitem** gemakkelijker te vinden wilt  maken, kunt u de vervolgkeuzeoptie in de sectie Beschikbare kolommen wijzigen van **Alle** in **Eigenschappen.**

De **kolom Selfserviceonderhoud** wordt nu weergegeven in de lijst met virtuele-machineschaalsets. Elke virtuele-machineschaalset kan een van de volgende waarden hebben voor de kolom selfserviceonderhoud:

| Waarde | Beschrijving |
|-------|-------------|
| Ja | Ten minste één virtuele machine in uw virtuele-machineschaalset staat in een selfservicevenster. U kunt op elk moment onderhoud starten tijdens dit selfservicevenster. | 
| No | Er zijn geen VM's in een selfservicevenster in de betreffende virtuele-machineschaalset. | 
| - | Uw virtuele-machineschaalsets maken geen deel uit van een geplande onderhoudsgolf.| 

## <a name="notification-and-alerts-in-the-portal"></a>Meldingen en waarschuwingen in de portal

Azure communiceert een planning voor gepland onderhoud door een e-mail te verzenden naar de eigenaar van het abonnement en de groep mede-eigenaren. U kunt ontvangers en kanalen toevoegen aan deze communicatie door waarschuwingen voor activiteitenlogboek te maken. Zie Abonnementsactiviteiten bewaken met het [Azure-activiteitenlogboek voor meer informatie.](../azure-monitor/essentials/platform-logs-overview.md)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer Monitor in het menu **aan de linkerkant.** 
3. Selecteer in **het deelvenster Monitor - Waarschuwingen (klassiek)** de optie **+Waarschuwing voor activiteitenlogboek toevoegen.**
4. Selecteer of **voer op de pagina Waarschuwing voor activiteitenlogboek** toevoegen de gevraagde gegevens in. Zorg ervoor dat u in **Criteria** de volgende waarden in stelt:
   - **Gebeurteniscategorie:** selecteer **Service Health**.
   - **Services:** selecteer **Virtual Machine Scale Sets en Virtual Machines**.
   - **Type:** selecteer **Gepland onderhoud.** 
    
Zie Waarschuwingen voor activiteitenlogboek maken voor meer informatie over het configureren [van waarschuwingen voor activiteitenlogboek](../azure-monitor/alerts/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>Onderhoud starten voor uw virtuele-machineschaalset vanuit de portal

Meer onderhoudsgerelateerde details vindt u in het overzicht van virtuele-machineschaalsets. Als er ten minste één virtuele machine in de virtuele-machineschaalset is opgenomen in de geplande onderhoudsgolf, wordt boven aan de pagina een nieuw meldingslint toegevoegd. Selecteer het meldingenlint om naar de pagina **Onderhoud te** gaan. 

Op de **pagina** Onderhoud kunt u zien welke VM-instantie wordt beïnvloed door het geplande onderhoud. Als u onderhoud wilt starten, selecteert u het selectievakje dat overeenkomt met de betreffende VM. Selecteer vervolgens **Onderhoud starten.**

Nadat u het onderhoud hebt start, worden de betrokken VM's in uw virtuele-machineschaalset onderhouden en zijn ze tijdelijk niet beschikbaar. Als u het selfservicevenster hebt gemist, ziet u nog steeds het tijdvenster waarin uw virtuele-machineschaalset wordt onderhouden door Azure.
 
## <a name="check-maintenance-status-by-using-powershell"></a>De onderhoudsstatus controleren met behulp van PowerShell

U kunt deze Azure PowerShell om te zien wanneer VM's in uw virtuele-machineschaalsets zijn gepland voor onderhoud. Informatie over gepland onderhoud is beschikbaar met behulp van de cmdlet [Get-AzVmss](/powershell/module/az.compute/get-azvmss) wanneer u de `-InstanceView` parameter gebruikt.
 
Onderhoudsinformatie wordt alleen geretourneerd als onderhoud is gepland. Als er geen onderhoud is gepland dat van invloed is op het VM-exemplaar, retourneert de cmdlet geen onderhoudsgegevens. 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

De volgende eigenschappen worden geretourneerd onder **MaintenanceRedeployStatus:** 

| Waarde | Beschrijving   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Geeft aan of u op dit moment onderhoud aan de VM kunt starten. |
| PreMaintenanceWindowStartTime         | Het begin van het selfservicevenster voor onderhoud wanneer u onderhoud aan uw VM kunt starten. |
| PreMaintenanceWindowEndTime           | Het einde van het selfservicevenster voor onderhoud wanneer u onderhoud aan uw VM kunt starten. |
| OnderhoudWindowStartTime            | Het begin van het geplande onderhoud waarin Azure onderhoud aan uw VM initieert. |
| OnderhoudWindowEndTime              | Het einde van het geplande onderhoudsvenster waarin Azure onderhoud aan uw VM initieert. |
| LastOperationResultCode               | Het resultaat van de laatste poging om onderhoud te starten op de VM. |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>Onderhoud starten op uw VM-exemplaar met behulp van PowerShell

U kunt onderhoud starten op een VM als **IsCustomerInitiatedMaintenanceAllowed** is ingesteld op **true.** Gebruik de [cmdlet Set-AzVmss](/powershell/module/az.compute/set-azvmss) met `-PerformMaintenance` parameter .

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>De onderhoudsstatus controleren met behulp van de CLI

U kunt informatie over gepland onderhoud weergeven met [az vmss list-instances.](/cli/azure/vmss#az_vmss_list_instances)
 
Onderhoudsinformatie wordt alleen geretourneerd als onderhoud is gepland. Als er geen onderhoud is gepland dat van invloed is op het VM-exemplaar, retourneert de opdracht geen onderhoudsgegevens. 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

De volgende eigenschappen worden geretourneerd onder **MaintenanceRedeployStatus** voor elk VM-exemplaar: 

| Waarde | Beschrijving   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Geeft aan of u op dit moment onderhoud aan de VM kunt starten. |
| PreMaintenanceWindowStartTime         | Het begin van het selfservicevenster voor onderhoud wanneer u onderhoud aan uw VM kunt starten. |
| PreMaintenanceWindowEndTime           | Het einde van het selfservicevenster voor onderhoud wanneer u onderhoud aan uw VM kunt starten. |
| OnderhoudWindowStartTime            | Het begin van het geplande onderhoud waarin Azure onderhoud aan uw VM initieert. |
| OnderhoudWindowEndTime              | Het einde van het geplande onderhoudsvenster waarin Azure onderhoud aan uw VM initieert. |
| LastOperationResultCode               | Het resultaat van de laatste poging om onderhoud aan de VM te starten. |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>Onderhoud starten op uw VM-exemplaar met behulp van de CLI

Met de volgende aanroep wordt onderhoud gestart voor een VM-exemplaar als `IsCustomerInitiatedMaintenanceAllowed` is ingesteld op **true**:

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>Veelgestelde vragen

**V: Waarom moet u mijn VM's nu opnieuw opstarten?**

**A:** Hoewel de meeste updates en upgrades voor het Azure-platform geen invloed hebben op de beschikbaarheid van VM's, kunnen we in sommige gevallen niet voorkomen dat we VM's die in Azure worden gehost opnieuw opstarten. We hebben verschillende wijzigingen verzameld waarvoor we onze servers opnieuw moeten opstarten, waardoor de VM opnieuw wordt opgestart.

**V: Als ik uw aanbevelingen voor hoge beschikbaarheid volg met behulp van een beschikbaarheidsset, ben ik dan veilig?**

**A:** Virtuele machines die zijn geïmplementeerd in een beschikbaarheidsset of in virtuele-machineschaalsets gebruiken updatedomeinen. Bij het uitvoeren van onderhoud houdt Azure zich aan de beperking van het updatedomein en worden VM's niet opnieuw opgestart vanuit een ander updatedomein (binnen dezelfde beschikbaarheidsset). Azure wacht ook ten minste 30 minuten voordat het wordt verplaatst naar de volgende groep VM's. 

Zie Regio's en beschikbaarheid voor virtuele machines in Azure voor meer informatie over [hoge beschikbaarheid.](../virtual-machines/availability.md)

**V: Hoe kan ik op de hoogte worden gesteld van gepland onderhoud?**

**A:** Een geplande onderhoudsgolf begint met het instellen van een planning op een of meer Azure-regio's. Kort daarna wordt een e-mailmelding verzonden naar de abonnementsbeheerders, medebeheerders, eigenaren en bijdragers (één e-mail per abonnement). Aanvullende kanalen en ontvangers voor deze melding kunnen worden geconfigureerd met waarschuwingen voor activiteitenlogboek. Als u een virtuele machine implementeert in een regio waar gepland onderhoud al is gepland, ontvangt u geen melding. Controleer in plaats daarvan de onderhoudstoestand van de VM.

**V: Ik zie geen indicatie van gepland onderhoud in de portal, PowerShell of de CLI. Wat is er aan de hand?**

**A:** Informatie met betrekking tot gepland onderhoud is alleen beschikbaar tijdens een geplande onderhoudsgolf voor de VM's die worden beïnvloed door het geplande onderhoud. Als u geen gegevens ziet, is de onderhoudsgolf mogelijk al voltooid (of niet gestart) of wordt uw VM al gehost op een bijgewerkte server.

**V: Is er een manier om precies te weten wanneer mijn VM wordt beïnvloed?**

**A:** Wanneer we de planning instellen, definiëren we een tijdvenster van enkele dagen. De exacte volgorde van servers (en virtuele machines) binnen dit venster is onbekend. Als u precies wilt weten wanneer uw VM's worden bijgewerkt, kunt u geplande [gebeurtenissen gebruiken.](../virtual-machines/windows/scheduled-events.md) Wanneer u geplande gebeurtenissen gebruikt, kunt u vanuit de VM een query uitvoeren en een melding van 15 minuten ontvangen voordat een VM opnieuw wordt opgestart.

**V: Hoe lang duurt het om mijn VM opnieuw op te starten?**

**A:**  Afhankelijk van de grootte van uw VM kan het opnieuw opstarten enkele minuten duren tijdens het selfserviceonderhoudsvenster. Tijdens het opnieuw opstarten door Azure in het geplande onderhoudsvenster duurt het opnieuw opstarten doorgaans ongeveer 25 minuten. Als u Cloud Services (web-/werkrol), virtuele-machineschaalsets of beschikbaarheidssets gebruikt, krijgt u 30 minuten tussen elke groep VM's (updatedomein) tijdens het geplande onderhoudsvenster. 

**V: Ik zie geen onderhoudsinformatie op mijn VM's. Wat is er misgegaan?**

**A:** Er zijn verschillende redenen waarom u mogelijk geen onderhoudsinformatie op uw VM's ziet:
   - U gebruikt een abonnement dat is gemarkeerd als *Microsoft Internal*.
   - Uw VM's zijn niet gepland voor onderhoud. Het kan zijn dat de onderhoudsgolf is beëindigd, geannuleerd of gewijzigd, zodat uw VM's er niet meer door worden beïnvloed.
   - U hebt de kolom Onderhoud **niet toegevoegd** aan uw VM-lijstweergave. Hoewel we deze kolom aan de standaardweergave hebben toegevoegd, moet u, als u  uw weergave configureert om niet-standaardkolommen weer te geven, de kolom Onderhoud handmatig toevoegen aan uw VM-lijstweergave.

**V: Mijn VM is voor de tweede keer gepland voor onderhoud. Waarom?**

**A:** In verschillende gebruiksgevallen wordt uw VM gepland voor onderhoud nadat u het onderhoud al hebt voltooid en opnieuw hebt geïmplementeerd:
   - We hebben de onderhoudsgolf geannuleerd en opnieuw opgestart met een andere nettolading. Mogelijk hebben we een defecte nettolading gedetecteerd en hoeven we alleen maar een extra nettolading te implementeren.
   - Uw VM is *hersteld naar een* ander knooppunt vanwege een hardwarefout.
   - U hebt geselecteerd om de VM te stoppen (de toewijzing te herstellen) en de VM opnieuw op te starten.
   - Automatisch **afsluiten is** ingeschakeld voor de VM.

## <a name="next-steps"></a>Volgende stappen

Leer hoe u zich registreert voor onderhoudsgebeurtenissen vanuit de VM met behulp [van geplande gebeurtenissen.](../virtual-machines/windows/scheduled-events.md)
