---
title: Azure Container Instances implementeren & beheren
description: Taken en werk stromen automatiseren waarmee container implementaties in Azure Container Instances worden gemaakt en beheerd met behulp van Azure Logic Apps
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76046290"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Azure Container Instances implementeren en beheren met behulp van Azure Logic Apps

Met Azure Logic Apps en de Azure container instance-connector kunt u geautomatiseerde taken en werk stromen instellen waarmee [container groepen](../container-instances/container-instances-container-groups.md)worden geïmplementeerd en beheerd. De container instance-connector ondersteunt de volgende acties:

* Een container groep maken of verwijderen
* De eigenschappen van een container groep ophalen
* Een lijst met container groepen ophalen
* De logboeken van een container exemplaar ophalen

Gebruik deze acties in uw Logic apps voor taken, zoals het uitvoeren van een container werk belasting in reactie op een Logic Apps trigger. U kunt ook andere acties uitvoeren op basis van de acties van container instanties. 

Deze connector biedt alleen acties, dus als u uw logische app wilt starten, gebruikt u een afzonderlijke trigger, zoals een **terugkeer patroon** , om een container workload volgens een regel matig schema uit te voeren. Of u moet mogelijk een implementatie van een container groep activeren na een gebeurtenis, zoals de ontvangst van een Outlook-e-mail. 

Als u geen ervaring hebt met Logic apps, raadpleegt u [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/). 

* Basis kennis over [het maken van logische apps](../logic-apps/quickstart-create-first-logic-app-workflow.md) en [het maken en beheren van container instanties](../container-instances/container-instances-quickstart.md)

* De logische app waartoe u toegang wilt krijgen tot uw container exemplaren. Als u een actie wilt gebruiken, start u uw logische app met een andere trigger, bijvoorbeeld de trigger voor **terugkeer patroon** .

## <a name="add-a-container-instance-action"></a>Een actie voor een container exemplaar toevoegen

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open de logische app in de ontwerp functie voor logische apps, als deze nog niet is geopend.

1. Kies een pad: 

   * Kies **nieuwe stap** onder de laatste stap waarin u een actie wilt toevoegen. 

     -of-

   * Als u een actie wilt toevoegen, plaatst u de muis aanwijzer op de pijl tussen de stappen. 
   Kies het plus teken ( **+** ) dat wordt weer gegeven en selecteer vervolgens **een actie toevoegen**.

1. Voer in het zoekvak ' container instance ' in als uw filter. Selecteer in de lijst acties de gewenste actie voor de Azure container instance-connector.

1. Geef een naam op voor uw verbinding. 

1. Geef de benodigde gegevens voor de geselecteerde actie op en blijf door gaan met het bouwen van de werk stroom van uw logische app.

  Selecteer bijvoorbeeld **container groep maken** en voer de eigenschappen voor een container groep en een of meer container instanties in de groep in, zoals wordt weer gegeven in de volgende afbeelding (gedeeltelijke details):

  ![Containergroep maken](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Connector-verwijzing

Raadpleeg de [referentie pagina](/connectors/aci/) van de connector of de [verwijzing naar](../container-instances/container-instances-reference-yaml.md)de container groep yaml voor technische informatie over triggers, acties en limieten die worden beschreven in de beschrijving van de OpenAPI (voorheen Swagger) van de connector.

## <a name="next-steps"></a>Volgende stappen

* Bekijk een voor beeld van een [logische app](https://github.com/Azure-Samples/aci-logicapps-integration) die een container in azure container instances uitvoert om de sentiment van E-mail of Twitter-tekst te analyseren

* Meer informatie over andere [Logic apps-connectors](../connectors/apis-list.md)