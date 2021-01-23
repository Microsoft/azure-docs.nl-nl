---
title: 'Zelfstudie: configuratie van virtuele Windows-machines beheren in Azure'
description: In deze zelfstudie leert u hoe u wijzigingen identificeert en pakketupdates op een virtuele Windows-machine beheert
author: cynthn
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 12/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b28d7ee5d2eeb1015695e32e5918bd94f9051050
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736665"
---
# <a name="tutorial-monitor-changes-and-update-a-windows-virtual-machine-in-azure"></a>Zelfstudie: Wijzigingen bewaken en een virtuele Windows-machine bijwerken in Azure

Met [Wijzigingen bijhouden](../../automation/change-tracking/overview.md) en [Updatebeheer](../../automation/update-management/overview.md) van Azure kunt u eenvoudig wijzigingen in uw virtuele Windows-machines in Azure identificeren respectievelijk updates van besturingssystemen voor deze VM's beheren.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Windows-updates beheren.
> * Wijzigingen en inventaris bewaken.

## <a name="open-azure-cloud-shell"></a>Azure Cloud Shell openen

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw Azure-account.

Als u een codeblok in Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van het codeblok.

Als u naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) gaat, kunt u Cloud Shell ook openen in een afzonderlijk browsertabblad. Selecteer **Kopiëren** om codeblokken te kopiëren en ze op het tabblad Cloud Shell te plakken. Selecteer Enter om de code uit te voeren.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Voor het configureren van Azure-bewaking en updatebeheer in deze zelfstudie hebt u een Windows-VM in Azure nodig.

Stel eerst een beheerdersnaam en -wachtwoord in voor de virtuele machine met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Maak nu de VM met [New-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voorbeeld wordt een VM met de naam `myVM` gemaakt op de locatie `East US`. Als ze niet al bestaan, worden de resourcegroep `myResourceGroupMonitor` en ondersteunende netwerkresources gemaakt:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Het duurt enkele minuten voordat de bronnen en virtuele machine zijn gemaakt.

## <a name="manage-windows-updates"></a>Windows-updates beheren

Met Updatebeheer kunt u updates en patches beheren voor de Azure Windows-VM's. Rechtstreeks vanuit uw VM kunt u snel het volgende doen:

- De status van beschikbare updates evalueren.
- De installatie van vereiste updates plannen.
- De resultaten van de implementatie bekijken om te controleren of de updates op de VM zijn toegepast.

Zie [Automation-prijzen voor Updatebeheer](https://azure.microsoft.com/pricing/details/automation/) voor prijsinformatie.

### <a name="enable-update-management"></a>Updatebeheer inschakelen

Updatebeheer inschakelen voor de VM:

1. Selecteer aan de linkerkant van het venster **Virtuele machines**.
1. Kies een VM in de lijst.
1. Selecteer in het deelvenster **Bewerkingen** van het VM-venster de optie **Updatebeheer**.
1. Het venster **Updatebeheer inschakelen** wordt geopend.

Er wordt een validatie uitgevoerd om te bepalen of Updatebeheer is ingeschakeld voor deze VM. De validatie bevat controles voor een Log Analytics-werkruimte en een gekoppeld Automation-account en controleert of de oplossing zich in de werkruimte bevindt.

Een [Log Analytics](../../azure-monitor/log-query/log-query-overview.md)-werkruimte wordt gebruikt om gegevens te verzamelen die worden gegenereerd door functies en services, zoals Updatebeheer. De werkruimte biedt één locatie om gegevens uit meerdere bronnen te bekijken en te analyseren.

Als u aanvullende acties wilt uitvoeren op VM's waarvoor updates zijn vereist, kunt u Azure Automation gebruiken om runbooks uit te voeren op VM's. Dergelijke acties omvatten het downloaden of toepassen van updates.

Tijdens het validatieproces wordt ook gecontroleerd of de VM is ingericht met Microsoft Monitoring Agent (MMA) en Automation Hybrid Runbook Worker. U gebruikt de agent om te communiceren met de VM en om informatie op te vragen over de status van de update.

Kies in het venster **Updatebeheer inschakelen** de Log Analytics-werkruimte en het Automation-account en selecteer **Inschakelen**. Het duurt maximaal vijftien minuten om Updatebeheer in te schakelen.

De volgende vereiste onderdelen na de onboarding eventueel ontbreken, worden automatisch toegevoegd:

* [Log Analytics](../../azure-monitor/log-query/log-query-overview.md)-werkruimte
* [Automation](../../automation/index.yml)
* Een [Hybride runbook worker](../../automation/automation-hybrid-runbook-worker.md) die op de VM is ingeschakeld

Nadat de oplossing is ingeschakeld, wordt het venster **Updatebeheer** geopend. Configureer de locatie, de Log Analytics-werkruimte en het te gebruiken Automation-account en selecteer **Inschakelen**. Als de opties grijs worden weergegeven, betekent dit dat er een andere automatiseringsoplossing voor de VM wordt ingeschakeld en dat de werkruimte en het Automation-account van de oplossing moeten worden gebruikt.

![Oplossing voor Updatebeheer inschakelen](./media/tutorial-monitoring/manageupdates-update-enable.png)

Het kan vijftien minuten duren voordat Updatebeheer is ingeschakeld. Sluit gedurende deze tijd het browservenster niet. Nadat de oplossing is ingeschakeld, wordt informatie over ontbrekende updates op de VM naar Azure Monitor-logboeken verzonden. Het kan 30 minuten tot 6 uur duren voordat de gegevens beschikbaar zijn voor analyse.

### <a name="view-an-update-assessment"></a>Een update-evaluatie bekijken

Als Updatebeheer is ingeschakeld, wordt het venster **Updatebeheer** weergegeven. Nadat de evaluatie van updates is voltooid, ziet u een lijst met ontbrekende updates op het tabblad **Ontbrekende updates**.

 ![Updatestatus bekijken](./media/tutorial-monitoring/manageupdates-view-status-win.png)

### <a name="schedule-an-update-deployment"></a>Een update-implementatie plannen

Als u updates wilt installeren, plant u een implementatie na uw release-planning en servicevenster. U kiest welke typen updates in de implementatie moeten worden opgenomen. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

Als u een nieuwe update-implementatie voor de VM wilt plannen, selecteert u **Update-implementatie plannen** boven aan het venster **Updatebeheer**. Geef de volgende gegevens op in het venster **Nieuwe update-implementatie**:

| Optie | Beschrijving |
| --- | --- |
| **Naam** |Voer een unieke naam in om de update-implementatie te identificeren. |
|**Besturingssysteem**| Selecteer **Linux** of **Windows**.|
| **Bij te werken groepen** |Voor VM's die in Azure worden gehost, definieert u een query op basis van een combinatie van abonnement, resourcegroepen, locaties en tags. Met deze query wordt een dynamische groep met door Azure gehoste VM's opgebouwd die in uw implementatie moeten worden opgenomen. </br></br>Voor VM's die niet in Azure worden gehost, selecteert u een bestaande opgeslagen zoekopdracht. Met deze zoekopdracht kunt u een groep van deze VM's selecteren die in de implementatie moet worden meegenomen. </br></br> Zie [Dynamische groepen](../../automation/update-management/configure-groups.md) voor meer informatie.|
| **Bij te werken machines** |Selecteer **Opgeslagen zoekopdracht**, **Geïmporteerde groep** of **Machines**.<br/><br/>Als u **Machines**  selecteert, kunt u in de vervolgkeuzelijst uit afzonderlijke machines kiezen. De gereedheid van elke machine wordt weergegeven in de kolom **GEREEDHEID VOOR UPDATE-AGENT** van de tabel.</br></br> Zie [Computergroepen in Azure Monitorlogboeken](../../azure-monitor/platform/computer-groups.md) voor meer informatie over de verschillende manieren waarop u computergroepen kunt maken in Azure Monitor-logboeken |
|**Updateclassificaties**|Kies alle benodigde updateclassificaties.|
|**Updates opnemen/uitsluiten**|Selecteer deze optie om het deelvenster **Opnemen/uitsluiten** te openen. Updates die moeten worden opgenomen en die moeten worden uitgesloten, worden op afzonderlijke tabbladen weergegeven. Zie [Een update-implementatie plannen](../../automation/update-management/deploy-updates.md#schedule-an-update-deployment) voor meer informatie over het verwerken van de opname. |
|**Instellingen voor planning**|Kies het tijdstip waarop u wilt beginnen en selecteer **Eenmaal**, of **Terugkerend**.|
| **Scripts vooraf en scripts achteraf**|Kies de scripts die vóór en na de implementatie moeten worden uitgevoerd.|
| **Onderhoudsvenster** | Voer het aantal minuten in dat voor updates is ingesteld. Geldige waarden variëren van 30 tot 360 minuten. |
| **Besturingselement voor opnieuw opstarten**| Selecteer hoe opnieuw opstarten wordt afgehandeld. Beschikbare selecties zijn:<ul><li>**Opnieuw opstarten indien nodig**</li><li>**Altijd opnieuw opstarten**</li><li>**Nooit opnieuw opstarten**</li><li>**Alleen opnieuw opstarten**</li></ul>**Opnieuw opstarten als dit vereist**  is de standaardselectie. Als u **Alleen opnieuw opstarten** selecteert, worden er geen updates geïnstalleerd.|

Nadat u klaar bent met het configureren van de planning, klikt u op **Maken** om naar het statusdashboard terug te gaan. De tabel **Gepland** toont de implementatieplanning die u hebt gemaakt.

U kunt ook programmatisch update-implementaties maken. Zie [Software Update Configurations - Create](/rest/api/automation/softwareupdateconfigurations/create) (software-updateconfiguraties: maken) als u wilt leren hoe u een update-implementatie kunt maken met de REST-API. Er is ook een voorbeeldrunbook dat u kunt gebruiken om een wekelijkse update-implementatie te maken. Zie voor meer informatie over dit runbook [Create a weekly update deployment for one or more VMs in a resource group](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1) (een wekelijkse update-implementatie maken voor een of meer VM's in een resourcegroep).

### <a name="view-results-of-an-update-deployment"></a>Resultaten van een update-implementatie weergeven

Nadat de geplande implementatie is gestart, ziet u de implementatiestatus op het tabblad **Update-implementaties** in het venster **Updatebeheer**.

Als de implementatie wordt uitgevoerd, wordt de status ervan weergegeven als Actief. Wanneer de implementatie is voltooid, wordt de status gewijzigd in Geslaagd. Als er updates in de implementatie zijn mislukt, wordt de status weergegeven als Gedeeltelijk mislukt.

Selecteer de voltooide update-implementatie om het dashboard voor die implementatie te bekijken.

![Statusdashboard voor update-implementatie voor specifieke implementatie](./media/tutorial-monitoring/manageupdates-view-results.png)

Op de tegel **Updateresultaten** ziet u een overzicht van het totale aantal updates en van de implementatieresultaten op de VM. In de tabel aan de rechterkant vindt u gedetailleerde informatie over elke update en het resultaat van de installatie. Elk resultaat heeft een van de volgende waarden:

* **Niet geprobeerd**: De update is niet geïnstalleerd. Er was onvoldoende tijd beschikbaar op basis van de gedefinieerde duur van het onderhoudsvenster.
* **Geslaagd**: De update is bijgewerkt.
* **Mislukt**: De update is mislukt.

Selecteer **Alle logboeken** als u alle logboekvermeldingen wilt zien die tijdens de implementatie zijn gemaakt.

Selecteer de tegel **Uitvoer** als u de taakstroom wilt bekijken van het runbook dat verantwoordelijk is voor het beheer van de implementatie van de updates op de doel-VM.

Selecteer **Fouten** voor gedetailleerde informatie over eventuele implementatiefouten.

## <a name="monitor-changes-and-inventory"></a>Wijzigingen en inventaris bewaken

U kunt inventarisgegevens verzamelen en weergeven voor de software, bestanden, Linux-daemons, Windows-services en Windows-registersleutels op uw computers. Door de configuraties van uw computer bij te houden,kunt u gemakkelijker vaststellen waar zich in uw omgeving operationele problemen voordoen, en kunt u beter beoordelen wat de status van uw computers is.

### <a name="enable-change-and-inventory-management"></a>Wijzigings- en inventarisbeheer inschakelen

Wijzigings- en inventarisbeheer inschakelen voor de VM:

1. Selecteer aan de linkerkant van het venster **Virtuele machines**.
1. Kies een VM in de lijst.
1. Selecteer onder **Bewerkingen** in het VM-venster de optie **Inventaris** of **Wijzigingen bijhouden**.
1. De pagina **Wijzigingen bijhouden en Inventaris inschakelen** wordt weergegeven.

Configureer de locatie, de Log Analytics-werkruimte en het te gebruiken Automation-account en selecteer **Inschakelen**. Als de opties grijs worden weergegeven, is er al een automatiseringsoplossing ingeschakeld voor de VM. In dit geval moeten de werkruimte en het Automation-account worden gebruikt die al zijn ingeschakeld.

Hoewel de oplossingen afzonderlijk in het menu worden weergegeven, is het dezelfde oplossing. Als u er één inschakelt, worden beide ingeschakeld voor de VM.

![Bijhouden van wijzigingen en inventaris inschakelen](./media/tutorial-monitoring/manage-inventory-enable.png)

Nadat de oplossing is ingeschakeld, kan het enige tijd duren voordat de gegevens zichtbaar zijn terwijl de inventaris wordt verzameld op de VM.

### <a name="track-changes"></a>Wijzigingen bijhouden

Selecteer op de VM onder **BEWERKINGEN** de optie **Wijzigingen bijhouden** en selecteer vervolgens **Instellingen bewerken**. Het deelvenster **Wijzigingen bijhouden** wordt geopend. Selecteer het type instelling dat u wilt bijhouden en selecteer **+ Toevoegen** om de instellingen te configureren.

De beschikbare instellingsopties voor Windows zijn:

* Windows-register
* Windows-bestanden

Zie [Problemen met wijzigingen op een VM oplossen](../../automation/automation-tutorial-troubleshoot-changes.md) voor gedetailleerde informatie over Wijzigingen bijhouden.

### <a name="view-inventory"></a>Inventaris weergeven

Selecteer op de VM de optie **Inventaris** onder **BEWERKINGEN**. Op het tabblad **Software** wordt een tabel weergegeven met de software die is gevonden. De algemene details voor elke softwarerecord worden in de tabel weergegeven. Deze details omvatten de naam, versie, uitgever en laatste tijdstip van vernieuwen voor de software.

![Inventaris weergeven](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>Activiteitenlogboeken en wijzigingen bewaken

Selecteer in het venster **Wijzigingen bijhouden** op de VM de optie **Verbinding met activiteitenlogboek beheren** om het deelvenster **Azure-activiteitenlogboek** te openen. Selecteer **Verbinding maken** om Wijzigingen bijhouden te verbinden met het Azure-activiteitenlogboek voor uw VM.

Nadat Wijzigingen bijhouden is ingeschakeld, gaat u naar het deelvenster **Overzicht** voor de VM en selecteert u **Stoppen** om de VM te stoppen. Wanneer u daarom wordt gevraagd, selecteert u **Ja** om de virtuele machine te stoppen. Wanneer de toewijzing van de VM ongedaan is gemaakt, selecteert u **Starten** om de VM opnieuw op te starten.

Wanneer een VM wordt gestart en gestopt, wordt er een gebeurtenis geregistreerd in het activiteitenlogboek. Ga terug naar de pagina **Wijzigingen bijhouden** en selecteer het tabblad **Gebeurtenissen** onder aan het deelvenster. Na een tijdje worden de gebeurtenissen weergegeven in de grafiek en in de tabel. U kunt elke gebeurtenis selecteren om gedetailleerde informatie over die gebeurtenis weer te geven.

![Wijzigingen in het activiteitenlogboek weergeven](./media/tutorial-monitoring/manage-activitylog-view-results.png)

In de vorige grafiek worden wijzigingen getoond die in de loop der tijd hebben plaatsgevonden. Nadat u een verbinding met een Azure-activiteitenlogboek hebt toegevoegd, toont de lijngrafiek bovenaan gebeurtenissen uit het Azure-actitviteitenlogboek.

Elke rij in het staafdiagram vertegenwoordigt een ander type wijziging dat kan worden gevolgd. Deze typen zijn Linux-daemons, bestanden, Windows-registersleutels, software en Windows-services. Op het tabblad **Wijzigen** worden de wijzigingsgegevens weergegeven. Wijzigingen worden weergegeven in de volgorde waarin ze zich voordoen, waarbij de meest recente wijziging het eerst wordt weergegeven.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u Wijzigingen bijhouden en Updatebeheer voor uw virtuele machine geconfigureerd en gecontroleerd. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een resourcegroep en een VM maakt.
> * Windows-updates beheert.
> * Wijzigingen en inventaris bewaakt.

Ga naar de volgende zelfstudie voor meer informatie over het bewaken van uw VM.

> [!div class="nextstepaction"]
> [Virtuele machines bewaken](tutorial-monitor.md)
