---
title: Een Azure Automation uitvoeren als-account maken
description: In dit artikel leest u hoe u een uitvoeren als-account maakt met Power shell of via de Azure Portal.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: ef6afff30da48b79b42e5fb4b3c72c3500f22dd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172300"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Een Azure Automation uitvoeren als-account maken

Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheren van resources op het Azure Resource Manager of het klassieke Azure-implementatie model met behulp van Automation-runbooks en andere automatiserings functies. In dit artikel wordt beschreven hoe u een uitvoeren als-of klassiek uitvoeren als-account maakt vanuit de Azure Portal of Azure PowerShell.

## <a name="create-account-in-azure-portal"></a>Account maken in Azure Portal

Voer de volgende stappen uit om uw Azure Automation-account bij te werken in de Azure Portal. De uitvoeren als-en klassieke uitvoeren als-accounts worden afzonderlijk gemaakt. Als u geen klassieke resources hoeft te beheren, kunt u alleen de Uitvoeren als-account van Azure maken.

1. Meld u aan bij Azure Portal met een account dat lid is van de rol Abonnementsbeheerders en dat medebeheerder is van het abonnement.

2. Zoek en selecteer **Automation-accounts**.

3. Selecteer op de pagina **Automation-accounts** uw Automation-account in de lijst.

4. Selecteer in het linkerdeel venster **uitvoeren als-accounts** in de sectie **account instellingen** .

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="Selecteer de optie uitvoeren als-account.":::

5. Afhankelijk van het account dat u nodig hebt, gebruikt u het deel venster **uitvoeren als-account + Azure** of het **klassieke uitvoeren als-account van Azure** . Nadat u de overzichts informatie hebt bekeken, klikt u op **maken**.

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="Selecteer de optie voor het maken van een run as-account":::

6. Terwijl in Azure het Uitvoeren als-account wordt gemaakt, kunt u in het menu onder **Meldingen** de voortgang hiervan volgen. Er wordt ook een banner weer gegeven met de mede deling dat het account wordt gemaakt. Het proces kan een paar minuten duren.

## <a name="create-account-using-powershell"></a>Account maken met Power shell

De volgende lijst bevat de vereisten voor het maken van een uitvoeren als-account in Power shell met behulp van een script. Deze vereisten zijn van toepassing op beide typen run as-accounts.

* Windows 10 of Windows Server 2016 met Azure Resource Manager modules 3.4.1 en hoger. Het Power shell-script biedt geen ondersteuning voor eerdere versies van Windows.
* Azure PowerShell Power shell 6.2.4 of hoger. Zie [Azure PowerShell installeren en configureren](/powershell/azure/install-az-ps)voor meer informatie.
* Een Automation-account waarnaar wordt verwezen als de waarde voor de `AutomationAccountName` `ApplicationDisplayName` para meters en.
* Machtigingen die gelijk zijn aan de items in de [vereiste machtigingen voor het configureren van run as-accounts](automation-security-overview.md#permissions).

`AutomationAccountName` `SubscriptionId` Voer de volgende stappen uit om de waarden voor,, en `ResourceGroupName` , de vereiste para meters voor het Power shell-script, op te halen.

1. Meld u aan bij Azure Portal.

1. Zoek en selecteer **Automation-accounts**.

1. Selecteer op de pagina Automation-accounts uw Automation-account in de lijst.

1. Selecteer **Eigenschappen** in het linkerdeelvenster.

1. Noteer de waarden voor **naam**, **abonnements-id** en **resource groep** op de pagina **Eigenschappen** .

   ![Eigenschappen pagina voor Automation-account](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Power shell-script voor het maken van een uitvoeren als-account

Het Power shell-script biedt ondersteuning voor verschillende configuraties.

* Een Uitvoeren als-account maken met een zelfondertekend certificaat.
* Een uitvoeren als-account en/of een klassiek uitvoeren als-account maken met behulp van een zelfondertekend certificaat.
* Maak een uitvoeren als-account en/of een klassiek uitvoeren als-account met behulp van een certificaat dat is uitgegeven door uw bedrijfs certificerings instantie (CA).
* Maak een uitvoeren als-account en/of een klassiek uitvoeren als-account met behulp van een zelfondertekend certificaat in de Azure Government Cloud.

1. Down load het script en sla het op in een lokale map met behulp van de volgende opdracht.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Start Power shell met verhoogde gebruikers rechten en navigeer naar de map die het script bevat.

3. Voer een van de volgende opdrachten uit om een uitvoeren als-en/of klassiek uitvoeren als-account te maken op basis van uw vereisten.

    * Een uitvoeren als-account maken met een zelfondertekend certificaat.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een bedrijfscertificaat.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Als u een klassiek uitvoeren als-account hebt gemaakt met een openbaar certificaat (. cer-bestand) van een onderneming, gebruikt u dit certificaat. Met het script wordt het bestand gemaakt en opgeslagen in de map met tijdelijke bestanden op uw computer, onder het gebruikers profiel dat `%USERPROFILE%\AppData\Local\Temp` u hebt gebruikt om de Power shell-sessie uit te voeren. Zie [een beheer-API-certificaat uploaden naar de Azure Portal](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Een Uitvoeren als-account en een klassiek Uitvoeren als-account maken met een zelfondertekend certificaat in de cloud van Azure Government

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Nadat het script is uitgevoerd, wordt u gevraagd om u te verifiëren bij Azure. Meld u aan met een account dat lid is van de rol abonnements beheerders. Als u een klassiek uitvoeren als-account maakt, moet uw account een mede beheerder van het abonnement zijn.

## <a name="next-steps"></a>Volgende stappen

* Zie [grafische Runbooks ontwerpen in azure Automation](automation-graphical-authoring-intro.md)voor meer informatie over het ontwerpen van grafieken.
* Zie [zelf studie: een Power shell-Runbook maken](learn/automation-tutorial-runbook-textual-powershell.md)om aan de slag te gaan met Power shell-runbooks.
* Zie [zelf studie: een python 3-Runbook maken](learn/automation-tutorial-runbook-textual-python-3.md)om aan de slag te gaan met een python 3-runbook.
