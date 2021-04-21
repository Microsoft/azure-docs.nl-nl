---
title: Azure Automation accountverificatieoverzicht
description: In dit artikel vindt u een overzicht van Azure Automation accountverificatie.
keywords: automation-beveiliging, veilige automation; automation-verificatie
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 546407ce7286cebc04d3c86422f6242051d1dbf3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830426"
---
# <a name="azure-automation-account-authentication-overview"></a>Azure Automation accountverificatieoverzicht

Met Azure Automation kunt u taken automatiseren voor bronnen in Azure, on-premises en bij andere cloudproviders zoals Amazon Web Services (AWS). U kunt runbooks gebruiken om uw taken te automatiseren, of een Hybrid Runbook Worker als u zakelijke of operationele processen hebt om buiten Azure te beheren. Voor het werken in een van deze omgevingen zijn machtigingen vereist voor veilige toegang tot de resources met de minimale rechten die vereist zijn.

In dit artikel worden verificatiescenario's beschreven die door Azure Automation worden ondersteund en wordt beschreven hoe u aan de slag kunt gaan op basis van de omgeving of omgevingen die u moet beheren.

## <a name="automation-account"></a>Automation-account

Wanneer u Azure Automation voor het eerst start, moet u ten minste één Automation-account maken. Met Automation-accounts kunt u uw Automation-resources, runbooks, assets en configuraties isoleren van de resources van andere accounts. U kunt Automation-accounts gebruiken om resources te scheiden in afzonderlijke logische omgevingen of gedelegeerde verantwoordelijkheden. U kunt bijvoorbeeld één account maken voor ontwikkeling, één voor productie, en één voor uw on-premises omgeving. Of u kunt een Automation-account gebruiken voor het beheren van updates van besturingssystemen op al uw computers met [Updatebeheer](update-management/overview.md). 

Een Azure Automation-account verschilt van uw Microsoft-account of de accounts die zijn gemaakt in uw Azure-abonnement. Zie Een Automation-account maken voor een inleiding tot het [maken van een Automation-account.](automation-quickstart-create-account.md)

## <a name="automation-resources"></a>Automation-resources

De Automation-resources voor elk Automation-account zijn gekoppeld aan één Azure-regio, maar het account kan alle resources in uw Azure-abonnement beheren. De belangrijkste reden voor het maken van Automation-accounts in verschillende regio's is als u beleidsregels hebt die vereisen dat gegevens en resources worden geïsoleerd in een specifieke regio.

Alle taken die u maakt op basis van resources met behulp van Azure Resource Manager en de PowerShell-cmdlets in Azure Automation moeten worden geverifieerd bij Azure met behulp van verificatie op basis van organisatie-idreferenties van Azure Active Directory (Azure AD).

## <a name="managed-identities-preview"></a>Beheerde identiteiten (preview)

Met een beheerde identiteit van Azure Active Directory (Azure AD) heeft uw runbook eenvoudig toegang tot andere met Azure AD beveiligde resources. De identiteit wordt beheerd door het Azure-platform en u hoeft geen geheimen in terichten of te draaien. Zie Beheerde identiteiten voor Azure-resources voor meer informatie over [beheerde identiteiten](/azure/active-directory/managed-identities-azure-resources/overview)in Azure AD.

Hier zijn enkele voordelen van het gebruik van beheerde identiteiten:

- U kunt beheerde identiteiten gebruiken om te verifiëren bij elke Azure-service die ondersteuning biedt voor Azure AD-verificatie. Ze kunnen worden gebruikt voor cloudtaken en hybride taken. Hybride taken kunnen beheerde identiteiten gebruiken wanneer ze worden uitgevoerd op een Hybrid Runbook Worker die wordt uitgevoerd op een Azure- of niet-Azure-VM.

- Beheerde identiteiten kunnen worden gebruikt zonder extra kosten.

- U hoeft het certificaat dat wordt gebruikt door het Uitvoeren als-account van Automation, niet te vernieuwen.

- U hoeft het Run As-verbindingsobject niet op te geven in uw runbookcode. U hebt toegang tot resources met behulp van de beheerde identiteit van uw Automation-account vanuit een runbook zonder certificaten, verbindingen, Uitvoeren als-accounts, enzovoort te maken.

Aan een Automation-account kunnen twee typen identiteiten worden verleend:

- Een door het systeem toegewezen identiteit is gekoppeld aan uw toepassing en wordt verwijderd als uw app wordt verwijderd. Een app kan slechts één door het systeem toegewezen identiteit hebben.

- Een door de gebruiker toegewezen identiteit is een zelfstandige Azure-resource die kan worden toegewezen aan uw app. Een app kan meerdere door de gebruiker toegewezen identiteiten hebben.

>[!NOTE]
> Door de gebruiker toegewezen identiteiten worden nog niet ondersteund.

Zie Beheerde identiteit inschakelen voor Azure Automation (preview) voor meer informatie over het gebruik van [beheerde identiteiten.](enable-managed-identity-for-automation.md)

## <a name="run-as-accounts"></a>Uitvoeren als-accounts

Uitvoeren als-accounts in Azure Automation verificatie bieden voor het beheren van Azure Resource Manager resources of resources die zijn geïmplementeerd op het klassieke implementatiemodel. Er zijn twee typen Uitvoeren als-accounts in Azure Automation:

* Uitvoeren als-account van Azure: Hiermee kunt u Azure-resources beheren op basis van Azure Resource Manager implementatie- en beheerservice voor Azure.
* Klassiek Uitvoeren als-account van Azure: Hiermee kunt u klassieke Azure-resources beheren op basis van het klassieke implementatiemodel.

Zie voor meer informatie over de Azure Resource Manager en klassieke implementatiemodellen Resource Manager [klassieke implementatie.](../azure-resource-manager/management/deployment-models.md)

>[!NOTE]
>Azure Cloud Solution Provider (CSP)-abonnementen ondersteunen alleen het Azure Resource Manager model. Niet-Azure Resource Manager-services zijn niet beschikbaar in het programma. Wanneer u een CSP-abonnement gebruikt, wordt het klassieke Uitvoeren als-account van Azure niet gemaakt, maar wordt het Uitvoeren als-account van Azure gemaakt. Zie Beschikbare services in CSP-abonnementen voor meer informatie over [CSP-abonnementen.](/azure/cloud-solution-provider/overview/azure-csp-available-services)

Wanneer u een Automation-account maakt, wordt het Uitvoeren als-account standaard op hetzelfde moment gemaakt. Als u ervoor hebt gekozen om het account niet samen met het Automation-account te maken, kan dit op een later tijdstip afzonderlijk worden gemaakt. Een klassiek Uitvoeren als-account van Azure is optioneel en wordt afzonderlijk gemaakt als u klassieke resources wilt beheren.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>Run As-account

Wanneer u een Uitvoeren als-account maakt, worden de volgende taken uitgevoerd:

* Hiermee maakt u een Azure AD-toepassing met een zelf-ondertekend certificaat, maakt u [](../role-based-access-control/built-in-roles.md#contributor) een service-principal-account voor de toepassing in Azure AD en wijst u de rol Inzender toe voor het account in uw huidige abonnement. U kunt de certificaatinstelling wijzigen in [Lezer](../role-based-access-control/built-in-roles.md#reader) of een andere rol. Zie [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie.

* Hiermee maakt u een Automation-certificaatactivum `AzureRunAsCertificate` met de naam in het opgegeven Automation-account. Het certificaatactivum bevat de persoonlijke sleutel van het certificaat die door de Azure AD-toepassing wordt gebruikt.

* Hiermee maakt u een Automation-verbindingsactivum `AzureRunAsConnection` met de naam in het opgegeven Automation-account. De verbindingsactivum bevat de toepassings-id, tenant-id, abonnements-id en vingerafdruk van het certificaat.

### <a name="azure-classic-run-as-account"></a>Klassiek Uitvoeren als-account van Azure

Wanneer u een klassiek Uitvoeren als-account van Azure maakt, worden de volgende taken uitgevoerd:

> [!NOTE]
> U moet een medebeheerder van het abonnement zijn om dit type Uitvoeren als-account te maken of te verlengen.

* Hiermee maakt u een beheercertificaat in het abonnement.

* Hiermee maakt u een Automation-certificaatactivum `AzureClassicRunAsCertificate` met de naam in het opgegeven Automation-account. Het certificaatasset bevat de persoonlijke sleutel van het certificaat die wordt gebruikt door het beheercertificaat.

* Hiermee maakt u een Automation-verbindingsactivum `AzureClassicRunAsConnection` met de naam in het opgegeven Automation-account. De verbindingsactivum bevat de naam van het abonnement, de abonnements-id en de naam van het certificaatactivum.

## <a name="service-principal-for-run-as-account"></a>Service-principal voor Uitvoeren als-account

De service-principal voor een Uitvoeren als-account heeft standaard geen machtigingen om Azure AD te lezen. Als u machtigingen wilt toevoegen voor het lezen of beheren van Azure AD, moet u de machtigingen voor de service-principal onder **API-machtigingen verlenen.** Zie Machtigingen toevoegen voor toegang tot uw [web-API voor meer informatie.](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Uitvoeren als-accountmachtigingen

In deze sectie worden machtigingen voor zowel reguliere Uitvoeren als-accounts als klassieke Uitvoeren als-accounts definieert.

* Als u een Uitvoeren als-account wilt maken of bijwerken, kunnen Toepassingsbeheerder in Azure Active Directory en een eigenaar in het abonnement alle taken uitvoeren.
* Als u klassieke Uitvoeren als-accounts wilt configureren of vernieuwen, moet u de rol Co-beheerder hebben op abonnementsniveau. Zie Klassieke Azure-abonnementsbeheerders voor meer informatie over klassieke [abonnementsmachtigingen.](../role-based-access-control/classic-administrators.md#add-a-co-administrator)

In een situatie waarin u een scheiding van taken hebt, toont de volgende tabel een lijst met de taken, de equivalente cmdlet en de benodigde machtigingen:

|Taak|Cmdlet  |Minimale machtigingen  |Waar u de machtigingen in stelt|
|---|---------|---------|---|
|Een Azure AD-toepassing maken|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Rol van toepassingsontwikkelaar<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > Azure AD-> app-registraties |
|Voeg een referentie toe aan de toepassing.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Toepassingsbeheerder of globale beheerder<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > azure AD-> app-registraties|
|Een Azure AD-service-principal maken en krijgen|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Toepassingsbeheerder of globale beheerder<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Start > azure AD-> app-registraties|
|De Azure-rol voor de opgegeven principal toewijzen of krijgen|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Beheerder of eigenaar van gebruikerstoegang, of u hebt de volgende machtigingen:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Abonnement](../role-based-access-control/role-assignments-portal.md)</br>Home > Subscriptions > \<subscription name\> - Access Control (IAM)|
|Een Automation-certificaat maken of verwijderen|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Inzender voor resourcegroep         |Resourcegroep voor Automation-account|
|Een Automation-verbinding maken of verwijderen|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Inzender voor resourcegroep |Resourcegroep voor Automation-account|

<sup>1</sup> Niet-beheerders in uw Azure AD-tenant kunnen [AD-toepassingen](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) registreren als de  optie Gebruikers kunnen toepassingen registreren van de Azure AD-tenant op de pagina Gebruikersinstellingen is ingesteld op  **Ja.** Als de instelling voor toepassingsregistratie **Nee** is, moet de gebruiker die deze actie uitvoeren, zijn zoals gedefinieerd in deze tabel.

Als u geen lid bent van het Active Directory-exemplaar van het abonnement voordat u wordt toegevoegd aan de rol Globale beheerder van het abonnement, wordt u als gast toegevoegd. In dit geval ontvangt u een waarschuwing `You do not have permissions to create…` op de pagina **Automation-account** toevoegen.

Controleren of de situatie die het foutbericht produceert, is opgelost:

1. Selecteer in Azure Active Directory deelvenster in Azure Portal de optie **Gebruikers en groepen.**
2. Selecteer **Alle gebruikers.**
3. Kies uw naam en selecteer vervolgens **Profiel**.
4. Zorg ervoor dat de waarde van het **kenmerk Gebruikerstype** onder het profiel van uw gebruiker niet is ingesteld op **Gast**.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

Op rollen gebaseerd toegangsbeheer is beschikbaar met Azure Resource Manager om toegestane acties toe te staan aan een Azure AD-gebruikersaccount en Uitvoeren als-account, en om de service-principal te verifiëren. Lees het artikel [Op rollen gebaseerd toegangsbeheer in Azure Automation](automation-role-based-access-control.md) voor meer informatie die u helpt bij het ontwikkelen van een model voor het beheren van machtigingen in Automation.

Als u strikte beveiligingscontroles voor machtigingstoewijzing in resourcegroepen hebt,  moet u het Run As-accountlidmaatschap toewijzen aan de rol Inzender in de resourcegroep.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Runbookverificatie met Hybrid Runbook Worker

Runbooks die worden uitgevoerd op een Hybrid Runbook Worker in uw datacenter of voor computingservices in andere cloudomgevingen, zoals AWS, kunnen niet dezelfde methode gebruiken die doorgaans wordt gebruikt voor runbooks die worden gebruikt voor het authenticeren van Azure-resources. Dit is omdat deze resources buiten Azure worden uitgevoerd en er daarom voor deze resources eigen beveiligingsreferenties moeten worden gedefinieerd in Automation, zodat ze kunnen worden geverifieerd voor resources waartoe ze lokaal toegang hebben. Zie Runbooks uitvoeren op een Hybrid Runbook Worker voor meer informatie over [runbookverificatie met runbook workers.](automation-hrw-run-runbooks.md)

Voor runbooks die gebruikmaken van Hybrid Runbook Workers op azure-VM's, kunt u [runbookverificatie](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) met beheerde identiteiten gebruiken in plaats van Uitvoeren als-accounts om te verifiëren bij uw Azure-resources.

## <a name="next-steps"></a>Volgende stappen

* Als u een Automation-account wilt maken op Azure Portal, zie [Een zelfstandig Azure Automation maken.](automation-create-standalone-account.md)
* Als u liever uw account maakt met behulp van een sjabloon, zie [Een Automation-account maken](quickstart-create-automation-account-template.md)met behulp van een Azure Resource Manager sjabloon .
* Zie [Runbooks](automation-config-aws-account.md)verifiëren met Amazon Web Services voor verificatie met Amazon Web Services .
* Zie [Services die ondersteuning bieden voor beheerde identiteiten voor Azure-resources](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities) voor meer informatie.
