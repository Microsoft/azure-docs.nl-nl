---
title: Symantec Endpoint Protection installeren op een Windows-VM in azure
description: Meer informatie over het installeren en configureren van de Symantec Endpoint Protection Security-extensie op een nieuwe of bestaande virtuele Azure-machine die is gemaakt met het klassieke implementatie model.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/31/2017
ms.openlocfilehash: 9a25e9eb27111a450f787f4efb3e0d39456fc757
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559644"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Symantec-Endpoint Protection installeren en configureren op een Windows-VM
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../../azure-resource-manager/management/deployment-models.md). In dit artikel wordt beschreven hoe u het klassieke implementatie model gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken.

In dit artikel wordt beschreven hoe u de Symantec Endpoint Protection-client installeert en configureert op een bestaande virtuele machine (VM) waarop Windows Server wordt uitgevoerd. Deze volledige client omvat services zoals virus-en spyware beveiliging, firewall en bescherming tegen indringers. De-client wordt geïnstalleerd als een beveiligings extensie met behulp van de VM-agent.

Als u een bestaand abonnement van Symantec hebt voor een lokale oplossing, kunt u dit gebruiken om uw Azure virtual machines te beveiligen. Als u nog geen klant bent, kunt u zich aanmelden voor een proef abonnement. Zie [Symantec Endpoint Protection op het Azure-platform van micro soft][Symantec]voor meer informatie over deze oplossing. Deze pagina bevat ook koppelingen naar licentie-informatie en instructies voor het installeren van de client als u al een Symantec-klant bent.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Symantec Endpoint Protection installeren op een bestaande VM
Voordat u begint, hebt u het volgende nodig:

* De Azure PowerShell module versie 0.8.2 of hoger op uw werk computer. U kunt de versie van Azure PowerShell controleren die u hebt geïnstalleerd met de opdracht **Get-module Azure | indeling-Table versie** . Zie [Azure PowerShell installeren en configureren][PS]voor instructies en een koppeling naar de meest recente versie. Meld u aan bij uw Azure-abonnement met `Add-AzureAccount` .
* De VM-agent die wordt uitgevoerd op de virtuele machine van Azure.

Controleer eerst of de VM-agent al is geïnstalleerd op de virtuele machine. Vul de naam van de Cloud service en de virtuele machine in en voer de volgende opdrachten uit op de opdracht prompt op beheerders niveau Azure PowerShell. Vervang alles binnen de aanhalings tekens, met inbegrip van de <-en > teken.

> [!TIP]
> Als u de namen van de Cloud service en de virtuele machine niet weet, voert u **Get-AzureVM** uit om de namen van alle virtuele machines in uw huidige abonnement weer te geven.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Als de opdracht **Write-host** de **waarde True** weergeeft, wordt de VM-agent geïnstalleerd. Als deze **Onwaar** wordt weer gegeven, raadpleegt u de instructies en een koppeling naar de down load in de Azure blog post [VM-agent en-extensies-deel 2][Agent].

Als de VM-agent is geïnstalleerd, voert u deze opdrachten uit om de Symantec Endpoint Protection-agent te installeren.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

U kunt als volgt controleren of de Symantec-beveiligings uitbreiding is geïnstalleerd en up-to-date is:

1. Meld u aan bij de virtuele machine. Zie [Aanmelden bij een virtuele machine met Windows Server][Logon]voor instructies.
2. Klik voor Windows Server 2008 R2 op **Start > Symantec Endpoint Protection**. Voor Windows Server 2012 of Windows Server 2012 R2 typt u **Symantec** in het Start scherm en klikt u vervolgens op **Symantec Endpoint Protection**.
3. Op het tabblad **status** van het venster **status-Symantec Endpoint Protection** past u updates of opnieuw opstarten toe, indien nodig.

## <a name="additional-resources"></a>Aanvullende bronnen
[Aanmelden bij een virtuele machine met Windows Server][Logon]

[Azure VM-extensies en-functies][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azure/

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: features-windows.md
