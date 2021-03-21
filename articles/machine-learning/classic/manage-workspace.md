---
title: 'ML Studio (klassiek): werk ruimten beheren-Azure'
description: Toegang tot Azure Machine Learning Studio (klassieke) werk ruimten beheren en Machine Learning API-webservices implementeren en beheren
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: c41c5350e29555a11bb74eeca9af62a6b87cfced
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094270"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Een Azure Machine Learning Studio-werk ruimte (klassieke) beheren

**VAN TOEPASSING OP:**  ![Van toepassing op.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (klassiek) ![Niet van toepassing op.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


> [!NOTE]
> Voor informatie over het beheren van webservices in de Machine Learning Web Services-portal, Zie [een webservice beheren met de Azure machine learning Web Services-portal](manage-new-webservice.md).
> 
> 

U kunt Machine Learning Studio (klassieke) werk ruimten in de Azure Portal beheren.



## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Een studio-werk ruimte (klassieke) beheren in de Azure Portal:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met behulp van een Azure-abonnement beheerders account.
2. In het zoekvak boven aan de pagina voert u ' machine learning Studio (klassieke) werk ruimten ' in en selecteert u vervolgens **machine learning Studio (klassieke) werk ruimten**.
3. Klik op de werk ruimte die u wilt beheren.

Naast de standaard informatie en beschik bare opties voor resource management kunt u het volgende doen:

- **Eigenschappen** weer geven: deze pagina bevat informatie over de werk ruimte en de resource, en u kunt het abonnement en de resource groep wijzigen waarmee deze werk ruimte is verbonden.
- **Opslag sleutels** voor opnieuw synchroniseren: de werk ruimte behoudt de sleutels voor het opslag account. Als het opslag account sleutels wijzigt, kunt u op **Resync-sleutels** klikken om de sleutels te synchroniseren met de werk ruimte.

Gebruik de Machine Learning Web Services-portal om de webservices te beheren die zijn gekoppeld aan deze studio-werk ruimte (klassieke). Zie [Manage a web service using the Azure machine learning Web Services portal](manage-new-webservice.md) (Engelstalig) voor uitgebreide informatie.

> [!NOTE]
> Als u nieuwe webservices wilt implementeren of beheren, moet u een rol Inzender of beheerder toewijzen aan het abonnement waarop de webservice is geïmplementeerd. Als u een andere gebruiker uitnodigt voor een machine learning studio-werk ruimte, moet u deze toewijzen aan de rol Inzender of beheerder voor het abonnement voordat ze webservices kunnen implementeren of beheren. 
> 
>Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het instellen van toegangs machtigingen.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [implementeren van machine learning met Azure Resource Manager-sjablonen](deploy-with-resource-manager-template.md).