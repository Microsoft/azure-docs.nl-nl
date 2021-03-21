---
title: Een e-mailbericht verzenden vanuit een Azure Automation-runbook
description: In dit artikel leest u hoe u een e-mailbericht verzendt vanuit een runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/05/2021
ms.topic: conceptual
ms.openlocfilehash: 915a0d75622a98b33f647041f3c3b622cb5236b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99053717"
---
# <a name="send-an-email-from-a-runbook"></a>Een e-mailbericht vanuit een runbook verzenden

U kunt een e-mailbericht verzenden vanuit een runbook met [SendGrid](https://sendgrid.com/solutions) met behulp van PowerShell. 

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Een SendGrid-account](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account).
* [Automation-account](./index.yml) met **Az**-modules.
* [Uitvoeren als-account](./automation-security-overview.md#run-as-accounts) om het runbook op te slaan en uit te voeren.

## <a name="create-an-azure-key-vault"></a>Een Azure Key Vault maken

U kunt een Azure Key Vault maken met behulp van het volgende PowerShell-script. Vervang de variabele waarden door de waarden die specifiek zijn voor uw omgeving. Gebruik de ingesloten Azure Cloud Shell via de knop **Nu proberen** in de rechterbovenhoek van het codeblok. U kunt de code ook lokaal kopiëren en uitvoeren als de [Az-modules](/powershell/azure/install-az-ps) op uw lokale computer zijn geïnstalleerd.

> [!NOTE]
> Als u uw API-sleutel wilt ophalen, gebruikt u de stappen in [Uw SendGrid-API-sleutel zoeken](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Zie [Key Vault-quickstarts](../key-vault/index.yml) voor andere manieren om een Azure Key Vault te maken en een geheim op te slaan.

## <a name="import-required-modules-into-your-automation-account"></a>Vereiste modules importeren in uw Automation-account

Als u Azure Key Vault binnen een runbook wilt gebruiken, moet u de volgende modules importeren in uw Automation-account:

* [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Zie [Az-modules importeren](shared-resources/modules.md#import-az-modules) voor instructies.

## <a name="create-the-runbook-to-send-an-email"></a>Het runbook maken om een e-mailbericht te verzenden

Nadat u een Key Vault hebt gemaakt en uw `SendGrid`-API-sleutel hebt opgeslagen, kunt u het runbook gaan maken waarmee de API-sleutel wordt opgehaald en een e-mailbericht wordt verzonden. We gebruiken een runbook dat gebruikmaakt van `AzureRunAsConnection` als een [Uitvoeren als-account](./automation-security-overview.md#run-as-accounts) voor verificatie met Azure om het geheim uit Azure Key Vault op te halen. Het runbook krijgt de naam **Send-GridMailMessage**. U kunt het PowerShell-script wijzigen dat als voorbeeld wordt gebruikt en dit voor verschillende scenario's opnieuw gebruiken.

1. Ga naar uw Azure Automation-account.
2. Selecteer onder **Procesautomatisering** de optie **Runbooks**.
3. Selecteer boven aan de lijst met runbooks de optie **+ Een runbook maken**.
4. Voer op de pagina Runbook toevoegen **Send-GridMailMessage** in voor de naam van het runbook. Selecteer **PowerShell** voor het type runbook. Ten slotte selecteert u **Create**.
   ![Runbook maken](./media/automation-send-email/automation-send-email-runbook.png)
5. Het runbook wordt gemaakt en de pagina PowerShell-runbook bewerken wordt geopend.
   ![Het runbook bewerken](./media/automation-send-email/automation-send-email-edit.png)
6. Kopieer het volgende PowerShell-voorbeeld naar de pagina Bewerken. Zorg ervoor dat de `VaultName` de naam aangeeft die u voor uw Key Vault hebt gekozen.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValue
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Selecteer **Publiceren** om het runbook op te slaan en te publiceren.

Als u wilt controleren of het runbook wordt uitgevoerd, kunt u de stappen volgen onder [Een runbook testen](manage-runbooks.md#test-a-runbook) of [Een runbook starten](start-runbooks.md).

Als u uw test-e-mailbericht in eerste instantie niet ziet, controleert u de mappen **Ongewenste e-mail** en **Spam**.

## <a name="clean-up-resources-after-the-email-operation"></a>Resources opschonen na de e-mailbewerking

1. Wanneer u het runbook niet meer nodig hebt, selecteert u het in de lijst met runbooks en klikt u op **Verwijderen**.

2. Verwijder de Key Vault met behulp van de cmdlet [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault).

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg [Azure Automation-taakgegevens doorsturen naar Azure Monitor-logboeken](automation-manage-send-joblogs-log-analytics.md) als u runbook-taakgegevens wilt verzenden naar uw Log Analytics-werkruimte.
* Raadpleeg [Een waarschuwing gebruiken om een Azure Automation-runbook te activeren](automation-create-alert-triggered-runbook.md) als u metrische gegevens en logboeken op basisniveau wilt bewaken.
* Raadpleeg [Problemen met runbooks oplossen](./troubleshoot/runbooks.md) om problemen op te lossen die tijdens runbook-bewerkingen ontstaan.
