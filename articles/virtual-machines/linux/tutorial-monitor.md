---
title: 'Zelfstudie: virtuele Linux-machines bewaken in Azure'
description: In deze zelfstudie leert u hoe u de prestaties en gedetecteerde toepassingsonderdelen kunt bewaken die op uw virtuele Linux-machines worden uitgevoerd.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a21df6e5f8d437415bb5376969d56d26153b5c5f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500458"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>Zelfstudie: Een virtuele Linux-machine bewaken in Azure

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

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.0.30 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="create-vm"></a>VM maken

Om diagnostische gegevens en metrische gegevens in actie te zien, hebt u een virtuele machine nodig. Maak eerst een resourcegroep met [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroupMonitor* gemaakt op de locatie *VS Oost*.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

Maak een virtuele machine met [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create). In het volgende voorbeeld wordt een VM gemaakt met de naam *myVM* en worden er in *~/.ssh/* SSH-sleutels gemaakt als deze nog niet bestaan:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>Diagnostische gegevens over opstarten inschakelen

Als virtuele Linux-machines worden opgestart, wordt uitvoer vastgelegd in de extensie voor diagnostische gegevens over opstarten en opgeslagen in Azure Storage. Deze gegevens kunnen worden gebruikt om opstartproblemen met virtuele machines op te lossen. Diagnostische gegevens over opstarten zijn niet automatisch ingeschakeld wanneer u een Linux-VM met de Azure CLI maakt.

Voordat u diagnostische gegevens over opstarten inschakelt, moet een opslagaccount worden gemaakt voor het opslaan van de opstartlogboeken. Opslagaccounts moeten een globaal unieke naam hebben, tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. Maak een opslagaccount met de opdracht [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create). In dit voorbeeld wordt een willekeurige tekenreeks gebruikt voor het maken van een unieke naam voor het opslagaccount.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

Als u diagnostische gegevens over opstarten inschakelt, is de URI naar de opslagcontainer voor de blob nodig. Met de volgende opdracht wordt een query uitgevoerd op de opslagaccount om deze URI op te halen. De URI-waarde wordt opgeslagen in een variabele met de naam *bloburi*, die in de volgende stap wordt gebruikt.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

Schakel diagnostische gegevens nu in met [az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable). De waarde `--storage` is de blob-URI die in de vorige stap is opgehaald.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>Diagnostische gegevens over opstarten bekijken

Wanneer diagnostische gegevens over opstarten zijn ingeschakeld, wordt telkens wanneer u de virtuele machine stopt en start informatie over het opstartproces naar een logboekbestand geschreven. In dit voorbeeld moet u eerst de toewijzing van de virtuele machine als volgt ongedaan maken met de opdracht [az vm deallocate](/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate):

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

Start de virtuele machine nu als volgt met de opdracht [az vm start](/cli/azure/vm?view=azure-cli-latest#az-vm-start):

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

U kunt de diagnostische gegevens over opstarten voor *myVM* als volgt ophalen met de opdracht [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log):

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>Metrische gegevens over de host weergeven

Een Linux-VM heeft een toegewezen host in Azure die met deze VM samenwerkt. Metrische gegevens worden automatisch verzameld voor de host, en kunnen als volgt worden weergegeven in Azure Portal:

1. Selecteer in Azure Portal de optie **Resourcegroepen**, kies **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.
1. Als u wilt zien hoe de host-VM presteert, selecteert u **Metrische gegevens** in het VM-venster en kiest u vervolgens een van de metrische gegevens voor *[Host]* onder **Beschikbare metrische gegevens**.

    ![Metrische gegevens over de host weergeven](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Geavanceerde bewaking inschakelen

Ga als volgt te werk als u de bewaking van uw Azure-VM met Azure Monitor voor VM's wilt inschakelen:

1. Klik in Azure Portal op **Resourcegroepen**, selecteer **myResourceGroupMonitor** en selecteer vervolgens **myVM** in de lijst met resources.

2. Selecteer op de VM-pagina, in de sectie **Bewaking**, de optie **Inzichten (preview)** .

3. Selecteer op de pagina **Inzichten (preview)** de optie **Nu uitproberen**.

    ![Azure Monitor voor VM's voor een VM inschakelen](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. Als u een bestaande Log Analytics-werkruimte in hetzelfde abonnement hebt, selecteert u deze in de vervolgkeuzelijst op de pagina **Onboarding van Azure Monitor Insights**.  

    De lijst selecteert vooraf de standaardwerkruimte en de locatie waar de VM in het abonnement is geïmplementeerd. 

    >[!NOTE]
    >Zie [Een Log Analytics-werkruimte maken](../../azure-monitor/learn/quick-create-workspace.md) om een nieuwe Log Analytics-werkruimte te maken voor het opslaan van de bewakingsgegevens van de VM. De werkruimte moet deel uitmaken van een van de [ondersteunde regio's](../../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).

Nadat u bewaking hebt ingeschakeld, moet u mogelijk enkele minuten wachten voordat u de metrische prestatiegegevens voor de VM kunt weergeven.

![Verwerking van de implementatie van de bewaking van Azure Monitor voor VM's inschakelen](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

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
> [VM-beveiliging beheren](../tutorial-azure-security.md)