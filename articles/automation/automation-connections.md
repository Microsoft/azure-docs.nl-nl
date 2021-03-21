---
title: Verbindingen in Azure Automation beheren
description: In dit artikel leest u hoe u Azure Automation verbindingen met externe services of toepassingen kunt beheren en hoe u deze kunt gebruiken in runbooks.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 5f6494eb72084c683ddbb8b27a49acdb1fbc2bfc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054260"
---
# <a name="manage-connections-in-azure-automation"></a>Verbindingen in Azure Automation beheren

Een Azure Automation-verbindings element bevat de informatie die hieronder wordt weer gegeven. Deze informatie is vereist voor de verbinding met een externe service of toepassing vanuit een runbook of DSC-configuratie. 

* Informatie die nodig is voor verificatie, zoals gebruikers naam en wacht woord
* Verbindings gegevens, zoals URL of poort

De verbindings Asset houdt alle eigenschappen bij om verbinding te maken met een bepaalde toepassing, waardoor er geen meerdere variabelen meer hoeven te worden gemaakt. U kunt de waarden voor een verbinding op één plek bewerken en u kunt de naam van een verbinding met een runbook of DSC-configuratie door geven in één para meter. Het runbook of de configuratie heeft toegang tot de eigenschappen voor een verbinding met behulp van de interne `Get-AutomationConnection` cmdlet.

Wanneer u een verbinding maakt, moet u een verbindings type opgeven. Het verbindings type is een sjabloon die een set eigenschappen definieert. U kunt een verbindings type toevoegen aan Azure Automation met behulp van een integratie module met een meta gegevensbestand. Het is ook mogelijk om een verbindings type te maken met behulp van de [Azure Automation-API](/previous-versions/azure/reference/mt163818(v=azure.100)) als de integratie module een verbindings type bevat en wordt geïmporteerd in uw Automation-account. 

>[!NOTE]
>Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Azure Automation slaat de sleutel op in de door het systeem beheerde Key Vault. Voordat u een beveiligde Asset opslaat, laadt Automation de sleutel van Key Vault en gebruikt deze om de Asset te versleutelen. 

## <a name="connection-types"></a>Verbindingstypen

Azure Automation maakt de volgende ingebouwde verbindings typen beschikbaar:

* `Azure` -Vertegenwoordigt een verbinding die wordt gebruikt om klassieke resources te beheren.
* `AzureServicePrincipal` -Vertegenwoordigt een verbinding die wordt gebruikt door het uitvoeren als-account van Azure.
* `AzureClassicCertificate` -Vertegenwoordigt een verbinding die wordt gebruikt door het klassieke uitvoeren als-account van Azure.

In de meeste gevallen hoeft u geen verbindings bron te maken omdat deze wordt gemaakt wanneer u een [uitvoeren als-account](automation-security-overview.md)maakt.

## <a name="powershell-cmdlets-to-access-connections"></a>Power shell-cmdlets voor toegang tot verbindingen

Met de cmdlets in de volgende tabel worden Automation-verbindingen met Power shell gemaakt en beheerd. Ze worden geleverd als onderdeel van de [AZ-modules](shared-resources/modules.md#az-modules).

|Cmdlet|Beschrijving|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Hiermee haalt u informatie op over een verbinding.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Hiermee maakt u een nieuwe verbinding.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Hiermee verwijdert u een bestaande verbinding.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Hiermee stelt u de waarde van een bepaald veld voor een bestaande verbinding in.|

## <a name="internal-cmdlets-to-access-connections"></a>Interne cmdlets voor toegang tot verbindingen

De interne cmdlet in de volgende tabel wordt gebruikt voor toegang tot verbindingen in uw runbooks en DSC-configuraties. Deze cmdlet wordt geleverd met de globale module `Orchestrator.AssetManagement.Cmdlets` . Zie [interne cmdlets](shared-resources/modules.md#internal-cmdlets)voor meer informatie.

|Interne cmdlet|Beschrijving|
|---|---|
|`Get-AutomationConnection` | Haalt de waarden van de verschillende velden in de verbinding op en retourneert deze als een [hashtabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables). U kunt deze hashtabel vervolgens gebruiken met de juiste opdrachten in het runbook of de DSC-configuratie.|

>[!NOTE]
>Vermijd het gebruik van variabelen met de `Name` para meter van `Get-AutomationConnection` . Het gebruik van variabelen in dit geval kan de detectie van afhankelijkheden tussen runbooks of DSC-configuraties en verbindings assets tijdens het ontwerpen bemoeilijken.

## <a name="python-functions-to-access-connections"></a>Python-functies voor toegang tot verbindingen

De functie in de volgende tabel wordt gebruikt voor toegang tot verbindingen in een python 2-en 3-runbook. Python 3-runbooks zijn momenteel beschikbaar als preview-versie.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_connection` | Hiermee haalt u een verbinding op. Retourneert een woorden lijst met de eigenschappen van de verbinding. |

> [!NOTE]
> U moet de `automationassets` module boven aan het python-runbook importeren om toegang te krijgen tot de Asset-functies.

## <a name="create-a-new-connection"></a>Een nieuwe verbinding maken

### <a name="create-a-new-connection-with-the-azure-portal"></a>Een nieuwe verbinding maken met de Azure Portal

Een nieuwe verbinding maken in de Azure Portal:

1. Klik vanuit uw Automation-account op **verbindingen** onder **gedeelde bronnen**.
2. Klik op **+ een verbinding toevoegen** op de pagina verbindingen.
4. Selecteer in het veld **type** in het deel venster nieuwe verbinding het type verbinding dat u wilt maken. Uw keuzes zijn `Azure` , `AzureServicePrincipal` , en `AzureClassicCertificate` . 
5. Het formulier bevat eigenschappen voor het verbindings type dat u hebt gekozen. Vul het formulier in en klik op **maken** om de nieuwe verbinding op te slaan.

### <a name="create-a-new-connection-with-windows-powershell"></a>Een nieuwe verbinding maken met Windows Power shell

Een nieuwe verbinding maken met Windows Power shell met de `New-AzAutomationConnection` cmdlet. Deze cmdlet heeft een `ConnectionFieldValues` para meter die verwacht dat een hashtabel waarden definieert voor elk van de eigenschappen die worden gedefinieerd door het verbindings type.

U kunt de volgende voorbeeld opdrachten gebruiken als alternatief voor het maken van het uitvoeren als-account vanuit de portal om een nieuw verbindings element te maken.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Wanneer u uw Automation-account maakt, bevat het standaard verschillende globale modules, samen met het verbindings type `AzureServicePrincipal` voor het maken van de `AzureRunAsConnection` verbindings Asset. Als u een nieuw verbindings element maakt om verbinding te maken met een service of toepassing met een andere verificatie methode, mislukt de bewerking omdat het verbindings type niet al is gedefinieerd in uw Automation-account. Zie [een verbindings type toevoegen](#add-a-connection-type)voor meer informatie over het maken van uw eigen verbindings type voor een aangepaste module.

## <a name="add-a-connection-type"></a>Een verbindings type toevoegen

Als uw runbook of DSC-configuratie verbinding maakt met een externe service, moet u een verbindings type definiëren in een [aangepaste module](shared-resources/modules.md#custom-modules) die een integratie module wordt genoemd. Deze module bevat een meta gegevensbestand dat eigenschappen van het verbindings type specificeert en de naam **&lt; module &gt;-Automation.jsheeft op**. Deze bevindt zich in de map module van het gecomprimeerde **zip** -bestand. Dit bestand bevat de velden van een verbinding die vereist zijn om verbinding te maken met het systeem of de service die door de module wordt vertegenwoordigd. Met dit bestand kunt u de veld namen, gegevens typen, versleutelings status en optionele status voor het verbindings type instellen. 

Het volgende voor beeld is een sjabloon in de **JSON** -bestands indeling die de gebruikers naam-en wachtwoord eigenschappen definieert voor een aangepast verbindings type met de naam `MyModuleConnection` :

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Verbinding in een runbook of DSC-configuratie verkrijgen

Een verbinding in een runbook of DSC-configuratie ophalen met de interne `Get-AutomationConnection` cmdlet. Deze cmdlet heeft de voor keur boven de `Get-AzAutomationConnection` cmdlet, omdat deze de verbindings waarden ophaalt in plaats van informatie over de verbinding.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In het volgende voor beeld ziet u hoe u het uitvoeren als-account gebruikt om te verifiëren met Azure Resource Manager-resources in uw runbook. Er wordt gebruikgemaakt van een verbindings element met het run as-account dat verwijst naar de Service-Principal op basis van certificaten.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

In het volgende voor beeld ziet u hoe u met de uitvoeren als-verbinding kunt verifiëren in een python 2-en 3-runbook.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resource manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

---

### <a name="graphical-runbook-examples"></a>Voor beelden van grafische runbook

U kunt een activiteit voor de interne `Get-AutomationConnection` cmdlet toevoegen aan een grafisch runbook. Klik met de rechter muisknop op de verbinding in het deel venster Bibliotheek van de grafische editor en selecteer **toevoegen aan canvas**.

![toevoegen aan canvas](media/automation-connections/connection-add-canvas.png)

In de volgende afbeelding ziet u een voor beeld van het gebruik van een verbindings object in een grafisch runbook. In dit voor beeld wordt de `Constant value` gegevensset voor de `Get RunAs Connection` activiteit gebruikt, waarbij een verbindings object voor verificatie wordt gebruikt. Er wordt hier een [pijplijn koppeling](automation-graphical-authoring-intro.md#use-links-for-workflow) gebruikt omdat de `ServicePrincipalCertificate` parameterset een enkel object verwacht.

![verbindingen ophalen](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [modules beheren in azure Automation](shared-resources/modules.md)voor meer informatie over de cmdlets die worden gebruikt voor toegang tot verbindingen.
* Zie voor algemene informatie over runbooks [Runbook-uitvoering in azure Automation](automation-runbook-execution.md).
* Zie [status configuratie-overzicht](automation-dsc-overview.md)voor meer informatie over DSC-configuraties.