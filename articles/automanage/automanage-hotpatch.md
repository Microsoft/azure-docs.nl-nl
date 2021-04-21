---
title: Hotpatch voor Windows Server Azure Edition (preview)
description: Meer informatie over hotpatch voor Windows Server Azure Edition en hoe u deze kunt inschakelen
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b3fc9f12dfa6ad4edcc120ac7c9592c9435a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830174"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Hotpatch voor nieuwe virtuele machines (preview)

Hotpatching is een nieuwe manier om updates te installeren op nieuwe virtuele windows Server Azure Edition-machines (VM's) waarvoor na de installatie niet opnieuw hoeft te worden opgestart. Dit artikel bevat informatie over hotpatch voor virtuele Windows Server Azure Edition-VM's. Dit heeft de volgende voordelen:
* Minder workloadimpact met minder herstarts
* Snellere implementatie van updates omdat de pakketten kleiner zijn, sneller worden geïnstalleerd en eenvoudiger patch-orchestration hebben met Azure Update Manager
* Betere beveiliging, omdat de hotpatch-updatepakketten zijn beperkt tot Windows-beveiligingsupdates die sneller worden geïnstalleerd zonder opnieuw op te starten

## <a name="how-hotpatch-works"></a>Hoe hotpatch werkt

Hotpatch werkt door eerst een basislijn op te stellen met een Windows Update laatste cumulatieve update. Hotpatches worden periodiek uitgebracht (bijvoorbeeld op de tweede dinsdag van de maand) die op die basislijn voortbouwen. Hotpatches bevatten updates waarvoor opnieuw opstarten niet is vereist. Periodiek (vanaf elke drie maanden) wordt de basislijn vernieuwd met een nieuwe laatste cumulatieve update.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch-voorbeeldschema.":::

Er zijn twee typen basislijnen: **geplande basislijnen** en **niet-geplande basislijnen.**
*  **Geplande basislijnen** worden regelmatig uitgebracht, met tussendoor hotpatch-releases.  Geplande basislijnen omvatten alle updates in een vergelijkbare laatste cumulatieve _update_ voor die maand en moeten opnieuw worden opgestart.
    * Het bovenstaande voorbeeldschema illustreert vier geplande basislijnreleases in een kalenderjaar (vijf totaal in het diagram) en acht hotpatch-releases.
* **Niet-geplande basislijnen** worden uitgebracht wanneer een belangrijke update (zoals een zero-day fix) wordt uitgebracht en een bepaalde update niet als hotpatch kan worden uitgebracht.  Wanneer niet-geplande basislijnen worden uitgebracht, wordt een hotpatch-release in die maand vervangen door een niet-geplande basislijn.  Niet-geplande basislijnen bevatten ook alle updates in een vergelijkbare laatste cumulatieve _update_ voor die maand en vereisen ook opnieuw opstarten.
    * Het bovenstaande voorbeeldschema illustreert twee niet-geplande basislijnen die de hotpatch-releases voor die maanden zouden vervangen (het werkelijke aantal niet-geplande basislijnen in een jaar is vooraf niet bekend).

## <a name="regional-availability"></a>Regionale beschikbaarheid
Hotpatch is beschikbaar in alle algemene Azure-regio's in preview. Azure Government regio's worden niet ondersteund in de preview-versie.

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag

> [!NOTE]
> Tijdens de preview-fase kunt u alleen aan de slag in de Azure Portal met [behulp van deze koppeling](https://aka.ms/AzureAutomanageHotPatch).

Als u Hotpatch wilt gebruiken op een nieuwe VM, volgt u deze stappen:
1.  Preview-toegang inschakelen
    * Een een time-time preview-toegang is vereist per abonnement.
    * Preview-toegang kan worden ingeschakeld via API, PowerShell of CLI, zoals beschreven in de volgende sectie.
1.  Een VM maken op Azure Portal
    * Tijdens de preview moet u aan de slag met [deze koppeling](https://aka.ms/AzureAutomanageHotPatch).
1.  VM-gegevens verstrekken
    * Zorg ervoor _dat Windows Server 2019 Datacenter: Azure Edition_ is geselecteerd in de vervolgkeuzeruimte Image)
    * Schuif op de tabbladstap Beheer omlaag naar de sectie 'Updates van gast-besturingssysteem'. U ziet dat Hotpatching is ingesteld op Aan en De installatie van de patch is standaard ingesteld op Door Azure-orchestrated patching.
    * Best practices voor automatischmanage van VM's worden standaard ingeschakeld
1. Uw nieuwe VM maken

## <a name="enabling-preview-access"></a>Preview-toegang inschakelen

### <a name="rest-api"></a>REST-API

In het volgende voorbeeld wordt beschreven hoe u de preview-versie voor uw abonnement inschakelen:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te gaan naar de Compute-resourceprovider.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de ```Register-AzProviderFeature``` cmdlet om de preview voor uw abonnement in te stellen.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te gaan naar de Compute-resourceprovider.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

Gebruik ```az feature register``` om de preview voor uw abonnement in te stellen.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

De registratie van functies kan maximaal 15 minuten duren. De registratiestatus controleren:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Zodra de functie is geregistreerd voor uw abonnement, voltooit u het opt-in-proces door de wijziging door te gaan naar de Compute-resourceprovider.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Patchinstallatie

Tijdens de preview wordt [automatische VM-gastpatching](../virtual-machines/automatic-vm-guest-patching.md) automatisch ingeschakeld voor alle VM's die zijn gemaakt _met Windows Server 2019 Datacenter: Azure Edition._ Met automatische VM-gastpatching ingeschakeld:
* Patches die zijn geclassificeerd als Kritiek of Beveiliging worden automatisch gedownload en toegepast op de VM.
* Patches worden toegepast tijdens daluren in de tijdzone van de VM.
* Patch-orchestration wordt beheerd door Azure en patches worden toegepast volgens [de beschikbaarheidsprincipes](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching).
* De status van virtuele machines, zoals bepaald door middel van statussignalen van het platform, wordt gecontroleerd om patchfouten te detecteren.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Hoe werkt automatische VM-gastpatching?

Wanneer [automatische VM-gastpatches](../virtual-machines/automatic-vm-guest-patching.md) is ingeschakeld op een VM, worden de beschikbare essentiële en beveiligingspatches gedownload en automatisch toegepast. Dit proces wordt elke maand automatisch van start gaat wanneer er nieuwe patches worden uitgebracht. Patchevaluatie en -installatie worden automatisch uitgevoerd en het proces omvat het opnieuw opstarten van de VM, indien nodig.

Als Hotpatch is ingeschakeld in _Windows Server 2019 Datacenter: Azure Edition_ VM's, worden de meeste maandelijkse beveiligingsupdates geleverd als hotpatches waarvoor niet opnieuw hoeft te worden opgestart. Voor de meest recente cumulatieve updates die worden verzonden op geplande of niet-geplande basislijn maanden, moet de VM opnieuw worden opgestart. Er kunnen ook periodiek aanvullende essentiële of beveiligingspatches beschikbaar zijn, waarvoor de VM mogelijk opnieuw moet worden opgestart.

De VM wordt automatisch om de paar dagen en meerdere keren binnen een periode van 30 dagen beoordeeld om de toepasselijke patches voor die VM te bepalen. Deze automatische evaluatie zorgt ervoor dat ontbrekende patches zo snel mogelijk worden ontdekt.

Patches worden geïnstalleerd binnen 30 dagen na de maandelijkse patchreleases, volgens [de principes voor beschikbaarheid.](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching) Patches worden alleen geïnstalleerd buiten piekuren voor de VM, afhankelijk van de tijdzone van de VM. De VM moet actief zijn tijdens de daluren om ervoor te zorgen dat patches automatisch worden geïnstalleerd. Als een VM tijdens een periodieke evaluatie wordt uitgeschakeld, wordt de VM beoordeeld en worden de toepasselijke patches automatisch geïnstalleerd tijdens de volgende periodieke evaluatie wanneer de VM wordt ingeschakeld. De volgende periodieke evaluatie vindt doorgaans binnen een paar dagen plaats.

Definitie-updates en andere patches die niet zijn geclassificeerd als Kritiek of Beveiliging, worden niet geïnstalleerd via automatische VM-gastpatches.

## <a name="understanding-the-patch-status-for-your-vm"></a>Informatie over de patchstatus voor uw VM

Als u de patchstatus voor uw VM wilt weergeven, gaat u naar de sectie **Gast-** en hostupdates voor uw VM in de Azure Portal. Klik in **de sectie Updates van** gastbesturingssysteem op 'Ga naar Hotpatch (preview)' om de meest recente patchstatus voor uw VM weer te geven.

In dit scherm ziet u de hotpatch-status voor uw VM. U kunt ook controleren of er beschikbare patches zijn voor uw VM die niet zijn geïnstalleerd. Zoals beschreven in de sectie Patchinstallatie hierboven, worden alle beveiligingsupdates en essentiële updates automatisch geïnstalleerd op uw VM met automatische [VM-gastpatching](../virtual-machines/automatic-vm-guest-patching.md) en zijn er geen extra acties vereist. Patches met andere updateclassificaties worden niet automatisch geïnstalleerd. In plaats daarvan kunnen ze worden weergegeven in de lijst met beschikbare patches op het tabblad Update-naleving. U kunt ook de geschiedenis van update-implementaties op uw VM bekijken via de 'Updategeschiedenis'. De updategeschiedenis van de afgelopen 30 dagen wordt weergegeven, samen met de installatiedetails van de patch.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Hotpatch-beheer.":::

Met automatische VM-gastpatching wordt uw VM periodiek en automatisch beoordeeld op beschikbare updates. Deze periodieke evaluaties zorgen ervoor dat beschikbare patches worden gedetecteerd. U kunt de resultaten van de evaluatie bekijken op het scherm Updates hierboven, inclusief het tijdstip van de laatste evaluatie. U kunt er ook voor kiezen om op elk moment een patchevaluatie op aanvraag te activeren voor uw VM met behulp van de optie Nu beoordelen en de resultaten te bekijken nadat de evaluatie is voltooid.

Net als bij evaluatie op aanvraag kunt u ook patches op aanvraag installeren voor uw VM met behulp van de optie Nu updates installeren. Hier kunt u ervoor kiezen om alle updates onder specifieke patchclassificaties te installeren. U kunt ook updates opgeven die moeten worden opgenomen of uitgesloten door een lijst met afzonderlijke Knowledge Base-artikelen op te geven. Patches die op aanvraag zijn geïnstalleerd, worden niet geïnstalleerd volgens de principes van availability-first en vereisen mogelijk meer herstarts en VM-downtime voor de installatie van updates.

## <a name="supported-updates"></a>Ondersteunde updates

Hotpatch behandelt Windows-beveiliging updates en onderhoudt pariteit met de inhoud van beveiligingsupdates die zijn uitgegeven aan in het normale (niet-hotpatch) Windows-updatekanaal.

Er zijn enkele belangrijke overwegingen voor het uitvoeren van een Windows Server Azure Edition-VM met Hotpatch ingeschakeld. Opnieuw opstarten is nog steeds vereist om updates te installeren die niet zijn opgenomen in het Hotpatch-programma. Opnieuw opstarten is ook regelmatig vereist nadat een nieuwe basislijn is geïnstalleerd. Deze herstarts zorgen ervoor dat de VM gesynchroniseerd blijft met niet-beveiligingspatches die zijn opgenomen in de meest recente cumulatieve update.
* Patches die momenteel niet zijn opgenomen in het hotpatch-programma bevatten niet-beveiligingsupdates die zijn uitgebracht voor Windows en niet-Windows-updates (zoals .NET-patches).  Deze typen patches moeten tijdens een basislijnmaand worden geïnstalleerd en moeten opnieuw worden opgestart.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="what-is-hotpatching"></a>Wat is hotpatching?

* Hotpatching is een nieuwe manier om updates te installeren op een Windows Server 2019 Datacenter: Azure Edition VM in Azure waarvoor na de installatie geen herstart hoeft te worden uitgevoerd. Dit werkt door de code in het geheugen van de lopende processen te patchen zonder dat het proces opnieuw hoeft te worden gestart.

### <a name="how-does-hotpatching-work"></a>Hoe werkt hotpatching?

* Hotpatching werkt door een basislijn tot stand te Windows Update met een Windows Update Laatste cumulatieve update en bouwt vervolgens voort op die basislijn met updates waarvoor geen herstart hoeft te worden doorgevoerd.  De basislijn wordt regelmatig bijgewerkt met een nieuwe cumulatieve update. De cumulatieve update bevat alle beveiligings- en kwaliteitsupdates en vereist opnieuw opstarten.

### <a name="why-should-i-use-hotpatch"></a>Waarom moet ik Hotpatch gebruiken?

* Wanneer u Hotpatch gebruikt in Windows Server 2019 Datacenter: Azure Edition, heeft uw VM een hogere beschikbaarheid (minder herstarts) en snellere updates (kleinere pakketten die sneller worden geïnstalleerd zonder processen opnieuw te hoeven starten). Dit proces resulteert in een VM die altijd up-to-date en veilig is.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Welke typen updates vallen onder Hotpatch?

* Hotpatch heeft momenteel betrekking op Windows-beveiligingsupdates.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Wanneer ontvang ik de eerste hotpatch-update?

* Hotpatch-updates worden doorgaans op de tweede dinsdag van elke maand uitgebracht. Zie hieronder voor meer informatie.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Hoe ziet het hotpatch-schema eruit?

* Hotpatching werkt door een basislijn op te stellen met een Windows Update laatste cumulatieve update, en bouwt vervolgens voort op die basislijn met hotpatch-updates die maandelijks worden uitgebracht.  Tijdens de preview worden basislijnen elke drie maanden uitgebracht. Zie de onderstaande afbeelding voor een voorbeeld van een jaarlijkse planning voor drie maanden (inclusief voorbeeld van niet-geplande basislijnen vanwege zero-day fixes).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Hotpatch-voorbeeldschema.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Moet er nog steeds opnieuw worden opgestart voor een VM die is ingeschreven bij Hotpatch?

* Opnieuw opstarten is nog steeds vereist om updates te installeren die niet zijn opgenomen in het hotpatch-programma en zijn regelmatig vereist nadat een basislijn (Windows Update laatste cumulatieve update) is geïnstalleerd. Door opnieuw op te starten blijft uw VM gesynchroniseerd met alle patches die zijn opgenomen in de cumulatieve update. Basislijnen (waarvoor opnieuw moet worden opgestart) worden met een tijdsfrequentie van drie maanden opgestart en na een periode van drie maanden groter.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Worden mijn toepassingen beïnvloed wanneer er een Hotpatch-update wordt geïnstalleerd?

* Omdat Hotpatch de in-memory code van de lopende processen patcht zonder dat het proces opnieuw hoeft te worden gestart, worden uw toepassingen niet beïnvloed door het patchproces. Houd er rekening mee dat dit los staat van mogelijke gevolgen voor de prestaties en functionaliteit van de patch zelf.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Kan ik Hotpatch uitschakelen op mijn VM?

* U kunt Hotpatch uitschakelen op een VM via de Azure Portal.  Als hotpatch wordt uitgeschakeld, wordt de registratie van de VM bij Hotpatch uitgeschakeld, waardoor de VM weer normaal wordt bijgewerkt voor Windows Server.  Zodra u de registratie van Hotpatch op een VM hebt teruggeplaatst, kunt u die VM opnieuw inschrijven wanneer de volgende Hotpatch-basislijn wordt uitgebracht.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Kan ik upgraden vanuit mijn bestaande Windows Server-besturingssysteem?

* Upgraden van bestaande versies van Windows Server (windows Server 2016 of 2019 niet-Azure-edities) wordt momenteel niet ondersteund. Upgraden naar toekomstige versies van Windows Server Azure Edition wordt ondersteund.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Kan ik Hotpatch gebruiken voor productieworkloads tijdens de preview?

* Previews zijn alleen bedoeld voor testdoeleinden en niet voor gebruik in productieomgevingen.

### <a name="will-i-be-charged-during-the-preview"></a>Worden er kosten in rekening gebracht tijdens de preview?

* De licentie voor Windows Server Azure Edition is gratis tijdens de preview. De kosten van een onderliggende infrastructuur die is ingesteld voor uw VM (opslag, rekenkracht, netwerken, enzovoort) worden echter nog steeds in rekening gebracht op uw abonnement.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Hoe krijg ik ondersteuning voor probleemoplossing voor hotpatching?

* U kunt een ticket voor [een technische ondersteuningscase indienen.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Zoek en selecteer virtuele machine met **Windows** onder Compute voor de optie Service. Selecteer **Azure-functies** als het probleemtype en **Automatische VM-gastpatching** voor het subtype probleem.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over Azure Updatebeheer [hier](../automation/update-management/overview.md).
* Meer informatie over automatische VM-gastpatching [is hier te vinden](../virtual-machines/automatic-vm-guest-patching.md)
