---
title: Referenties beheren in Azure Automation
description: In dit artikel leest u hoe u referentie-assets maakt en hoe u deze kunt gebruiken in een runbook of DSC-configuratie.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 9b9e42d55a982aeb55d7c9e26f7b1a6cbca32e0a
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052801"
---
# <a name="manage-credentials-in-azure-automation"></a>Referenties beheren in Azure Automation

Een Automation-referentie-Asset bevat een object dat beveiligings referenties bevat, zoals een gebruikers naam en een wacht woord. Runbooks en DSC-configuraties gebruiken cmdlets waarmee een [PSCredential](/dotnet/api/system.management.automation.pscredential) -object voor verificatie wordt geaccepteerd. Het is ook mogelijk om de gebruikers naam en het wacht woord van het `PSCredential` object op te halen om aan te geven aan bepaalde toepassingen of services waarvoor verificatie is vereist.

>[!NOTE]
>Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Azure Automation slaat de sleutel op in de door het systeem beheerde Key Vault. Voordat u een beveiligde Asset opslaat, laadt Automation de sleutel van Key Vault en gebruikt deze om de Asset te versleutelen. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>Power shell-cmdlets die worden gebruikt voor toegang tot referenties

Met de cmdlets in de volgende tabel worden Automation-referenties met Power shell gemaakt en beheerd. Ze worden geleverd als onderdeel van de [AZ-modules](modules.md#az-modules).

| Cmdlet | Beschrijving |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |Hiermee wordt een [CredentialInfo](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) -object opgehaald dat meta gegevens bevat over de referentie. De cmdlet haalt het `PSCredential` object zelf niet op.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |Hiermee maakt u een nieuwe Automation-referentie. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Hiermee verwijdert u een Automation-referentie. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |Hiermee stelt u de eigenschappen voor een bestaande Automation-referentie. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Andere cmdlets die worden gebruikt voor toegang tot referenties

De cmdlets in de volgende tabel worden gebruikt voor toegang tot referenties in uw runbooks en DSC-configuraties. 

| Cmdlet | Beschrijving |
|:--- |:--- |
| `Get-AutomationPSCredential` |Hiermee wordt een `PSCredential` object opgehaald dat in een runbook-of DSC-configuratie kan worden gebruikt. Meestal moet u deze [interne cmdlet](modules.md#internal-cmdlets) gebruiken in plaats van de `Get-AzAutomationCredential` cmdlet, aangezien de laatste alleen referentie gegevens ophaalt. Deze informatie is doorgaans niet nuttig voor het door geven van een andere cmdlet. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |Hiermee wordt een referentie opgehaald met de vraag naar een gebruikers naam en wacht woord. Deze cmdlet maakt deel uit van de standaard module micro soft. Power shell. Security. Zie [standaard modules](modules.md#default-modules).|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | Hiermee maakt u een referentie-element. Deze cmdlet maakt deel uit van de standaard Azure-module. Zie [standaard modules](modules.md#default-modules).|

Als `PSCredential` u objecten in uw code wilt ophalen, moet u de `Orchestrator.AssetManagement.Cmdlets` module importeren. Zie [modules beheren in azure Automation](modules.md)voor meer informatie.

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Vermijd het gebruik van variabelen in de `Name` para meter van `Get-AutomationPSCredential` . Hun gebruik kan de detectie van afhankelijkheden tussen runbooks of DSC-configuraties en referentie-assets tijdens het ontwerp bemoeilijken.

## <a name="python-functions-that-access-credentials"></a>Python-functies die toegang hebben tot referenties

De functie in de volgende tabel wordt gebruikt voor toegang tot referenties in een python 2-en 3-runbook. Python 3-runbooks zijn momenteel beschikbaar als preview-versie.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_credential` | Hiermee wordt informatie over een referentie-element opgehaald. |

> [!NOTE]
> Importeer de `automationassets` module boven aan het python-runbook om toegang te krijgen tot de Asset-functies.

## <a name="create-a-new-credential-asset"></a>Een nieuwe referentie-Asset maken

U kunt een nieuw referentie-element maken met behulp van de Azure Portal of met behulp van Windows Power shell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Maak een nieuw referentie-element met de Azure Portal

1. Klik vanuit uw Automation-account in het linkerdeel venster op **referenties** onder **gedeelde resources**.
2. Selecteer **een referentie toevoegen** op de pagina **referenties** .
3. Voer in het deel venster nieuwe referentie de juiste referentie naam in volgens uw naamgevings standaarden.
4. Typ uw toegangs-ID in het veld **gebruikers naam** .
5. Voor beide wachtwoord velden voert u uw geheime toegangs sleutel in.

    ![Nieuwe referentie maken](../media/credentials/credential-create.png)

6. Als het selectie vakje multi-factor Authentication is ingeschakeld, schakelt u dit uit.
7. Klik op **maken** om het nieuwe referentie-element op te slaan.

> [!NOTE]
> Azure Automation biedt geen ondersteuning voor gebruikers accounts die gebruikmaken van multi-factor Authentication.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Een nieuw referentie-element maken met Windows Power shell

In het volgende voor beeld ziet u hoe u een nieuw Automation-referentie-element maakt. Er `PSCredential` wordt eerst een object gemaakt met de naam en het wacht woord en vervolgens gebruikt voor het maken van de referentie-Asset. In plaats daarvan kunt u de- `Get-Credential` cmdlet gebruiken om de gebruiker te vragen een naam en wacht woord in te voeren.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Een referentie-element ophalen

Een runbook-of DSC-configuratie haalt een referentie-element op met de interne `Get-AutomationPSCredential` cmdlet. Met deze cmdlet wordt een `PSCredential` object opgehaald dat u kunt gebruiken met een cmdlet die een referentie vereist. U kunt ook de eigenschappen van het referentie object ophalen om het afzonderlijk te gebruiken. Het object heeft eigenschappen voor de gebruikers naam en het beveiligde wacht woord. 

> [!NOTE]
> De `Get-AzAutomationCredential` cmdlet haalt geen `PSCredential` object op dat voor verificatie kan worden gebruikt. Het bevat alleen informatie over de referentie. Als u een referentie in een runbook moet gebruiken, moet u deze als een object ophalen `PSCredential` met `Get-AutomationPSCredential` .

U kunt ook de methode [GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) gebruiken om een [NetworkCredential](/dotnet/api/system.net.networkcredential) -object op te halen dat een niet-beveiligde versie vertegenwoordigt van het wacht woord.

### <a name="textual-runbook-example"></a>Voor beeld van een tekst-runbook

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In het volgende voor beeld ziet u hoe u een Power shell-referentie gebruikt in een runbook. De referentie wordt opgehaald en de gebruikers naam en het wacht woord worden toegewezen aan variabelen.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

U kunt ook een referentie gebruiken om te verifiëren bij Azure met [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). In de meeste gevallen moet u een [uitvoeren als-account](../automation-security-overview.md#run-as-accounts) gebruiken en de verbinding ophalen met [Get-AzAutomationConnection](../automation-connections.md).

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

In het volgende voor beeld ziet u een voor beeld van toegang tot referenties in Python 2-runbooks.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

In het volgende voor beeld ziet u een voor beeld van toegang tot referenties in Python 3-runbooks (preview-versie).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>Voor beeld van grafisch runbook

U kunt een activiteit voor de interne `Get-AutomationPSCredential` cmdlet toevoegen aan een grafisch runbook door met de rechter muisknop op de referentie in het deel venster Bibliotheek van de grafische editor te klikken en **toevoegen aan canvas** te selecteren.

![Referentie-cmdlet toevoegen aan canvas](../media/credentials/credential-add-canvas.png)

In de volgende afbeelding ziet u een voor beeld van het gebruik van een referentie in een grafisch runbook. In dit geval biedt de referentie verificatie voor een runbook aan Azure-resources, zoals beschreven in [Azure AD gebruiken in azure Automation om te verifiëren bij Azure](../automation-use-azure-ad.md). Met de eerste activiteit wordt de referentie opgehaald die toegang heeft tot het Azure-abonnement. De activiteit account verbinding gebruikt vervolgens deze referentie om verificatie uit te voeren voor alle activiteiten die zich daarna voordoen. Er wordt hier een [pijplijn koppeling](../automation-graphical-authoring-intro.md#use-links-for-workflow) gebruikt omdat deze `Get-AutomationPSCredential` een enkel object verwacht.  

![Voor beeld van de werk stroom referentie met een pijplijn koppeling](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Referenties gebruiken in een DSC-configuratie

DSC-configuraties in Azure Automation kunnen werken met referentie-assets met `Get-AutomationPSCredential` , maar ze kunnen ook referentie-assets door geven via para meters. Zie [configuraties compileren in azure Automation DSC](../automation-dsc-compile.md#credential-assets)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [modules beheren in azure Automation](modules.md)voor meer informatie over de cmdlets die worden gebruikt voor toegang tot certificaten.
* Zie voor algemene informatie over runbooks [Runbook-uitvoering in azure Automation](../automation-runbook-execution.md).
* Zie [Azure Automation status configuratie Overview](../automation-dsc-overview.md)(Engelstalig) voor meer informatie over DSC-configuraties.