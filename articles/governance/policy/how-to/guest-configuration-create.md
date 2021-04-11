---
title: Beleidsregels voor gastconfiguratie voor Windows maken
description: Meer informatie over het maken van een Azure Policy-gast configuratie beleid voor Windows.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 72772743eba23ea7c2a93f5037ac84b671256a66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104887696"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Beleidsregels voor gastconfiguratie voor Windows maken

Voordat u aangepaste beleids definities maakt, is het een goed idee om de conceptuele overzichts informatie op de pagina [Azure Policy gast configuratie](../concepts/guest-configuration.md)te lezen.
 
Ga voor meer informatie over het maken van gast configuratie beleidsregels voor Linux naar de pagina [beleid voor gast configuratie maken voor Linux](./guest-configuration-create-linux.md)

Bij het controleren van Windows gebruikt gastconfiguratie de resourcemodule [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) om het configuratiebestand te maken. De DSC-configuratie definieert de toestand waarin de machine zich moet bevinden. Als de evaluatie van de configuratie mislukt, wordt het beleids effect **auditIfNotExists** geactiveerd en wordt de computer als **niet-compatibel** beschouwd.

[Azure Policy-gast configuratie](../concepts/guest-configuration.md) kan alleen worden gebruikt voor het controleren van instellingen binnen machines. Herstel van instellingen binnen computers is nog niet beschikbaar.

Gebruik de volgende acties om uw eigen configuratie te maken voor het valideren van de status van een Azure-of niet-Azure-machine.

> [!IMPORTANT]
> Aangepaste beleids definities met gast configuratie in de Azure Government-en Azure China-omgevingen is een preview-functie.
>
> De gastconfiguratie-extensie is vereist voor het uitvoeren van controles op virtuele machines van Azure.
> Als u de uitbrei ding op schaal op alle Windows-computers wilt implementeren, wijst u de volgende beleids definities toe: `Deploy prerequisites to enable Guest Configuration Policy on Windows VMs`
> 
> Gebruik geen geheimen of vertrouwelijke informatie in aangepaste inhouds pakketten.

## <a name="install-the-powershell-module"></a>De PowerShell-module installeren

De gast configuratie module automatiseert het proces van het maken van aangepaste inhoud, waaronder:

- Een inhouds artefact voor een gast configuratie maken (. zip)
- Geautomatiseerd testen van het artefact
- Een beleids definitie maken
- Het beleid publiceren

De module kan worden geïnstalleerd op een computer met Windows, macOS of Linux met Power shell 6,2 of later lokaal of met [Azure Cloud shell](https://shell.azure.com), of met de [Azure PowerShell core docker-installatie kopie](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> De compilatie van configuraties wordt nog niet ondersteund in Linux.

### <a name="base-requirements"></a>Basisvereisten

Besturings systemen waarop de module kan worden geïnstalleerd:

- Linux
- macOS
- Windows

Voor de module gast configuratie resource is de volgende software vereist:

- Power shell 6,2 of hoger. Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/powershell/scripting/install/installing-powershell) op.
- Azure PowerShell 1.5.0 of hoger. Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/powershell/azure/install-az-ps) op.
  - Alleen de AZ-modules AZ. accounts en AZ. resources zijn vereist.

### <a name="install-the-module"></a>Installeer de module

De **GuestConfiguration** -module installeren in Power shell:

1. Voer de volgende opdracht uit vanuit een Power shell-prompt:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Controleer of de module is geïmporteerd:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Gast configuratie artefacten en beleid voor Windows

Gast configuratie maakt gebruik van de gewenste status configuratie van Power shell als een taal abstractie voor het schrijven van wat er moet worden gecontroleerd in Windows. De agent laadt een zelfstandig exemplaar van Power shell 6,2, dus er is geen conflict met het gebruik van Power shell DSC in Windows Power shell 5,1 en er is geen vereiste om vooraf Power shell 6,2 of hoger te installeren.

Zie [overzicht van Power shell DSC](/powershell/scripting/dsc/overview/overview)voor een overzicht van DSC-concepten en terminologie.

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Hoe gast configuratie modules verschillen van Windows Power shell DSC-modules

Wanneer een gast configuratie een computer controleert, is de volg orde van de gebeurtenissen anders dan in Windows Power shell DSC.

1. De agent wordt eerst uitgevoerd `Test-TargetResource` om te bepalen of de configuratie de juiste status heeft.
1. De Booleaanse waarde die door de functie wordt geretourneerd, bepaalt of de Azure Resource Manager status voor de gast toewijzing compatibel/niet-compatibel moet zijn.
1. De provider wordt uitgevoerd `Get-TargetResource` om de huidige status van elke instelling te retour neren, zodat er informatie beschikbaar is over waarom een machine niet aan het beleid voldoet en om te bevestigen dat de huidige status compatibel is.

Para meters in Azure Policy die waarden door geven aan gast configuratie toewijzingen moeten een _teken reeks_ type zijn. Het is niet mogelijk matrices door te geven via para meters, zelfs als de DSC-resource matrices ondersteunt.

### <a name="get-targetresource-requirements"></a>Get-TargetResource vereisten

De functie `Get-TargetResource` heeft speciale vereisten voor gast configuratie die niet nodig is voor de configuratie van de desired state van Windows.

- De geretourneerde hashtabel moet een eigenschap met de naam **redenen** bevatten.
- De eigenschap redenen moet een matrix zijn.
- Elk item in de matrix moet een hashtabel zijn met sleutels met de naam **code** en een **zin**.

De eigenschap redenen wordt door de service gebruikt om te standaardiseren hoe informatie wordt gepresenteerd wanneer een computer niet meer compatibel is. U kunt elk item beschouwen als een ' reden ' dat de resource niet aan het beleid voldoet. De eigenschap is een matrix omdat een bron niet meer dan één reden kan worden nageleefd.

De instellingen voor de eigenschappen **code** en de **term** worden verwacht door de service. Wanneer u een aangepaste resource ontwerpt, stelt u de tekst (meestal stdout) in die u wilt weer geven als de reden waarom de resource niet aan de voor waarden voor **woord groep** voldoet. **Code** heeft specifieke opmaak vereisten, zodat rapportage duidelijk informatie kan weer geven over de resource die wordt gebruikt om de controle uit te voeren. Met deze oplossing wordt de gast configuratie uitbreidbaar. Elke opdracht kan worden uitgevoerd zolang de uitvoer kan worden geretourneerd als een teken reeks waarde voor de eigenschap **phrase** .

- **Code** (teken reeks): de naam van de resource, herhaald en vervolgens een korte naam zonder spaties als een id voor de reden. Deze drie waarden moeten door een dubbele punt worden gescheiden zonder spaties.
  - Een voor beeld is `registry:registry:keynotpresent`
- **Woordgroepen** (teken reeks): Lees bare tekst om uit te leggen waarom de instelling niet aan het beleid voldoet.
  - Een voor beeld is `The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

De eigenschap redenen moet worden toegevoegd aan de schema-MOF voor de resource als een Inge sloten klasse.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

Als de resource vereiste eigenschappen heeft, moeten deze ook `Get-TargetResource` parallel met de klasse worden geretourneerd `reasons` . Als `reasons` dit niet het geval is, omvat de service het gedrag ' catch-all ' waarmee de waarden worden vergeleken met `Get-TargetResource` en de waarden die worden geretourneerd door en `Get-TargetResource` die een gedetailleerde vergelijking biedt als `reasons` .

### <a name="configuration-requirements"></a>Configuratievereisten

De naam van de aangepaste configuratie moet consistent zijn. De naam van het zip-bestand voor het inhouds pakket, de configuratie naam in het MOF-bestand en de naam van de gast toewijzing in de Azure Resource Manager sjabloon (ARM-sjabloon) moet hetzelfde zijn.

### <a name="policy-requirements"></a>Beleids vereisten

De sectie beleids definitie `metadata` moet twee eigenschappen bevatten voor de gast configuratie service om het inrichten en rapporteren van gast configuratie toewijzingen te automatiseren. De `category` eigenschap moet worden ingesteld op ' gast configuratie ' en een sectie `Guest Configuration` met de naam moet informatie bevatten over de toewijzing van de gast configuratie. Met `New-GuestConfigurationPolicy` deze cmdlet wordt deze tekst automatisch gemaakt.
Zie de stapsgewijze instructies op deze pagina.

In het volgende voor beeld wordt de sectie gedemonstreerd `metadata` .

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

### <a name="scaffolding-a-guest-configuration-project"></a>Een configuratie project voor een gast steiger

Ontwikkel aars die het proces van aan de slag willen versnellen en werken vanuit voorbeeld code, kunnen een community-project met de naam **gast configuratie project** installeren. Het project installeert een sjabloon voor de [gips](https://github.com/powershell/plaster) -Power shell-module. Dit hulp programma kan worden gebruikt om een project te maken, met inbegrip van een werkende configuratie en voorbeeld resource, en een verzameling [ziekte](https://github.com/pester/pester) tests om het project te valideren. De sjabloon bevat ook taak lopers voor Visual Studio code voor het automatiseren van het maken en valideren van het gast configuratie pakket. Zie het project [gast configuratie](https://github.com/microsoft/guestconfigurationproject)van het project github voor meer informatie.

Zie [een configuratie schrijven, compileren en Toep assen](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)voor meer informatie over het werken met configuraties in het algemeen.

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>De verwachte inhoud van een gast configuratie artefact

Het voltooide pakket wordt gebruikt door de gast configuratie om de Azure Policy definities te maken. Het pakket bestaat uit:

- De gecompileerde DSC-configuratie als een MOF
- Map modules
  - GuestConfiguration-module
  - DscNativeResources-module
  - Windows DSC-resource modules die zijn vereist voor de MOF

Power shell-cmdlets helpen bij het maken van het pakket.
Er is geen map op het hoofd niveau of de versie van de map vereist.
De pakket indeling moet een zip-bestand zijn en mag niet groter zijn dan de totale grootte van 100 MB bij niet-gecomprimeerde bestanden.

### <a name="storing-guest-configuration-artifacts"></a>Gast configuratie artefacten opslaan

Het zip-pakket moet worden opgeslagen op een locatie die toegankelijk is voor de beheerde virtuele machines.
Voor beelden zijn GitHub-opslag plaatsen, een Azure-opslag plaats of Azure-opslag. Als u het pakket liever niet openbaar wilt maken, kunt u een [SAS-token](../../../storage/common/storage-sas-overview.md) in de URL toevoegen. U kunt ook [service-eind punten](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) implementeren voor computers in een particulier netwerk, hoewel deze configuratie alleen van toepassing is op toegang tot het pakket en niet communiceert met de service.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Stapsgewijze procedure, het maken van een aangepaste gast configuratie-controle beleid voor Windows

Een DSC-configuratie maken om instellingen te controleren. In het volgende Power shell-voorbeeld script wordt een configuratie met de naam **AuditBitLocker** gemaakt, de **PsDscResources** -resource module geïmporteerd en de `Service` resource gebruikt om te controleren of er een actieve service is. Het configuratie script kan worden uitgevoerd vanaf een Windows-of macOS-computer.

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

Voer dit script uit in een Power shell-terminal of sla dit bestand `config.ps1` op met de naam in de projectmap.
Voer deze uit in Power shell door `./config.ps1` uit te voeren in de Terminal. Er wordt een nieuw MOF-bestand gemaakt.

De `Node AuditBitlocker` opdracht is niet technisch vereist, maar er wordt een bestand gemaakt met de naam `AuditBitlocker.mof` in plaats van de standaard waarde `localhost.mof` . Als u de bestands naam. MOF volgt, is het eenvoudig om veel bestanden te organiseren wanneer deze op schaal worden uitgevoerd.

Zodra de MOF is gecompileerd, moeten de ondersteunende bestanden samen worden verpakt. Het voltooide pakket wordt gebruikt door de gast configuratie om de Azure Policy definities te maken.

Met de `New-GuestConfigurationPackage` cmdlet maakt u het pakket. Modules die nodig zijn voor de configuratie, moeten beschikbaar zijn in `$Env:PSModulePath` . Para meters van de `New-GuestConfigurationPackage` cmdlet bij het maken van Windows-inhoud:

- **Naam**: naam van het gast configuratie pakket.
- **Configuratie**: gecompileerd DSC-configuratie document volledig pad.
- **Pad**: pad naar map voor uitvoer. Deze parameter is optioneel. Als u niets opgeeft, wordt het pakket gemaakt in de huidige map.

Voer de volgende opdracht uit om een pakket te maken met behulp van de configuratie die in de vorige stap is gegeven:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './AuditBitlocker/AuditBitlocker.mof'
```

Nadat u het configuratie pakket hebt gemaakt, maar voordat u het naar Azure publiceert, kunt u het pakket testen vanaf uw werk station of doorlopende integratie-en continue implementatie (CI/CD)-omgeving. De GuestConfiguration-cmdlet `Test-GuestConfigurationPackage` bevat dezelfde agent in uw ontwikkel omgeving als wordt gebruikt binnen Azure-machines. Met deze oplossing kunt u integratie lokaal testen voordat u overbrengt naar gefactureerde Cloud omgevingen.

Omdat de agent de lokale omgeving werkelijk evalueert, moet u in de meeste gevallen de test-cmdlet uitvoeren op hetzelfde OS-platform als u wilt controleren. De test maakt alleen gebruik van modules die zijn opgenomen in het inhouds pakket.

Para meters van de `Test-GuestConfigurationPackage` cmdlet:

- **Naam**: naam van het gast configuratie beleid.
- **Para meter**: beleids parameters die zijn opgegeven in de hashtabel-indeling.
- **Pad**: volledig pad van het gast configuratie pakket.

Voer de volgende opdracht uit om het pakket te testen dat door de vorige stap is gemaakt:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

De cmdlet ondersteunt ook invoer van de Power shell-pijp lijn. Pipet de uitvoer van de `New-GuestConfigurationPackage` cmdlet naar de `Test-GuestConfigurationPackage` cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./AuditBitlocker/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

De volgende stap is het publiceren van het bestand naar Azure Blob Storage. Er zijn geen speciale vereisten voor het opslag account, maar het is een goed idee om het bestand in een regio in de buurt van uw computers te hosten. Als u geen opslag account hebt, gebruikt u het volgende voor beeld. Voor de onderstaande opdrachten, inclusief `Publish-GuestConfigurationPackage` , is de `Az.Storage` module vereist.

```azurepowershell-interactive
# Creates a new resource group, storage account, and container
New-AzResourceGroup -name myResourceGroupName -Location WestUS
New-AzStorageAccount -ResourceGroupName myResourceGroupName -Name myStorageAccountName -SkuName 'Standard_LRS' -Location 'WestUs' | New-AzStorageContainer -Name guestconfiguration -Permission Blob
```

Para meters van de `Publish-GuestConfigurationPackage` cmdlet:

- **Pad**: locatie van het pakket dat moet worden gepubliceerd
- **ResourceGroupName**: de naam van de resource groep waar het opslag account zich bevindt
- **StorageAccountName**: naam van het opslag account waarin het pakket moet worden gepubliceerd
- **StorageContainerName**: (standaard: *guestconfiguration*) naam van de opslag container in het opslag account
- **Geforceerd**: bestaand pakket overschrijven in het opslag account met dezelfde naam

In het volgende voor beeld wordt het pakket gepubliceerd naar een opslag container naam ' guestconfiguration '.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

Nadat een aangepast beleids pakket voor de gast configuratie is gemaakt en geüpload, maakt u de beleids definitie voor het gast configuratie beleid. De `New-GuestConfigurationPolicy` cmdlet gebruikt een aangepast beleids pakket en maakt een beleids definitie.

Para meters van de `New-GuestConfigurationPolicy` cmdlet:

- **ContentUri**: open bare http (s)-URI van het inhouds pakket voor de gast configuratie.
- **DisplayName**: weergave naam beleid.
- **Beschrijving**: beschrijving van het beleid.
- **Para meter**: beleids parameters die zijn opgegeven in de hashtabel-indeling.
- **Versie**: beleids versie.
- **Pad**: doelpad voor het maken van beleids definities.
- **Platform**: doel platform (Windows/Linux) voor gast configuratie beleid en inhouds pakket.
- **Tag** voegt een of meer label filters toe aan de beleids definitie
- **Categorie** stelt het veld meta gegevens van categorie in de beleids definitie in

In het volgende voor beeld worden de beleids definities in een opgegeven pad van een aangepast beleids pakket gemaakt:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

De volgende bestanden worden gemaakt door `New-GuestConfigurationPolicy` :

- **auditIfNotExists.jsop**

De cmdlet-uitvoer retourneert een object dat de weergave naam en het pad van de beleids bestanden bevat.

Ten slotte publiceert u de beleids definities met de `Publish-GuestConfigurationPolicy` cmdlet. De cmdlet heeft alleen de para meter **Path** die verwijst naar de locatie van de json-bestanden die zijn gemaakt door `New-GuestConfigurationPolicy` .

Als u de opdracht publiceren wilt uitvoeren, moet u toegang hebben tot beleids regels maken in Azure. De specifieke autorisatie vereisten worden beschreven op de pagina [overzicht van Azure Policy](../overview.md) . De beste ingebouwde rol is Inzender voor **resource beleid**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

De `Publish-GuestConfigurationPolicy` cmdlet accepteert het pad van de Power shell-pijp lijn. Deze functie houdt in dat u de beleids bestanden kunt maken en publiceren in één set opdrachten die zijn gesluisd.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Met het beleid dat is gemaakt in azure, is de laatste stap het toewijzen van de definitie. Zie de definitie toewijzen met [Portal](../assign-policy-portal.md), [Azure cli](../assign-policy-azurecli.md)en [Azure PowerShell](../assign-policy-powershell.md).

### <a name="filtering-guest-configuration-policies-using-tags"></a>Beleid voor gast configuratie filteren met behulp van Tags

De beleids definities die zijn gemaakt door cmdlets in de module gast configuratie kunnen eventueel een filter voor labels bevatten. De **tag** para meter van `New-GuestConfigurationPolicy` ondersteunt een matrix met hashtabellen die afzonderlijke tags bevatten. De tags worden toegevoegd aan de `If` sectie van de beleids definitie en kunnen niet worden gewijzigd door een beleids toewijzing.

Hieronder vindt u een voorbeeld fragment van een beleids definitie waarmee filters voor labels worden opgegeven.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Para meters gebruiken in de aangepaste gast configuratie beleids definities

Gast configuratie ondersteunt het overschrijven van eigenschappen van een configuratie tijdens runtime. Deze functie betekent dat de waarden in het MOF-bestand in het pakket niet als statisch moeten worden beschouwd. De onderdrukkings waarden worden geleverd via Azure Policy en hebben geen invloed op de manier waarop de configuraties worden gemaakt of gecompileerd.

De cmdlets `New-GuestConfigurationPolicy` en `Test-GuestConfigurationPolicyPackage` bevatten een para meter met de naam **para meter**. Deze para meter gebruikt de definitie van de hash-tabel, inclusief alle details over elke para meter en maakt de vereiste secties van elk bestand dat wordt gebruikt voor de Azure Policy definitie.

In het volgende voor beeld wordt een beleids definitie gemaakt voor het controleren van een service, waarbij de gebruiker selecteert in een lijst op het moment van beleids toewijzing.

```azurepowershell-interactive
# This DSC Resource text:
Service 'UserSelectedNameExample'
      {
          Name = 'ParameterValue'
          Ensure = 'Present'
          State = 'Running'
      }

# Would require the following hashtable:
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'UserSelectedNameExample'                                   # DSC configuration resource id (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>Gast configuratie uitbreiden met hulpprogram ma's van derden

De artefact pakketten voor gast configuratie kunnen worden uitgebreid met hulpprogram ma's van derden.
Het uitbreiden van de gast configuratie vereist het ontwikkelen van twee onderdelen.

- Een desired state Configuration-resource die alle activiteiten afhandelt die betrekking hebben op het beheer van het hulp programma van derden
  - Installeren
  - Aanroepen
  - Uitvoer converteren
- Inhoud in de juiste indeling voor het hulp programma dat systeem eigen verbruikt

Voor de DSC-resource is aangepaste ontwikkeling vereist als er nog geen community-oplossing bestaat.
U kunt community-oplossingen detecteren door de PowerShell Gallery te doorzoeken op tag [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22).

> [!Note]
> De uitbrei ding van gast configuratie is een scenario voor het nemen van uw eigen licentie. Zorg ervoor dat u voldoet aan de voor waarden van andere hulpprogram ma's van derden vóór gebruik.

Nadat de DSC-resource in de ontwikkel omgeving is geïnstalleerd, gebruikt u de para meter **FilesToInclude** voor `New-GuestConfigurationPackage` om inhoud voor het platform van derden in het inhouds artefact op te neemt.

## <a name="policy-lifecycle"></a>Levens duur van beleid

Als u een update wilt vrijgeven voor het beleid, brengt u de wijziging aan voor zowel het gast configuratie pakket als de details van de Azure Policy definitie.

> [!NOTE]
> De `version` eigenschap van de toewijzing van de gast configuratie heeft alleen invloed op pakketten die door micro soft worden gehost. De best practice voor het versie beheer van aangepaste inhoud is het insluiten van de versie in de bestands naam.

Geef bij uitvoering `New-GuestConfigurationPackage` een naam op voor het pakket dat deze uniek maakt ten opzichte van vorige versies. U kunt een versie nummer gebruiken in de naam zoals `PackageName_1.0.0` .
Het nummer in dit voor beeld wordt alleen gebruikt om het pakket uniek te maken, niet om op te geven dat het pakket moet worden beschouwd als een nieuwe of oudere versie dan andere pakketten.

Werk vervolgens de para meters die worden gebruikt met de `New-GuestConfigurationPolicy` cmdlet uit die hieronder worden beschreven.

- **Versie**: wanneer u de `New-GuestConfigurationPolicy` cmdlet uitvoert, moet u een versie nummer opgeven dat groter is dan het aantal dat momenteel is gepubliceerd.
- **contentUri**: wanneer u de `New-GuestConfigurationPolicy` cmdlet uitvoert, moet u een URI naar de locatie van het pakket opgeven. Met inbegrip van een pakket versie in de bestands naam zorgt u ervoor dat de waarde van deze eigenschap in elke versie verandert.
- **contentHash**: deze eigenschap wordt automatisch bijgewerkt door de `New-GuestConfigurationPolicy` cmdlet. Het is een hash-waarde van het pakket dat is gemaakt door `New-GuestConfigurationPackage` . De eigenschap moet correct zijn voor het `.zip` bestand dat u publiceert. Als alleen de eigenschap **contentUri** is bijgewerkt, wordt het inhouds pakket niet geaccepteerd door de extensie.

De eenvoudigste manier om een bijgewerkt pakket vrij te geven, is het proces dat wordt beschreven in dit artikel herhalen en een bijgewerkt versie nummer opgeven. Dit proces garandeert dat alle eigenschappen correct zijn bijgewerkt.

## <a name="optional-signing-guest-configuration-packages"></a>Optioneel: gast configuratie pakketten ondertekenen

Het aangepaste beleid voor gast configuratie gebruikt SHA256-hash om te valideren dat het beleids pakket niet is gewijzigd.
Klanten kunnen eventueel ook een certificaat gebruiken om pakketten te ondertekenen en de gast configuratie-extensie dwingen alleen ondertekende inhoud toe te staan.

Om dit scenario in te scha kelen, zijn er twee stappen die u moet volt ooien. Voer de cmdlet uit om het inhouds pakket te ondertekenen en voeg een tag toe aan de machines waarvoor code moet worden ondertekend.

Als u de functie voor handtekening validatie wilt gebruiken, voert `Protect-GuestConfigurationPackage` u de cmdlet uit om het pakket te ondertekenen voordat het wordt gepubliceerd. Voor deze cmdlet is het certificaat voor ondertekening van programma code vereist.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Para meters van de `Protect-GuestConfigurationPackage` cmdlet:

- **Pad**: volledig pad van het gast configuratie pakket.
- **Certificaat**: certificaat voor ondertekening van programma code voor het ondertekenen van het pakket. Deze para meter wordt alleen ondersteund bij het ondertekenen van inhoud voor Windows.

GuestConfiguration-agent verwacht dat de open bare sleutel van het certificaat aanwezig is in ' vertrouwde basis certificerings instanties ' op Windows-computers en in het pad `/usr/local/share/ca-certificates/extra` op Linux-machines. Installeer de open bare sleutel van het certificaat op de computer voordat u het aangepaste beleid toepast, zodat het knoop punt ondertekende inhoud verifieert. Dit proces kan worden uitgevoerd met behulp van een wille keurige techniek in de virtuele machine of met behulp van Azure Policy. [Hier](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)vindt u een voorbeeld sjabloon.
Het Key Vault toegangs beleid moet de compute-resource provider toegang geven tot certificaten tijdens implementaties. Zie [Key Vault instellen voor virtuele machines in azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)voor gedetailleerde stappen.

Hieronder volgt een voor beeld van het exporteren van de open bare sleutel van een handtekening certificaat om te importeren naar de machine.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Nadat de inhoud is gepubliceerd, voegt u een tag met de naam `GuestConfigPolicyCertificateValidation` en waarde `enabled` toe aan alle virtuele machines waarvoor ondertekening van programma code vereist is. Zie de voor [beelden](../samples/built-in-policies.md#tags) van tags voor de manier waarop Tags op schaal kunnen worden geleverd met behulp van Azure Policy. Zodra deze tag is geïmplementeerd, maakt de beleids definitie die is gegenereerd met de `New-GuestConfigurationPolicy` cmdlet, de vereiste via de gast configuratie-extensie.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het controleren van Vm's met [gast configuratie](../concepts/guest-configuration.md).
- Meer informatie over het [programmatisch maken van beleids regels](./programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](./get-compliance-data.md).
