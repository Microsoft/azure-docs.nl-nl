---
title: Beheerde identiteit op Azure Event Grid aangepaste onderwerpen en domeinen inschakelen
description: In dit artikel wordt beschreven hoe u de beheerde service-identiteit voor een Azure Event Grid aangepast onderwerp of domein inschakelt.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: b93fd44282d9e19e7111dd52c73d8d4c01c67a10
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278215"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Een door een systeem beheerde identiteit toewijzen aan een Event Grid aangepast onderwerp of domein 
Dit artikel laat u zien hoe u een door een systeem beheerde identiteit inschakelt voor een Event Grid aangepast onderwerp of een domein. Zie [Wat zijn beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.

## <a name="enable-identity-at-the-time-of-creation"></a>Identiteit inschakelen op het moment van maken

### <a name="using-azure-portal"></a>Azure Portal gebruiken
U kunt de door het systeem toegewezen identiteit inschakelen voor een aangepast onderwerp of een domein tijdens het maken in de Azure Portal. De volgende afbeelding laat zien hoe u een door een systeem beheerde identiteit voor een aangepast onderwerp inschakelt. In principe selecteert u de optie **systeem toegewezen identiteit inschakelen** op de pagina **Geavanceerd** van de wizard voor het maken van het onderwerp. U ziet deze optie ook op de pagina **Geavanceerd** van de wizard voor het maken van het domein. 

![Identiteit inschakelen tijdens het maken van een aangepast onderwerp](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Azure CLI gebruiken
U kunt ook de Azure CLI gebruiken om een aangepast onderwerp of domein te maken met een door het systeem toegewezen identiteit. Gebruik de `az eventgrid topic create` opdracht met de `--identity` para meter ingesteld op `systemassigned` . Als u geen waarde voor deze para meter opgeeft, wordt de standaard waarde `noidentity` gebruikt. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Op dezelfde manier kunt u de `az eventgrid domain create` opdracht gebruiken om een domein te maken met een door het systeem beheerde identiteit.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Identiteit voor een bestaand aangepast onderwerp of domein inschakelen
In deze sectie leert u hoe u een door een systeem beheerde identiteit inschakelt voor een bestaand aangepast onderwerp of domein. 

### <a name="using-azure-portal"></a>Azure Portal gebruiken
De volgende procedure laat zien hoe u een door het systeem beheerde identiteit voor een aangepast onderwerp inschakelt. De stappen voor het inschakelen van een identiteit voor een domein zijn vergelijkbaar. 

1. Ga naar de [Azure Portal](https://portal.azure.com).
2. Zoek naar **Event grid-onderwerpen** in de zoek balk aan de bovenkant.
3. Selecteer het **aangepaste onderwerp** waarvoor u de beheerde identiteit wilt inschakelen. 
4. Ga naar het tabblad **identiteit** . 
5. Schakel de switch **in** om de identiteit in te scha kelen. 
1. Selecteer **Opslaan** op de werk balk om de instelling op te slaan. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Identiteits pagina voor een aangepast onderwerp"::: 

U kunt soort gelijke stappen gebruiken om een identiteit in te scha kelen voor een event grid-domein.

### <a name="use-the-azure-cli"></a>Azure CLI gebruiken
Gebruik de `az eventgrid topic update` opdracht met `--identity` ingesteld op `systemassigned` om de door het systeem toegewezen identiteit in te scha kelen voor een bestaand aangepast onderwerp. Als u de identiteit wilt uitschakelen, geeft u `noidentity` de waarde op. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

De opdracht voor het bijwerken van een bestaand domein is vergelijkbaar ( `az eventgrid domain update` ).


## <a name="next-steps"></a>Volgende stappen
Voeg de identiteit toe aan een geschikte rol (bijvoorbeeld Service Bus verzender van gegevens) op de bestemming (bijvoorbeeld een Service Bus wachtrij). Zie voor gedetailleerde stappen [beheerde identiteit verlenen de toegang tot Event grid bestemming](add-identity-roles.md). 
