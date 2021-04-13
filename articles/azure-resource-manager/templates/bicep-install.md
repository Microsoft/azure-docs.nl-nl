---
title: Ontwikkel-en implementatie omgevingen van Bicep instellen
description: Ontwikkel-en implementatie omgevingen van Bicep configureren
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2c905a3885fcfc5c9eb9d9db4004126882798611
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313552"
---
# <a name="install-bicep-preview"></a>Bicep installeren (preview-versie)

Meer informatie over het instellen van Bicep-ontwikkel-en implementatie omgevingen.

## <a name="development-environment"></a>Ontwikkelomgeving

Voor de beste ervaring voor het ontwerpen van Bicep hebt u twee onderdelen nodig:

- **Bicep-extensie voor Visual Studio code**. Als u Bicep-bestanden wilt maken, hebt u een goede Bicep-editor nodig. We raden [Visual Studio code](https://code.visualstudio.com/) aan met de [Bicep-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Deze hulpprogram ma's bieden taal ondersteuning en automatisch aanvullen van resources. Ze helpen u bij het maken en valideren van Bicep-bestanden. Zie [Quick Start: Bicep-bestanden maken met Visual Studio code](./quickstart-create-bicep-use-visual-studio-code.md)voor meer informatie over het gebruik van Visual Studio code en de Bicep-extensie.
- **BICEP cli**. Gebruik Bicep CLI voor het compileren van Bicep-bestanden voor ARM JSON-sjablonen en het decompileren van ARM JSON-sjablonen op Bicep-bestanden. Zie [install BICEP cli](#install-manually)(Engelstalig) voor de installatie-instructies.

## <a name="deployment-environment"></a>Implementatieomgeving

Voor het implementeren van lokale Bicep-bestanden hebt u twee onderdelen nodig:

- **Azure CLI-versie 2.20.0 of hoger, of Azure PowerShell versie 5.6.0 of hoger**. Zie voor installatie-instructies:

  - [Azure PowerShell installeren](/powershell/azure/install-az-ps)
  - [Azure CLI installeren in Windows](/cli/azure/install-azure-cli-windows)
  - [Azure CLI installeren in Linux](/cli/azure/install-azure-cli-linux)
  - [Azure CLI installeren in macOS](/cli/azure/install-azure-cli-macos)

  > [!NOTE]
  > Momenteel kunnen alleen lokale Bicep-bestanden worden geïmplementeerd met Azure CLI en Azure PowerShell. Zie [Deploy-cli](./deploy-cli.md#deploy-remote-template)voor meer informatie over het implementeren van Bicep-bestanden met behulp van Azure cli. Zie [Deploy-Power shell]( ./deploy-powershell.md#deploy-remote-template)(Engelstalig) voor meer informatie over het implementeren van Bicep-bestanden met behulp van Azure PowerShell.

- **BICEP cli**. Bicep CLI is vereist voor het compileren van Bicep-bestanden naar JSON-sjablonen vóór de implementatie. Zie [install BICEP cli](#install-bicep-cli)(Engelstalig) voor de installatie-instructies.

Nadat de onderdelen zijn geïnstalleerd, kunt u een Bicep-bestand implementeren met:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>Bicep CLI installeren

- Zie [hand matig installeren](#install-manually)om Bicep CLI te gebruiken voor het compileren en decompileren van Bicep-bestanden.
- Zie [gebruiken met Azure cli](#use-with-azure-cli)om Azure CLI te gebruiken voor het implementeren van Bicep-bestanden.
- Zie [gebruiken met Azure PowerShell](#use-with-azure-powershell)om Azure PowerShell te gebruiken voor het implementeren van Bicep-bestanden.

### <a name="use-with-azure-cli"></a>Gebruiken met Azure CLI

Als Azure CLI-versie 2.20.0 of hoger is geïnstalleerd, wordt de Bicep-CLI automatisch geïnstalleerd wanneer een opdracht die ervan afhankelijk is, wordt uitgevoerd. Bijvoorbeeld:

```azurecli
az deployment group create --template-file azuredeploy.bicep --resource-group myResourceGroup
```

of

```azurecli
az bicep ...
```

U kunt de CLI ook hand matig installeren met behulp van de ingebouwde opdrachten:

```bash
az bicep install
```

Bijwerken naar de nieuwste versie:

```bash
az bicep upgrade
```

Een specifieke versie installeren:

```bash
az bicep install --version v0.3.255
```

> [!IMPORTANT]
> Met Azure CLI wordt een afzonderlijke versie van de Bicep-CLI geïnstalleerd die niet in conflict is met andere installaties van Bicep die u mogelijk hebt, en voegt Azure CLI geen Bicep CLI toe aan uw pad. Zie [hand matig installeren](#install-manually) of [gebruiken met Azure Power shell](#use-with-azure-powershell)als u Bicep cli wilt gebruiken voor het compileren/decompileren van Bicep-bestanden of als u Azure PowerShell wilt gebruiken voor het implementeren van Bicep-bestanden.

Alle beschik bare versies van Bicep CLI weer geven:

```bash
az bicep list-versions
```

De geïnstalleerde versies weer geven:

```bash
az bicep version
```

### <a name="use-with-azure-powershell"></a>Gebruiken met Azure PowerShell

Azure PowerShell beschikt niet over de mogelijkheid om de Bicep-CLI nog te installeren. Azure PowerShell (v 5.6.0 of hoger) verwacht dat de Bicep CLI al is geïnstalleerd en beschikbaar is op het pad. Voer een van de [hand matige installatie methoden](#install-manually)uit.

Voor het implementeren van Bicep-bestanden is Bicep CLI-versie 0.3.1 of later vereist. De Bicep CLI-versie controleren:

```cmd
bicep --version
```

> [!IMPORTANT]
> Met Azure CLI wordt een eigen, op zichzelf staande versie van Bicep CLI geïnstalleerd. Azure PowerShell implementatie mislukt, zelfs als u de vereiste versies voor Azure CLI hebt geïnstalleerd.

Zodra de Bicep CLI is geïnstalleerd, wordt Bicep CLI aangeroepen wanneer dit nodig is voor een implementatie-cmdlet. Bijvoorbeeld:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile azuredeploy.bicep
```

### <a name="install-manually"></a>De installatie handmatig uitvoeren

Met de volgende methoden installeert u de Bicep CLI en voegt u deze toe aan uw pad.

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>via Homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep

# Install the tool
brew install bicep
```

##### <a name="macos-manual-install"></a>hand matige installatie van macOS

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows Installer

Down load en start de [meest recente versie van Windows Installer](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe). Voor het installatie programma zijn geen beheerders bevoegdheden vereist. Na de installatie wordt Bicep CLI toegevoegd aan het pad van uw gebruiker. Sluit alle geopende opdracht prompt Vensters en open deze opnieuw om de gewijzigde paden van kracht te laten worden.

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>Hand matig met Power shell

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>De nacht builds installeren

Als u de nieuwste pre-release-bits van Bicep wilt proberen voordat ze worden uitgebracht, raadpleegt u [nacht builds installeren](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md).

> [!WARNING]
> Deze voorlopige versies zijn waarschijnlijk veel meer bekend of onbekende fouten.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de [Bicep Snelstartgids](./quickstart-create-bicep-use-visual-studio-code.md).
