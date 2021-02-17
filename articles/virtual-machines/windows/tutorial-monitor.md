---
title: 'Zelfstudie: virtuele Windows-machines bewaken in Azure'
description: In deze zelfstudie leert u hoe u de prestaties en gedetecteerde toepassingsonderdelen kunt bewaken die op uw virtuele Windows-machines worden uitgevoerd.
author: mgoedtel
manager: carmonm
ms.service: virtual-machines-windows
ms.subservice: monitoring
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3e7d93d7a1d0e804f340a7ce785ccc1165e93ee5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570082"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Zelfstudie: Een virtuele Windows-machine bewaken in Azure

Voor de bewaking in Azure worden agents gebruikt om opstart- en prestatiegegevens te verzamelen van Azure-VM's, om deze gegevens op te slaan in Azure-opslag en om ze toegankelijk te maken via de portal, de Azure Powershell-module en de Azure CLI. Voor geavanceerde bewaking is Azure Monitor voor VM's verantwoordelijk door metrische gegevens over prestaties te verzamelen, toepassingsonderdelen te detecteren die op de VM zijn geïnstalleerd. Hierin zijn ook prestatiegrafieken en een afhankelijkheidskaart opgenomen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Diagnostische gegevens over opstarten op een VM inschakelen
> * Diagnostische gegevens over opstarten bekijken
> * Metrische gegevens over de VM-host weergeven
> * Azure Monitor voor VM's inschakelen
> * Metrische prestatiegegevens van VM's weergeven
> * Een waarschuwing maken

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/powershell](https://shell.azure.com/powershell) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

## <a name="create-virtual-machine"></a>Virtuele machine maken

Voor het configureren van Azure-bewaking en updatebeheer in deze zelfstudie hebt u een Windows-VM in Azure nodig. Stel eerst een beheerdersnaam en -wachtwoord in voor de virtuele machine met [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Maak nu de VM met [New-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt op de locatie *VS Oost*. Als deze niet al bestaan, worden de resourcegroep *myResourceGroupMonitorMonitor* en ondersteunende netwerkresources gemaakt:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Het duurt enkele minuten voordat de bronnen en virtuele machine zijn gemaakt.

## <a name="view-boot-diagnostics"></a>Diagnostische gegevens over opstarten bekijken

Als virtuele Windows-machines opstarten, legt de agent voor diagnostische opstartgegevens schermuitvoer vast voor het oplossen van problemen. Deze mogelijkheid is standaard ingeschakeld. De vastgelegde schermafbeeldingen worden opgeslagen in een Azure-opslagaccount, dat ook standaard wordt gemaakt.

U krijgt de diagnostische opstartgegevens met de opdracht [Get-AzureRmVMBootDiagnosticsData](/powershell/module/az.compute/get-azvmbootdiagnosticsdata). In het volgende voorbeeld wordt de diagnostische opstartgegevens gedownload naar de hoofdmap van station * c:\*.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Metrische gegevens over de host weergeven

Een Windows-VM heeft een toegewezen host-VM in Windows die met deze VM samenwerkt. Metrische gegevens worden automatisch verzameld voor de host en kunnen worden weergegeven in Azure Portal.

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.
2. Als u wilt zien hoe de host-VM wordt uitgevoerd, klikt u op **Metrische gegevens** op de blade van de virtuele machine en selecteert u vervolgens een van de metrische gegevens over Host onder **Beschikbare metrische gegevens**.

    ![Metrische gegevens over de host weergeven](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Geavanceerde bewaking inschakelen

Ga als volgt te werk als u de bewaking van uw Azure-VM met Azure Monitor voor VM's wilt inschakelen:

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.

2. Selecteer op de VM-pagina, in de sectie **Bewaking**, de optie **Inzichten (preview)** .

3. Selecteer op de pagina **Inzichten (preview)** de optie **Nu uitproberen**.

    ![Azure Monitor voor VM's voor een VM inschakelen](../../azure-monitor/vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

4. Als u een bestaande Log Analytics-werkruimte in hetzelfde abonnement hebt, selecteert u deze in de vervolgkeuzelijst op de pagina **Onboarding van Azure Monitor Insights**.  

    De lijst selecteert vooraf de standaardwerkruimte en de locatie waar de VM in het abonnement is geïmplementeerd. 

    >[!NOTE]
    >Zie [Een Log Analytics-werkruimte maken](../../azure-monitor/logs/quick-create-workspace.md) om een nieuwe Log Analytics-werkruimte te maken voor het opslaan van de bewakingsgegevens van de VM. De werkruimte moet deel uitmaken van een van de [ondersteunde regio's](../../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).

Nadat u bewaking hebt ingeschakeld, moet u mogelijk enkele minuten wachten voordat u de metrische prestatiegegevens voor de VM kunt weergeven.

![Verwerking van de implementatie van de bewaking van Azure Monitor voor VM's inschakelen](../../azure-monitor/vm/media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>Metrische prestatiegegevens van VM's weergeven

Azure Monitor voor VM's bevat een reeks prestatiegrafieken die gericht zijn op verschillende Key Performance Indicators (KPI's), waarmee u de prestaties van een virtuele machine kunt vaststellen. Voer de volgende stappen uit om toegang te krijgen vanaf uw VM.

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.

2. Selecteer op de VM-pagina, in de sectie **Bewaking**, de optie **Inzichten (preview)** .

3. Selecteer het tabblad **Prestaties**.

Deze pagina bevat niet alleen grafieken voor prestatiebelasting, maar ook een tabel die voor elke gedetecteerde logische schijf, de capaciteit, de belasting en het totale gemiddelde van elke meting laat zien.

## <a name="create-alerts"></a>Waarschuwingen maken

U kunt waarschuwingen maken op basis van specifieke maatstaven voor prestaties. Waarschuwingen kunnen worden gebruikt om u een melding te sturen wanneer het gemiddelde CPU-gebruik een bepaalde drempelwaarde overschrijdt of de beschikbare vrije schijfruimte onder een bepaalde hoeveelheid komt. Waarschuwingen worden weergegeven in Azure Portal of kunnen worden verzonden via e-mail. U kunt ook Azure Automation-runbooks of Azure Logic Apps activeren als reactie op het genereren van waarschuwingen.

In het volgende voorbeeld wordt een waarschuwing gemaakt voor het gemiddelde CPU-gebruik.

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.

2. Klik op de VM-blade op **Waarschuwingsregels** en klik vervolgens boven aan de waarschuwingenblade op **Waarschuwing voor metrische gegevens toevoegen**.

3. Geef een **Naam** op voor de waarschuwing, zoals *myAlertRule*

4. Als u een waarschuwing wilt activeren wanneer het CPU-percentage gedurende vijf minuten 1.0 overschrijdt, laat u alle overige standaardwaarden geselecteerd.

5. Schakel desgewenst het selectievakje voor *E-mailadressen van eigenaren, bijdragers en lezers* in om een e-mailmelding te verzenden. Standaard wordt een melding in de portal weergegeven.

6. Klik op de knop **OK**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de prestaties van uw VM geconfigureerd en bekeken. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een resourcegroep en VM maken
> * Diagnostische gegevens over opstarten op de virtuele machine inschakelen
> * Diagnostische gegevens over opstarten bekijken
> * Metrische gegevens over de host weergeven
> * Azure Monitor voor VM's inschakelen
> * Metrische gegevens over de VM weergeven
> * Een waarschuwing maken

Ga naar de volgende zelfstudie voor meer informatie over Azure Security Center.

> [!div class="nextstepaction"]
> [VM-beveiliging beheren](../../security/fundamentals/overview.md)
