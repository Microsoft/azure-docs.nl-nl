---
title: Trend Micro diepe beveiliging op een virtuele machine installeren
description: In dit artikel wordt beschreven hoe u Trend Micro beveiliging kunt installeren en configureren op een virtuele machine die is gemaakt met het klassieke implementatie model in Azure.
author: axayjo
tags: azure-service-management
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: 9d6d80287d05517933f066d5e49fa31e78a48943
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966376"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Trend Micro diepe Security als een service op een Windows-VM installeren en configureren

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
In dit artikel wordt beschreven hoe u een trend micro diepe beveiliging installeert en configureert als een service op een nieuwe of bestaande virtuele machine (VM) waarop Windows Server wordt uitgevoerd. Uitgebreide beveiliging als een service omvat anti-malware-beveiliging, een firewall, een indringings systeem en integriteits bewaking.

De-client wordt geïnstalleerd als een beveiligings extensie via de VM-agent. Op een nieuwe virtuele machine installeert u de grondige beveiligings agent, omdat de VM-agent automatisch wordt gemaakt door de Azure Portal.

Een bestaande virtuele machine die is gemaakt met behulp van de Azure Portal, de Azure CLI of Power Shell heeft mogelijk geen VM-agent. Voor een bestaande virtuele machine die niet over de VM-agent beschikt, moet u deze eerst downloaden en installeren. In dit artikel worden beide situaties besproken.

Als u een huidig abonnement hebt van Trend Micro voor een on-premises oplossing, kunt u dit gebruiken om uw Azure virtual machines te beveiligen. Als u nog geen klant bent, kunt u zich aanmelden voor een proef abonnement. Voor meer informatie over deze oplossing raadpleegt u het trend micro blog post [Microsoft Azure VM-agent extensie voor diepe beveiliging](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installeer de grondige beveiligings agent op een nieuwe virtuele machine

Met de [Azure Portal](https://portal.azure.com) kunt u de trend micro Security-extensie installeren wanneer u een installatie kopie van de **Marketplace** gebruikt om de virtuele machine te maken. Als u één virtuele machine maakt, is het gebruik van de portal een eenvoudige manier om beveiliging van Trend Micro toe te voegen.

Als u een item uit de **Marketplace** gebruikt, wordt er een wizard geopend die u helpt bij het instellen van de virtuele machine. U gebruikt de Blade **instellingen** , het derde paneel van de wizard, om de trend micro Security-extensie te installeren.  Zie [een virtuele machine met Windows maken in de Azure Portal](../windows/quick-create-portal.md)voor algemene instructies.

Wanneer u de Blade **instellingen** van de wizard krijgt, voert u de volgende stappen uit:

1. Klik op **uitbrei dingen** en klik vervolgens in het volgende deel venster op **uitbrei ding toevoegen** .

   ![Toevoegen van de uitbrei ding starten][1]

2. Selecteer **diepe beveiligings agent** in het deel venster **nieuwe resource** . Klik in het deel venster diepe beveiligings agent op **maken**.

   ![Grondige beveiligings agent identificeren][2]

3. Voer de **Tenant-id** en het **wacht woord voor Tenant activering** in voor de extensie. U kunt desgewenst een **beveiligings beleid-id** invoeren. Klik vervolgens op **OK** om de client toe te voegen.

   ![Details van de extensie opgeven][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installeer de grondige beveiligings agent op een bestaande virtuele machine
Als u de agent wilt installeren op een bestaande virtuele machine, hebt u de volgende items nodig:

* De Azure PowerShell module versie 0.8.2 of nieuwer, geïnstalleerd op uw lokale computer. U kunt de versie van Azure PowerShell die u hebt geïnstalleerd controleren met behulp van de opdracht **Get-module Azure | indeling-Table versie** . Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor instructies en een koppeling naar de meest recente versie. Meld u aan bij uw Azure-abonnement met `Add-AzureAccount` .
* De VM-agent die is geïnstalleerd op de virtuele doel machine.

Controleer eerst of de VM-agent al is geïnstalleerd. Vul de naam van de Cloud service en de virtuele machine in en voer de volgende opdrachten uit op de opdracht prompt op beheerders niveau Azure PowerShell. Vervang alles binnen de aanhalings tekens, met inbegrip van de <-en > teken.

```azurepowershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Als u de naam van de Cloud service en de virtuele machine niet weet, voert u **Get-AzureVM** uit om die informatie weer te geven voor alle virtuele machines in uw huidige abonnement.

Als de **Write-host-** opdracht **True** retourneert, wordt de VM-agent geïnstalleerd. Als de waarde **False** retourneert, raadpleegt u de instructies en een koppeling naar de down load in de Azure blog post [VM-agent en-extensies-deel 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Als de VM-agent is geïnstalleerd, voert u deze opdrachten uit.

```azurepowershell
$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM
```

## <a name="next-steps"></a>Volgende stappen
Het duurt enkele minuten voordat de agent wordt gestart wanneer deze is geïnstalleerd. Daarna moet u de grondige beveiliging op de virtuele machine activeren zodat deze kan worden beheerd door een grondige beveiligings beheerder. Raadpleeg de volgende artikelen voor aanvullende instructies:

* Artikel over de trend over deze oplossing, [directe beveiliging van de Cloud voor Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* Een voor beeld van een [Windows Power shell-script](https://go.microsoft.com/fwlink/?LinkId=404100) voor het configureren van de virtuele machine
* [Instructies](https://go.microsoft.com/fwlink/?LinkId=404099) voor het voor beeld

## <a name="additional-resources"></a>Aanvullende bronnen
[Aanmelden bij een virtuele machine met Windows Server]

[Azure VM-extensies en-functies]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Aanmelden bij een virtuele machine met Windows Server]:../windows/classic/connect-logon.md
[Azure VM-extensies en-functies]: features-windows.md
