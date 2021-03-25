---
title: Resources verplaatsen naar een nieuw abonnement of een nieuwe resource groep
description: Gebruik Azure Resource Manager om resources te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 31710354d39c5c74fcbd3ce1bfb2917d79dfd670
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108635"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement

In dit artikel wordt beschreven hoe u Azure-resources verplaatst naar een ander Azure-abonnement of een andere resource groep onder hetzelfde abonnement. U kunt resources verplaatsen via de Azure-portal, Azure PowerShell, Azure CLI of de REST API.

Zowel de bron groep als de doel groep worden tijdens de verplaatsings bewerking vergrendeld. Schrijf- en verwijderingsbewerkingen voor de resourcegroepen worden vergrendeld tot de bewerking is voltooid. Deze vergren deling betekent dat u geen resources in de resource groepen kunt toevoegen, bijwerken of verwijderen. Dit betekent niet dat de resources zijn geblokkeerd. Als u bijvoorbeeld een logische Azure SQL-Server en de bijbehorende data bases verplaatst naar een nieuwe resource groep of een nieuw abonnement, hebben toepassingen die gebruikmaken van de data bases geen downtime. Ze kunnen nog steeds lezen en schrijven naar de data bases. De vergren deling kan Maxi maal vier uur duren, maar de meeste verplaatsingen worden veel minder tijd in beslag.

Als u een resource verplaatst, wordt deze alleen verplaatst naar een nieuwe resourcegroep of nieuw abonnement. Hierdoor wordt de locatie van de resource niet gewijzigd.

## <a name="changed-resource-id"></a>Resource-ID gewijzigd

Wanneer u een resource verplaatst, wijzigt u de resource-ID. De standaard indeling voor een resource-ID is `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}` . Wanneer u een resource naar een nieuwe resource groep of een nieuw abonnement verplaatst, wijzigt u een of meer waarden in dat pad.

Als u de resource-ID overal gebruikt, moet u deze waarde wijzigen. Als u bijvoorbeeld een [aangepast dash board](../../azure-portal/quickstart-portal-dashboard-azure-cli.md) in de portal hebt dat verwijst naar een resource-id, moet u die waarde bijwerken. Zoek naar scripts of sjablonen die moeten worden bijgewerkt voor de nieuwe resource-ID.

## <a name="checklist-before-moving-resources"></a>Controlelijst voordat u de resource verplaatst

Voordat u een resource verplaatst, moeten er enkele belangrijke stappen worden uitgevoerd. U kunt fouten voorkomen door te controleren of aan de volgende voorwaarden is voldaan.

1. De resources die u wilt verplaatsen, moeten de verplaatsings bewerking ondersteunen. Zie [ondersteuning voor het verplaatsen van resources voor bronnen](move-support-resources.md)voor een lijst met resources die kunnen worden verplaatst.

1. Sommige services hebben specifieke beperkingen of vereisten bij het verplaatsen van resources. Als u een van de volgende services wilt verplaatsen, controleert u of de richt lijnen voorafgaand aan het verplaatsen.

   * Als u Azure Stack hub gebruikt, kunt u geen resources verplaatsen tussen groepen.
   * [Hulp App Services verplaatsen](./move-limitations/app-service-move-limitations.md)
   * [Richt lijnen voor het verplaatsen van Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Klassiek implementatie model richt lijnen](./move-limitations/classic-model-move-limitations.md) voor het verplaatsen van klassieke compute, klassieke opslag, klassieke virtuele netwerken en Cloud Services
   * [Richt lijnen voor netwerk verplaatsing](./move-limitations/networking-move-limitations.md)
   * [Hulp Recovery Services verplaatsen](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Hulp Virtual Machines verplaatsen](./move-limitations/virtual-machines-move-limitations.md)
   * Zie [abonnementen verplaatsen](../../governance/management-groups/manage.md#move-subscriptions)voor informatie over het verplaatsen van een Azure-abonnement naar een nieuwe beheer groep.

1. Als u een resource verplaatst waaraan een Azure-rol rechtstreeks is toegewezen aan de resource (of een onderliggende resource), wordt de roltoewijzing niet verplaatst en wordt deze zwevend. Nadat de verplaatsing is verplaatst, moet u de roltoewijzing opnieuw maken. Uiteindelijk wordt de zwevende roltoewijzing automatisch verwijderd, maar we raden u aan de roltoewijzing vóór de verplaatsing te verwijderen.

    Zie [Azure-roltoewijzingen weer geven](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) en [Azure-rollen toewijzen](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het beheren van roltoewijzingen.

1. De bron-en doel abonnementen moeten actief zijn. Als u problemen ondervindt bij het inschakelen van een uitgeschakeld account, [maakt u een ondersteunings aanvraag voor Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecteer **abonnements beheer** voor het type probleem.

1. De bron-en doel abonnementen moeten zich in dezelfde [Azure Active Directory Tenant](../../active-directory/develop/quickstart-create-new-tenant.md)bevinden. Gebruik Azure PowerShell of Azure CLI om te controleren of beide abonnementen dezelfde Tenant-ID hebben.

   Voor Azure PowerShell gebruikt u:

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Gebruik voor Azure CLI:

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   Als de Tenant-Id's voor de bron-en doel abonnementen niet hetzelfde zijn, gebruikt u de volgende methoden voor het afstemmen van de Tenant-Id's:

   * [Eigendom van een Azure-abonnement naar een ander account overdragen](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [Een Azure-abonnement koppelen of toevoegen aan Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Het doelabonnement moet zijn geregistreerd voor de resourceprovider van de resource die wordt verplaatst. Als dat niet het geval is, wordt er een fout bericht weer gegeven met de mede deling dat het **abonnement niet is geregistreerd voor een resource type**. Mogelijk ziet u deze fout wanneer u een resource verplaatst naar een nieuw abonnement, maar dat abonnement nooit is gebruikt voor dat resource type.

   Gebruik voor Power shell de volgende opdrachten om de registratie status op te halen:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Als u een resource provider wilt registreren, gebruikt u:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Gebruik voor Azure CLI de volgende opdrachten om de registratie status op te halen:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Als u een resource provider wilt registreren, gebruikt u:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Het account dat de resources verplaatst, moet ten minste de volgende machtigingen hebben:

   * **Micro soft. resources/abonnementen/resourceGroups/moveResources/actie** voor de bron resource groep.
   * **Micro soft. resources/abonnementen/resourceGroups/schrijven** voor de doel resource groep.

1. Controleer voordat u de resources verplaatst de abonnements quota's voor het abonnement waarnaar u de resources verplaatst. Als het verplaatsen van de resources betekent dat het abonnement groter is dan de limieten, moet u controleren of u een toename van het quotum kunt aanvragen. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../../azure-resource-manager/management/azure-subscription-service-limits.md)voor een lijst met limieten en het aanvragen van een verhoging.

1. **Voor een verplaatsing tussen abonnementen moeten de resource en de afhankelijke resources zich in dezelfde resource groep bevinden en moeten ze samen worden verplaatst.** Een virtuele machine met Managed disks vereist bijvoorbeeld dat de virtuele machine en de beheerde schijven samen worden verplaatst, samen met andere afhankelijke bronnen.

   Als u een resource naar een nieuw abonnement verplaatst, controleert u of de resource afhankelijke resources heeft en of ze zich in dezelfde resource groep bevinden. Als de resources zich niet in dezelfde resource groep bevinden, controleert u of de resources kunnen worden gecombineerd in dezelfde resource groep. Als dit het geval is, kunt u al deze resources in dezelfde resource groep plaatsen met behulp van een Verplaats bewerking tussen resource groepen.

   Zie [scenario voor verplaatsen tussen abonnementen](#scenario-for-move-across-subscriptions)voor meer informatie.

## <a name="scenario-for-move-across-subscriptions"></a>Scenario voor het verplaatsen tussen abonnementen

Het verplaatsen van resources van het ene naar het andere abonnement is een proces dat uit drie stappen bestaat:

![scenario voor het verplaatsen van meerdere abonnementen](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Voor illustratie doeleinden hebben we slechts één afhankelijke resource.

* Stap 1: als afhankelijke resources worden gedistribueerd over verschillende resource groepen, moet u deze eerst verplaatsen naar één resource groep.
* Stap 2: Verplaats de resource en afhankelijke resources samen van het bron abonnement naar het doel abonnement.
* Stap 3: de afhankelijke resources eventueel opnieuw distribueren naar verschillende resource groepen binnen het doel abonnement.

## <a name="validate-move"></a>Verplaatsing valideren

Met de [bewerking verplaatsing valideren](/rest/api/resources/resources/validatemoveresources) kunt u het verplaatsings scenario testen zonder de resources daad werkelijk te verplaatsen. Gebruik deze bewerking om te controleren of de verplaatsing slaagt. Validatie wordt automatisch aangeroepen wanneer u een verplaatsings aanvraag verzendt. Gebruik deze bewerking alleen als u de resultaten vooraf moet bepalen. Als u deze bewerking wilt uitvoeren, hebt u het volgende nodig:

* naam van de bron resource groep
* Resource-ID van de doel resource groep
* Resource-ID van elke resource die moet worden verplaatst
* het [toegangs token](/rest/api/azure/#acquire-an-access-token) voor uw account

Verzend de volgende aanvraag:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Met een aanvraag tekst:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Als de aanvraag correct is ingedeeld, wordt de bewerking als resultaat gegeven:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

De 202-status code geeft aan dat de validatie aanvraag is geaccepteerd, maar nog niet is vastgesteld of de verplaatsings bewerking slaagt. De `location` waarde bevat een URL die u gebruikt om de status van de langlopende bewerking te controleren.  

Als u de status wilt controleren, verzendt u de volgende aanvraag:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Terwijl de bewerking nog steeds wordt uitgevoerd, blijft de 202-status code ontvangen. Wacht het aantal seconden dat is opgegeven in de `retry-after` waarde voordat u het opnieuw probeert. Als de verplaatsings bewerking correct wordt gevalideerd, ontvangt u de 204-status code. Als de verplaatsing niet kan worden gevalideerd, wordt er een fout bericht weer gegeven, zoals:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Gebruik de portal

Als u resources wilt verplaatsen, selecteert u de resource groep die de resources bevat.

Wanneer u de resource groep bekijkt, is de optie verplaatsen uitgeschakeld.

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="optie voor verplaatsen is uitgeschakeld":::

Als u de optie verplaatsen wilt inschakelen, selecteert u de resources die u wilt verplaatsen. Als u alle resources wilt selecteren, schakelt u het selectie vakje boven aan de lijst in. U kunt ook afzonderlijke resources selecteren. Na het selecteren van resources is de optie verplaatsen ingeschakeld.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="resources selecteren":::

Selecteer de knop **verplaatsen** .

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="opties verplaatsen":::

Deze knop biedt u drie opties:

* Verplaatsen naar een nieuwe resource groep.
* Ga naar een nieuw abonnement.
* Verplaatsen naar een nieuwe regio. Zie [resources verplaatsen tussen regio's (van resource groep)](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json)om regio's te wijzigen.

Selecteer of u de resources verplaatst naar een nieuwe resource groep of een nieuw abonnement.

Selecteer de doel resource groep. Bevestig dat u scripts voor deze resources moet bijwerken en selecteer **OK**. Als u hebt geselecteerd om naar een nieuw abonnement te gaan, moet u ook het doel abonnement selecteren.

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="doel selecteren":::

Nadat u hebt gecontroleerd of de resources kunnen worden verplaatst, ziet u een melding dat de verplaatsings bewerking wordt uitgevoerd.

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="melding":::

Wanneer deze is voltooid, ontvangt u een melding van het resultaat.

Als er een fout optreedt, raadpleegt u [problemen met het verplaatsen van Azure-resources naar een nieuwe resource groep of een nieuw abonnement](troubleshoot-move.md).

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resource groep of een ander abonnement, gebruikt u de opdracht [Move-AzResource](/powershell/module/az.resources/move-azresource) . In het volgende voor beeld ziet u hoe u verschillende resources kunt verplaatsen naar een nieuwe resource groep.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Als u wilt overstappen op een nieuw abonnement, neemt u een waarde op voor de `DestinationSubscriptionId` para meter.

Als er een fout optreedt, raadpleegt u [problemen met het verplaatsen van Azure-resources naar een nieuwe resource groep of een nieuw abonnement](troubleshoot-move.md).

## <a name="use-azure-cli"></a>Azure CLI gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resource groep of een ander abonnement, gebruikt u de opdracht [AZ resource Move](/cli/azure/resource#az-resource-move) . Geef de resource-Id's op van de resources die moeten worden verplaatst. In het volgende voor beeld ziet u hoe u verschillende resources kunt verplaatsen naar een nieuwe resource groep. Geef in de `--ids` para meter een door spaties gescheiden lijst op met de resource-id's die u wilt verplaatsen.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Als u wilt overstappen op een nieuw abonnement, geeft u de `--destination-subscription-id` para meter op.

Als er een fout optreedt, raadpleegt u [problemen met het verplaatsen van Azure-resources naar een nieuwe resource groep of een nieuw abonnement](troubleshoot-move.md).

## <a name="use-rest-api"></a>REST API gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resource groep of een ander abonnement, gebruikt u de bewerking [resources verplaatsen](/rest/api/resources/Resources/MoveResources) .

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

In de hoofd tekst van de aanvraag geeft u de doel resource groep en de resources op die u wilt verplaatsen.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Als er een fout optreedt, raadpleegt u [problemen met het verplaatsen van Azure-resources naar een nieuwe resource groep of een nieuw abonnement](troubleshoot-move.md).

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Vraag: de bewerking voor het verplaatsen van resources, die meestal een paar minuten duurt, is bijna een uur actief. Is er iets mis?**

Het verplaatsen van een resource is een complexe bewerking met verschillende fasen. Dit kan meer inhouden dan alleen de resource provider van de resource die u wilt verplaatsen. Vanwege de afhankelijkheden tussen resource providers, Azure Resource Manager 4 uur toegestaan om de bewerking te volt ooien. Deze tijds periode biedt resource providers de mogelijkheid om te herstellen van tijdelijke problemen. Als uw verplaatsings aanvraag binnen de periode van vier uur valt, wordt geprobeerd de bewerking te volt ooien. Dit kan nog steeds slagen. De bron-en doel resource groepen zijn tijdens deze periode vergrendeld om consistentie problemen te voor komen.

**Vraag: Waarom is mijn resource groep gedurende vier uur vergrendeld tijdens het verplaatsen van de resource?**

Een verplaatsings aanvraag mag Maxi maal vier uur zijn voltooid. Om te voor komen dat de resources worden verplaatst, worden zowel de bron-als de doel resource groep vergrendeld tijdens het verplaatsen van de resource.

Er zijn twee fasen in een verplaatsings aanvraag. In de eerste fase wordt de resource verplaatst. In de tweede fase worden meldingen verzonden naar andere resource providers die afhankelijk zijn van de resource die wordt verplaatst. Een resource groep kan voor de hele vier uur worden vergrendeld wanneer een resource provider niet in een fase werkt. Tijdens de toegestane tijd probeert Resource Manager de mislukte stap opnieuw.

Als een resource niet binnen vier uur kan worden verplaatst, ontgrendelt Resource Manager beide resource groepen. Resources die zijn verplaatst, bevinden zich in de doel resource groep. Resources die niet kunnen worden verplaatst, blijven de bron resource groep.

**Vraag: wat zijn de gevolgen van de bron-en doel resource groepen die worden vergrendeld tijdens het verplaatsen van de resource?**

Met de vergren deling wordt voor komen dat u een resource groep verwijdert, een nieuwe resource maakt in een van de resource groepen of een van de resources verwijdert die bij de verplaatsing betrokken zijn.

In de volgende afbeelding ziet u een fout bericht van de Azure Portal wanneer een gebruiker een resource groep probeert te verwijderen die deel uitmaakt van een doorlopende verplaatsing.

![Fout bericht bij poging tot verwijderen verplaatsen](./media/move-resource-group-and-subscription/move-error-delete.png)

**Vraag: wat betekent de fout code ' MissingMoveDependentResources '?**

Bij het verplaatsen van een resource moeten de afhankelijke resources bestaan in de doel resource groep of het abonnement of worden opgenomen in de verplaatsings aanvraag. U krijgt de fout code MissingMoveDependentResources wanneer een afhankelijke resource niet aan deze vereiste voldoet. Het fout bericht bevat details over de afhankelijke resource die moet worden opgenomen in de verplaatsings aanvraag.

Als u bijvoorbeeld een virtuele machine verplaatst, moet u zeven resource typen verplaatsen met drie verschillende resource providers. De resource providers en-typen zijn:

* Microsoft.Compute

  * Informatie
  * cd's
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * Storage accounts

Een ander algemeen voor beeld is het verplaatsen van een virtueel netwerk. Mogelijk moet u enkele andere bronnen verplaatsen die aan het virtuele netwerk zijn gekoppeld. De verplaatsings aanvraag kan het verplaatsen van open bare IP-adressen, route tabellen, virtuele netwerk gateways, netwerk beveiligings groepen en andere vereisen.

**Vraag: Waarom kan ik sommige resources niet verplaatsen in azure?**

Momenteel kunnen niet alle resources in azure-ondersteuning worden verplaatst. Zie [ondersteuning voor het verplaatsen van resources voor bronnen](move-support-resources.md)voor een lijst met resources die kunnen worden verplaatst.

## <a name="next-steps"></a>Volgende stappen

Zie [ondersteuning voor het verplaatsen van resources voor bronnen](move-support-resources.md)voor een lijst met resources die kunnen worden verplaatst.
