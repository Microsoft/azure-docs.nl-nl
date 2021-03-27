---
title: Beheerde identiteit toevoegen aan een rol op Azure Event Grid bestemming
description: In dit artikel wordt beschreven hoe u beheerde identiteit kunt toevoegen aan Azure-rollen op doelen zoals Azure Service Bus en Azure Event Hubs.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1bcef878c982122d80980dd7194fae2de6fc8762
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630383"
---
# <a name="add-an-identity-to-azure-roles-on-azure-event-grid-destinations"></a>Een identiteit toevoegen aan Azure-rollen op Azure Event Grid doelen
In deze sectie wordt beschreven hoe u de identiteit voor uw systeem onderwerp, aangepast onderwerp of domein toevoegt aan een Azure-rol. 

## <a name="prerequisites"></a>Vereisten
Wijs een door het systeem toegewezen beheerde identiteit toe met behulp van instructies uit de volgende artikelen:

- [Aangepaste onderwerpen of domeinen](enable-identity-custom-topics-domains.md)
- [Systeemonderwerpen](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>Ondersteunde doelen en Azure-rollen
Nadat u de identiteit voor het aangepaste onderwerp of domein van het gebeurtenis raster hebt ingeschakeld, maakt Azure automatisch een identiteit in Azure Active Directory. Voeg deze identiteit toe aan de juiste Azure-rollen zodat het aangepaste onderwerp of domein gebeurtenissen naar ondersteunde bestemmingen kan door sturen. U kunt bijvoorbeeld de identiteit toevoegen aan de rol **azure Event hubs data Sender** voor een Azure Event hubs-naam ruimte, zodat het aangepaste onderwerp Event grid gebeurtenissen kan door sturen naar Event hubs in die naam ruimte. 

Op dit moment ondersteunt Azure Event grid aangepaste onderwerpen of domeinen die zijn geconfigureerd met een door het systeem toegewezen beheerde identiteit voor het door sturen van gebeurtenissen naar de volgende bestemmingen. In deze tabel vindt u ook de rollen waarin de identiteit zich moet bevinden, zodat het aangepaste onderwerp de gebeurtenissen kan door sturen.

| Doel | Azure-rol | 
| ----------- | --------- | 
| Service Bus-wacht rijen en-onderwerpen | [Afzender van Azure Service Bus gegevens](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Afzender van Azure Event Hubs gegevens](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Inzender voor Storage Blob-gegevens](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Azure Queue Storage |[Afzender gegevens bericht van opslag wachtrij](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken
U kunt de Azure Portal gebruiken om het aangepaste onderwerp of de domein identiteit toe te wijzen aan een geschikte rol, zodat het aangepaste onderwerp of domein gebeurtenissen naar het doel kan door sturen. 

In het volgende voor beeld wordt een beheerde identiteit voor een aangepast Event grid-onderwerp met de naam **msitesttopic** aan de **Azure service bus data Sender** role toegevoegd voor een service bus naam ruimte die een wachtrij of onderwerp-resource bevat. Wanneer u aan de rol op het niveau van de naam ruimte toevoegt, kan het aangepaste Event grid-onderwerp gebeurtenissen door sturen naar alle entiteiten in de naam ruimte. 

1. Ga naar de **naam ruimte** van uw service bus in de [Azure Portal](https://portal.azure.com). 
1. Selecteer **Access Control** in het linkerdeel venster. 
1. Selecteer **toevoegen** in de sectie **toewijzing van een rol toevoegen** . 
1. Voer op de pagina **een roltoewijzing toevoegen** de volgende stappen uit:
    1. Selecteer de rol. In dit geval is het **Azure Service Bus gegevens verzender**. 
    1. Selecteer de **identiteit** voor uw aangepaste Event grid-onderwerp of-domein. 
    1. Selecteer **Opslaan** om de configuratie op te slaan.

De stappen zijn vergelijkbaar voor het toevoegen van een identiteit aan andere rollen die in de tabel worden genoemd. 

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken
In het voor beeld in deze sectie wordt beschreven hoe u de Azure CLI gebruikt om een identiteit toe te voegen aan een Azure-rol. De voorbeeld opdrachten zijn voor aangepaste Event grid-onderwerpen. De opdrachten voor Event grid-domeinen zijn vergelijkbaar. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>De principal-ID voor de systeem identiteit van het aangepaste onderwerp ophalen 
Haal eerst de principal-ID op van de door het systeem beheerde identiteit van het aangepaste onderwerp en wijs de identiteit toe aan de juiste rollen.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Een roltoewijzing maken voor Event hubs in verschillende bereiken 
In het volgende CLI-voor beeld ziet u hoe u de identiteit van een aangepast onderwerp kunt toevoegen aan de rol van **Azure Event hubs data Sender** op het niveau van de naam ruimte of op Event hub niveau. Als u de roltoewijzing op het niveau van de naam ruimte maakt, kan het aangepaste onderwerp gebeurtenissen door sturen naar alle Event hubs in die naam ruimte. Als u op Event Hub niveau een roltoewijzing maakt, kan het aangepaste onderwerp alleen gebeurtenissen door sturen naar die specifieke Event Hub. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Een roltoewijzing maken voor een Service Bus onderwerp in verschillende bereiken 
In het volgende CLI-voor beeld ziet u hoe u de identiteit van een aangepast Event grid-onderwerp kunt toevoegen aan de rol van **Azure service bus data Sender** op het niveau van de naam ruimte of op het niveau van service bus onderwerp. Als u de roltoewijzing op het niveau van de naam ruimte maakt, kan het event grid-onderwerp gebeurtenissen door sturen naar alle entiteiten (Service Bus wacht rijen of onderwerpen) binnen die naam ruimte. Als u een roltoewijzing maakt op het niveau van de Service Bus wachtrij of het onderwerp, kan het aangepaste onderwerp gebeurtenis raster alleen gebeurtenissen door sturen naar die specifieke Service Bus wachtrij of het onderwerp. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>Volgende stappen
Nu u een door het systeem toegewezen identiteit aan uw systeem onderwerp, aangepast onderwerp of domein hebt toegewezen en de identiteit aan de juiste rollen op bestemmingen hebt toegevoegd, raadpleegt u [Devlier-gebeurtenissen met behulp](managed-service-identity.md) van de identiteit voor het leveren van gebeurtenissen aan bestemmingen met behulp van de identiteit.


