---
title: Veelvoorkomende implementatiefouten oplossen
description: Hierin wordt beschreven hoe u veelvoorkomende fouten oplost wanneer u resources implementeert in azure met behulp van Azure Resource Manager.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 01/20/2021
ms.openlocfilehash: 40e6317a1d879704ef00e928a971ae08fc9e0f72
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564344"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Veelvoorkomende fouten met Azure-implementatie oplossen met Azure Resource Manager

In dit artikel worden enkele veelvoorkomende problemen met Azure-implementatie beschreven en vindt u informatie over het oplossen van de fouten. Zie [Foutcodes zoeken](#find-error-code) als u de foutcode voor uw implementatiefout niet kunt vinden.

Als u op zoek bent naar informatie over een fout code en deze informatie niet in dit artikel wordt verstrekt, laat het ons dan weten. Onder aan deze pagina kunt u feedback geven. De feedback wordt bijgehouden met GitHub-problemen.

## <a name="error-codes"></a>Foutcodes

| Foutcode | Oplossing | Meer informatie |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Volg de naam beperkingen voor opslag accounts. | [Naam van opslag account oplossen](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Controleer de beschik bare eigenschappen van het opslag account. | [Storage accounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Het cluster of de regio heeft geen resources beschikbaar of kan de aangevraagde VM-grootte niet ondersteunen. Voer de aanvraag op een later tijdstip opnieuw uit of vraag een andere VM-grootte aan. | [Problemen met inrichten en toewijzen voor Linux](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux), [inrichting en toewijzing van problemen voor Windows](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) en het [oplossen van toewijzings fouten](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | Wacht tot de gelijktijdige bewerking is voltooid. | |
| AuthorizationFailed | Uw account of Service-Principal heeft onvoldoende toegangs rechten om de implementatie te volt ooien. Controleer de rol waartoe uw account behoort en de toegang tot het implementatie bereik.<br><br>Deze fout kan optreden wanneer een vereiste resource provider niet is geregistreerd. | [Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Registratie oplossen](error-register-resource-provider.md) |
| BadRequest | U hebt implementatie waarden verzonden die niet overeenkomen met wat er wordt verwacht door Resource Manager. Controleer het binnenste status bericht voor hulp bij het oplossen van problemen. | [Sjabloon verwijzing](/azure/templates/) en [ondersteunde locaties](resource-location.md) |
| Conflict | U vraagt een bewerking aan die niet is toegestaan in de huidige status van de resource. Het wijzigen van de grootte van een schijf is bijvoorbeeld alleen toegestaan bij het maken van een virtuele machine of wanneer de toewijzing van de virtuele machine ongedaan wordt gemaakt. | |
| DeploymentActiveAndUneditable | Wacht tot de gelijktijdige implementatie naar deze resource groep is voltooid. | |
| DeploymentFailedCleanUp | Wanneer u in de volledige modus implementeert, worden alle resources die zich niet in de sjabloon bevinden, verwijderd. U krijgt deze fout melding wanneer u niet over de juiste machtigingen beschikt om alle resources te verwijderen die niet in de sjabloon staan. Als u de fout wilt voor komen, wijzigt u de implementatie modus in incrementeel. | [Azure Resource Manager deployment modes](deployment-modes.md) (Implementatiemodi voor Azure Resource Manager) |
| DeploymentNameInvalidCharacters | De implementatie naam mag alleen letter, cijfer, '-', '. ' of ' _ ' bevatten. | |
| DeploymentNameLengthLimitExceeded | De namen van de implementaties zijn beperkt tot 64 tekens.  | |
| Heeft | De heeft-fout is een algemene fout die niet de details biedt die u nodig hebt om de fout op te lossen. Bekijk de fout Details voor een fout code die meer informatie bevat. | [Fout code zoeken](#find-error-code) |
| DeploymentQuotaExceeded | Als u de limiet van 800 implementaties per resource groep bereikt, verwijdert u implementaties uit de geschiedenis die niet meer nodig zijn. | [Fout oplossen wanneer het aantal implementaties groter is dan 800](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | Vereenvoudig uw sjabloon om de grootte te verminderen. | [Fouten in de sjabloon grootte oplossen](error-job-size-exceeded.md) |
| DnsRecordInUse | De naam van de DNS-record moet uniek zijn. Voer een andere naam in. | |
| ImageNotFound | Controleer de instellingen van de VM-installatie kopie. |  |
| InUseSubnetCannotBeDeleted | Deze fout kan optreden wanneer u een resource probeert bij te werken en de aanvraag wordt verwerkt door de resource te verwijderen en te maken. Zorg ervoor dat u alle ongewijzigde waarden opgeeft. | [Bron bijwerken](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Haal het toegangs token voor de juiste Tenant op. U kunt het token alleen ophalen van de Tenant waarvan uw account deel uitmaakt. | |
| InvalidContentLink | U hebt waarschijnlijk geprobeerd een koppeling te maken met een geneste sjabloon die niet beschikbaar is. Controleer de URI die u hebt ingevoerd voor de geneste sjabloon dubbel. Als de sjabloon zich in een opslag account bevindt, controleert u of de URI toegankelijk is. Mogelijk moet u een SAS-token door geven. Op dit moment kunt u geen koppeling maken met een sjabloon die zich in een opslag account bevindt achter een [Azure Storage firewall](../../storage/common/storage-network-security.md). Overweeg om uw sjabloon te verplaatsen naar een andere opslag plaats, zoals GitHub. | [Gekoppelde sjablonen](linked-templates.md) |
| InvalidDeploymentLocation | Wanneer u implementeert op abonnements niveau, hebt u een andere locatie voor een eerder gebruikte implementatie naam ingevoerd. | [Implementaties op abonnements niveau](deploy-to-subscription.md) |
| InvalidParameter | Een van de waarden die u voor een resource hebt opgegeven, komt niet overeen met de verwachte waarde. Deze fout kan worden veroorzaakt door verschillende voor waarden. Zo kan een wacht woord ontoereikend zijn of kan de naam van een BLOB onjuist zijn. In het fout bericht moet worden aangegeven welke waarde moet worden gecorrigeerd. | |
| InvalidRequestContent | De implementatie waarden bevatten waarden die niet worden herkend of de vereiste waarden ontbreken. Bevestig de waarden voor het bron type. | [Sjabloonverwijzing](/azure/templates/) |
| InvalidRequestFormat | Schakel logboek registratie voor fout opsporing in bij het uitvoeren van de implementatie en controleer de inhoud van de aanvraag. | [Logboek registratie van fouten](#enable-debug-logging) |
| InvalidResourceNamespace | Controleer de resource naam ruimte die u hebt opgegeven in de eigenschap **type** . | [Sjabloonverwijzing](/azure/templates/) |
| InvalidResourceReference | De resource bestaat nog niet of heeft niet de juiste verwijzing. Controleer of u een afhankelijkheid moet toevoegen. Controleer of het gebruik van de functie **verwijzing** de vereiste para meters voor uw scenario bevat. | [Afhankelijkheden oplossen](error-not-found.md) |
| InvalidResourceType | Controleer het resource type dat u hebt opgegeven in de eigenschap **type** . | [Sjabloonverwijzing](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registreer uw abonnement bij de resource provider. | [Registratie oplossen](error-register-resource-provider.md) |
| InvalidTemplate | Controleer de syntaxis van de sjabloon op fouten. | [Ongeldige sjabloon oplossen](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Verwijder overbodige afhankelijkheden. | [Circulaire afhankelijkheden oplossen](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Vereenvoudig uw sjabloon om de grootte te verminderen. | [Fouten in de sjabloon grootte oplossen](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Controleer of uw account deel uitmaakt van dezelfde Tenant als de resource groep die u implementeert. | |
| LinkedInvalidPropertyId | De resource-ID voor een resource wordt niet correct opgelost. Controleer of u alle vereiste waarden opgeeft voor de resource-ID, inclusief de abonnements-ID, de naam van de resource groep, het resource type, de naam van de bovenliggende resource (indien nodig) en de resource naam. | |
| LocationRequired | Geef een locatie op voor de resource. | [Locatie instellen](resource-location.md) |
| MismatchingResourceSegments | Zorg ervoor dat geneste resources het juiste aantal segmenten in naam en type hebben. | [Resource segmenten omzetten](error-invalid-template.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Controleer de registratie status van de resource provider en de ondersteunde locaties. | [Registratie oplossen](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Registreer uw abonnement bij de resource provider. | [Registratie oplossen](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Controleer de registratie status van de resource provider. | [Registratie oplossen](error-register-resource-provider.md) |
| NotFound | Mogelijk probeert u een afhankelijke bron parallel te implementeren met een bovenliggende resource. Controleer of u een afhankelijkheid moet toevoegen. | [Afhankelijkheden oplossen](error-not-found.md) |
| OperationNotAllowed | Er wordt geprobeerd een bewerking uit te voeren die het quotum voor het abonnement, de resource groep of de regio overschrijdt. Als dat mogelijk is, kunt u uw implementatie herzien zodat deze binnen de quota's blijft. Als dat niet het geval is, kunt u een wijziging aanvragen voor uw quota's. | [Quota's omzetten](error-resource-quota.md) |
| ParentResourceNotFound | Zorg ervoor dat er een bovenliggende resource bestaat voordat u de onderliggende resources maakt. | [Bovenliggende resource omzetten](error-parent-resource.md) |
| PasswordTooLong | Mogelijk hebt u een wacht woord met te veel tekens geselecteerd of hebt u uw wachtwoord waarde naar een beveiligde teken reeks geconverteerd voordat u deze als een para meter door gegeven. Als de sjabloon een **beveiligde teken reeks** parameter bevat, hoeft u de waarde niet te converteren naar een veilige teken reeks. Geef de wachtwoord waarde op als tekst. |  |
| PrivateIPAddressInReservedRange | Het opgegeven IP-adres bevat een adres bereik dat vereist is voor Azure. Wijzig het IP-adres om gereserveerd bereik te voor komen. | [IP-adressen](../../virtual-network/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | Het opgegeven IP-adres valt buiten het bereik van het subnet. Wijzig het IP-adres in het bereik van het subnet. | [IP-adressen](../../virtual-network/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Sommige eigenschappen kunnen niet worden gewijzigd op een geïmplementeerde resource. Wanneer u een resource bijwerkt, kunt u de wijzigingen beperken tot toegestane eigenschappen. | [Bron bijwerken](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | Uw abonnement bevat een resource beleid waarmee wordt voor komen dat u tijdens de implementatie een actie probeert uit te voeren. Zoek het beleid waarmee de actie wordt geblokkeerd. Wijzig, indien mogelijk, uw implementatie zodat deze voldoet aan de beperkingen van het beleid. | [Beleid oplossen](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Geef een resource naam op die geen gereserveerde naam bevat. | [Gereserveerde resource namen](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Wacht tot de verwijdering is voltooid. | |
| ResourceGroupNotFound | Controleer de naam van de doel resource groep voor de implementatie. De doel resource groep moet al bestaan in uw abonnement. Controleer de context van uw abonnement. | [Azure cli](/cli/azure/account?#az-account-set) [Power shell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Uw implementatie verwijst naar een resource die niet kan worden omgezet. Controleer of het gebruik van de functie **Reference** de para meters bevat die vereist zijn voor uw scenario. | [Verwijzingen oplossen](error-not-found.md) |
| ResourceQuotaExceeded | De implementatie probeert resources te maken die het quotum voor het abonnement, de resource groep of de regio overschrijden. Als dat mogelijk is, kunt u uw infra structuur herzien zodat deze binnen de quota's blijft. Als dat niet het geval is, kunt u een wijziging aanvragen voor uw quota's. | [Quota's omzetten](error-resource-quota.md) |
| SkuNotAvailable | Selecteer SKU (zoals VM-grootte) die beschikbaar is voor de locatie die u hebt geselecteerd. | [SKU oplossen](error-sku-not-available.md) |
| StorageAccountAlreadyExists | Geef een unieke naam op voor het opslagaccount. | [Naam van opslag account oplossen](error-storage-account-name.md)  |
| StorageAccountAlreadyTaken | Geef een unieke naam op voor het opslagaccount. | [Naam van opslag account oplossen](error-storage-account-name.md) |
| StorageAccountNotFound | Controleer het abonnement, de resource groep en de naam van het opslag account dat u wilt gebruiken. | |
| SubnetsNotInSameVnet | Een virtuele machine kan slechts één virtueel netwerk hebben. Zorg er bij het implementeren van verschillende Nic's voor dat deze deel uitmaken van hetzelfde virtuele netwerk. | [Meerdere Nic's](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | Er kan geen toegang worden verkregen tot een opgegeven abonnement voor de implementatie. Het kan zijn dat de abonnements-ID onjuist is. de gebruiker die de sjabloon implementeert, beschikt niet over de vereiste machtigingen om te implementeren voor het abonnement of de abonnements-ID heeft de verkeerde indeling. Wanneer u geneste implementaties gebruikt voor het [implementeren van verschillende bereiken](./deploy-to-resource-group.md), geeft u de GUID voor het abonnement op. | |
| SubscriptionNotRegistered | Bij het implementeren van een resource moet de resource provider zijn geregistreerd voor uw abonnement. Wanneer u een Azure Resource Manager sjabloon voor implementatie gebruikt, wordt de resource provider automatisch geregistreerd in het abonnement. Soms wordt de automatische registratie niet op tijd voltooid. U kunt deze fout voor komen door de resource provider te registreren vóór de implementatie. | [Registratie oplossen](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Verwijder overbodige afhankelijkheden. | [Circulaire afhankelijkheden oplossen](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Verminder het aantal resource groepen voor één implementatie. | [Implementatie op meerdere scopes](./deploy-to-resource-group.md) |

## <a name="find-error-code"></a>Fout code zoeken

U kunt twee typen foutmeldingen krijgen:

* validatiefouten
* implementatiefouten

Validatiefouten ontstaan door scenario's en kunnen vóór de implementatie worden vastgesteld. Ze bevatten syntaxisfouten in de sjabloon of proberen resources te implementeren waarmee uw abonnementquota worden overschreden. Implementatiefouten ontstaan als gevolg van voorwaarden die tijdens het implementatieproces optreden. Bijvoorbeeld wanneer deze toegang proberen te krijgen tot een resource die parallel wordt geïmplementeerd.

Beide typen fouten retourneren een foutcode die u gebruikt om de problemen met de implementatie op te lossen. Beide typen fouten worden weergegeven in het [activiteitenlogboek](../management/view-activity-logs.md). Validatiefouten worden echter niet weergegeven in de implementatiegeschiedenis omdat de implementatie niet is gestart.

### <a name="validation-errors"></a>Validatie fouten

Wanneer u een implementatie uitvoert via de portal, ziet u een validatiefout na het indienen van uw waarden.

![de portal validatie fout weer geven](./media/common-deployment-errors/validation-error.png)

Selecteer het bericht voor meer informatie. In de volgende afbeelding ziet u een **InvalidTemplateDeployment** -fout en een bericht dat aangeeft dat de implementatie van een beleid is geblokkeerd.

![validatie details weer geven](./media/common-deployment-errors/validation-details.png)

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

![meldings fout](./media/common-deployment-errors/notification.png)

U ziet meer informatie over de implementatie. Selecteer de optie voor meer informatie over de fout.

![implementatie is mislukt](./media/common-deployment-errors/deployment-failed.png)

Het foutbericht en de foutcodes worden dan weergegeven. U ziet twee foutcodes. De eerste foutcode (**DeploymentFailed**) is een algemene fout die geen informatie biedt die u nodig hebt om het probleem op te lossen. De tweede foutcode (**StorageAccountNotFound**) bevat de details die u nodig hebt.

![Fout Details](./media/common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Logboek registratie voor fout opsporing inschakelen

Soms hebt u meer informatie nodig over de aanvraag en het antwoord om te ontdekken wat er mis ging. Tijdens de implementatie kunt u aanvragen dat er aanvullende informatie wordt geregistreerd tijdens een implementatie.

### <a name="powershell"></a>PowerShell

Stel in Power shell de para meter **DeploymentDebugLogLevel** in op all, ResponseContent of RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Controleer de inhoud van de aanvraag met de volgende cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

Of de antwoord inhoud met:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Aan de hand van deze informatie kunt u bepalen of een waarde in de sjabloon onjuist is ingesteld.

### <a name="azure-cli"></a>Azure CLI

Momenteel biedt Azure CLI geen ondersteuning voor het inschakelen van logboek registratie voor fout opsporing, maar u kunt logboek registratie voor fout opsporing ophalen.

Controleer de implementatie bewerkingen met de volgende opdracht:

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

Controleer de inhoud van de aanvraag met de volgende opdracht:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Bekijk de antwoord inhoud met de volgende opdracht:

```azurecli
az deployment operation group list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Geneste sjabloon

Als u foutopsporingsinformatie wilt registreren voor een geneste sjabloon, gebruikt u het element **debugSetting** .

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

## <a name="create-a-troubleshooting-template"></a>Een sjabloon voor het oplossen van problemen maken

In sommige gevallen kunt u het beste de eenvoudigste manier om de sjabloon op te lossen door delen ervan te testen. U kunt een vereenvoudigde sjabloon maken die u kunt concentreren op het deel dat de fout veroorzaakt. Stel dat u een fout bericht ontvangt bij het verwijzen naar een resource. In plaats van een hele sjabloon te gebruiken, maakt u een sjabloon die het onderdeel retourneert dat mogelijk de oorzaak van het probleem is. Hiermee kunt u bepalen of u in de juiste para meters gaat, met behulp van sjabloon functies en de verwachte resource ophalen.

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

Of stel dat u implementatie fouten krijgt die u vermoedt dat ze zijn gerelateerd aan onjuist ingestelde afhankelijkheden. Test uw sjabloon door deze te splitsen in vereenvoudigde sjablonen. Maak eerst een sjabloon waarmee slechts één resource (zoals een SQL Server) wordt geïmplementeerd. Als u zeker weet dat de bron correct is gedefinieerd, voegt u een bron toe die hiervan afhankelijk is (zoals een SQL Database). Wanneer u deze twee resources correct hebt gedefinieerd, voegt u andere afhankelijke resources toe (zoals controle beleid). Verwijder in tussen elke test implementatie de resource groep om ervoor te zorgen dat u de afhankelijkheden adequaat kunt testen.

## <a name="next-steps"></a>Volgende stappen

* Zie [zelf studie: problemen met implementaties van Resource Manager-sjablonen oplossen](template-tutorial-troubleshoot.md) voor een zelf studie over probleem oplossing.
* Zie [bewerkingen controleren met Resource Manager](../management/view-activity-logs.md)voor meer informatie over controle acties.
* Zie [implementatie bewerkingen weer geven](deployment-history.md)voor meer informatie over acties om de fouten te bepalen tijdens de implementatie.