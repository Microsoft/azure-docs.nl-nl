---
title: 'Quickstart: een virtuele Windows-machine maken met de Azure CLI'
description: In deze quickstart leert u hoe u de Azure CLI gebruikt om een virtuele Windows-machine te maken
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 769b42133fb894c916ecaa3c42dd7de85206c765
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556244"
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli"></a>Quickstart: Een virtuele Windows-machine maken met de Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze snelstart wordt beschreven hoe u de Azure CLI gebruikt voor het implementeren van een virtuele machine (VM) in Azure waarop Windows Server 2016 wordt uitgevoerd. Om uw VM in actie te zien, voert u een externe bureaubladsessie voor de virtuele machine uit en installeert u de IIS-webserver.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. 

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op **Enter** om de code uit te voeren.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Virtuele machine maken

Maak een VM met [az vm create](/cli/azure/vm). In het volgende voorbeeld wordt een VM met de naam *myVM* gemaakt. In dit voorbeeld wordt *azureuser* als naam van een gebruiker met beheerdersrechten gebruikt. 

U moet een wachtwoord opgeven dat voldoet aan de [wachtwoordvereisten voor VM's in Azure](./faq.md#what-are-the-password-requirements-when-creating-a-vm
). In het onderstaande voorbeeld wordt u gevraagd om een wachtwoord op te geven via een opdrachtregel. U kunt ook de parameter `--admin-password` toevoegen met een waarde voor uw wachtwoord. De gebruikersnaam en het wachtwoord worden later gebruikt om verbinding met de virtuele machine te maken.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser 
```

Het maken van de virtuele machine en de ondersteunende resources duurt enkele minuten. In het volgende voorbeeld van uitvoer ziet u dat het maken van de virtuele machine is geslaagd.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Noteer uw eigen `publicIpAddress` in de uitvoer van uw virtuele machine. Dit adres wordt gebruikt voor toegang tot de virtuele machine in de volgende stappen.

## <a name="open-port-80-for-web-traffic"></a>Poort 80 openen voor webverkeer

Standaard worden alleen verbindingen met een extern bureaublad geopend wanneer u een virtuele Windows-machine in Azure maakt. Gebruik [az vm open-port](/cli/azure/vm) om TCP-poort 80 te openen voor gebruik met de IIS-webserver:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Verbinding maken met de virtuele machine

Gebruik de volgende opdracht om een sessie met een extern bureaublad te starten vanaf uw lokale computer. Vervang het IP-adres door het openbare IP-adres van de virtuele machine. Wanneer u hierom wordt gevraagd, voert u de referenties in die zijn gebruikt toen de VM is gemaakt:

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Webserver installeren

Als u uw VM in actie wilt zien, installeert u de IIS-webserver. Open een PowerShell-prompt op de virtuele machine en voer de volgende opdracht uit:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Wanneer u klaar bent, sluit u de externe-bureaubladverbinding met de virtuele machine.

## <a name="view-the-web-server-in-action"></a>De webserver in actie zien

Nu IIS is geïnstalleerd en poort 80 op de virtuele machine is geopend voor toegang vanaf internet, kunt u een webbrowser van uw keuze gebruiken om de standaardwelkomstpagina van IIS weer te geven. Gebruik het openbare IP-adres van uw VM dat is verkregen in een vorige stap. In het volgende voorbeeld ziet u de IIS-standaardwebsite:

![Standaardsite van IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige virtuele machine geïmplementeerd, een netwerkpoort geopend voor internetverkeer en een eenvoudige webserver geïnstalleerd. Voor meer informatie over virtuele machines in Azure, gaat u verder met de zelfstudie voor virtuele Windows-machines.

> [!div class="nextstepaction"]
> [Zelfstudies over virtuele Windows-machines](./tutorial-manage-vm.md)
