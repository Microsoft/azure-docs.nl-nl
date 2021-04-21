---
title: Veelvoorkomende implementatiefouten oplossen
description: Beschrijft hoe u veelvoorkomende fouten kunt oplossen wanneer u resources in Azure implementeert met behulp van Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 01/20/2021
ms.openlocfilehash: 07c197f1b54522b96a3bfa2d6a5ce7b368be3b35
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789173"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Veelvoorkomende fouten met Azure-implementatie oplossen met Azure Resource Manager

In dit artikel worden enkele veelvoorkomende implementatiefouten in Azure beschreven en wordt informatie gegeven om de fouten op te lossen. Zie [Foutcodes zoeken](#find-error-code) als u de foutcode voor uw implementatiefout niet kunt vinden.

Laat het ons weten als u op zoek bent naar informatie over een foutcode en deze informatie niet is opgegeven in dit artikel. Onderaan deze pagina kunt u feedback geven. De feedback wordt bij te houden met GitHub Issues.

## <a name="error-codes"></a>Foutcodes

| Foutcode | Oplossing | Meer informatie |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Volg de naamgevingsbeperkingen voor opslagaccounts. | [Naam van opslagaccount oplossen](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Controleer de beschikbare eigenschappen van het opslagaccount. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Het cluster of de regio heeft geen resources beschikbaar of kan de aangevraagde VM-grootte niet ondersteunen. U kunt de aanvraag op een later tijdstip opnieuw proberen of een andere VM-grootte aanvragen. | [Inrichtings- en toewijzingsproblemen voor Linux,](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux) [inrichtings-](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) en toewijzingsproblemen voor Windows en [Toewijzingsfouten oplossen](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | Wacht tot de gelijktijdige bewerking is voltooid. | |
| AuthorizationFailed | Uw account of service-principal heeft onvoldoende toegang om de implementatie te voltooien. Controleer de rol van uw account en de toegang tot het implementatiebereik.<br><br>Deze fout kan worden weergegeven wanneer een vereiste resourceprovider niet is geregistreerd. | [Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Registratie oplossen](error-register-resource-provider.md) |
| BadRequest | U hebt implementatiewaarden verzonden die niet overeenkomen met wat wordt verwacht door Resource Manager. Controleer het interne statusbericht voor hulp bij het oplossen van problemen. | [Sjabloonverwijzing en](/azure/templates/) [Ondersteunde locaties](resource-location.md) |
| Conflict | U vraagt een bewerking aan die niet is toegestaan in de huidige status van de resource. Het formaat van de schijf is bijvoorbeeld alleen toegestaan bij het maken van een VM of wanneer de toewijzing van de VM is verbreed. | |
| DeploymentActiveAndUneditable | Wacht tot de gelijktijdige implementatie naar deze resourcegroep is voltooid. | |
| DeploymentFailedCleanUp | Wanneer u in de modus Volledig implementeert, worden alle resources verwijderd die zich niet in de sjabloon hebben. U krijgt deze foutmelding wanneer u niet voldoende machtigingen hebt om alle resources te verwijderen die niet in de sjabloon zijn opgeslagen. Om de fout te voorkomen, wijzigt u de implementatiemodus in incrementeel. | [Azure Resource Manager deployment modes](deployment-modes.md) (Implementatiemodi voor Azure Resource Manager) |
| DeploymentNameInvalidCharacters | De implementatienaam mag alleen letter, cijfer, '-', '.' of '_' bevatten. | |
| DeploymentNameLengthLimitExceeded | De implementatienamen zijn beperkt tot 64 tekens.  | |
| DeploymentFailed | De DeploymentFailed-fout is een algemene fout die niet de details bevat die u nodig hebt om de fout op te lossen. Zoek in de foutdetails naar een foutcode die meer informatie biedt. | [Foutcode zoeken](#find-error-code) |
| DeploymentQuotaExceeded | Als u de limiet van 800 implementaties per resourcegroep bereikt, verwijdert u implementaties uit de geschiedenis die niet meer nodig zijn. | [Fout oplossen wanneer het aantal implementaties hoger is dan 800](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | Vereenvoudig uw sjabloon om de grootte te verkleinen. | [Fouten met de sjabloongrootte oplossen](error-job-size-exceeded.md) |
| DnsRecordInUse | De DNS-recordnaam moet uniek zijn. Voer een andere naam in. | |
| ImageNotFound | Controleer de instellingen voor de VM-afbeelding. |  |
| InUseSubnetCannotBeDeleted | Mogelijk krijgt u deze foutmelding wanneer u probeert een resource bij te werken en wordt de aanvraag verwerkt door de resource te verwijderen en te maken. Zorg ervoor dat u alle ongewijzigde waarden opgeeft. | [Bron bijwerken](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Toegangs token voor de juiste tenant op te halen. U kunt het token alleen krijgen van de tenant waar uw account bij hoort. | |
| InvalidContentLink | U hebt waarschijnlijk geprobeerd een koppeling te maken naar een geneste sjabloon die niet beschikbaar is. Controleer de URI die u hebt opgegeven voor de geneste sjabloon. Als de sjabloon in een opslagaccount bestaat, moet u ervoor zorgen dat de URI toegankelijk is. Mogelijk moet u een SAS-token doorgeven. Op dit moment kunt u geen koppeling maken naar een sjabloon die zich in een opslagaccount achter een [Azure Storage firewall.](../../storage/common/storage-network-security.md) Overweeg uw sjabloon te verplaatsen naar een andere opslagplaats, zoals GitHub. | [Gekoppelde sjablonen](linked-templates.md) |
| InvalidDeploymentLocation | Wanneer u implementeert op abonnementsniveau, hebt u een andere locatie opgegeven voor een eerder gebruikte implementatienaam. | [Implementaties op abonnementsniveau](deploy-to-subscription.md) |
| InvalidParameter | Een van de waarden die u hebt opgegeven voor een resource komt niet overeen met de verwachte waarde. Deze fout kan het gevolg zijn van veel verschillende voorwaarden. Een wachtwoord kan bijvoorbeeld onvoldoende zijn of de naam van een blob is mogelijk onjuist. Het foutbericht moet aangeven welke waarde moet worden gecorrigeerd. | |
| InvalidRequestContent | De implementatiewaarden bevatten waarden die niet worden herkend of vereiste waarden ontbreken. Bevestig de waarden voor uw resourcetype. | [Sjabloonverwijzing](/azure/templates/) |
| InvalidRequestFormat | Schakel logboekregistratie voor foutopsporing in bij het uitvoeren van de implementatie en controleer de inhoud van de aanvraag. | [Fouten opsporen in logboekregistratie](#enable-debug-logging) |
| InvalidResourceNamespace | Controleer de resourcenaamruimte die u hebt opgegeven in de **eigenschap type.** | [Sjabloonverwijzing](/azure/templates/) |
| InvalidResourceReference | De resource bestaat nog niet of er wordt onjuist naar verwezen. Controleer of u een afhankelijkheid moet toevoegen. Controleer of uw gebruik van de **referentiefunctie** de vereiste parameters voor uw scenario bevat. | [Afhankelijkheden oplossen](error-not-found.md) |
| InvalidResourceType | Controleer het resourcetype dat u hebt opgegeven in de **eigenschap type.** | [Sjabloonverwijzing](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registreer uw abonnement bij de resourceprovider. | [Registratie oplossen](error-register-resource-provider.md) |
| InvalidTemplate | Controleer de sjabloonsyntaxis op fouten. | [Ongeldige sjabloon oplossen](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Verwijder onnodige afhankelijkheden. | [Kringafhankelijkheden oplossen](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Vereenvoudig uw sjabloon om de grootte te verkleinen. | [Fouten met de sjabloongrootte oplossen](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Controleer of uw account tot dezelfde tenant behoort als de resourcegroep die u implementeert. | |
| LinkedInvalidPropertyId | De resource-id voor een resource wordt niet correct opgelost. Controleer of u alle vereiste waarden voor de resource-id op geeft, waaronder de abonnements-id, de naam van de resourcegroep, het resourcetype, de naam van de bovenliggende resource (indien nodig) en de resourcenaam. | |
| LocationRequired | Geef een locatie op voor de resource. | [Locatie instellen](resource-location.md) |
| Niet-overeenkomenderesourcesegmenten | Zorg ervoor dat geneste resource het juiste aantal segmenten in naam en type heeft. | [Resourcesegmenten oplossen](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Controleer de registratiestatus van de resourceprovider en de ondersteunde locaties. | [Registratie oplossen](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Registreer uw abonnement bij de resourceprovider. | [Registratie oplossen](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Controleer de registratiestatus van de resourceprovider. | [Registratie oplossen](error-register-resource-provider.md) |
| NotFound | Mogelijk probeert u een afhankelijke resource parallel te implementeren met een bovenliggende resource. Controleer of u een afhankelijkheid moet toevoegen. | [Afhankelijkheden oplossen](error-not-found.md) |
| OperationNotAllowed | De implementatie probeert een bewerking uit te voeren die het quotum voor het abonnement, de resourcegroep of de regio overschrijdt. Wijzig indien mogelijk uw implementatie om binnen de quota te blijven. Anders kunt u overwegen om een wijziging in uw quota aan te vragen. | [Quota oplossen](error-resource-quota.md) |
| ParentResourceNotFound | Zorg ervoor dat er een bovenliggende resource bestaat voordat u de onderliggende resources maakt. | [Bovenliggende resource oplossen](error-parent-resource.md) |
| PasswordTooLong | Mogelijk hebt u een wachtwoord met te veel tekens geselecteerd of uw wachtwoordwaarde geconverteerd naar een beveiligde tekenreeks voordat u dit als parameter doorgeeft. Als de sjabloon een beveiligde **tekenreeksparameter** bevat, hoeft u de waarde niet te converteren naar een beveiligde tekenreeks. Geef de wachtwoordwaarde op als tekst. |  |
| PrivateIPAddressInReservedRange | Het opgegeven IP-adres bevat een adresbereik dat is vereist door Azure. Wijzig het IP-adres om gereserveerd bereik te vermijden. | [IP-adressen](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | Het opgegeven IP-adres valt buiten het subnetbereik. Wijzig het IP-adres om binnen het subnetbereik te vallen. | [IP-adressen](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Sommige eigenschappen kunnen niet worden gewijzigd voor een geïmplementeerde resource. Beperk bij het bijwerken van een resource uw wijzigingen tot toegestane eigenschappen. | [Bron bijwerken](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | Uw abonnement bevat een resourcebeleid dat voorkomt dat een actie die u probeert uit te voeren tijdens de implementatie. Zoek het beleid dat de actie blokkeert. Wijzig indien mogelijk uw implementatie om te voldoen aan de beperkingen van het beleid. | [Beleidsregels oplossen](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Geef een resourcenaam op die geen gereserveerde naam bevat. | [Namen van gereserveerde resources](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Wacht tot het verwijderen is voltooid. | |
| ResourceGroupNotFound | Controleer de naam van de doelresourcegroep voor de implementatie. De doelresourcegroep moet al bestaan in uw abonnement. Controleer de context van uw abonnement. | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Uw implementatie verwijst naar een resource die niet kan worden opgelost. Controleer of uw gebruik van de **referentiefunctie** de parameters bevat die vereist zijn voor uw scenario. | [Verwijzingen oplossen](error-not-found.md) |
| ResourceQuotaExceeded | De implementatie probeert resources te maken die het quotum voor het abonnement, de resourcegroep of de regio overschrijden. Wijzig indien mogelijk uw infrastructuur om binnen de quota te blijven. Anders kunt u overwegen om een wijziging in uw quota aan te vragen. | [Quota oplossen](error-resource-quota.md) |
| SkuNotAvailable | Selecteer SKU (zoals VM-grootte) die beschikbaar is voor de locatie die u hebt geselecteerd. | [SKU oplossen](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Geef een unieke naam op voor het opslagaccount. | [Naam van opslagaccount oplossen](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | Geef een unieke naam op voor het opslagaccount. | [Naam van opslagaccount oplossen](error-storage-account-name.md) |
| StorageAccountNotFound | Controleer het abonnement, de resourcegroep en de naam van het opslagaccount dat u wilt gebruiken. | |
| SubnettenNotInSameVnet | Een virtuele machine kan slechts één virtueel netwerk hebben. Wanneer u verschillende NIC's implementeert, moet u ervoor zorgen dat ze deel uitmaken van hetzelfde virtuele netwerk. | [Meerdere NIC's](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | Een opgegeven abonnement voor implementatie kan niet worden gebruikt. Mogelijk is de abonnements-id onjuist, heeft de gebruiker die de sjabloon implementeert niet voldoende machtigingen om in het abonnement te implementeren of heeft de abonnements-id de verkeerde indeling. Wanneer u geneste implementaties gebruikt om [te implementeren in verschillende scopes,](./deploy-to-resource-group.md)geeft u de GUID voor het abonnement op. | |
| SubscriptionNotRegistered | Bij het implementeren van een resource moet de resourceprovider zijn geregistreerd voor uw abonnement. Wanneer u een Azure Resource Manager voor implementatie gebruikt, wordt de resourceprovider automatisch geregistreerd in het abonnement. Soms wordt de automatische registratie niet op tijd voltooid. Registreer de resourceprovider vóór de implementatie om deze onregelmatige fout te voorkomen. | [Registratie oplossen](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Verwijder onnodige afhankelijkheden. | [Kringafhankelijkheden oplossen](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Verminder het aantal resourcegroepen voor één implementatie. | [Implementatie tussen scopes](./deploy-to-resource-group.md) |

## <a name="find-error-code"></a>Foutcode zoeken

U kunt twee typen foutmeldingen krijgen:

* validatiefouten
* implementatiefouten

Validatiefouten ontstaan door scenario's en kunnen vóór de implementatie worden vastgesteld. Ze bevatten syntaxisfouten in de sjabloon of proberen resources te implementeren waarmee uw abonnementquota worden overschreden. Implementatiefouten ontstaan als gevolg van voorwaarden die tijdens het implementatieproces optreden. Bijvoorbeeld wanneer deze toegang proberen te krijgen tot een resource die parallel wordt geïmplementeerd.

Beide typen fouten retourneren een foutcode die u gebruikt om de problemen met de implementatie op te lossen. Beide typen fouten worden weergegeven in het [activiteitenlogboek](../management/view-activity-logs.md). Validatiefouten worden echter niet weergegeven in de implementatiegeschiedenis omdat de implementatie niet is gestart.

### <a name="validation-errors"></a>Validatiefouten

Wanneer u een implementatie uitvoert via de portal, ziet u een validatiefout na het indienen van uw waarden.

![validatiefout in portal tonen](./media/common-deployment-errors/validation-error.png)

Selecteer het bericht voor meer informatie. In de volgende afbeelding ziet u een **InvalidTemplateDeployment-fout** en een bericht dat aangeeft dat de implementatie is geblokkeerd door een beleid.

![validatiedetails tonen](./media/common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Implementatiefouten

Als de bewerking is gevalideerd, maar mislukt tijdens de implementatie, treedt er een implementatiefout op.

Om foutcodes en meldingen van implementatiefouten weer te geven in PowerShell, gebruikt u:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Om foutcodes en meldingen van implementatiefouten weer te geven in de Azure-CLI, gebruikt u:

```azurecli-interactive
az deployment operation group list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

In de portal selecteert u de melding.

![meldingsfout](./media/common-deployment-errors/notification.png)

U ziet meer informatie over de implementatie. Selecteer de optie voor meer informatie over de fout.

![implementatie mislukt](./media/common-deployment-errors/deployment-failed.png)

Het foutbericht en de foutcodes worden dan weergegeven. U ziet twee foutcodes. De eerste foutcode (**DeploymentFailed**) is een algemene fout die geen informatie biedt die u nodig hebt om het probleem op te lossen. De tweede foutcode (**StorageAccountNotFound**) bevat de details die u nodig hebt.

![foutdetails](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Logboekregistratie voor foutopsporing inschakelen

Soms hebt u meer informatie nodig over de aanvraag en het antwoord om te weten te komen wat er mis is gegaan. Tijdens de implementatie kunt u aanvragen dat aanvullende informatie wordt geregistreerd tijdens een implementatie.

### <a name="powershell"></a>PowerShell

Stel in PowerShell de parameter **DeploymentDebugLogLevel** in op Alle, ResponseContent of RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Bekijk de inhoud van de aanvraag met de volgende cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Of de antwoordinhoud met:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Deze informatie kan u helpen bepalen of een waarde in de sjabloon onjuist is ingesteld.

### <a name="azure-cli"></a>Azure CLI

Momenteel biedt Azure CLI geen ondersteuning voor het inschakelen van logboekregistratie voor foutopsporing, maar u kunt logboekregistratie voor foutopsporing ophalen.

Bekijk de implementatiebewerkingen met de volgende opdracht:

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Bekijk de inhoud van de aanvraag met de volgende opdracht:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Bekijk de inhoud van het antwoord met de volgende opdracht:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Geneste sjabloon

Als u foutopsporingsinformatie voor een geneste sjabloon wilt in een logboek opslaan, gebruikt **u het element debugSetting.**

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

## <a name="create-a-troubleshooting-template"></a>Een sjabloon voor probleemoplossing maken

In sommige gevallen is het testen van delen ervan de eenvoudigste manier om problemen met uw sjabloon op te lossen. U kunt een vereenvoudigde sjabloon maken waarmee u zich kunt richten op het onderdeel dat volgens u de fout veroorzaakt. Stel bijvoorbeeld dat u een foutmelding krijgt wanneer u naar een resource verwijst. In plaats van een volledige sjabloon te gebruiken, maakt u een sjabloon die het onderdeel retourneert dat uw probleem kan veroorzaken. Het kan u helpen om te bepalen of u de juiste parameters door te geven, sjabloonfuncties correct gebruikt en de resource op te vragen die u verwacht.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  "storageName": {
    "type": "string"
  },
  "storageResourceGroup": {
    "type": "string"
  }
  },
  "variables": {},
  "resources": [],
  "outputs": {
  "exampleOutput": {
    "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
    "type" : "object"
  }
  }
}
```

Of stel dat u implementatiefouten krijgt die volgens u zijn gerelateerd aan onjuist ingestelde afhankelijkheden. Test uw sjabloon door deze op te breken in vereenvoudigde sjablonen. Maak eerst een sjabloon die slechts één resource implementeert (zoals een SQL Server). Wanneer u zeker weet dat u die resource correct hebt gedefinieerd, voegt u een resource toe die ervan afhankelijk is (zoals een SQL Database). Wanneer u deze twee resources correct hebt gedefinieerd, voegt u andere afhankelijke resources toe (zoals controlebeleid). Verwijder tussen elke testimplementatie de resourcegroep om ervoor te zorgen dat u de afhankelijkheden op de juiste wijze test.

## <a name="next-steps"></a>Volgende stappen

* Zie Zelfstudie: Problemen met sjabloonimplementaties oplossen voor meer informatie [Resource Manager zelfstudie over](template-tutorial-troubleshoot.md) het oplossen van problemen
* Zie Bewerkingen controleren met Resource Manager voor meer informatie [over controleacties.](../management/view-activity-logs.md)
* Zie Implementatiebewerkingen weergeven voor meer informatie over acties om de fouten tijdens de implementatie [te bepalen.](deployment-history.md)