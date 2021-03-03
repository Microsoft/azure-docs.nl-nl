---
title: Een beheerde identiteit inschakelen voor routerings gebeurtenissen (preview)-CLI
titleSuffix: Azure Digital Twins
description: Zie een door het systeem toegewezen identiteit inschakelen voor Azure Digital Apparaatdubbels en deze gebruiken om gebeurtenissen door te sturen met behulp van de Azure CLI.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 035d782321feb5d467638159fc191f65573b1042
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101716122"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-cli"></a>Een beheerde identiteit inschakelen voor het routeren van Azure Digital Apparaatdubbels-gebeurtenissen (preview): Azure CLI

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

In dit artikel wordt beschreven hoe u een door het [systeem toegewezen identiteit kunt inschakelen voor een Azure Digital apparaatdubbels-exemplaar](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (momenteel in de preview-versie) en hoe u de identiteit gebruikt bij het door sturen van gebeurtenissen naar ondersteunde doelen, zoals [Event hub](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   doelen en [Azure storage container](../storage/blobs/storage-blobs-introduction.md).

Dit artikel begeleidt u bij het proces met behulp van de [**Azure cli**](/cli/azure/what-is-azure-cli).

Hier volgen de stappen die in dit artikel worden behandeld: 

1. Een Azure Digital Apparaatdubbels-exemplaar maken met een door het systeem toegewezen identiteit of door het systeem toegewezen identiteit in te scha kelen voor een bestaand exemplaar van Azure Digital Apparaatdubbels. 
1. Een geschikte rol of rollen toevoegen aan de identiteit. Wijs bijvoorbeeld de rol **Azure Event hub data Sender** toe aan de identiteit als het eind punt Event hub is of **Azure service bus rol gegevens afzender** als het eind punt service bus is.
1. Een eind punt maken in azure Digital Apparaatdubbels dat door het systeem toegewezen identiteiten kan gebruiken voor verificatie.

## <a name="enable-system-managed-identities-for-an-instance"></a>Door systeem beheerde identiteiten inschakelen voor een exemplaar 

Wanneer u een door het systeem toegewezen identiteit inschakelt voor uw Azure Digital Apparaatdubbels-exemplaar, wordt er in azure automatisch een identiteit voor gemaakt in [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Deze identiteit kan vervolgens worden gebruikt om te verifiëren bij Azure Digital Apparaatdubbels-eind punten voor het door sturen van gebeurtenissen.

U kunt door het systeem beheerde identiteiten inschakelen voor een Azure Digital Apparaatdubbels-exemplaar **als onderdeel van de eerste installatie van het exemplaar**, of **het later inschakelen voor een exemplaar dat al bestaat**.

Een van deze aanmaak methoden biedt dezelfde configuratie opties en hetzelfde eind resultaat voor uw exemplaar. In deze sectie wordt beschreven hoe u dit doet.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Een door een systeem beheerde identiteit toevoegen tijdens het maken van een exemplaar

In deze sectie leert u hoe u een door een systeem beheerde identiteit inschakelt op een Azure Digital Apparaatdubbels-exemplaar dat momenteel wordt gemaakt. 

Dit doet u door een `--assign-identity` para meter toe te voegen aan de `az dt create` opdracht die wordt gebruikt om het exemplaar te maken. (Zie de [documentatie](/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_create) of [algemene instructies voor het instellen van een Azure Digital apparaatdubbels-instantie](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)voor meer informatie over deze opdracht.

Als u een exemplaar met een door het systeem beheerde identiteit wilt maken, voegt u de  `--assign-identity` para meter als volgt toe:

```azurecli-interactive
az dt create -n {new_instance_name} -g {resource_group} --assign-identity
```

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Een door een systeem beheerde identiteit toevoegen aan een bestaand exemplaar

In deze sectie voegt u een door het systeem beheerde identiteit toe aan een Azure Digital Apparaatdubbels-exemplaar dat al bestaat.

Dit gebeurt ook met de `az dt create` opdracht en de `--assign-identity` para meter. In plaats van een nieuwe naam op te geven van een exemplaar, kunt u de naam opgeven van een exemplaar dat al bestaat om de waarde van `--assign-identity` voor die instantie bij te werken.

De opdracht voor het **inschakelen** van beheerde identiteit is hetzelfde als de opdracht voor het maken van een exemplaar met een door het systeem beheerde identiteit. Alle wijzigingen zijn de waarde van de para meter exemplaar naam:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity
```

Als u de beheerde identiteit wilt **uitschakelen** voor een exemplaar waarop deze momenteel is ingeschakeld, gebruikt u de volgende vergelijk bare opdracht om in te stellen op `--assign-identity` `false` .

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --assign-identity false
```

## <a name="assign-azure-roles-to-the-identity"></a>Azure-rollen toewijzen aan de identiteit 

Zodra een door het systeem toegewezen identiteit is gemaakt voor uw Azure Digital Apparaatdubbels-exemplaar, moet u de juiste rollen toewijzen voor verificatie met verschillende typen [eind punten](concepts-route-events.md) voor het door sturen van gebeurtenissen naar ondersteunde bestemmingen. In deze sectie worden de opties voor de functie beschreven en wordt uitgelegd hoe u deze toewijst aan de door het systeem toegewezen identiteit.

>[!NOTE]
> Dit is een belang rijke stap, zonder IT, de identiteit heeft geen toegang tot uw eind punten en gebeurtenissen worden niet bezorgd.

### <a name="supported-destinations-and-azure-roles"></a>Ondersteunde doelen en Azure-rollen 

Hier volgen de minimale rollen die een identiteit nodig heeft voor toegang tot een eind punt, afhankelijk van het type bestemming. Rollen met hogere machtigingen (zoals rollen van de eigenaar van gegevens) werken ook.

| Doel | Azure-rol |
| --- | --- |
| Azure Event Hubs | Afzender van Azure Event Hubs gegevens |
| Azure Service Bus | Afzender van Azure Service Bus gegevens |
| Azure storage-container | Inzender voor Storage Blob-gegevens |

Zie voor meer informatie over eind punten, routes en de typen bestemmingen die worden ondersteund voor route ring in azure Digital Apparaatdubbels de [*concepten: gebeurtenis routes*](concepts-route-events.md).

### <a name="assign-the-role"></a>De rol toewijzen

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

U kunt de `--scopes` para meter toevoegen aan de `az dt create` opdracht, zodat de identiteit kan worden toegewezen aan een of meer bereiken met een opgegeven rol. Dit kan worden gebruikt bij het maken van het exemplaar of later door door gegeven in de naam van een exemplaar dat al bestaat.

Hier volgt een voor beeld van het maken van een exemplaar met een door het systeem beheerde identiteit en het toewijzen van een aangepaste rol die wordt aangeroepen `MyCustomRole` in een event hub.

```azurecli-interactive
az dt create -n {instance_name} -g {resource_group} --assign-identity --scopes "/subscriptions/<subscription ID>/resourceGroups/<resource_group>/providers/Microsoft.EventHub/namespaces/<Event_Hubs_namespace>/eventhubs/<event_hub_name>" --role MyCustomRole
```

Zie de [naslag documentatie **AZ DT maken**](/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_create)voor meer voor beelden van roltoewijzingen met deze opdracht.

U kunt ook de opdracht [**AZ Role Assignment**](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true) gebruiken om rollen te maken en te beheren. Dit kan worden gebruikt ter ondersteuning van aanvullende scenario's waarin u de roltoewijzing niet wilt groeperen met de opdracht Create.

## <a name="create-an-endpoint-with-identity-based-authentication"></a>Een eind punt maken met verificatie op basis van een identiteit

Nadat u een door een systeem beheerde identiteit hebt ingesteld voor uw Azure Digital Apparaatdubbels-exemplaar en de juiste rol (len) hebt toegewezen, kunt u Azure Digital Apparaatdubbels- [eind punten](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) maken die de identiteit kunnen gebruiken voor verificatie. Deze optie is alleen beschikbaar voor Event hub-en Service Bus-type-eind punten (wordt niet ondersteund voor Event Grid).

>[!NOTE]
> U kunt een eind punt dat al is gemaakt met een op de sleutel gebaseerde identiteit niet bewerken om te wijzigen in verificatie op basis van identiteit. U moet het verificatie type kiezen wanneer het eind punt voor het eerst wordt gemaakt.

Dit doet u door een `--auth-type` para meter toe te voegen aan de `az dt endpoint create` opdracht die wordt gebruikt om het eind punt te maken. (Zie de [documentatie](/cli/azure/ext/azure-iot/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) of [algemene instructies voor het instellen van een Azure Digital apparaatdubbels-eind punt](how-to-manage-routes-apis-cli.md#create-the-endpoint)voor meer informatie over deze opdracht.

Als u een eind punt wilt maken dat gebruikmaakt van verificatie op basis van een identiteit, geeft u het `IdentityBased` verificatie type op met de  `--auth-type` para meter. In het onderstaande voor beeld ziet u dit voor een Event Hubs eind punt.

```azurecli-interactive
az dt endpoint create eventhub --endpoint-name {endpoint_name} --eventhub-resource-group {eventhub_resource_group} --eventhub-namespace {eventhub_namespace} --eventhub {eventhub_name} --auth-type IdentityBased -n {instance_name}
```

## <a name="considerations-for-disabling-system-managed-identities"></a>Overwegingen voor het uitschakelen van door het systeem beheerde identiteiten

Omdat een identiteit afzonderlijk wordt beheerd vanuit de eind punten die deze gebruiken, is het belang rijk om te overwegen wat de gevolgen zijn van wijzigingen aan de identiteit of hun rollen op de eind punten in uw Azure Digital Apparaatdubbels-exemplaar. Als de identiteit is uitgeschakeld of een vereiste rol voor een eind punt wordt verwijderd, kan het eind punt ontoegankelijk worden en wordt de stroom van gebeurtenissen onderbroken.

Als u een eind punt wilt blijven gebruiken dat is ingesteld met een beheerde identiteit die nu is uitgeschakeld, moet u het eind punt verwijderen en [opnieuw maken](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins) met een ander verificatie type. Het kan tot een uur duren voordat gebeurtenissen na deze wijziging naar het eind punt worden hervat.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over beheerde identiteiten in azure AD: 
* [*Beheerde identiteiten voor Azure-resources*](../active-directory/managed-identities-azure-resources/overview.md)