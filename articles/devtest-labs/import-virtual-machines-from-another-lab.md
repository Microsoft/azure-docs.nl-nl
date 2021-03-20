---
title: Virtuele machines importeren vanuit een ander lab in Azure DevTest Labs
description: In dit artikel wordt beschreven hoe u virtuele machines vanuit een ander lab importeert in het huidige lab in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f664a0ae399575ee936565adaf7364fd1c5ce5c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85475931"
---
# <a name="import-virtual-machines-from-another-lab-in-azure-devtest-labs"></a>Virtuele machines importeren vanuit een ander lab in Azure DevTest Labs
Dit artikel bevat informatie over het importeren van virtuele machines van een ander lab in uw Lab.

## <a name="scenarios"></a>Scenario's
Hier volgen enkele scenario's waarin u Vm's van het ene lab naar een ander lab moet importeren:

- Een individu van het team gaat naar een andere groep binnen de onderneming en wil het ontwikkelaars bureau nemen naar de DevTest Labs van het nieuwe team.
- De groep heeft een [quotum op abonnements niveau](../azure-resource-manager/management/azure-subscription-service-limits.md) bereikt en wil de teams opsplitsen in een aantal abonnementen
- Het bedrijf gaat over naar een snelle route (of een andere nieuwe netwerk topologie) en het team wil de Virtual Machines verplaatsen om deze nieuwe infra structuur te gebruiken

## <a name="solution-and-constraints"></a>Oplossing en beperkingen
Met deze functie kunt u virtuele machines in één Lab (bron) importeren in een ander lab (doel). U kunt eventueel een nieuwe naam voor de doel-VM in het proces opgeven. Het import proces bevat alle afhankelijkheden, zoals schijven, schema's, netwerk instellingen, enzovoort.

Het proces neemt enige tijd in beslag en wordt beïnvloed door de volgende factoren:

- Aantal of de grootte van de schijven die zijn gekoppeld aan de bron machine (omdat het een Kopieer bewerking is en geen Verplaats bewerking)
- Afstand tot het doel (bijvoorbeeld de regio VS-Oost naar Zuidoost-Azië).

Zodra het proces is voltooid, blijft de virtuele bron machine afgesloten en wordt het nieuwe uitgevoerd in het bestemmings Lab.

Er zijn twee belang rijke beperkingen waarmee u rekening moet houden bij het importeren van Vm's van één Lab in naar een ander lab:

- De virtuele machine wordt geïmporteerd in abonnementen en tussen regio's wordt ondersteund, maar de abonnementen moeten aan dezelfde Azure Active Directory-Tenant zijn gekoppeld.
- Virtual Machines mag niet een claim bare status in het bron laboratorium hebben.
- U bent de eigenaar van de virtuele machine in de bron-Lab en de eigenaar van het lab in het doel-Lab.
- Deze functie wordt momenteel alleen ondersteund via Power shell en REST API.

## <a name="use-powershell"></a>PowerShell gebruiken
Down load ImportVirtualMachines.ps1-bestand van de [github](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImportVirtualMachines). U kunt het script gebruiken om één virtuele machine of alle virtuele machines in het bron laboratorium te importeren in het doel omgeving.

### <a name="use-powershell-to-import-a-single-vm"></a>Power shell gebruiken voor het importeren van één VM
Voor het uitvoeren van dit Power shell-script moet u de bron-VM en het doel-Lab identificeren en optioneel een nieuwe naam opgeven om te gebruiken voor de doel computer:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -SourceVirtualMachineName "<Name of the VM to be imported from the source lab> " `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>" `
                            -DestinationVirtualMachineName "<Optional: specify a new name for the imported VM in the destination lab>"
```

### <a name="use-powershell-to-import-all-vms-in-the-source-lab"></a>Power shell gebruiken voor het importeren van alle virtuele machines in het bron laboratorium
Als de virtuele bron machine niet is opgegeven, worden in het script automatisch alle virtuele machines in de DevTest Labs geïmporteerd.  Bijvoorbeeld:

```powershell
./ImportVirtualMachines.ps1 -SourceSubscriptionId "<ID of the subscription that contains the source lab>" `
                            -SourceDevTestLabName "<Name of the source lab>" `
                            -DestinationSubscriptionId "<ID of the subscription that contians the destination lab>" `
                            -DestinationDevTestLabName "<Name of the destination lab>"
```

## <a name="use-http-rest-to-import-a-vm"></a>HTTP REST gebruiken voor het importeren van een virtuele machine
De REST-aanroep is eenvoudig. U geeft voldoende informatie om de bron-en doel resources te identificeren. Houd er rekening mee dat de bewerking plaatsvindt op de bestemmings Lab-resource.

```REST
POST https://management.azure.com/subscriptions/<DestinationSubscriptionID>/resourceGroups/<DestinationResourceGroup>/providers/Microsoft.DevTestLab/labs/<DestinationLab>/ImportVirtualMachine?api-version=2017-04-26-preview
{
   sourceVirtualMachineResourceId: "/subscriptions/<SourceSubscriptionID>/resourcegroups/<SourceResourceGroup>/providers/microsoft.devtestlab/labs/<SourceLab>/virtualmachines/<NameofVMTobeImported>",
   destinationVirtualMachineName: "<NewNameForImportedVM>"
}
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Beleids regels instellen voor een Lab](devtest-lab-set-lab-policy.md)
- [Veelgestelde vragen](devtest-lab-faq.md)
