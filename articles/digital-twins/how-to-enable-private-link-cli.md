---
title: Persoonlijke toegang inschakelen met persoonlijke koppeling (preview)-CLI
titleSuffix: Azure Digital Twins
description: Zie persoonlijke toegang inschakelen voor Azure Digital Apparaatdubbels-oplossingen met een persoonlijke koppeling met behulp van de Azure CLI.
author: baanders
ms.author: baanders
ms.date: 02/09/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4dab08983fc1348ca49e728a65d48aa65fe19a47
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105711"
---
# <a name="enable-private-access-with-private-link-preview-azure-cli"></a>Persoonlijke toegang inschakelen met persoonlijke koppeling (preview): Azure CLI

[!INCLUDE [digital-twins-private-link-selector.md](../../includes/digital-twins-private-link-selector.md)]

In dit artikel worden de verschillende manieren beschreven om [persoonlijke koppelingen in te scha kelen met een persoonlijk eind punt voor een Azure Digital apparaatdubbels-exemplaar](concepts-security.md#private-network-access-with-azure-private-link-preview) (momenteel in preview-versie). Als u een persoonlijk eind punt voor uw Azure Digital Apparaatdubbels-exemplaar configureert, kunt u uw Azure Digital Apparaatdubbels-exemplaar beveiligen en de open bare bloot stelling elimineren, en gegevens exfiltration van uw [Azure-Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md)voor komen.

Dit artikel doorloopt het proces met behulp van de [**Azure cli**](/cli/azure/what-is-azure-cli).

Hier volgen de stappen die in dit artikel worden behandeld: 
1. Schakel persoonlijke koppeling in en configureer een persoonlijk eind punt voor een Azure Digital Apparaatdubbels-exemplaar.
1. Schakel toegangs vlaggen voor open bare netwerken in of uit om de API-toegang tot verbindingen van particuliere koppelingen alleen te beperken.

## <a name="prerequisites"></a>Vereisten

Voordat u een persoonlijk eind punt kunt instellen, hebt u een [**Azure-Virtual Network (VNet)**](../virtual-network/virtual-networks-overview.md) nodig waar het eind punt kan worden geïmplementeerd. Als u nog geen VNet hebt, kunt u een van de Virtual Network [Snelstartgids](../virtual-network/quick-create-portal.md) van Azure volgen om dit in te stellen.

## <a name="manage-private-endpoints-for-an-azure-digital-twins-instance"></a>Privé-eind punten beheren voor een Azure Digital Apparaatdubbels-exemplaar 

Wanneer u de [Azure cli](/cli/azure/what-is-azure-cli)gebruikt, kunt u persoonlijke eind punten instellen met een persoonlijke koppeling op een Azure Digital apparaatdubbels-exemplaar dat al bestaat (het kan niet worden toegevoegd als onderdeel van het maken van een exemplaar). U kunt vervolgens door gaan met het weer geven en beheren ervan via extra CLI-opdrachten. 

>[!TIP]
> U kunt ook een persoonlijk eind punt via de service voor persoonlijke koppelingen instellen, in plaats van via uw Azure Digital Apparaatdubbels-exemplaar. Dit geeft ook dezelfde configuratie opties en hetzelfde eind resultaat.
>
> Zie de documentatie over persoonlijke koppelingen voor de [Azure Portal](../private-link/create-private-endpoint-portal.md), [Azure cli](../private-link/create-private-endpoint-cli.md), [arm-sjablonen](../private-link/create-private-endpoint-template.md)of [Power shell](../private-link/create-private-endpoint-powershell.md)voor meer informatie over het instellen van persoonlijke koppelings bronnen.

### <a name="add-a-private-endpoint-to-an-existing-instance"></a>Een persoonlijk eind punt toevoegen aan een bestaand exemplaar

Als u een persoonlijk eind punt wilt maken en deze wilt koppelen aan een Azure Digital Apparaatdubbels-exemplaar, gebruikt u de opdracht [**AZ Network private-endpoint Create**](/cli/azure/network/private-endpoint#az_network_private_endpoint_create) . Bepaal het Azure Digital Apparaatdubbels-exemplaar met behulp van de volledig gekwalificeerde ID in de `--private-connection-resource-id` para meter.

Hier volgt een voor beeld waarin de opdracht wordt gebruikt voor het maken van een persoonlijk eind punt met alleen de vereiste para meters.

```azurecli-interactive
az network private-endpoint create --connection-name {private_link_service_connection} -n {name_for_private_endpoint} -g {resource_group} --subnet {subnet_ID} --private-connection-resource-id "/subscriptions/{subscription_ID}/resourceGroups/{resource_group}/providers/Microsoft.DigitalTwins/digitalTwinsInstances/{Azure_Digital_Twins_instance_name}" 
```

Voor een volledige lijst met vereiste en optionele para meters, evenals meer voor beelden van het maken van een persoonlijk eind punt, raadpleegt u het aanmeld [ **netwerk persoonlijk-eind punt** referentie documentatie maken](/cli/azure/network/private-endpoint#az_network_private_endpoint_create).

### <a name="manage-private-endpoint-connections-on-the-instance"></a>Particuliere endpoint-verbindingen op het exemplaar beheren

Zodra een persoonlijk eind punt is gemaakt voor uw Azure Digital Apparaatdubbels-exemplaar, kunt u de opdracht [**AZ DT Network private-endpoint Connection**](/cli/azure/dt/network/private-endpoint/connection) gebruiken om de **beheer van privé-eind punten met** betrekking tot het exemplaar voort te zetten. Bewerkingen zijn onder andere:
* Een verbinding met een privé-eind punt weer geven
* De status van de particuliere-endpoint-verbinding instellen
* De particuliere-endpoint-verbinding verwijderen
* Alle privé-eindpunt verbindingen voor een exemplaar weer geven

Voor meer informatie en voor beelden raadpleegt u de [documentatie **AZ DT Network private-endpoint** Reference](/cli/azure/dt/network/private-endpoint).

### <a name="manage-other-private-link-information-on-an-azure-digital-twins-instance"></a>Andere informatie over persoonlijke koppelingen beheren op een Azure Digital Apparaatdubbels-exemplaar

U kunt aanvullende informatie over de status van de persoonlijke koppeling van uw exemplaar ontvangen met de opdrachten [**AZ DT Network private-link**](/cli/azure/dt/network/private-link) . Bewerkingen zijn onder andere:
* Persoonlijke koppelingen weer geven die zijn gekoppeld aan een Azure Digital Apparaatdubbels-exemplaar
* Een persoonlijke koppeling weer geven die is gekoppeld aan het exemplaar

Voor meer informatie en voor beelden raadpleegt u de [naslag documentatie **AZ DT Network private-link**](/cli/azure/dt/network/private-link).

## <a name="disable--enable-public-network-access-flags"></a>Toegangs vlaggen voor open bare netwerken uitschakelen/inschakelen

U kunt uw Azure Digital Apparaatdubbels-exemplaar zo configureren dat alle open bare verbindingen worden geweigerd en alleen verbindingen via persoonlijke eind punten toestaan om de netwerk beveiliging te verbeteren. Deze actie wordt uitgevoerd met een **markering voor open bare toegang tot het netwerk**. 

Met dit beleid kunt u de API-toegang beperken tot verbindingen van een particuliere verbinding. Wanneer de vlag voor open bare netwerk toegang is ingesteld op *uitgeschakeld*, retour neren alle rest API naar het gegevens vlak van het Azure Digital apparaatdubbels-exemplaar vanuit de open bare Cloud `403, Unauthorized` . Als het beleid is ingesteld op *uitgeschakeld* en een aanvraag wordt ingediend via een persoonlijk eind punt, wordt de API-aanroep uitgevoerd.

In dit artikel wordt beschreven hoe u de waarde van de netwerk vlag bijwerkt met behulp van de [Azure cli](/cli/azure/) of het [opdracht regel programma ARMClient](https://github.com/projectkudu/ARMClient). Zie de [Portal versie](how-to-enable-private-link-portal.md) van dit artikel voor instructies over hoe u dit doet met de Azure Portal.

### <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In de Azure CLI kunt u open bare netwerk toegang uitschakelen of inschakelen door een `--public-network-access` para meter aan de opdracht toe te voegen `az dt create` . Hoewel deze opdracht ook kan worden gebruikt om een nieuw exemplaar te maken, kunt u het gebruiken om de eigenschappen van een bestaand exemplaar te bewerken door de naam van een exemplaar op te geven dat al bestaat. (Zie de [documentatie](/cli/azure/dt#az_dt_create) of [algemene instructies voor het instellen van een Azure Digital apparaatdubbels-instantie](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance)voor meer informatie over deze opdracht.

Als u open bare netwerk toegang wilt **uitschakelen** voor een Azure Digital apparaatdubbels-exemplaar, gebruikt u de `--public-network-access` para meter als volgt:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Disabled
```

Als u open bare toegang tot het netwerk wilt **inschakelen** voor een instantie waar deze momenteel is uitgeschakeld, gebruikt u de volgende opdracht:

```azurecli-interactive
az dt create -n {name_of_existing_instance} -g {resource_group} --public-network-access Enabled
```

### <a name="usethe-armclientcommand-tool"></a>Gebruik het opdracht hulpprogramma ARMClient 

Met het [opdracht programma ARMClient](https://github.com/projectkudu/ARMClient)is open bare netwerk toegang ingeschakeld of uitgeschakeld met behulp van de onderstaande opdrachten. 

Open bare netwerk toegang **uitschakelen** :
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'disabled' } }"  
```

Open bare netwerk toegang **inschakelen** :  
  
```cmd/sh
armclient login 

armclient PATCH /subscriptions/<your-Azure-subscription-ID>/resourceGroups/<your-resource-group>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<your-Azure-Digital-Twins-instance>?api-version=2020-12-01 "{ 'properties': { 'publicNetworkAccess': 'enabled' } }"  
``` 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over persoonlijke koppelingen voor Azure: 
* [*Wat is Azure Private Link service?*](../private-link/private-link-service-overview.md)