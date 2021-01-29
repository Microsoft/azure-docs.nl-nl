---
title: Overzicht van verificatie van Azure Automation-account
description: Dit artikel bevat een overzicht van de verificatie van Azure Automation-account.
keywords: automation-beveiliging, veilige automation; automation-verificatie
services: automation
ms.subservice: process-automation
ms.date: 01/21/2021
ms.topic: conceptual
ms.openlocfilehash: e08a8bf3b06ca976cb10249af25099c7652e1b49
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053828"
---
# <a name="automation-account-authentication-overview"></a>Overzicht van Automation-accountverificatie

Met Azure Automation kunt u taken automatiseren voor bronnen in Azure, on-premises en bij andere cloudproviders zoals Amazon Web Services (AWS). U kunt runbooks gebruiken om uw taken te automatiseren, of een Hybrid Runbook Worker als u zakelijke of operationele processen hebt voor het beheren van buiten Azure. Voor het werken in een van deze omgevingen zijn machtigingen vereist om veilig toegang te krijgen tot de resources met de mini maal vereiste rechten.

In dit artikel worden verificatie scenario's behandeld die door Azure Automation worden ondersteund en wordt uitgelegd hoe u aan de slag kunt gaan op basis van de omgeving of omgevingen die u wilt beheren.

## <a name="automation-account"></a>Automation-account

Wanneer u Azure Automation voor het eerst start, moet u ten minste één Automation-account maken. Met Automation-accounts kunt u uw Automation-resources, runbooks, assets en configuraties isoleren van de resources van andere accounts. U kunt Automation-accounts gebruiken om resources te scheiden in afzonderlijke logische omgevingen of gedelegeerde verantwoordelijkheden. U kunt bijvoorbeeld één account maken voor ontwikkeling, één voor productie, en één voor uw on-premises omgeving. Of u kunt een Automation-account gebruiken om updates van het besturings systeem te beheren op al uw computers met [updatebeheer](update-management/overview.md). 

Een Azure Automation-account verschilt van uw Microsoft-account of de accounts die zijn gemaakt in uw Azure-abonnement. Zie [een Automation-account maken](automation-quickstart-create-account.md)voor een inleiding tot het maken van een Automation-account.

## <a name="automation-resources"></a>Automation-resources

De Automation-resources voor elk Automation-account zijn gekoppeld aan één Azure-regio, maar het account kan alle resources in uw Azure-abonnement beheren. De belangrijkste reden voor het maken van Automation-accounts in verschillende regio's is als u beleids regels hebt waarvoor gegevens en resources moeten worden geïsoleerd in een bepaalde regio.

Alle taken die u maakt met behulp van Azure Resource Manager en de Power shell-cmdlets in Azure Automation moeten worden geverifieerd bij Azure met behulp van Azure Active Directory (Azure AD)-verificatie op basis van de identiteit van de organisatie.

## <a name="run-as-accounts"></a>Uitvoeren als-accounts

Uitvoeren als-accounts in Azure Automation bieden verificatie voor het beheren van Azure Resource Manager resources of resources die zijn geïmplementeerd in het klassieke implementatie model. Er zijn twee typen uitvoeren als-accounts in Azure Automation:

* Uitvoeren als-account voor Azure: Hiermee kunt u Azure-resources beheren op basis van de Azure Resource Manager-implementatie en-beheer service voor Azure.
* Klassiek uitvoeren als-account voor Azure: Hiermee kunt u klassieke Azure-resources beheren op basis van het klassieke implementatie model.

Zie [Resource Manager en klassieke implementatie](../azure-resource-manager/management/deployment-models.md)voor meer informatie over de Azure Resource Manager en klassieke implementatie modellen.

>[!NOTE]
>Azure Cloud Solution Provider-abonnementen (CSP) ondersteunen alleen het Azure Resource Manager model. Niet-Azure Resource Manager services zijn niet beschikbaar in het programma. Wanneer u een CSP-abonnement gebruikt, wordt het klassieke uitvoeren als-account van Azure niet gemaakt, maar wordt het uitvoeren als-account van Azure gemaakt. Zie [beschik bare Services in CSP-abonnementen](/azure/cloud-solution-provider/overview/azure-csp-available-services)voor meer informatie over CSP-abonnementen.

Wanneer u een Automation-account maakt, wordt het uitvoeren als-account standaard op hetzelfde moment gemaakt. Als u ervoor hebt gekozen om deze niet samen met het Automation-account te maken, kan dit op een later tijdstip afzonderlijk worden gemaakt. Een klassiek uitvoeren als-account van Azure is optioneel en wordt afzonderlijk gemaakt als u klassieke resources moet beheren.

### <a name="run-as-account"></a>Run As-account

Wanneer u een uitvoeren als-account maakt, worden de volgende taken uitgevoerd:

* Hiermee maakt u een Azure AD-toepassing met een zelfondertekend certificaat, maakt u een Service-Principal-account voor de toepassing in azure AD en wijst u de rol [Inzender](../role-based-access-control/built-in-roles.md#contributor) toe voor het account in uw huidige abonnement. U kunt de certificaat instelling wijzigen in [lezer](../role-based-access-control/built-in-roles.md#reader) of een andere rol. Zie [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie.

* Hiermee maakt u een Automation-certificaat Asset `AzureRunAsCertificate` met de naam in het opgegeven Automation-account. De certificaat Asset bevat de persoonlijke sleutel van het certificaat dat door de Azure AD-toepassing wordt gebruikt.

* Hiermee wordt een Automation-verbindings element gemaakt `AzureRunAsConnection` met de naam in het opgegeven Automation-account. Het verbindings element bevat de toepassings-ID, Tenant-ID, abonnements-ID en vinger afdruk van het certificaat.

### <a name="azure-classic-run-as-account"></a>Klassiek uitvoeren als-account van Azure

Wanneer u een klassiek uitvoeren als-account voor Azure maakt, worden de volgende taken uitgevoerd:

> [!NOTE]
> U moet een mede beheerder zijn van het abonnement om dit type run as-account te maken of te vernieuwen.

* Hiermee maakt u een beheer certificaat in het abonnement.

* Hiermee maakt u een Automation-certificaat Asset `AzureClassicRunAsCertificate` met de naam in het opgegeven Automation-account. Het certificaatasset bevat de persoonlijke sleutel van het certificaat die wordt gebruikt door het beheercertificaat.

* Hiermee wordt een Automation-verbindings element gemaakt `AzureClassicRunAsConnection` met de naam in het opgegeven Automation-account. Het verbindings element bevat de naam van het abonnement, de abonnements-ID en de naam van de certificaat Asset.

## <a name="service-principal-for-run-as-account"></a>Service-Principal voor uitvoeren als-account

De service-principal voor een uitvoeren als-account heeft geen machtigingen om Azure AD standaard te lezen. Als u machtigingen wilt toevoegen om Azure AD te lezen of te beheren, moet u de machtigingen voor de Service-Principal verlenen onder **API-machtigingen**. Zie [machtigingen toevoegen voor toegang tot uw web-API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)voor meer informatie.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Machtigingen voor het run as-account

In deze sectie worden de machtigingen voor reguliere run as-accounts en klassieke uitvoeren als-accounts gedefinieerd.

* Als u een uitvoeren als-account wilt maken of bijwerken, kan een toepassings beheerder in Azure Active Directory en een eigenaar in het abonnement alle taken volt ooien.
* Als u klassieke uitvoeren als-accounts wilt configureren of vernieuwen, moet u de rol co-beheerder hebben op het abonnements niveau. Zie voor meer informatie over de machtigingen voor klassieke abonnementen [Azure Classic Subscription Administrators](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

In een situatie waarin u een schei ding van taken hebt, ziet u in de volgende tabel een lijst van de taken, de gelijkwaardige cmdlet en de benodigde machtigingen:

|Taak|Cmdlet  |Minimale machtigingen  |Waar u de machtigingen instelt|
|---|---------|---------|---|
|Een Azure AD-toepassing maken|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Application Developer-rol<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registraties |
|Voeg een referentie toe aan de toepassing.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Toepassings beheerder of globale beheerder<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registraties|
|Een Azure AD-service-principal maken en ophalen|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Toepassings beheerder of globale beheerder<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD > app-registraties|
|De Azure-rol voor de opgegeven Principal toewijzen of ophalen|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Beheerder of eigenaar van de gebruikers toegang of de volgende machtigingen hebben:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Home >-abonnementen > \<subscription name\> -Access Control (IAM)|
|Een Automation-certificaat maken of verwijderen|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Inzender voor resource groep         |Resource groep voor Automation-account|
|Een Automation-verbinding maken of verwijderen|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Inzender voor resource groep |Resource groep voor Automation-account|

<sup>1</sup> gebruikers die geen beheerder zijn in uw Azure AD-TENANT kunnen [ad-toepassingen registreren](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) als de optie gebruikers van de Azure AD-Tenant **kunnen toepassingen registreren** op de pagina **gebruikers instellingen** is ingesteld op **Ja**. Als de instelling voor de registratie van de toepassing **Nee** is, moet de gebruiker die deze actie uitvoert, worden gedefinieerd in deze tabel.

Als u geen lid bent van het Active Directory exemplaar van het abonnement voordat u wordt toegevoegd aan de rol van globale beheerder van het abonnement, wordt u als gast toegevoegd. In dit geval ontvangt u een `You do not have permissions to create…` waarschuwing op de pagina **Automation-account toevoegen** .

Om te controleren of de situatie die het fout bericht produceert, is opgelost:

1. Selecteer in het deel venster Azure Active Directory in het Azure Portal de optie **gebruikers en groepen**.
2. Selecteer **alle gebruikers**.
3. Kies uw naam en selecteer vervolgens **profiel**.
4. Zorg ervoor dat de waarde van het kenmerk **gebruikers type** onder het profiel van de gebruiker niet is ingesteld op **gast**.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Toegangs beheer op basis van rollen is beschikbaar met Azure Resource Manager om toegestane acties te verlenen aan een Azure AD-gebruikers account en een uitvoeren als-account en de service-principal te verifiëren. Lees het artikel [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie die u helpt bij het ontwikkelen van een model voor het beheren van machtigingen in Automation.

Als u strikte beveiligings controles voor machtigings toewijzing in resource groepen hebt, moet u het lidmaatschap van het run as-account toewijzen aan de rol **Inzender** in de resource groep.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbook-verificatie met Hybrid Runbook Worker

Runbooks die worden uitgevoerd op een Hybrid Runbook Worker in uw Data Center of voor computer services in andere Cloud omgevingen, zoals AWS, kunnen niet dezelfde methode gebruiken die doorgaans wordt gebruikt voor runbooks die worden geverifieerd bij Azure-resources. Dit is omdat deze resources buiten Azure worden uitgevoerd en er daarom voor deze resources eigen beveiligingsreferenties moeten worden gedefinieerd in Automation, zodat ze kunnen worden geverifieerd voor resources waartoe ze lokaal toegang hebben. Zie [Runbooks uitvoeren op een Hybrid Runbook worker](automation-hrw-run-runbooks.md)voor meer informatie over runbook-verificatie met runbook-werk rollen.

Voor runbooks die Hybrid Runbook Workers op virtuele machines van Azure gebruiken, kunt u [Runbook-verificatie met beheerde identiteiten](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) gebruiken in plaats van run as-accounts om uw Azure-resources te verifiëren.

## <a name="next-steps"></a>Volgende stappen

* Zie [een zelfstandig Azure Automation account maken](automation-create-standalone-account.md)als u een Automation-account wilt maken op basis van de Azure Portal.
* Als u uw account liever met behulp van een sjabloon wilt maken, raadpleegt u [een Automation-account maken met behulp van een Azure Resource Manager sjabloon](quickstart-create-automation-account-template.md).
* Zie [Runbooks verifiëren met Amazon Web Services](automation-config-aws-account.md)voor verificatie met behulp van Amazon Web Services.