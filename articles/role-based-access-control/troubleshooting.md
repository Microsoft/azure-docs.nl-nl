---
title: Problemen met Azure RBAC oplossen
description: Problemen oplossen met toegangs beheer op basis van rollen (Azure RBAC) van Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1, devx-track-azurecli
ms.openlocfilehash: d77468619fcd67887273b2fbd452b37add1e19b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555879"
---
# <a name="troubleshoot-azure-rbac"></a>Problemen met Azure RBAC oplossen

In dit artikel worden enkele veelgestelde vragen over toegangs beheer op basis van rollen (Azure RBAC) beantwoord, zodat u zeker weet wat u kunt verwachten wanneer u de functies gebruikt en toegangs problemen wilt oplossen.

## <a name="azure-role-assignments-limit"></a>Limiet voor Azure-roltoewijzingen

Azure ondersteunt maximaal **2000** roltoewijzingen per abonnement. Deze limiet omvat roltoewijzingen voor het abonnement, de resourcegroep en het resourcebereik. Als u het fout bericht ' er kunnen geen roltoewijzingen meer worden gemaakt (code: RoleAssignmentLimitExceeded) ' wordt weer gegeven wanneer u probeert een rol toe te wijzen, kunt u het aantal roltoewijzingen in het abonnement verminderen.

> [!NOTE]
> De limieten voor het aantal roltoewijzingen per abonnement van **2000** zijn vast en kunnen niet worden verhoogd.

Als u bijna de limiet hebt bereikt, kunt u het aantal roltoewijzingen beperken door het volgende te doen:

- Voeg gebruikers toe aan groepen en wijs in plaats daarvan rollen toe aan de groepen. 
- Combi neer meerdere ingebouwde rollen met een aangepaste rol. 
- Algemene roltoewijzingen maken voor een hoger bereik, zoals een abonnement of een beheer groep.
- Als u Azure AD Premium P2 hebt, maakt u roltoewijzingen in aanmerking in [Azure AD privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) in plaats van permanent toegewezen. 
- Voeg een extra abonnement toe. 

Als u het aantal roltoewijzingen wilt weten, kunt u de grafiek weer geven [op de pagina toegangs beheer (IAM)](role-assignments-list-portal.md#list-number-of-role-assignments) in de Azure Portal. U kunt ook de volgende Azure PowerShell-opdrachten gebruiken:

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Problemen met Azure-roltoewijzingen

- Als u geen rol kunt toewijzen in de Azure Portal op **toegangs beheer (IAM),** **omdat de**  >  optie **toewijzing van roltoewijzing** toevoegen is uitgeschakeld of omdat u de machtigingen fout ' de client met object-id heeft geen toestemming hebt om actie uit te voeren ', Controleer of u momenteel bent aangemeld met een gebruiker aan wie een rol is toegewezen die de `Microsoft.Authorization/roleAssignments/write` machtiging heeft, zoals [eigenaar](built-in-roles.md#owner) of [gebruikers toegangs beheerder](built-in-roles.md#user-access-administrator) in het bereik dat u probeert toe te wijzen aan de rol.
- Als u een Service-Principal gebruikt om rollen toe te wijzen, kunt u het volgende fout bericht krijgen: onvoldoende bevoegdheden om de bewerking te volt ooien. Stel bijvoorbeeld dat u een Service-Principal hebt waaraan de rol eigenaar is toegewezen en u probeert de volgende roltoewijzing te maken als service-principal met behulp van Azure CLI:

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    Als u de fout melding ' onvoldoende bevoegdheden om de bewerking te volt ooien ' krijgt, wordt de identiteit van de toegewezen persoon in azure AD waarschijnlijk niet door Azure CLI gezocht en kan Azure AD niet standaard door de service-principal worden gelezen.

    Er zijn twee manieren om deze fout op te lossen. De eerste manier is het toewijzen van de rol van de [Directory lezers](../active-directory/roles/permissions-reference.md#directory-readers) aan de Service-Principal zodat deze gegevens in de map kan lezen.

    De tweede manier om deze fout op te lossen is door de roltoewijzing te maken met behulp `--assignee-object-id` van de para meter in plaats van `--assignee` . Als u `--assignee-object-id` Azure cli gebruikt, wordt de Azure AD-zoek opdracht overs Laan. U moet de object-ID van de gebruiker, groep of toepassing ophalen waaraan u de rol wilt toewijzen. Zie [Azure-rollen toewijzen met Azure cli](role-assignments-cli.md#assign-a-role-for-a-new-service-principal-at-a-resource-group-scope)voor meer informatie.

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```
- Als u probeert de toewijzing van de laatste eigenaar voor een abonnement te verwijderen, ziet u mogelijk de fout "kan de laatste RBAC-beheer toewijzing niet verwijderen." Het verwijderen van de roltoewijzing voor de laatste eigenaar voor een abonnement wordt niet ondersteund om zwevende het abonnement te voor komen. Als u uw abonnement wilt annuleren, raadpleegt u [uw Azure-abonnement annuleren](../cost-management-billing/manage/cancel-azure-subscription.md).

## <a name="problems-with-custom-roles"></a>Problemen met aangepaste rollen

- Als u stappen nodig hebt voor het maken van een aangepaste rol, raadpleegt u de zelf studies voor aangepaste rollen met behulp van de [Azure Portal](custom-roles-portal.md) (momenteel in Preview), [Azure POWERSHELL](tutorial-custom-role-powershell.md)of [Azure cli](tutorial-custom-role-cli.md).
- Als u een bestaande aangepaste rol niet kunt bijwerken, controleert u of u momenteel bent aangemeld met een gebruiker aan wie een rol is toegewezen die de `Microsoft.Authorization/roleDefinition/write` machtiging heeft, zoals [eigenaar](built-in-roles.md#owner) of [beheerder van gebruikers toegang](built-in-roles.md#user-access-administrator).
- Als het u niet lukt om een aangepaste rol te verwijderen en het foutbericht 'There are existing role assignments referencing role (code: RoleDefinitionHasAssignments)' (Er zijn bestaande roltoewijzingen die aan een rol refereren (code: RoleDefinitionHasAssignments)) verschijnt, dan zijn er roltoewijzingen die nog altijd de aangepaste rol gebruiken. Verwijder deze roltoewijzingen en probeer de aangepaste rol opnieuw te verwijderen.
- Als tijdens het maken van een nieuwe aangepaste rol het foutbericht 'Role definition limit exceeded. No more role definitions can be created (RoleDefinitionLimitExceeded)' Er kunnen geen roldefinities meer worden gemaakt (code: RoleDefinitionLimitExceeded) "wanneer u probeert een nieuwe aangepaste rol te maken, verwijdert u alle aangepaste rollen die niet worden gebruikt. Azure biedt ondersteuning voor Maxi maal **5000** aangepaste rollen in een directory. (Voor Azure Duitsland en Azure China 21Vianet is de limiet 2000 aangepaste rollen.)
- Als er een fout bericht wordt weer gegeven dat vergelijkbaar is met de machtiging ' de client is gemachtigd voor het uitvoeren van de actie ' micro soft. Authorization/roleDefinitions/write ' op het bereik '/Subscriptions/{subscriptionid} ', is het gekoppelde abonnement niet gevonden ' wanneer u een aangepaste rol probeert bij te werken, controleert u of een of meer [toewijs bare bereiken](role-definitions.md#assignablescopes) in de map zijn verwijderd. Als het bereik is verwijderd, maakt u een ondersteuningsticket, aangezien hiervoor op dit moment geen selfserviceoplossing beschikbaar is.

## <a name="custom-roles-and-management-groups"></a>Aangepaste rollen en beheer groepen

- U kunt slechts één beheer groep definiëren in `AssignableScopes` een aangepaste rol. Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase.
- Aangepaste rollen met `DataActions` kunnen niet worden toegewezen in het bereik van de beheer groep.
- Azure Resource Manager valideert niet het bestaan van de beheer groep in het toewijs bare bereik van de roldefinitie.
- Zie [uw resources organiseren met Azure-beheer groepen](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)voor meer informatie over aangepaste rollen en beheer groepen.

## <a name="transferring-a-subscription-to-a-different-directory"></a>Een abonnement overdragen naar een andere map

- Zie [een Azure-abonnement overdragen naar een andere Azure AD-adres lijst](transfer-subscription.md)als u stappen nodig hebt voor het overdragen van een abonnement naar een andere Azure AD-adres lijst.
- Als u een abonnement overbrengt naar een andere Azure AD-adres lijst, worden alle roltoewijzingen **permanent** verwijderd uit de Azure AD-bron directory en worden ze niet gemigreerd naar de Azure AD-doel directory. U moet uw roltoewijzingen opnieuw maken in de doel directory. U moet ook hand matig beheerde identiteiten voor Azure-resources maken. Zie [Veelgestelde vragen en bekende problemen met beheerde identiteiten](../active-directory/managed-identities-azure-resources/known-issues.md)voor meer informatie.
- Als u een globale Azure AD-beheerder bent en u geen toegang hebt tot een abonnement nadat het is overgebracht tussen directory's, gebruikt u de wissel knop **voor het toegangs beheer voor Azure-resources** om uw toegang tijdelijk uit te [breiden](elevate-access-global-admin.md) om toegang te krijgen tot het abonnement.

## <a name="issues-with-service-admins-or-co-admins"></a>Problemen met servicebeheerders of co-beheerders

- Als u problemen ondervindt met Service beheerder of mede beheerders, raadpleegt u Azure-abonnements beheerders en [klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-rollen](rbac-and-directory-admin-roles.md) [toevoegen of wijzigen](../cost-management-billing/manage/add-change-subscription-administrator.md) .

## <a name="access-denied-or-permission-errors"></a>Toegang geweigerd of machtigings fouten

- Als de machtigingsfout 'Client met object-id is niet gemachtigd om actie uit te voeren over bereik (code: AuthorizationFailed)' wanneer u een resource wilt maken, controleert u of u momenteel bent aangemeld met een gebruiker die een rol heeft met schrijfrechten voor de resource in het geselecteerde bereik. Als u bijvoorbeeld virtuele machines in een resourcegroep wilt beheren, moet u de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toewijzen aan de resourcegroep (of bovenliggend bereik). Zie [ingebouwde rollen van Azure](built-in-roles.md)voor een lijst met machtigingen voor elke ingebouwde rol.
- Als u de machtiging fout ' u bent niet gemachtigd om een ondersteunings aanvraag te maken ' krijgt wanneer u een ondersteunings ticket probeert te maken of bij te werken, controleert u of u momenteel bent aangemeld met een gebruiker aan wie een rol is toegewezen `Microsoft.Support/supportTickets/write` , zoals [mede werker van de ondersteunings aanvraag](built-in-roles.md#support-request-contributor).

## <a name="move-resources-with-role-assignments"></a>Resources verplaatsen met roltoewijzingen

Als u een resource verplaatst waaraan een Azure-rol rechtstreeks is toegewezen aan de resource (of een onderliggende resource), wordt de roltoewijzing niet verplaatst en wordt deze zwevend. Nadat de verplaatsing is verplaatst, moet u de roltoewijzing opnieuw maken. Uiteindelijk wordt de zwevende roltoewijzing automatisch verwijderd, maar het is een best practice om de roltoewijzing te verwijderen voordat u de resource verplaatst.

Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)voor meer informatie over het verplaatsen van resources.

## <a name="role-assignments-with-identity-not-found"></a>Roltoewijzingen met identiteit niet gevonden

In de lijst met roltoewijzingen voor de Azure Portal ziet u misschien dat de beveiligingsprincipal (gebruiker, groep, Service-Principal of beheerde identiteit) wordt weer gegeven als identiteit die **niet is gevonden** met een **onbekend** type.

![Kan de identiteit niet vinden die wordt weer gegeven in azure-roltoewijzingen](./media/troubleshooting/unknown-security-principal.png)

De identiteit kan om twee redenen niet worden gevonden:

- U hebt onlangs een gebruiker uitgenodigd bij het maken van een roltoewijzing
- U hebt een beveiligingsprincipal met een roltoewijzing verwijderd

Als u onlangs een gebruiker hebt uitgenodigd bij het maken van een roltoewijzing, kan deze beveiligingsprincipal zich nog steeds in het replicatie proces tussen regio's bevinden. Als dit het geval is, wacht u even en vernieuwt u de lijst met roltoewijzingen.

Als deze beveiligingsprincipal echter geen recent uitgenodigde gebruiker is, is het mogelijk een verwijderde beveiligingsprincipal. Als u een rol toewijst aan een beveiligingsprincipal en vervolgens die beveiligingsprincipal verwijdert zonder eerst de roltoewijzing te verwijderen, wordt de beveiligingsprincipal vermeld als **identiteit niet gevonden** en een **onbekend** type.

Als u deze roltoewijzing met Azure PowerShell vermeldt, ziet u mogelijk een lege `DisplayName` en een `ObjectType` ingesteld op **onbekend**. [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) retourneert bijvoorbeeld een roltoewijzing die vergelijkbaar is met de volgende uitvoer:

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

Ook als u deze roltoewijzing met behulp van Azure CLI vermeldt, ziet u mogelijk een leeg `principalName` . Bijvoorbeeld, de [toewijzings lijst AZ Role](/cli/azure/role/assignment#az-role-assignment-list) retourneert een roltoewijzing die vergelijkbaar is met de volgende uitvoer:

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

Het is geen probleem om deze roltoewijzingen te verlaten wanneer de beveiligingsprincipal is verwijderd. Als u wilt, kunt u deze roltoewijzingen verwijderen met behulp van stappen die vergelijkbaar zijn met andere roltoewijzingen. Zie [Azure-roltoewijzingen verwijderen](role-assignments-remove.md)voor meer informatie over het verwijderen van roltoewijzingen.

Als u in Power shell probeert de roltoewijzingen te verwijderen met de naam van de object-ID en roldefinitie en er meer dan één roltoewijzing overeenkomt met de para meters, wordt het volgende fout bericht weer gegeven: ' de opgegeven informatie is niet toegewezen aan een roltoewijzing '. In de volgende uitvoer ziet u een voor beeld van het fout bericht:

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

Als u dit fout bericht ontvangt, moet u ook de `-Scope` `-ResourceGroupName` para meters of opgeven.

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>Er worden geen wijzigingen voor roltoewijzingen gedetecteerd

Azure Resource Manager worden soms configuraties en gegevens in de cache opgeslagen om de prestaties te verbeteren. Wanneer u rollen toewijst of roltoewijzingen verwijdert, kan het tot 30 minuten duren voordat de wijzigingen van kracht worden. Als u de Azure Portal, Azure PowerShell of Azure CLI gebruikt, kunt u het vernieuwen van uw roltoewijzingen afdwingen door u af te melden en u aan te melden. Als u wijzigingen aanbrengt in de roltoewijzing met REST API-aanroepen, kunt u een vernieuwing afdwingen door uw toegangs token te vernieuwen.

Als u een roltoewijzing toevoegt of verwijdert in het bereik van de beheer groep en de rol heeft `DataActions` , kan de toegang op het gegevensvlak niet enkele uren worden bijgewerkt. Dit geldt alleen voor beheer groeps bereik en het gegevens vlak.

## <a name="web-app-features-that-require-write-access"></a>Functies van web-apps waarvoor schrijf toegang is vereist

Als u een gebruiker alleen-lezen toegang verleent tot één web-app, worden sommige functies uitgeschakeld die u mogelijk niet verwacht. De volgende beheer functies vereisen **Schrijf** toegang tot een web-app (Inzender of eigenaar) en zijn niet beschikbaar in een alleen-lezen scenario.

* Opdrachten (zoals starten, stoppen, enz.)
* Instellingen wijzigen, zoals algemene configuratie, schaal instellingen, back-upinstellingen en controle-instellingen
* Toegang tot publicatie referenties en andere geheimen, zoals app-instellingen en verbindings reeksen
* Streaminglogboeken
* Configuratie van bron logboeken
* Console (opdracht prompt)
* Actieve en recente implementaties (voor lokale Git-continue implementatie)
* Geschatte uitgaven
* Webtests
* Virtueel netwerk (alleen zichtbaar voor een lezer als een virtueel netwerk eerder is geconfigureerd door een gebruiker met schrijf toegang).

Als u geen toegang hebt tot deze tegels, moet u de beheerder vragen om de toegang tot de web-app te verlenen.

## <a name="web-app-resources-that-require-write-access"></a>Web-app-resources waarvoor schrijf toegang is vereist

Web-apps zijn gecompliceerd door de aanwezigheid van een aantal verschillende resources die Interplay. Hier volgt een typische resource groep met een aantal websites:

![Resource groep voor web-app](./media/troubleshooting/website-resource-model.png)

Als u iemand toegang verleent tot alleen de web-app, is veel van de functionaliteit op de Blade website in de Azure Portal uitgeschakeld.

Voor deze items is **Schrijf** toegang vereist voor het **app service abonnement** dat overeenkomt met uw website:  

* De prijs categorie van de web-app weer geven (gratis of standaard)  
* Configuratie schalen (aantal exemplaren, grootte van de virtuele machine, instellingen voor automatisch schalen)  
* Quota (opslag, band breedte, CPU)  

Deze items hebben **Schrijf** toegang tot de hele **resource groep** die uw website bevat:  

* TLS/SSL-certificaten en bindingen (TLS/SSL-certificaten kunnen worden gedeeld tussen sites in dezelfde resource groep en geografische locatie)  
* Waarschuwingsregels  
* Instellingen voor automatisch schalen  
* Application Insights-onderdelen  
* Webtests  

## <a name="virtual-machine-features-that-require-write-access"></a>Functies van virtuele machines waarvoor schrijf toegang is vereist

Net als web apps vereist sommige functies op de Blade van de virtuele machine schrijf toegang tot de virtuele machine of andere resources in de resource groep.

Virtuele machines zijn gerelateerd aan domein namen, virtuele netwerken, opslag accounts en waarschuwings regels.

Voor deze items is **Schrijf** toegang tot de **virtuele machine** vereist:

* Eindpunten  
* IP-adressen  
* Disks  
* Uitbreidingen  

Hiervoor is **Schrijf** toegang vereist voor zowel de **virtuele machine** als de **resource groep** (samen met de domein naam) waarin deze zich bevindt:  

* Beschikbaarheidsset  
* Set met gelijke taak verdeling  
* Waarschuwingsregels  

Als u geen toegang hebt tot deze tegels, vraagt u uw beheerder om toegang tot de resource groep te krijgen.

## <a name="azure-functions-and-write-access"></a>Azure Functions en schrijf toegang

Voor sommige functies van [Azure functions](../azure-functions/functions-overview.md) is schrijf toegang vereist. Als een gebruiker bijvoorbeeld de rol [lezer](built-in-roles.md#reader) krijgt toegewezen, kunnen ze de functies in een functie-app niet weer geven. De portal wordt weer gegeven **(geen toegang)**.

![Functie-apps zonder toegang](./media/troubleshooting/functionapps-noaccess.png)

Een lezer kan op het tabblad **platform functies** klikken en vervolgens op **alle instellingen** klikken om enkele instellingen te bekijken die betrekking hebben op een functie-app (vergelijkbaar met een web-app), maar ze kunnen geen van deze instellingen wijzigen. U hebt de rol [Inzender](built-in-roles.md#contributor) nodig om toegang te krijgen tot deze functies.

## <a name="next-steps"></a>Volgende stappen

- [Problemen met gast gebruikers oplossen](role-assignments-external-users.md#troubleshoot)
- [Azure-rollen toewijzen met behulp van de Azure Portal](role-assignments-portal.md)
- [Activiteiten logboeken voor Azure RBAC-wijzigingen weer geven](change-history-report.md)
