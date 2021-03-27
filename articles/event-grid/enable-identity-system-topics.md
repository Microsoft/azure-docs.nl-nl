---
title: Beheerde identiteit inschakelen op Azure Event Grid systeem onderwerp
description: In dit artikel wordt beschreven hoe u de beheerde service-identiteit voor een Azure Event Grid systeem onderwerp inschakelt.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 66b418787e5570dc5da06a5332dd834ccbfd4aef
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630416"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Een door een systeem beheerde identiteit toewijzen aan een Event Grid systeem onderwerp
In dit artikel leert u hoe u door het systeem beheerde identiteit kunt inschakelen voor een bestaand Event Grid systeem onderwerp. Zie [Wat zijn beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten.  

> [!IMPORTANT]
> Op dit moment kunt u een door het systeem beheerde identiteit niet inschakelen bij het maken van een nieuw systeem onderwerp, dat wil zeggen, wanneer u een gebeurtenis abonnement maakt op een Azure-resource die systeem onderwerpen ondersteunt. 


## <a name="use-azure-portal"></a>Azure Portal gebruiken
De volgende procedure laat zien hoe u een door het systeem beheerde identiteit voor een systeem onderwerp inschakelt. 

1. Ga naar de [Azure Portal](https://portal.azure.com).
2. Zoek in de zoek balk bovenaan naar **Event grid-systeem onderwerpen** .
3. Selecteer het **onderwerp** van het systeem waarvoor u de beheerde identiteit wilt inschakelen. 
4. Selecteer **identiteit** in het menu links. Deze optie wordt niet weer geven voor een systeem onderwerp dat zich op de algemene locatie bevinden. 
5. Schakel de switch **in** om de identiteit in te scha kelen. 
1. Selecteer **Opslaan** op de werk balk om de instelling op te slaan. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Identiteits pagina voor een systeem onderwerp"::: 
1. Selecteer **Ja** in het bevestigings bericht. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Identiteit toewijzen aan een systeem onderwerp-bevestiging"::: 
1. Controleer of u de object-ID van de door het systeem toegewezen beheerde identiteit ziet en raadpleeg een koppeling om rollen toe te wijzen. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Identiteit toewijzen aan een systeem onderwerp-voltooid"::: 

## <a name="global-azure-sources"></a>Wereld wijde Azure-bronnen
U kunt door het systeem beheerde identiteit alleen inschakelen voor de regionale Azure-resources. U kunt deze niet inschakelen voor systeem onderwerpen die zijn gekoppeld aan algemene Azure-resources, zoals Azure-abonnementen, resource groepen of Azure Maps. De systeem onderwerpen voor deze globale bronnen zijn ook niet gekoppeld aan een specifieke regio. U ziet de pagina **identiteit** voor het onderwerp System waarvan de locatie is ingesteld op **Global**. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="Systeem onderwerp met de locatie die is ingesteld op Global"::: 



## <a name="next-steps"></a>Volgende stappen
Voeg de identiteit toe aan een geschikte rol (bijvoorbeeld Service Bus verzender van gegevens) op de bestemming (bijvoorbeeld een Service Bus wachtrij). Zie [identiteit toevoegen aan Azure-rollen op bestemmingen](add-identity-roles.md)voor gedetailleerde stappen. 