---
title: Problemen met artefacten in Azure DevTest Labs oplossen | Microsoft Docs
description: Informatie over het oplossen van problemen die zich voordoen bij het Toep assen van artefacten in een Azure DevTest Labs virtuele machine.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a89b675a1b3bf134b98e09c7278f0eccb594c325
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483190"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Problemen oplossen bij het Toep assen van artefacten in een Azure DevTest Labs virtuele machine
Het Toep assen van artefacten op een virtuele machine kan om verschillende redenen mislukken. In dit artikel wordt stapsgewijs beschreven hoe u mogelijke oorzaken kunt identificeren.

Als u op elk gewenst moment meer hulp nodig hebt, kunt u contact opnemen met de experts van de Azure DevTest Labs (DTL) op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen.   

> [!NOTE]
> Dit artikel is van toepassing op virtuele Windows-en niet-Windows-machines. Hoewel er enkele verschillen zijn, worden deze expliciet in dit artikel genoemd.

## <a name="quick-troubleshooting-steps"></a>Snelle probleemoplossings stappen
Controleer of de virtuele machine wordt uitgevoerd. DevTest Labs vereist dat de VM wordt uitgevoerd en dat de [Microsoft Azure virtuele machine agent (VM-agent)](../virtual-machines/extensions/agent-windows.md) is geïnstalleerd en gereed is.

> [!TIP]
> Ga in het **Azure Portal** naar de pagina **artefacten beheren** voor de virtuele machine om te zien of de VM gereed is voor het Toep assen van artefacten. Er wordt een bericht aan de bovenkant van de pagina weer gegeven. 
> 
> Inspecteer met behulp van **Azure PowerShell** de vlag **canApplyArtifacts**, die alleen wordt geretourneerd wanneer u een Get-bewerking uitvouwt. Raadpleeg de volgende voorbeeld opdracht:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Manieren om problemen op te lossen 
U kunt virtuele machines die zijn gemaakt met DevTest Labs en het Resource Manager-implementatie model, met een van de volgende methoden oplossen:

- **Azure Portal** -geweldig als u snel een visuele hint moet krijgen van wat het probleem kan veroorzaken.
- **Azure PowerShell** : als u vertrouwd bent met een Power shell-prompt, kunt u snel DevTest Labs-resources opvragen met de Azure PowerShell-cmdlets.

> [!TIP]
> Zie [problemen met artefacten in het lab diagnosticeren](devtest-lab-troubleshoot-artifact-failure.md)voor meer informatie over het controleren van de uitvoering van artefacten binnen een virtuele machine.

## <a name="symptoms-causes-and-potential-resolutions"></a>Symptomen, oorzaken en mogelijke oplossingen 

### <a name="artifact-appears-to-stop-responding"></a>Artefact lijkt niet meer te reageren

Een artefact lijkt niet meer te reageren totdat een vooraf gedefinieerde time-outperiode is verstreken en het artefact is gemarkeerd als **mislukt**.

Wanneer een artefact vastloopt, moet u eerst bepalen waar deze zich bevinden. Een artefact kan tijdens de uitvoering worden geblokkeerd met een van de volgende stappen:

- **Tijdens de eerste aanvraag**. DevTest Labs maakt een Azure Resource Manager-sjabloon om het gebruik van de aangepaste script extensie (CSE) aan te vragen. Daarom wordt er achter de schermen een implementatie van een resource groep geactiveerd. Als er een fout optreedt op dit niveau, krijgt u details in de **activiteiten logboeken** van de resource groep voor de desbetreffende VM.  
    - U kunt het activiteiten logboek openen via de navigatie balk van de VM-test omgeving. Wanneer u deze optie selecteert, ziet u een vermelding voor het **Toep assen van artefacten op de virtuele machine** (als de bewerking artefacten Toep assen rechtstreeks is geactiveerd) of het **toevoegen of wijzigen van virtuele machines** (als de bewerking artefacten Toep assen onderdeel was van het proces voor het maken van de VM).
    - Zoek naar fouten onder deze vermeldingen. De fout wordt soms niet dienovereenkomstig gelabeld en u moet elk item onderzoeken.
    - Zorg ervoor dat u de inhoud van de JSON-nettolading bekijkt wanneer u de details van elke vermelding onderzoekt. Er wordt mogelijk een fout weer geven aan de onderkant van het document.
- **Bij het uitvoeren van het artefact**. Dit kan worden veroorzaakt door netwerk-of opslag problemen. Zie de betreffende sectie verderop in dit artikel voor meer informatie. Dit kan ook gebeuren door de manier waarop het script is gemaakt. Bijvoorbeeld:
    - Een Power shell-script heeft **verplichte para meters**, maar er kan wel een waarde worden door gegeven, omdat u toestaat dat de gebruiker het veld leeg laat of omdat u geen standaard waarde hebt voor de eigenschap in het artifactfile.jsvan het definitie bestand. Het script reageert niet meer omdat het wacht op invoer van de gebruiker.
    - Een Power shell-script **vereist invoer** van de gebruiker als onderdeel van de uitvoering. Scripts moeten zonder tussen komst van de gebruiker worden geschreven om op de achtergrond te worden uitgevoerd.
- **Het duurt lang voordat de VM-agent gereed is**. Wanneer de virtuele machine voor het eerst wordt gestart, of wanneer de aangepaste script extensie voor het eerst wordt geïnstalleerd om artefacten toe te passen, moet de VM mogelijk een upgrade van de VM-agent uitvoeren of wachten tot de VM-agent is geïnitialiseerd. Het kan voor komen dat de VM-agent veel tijd in beslag neemt. In dergelijke gevallen raadpleegt u [overzicht van Azure virtual machine agent](../virtual-machines/extensions/agent-windows.md) voor verdere probleem oplossing.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-script"></a>Controleren of het artefact lijkt te reageren vanwege het script

1. Meld u aan bij de betreffende virtuele machine.
2. Kopieer het script lokaal in de virtuele machine of zoek het naar de virtuele machine onder `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>` . Het is de locatie waar de artefact scripts worden gedownload.
3. Voer het script lokaal uit met behulp van een opdracht prompt met verhoogde bevoegdheid, zodat dezelfde parameter waarden worden gebruikt om het probleem te veroorzaken.
4. Bepaal of het script te lijden heeft aan ongewenste gedrag. Indien dit het geval is, moet u een update aanvragen bij het artefact (als deze afkomstig is van de open bare opslag plaats). of maak de correcties zelf (als deze afkomstig zijn van uw persoonlijke opslag plaats).

> [!TIP]
> U kunt problemen oplossen met artefacten die worden gehost in onze [open bare opslag plaats](https://github.com/Azure/azure-devtestlab) en de wijzigingen verzenden voor onze beoordeling en goed keuring. Zie de sectie **bijdragen** in het [README.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) -document.
> 
> Zie [AUTHORING.MD](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) -document voor meer informatie over het schrijven van uw eigen artefacten.

### <a name="to-verify-if-the-artifact-appears-to-stop-responding-because-of-the-vm-agent"></a>Ga als volgt te werk om te controleren of het artefact lijkt te reageren vanwege de VM-agent:
1. Meld u aan bij de betreffende virtuele machine.
2. Ga in bestanden Verkenner naar **C:\WindowsAzure\logs**.
3. Zoek en open bestand **WaAppAgent. log**.
4. Zoek naar vermeldingen die laten zien wanneer de VM-agent wordt gestart en wanneer deze de initialisatie voltooit (dat wil zeggen, de eerste heartbeat wordt verzonden). Voor keur voor nieuwere vermeldingen of specifieke items rond de periode waarin het probleem zich voordoet.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    In dit voor beeld ziet u dat de start tijd van de VM-agent 10 minuten en 20 seconden heeft geduurd omdat er een heartbeat is verzonden. De oorzaak in dit geval was dat de OOBE-service veel tijd in beslag neemt.

> [!TIP]
> Zie [extensies en functies van virtuele Azure-machines](../virtual-machines/extensions/overview.md)voor algemene informatie over Azure-extensies.

## <a name="storage-errors"></a>Opslag fouten
DevTest Labs vereist toegang tot het opslag account van de Lab dat is gemaakt om artefacten in de cache op te slaan. Wanneer in DevTest Labs een artefact wordt toegepast, worden de artefact configuratie en de bijbehorende bestanden van de geconfigureerde opslag plaatsen gelezen. DevTest Labs configureert standaard de toegang tot de **open bare artefact opslag plaats**.

Afhankelijk van hoe een virtuele machine is geconfigureerd, heeft deze mogelijk geen rechtstreekse toegang tot deze opslag plaats. Daarom worden in DevTest Labs de artefacten in een opslag account opgeslagen die worden gemaakt wanneer het lab voor het eerst wordt geïnitialiseerd.

Als toegang tot dit opslag account op een wille keurige manier wordt geblokkeerd, zoals wanneer het verkeer wordt geblokkeerd van de virtuele machine naar de Azure Storage-service, wordt er mogelijk een fout weer gegeven die vergelijkbaar is met de volgende:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

De bovenstaande fout wordt weer gegeven in de sectie **implementatie bericht** van de pagina **artefact resultaten** onder **artefacten beheren**. Het wordt ook weer gegeven in de **activiteiten logboeken** onder de resource groep van de betreffende virtuele machine.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Om ervoor te zorgen dat de communicatie met de Azure Storage-service niet wordt geblokkeerd:

- **Controleren op toegevoegde netwerk beveiligings groepen (NSG)**. Het kan zijn dat er een abonnements beleid is toegevoegd waar Nsg's automatisch worden geconfigureerd in alle virtuele netwerken. Dit is ook van invloed op het virtuele standaard netwerk van de Lab, indien gebruikt, of een ander virtueel netwerk dat in uw Lab is geconfigureerd, dat wordt gebruikt voor het maken van Vm's.
- **Controleer het standaard-Lab-opslag account** (dat wil zeggen, het eerste opslag account dat is gemaakt tijdens het maken van het lab, waarvan de naam begint met de letter ' a ' en eindigt met een nummer dat uit meerdere cijfers bestaat \<labname\> ).
    1. Navigeer naar de resource groep van de test omgeving.
    2. Zoek de bron van het type **opslag account**, waarvan de naam overeenkomt met de Conventie.
    3. Navigeer naar de pagina voor het opslag account met de naam **firewalls en virtuele netwerken**.
    4. Zorg ervoor dat deze is ingesteld op **alle netwerken**. Als de optie **geselecteerde netwerken** is geselecteerd, zorgt u ervoor dat de virtuele netwerken van de test omgeving die worden gebruikt voor het maken van vm's, worden toegevoegd aan de lijst.

Zie [Azure Storage firewalls en virtuele netwerken configureren](../storage/common/storage-network-security.md)voor meer gedetailleerde probleem oplossing.

> [!TIP]
> **Controleer de regels voor de netwerk beveiligings groep**. Gebruik [IP-stroom controleren](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) om te controleren of het verkeer van of naar een virtuele machine wordt geblokkeerd door een regel in een netwerk beveiligings groep. U kunt ook de juiste regels voor beveiligings groepen controleren om ervoor te zorgen **dat binnenkomende NSG** regel bestaat. Zie [using effectief security rules to Troubleshooting VM Traffic Flow](../virtual-network/diagnose-network-traffic-filter-problem.md)voor meer informatie.

## <a name="other-sources-of-error"></a>Andere fout bronnen
Er zijn andere minder voorkomende fout bronnen beschikbaar. Controleer elke beoordeling om te zien of deze van toepassing is op uw aanvraag. Hier volgt een van deze: 

- **Het persoonlijke toegangs token voor de persoonlijke opslag plaats is verlopen**. Wanneer het artefact is verlopen, worden geen scripts weer gegeven die verwijzen naar artefacten vanuit een opslag plaats met een verlopen persoonlijk toegangs token.

## <a name="next-steps"></a>Volgende stappen
Als er geen fouten zijn opgetreden en u nog steeds geen artefacten kunt Toep assen, kan een ondersteunings incident voor Azure worden vastgelegd. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.
