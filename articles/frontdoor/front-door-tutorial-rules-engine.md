---
title: 'Zelfstudie: Regelengine configureren - Azure Front Door'
description: Dit artikel bestaat uit een zelfstudie voor het configureren van de regelengine met zowel de Azure-portal als de CLI.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/09/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5d26d36c9f4ecb4aa0c7114b1ebef066e104f175
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198860"
---
# <a name="tutorial-configure-your-rules-engine"></a>Zelfstudie: Regelengine configureren

In deze zelfstudie leert u hoe u een configuratie van de regelengine en uw eerste regel in de Azure Portal en CLI kunt maken. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Regelengine configureren met behulp van de portal.
> - Regelengine configureren met behulp van Azure CLI

## <a name="prerequisites"></a>Vereisten

* Voordat u de stappen in deze zelfstudie kunt voltooien, moet u een Front Door maken. Raadpleeg [Snelstartgids: Een Front Door maken](quickstart-create-front-door.md) voor meer informatie.

## <a name="configure-rules-engine-in-azure-portal"></a>Regelengine configureren in de Azure-portal
1. Ga in de resource van de front door naar **Instellingen** en selecteer **Configuratie van regelengine**. Klik op **Toevoegen**, geef een naam op voor de configuratie en begin met het maken van uw eerste configuratie van de regelengine.

    ![Het menu Instellingen voor Front Door](./media/front-door-rules-engine/rules-engine-tutorial-1.png)

1. Klik op **Regel toevoegen** om uw eerste regel te maken. Klik vervolgens op **Voorwaarde toevoegen** of **Actie toevoegen** om de regel te definiëren.
    
    > [!NOTE]
    >- Als u een voorwaarde of actie wilt verwijderen uit een regel, gebruikt u het pictogram Prullenbak rechts van de voorwaarde of actie die u wilt verwijderen.
    > - Als u een regel wilt maken die van toepassing is op alle binnenkomend verkeer, geeft u geen voorwaarden op.
    > - Als u wilt stoppen met het evalueren van regels zodra aan de eerste voorwaarde is voldaan, selecteert u **Stoppen met evalueren van resterende regels**. Als deze optie is ingeschakeld en aan alle voorwaarden van een bepaalde regel wordt voldaan, worden de resterende regels in de configuratie niet uitgevoerd.  

    ![Configuratie van een regelengine](./media/front-door-rules-engine/rules-engine-tutorial-4.png) 

1. Bepaal de prioriteit van de regels binnen uw configuratie met behulp van de knoppen Omhoog, Omlaag en Bovenaan. De prioriteit werkt in oplopende volgorde, wat betekent dat de regel die het eerst wordt vermeld, de belangrijkste regel is.

1. Als u een of meer regels hebt gemaakt, selecteert u **Opslaan**. De configuratie van de regelengine wordt nu gemaakt.

1. Wanneer u een of meer configuraties hebt gemaakt, koppelt u een configuratie van de regelengine aan een routeregel. Hoewel één configuratie kan worden toegepast op verschillende routeregels, mag een routeregel slechts één configuratie van de regelengine bevatten. Als u de koppeling wilt maken, gaat u naar de **ontwerpfunctie van Front Door** > **Routeregels**. Selecteer de routeregel waaraan u de configuratie van de regelengine wilt toevoegen, ga naar **Routedetails** > **Configuratie van regelengine** en selecteer de configuratie die u wilt koppelen.

    ![Configureren voor een routeringsregel](./media/front-door-rules-engine/rules-engine-tutorial-5.png)


## <a name="configure-rules-engine-in-azure-cli"></a>Regelengine configureren in de Azure CLI

1. Installeer [Azure CLI](/cli/azure/install-azure-cli) als u dat nog niet hebt gedaan. Voeg de extensie 'front-door' toe: -az extension add --name front-door. Meld u vervolgens aan en schakel over naar uw abonnement met az account set --subscription <naam_of_id>.

1. Begin met het maken van een regelengine. In dit voorbeeld maakt u één regel met één actie op basis van de header en één match-voorwaarde. 

    ```azurecli-interactive
    az network front-door rules-engine rule create -f {front_door} -g {resource_group} --rules-engine-name {rules_engine} --name {rule1} --priority 1 --action-type RequestHeader --header-action Overwrite --header-name Rewrite --header-value True --match-variable RequestFilenameExtension --operator Contains --match-values jpg png --transforms Lowercase
    ```

1. Geef alle regels weer. 

    ```azurecli-interactive
    az network front-door rules-engine rule list -f {front_door} -g {rg} --name {rules_engine}
    ```

1. Voeg een actie toe voor het overschrijven van een doorstuurroute. 

    ```azurecli-interactive
    az network front-door rules-engine rule action add -f {front_door} -g {rg} --rules-engine-name {rules_engine} --name {rule1} --action-type ForwardRouteOverride --backend-pool {backend_pool_name} --caching Disabled
    ```

1. Geef alle acties in een regel weer. 

    ```azurecli-interactive
    az network front-door rules-engine rule action list -f {front_door} -g {rg} -r {rules_engine} --name {rule1}
    ```

1. Koppel een configuratie van de regelengine aan een routeregel.  

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --rules-engine {rules_engine}
    ```

1. Koppel de regelengine los. 

    ```azurecli-interactive
    az network front-door routing-rule update -g {rg} -f {front_door} -n {routing_rule_name} --remove rulesEngine # case sensitive word ‘rulesEngine’
    ```

Voor meer informatie vindt u [hier](/cli/azure/ext/front-door/network/front-door/rules-engine) een volledige lijst met opdrachten voor de AFD-regelengine.   

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u de configuratie van de regelengine geconfigureerd en gekoppeld aan uw routeringsregels. Als u de configuratie van de regelengine die aan uw Front door is gekoppeld niet meer wilt, kunt u de configuratie verwijderen door de volgende stappen uit te voeren:

1. Koppel alle routeringsregels uit de configuratie van de regelengine los door te klikken op de drie stippen naast de naam van de regelengine.

    :::image type="content" source="./media/front-door-rules-engine/front-door-rule-engine-routing-association.png" alt-text="Routeringsregels koppelen":::

1. Schakel alle routeringsregels uit waaraan deze regelengine-configuratie is gekoppeld en klik op Opslaan.

    :::image type="content" source="./media/front-door-rules-engine/front-door-routing-rule-association.png" alt-text="Koppeling voor routeringsregel":::

1. U kunt nu de configuratie van de regelengine verwijderen van uw Front Door.

    :::image type="content" source="./media/front-door-rules-engine/front-door-delete-rule-engine-configuration.png" alt-text="Regelengine-configuratie verwijderen":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Een regelengine-configuratie verwijderen
* Koppel de configuratie aan uw Front Door-routeringsregels.

Ga naar de volgende zelfstudie om te leren hoe u beveiligingskopteksten kunt toevoegen met de regelengine.

> [!div class="nextstepaction"]
> [Beveiligingsheaders met de regelengine](front-door-security-headers.md)