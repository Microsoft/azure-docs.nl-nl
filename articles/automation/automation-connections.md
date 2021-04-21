---
title: Verbindingen beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u Azure Automation externe services of toepassingen beheert en hoe u hiermee kunt werken in runbooks.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 4b5983d6ea4ea9065d1292a2c5ee0c094cea093b
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834890"
---
# <a name="manage-connections-in-azure-automation"></a>Verbindingen beheren in Azure Automation

Een Azure Automation verbindingsactivum bevat de onderstaande informatie. Deze informatie is vereist voor verbinding met een externe service of toepassing vanuit een runbook- of DSC-configuratie. 

* Informatie die nodig is voor verificatie, zoals gebruikersnaam en wachtwoord
* Verbindingsgegevens, zoals URL of poort

De verbindingsactivum houdt alle eigenschappen bij elkaar om verbinding te maken met een bepaalde toepassing, waardoor het niet nodig is om meerdere variabelen te maken. U kunt de waarden voor een verbinding op één plek bewerken en u kunt de naam van een verbinding doorgeven aan een runbook of DSC-configuratie in één parameter. Het runbook of de configuratie heeft toegang tot de eigenschappen voor een verbinding met behulp van de `Get-AutomationConnection` interne cmdlet .

Wanneer u een verbinding maakt, moet u een verbindingstype opgeven. Het verbindingstype is een sjabloon die een set eigenschappen definieert. U kunt een verbindingstype toevoegen aan Azure Automation behulp van een integratiemodule met een metagegevensbestand. Het is ook mogelijk om een verbindingstype te maken met behulp van [de Azure Automation-API](/previous-versions/azure/reference/mt163818(v=azure.100)) als de integratiemodule een verbindingstype bevat en wordt geïmporteerd in uw Automation-account. 

>[!NOTE]
>Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen. Deze assets worden versleuteld en opgeslagen in Azure Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Azure Automation slaat de sleutel op in de door het systeem beheerde Key Vault. Voordat een beveiligde asset wordt opgeslagen, laadt Automation de sleutel van Key Vault gebruikt om de asset te versleutelen. 

## <a name="connection-types"></a>Verbindingstypen

Azure Automation maakt de volgende ingebouwde verbindingstypen beschikbaar:

* `Azure` - Vertegenwoordigt een verbinding die wordt gebruikt voor het beheren van klassieke resources.
* `AzureServicePrincipal` - Vertegenwoordigt een verbinding die wordt gebruikt door het Uitvoeren als-account van Azure.
* `AzureClassicCertificate` - Vertegenwoordigt een verbinding die wordt gebruikt door het klassieke Uitvoeren als-account van Azure.

In de meeste gevallen hoeft u geen verbindingsresource te maken, omdat deze wordt gemaakt wanneer u een [Uitvoeren als-account maakt.](automation-security-overview.md)

## <a name="powershell-cmdlets-to-access-connections"></a>PowerShell-cmdlets voor toegang tot verbindingen

De cmdlets in de volgende tabel maken en beheren Automation-verbindingen met PowerShell. Ze worden als onderdeel van de [Az-modules verzenden.](shared-resources/modules.md#az-modules)

|Cmdlet|Beschrijving|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Hiermee wordt informatie over een verbinding opgehaald.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Hiermee maakt u een nieuwe verbinding.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Hiermee verwijdert u een bestaande verbinding.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Hiermee stelt u de waarde van een bepaald veld voor een bestaande verbinding in.|

## <a name="internal-cmdlets-to-access-connections"></a>Interne cmdlets voor toegang tot verbindingen

De interne cmdlet in de volgende tabel wordt gebruikt voor toegang tot verbindingen in uw runbooks en DSC-configuraties. Deze cmdlet wordt geleverd met de globale module `Orchestrator.AssetManagement.Cmdlets` . Zie Interne [cmdlets voor meer informatie.](shared-resources/modules.md#internal-cmdlets)

|Interne cmdlet|Description|
|---|---|
|`Get-AutomationConnection` | Hiermee haalt u de waarden van de verschillende velden in de verbinding op en retourneert u deze als een [hashtabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables). U kunt deze hashtabel vervolgens gebruiken met de juiste opdrachten in het runbook of de DSC-configuratie.|

>[!NOTE]
>Vermijd het gebruik van variabelen met de `Name` parameter van `Get-AutomationConnection` . Het gebruik van variabelen in dit geval kan de detectie van afhankelijkheden tussen runbooks of DSC-configuraties en verbindingsactiva tijdens de ontwerptijd bemoeilijken.

## <a name="python-functions-to-access-connections"></a>Python-functies voor toegang tot verbindingen

De functie in de volgende tabel wordt gebruikt voor toegang tot verbindingen in een Python 2- en 3-runbook. Python 3-runbooks zijn momenteel beschikbaar als preview-versie.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_connection` | Hiermee haalt u een verbinding op. Retourneert een woordenlijst met de eigenschappen van de verbinding. |

> [!NOTE]
> U moet de `automationassets` module bovenaan uw Python-runbook importeren om toegang te krijgen tot de assetfuncties.

## <a name="create-a-new-connection"></a>Een nieuwe verbinding maken

### <a name="create-a-new-connection-with-the-azure-portal"></a>Maak een nieuwe verbinding met de Azure Portal

Een nieuwe verbinding maken in de Azure Portal:

1. Klik vanuit uw Automation-account **op Verbindingen onder** Gedeelde **resources.**
2. Klik **op + Een verbinding toevoegen** op de pagina Verbindingen.
4. Selecteer in **het veld Type** in het deelvenster Nieuwe verbinding het type verbinding dat u wilt maken. Uw keuzes zijn `Azure` , `AzureServicePrincipal` en `AzureClassicCertificate` . 
5. Het formulier bevat eigenschappen voor het verbindingstype dat u hebt gekozen. Vul het formulier in en klik **op Maken om** de nieuwe verbinding op te slaan.

### <a name="create-a-new-connection-with-windows-powershell"></a>Maak een nieuwe verbinding met Windows PowerShell

Maak een nieuwe verbinding met Windows PowerShell met behulp van `New-AzAutomationConnection` de cmdlet . Deze cmdlet heeft een parameter die een hashtabel verwacht die waarden definieert voor elk van de eigenschappen die `ConnectionFieldValues` zijn gedefinieerd door het verbindingstype.

U kunt de volgende voorbeeldopdrachten gebruiken als alternatief voor het maken van het Uitvoeren als-account vanuit de portal om een nieuwe verbindingsactivum te maken.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Wanneer u uw Automation-account maakt, bevat het standaard verschillende algemene modules, samen met het verbindingstype om `AzureServicePrincipal` de verbindingsactivum `AzureRunAsConnection` te maken. Als u probeert een nieuwe verbindingsactivum te maken om verbinding te maken met een service of toepassing met een andere verificatiemethode, mislukt de bewerking omdat het verbindingstype nog niet is gedefinieerd in uw Automation-account. Zie Een verbindingstype toevoegen voor meer informatie over het maken van uw eigen [verbindingstype voor een aangepaste module.](#add-a-connection-type)

## <a name="add-a-connection-type"></a>Een verbindingstype toevoegen

Als uw runbook of DSC-configuratie verbinding maakt met een externe service, moet u een verbindingstype definiëren in een aangepaste [module](shared-resources/modules.md#custom-modules) die een integratiemodule wordt genoemd. Deze module bevat een metagegevensbestand met de eigenschappen van het verbindingstype en heet **&lt; ModuleName &gt;-Automation.jsop**, dat zich bevindt in de modulemap van het **gecomprimeerde ZIP-bestand.** Dit bestand bevat de velden van een verbinding die vereist zijn om verbinding te maken met het systeem of de service die de module vertegenwoordigt. Met dit bestand kunt u de veldnamen, gegevenstypen, versleutelingsstatus en optionele status voor het verbindingstype instellen. 

Het volgende voorbeeld is een sjabloon in **de bestandsindeling .json** die de eigenschappen gebruikersnaam en wachtwoord definieert voor een aangepast verbindingstype met de naam `MyModuleConnection` :

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

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Verbinding maken in een runbook of DSC-configuratie

Haal een verbinding op in een runbook- of DSC-configuratie met de interne `Get-AutomationConnection` cmdlet . Deze cmdlet heeft de voorkeur boven de cmdlet, omdat hiermee de verbindingswaarden worden opgehaald in plaats van `Get-AzAutomationConnection` informatie over de verbinding.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In het volgende voorbeeld ziet u hoe u het Uitvoeren als-account gebruikt om te verifiëren met Azure Resource Manager resources in uw runbook. Er wordt een verbindingsactivum gebruikt dat het Uitvoeren als-account vertegenwoordigt, dat verwijst naar de service-principal op basis van certificaten.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

In het volgende voorbeeld ziet u hoe u verifieert met behulp van de Run As-verbinding in een Python 2- en 3-runbook.

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

### <a name="graphical-runbook-examples"></a>Grafische runbookvoorbeelden

U kunt een activiteit voor de interne `Get-AutomationConnection` cmdlet toevoegen aan een grafisch runbook. Klik met de rechtermuisknop op de verbinding in het deelvenster Bibliotheek van de grafische editor en selecteer **Toevoegen aan canvas.**

![toevoegen aan canvas](media/automation-connections/connection-add-canvas.png)

In de volgende afbeelding ziet u een voorbeeld van het gebruik van een verbindingsobject in een grafisch runbook. In dit voorbeeld wordt de `Constant value` gegevensset voor de `Get RunAs Connection` activiteit gebruikt, die gebruikmaakt van een verbindingsobject voor verificatie. Hier [wordt een pijplijnkoppeling](automation-graphical-authoring-intro.md#use-links-for-workflow) gebruikt omdat de `ServicePrincipalCertificate` parameterset één object verwacht.

![verbindingen op te halen](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Volgende stappen

* Zie Modules beheren in Azure Automation voor meer informatie over de cmdlets die worden gebruikt [voor toegang tot Azure Automation.](shared-resources/modules.md)
* Zie Runbookuitvoering in Azure Automation voor algemene [informatie over runbooks.](automation-runbook-execution.md)
* Zie overzicht van State Configuration voor [meer informatie State Configuration DSC-configuraties.](automation-dsc-overview.md)
