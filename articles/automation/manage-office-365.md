---
title: Office 365-services beheren met Azure Automation
description: In dit artikel leest u hoe u Azure Automation kunt gebruiken om Office 365-abonnements services te beheren.
services: automation
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: 70c8892969a3b13175c60a4e20e0cf9086112abe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93398042"
---
# <a name="manage-office-365-services"></a>Office 365-services beheren

U kunt Azure Automation gebruiken voor het beheer van Office 365-abonnements Services, voor producten zoals micro soft Word en micro soft Outlook. Interacties met Office 365 zijn ingeschakeld door [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Zie [Azure AD gebruiken in azure Automation om te verifiëren bij Azure](automation-use-azure-ad.md).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig voor het beheren van Office 365-abonnements Services in Azure Automation.

* Een Azure-abonnement. Zie de [hand leiding voor abonnements beslissingen](/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Een Automation-object in azure voor de referenties van het gebruikers account en runbooks. Bekijk [een inleiding tot Azure Automation](./automation-intro.md).
* Azure AD. Zie [Azure AD gebruiken in azure Automation om te verifiëren bij Azure](automation-use-azure-ad.md).
* Een Office 365-Tenant met een account. Zie [uw Office 365-Tenant instellen](/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="install-the-msonline-and-msonlineext-modules"></a>De MSOnline-en MSOnlineExt-modules installeren

Het gebruik van Office 365 binnen Azure Automation vereist Microsoft Azure Active Directory voor Windows Power shell ( `MSOnline` module). U hebt ook de module nodig [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35) , waarmee u Azure AD-beheer in omgevingen met één of meerdere tenants vereenvoudigt. Installeer de modules zoals beschreven in [Azure AD gebruiken in azure Automation om te verifiëren bij Azure](automation-use-azure-ad.md).

>[!NOTE]
>Als u MSOnline Power shell wilt gebruiken, moet u lid zijn van Azure AD. Gast gebruikers kunnen de module niet gebruiken.

## <a name="create-an-azure-automation-account"></a>Een Azure Automation-account maken

U hebt een account in Azure Automation nodig om de stappen in dit artikel te volt ooien. Zie [een Azure Automation-account maken](automation-quickstart-create-account.md).
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>MSOnline en MSOnlineExt als assets toevoegen

Voeg nu de geïnstalleerde MSOnline-en MSOnlineExt-modules toe om de Office 365-functionaliteit in te scha kelen. Raadpleeg [modules beheren in azure Automation](shared-resources/modules.md).

1. Selecteer in de Azure Portal **Automation-accounts**.
2. Kies uw Automation-account.
3. Selecteer **modules galerie** onder **gedeelde resources**.
4. Zoek naar MSOnline.
5. Selecteer de `MSOnline` Power shell-module en klik op **importeren** om de module als een Asset te importeren.
6. Herhaal stap 4 en 5 om de module te zoeken en te importeren `MSOnlineExt` .

## <a name="create-a-credential-asset-optional"></a>Een referentie-activum maken (optioneel)

Het is optioneel om een referentie-element te maken voor de Office 365-gebruiker met beheerders rechten die gemachtigd is om uw script uit te voeren. U kunt er echter voor zorgen dat gebruikers namen en wacht woorden in Power shell-scripts worden weer gegeven. Zie [een referentie-Asset maken](automation-use-azure-ad.md#create-a-credential-asset)voor instructies.

## <a name="create-an-office-365-service-account"></a>Een Office 365-service account maken

Als u Office 365-abonnements Services wilt uitvoeren, hebt u een Office 365 service-account nodig met de juiste machtigingen. U kunt één algemeen beheerders account gebruiken, één account per service of een functie of script hebben om uit te voeren. In elk geval is een complex en veilig wacht woord vereist voor het service account. Zie [Office 365 voor bedrijven instellen](/microsoft-365/admin/setup/setup).

## <a name="connect-to-the-azure-ad-online-service"></a>Verbinding maken met de Azure AD online-service

>[!NOTE]
>Als u de MSOnline-module-cmdlets wilt gebruiken, moet u deze uitvoeren vanuit Windows Power shell. Power shell core biedt geen ondersteuning voor deze cmdlets.

U kunt de MSOnline-module gebruiken om vanuit het Office 365-abonnement verbinding te maken met Azure AD. De verbinding maakt gebruik van een Office 365-gebruikers naam en-wacht woord of gebruikt multi-factor Authentication (MFA). U kunt verbinding maken met behulp van de Azure Portal of een Windows Power shell-opdracht prompt (heeft geen verhoogde bevoegdheden nodig).

Hieronder ziet u een voor beeld van Power shell. Met de cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) wordt om referenties gevraagd en opgeslagen in de `Msolcred` variabele. Vervolgens maakt de [Connect-MsolService](/powershell/module/msonline/connect-msolservice) -cmdlet gebruik van de referenties om verbinding te maken met de online service Azure Directory. Als u verbinding wilt maken met een specifieke Azure-omgeving, gebruikt u de `AzureEnvironment` para meter.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Als er geen fouten worden weer gegeven, hebt u verbinding gemaakt. Een snelle test is een Office 365-cmdlet uit te voeren, bijvoorbeeld, `Get-MsolUser` en de resultaten te bekijken. Als u fouten ontvangt, moet u er rekening mee houden dat een veelvoorkomend probleem een onjuist wacht woord is.

>[!NOTE]
>U kunt ook de module AzureRM of de module AZ gebruiken om vanuit het Office 365-abonnement verbinding te maken met Azure AD. De hoofd verbindings-cmdlet is [Connect-AzureAD](/powershell/module/azuread/connect-azuread). Met deze cmdlet wordt de `AzureEnvironmentName` para meter voor specifieke Office 365-omgevingen ondersteund.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>Een Power shell-runbook maken op basis van een bestaand script

U opent de Office 365-functionaliteit vanuit een Power shell-script. Hier volgt een voor beeld van een script voor een referentie `Office-Credentials` met de naam met de gebruikers naam van `admin@TenantOne.com` . De oplossing wordt gebruikt `Get-AutomationPSCredential` voor het importeren van de Office 365-referentie.

```powershell
$emailFromAddress = "admin@TenantOne.com"
$emailToAddress = "servicedesk@TenantOne.com"
$emailSMTPServer = "outlook.office365.com"
$emailSubject = "Office 365 License Report"

$credObject = Get-AutomationPSCredential -Name "Office-Credentials"
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body $O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="run-the-script-in-a-runbook"></a>Het script uitvoeren in een runbook

U kunt uw script gebruiken in een Azure Automation runbook. We gebruiken bijvoorbeeld het type Power shell-runbook.

1. Maak een nieuw Power shell-runbook. Raadpleeg [een Azure Automation Runbook maken](./automation-quickstart-create-runbook.md).
2. Selecteer in uw Automation-account **Runbooks** onder **proces automatisering**.
3. Selecteer het nieuwe runbook en klik op **bewerken**.
4. Kopieer uw script en plak het in de tekst editor voor het runbook.
5. Selecteer **assets**, vouw vervolgens **referenties** uit en controleer of de Office 365-referentie daar aanwezig is.
6. Klik op **Opslaan**.
7. Selecteer **test deel venster** en klik vervolgens op **starten** om te beginnen met het testen van uw runbook. Zie [Runbooks beheren in azure Automation](./manage-runbooks.md).
8. Wanneer het testen is voltooid, sluit u af vanuit het test venster.

## <a name="publish-and-schedule-the-runbook"></a>Het runbook publiceren en plannen

Als u uw runbook wilt publiceren en vervolgens wilt plannen, raadpleegt u [Runbooks beheren in azure Automation](./manage-runbooks.md).

## <a name="next-steps"></a>Volgende stappen

* Zie [referenties beheren in azure Automation](shared-resources/credentials.md)voor meer informatie over het gebruik van referenties.
* Zie [modules beheren in azure Automation](shared-resources/modules.md)voor meer informatie over modules.
* Als u een runbook moet starten, raadpleegt u [een Runbook starten in azure Automation](start-runbooks.md).
* Zie Power shell- [documenten](/powershell/scripting/overview)voor meer informatie over Power shell.
