---
title: Resources verplaatsen naar een nieuw abonnement of een nieuwe resourcegroep
description: Gebruik Azure Resource Manager om resources te verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 04/16/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7a50ecc6081f8fa7c7600ddf1f98a95eceafa73b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784619"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement

In dit artikel wordt beschreven hoe u Azure-resources verplaatst naar een ander Azure-abonnement of een andere resourcegroep onder hetzelfde abonnement. U kunt resources verplaatsen via de Azure-portal, Azure PowerShell, Azure CLI of de REST API.

Zowel de brongroep als de doelgroep worden tijdens de verhuisbewerking vergrendeld. Schrijf- en verwijderingsbewerkingen voor de resourcegroepen worden vergrendeld tot de bewerking is voltooid. Deze vergrendeling betekent dat u geen resources in de resourcegroepen kunt toevoegen, bijwerken of verwijderen. Het betekent niet dat de resources bevroren zijn. Als u bijvoorbeeld een logische server Azure SQL databases naar een nieuwe resourcegroep of een nieuw abonnement verplaatst, is er geen downtime voor toepassingen die gebruikmaken van de databases. Ze kunnen nog steeds lezen en schrijven naar de databases. De vergrendeling kan maximaal vier uur duren, maar de meeste stappen worden in veel minder tijd voltooid.

Als u een resource verplaatst, wordt deze alleen verplaatst naar een nieuwe resourcegroep of nieuw abonnement. Hierdoor wordt de locatie van de resource niet gewijzigd.

## <a name="changed-resource-id"></a>Resource-id gewijzigd

Wanneer u een resource verplaatst, wijzigt u de resource-id. De standaardindeling voor een resource-id is `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}` . Wanneer u een resource naar een nieuwe resourcegroep of een nieuw abonnement verplaatst, wijzigt u een of meer waarden in dat pad.

Als u de resource-id overal gebruikt, moet u die waarde wijzigen. Als u bijvoorbeeld een aangepast [dashboard](../../azure-portal/quickstart-portal-dashboard-azure-cli.md) in de portal hebt dat verwijst naar een resource-id, moet u die waarde bijwerken. Zoek naar scripts of sjablonen die moeten worden bijgewerkt voor de nieuwe resource-id.

## <a name="checklist-before-moving-resources"></a>Controlelijst voordat u de resource verplaatst

Voordat u een resource verplaatst, moeten er enkele belangrijke stappen worden uitgevoerd. U kunt fouten voorkomen door te controleren of aan de volgende voorwaarden is voldaan.

1. De resources die u wilt verplaatsen, moeten de verplaatsbewerking ondersteunen. Zie Ondersteuning voor het verplaatsen van resources voor een lijst met resources die ondersteuning bieden voor [verplaatsen.](move-support-resources.md)

1. Sommige services hebben specifieke beperkingen of vereisten bij het verplaatsen van resources. Als u een van de volgende services verplaatst, controleert u deze richtlijnen voordat u gaat verplaatsen.

   * Als u een Azure Stack Hub, kunt u resources niet verplaatsen tussen groepen.
   * [App Services richtlijnen voor verplaatsen](./move-limitations/app-service-move-limitations.md)
   * [Richtlijnen voor het verplaatsen van Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [Richtlijnen voor het verplaatsen van klassieke implementatiemodellen:](./move-limitations/classic-model-move-limitations.md) klassieke rekenkracht, klassieke opslag, klassieke virtuele netwerken en Cloud Services
   * [Richtlijnen voor het verplaatsen van netwerken](./move-limitations/networking-move-limitations.md)
   * [Richtlijnen voor het verplaatsen van Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Virtual Machines richtlijnen voor verplaatsen](./move-limitations/virtual-machines-move-limitations.md)
   * Zie Abonnementen verplaatsen als u een Azure-abonnement wilt verplaatsen naar [een nieuwe beheergroep.](../../governance/management-groups/manage.md#move-subscriptions)

1. Als u een resource verplaatst aan wie een Azure-rol rechtstreeks is toegewezen aan de resource (of een onderliggende resource), wordt de roltoewijzing niet verplaatst en wordt deze zwevend. Na de verplaatsen moet u de roltoewijzing opnieuw maken. Uiteindelijk wordt de zwevende roltoewijzing automatisch verwijderd, maar we raden u aan de roltoewijzing vóór de verplaatsen te verwijderen.

    Zie Lijst met Azure-roltoewijzingen en [Azure-rollen](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) toewijzen voor meer informatie over het beheren [van roltoewijzingen.](../../role-based-access-control/role-assignments-portal.md)

1. De bron- en doelabonnementen moeten actief zijn. Als u problemen hebt met het inschakelen van een account dat is uitgeschakeld, [maakt u een ondersteuning voor Azure aanvraag](../../azure-portal/supportability/how-to-create-azure-support-request.md). Selecteer **Abonnementsbeheer** als het probleemtype.

1. De bron- en doelabonnementen moeten zich binnen dezelfde [tenant Azure Active Directory.](../../active-directory/develop/quickstart-create-new-tenant.md) Als u wilt controleren of beide abonnementen dezelfde tenant-id hebben, gebruikt u Azure PowerShell of Azure CLI.

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

   Als de tenant-ID's voor de bron- en doelabonnementen niet hetzelfde zijn, gebruikt u de volgende methoden om de tenant-ID's af te stemmen:

   * [Eigendom van een Azure-abonnement naar een ander account overdragen](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [Een Azure-abonnement koppelen of toevoegen aan Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Het doelabonnement moet zijn geregistreerd voor de resourceprovider van de resource die wordt verplaatst. Zo niet, dan ontvangt u een foutbericht met de mededeling **dat het abonnement niet is geregistreerd voor een resourcetype**. Mogelijk ziet u deze fout bij het verplaatsen van een resource naar een nieuw abonnement, maar dat abonnement is nog nooit gebruikt met dat resourcetype.

   Gebruik voor PowerShell de volgende opdrachten om de registratiestatus op te halen:

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   Als u een resourceprovider wilt registreren, gebruikt u:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Gebruik voor Azure CLI de volgende opdrachten om de registratiestatus op te halen:

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   Als u een resourceprovider wilt registreren, gebruikt u:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. Het account dat de resources verplaatst, moet ten minste de volgende machtigingen hebben:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** voor de bronresourcegroep.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** op de doelresourcegroep.

1. Voordat u de resources verplaatst, controleert u de abonnementsquota voor het abonnement waar u de resources naar verplaatst. Als het verplaatsen van de resources betekent dat het abonnement de limieten overschrijdt, moet u controleren of u een verhoging van het quotum kunt aanvragen. Zie Azure subscription [and service limits, quotas, and constraints (Limieten, quota](../../azure-resource-manager/management/azure-subscription-service-limits.md)en beperkingen voor Azure-abonnementen en -service) voor een lijst met limieten en hoe u een verhoging kunt aanvragen.

1. **Voor een overstap tussen abonnementen moeten de resource en de afhankelijke resources zich in dezelfde resourcegroep bevinden en moeten ze samen worden verplaatst.** Een VM met beheerde schijven vereist bijvoorbeeld dat de VM en de beheerde schijven samen worden verplaatst, samen met andere afhankelijke resources.

   Als u een resource naar een nieuw abonnement verplaatst, controleert u of de resource afhankelijke resources heeft en of deze zich in dezelfde resourcegroep bevinden. Als de resources zich niet in dezelfde resourcegroep hebben, controleert u of de resources in dezelfde resourcegroep kunnen worden gecombineerd. Als dat het zo is, brengt u al deze resources naar dezelfde resourcegroep met behulp van een move-bewerking tussen resourcegroepen.

   Zie Scenario voor het verplaatsen [tussen abonnementen voor meer informatie.](#scenario-for-move-across-subscriptions)

## <a name="scenario-for-move-across-subscriptions"></a>Scenario voor het verplaatsen tussen abonnementen

Het verplaatsen van resources van het ene naar het andere abonnement is een proces dat uit drie stappen bestaat:

![scenario voor verplaatsen tussen abonnementen](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

Ter illustratie hebben we slechts één afhankelijke resource.

* Stap 1: als afhankelijke resources worden verdeeld over verschillende resourcegroepen, verplaatst u ze eerst naar één resourcegroep.
* Stap 2: verplaats de resource en afhankelijke resources samen van het bronabonnement naar het doelabonnement.
* Stap 3: distribueren de afhankelijke resources desgewenst opnieuw naar verschillende resourcegroepen binnen het doelabonnement.

## <a name="validate-move"></a>Verplaatsen valideren

Met [de bewerking verplaatsen valideren](/rest/api/resources/resources/moveresources) kunt u uw scenario voor verplaatsen testen zonder de resources daadwerkelijk te verplaatsen. Gebruik deze bewerking om te controleren of de overstap slaagt. Validatie wordt automatisch aangeroepen wanneer u een aanvraag voor verplaatsen verzendt. Gebruik deze bewerking alleen als u de resultaten vooraf moet bepalen. Als u deze bewerking wilt uitvoeren, hebt u het volgende nodig:

* naam van de bronresourcegroep
* resource-id van de doelresourcegroep
* resource-id van elke resource die moet worden verplaatst
* het [toegangsken](/rest/api/azure/#acquire-an-access-token) voor uw account

Verzend de volgende aanvraag:

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

Met een aanvraag body:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Als de aanvraag correct is opgemaakt, retourneert de bewerking:

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

De 202-statuscode geeft aan dat de validatieaanvraag is geaccepteerd, maar het is nog niet vastgesteld of de bewerking wordt uitgevoerd. De `location` waarde bevat een URL die u gebruikt om de status van de langlopende bewerking te controleren.

Verzend de volgende aanvraag om de status te controleren:

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

Terwijl de bewerking nog steeds wordt uitgevoerd, blijft u de 202-statuscode ontvangen. Wacht het aantal seconden dat is aangegeven in de `retry-after` waarde voordat u het opnieuw probeert. Als de bewerking voor verplaatsen wordt gevalideerd, ontvangt u de statuscode 204. Als de validatie van de verplaatsen mislukt, ontvangt u een foutbericht, zoals:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>Gebruik de portal

Als u resources wilt verplaatsen, selecteert u de resourcegroep die deze resources bevat.

Wanneer u de resourcegroep bekijkt, wordt de optie verplaatsen uitgeschakeld.

:::image type="content" source="./media/move-resource-group-and-subscription/move-first-view.png" alt-text="optie verplaatsen uitgeschakeld":::

Als u de optie verplaatsen wilt inschakelen, selecteert u de resources die u wilt verplaatsen. Als u alle resources wilt selecteren, selecteert u het selectievakje bovenaan de lijst. U kunt ook resources afzonderlijk selecteren. Nadat u resources selecteert, wordt de optie verplaatsen ingeschakeld.

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources.png" alt-text="resources selecteren":::

Selecteer de **knop** Verplaatsen.

:::image type="content" source="./media/move-resource-group-and-subscription/move-options.png" alt-text="opties voor verplaatsen":::

Met deze knop hebt u drie opties:

* Ga naar een nieuwe resourcegroep.
* Ga naar een nieuw abonnement.
* Verplaatsen naar een nieuwe regio. Zie Resources verplaatsen tussen regio's [(vanuit resourcegroep) om regio's te wijzigen.](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json)

Selecteer of u de resources verplaatst naar een nieuwe resourcegroep of een nieuw abonnement.

Selecteer de doelresourcegroep. Bevestig dat u scripts voor deze resources moet bijwerken en selecteer **OK.** Als u ervoor hebt gekozen om over te gaan naar een nieuw abonnement, moet u ook het doelabonnement selecteren.

:::image type="content" source="./media/move-resource-group-and-subscription/move-destination.png" alt-text="doel selecteren":::

Nadat u hebt valideren dat de resources kunnen worden verplaatst, ziet u een melding dat de bewerking voor verplaatsen wordt uitgevoerd.

:::image type="content" source="./media/move-resource-group-and-subscription/move-notification.png" alt-text="Kennisgeving":::

Wanneer dit is voltooid, wordt u op de hoogte gesteld van het resultaat.

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resourcegroep of een ander abonnement, gebruikt [u de opdracht Move-AzResource.](/powershell/module/az.resources/move-azresource) In het volgende voorbeeld ziet u hoe u verschillende resources naar een nieuwe resourcegroep verplaatst.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Als u wilt over naar een nieuw abonnement, moet u een waarde voor de `DestinationSubscriptionId` parameter opnemen.

## <a name="use-azure-cli"></a>Azure CLI gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resourcegroep of een ander abonnement, gebruikt u [de opdracht az resource move.](/cli/azure/resource#az_resource_move) Geef de resource-ID's op van de resources die moeten worden verplaatst. In het volgende voorbeeld ziet u hoe u verschillende resources naar een nieuwe resourcegroep verplaatst. Geef in `--ids` de parameter een door spatie gescheiden lijst op van de resource-ID's die moeten worden verplaatst.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Geef de parameter op om over te gaan naar een `--destination-subscription-id` nieuw abonnement.

## <a name="use-rest-api"></a>REST API gebruiken

Als u bestaande resources wilt verplaatsen naar een andere resourcegroep of een ander abonnement, gebruikt [u de bewerking Resources](/rest/api/resources/resources/moveresources) verplaatsen.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

In de aanvraag body geeft u de doelresourcegroep en de resources op die moeten worden verplaatst.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**Vraag: Mijn bewerking voor het verplaatsen van resources, die meestal enkele minuten duurt, is bijna een uur actief. Is er iets mis?**

Het verplaatsen van een resource is een complexe bewerking met verschillende fasen. Het kan meer omvatten dan alleen de resourceprovider van de resource die u wilt verplaatsen. Vanwege de afhankelijkheden tussen resourceproviders kan Azure Resource Manager vier uur duren voordat de bewerking is voltooid. Deze periode biedt resourceproviders de mogelijkheid om te herstellen van tijdelijke problemen. Als uw aanvraag voor verplaatsen binnen de periode van vier uur valt, blijft de bewerking proberen te voltooien en kan deze nog steeds slagen. De bron- en doelresourcegroepen zijn gedurende deze tijd vergrendeld om consistentieproblemen te voorkomen.

**Vraag: Waarom is mijn resourcegroep vier uur vergrendeld tijdens het verplaatsen van resources?**

Een aanvraag voor verplaatsen mag maximaal vier uur worden voltooid. Om te voorkomen dat wijzigingen in de resources worden verplaatst, worden zowel de bron- als doelresourcegroepen tijdens het verplaatsen van de resource vergrendeld.

Een aanvraag voor verplaatsen bestaat uit twee fasen. In de eerste fase wordt de resource verplaatst. In de tweede fase worden meldingen verzonden naar andere resourceproviders die afhankelijk zijn van de resource die wordt verplaatst. Een resourcegroep kan vier uur worden vergrendeld wanneer een resourceprovider een van beide fasen uitvalt. Tijdens de toegestane tijd wordt Resource Manager mislukte stap opnieuw proberen uit te proberen.

Als een resource niet binnen vier uur kan worden verplaatst, Resource Manager beide resourcegroepen ontgrendeld. Resources die zijn verplaatst, staan in de doelresourcegroep. Resources die niet zijn verplaatst, verlaten de bronresourcegroep.

**Vraag: Wat zijn de gevolgen van de bron- en doelresourcegroepen die tijdens het verplaatsen van de resource worden vergrendeld?**

Met de vergrendeling voorkomt u dat u een resourcegroep kunt verwijderen, een nieuwe resource in een resourcegroep kunt maken of een van de resources kunt verwijderen die bij de verplaatsen betrokken zijn.

In de volgende afbeelding ziet u een foutbericht van de Azure Portal wanneer een gebruiker probeert een resourcegroep te verwijderen die deel uitmaakt van een doorlopende move.

![Foutbericht verplaatsen dat wordt verwijderd](./media/move-resource-group-and-subscription/move-error-delete.png)

**Vraag: Wat betekent de foutcode MissingMoveDependentResources?**

Bij het verplaatsen van een resource moeten de afhankelijke resources bestaan in de doelresourcegroep of het abonnement, of worden opgenomen in de aanvraag voor verplaatsen. U krijgt de missingMoveDependentResources-foutcode wanneer een afhankelijke resource niet aan deze vereiste voldoet. Het foutbericht bevat details over de afhankelijke resource die moet worden opgenomen in de aanvraag voor verplaatsen.

Voor het verplaatsen van een virtuele machine kunnen bijvoorbeeld zeven resourcetypen met drie verschillende resourceproviders worden verplaatst. Deze resourceproviders en -typen zijn:

* Microsoft.Compute

  * virtualMachines
  * Schijven
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

Een ander veelvoorkomende voorbeeld is het verplaatsen van een virtueel netwerk. Mogelijk moet u verschillende andere resources verplaatsen die aan dat virtuele netwerk zijn gekoppeld. Voor de aanvraag voor verplaatsen moeten mogelijk openbare IP-adressen, routetabellen, virtuele netwerkgateways, netwerkbeveiligingsgroepen en andere worden verplaatst.

**Vraag: Waarom kan ik sommige resources niet verplaatsen in Azure?**

Momenteel zijn niet alle resources in ondersteuning voor Azure verplaatst. Zie Ondersteuning voor het verplaatsen van resources voor een lijst met resources die ondersteuning bieden voor [verplaatsen.](move-support-resources.md)

**Vraag: Hoeveel resources kan ik verplaatsen in één bewerking?**

Indien mogelijk, kunt u grote bewerkingen in afzonderlijke verplaatsbewerkingen opbreiden. Resource Manager retourneert onmiddellijk een fout wanneer er meer dan 800 resources in één bewerking zijn. Het verplaatsen van minder dan 800 resources kan echter ook mislukken door een time-out.

**Vraag: Wat is de betekenis van de fout dat een resource niet de status Geslaagd heeft?**

Wanneer u een foutbericht krijgt dat aangeeft dat een resource niet kan worden verplaatst omdat deze niet de status Geslaagd heeft, kan het in werkelijkheid een afhankelijke resource zijn die de verplaatsen blokkeert. Normaal gesproken is de foutcode **MoveCannotProceedWithResourcesNotInSucceededState.**

Als de bron- of doelresourcegroep een virtueel netwerk bevat, worden de staten van alle afhankelijke resources voor het virtuele netwerk gecontroleerd tijdens het verplaatsen. De controle omvat deze resources direct en indirect afhankelijk van het virtuele netwerk. Als een van deze resources de status Mislukt heeft, wordt de verplaatsen geblokkeerd. Als een virtuele machine die gebruikmaakt van het virtuele netwerk bijvoorbeeld is mislukt, wordt de overstap geblokkeerd. De overstap wordt geblokkeerd, zelfs wanneer de virtuele machine niet een van de resources is die wordt verplaatst en zich niet in een van de resourcegroepen voor de verplaatsen.

Wanneer u deze foutmelding ontvangt, hebt u twee opties. Verplaats uw resources naar een resourcegroep die geen virtueel netwerk heeft of [neem contact op met de ondersteuning](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Volgende stappen

Zie Ondersteuning voor het verplaatsen van resources voor een lijst met resources die ondersteuning bieden voor [verplaatsen.](move-support-resources.md)
