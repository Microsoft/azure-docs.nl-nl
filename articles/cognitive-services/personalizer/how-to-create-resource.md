---
title: Een persoonlijke resource maken
description: In dit artikel leert u hoe u een persoonlijke resource maakt in de Azure Portal voor elke feedback-lus.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0c75d917f1abe72af2f4aa56b0f67dbb7bcd24a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91303553"
---
# <a name="create-a-personalizer-resource"></a>Een persoonlijke resource maken

Een persoonlijke resource is hetzelfde als een aangepaste lus voor het leren. Er wordt één resource of Learning-lus gemaakt voor elk onderwerp dat u hebt of het inhouds gebied dat u hebt. Gebruik niet meerdere inhouds gebieden in dezelfde lus, omdat dit de leer proces verwart en slechte voor spellingen oplevert.

Als u de beste inhoud voor meer dan één inhouds gebied van een webpagina wilt selecteren, moet u voor elke inhoud een andere lus gebruiken.


## <a name="create-a-resource-in-the-azure-portal"></a>Een resource maken in Azure Portal

Maak een persoonlijke resource voor elke feedback-lus.

1. Meld u aan bij de [Azure-portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Met de vorige koppeling gaat u naar de pagina **maken** voor de personaler-service.
1. Voer uw service naam in, selecteer een abonnement, locatie, prijs categorie en resource groep.

    > [!div class="mx-imgBorder"]
    > ![Gebruik Azure Portal om een persoonlijker resource te maken, ook wel een Learning-lus genoemd.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Selecteer **maken** om de resource te maken.

1. Nadat de resource is geïmplementeerd, selecteert u de knop **Ga naar resource** om naar uw persoonlijke resource te gaan.

1. Selecteer de pagina **Quick Start** voor uw resource en kopieer de waarden voor het eind punt en de sleutel. U hebt het resource-eind punt en de sleutel nodig voor het gebruik van de Rank-en belonings-Api's.

1. Selecteer de **configuratie** pagina voor de nieuwe resource om [de learning-lus te configureren](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Een resource maken met de Azure CLI

1. Meld u aan bij de Azure CLI met de volgende opdracht:

    ```azurecli-interactive
    az login
    ```

1. Maak een resource groep, een logische groepering, voor het beheer van alle Azure-resources die u wilt gebruiken met de persoonlijke resource.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Maak een nieuwe Personaler-resource, _Learning-lus_, met de volgende opdracht voor een bestaande resource groep.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Hiermee wordt een JSON-object geretourneerd dat uw **resource-eind punt** bevat.

1. Gebruik de volgende Azure CLI-opdracht om de **resource sleutel** op te halen.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    U hebt het resource-eind punt en de sleutel nodig voor het gebruik van de Rank-en belonings-Api's.

## <a name="next-steps"></a>Volgende stappen

* [Configureren](how-to-settings.md) Leer loop van personaler
