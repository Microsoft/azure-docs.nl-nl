---
title: Hotpatch voor Windows Server Azure Edition (preview-versie)
description: Meer informatie over hoe hotpatch voor Windows Server Azure Edition werkt en hoe u deze functie inschakelt
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 92b8bf240dfd73cc9191675db07f20816b7156a8
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953388"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Hotpatch voor nieuwe virtuele machines (preview-versie)

Hotpatching is een nieuwe manier om updates te installeren op nieuwe virtuele machines met Windows Server Azure Edition (Vm's) die na de installatie niet opnieuw moeten worden opgestart. Dit artikel bevat informatie over de hotpatch voor virtuele machines met Windows Server Azure Edition, die de volgende voor delen biedt:
* Minder gevolgen voor de werk belasting met weinig opnieuw opstarten
* Snellere implementatie van updates omdat de pakketten kleiner zijn, sneller worden geïnstalleerd en de patch indeling met Azure update manager is vereenvoudigd
* Betere beveiliging, omdat de hotpatch-update pakketten zijn afgestemd op Windows-beveiligings updates die sneller worden geïnstalleerd zonder opnieuw op te starten

## <a name="how-hotpatch-works"></a>Hoe hotpatch werkt

Hotpatch werkt eerst met een basis lijn met een Windows Update meest recente cumulatieve update. Hotpatches worden periodiek vrijgegeven (bijvoorbeeld op de tweede dinsdag van de maand) die op die basis lijn zijn gebouwd. Hotpatches bevat updates die niet opnieuw moeten worden opgestart. Periodiek (beginnend bij elke drie maanden) wordt de basis lijn vernieuwd met een nieuwe cumulatieve update.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Voorbeeld schema voor hotpatch.":::

Er zijn twee soorten basis lijnen: **geplande basis lijnen** en niet- **geplande basis lijnen**.
*  **Geplande basis lijnen** worden vrijgegeven op een reguliere uitgebracht, met hotpatch-releases tussen.  Geplande basis lijnen bevatten alle updates in een vergelijk bare, _meest recente cumulatieve update_ voor die maand, en moeten opnieuw worden opgestart.
    * De voorbeeld planning hierboven illustreert vier geplande basislijn releases in een kalender jaar (vijf in het diagram) en acht hotpatch-releases.
* Niet- **geplande basis lijnen** worden vrijgegeven wanneer een belang rijke update (zoals een correctie op basis van een dag) wordt uitgebracht en de specifieke update niet kan worden uitgebracht als een hotpatch.  Wanneer er niet-geplande basis lijnen worden vrijgegeven, wordt een hotpatch-release in die maand vervangen door een niet-geplande basis lijn.  Niet-geplande basis lijnen bevatten ook alle updates in een vergelijk bare, _meest recente cumulatieve update_ voor die maand, en u moet de computer ook opnieuw opstarten.
    * In het bovenstaande voor beeld schema ziet u twee ongeplande basis lijnen die de hotpatch-releases voor die maanden vervangen (het werkelijke aantal ongeplande basis lijnen in een jaar is niet vooraf bekend).

## <a name="regional-availability"></a>Regionale beschikbaarheid
Hotpatch is beschikbaar in alle wereld wijde Azure-regio's in de preview-versie. Azure Government regio's worden niet ondersteund in de preview-versie.

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag

> [!NOTE]
> Tijdens de preview-fase kunt u alleen aan de slag in de Azure Portal met behulp van [deze koppeling](https://aka.ms/AzureAutomanageHotPatch).

Ga als volgt te werk om hotpatch te gaan gebruiken op een nieuwe VM:
1.  Preview-toegang inschakelen
    * De activering van eenmalige preview-toegang is vereist per abonnement.
    * Preview-toegang kan worden ingeschakeld via API, Power shell of CLI, zoals beschreven in de volgende sectie.
1.  Een virtuele machine maken op basis van de Azure Portal
    * Tijdens de preview moet u aan de slag met [deze koppeling](https://aka.ms/AzureAutomanageHotPatch).
1.  Details van de VM opgeven
    * Zorg ervoor dat _Windows Server 2019 Data Center: Azure Edition_ is geselecteerd in de vervolg keuzelijst afbeelding)
    * Ga op de stap tabblad beheer naar het gedeelte ' updates van het gast besturingssysteem '. U ziet hotpatching ingesteld op on en patch-installatie standaard voor door Azure gegroepeerde patches.
    * Aanbevolen procedures voor het automatisch beheren van virtuele machines worden standaard ingeschakeld
1. Uw nieuwe VM maken

## <a name="enabling-preview-access"></a>Preview-toegang inschakelen

### <a name="rest-api"></a>REST-API

In het volgende voor beeld wordt beschreven hoe u de preview-versie van uw abonnement kunt inschakelen:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

De registratie van onderdelen kan Maxi maal 15 minuten duren. De registratiestatus controleren:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het aanmeldings proces door de wijziging door te geven aan de provider van de reken resource.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de ```Register-AzProviderFeature``` cmdlet om de preview voor uw abonnement in te scha kelen.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

De registratie van onderdelen kan Maxi maal 15 minuten duren. De registratiestatus controleren:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het aanmeldings proces door de wijziging door te geven aan de provider van de reken resource.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

Gebruiken ```az feature register``` om de preview voor uw abonnement in te scha kelen.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

De registratie van onderdelen kan Maxi maal 15 minuten duren. De registratiestatus controleren:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het aanmeldings proces door de wijziging door te geven aan de provider van de reken resource.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Installatie van patch

Tijdens de preview-fase wordt [automatische VM-gast patches](../virtual-machines/automatic-vm-guest-patching.md) automatisch ingeschakeld voor alle vm's die zijn gemaakt met _Windows Server 2019 Data Center: Azure Edition_. Als automatische VM-gast patching is ingeschakeld:
* Patches die zijn geclassificeerd als kritiek of beveiliging, worden automatisch gedownload en toegepast op de virtuele machine.
* Patches worden toegepast tijdens rustige uren in de tijd zone van de virtuele machine.
* Patch indeling wordt beheerd door Azure en patches worden toegepast na de [eerste principes van Beschik baarheid](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching).
* De status van de virtuele machine, zoals wordt bepaald door platform status signalen, wordt bewaakt om patch fouten te detecteren.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Hoe werkt automatische VM-gast patches?

Als [automatische VM-gast patches](../virtual-machines/automatic-vm-guest-patching.md) is ingeschakeld op een VM, worden de beschik bare essentiële en beveiligings patches automatisch gedownload en toegepast. Dit proces wordt elke maand automatisch uitgevoerd wanneer er nieuwe patches worden uitgebracht. De evaluatie en installatie van patches worden automatisch uitgevoerd en het proces omvat het opnieuw opstarten van de virtuele machine, indien nodig.

Als hotpatch is ingeschakeld in _Windows Server 2019 Data Center: Azure Edition_ vm's, worden de meeste maandelijkse beveiligings updates geleverd als hotpatches die niet opnieuw hoeven te worden opgestart. Voor de meest recente cumulatieve updates die zijn verzonden op geplande of niet-geplande basislijn maanden, moet de VM opnieuw worden opgestart. Er zijn mogelijk ook extra essentiële of beveiligings patches beschikbaar waarvoor het opnieuw opstarten van de VM mogelijk is.

De VM wordt elke paar dagen en meerdere keren binnen een periode van 30 dagen geëvalueerd om de toepasselijke patches voor die VM te bepalen. Deze automatische beoordeling zorgt ervoor dat eventuele ontbrekende patches zo snel mogelijk worden gedetecteerd.

Patches worden binnen 30 dagen na de maandelijkse patch releases geïnstalleerd, met de [eerste principes van Beschik baarheid](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching). Patches worden alleen geïnstalleerd tijdens daluren voor de virtuele machine, afhankelijk van de tijd zone van de virtuele machine. De virtuele machine moet worden uitgevoerd gedurende de daluren van de patches die automatisch worden geïnstalleerd. Als een virtuele machine is uitgeschakeld tijdens een periodieke evaluatie, wordt de VM beoordeeld en worden de toepasselijke patches automatisch geïnstalleerd tijdens de volgende periodieke evaluatie wanneer de virtuele machine wordt ingeschakeld. De volgende periodieke evaluatie gebeurt doorgaans binnen een paar dagen.

Definitie-updates en andere patches die niet zijn geclassificeerd als kritiek of beveiliging, worden niet geïnstalleerd via automatische VM-gast patches.

## <a name="understanding-the-patch-status-for-your-vm"></a>Informatie over de patch status voor uw VM

Als u de patch status voor uw virtuele machine wilt bekijken, gaat u naar de sectie **gast-en host-updates** voor uw virtuele machine in de Azure Portal. Klik onder de sectie updates van het **gast besturingssysteem** op ' go to hotpatch (preview) ' om de nieuwste patch status voor uw virtuele machine weer te geven.

In dit scherm ziet u de status van de hotpatch voor uw VM. U kunt ook controleren of er beschik bare patches beschikbaar zijn voor uw VM die nog niet zijn geïnstalleerd. Zoals beschreven in de sectie patch-installatie hierboven, worden alle beveiligings-en essentiële updates automatisch geïnstalleerd op uw virtuele machine met [automatische VM-gast patches](../virtual-machines/automatic-vm-guest-patching.md) . er zijn geen extra acties vereist. Patches met andere update classificaties worden niet automatisch geïnstalleerd. In plaats daarvan worden ze weer gegeven in de lijst met beschik bare patches op het tabblad Update naleving. U kunt ook de geschiedenis van update-implementaties op uw virtuele machine bekijken via de update geschiedenis. De update geschiedenis van de afgelopen 30 dagen wordt weer gegeven, samen met informatie over de installatie van de patch.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Hotpatch beheer.":::

Met automatische VM-gast patches wordt uw virtuele machine periodiek en automatisch beoordeeld op beschik bare updates. Deze periodieke evaluaties zorgen ervoor dat er beschik bare patches worden gedetecteerd. U kunt de resultaten van de evaluatie weer geven op het scherm updates hierboven, met inbegrip van de tijd van de laatste evaluatie. U kunt er ook voor kiezen om op elk gewenst moment een patch evaluatie op aanvraag uit te voeren voor uw virtuele machine met behulp van de optie nu evalueren en de resultaten te bekijken nadat de evaluatie is voltooid.

Net als bij evaluatie op aanvraag kunt u ook patches op aanvraag voor uw virtuele machine installeren met behulp van de optie updates nu installeren. Hier kunt u ervoor kiezen om alle updates te installeren onder specifieke patch classificaties. U kunt ook updates opgeven die moeten worden opgenomen of uitgesloten door een lijst met afzonderlijke Knowledge Base-artikelen op te geven. Patches die op aanvraag zijn geïnstalleerd, worden niet geïnstalleerd met behulp van Beschik baarheid-eerste principes en vereisen mogelijk meer opnieuw opstarten en uitval tijd van de VM voor de installatie van de update.

## <a name="supported-updates"></a>Ondersteunde updates

Hotpatch dekt Windows-beveiligings updates en onderhoudt pariteit met de inhoud van beveiligings updates die zijn uitgegeven in het normale (niet-hotpatch) Windows Update-kanaal.

Er zijn enkele belang rijke aandachtspunten voor het uitvoeren van een virtuele machine met Windows Server Azure Edition waarop hotpatch is ingeschakeld. Opnieuw opstarten is nog vereist om updates te installeren die niet zijn opgenomen in het hotpatch-programma. Opnieuw opstarten is ook periodiek vereist nadat een nieuwe basis lijn is geïnstalleerd. Door deze opnieuw op te starten, blijft de virtuele machine gesynchroniseerd met niet-beveiligings patches die zijn opgenomen in de meest recente cumulatieve update.
* Patches die momenteel niet zijn opgenomen in het hotpatch-programma, bevatten niet-beveiligings updates die zijn uitgebracht voor Windows, en niet-Windows-updates (zoals .NET-patches).  Deze typen patches moeten worden geïnstalleerd tijdens een basislijn maand en moeten opnieuw worden opgestart.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="what-is-hotpatching"></a>Wat is hotpatching?

* Hotpatching is een nieuwe manier om updates te installeren op een Windows Server 2019 Data Center: Azure-editie-VM in azure waarvoor opnieuw opstarten na de installatie niet nodig is. Het werkt met de patch voor de code in het geheugen van actieve processen zonder dat het proces opnieuw moet worden gestart.

### <a name="how-does-hotpatching-work"></a>Hoe werkt hotpatching?

* Hotpatching werkt met een basis lijn met een Windows Update meest recente cumulatieve update en bouwt vervolgens op die basis lijn met updates die niet nodig zijn om opnieuw te worden opgestart.  De basis lijn wordt regel matig bijgewerkt met een nieuwe cumulatieve update. De cumulatieve update omvat alle beveiligings-en kwaliteits updates en moet opnieuw worden opgestart.

### <a name="why-should-i-use-hotpatch"></a>Waarom moet ik hotpatch gebruiken?

* Wanneer u hotpatch gebruikt op Windows Server 2019 Data Center: Azure Edition, heeft uw virtuele machine een hogere Beschik baarheid (minder opnieuw opstarten) en snellere updates (kleinere pakketten die sneller worden geïnstalleerd zonder dat de processen opnieuw moeten worden opgestart). Dit proces resulteert in een VM die altijd up-to-date is en beveiligd is.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Welke typen updates worden gedekt door hotpatch?

* Hotpatch is momenteel van toepassing op Windows-beveiligings updates.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Wanneer ontvang ik de eerste hotpatch-update?

* Hotpatch-updates worden meestal op de tweede dinsdag van elke maand uitgebracht. Zie hieronder voor meer informatie.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Hoe ziet de hotpatch-planning eruit?

* Hotpatching werkt door een basis lijn in te stellen met een Windows Update meest recente cumulatieve update, waarna op die basis lijn wordt gebouwd met hotpatch-updates die maandelijks worden uitgebracht.  Tijdens de preview worden de basis lijnen vrijgegeven om de drie maanden. Zie de onderstaande afbeelding voor een voor beeld van een jaarlijks schema van drie maanden (inclusief voor beelden van niet-geplande basis lijnen als gevolg van correcties van nul dagen).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Voorbeeld schema voor hotpatch.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Worden er nog steeds opnieuw opgestart voor een virtuele machine die is geregistreerd in hotpatch?

* Opnieuw opstarten is nog steeds vereist om updates te installeren die niet zijn opgenomen in het hotpatch-programma en die regel matig worden vereist nadat een basis lijn (Windows Update meest recente cumulatieve update) is geïnstalleerd. Bij het opnieuw opstarten wordt uw virtuele machine gesynchroniseerd met alle patches die zijn opgenomen in de cumulatieve update. Basis lijnen (die opnieuw moeten worden opgestart) worden uitgevoerd op een uitgebracht van drie maanden en nemen meer tijd in beslag.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Worden mijn toepassingen beïnvloed wanneer een hotpatch-update wordt geïnstalleerd?

* Omdat hotpatch de code in het geheugen van actieve processen bijwerkt zonder dat het proces opnieuw moet worden gestart, worden uw toepassingen niet beïnvloed door het patch proces. Houd er rekening mee dat dit is gescheiden van de mogelijke gevolgen voor de prestaties en functionaliteit van de patch zelf.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Kan ik hotpatch uitschakelen op mijn VM?

* U kunt hotpatch op een virtuele machine uitschakelen via de Azure Portal.  Door hotpatch uit te scha kelen, wordt de registratie van de virtuele machine ongedaan gemaakt vanaf hotpatch, waardoor de virtuele machine wordt hersteld naar een normaal update gedrag voor Windows Server.  Zodra u de registratie van hotpatch op een virtuele machine ongedaan hebt gemaakt, kunt u die VM opnieuw inschrijven wanneer de volgende hotpatch-basis lijn wordt vrijgegeven.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Kan ik een upgrade uitvoeren van mijn bestaande Windows Server-besturings systeem?

* Een upgrade uitvoeren van bestaande versies van Windows Server (dat wil zeggen, Windows Server 2016 of 2019 niet-Azure-edities) wordt momenteel niet ondersteund. Upgraden naar toekomstige releases van Windows Server Azure Edition wordt ondersteund.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Kan ik hotpatch gebruiken voor werk belastingen voor productie tijdens de preview?

* Previews zijn alleen bedoeld voor test doeleinden en niet voor gebruik in productie omgevingen.

### <a name="will-i-be-charged-during-the-preview"></a>Worden er kosten in rekening gebracht tijdens de preview?

* De licentie voor Windows Server Azure Edition is gratis tijdens de preview-versie. De kosten van een onderliggende infra structuur die is ingesteld voor uw virtuele machine (opslag, berekening, netwerken, enzovoort), worden echter wel in rekening gebracht voor uw abonnement.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Hoe kan ik probleemoplossings ondersteuning voor hotpatching krijgen?

* U kunt een [technisch ondersteunings ticket](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)indienen. Voor de optie service zoekt en selecteert u de **virtuele machine waarop Windows wordt uitgevoerd** onder compute. Selecteer **Azure-functies** voor het probleem type en **automatische VM-gast patching** voor het subtype probleem.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Azure [updatebeheer.](../automation/update-management/overview.md)
* Meer informatie over automatische VM-gast patches [hier](../virtual-machines/automatic-vm-guest-patching.md)