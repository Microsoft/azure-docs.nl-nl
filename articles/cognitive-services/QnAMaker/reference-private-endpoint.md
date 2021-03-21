---
title: Een persoonlijk eind punt instellen-QnA Maker
description: Meer informatie over het maken van een persoonlijk eind punt in QnA Maker beheerd.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 7907c81e45680de49f6653891fb4204a59db1002
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710546"
---
# <a name="private-endpoints"></a>Privé-eindpunten

Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. QnA Maker biedt u nu ondersteuning voor het maken van persoonlijke eind punten voor de Azure Search-service. Deze functionaliteit is beschikbaar in QnA Maker beheerd. 

Privé-eind punten worden als een afzonderlijke service verzorgd door een [persoonlijke Azure-koppeling](../../private-link/private-link-overview.md). Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/private-link/) voor meer informatie over de kosten. 

## <a name="prerequisites"></a>Vereisten
> [!div class="checklist"]
> * Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.
> * Een door QnA Maker [beheerde resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) die in de Azure Portal is gemaakt. Onthoud uw Azure Active Directory-id, het abonnement en de naam van de QnA-resource die u hebt geselecteerd tijdens het maken van de resource.

## <a name="steps-to-enable-private-endpoint"></a>Stappen voor het inschakelen van een persoonlijk eind punt
1. Wijs de rol van *Contribute* toe aan QnA Maker beheerde service in het Azure Search service-exemplaar. Voor deze bewerking moet de *eigenaar* toegang hebben tot het abonnement. Ga naar het tabblad identiteit in de service resource om de identiteit op te halen.
![Beheerde service-identiteit](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Voeg de bovenstaande identiteit als *Contributer* toe door naar Azure Search service iam-tabblad te gaan. ![ IAM van beheerde service](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Klik op Roltoewijzingen *toevoegen*, voeg de identiteit toe en klik op *Opslaan*.
![Toewijzing van beheerde rollen](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Ga nu naar het tabblad *netwerken* in het exemplaar van de Azure Search-service en schakel de verbindings gegevens van het eind punt over van *openbaar* naar *particulier*. Deze bewerking is een langlopend proces en kan Maxi maal 30 minuten duren. 
![Beheerde Azure Search-netwerken](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Ga naar het tabblad *netwerk* van QnA Maker beheerde service en selecteer onder de optie *toegang toestaan vanaf* de *geselecteerde netwerken en persoonlijke eind punten* en klik op *Opslaan*. 
![Beheerde QnA Maker newtorking](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Hiermee maakt u een verbinding tussen een persoonlijk eind punt tussen de QnA Maker-service en het Azure cognitieve zoek service-exemplaar. U kunt de verbinding met een privé-eind punt controleren op het tabblad *netwerk* van Azure Search service-exemplaar. Zodra de hele bewerking is voltooid, kunt u uw QnA Maker-service gebruiken. 
![Beheerde netwerk service](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Ondersteuningsgegevens
 * Het wijzigen van Azure Search-service wordt niet ondersteund wanneer u persoonlijke toegang tot uw QnAMaker-service inschakelt. Als u de Azure Search-service via het tabblad configuratie wijzigt nadat u persoonlijke toegang hebt ingeschakeld, wordt de QnAMaker-service onbruikbaar.
 * Nadat u verbinding met een particulier eind punt tot stand hebt gebracht, kunt u de QnAMaker-service niet gebruiken als u Azure Search service netwerken naar ' openbaar ' schakelt. Azure Search-service netwerken moeten ' privé ' zijn voor de verbinding van het privé-eind punt