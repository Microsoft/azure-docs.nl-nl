---
title: Referenties beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u referentie-assets maakt en gebruikt in een runbook- of DSC-configuratie.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6220a44e952aa4d9856ac5fc2077d254103d4a2c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834278"
---
# <a name="manage-credentials-in-azure-automation"></a>Referenties beheren in Azure Automation

Een Automation-referentie-asset bevat een object dat beveiligingsreferenties bevat, zoals een gebruikersnaam en een wachtwoord. Runbooks en DSC-configuraties gebruiken cmdlets die een [PSCredential-object](/dotnet/api/system.management.automation.pscredential) accepteren voor verificatie. Ze kunnen ook de gebruikersnaam en het wachtwoord van het object extraheren om aan een toepassing of service te leveren waarvoor `PSCredential` verificatie is vereist.

>[!NOTE]
>Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Azure Automation slaat de sleutel op in de door het systeem beheerde Key Vault. Voordat een beveiligde asset wordt opgeslagen, laadt Automation de sleutel Key Vault gebruikt om de asset te versleutelen. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>PowerShell-cmdlets die worden gebruikt voor toegang tot referenties

De cmdlets in de volgende tabel maken en beheren Automation-referenties met PowerShell. Ze worden als onderdeel van de [Az-modules verzenden.](modules.md#az-modules)

| Cmdlet | Beschrijving |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |Haalt een [CredentialInfo-object](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) met metagegevens over de referentie. De cmdlet haalt het object zelf `PSCredential` niet op.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |Hiermee maakt u een nieuwe Automation-referentie. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Hiermee verwijdert u een Automation-referentie. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |Hiermee stelt u de eigenschappen voor een bestaande Automation-referentie in. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Andere cmdlets die worden gebruikt voor toegang tot referenties

De cmdlets in de volgende tabel worden gebruikt voor toegang tot referenties in uw runbooks en DSC-configuraties. 

| Cmdlet | Beschrijving |
|:--- |:--- |
| `Get-AutomationPSCredential` |Haalt een `PSCredential` -object op dat moet worden gebruikt in een runbook- of DSC-configuratie. Meestal moet u deze interne cmdlet gebruiken in plaats van de [cmdlet,](modules.md#internal-cmdlets) omdat met de laatste alleen `Get-AzAutomationCredential` referentiegegevens worden opgehaald. Deze informatie is normaal gesproken niet nuttig om door te geven aan een andere cmdlet. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |Haalt een referentie met een prompt voor gebruikersnaam en wachtwoord. Deze cmdlet maakt deel uit van de standaardmodule Microsoft.PowerShell.Security. Zie [Standaardmodules.](modules.md#default-modules)|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | Hiermee maakt u een referentie-asset. Deze cmdlet maakt deel uit van de standaardModule van Azure. Zie [Standaardmodules.](modules.md#default-modules)|

Als u `PSCredential` objecten in uw code wilt ophalen, moet u de module `Orchestrator.AssetManagement.Cmdlets` importeren. Zie Modules beheren in Azure Automation voor [meer Azure Automation.](modules.md)

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Vermijd het gebruik van variabelen in de `Name` parameter van `Get-AutomationPSCredential` . Het gebruik ervan kan de detectie van afhankelijkheden tussen runbooks of DSC-configuraties en referentie-assets tijdens de ontwerptijd bemoeilijken.

## <a name="python-functions-that-access-credentials"></a>Python-functies die toegang hebben tot referenties

De functie in de volgende tabel wordt gebruikt voor toegang tot referenties in een Python 2- en 3-runbook. Python 3-runbooks zijn momenteel beschikbaar als preview-versie.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_credential` | Hiermee wordt informatie opgehaald over een referentie-asset. |

> [!NOTE]
> Importeer `automationassets` de module bovenaan uw Python-runbook om toegang te krijgen tot de assetfuncties.

## <a name="create-a-new-credential-asset"></a>Een nieuwe referentie-asset maken

U kunt een nieuwe referentie-asset maken met behulp van de Azure Portal of met behulp van Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Maak een nieuwe referentie-asset met de Azure Portal

1. Selecteer in uw Automation-account in het linkerdeelvenster **Referenties onder** **Gedeelde resources.**
2. Selecteer op **de pagina Referenties** de optie Een referentie **toevoegen.**
3. Voer in het deelvenster Nieuwe referentie een geschikte referentienaam in volgens uw naamgevingsstandaarden.
4. Typ uw toegangs-id in **het veld** Gebruikersnaam.
5. Voer voor beide wachtwoordvelden uw geheime toegangssleutel in.

    ![Nieuwe referentie maken](../media/credentials/credential-create.png)

6. Als het selectievakje Meervoudige verificatie is ingeschakeld, moet u dit selectievakje uitschakelen.
7. Klik **op Maken** om de nieuwe referentie-asset op te slaan.

> [!NOTE]
> Azure Automation biedt geen ondersteuning voor gebruikersaccounts die meervoudige verificatie gebruiken.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Een nieuwe referentie-asset maken met Windows PowerShell

In het volgende voorbeeld ziet u hoe u een nieuwe Automation-referentie-asset maakt. Er `PSCredential` wordt eerst een -object gemaakt met de naam en het wachtwoord en vervolgens gebruikt om de referentie-asset te maken. In plaats daarvan kunt u de cmdlet gebruiken om de gebruiker te vragen `Get-Credential` een naam en wachtwoord in te voeren.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Een referentie-asset krijgen

Een runbook- of DSC-configuratie haalt een referentie-asset op met de `Get-AutomationPSCredential` interne cmdlet . Deze cmdlet haalt een `PSCredential` -object op dat u kunt gebruiken met een cmdlet die een referentie vereist. U kunt ook de eigenschappen van het referentieobject ophalen om afzonderlijk te gebruiken. Het object heeft eigenschappen voor de gebruikersnaam en het beveiligde wachtwoord. 

> [!NOTE]
> De `Get-AzAutomationCredential` cmdlet haalt geen `PSCredential` object op dat kan worden gebruikt voor verificatie. Het bevat alleen informatie over de referentie. Als u een referentie in een runbook wilt gebruiken, moet u deze ophalen als `PSCredential` een -object met behulp van `Get-AutomationPSCredential` .

U kunt ook de methode [GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) gebruiken om een [NetworkCredential-object](/dotnet/api/system.net.networkcredential) op te halen dat een niet-beveiligde versie van het wachtwoord vertegenwoordigt.

### <a name="textual-runbook-example"></a>Voorbeeld van een tekstueel runbook

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In het volgende voorbeeld ziet u hoe u een PowerShell-referentie gebruikt in een runbook. De referentie wordt opgehaald en de gebruikersnaam en het wachtwoord worden toegewezen aan variabelen.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

U kunt ook een referentie gebruiken om te verifiëren bij Azure met [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) In de meeste gevallen moet u een [Uitvoeren als-account gebruiken en](../automation-security-overview.md#run-as-accounts) de verbinding ophalen met [Get-AzAutomationConnection](../automation-connections.md).

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

In het volgende voorbeeld ziet u een voorbeeld van het openen van referenties in Python 2-runbooks.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

In het volgende voorbeeld ziet u een voorbeeld van het openen van referenties in Python 3-runbooks (preview).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>Voorbeeld van grafisch runbook

U kunt een activiteit voor de interne cmdlet toevoegen aan een grafisch runbook door met de rechtermuisknop op de referentie te klikken in het deelvenster Bibliotheek van de grafische editor en Toevoegen aan `Get-AutomationPSCredential` canvas te **selecteren.**

![Referentie-cmdlet toevoegen aan canvas](../media/credentials/credential-add-canvas.png)

In de volgende afbeelding ziet u een voorbeeld van het gebruik van een referentie in een grafisch runbook. In dit geval biedt de referentie verificatie voor een runbook naar Azure-resources, zoals beschreven [in Azure AD gebruiken in](../automation-use-azure-ad.md)Azure Automation verifiëren bij Azure . Met de eerste activiteit worden de referenties opgehaald die toegang hebben tot het Azure-abonnement. De accountverbindingsactiviteit gebruikt deze referentie vervolgens om verificatie te bieden voor alle activiteiten die erop volgen. Hier [wordt een pijplijnkoppeling](../automation-graphical-authoring-intro.md#use-links-for-workflow) gebruikt omdat één `Get-AutomationPSCredential` object verwacht.  

![Voorbeeld van referentiewerkstroom met pijplijnkoppeling](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Referenties gebruiken in een DSC-configuratie

Hoewel DSC-configuraties in Azure Automation kunnen werken met referentie-assets met behulp van , kunnen ze `Get-AutomationPSCredential` ook referentie-assets doorgeven via parameters. Zie Configuraties [compileren in Azure Automation DSC voor meer informatie.](../automation-dsc-compile.md#credential-assets)

## <a name="next-steps"></a>Volgende stappen

* Zie Modules beheren in Azure Automation voor meer informatie over de cmdlets die worden gebruikt [voor toegang tot Azure Automation.](modules.md)
* Zie Runbookuitvoering in Azure Automation voor [algemene informatie over runbooks.](../automation-runbook-execution.md)
* Zie overzicht van Azure Automation State Configuration [DSC-configuraties.](../automation-dsc-overview.md)
