---
title: Problemen met Azure RBAC oplossen
description: Problemen oplossen met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).
services: azure-portal
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/06/2021
ms.author: rolyon
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d816854c8d8a78931060c6e56fffbaee1fde5150
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771709"
---
# <a name="troubleshoot-azure-rbac"></a>Problemen met Azure RBAC oplossen

In dit artikel vindt u antwoorden op enkele veelvoorkomende vragen over op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC), zodat u weet wat u kunt verwachten wanneer u de rollen gebruikt en toegangsproblemen kunt oplossen.

## <a name="azure-role-assignments-limit"></a>Limiet voor Azure-roltoewijzingen

Azure ondersteunt maximaal **2000** roltoewijzingen per abonnement. Deze limiet omvat roltoewijzingen voor het abonnement, de resourcegroep en het resourcebereik. Als het foutbericht 'Er kunnen geen roltoewijzingen meer worden gemaakt (code: RoleAssignmentLimitExceeded)' wordt weergegeven wanneer u een rol probeert toe te wijzen, probeert u het aantal roltoewijzingen in het abonnement te verminderen.

> [!NOTE]
> De **limiet van 2000** roltoewijzingen per abonnement is vast en kan niet worden verhoogd.

Als u deze limiet bijna bereikt, kunt u op de volgende manieren het aantal roltoewijzingen verminderen:

- Voeg gebruikers toe aan groepen en wijs in plaats daarvan rollen toe aan de groepen. 
- Combineer meerdere ingebouwde rollen met een aangepaste rol. 
- Maak algemene roltoewijzingen met een hoger bereik, zoals een abonnement of beheergroep.
- Als u een Azure AD Premium P2 hebt, maakt u roltoewijzingen [in](../active-directory/privileged-identity-management/pim-configure.md) aanmerking voor Azure AD Privileged Identity Management in plaats van permanent toegewezen. 
- Voeg een extra abonnement toe. 

Als u het aantal roltoewijzingen wilt weten, kunt u de grafiek bekijken op de pagina Toegangsbeheer [(IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) in de Azure Portal. U kunt ook de volgende opdrachten Azure PowerShell gebruiken:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemen met Azure-roltoewijzingen

- Als u geen rol kunt toewijzen in de Azure Portal op Toegangsbeheer  **(IAM)** omdat de optie Roltoewijzing toevoegen is uitgeschakeld of omdat u de machtigingsfout 'De client met object-id is niet autorisatie heeft om actie uit te voeren' krijgt, controleert u of u momenteel bent aangemeld met een gebruiker die is aangemeld met een rol die de machtiging heeft, zoals Eigenaar of Beheerder van gebruikerstoegang voor het bereik dat u probeert toe te  >   `Microsoft.Authorization/roleAssignments/write` wijzen. [](built-in-roles.md#owner) [](built-in-roles.md#user-access-administrator)
- Als u een service-principal gebruikt om rollen toe te wijzen, krijgt u mogelijk de fout 'Onvoldoende bevoegdheden om de bewerking te voltooien'. Stel dat u een service-principal hebt aan wie de rol Eigenaar is toegewezen en dat u de volgende roltoewijzing als de service-principal probeert te maken met behulp van Azure CLI:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Als u de fout 'Onvoldoende bevoegdheden om de bewerking te voltooien' krijgt, is dit waarschijnlijk omdat Azure CLI probeert de toegewezen identiteit op te zoeken in Azure AD en de service-principal Azure AD standaard niet kan lezen.

    Er zijn twee manieren om deze fout op te lossen. De eerste manier is om de rol [Directory Readers toe](../active-directory/roles/permissions-reference.md#directory-readers) te wijzen aan de service-principal, zodat deze gegevens in de map kan lezen.

    De tweede manier om deze fout op te lossen, is door de roltoewijzing te maken met behulp van `--assignee-object-id` de parameter in plaats van `--assignee` . Met behulp `--assignee-object-id` van slaat Azure CLI de Azure AD-zoekactie over. U moet de object-id krijgen van de gebruiker, groep of toepassing aan wie u de rol wilt toewijzen. Zie Azure-rollen toewijzen [met behulp van Azure CLI voor meer informatie.](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope)

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

- Als u een nieuwe service-principal maakt en onmiddellijk probeert een rol toe te wijzen aan die service-principal, kan die roltoewijzing in sommige gevallen mislukken.

    Als u dit scenario wilt aanpakken, moet u de eigenschap `principalType` instellen op bij het maken van de `ServicePrincipal` roltoewijzing. U moet ook de `apiVersion` van de roltoewijzing instellen op `2018-09-01-preview` of hoger. Zie Azure-rollen toewijzen aan een nieuwe [service-principal](role-assignments-rest.md#new-service-principal) met behulp van de REST API of Azure-rollen toewijzen aan een nieuwe service-principal met behulp [van Azure Resource Manager-sjablonen](role-assignments-template.md#new-service-principal)

- Als u de roltoewijzing van de laatste eigenaar voor een abonnement probeert te verwijderen, ziet u mogelijk de fout 'Kan de laatste RBAC-beheerderstoewijzing niet verwijderen'. Het verwijderen van de roltoewijzing van de laatste eigenaar voor een abonnement wordt niet ondersteund om zwevende abonnementen te voorkomen. Zie Uw Azure-abonnement annuleren als u uw abonnement [wilt annuleren.](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="problems-with-custom-roles"></a>Problemen met aangepaste rollen

- Als u stappen nodig hebt voor het maken van een aangepaste rol, bekijkt u de zelfstudies over aangepaste rollen met behulp van [Azure Portal,](custom-roles-portal.md) [Azure PowerShell](tutorial-custom-role-powershell.md)of [Azure CLI.](tutorial-custom-role-cli.md)
- Als u een bestaande aangepaste rol niet kunt bijwerken, controleert u of u momenteel bent aangemeld met een gebruiker aan wie een rol met de machtiging is toegewezen, zoals Eigenaar of Beheerder voor `Microsoft.Authorization/roleDefinition/write` [gebruikerstoegang.](built-in-roles.md#user-access-administrator) [](built-in-roles.md#owner)
- Als het u niet lukt om een aangepaste rol te verwijderen en het foutbericht 'There are existing role assignments referencing role (code: RoleDefinitionHasAssignments)' (Er zijn bestaande roltoewijzingen die aan een rol refereren (code: RoleDefinitionHasAssignments)) verschijnt, dan zijn er roltoewijzingen die nog altijd de aangepaste rol gebruiken. Verwijder deze roltoewijzingen en probeer de aangepaste rol opnieuw te verwijderen.
- Als tijdens het maken van een nieuwe aangepaste rol het foutbericht 'Role definition limit exceeded. No more role definitions can be created (RoleDefinitionLimitExceeded)' Er kunnen geen roldefinities meer worden gemaakt (code: RoleDefinitionLimitExceeded)' wanneer u een nieuwe aangepaste rol probeert te maken, verwijdert u eventuele aangepaste rollen die niet worden gebruikt. Azure ondersteunt maximaal **5000 aangepaste** rollen in een directory. (Voor Azure Duitsland en Azure China 21Vianet is de limiet 2000 aangepaste rollen.)
- Als er een foutbericht wordt weergegeven dat vergelijkbaar is met 'De client heeft toestemming om de actie 'Microsoft.Authorization/roleDefinitions/write' uit te voeren op het bereik '/subscriptions/{subscriptionid}', maar het gekoppelde abonnement is niet gevonden' wanneer u probeert een aangepaste rol bij te werken, controleert u of een of meer [toewijsbare scopes](role-definitions.md#assignablescopes) zijn verwijderd uit de map. Als het bereik is verwijderd, maakt u een ondersteuningsticket, aangezien hiervoor op dit moment geen selfserviceoplossing beschikbaar is.

## <a name="custom-roles-and-management-groups"></a>Aangepaste rollen en beheergroepen

- U kunt slechts één beheergroep in `AssignableScopes` van een aangepaste rol definiëren. Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase.
- Aangepaste rollen met `DataActions` kunnen niet worden toegewezen in het bereik van de beheergroep.
- Azure Resource Manager valideert niet het bestaan van de beheergroep in het toewijsbare bereik van de roldefinitie.
- Zie Uw resources organiseren met Azure-beheergroepen voor meer informatie over aangepaste rollen [en beheergroepen.](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="transferring-a-subscription-to-a-different-directory"></a>Een abonnement overdragen naar een andere directory

- Zie Transfer an Azure subscription to a different Azure AD directory (Een Azure-abonnement overdragen naar een andere Azure AD-directory) als u stappen nodig hebt voor het overdragen van een abonnement naar een [andere Azure AD-directory.](transfer-subscription.md)
- Als u een abonnement overdraagt naar een andere Azure AD-directory, worden alle roltoewijzingen permanent verwijderd uit **de** Azure AD-brondirectory en worden ze niet gemigreerd naar de Azure AD-doeldirectory. U moet uw roltoewijzingen opnieuw maken in de doelmap. U moet ook handmatig beheerde identiteiten voor Azure-resources opnieuw maken. Zie Veelgestelde vragen en bekende problemen met beheerde identiteiten [voor meer informatie.](../active-directory/managed-identities-azure-resources/known-issues.md)
- Als u een globale beheerder van Azure AD bent en u geen toegang hebt tot een abonnement nadat het [](elevate-access-global-admin.md) is overgedragen tussen directories, gebruikt u de schakelknop Toegangsbeheer voor **Azure-resources** om uw toegang tijdelijk uit te verhogen om toegang tot het abonnement te krijgen.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemen met servicebeheerders of co-beheerders

- Als u problemen hebt met Servicebeheerder of medebeheerders, zie Azure-abonnementsbeheerders en klassieke [abonnementsbeheerdersrollen,](../cost-management-billing/manage/add-change-subscription-administrator.md) Azure-rollen en [Azure AD-rollen](rbac-and-directory-admin-roles.md)toevoegen of wijzigen.

## <a name="access-denied-or-permission-errors"></a>Toegang geweigerd of machtigingsfouten

- Als de machtigingsfout 'Client met object-id is niet gemachtigd om actie uit te voeren over bereik (code: AuthorizationFailed)' wanneer u een resource wilt maken, controleert u of u momenteel bent aangemeld met een gebruiker die een rol heeft met schrijfrechten voor de resource in het geselecteerde bereik. Als u bijvoorbeeld virtuele machines in een resourcegroep wilt beheren, moet u de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toewijzen aan de resourcegroep (of bovenliggend bereik). Zie Ingebouwde Azure-rollen voor een lijst met de machtigingen voor elke [ingebouwde rol.](built-in-roles.md)
- Als u de machtigingsfout 'U bent niet machtigingen voor het maken van een ondersteuningsaanvraag' krijgt wanneer u probeert een ondersteuningsticket te maken of bij te werken, controleert u of u momenteel bent aangemeld met een gebruiker aan wie een rol is toegewezen die de machtiging heeft, zoals Inzender voor `Microsoft.Support/supportTickets/write` ondersteuningsaanvraag. [](built-in-roles.md#support-request-contributor)

## <a name="move-resources-with-role-assignments"></a>Resources verplaatsen met roltoewijzingen

Als u een resource verplaatst aan wie een Azure-rol rechtstreeks is toegewezen aan de resource (of een onderliggende resource), wordt de roltoewijzing niet verplaatst en wordt deze zwevend. Na de verplaatsen moet u de roltoewijzing opnieuw maken. Uiteindelijk wordt de toewijzing van de zwevende rol automatisch verwijderd, maar het is een best practice roltoewijzing te verwijderen voordat u de resource verplaatst.

Zie Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement voor meer informatie over het [verplaatsen van resources.](../azure-resource-manager/management/move-resource-group-and-subscription.md)

## <a name="role-assignments-with-identity-not-found"></a>Roltoewijzingen met identiteit niet gevonden

In de lijst met roltoewijzingen voor de Azure Portal ziet u mogelijk dat de beveiligingsprincipaal (gebruiker,  groep, service-principal of beheerde identiteit) wordt vermeld als Identiteit niet gevonden met het **type** Onbekend.

![Identiteit niet gevonden in Azure-roltoewijzingen](./media/troubleshooting/unknown-security-principal.png)

De identiteit kan om twee redenen niet worden gevonden:

- U hebt onlangs een gebruiker uitgenodigd bij het maken van een roltoewijzing
- U hebt een beveiligingsprincipaal met een roltoewijzing verwijderd

Als u onlangs een gebruiker hebt uitgenodigd bij het maken van een roltoewijzing, is deze beveiligingsprincipaal mogelijk nog steeds in het replicatieproces tussen regio's. Als dat het zo is, wacht u even en vernieuwt u de lijst met roltoewijzingen.

Als deze beveiligingsprincipaal echter geen onlangs uitgenodigde gebruiker is, kan het een verwijderde beveiligingsprincipaal zijn. Als u een rol toewijst aan een beveiligingsprincipaal en u die beveiligingsprincipaal later verwijdert zonder eerst de roltoewijzing te verwijderen, wordt de beveiligingsprincipaal vermeld als Identiteit **niet** gevonden en als onbekend **type.**

Als u deze roltoewijzing met behulp van Azure PowerShell, ziet u mogelijk een leeg en een `DisplayName` die is ingesteld op `ObjectType` **Onbekend.** [Get-AzRoleAssignment retourneert](/powershell/module/az.resources/get-azroleassignment) bijvoorbeeld een roltoewijzing die vergelijkbaar is met de volgende uitvoer:

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Als u deze roltoewijzing vermeldt met behulp van Azure CLI, ziet u mogelijk een lege `principalName` . Az Role [Assignment List retourneert](/cli/azure/role/assignment#az_role_assignment_list) bijvoorbeeld een roltoewijzing die vergelijkbaar is met de volgende uitvoer:

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

Het is geen probleem om deze roltoewijzingen te laten waar de beveiligingsprincipaal is verwijderd. Als u wilt, kunt u deze roltoewijzingen verwijderen met behulp van stappen die vergelijkbaar zijn met andere roltoewijzingen. Zie Azure-roltoewijzingen verwijderen voor meer informatie over het verwijderen [van roltoewijzingen.](role-assignments-remove.md)

Als u in PowerShell de roltoewijzingen probeert te verwijderen met behulp van de object-id en de naam van de roldefinitie, en er meer dan één roltoewijzing overeenkomt met uw parameters, wordt het volgende foutbericht weergegeven: 'De opgegeven informatie wordt niet aan een roltoewijzing toewijsd'. In de volgende uitvoer ziet u een voorbeeld van het foutbericht:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Als dit foutbericht wordt weergegeven, moet u ook de `-Scope` parameters of `-ResourceGroupName` opgeven.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Wijzigingen in roltoewijzing worden niet gedetecteerd

Azure Resource Manager soms worden configuraties en gegevens in de cache opgeslagen om de prestaties te verbeteren. Wanneer u rollen toewijst of roltoewijzingen verwijdert, kan het tot 30 minuten duren voordat wijzigingen zijn doorgevoerd. Als u de Azure Portal, Azure PowerShell of Azure CLI gebruikt, kunt u het vernieuwen van uw roltoewijzingswijzigingen forceeren door u af te melden en aan te melden. Als u wijzigingen in de roltoewijzing aanroept REST API, kunt u een vernieuwing forceeren door uw toegangsken te vernieuwen.

Als u een roltoewijzing toevoegt aan of verwijdert bij het bereik van de beheergroep en de rol heeft , wordt de toegang tot het gegevensvlak mogelijk enkele `DataActions` uren niet bijgewerkt. Dit geldt alleen voor het bereik van beheergroep en het gegevensvlak.

## <a name="web-app-features-that-require-write-access"></a>Web-app-functies waarvoor schrijftoegang is vereist

Als u een gebruiker alleen-lezentoegang verleent tot één web-app, zijn sommige functies uitgeschakeld die u mogelijk niet verwacht. De volgende beheermogelijkheden **vereisen** schrijftoegang tot een web-app (Inzender of Eigenaar) en zijn niet beschikbaar in een alleen-lezen scenario.

* Opdrachten (zoals starten, stoppen, enzovoort)
* Instellingen wijzigen, zoals algemene configuratie, schaalinstellingen, back-upinstellingen en bewakingsinstellingen
* Toegang tot publicatiereferenties en andere geheimen, zoals app-instellingen en verbindingsreeksen
* Streaminglogboeken
* Configuratie van resourcelogboeken
* Console (opdrachtprompt)
* Actieve en recente implementaties (voor continue lokale git-implementatie)
* Geschatte uitgaven
* Webtests
* Virtueel netwerk (alleen zichtbaar voor een lezer als een virtueel netwerk eerder is geconfigureerd door een gebruiker met schrijftoegang).

Als u geen toegang hebt tot een van deze tegels, moet u uw beheerder vragen om toegang tot de web-app inzenders.

## <a name="web-app-resources-that-require-write-access"></a>Web-app-resources waarvoor schrijftoegang is vereist

Web-apps worden gecompliceerd door de aanwezigheid van een aantal verschillende resources die met elkaar worden gebruikt. Hier is een typische resourcegroep met een aantal websites:

![Resourcegroep voor web-app](./media/troubleshooting/website-resource-model.png)

Als u iemand alleen toegang verleent tot de web-app, is een groot deel van de functionaliteit op de blade van de website in de Azure Portal uitgeschakeld.

Deze items vereisen **schrijftoegang** tot het **App Service dat** overeenkomt met uw website:  

* De prijscategorie van de web-app weergeven (gratis of standaard)  
* Schaalconfiguratie (aantal exemplaren, grootte van virtuele machines, instellingen voor automatisch schalen)  
* Quota (opslag, bandbreedte, CPU)  

Voor deze items **is schrijftoegang** vereist tot de **hele resourcegroep** die uw website bevat:  

* TLS/SSL-certificaten en -bindingen (TLS/SSL-certificaten kunnen worden gedeeld tussen sites in dezelfde resourcegroep en geografische locatie)  
* Waarschuwingsregels  
* Instellingen voor automatisch schalen  
* Application Insights-onderdelen  
* Webtests  

## <a name="virtual-machine-features-that-require-write-access"></a>Functies van virtuele machines waarvoor schrijftoegang is vereist

Net als bij web-apps vereisen sommige functies op de blade van de virtuele machine schrijftoegang tot de virtuele machine of tot andere resources in de resourcegroep.

Virtuele machines zijn gerelateerd aan domeinnamen, virtuele netwerken, opslagaccounts en waarschuwingsregels.

Voor deze items is **schrijftoegang** tot de **virtuele machine vereist:**

* Eindpunten  
* IP-adressen  
* Disks  
* Uitbreidingen  

Deze vereisen **schrijftoegang** tot zowel de **virtuele machine** als de **resourcegroep** (samen met de domeinnaam) waarin deze zich in:  

* Beschikbaarheidsset  
* Set met load balanceds  
* Waarschuwingsregels  

Als u geen toegang hebt tot een van deze tegels, vraagt u de beheerder om toegang als Inzender voor de resourcegroep.

## <a name="azure-functions-and-write-access"></a>Azure Functions en schrijftoegang

Sommige functies van [Azure Functions](../azure-functions/functions-overview.md) vereisen schrijftoegang. Als aan een gebruiker bijvoorbeeld [](built-in-roles.md#reader) de rol Lezer is toegewezen, kunnen ze de functies in een functie-app niet weergeven. In de portal wordt **weergegeven (geen toegang).**

![Functie-apps hebben geen toegang](./media/troubleshooting/functionapps-noaccess.png)

Een lezer kan op het tabblad  **Platformfuncties** klikken en vervolgens op Alle instellingen klikken om bepaalde instellingen weer te geven die betrekking hebben op een functie-app (vergelijkbaar met een web-app), maar ze kunnen deze instellingen niet wijzigen. Voor toegang tot deze functies hebt u de rol [Inzender](built-in-roles.md#contributor) nodig.

## <a name="next-steps"></a>Volgende stappen

- [Problemen met gastgebruikers oplossen](role-assignments-external-users.md#troubleshoot)
- [Azure-rollen toewijzen met behulp van de Azure Portal](role-assignments-portal.md)
- [Activiteitenlogboeken voor Azure RBAC-wijzigingen weergeven](change-history-report.md)
