---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94574452"
---
> [!IMPORTANT]
> Het Cosmos DB exemplaar wordt gemaakt in een door micro soft beheerde resource groep in __uw abonnement__, samen met alle benodigde resources. Dit betekent dat u in rekening wordt gebracht voor dit Cosmos DB exemplaar. De beheerde resource groep krijgt de naam in de indeling `<AML Workspace Resource Group Name><GUID>` . Als uw Azure Machine Learning-werk ruimte een persoonlijk eind punt gebruikt, wordt er ook een virtueel netwerk voor het Cosmos DB exemplaar gemaakt. Dit VNet wordt gebruikt voor het beveiligen van de communicatie tussen Cosmos DB en Azure Machine Learning.
> 
> * __Verwijder niet de resource groep__ die dit Cosmos DB exemplaar bevat of een van de resources die automatisch in deze groep zijn gemaakt. Als u de resource groep, Cosmos DB-exemplaar, etc. wilt verwijderen, moet u de Azure Machine Learning-werk ruimte die deze gebruikt, verwijderen. De resource groep, Cosmos DB instantie en andere automatisch gemaakte resources worden verwijderd wanneer de bijbehorende werk ruimte wordt verwijderd.
> * De standaard [__aanvraag eenheden__](../articles/cosmos-db/request-units.md) voor dit Cosmos DB account is ingesteld op __8000__.
> * U __kunt uw eigen VNet niet opgeven voor gebruik met de Cosmos DB instantie__ die is gemaakt. U __kunt het virtuele netwerk ook niet wijzigen__. U kunt bijvoorbeeld het IP-adres bereik dat wordt gebruikt, niet wijzigen.