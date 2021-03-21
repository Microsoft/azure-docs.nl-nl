---
title: Apache Kafka maken voor Confluent Cloud - Azure-partneroplossingen
description: In dit artikel wordt beschreven hoe u in Azure Portal een instantie van Apache Kafka voor Confluent Cloud maakt.
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98253659"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>Snelstartgids: Aan de slag met Apache Kafka voor Confluent Cloud

In deze quickstart gebruikt u Azure Portal om een instantie van Apache Kafka voor Confluent Cloud te maken.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen actief abonnement op Azure hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) maken.
- U moet de rol _Eigenaar_ of _Inzender_ voor het Azure-abonnement hebben. De integratie tussen Azure en Confluent kan alleen worden ingesteld door gebruikers met de rol _Eigenaar_ of _Inzender_. [Controleer of u over de juiste rol beschikt](../../role-based-access-control/check-access.md) voor u aan de slag gaat.

## <a name="find-offer"></a>Aanbieding zoeken

Gebruik Azure Portal om de toepassing Apache Kafka voor Confluent Cloud te vinden.

1. Ga in een webbrowser naar [Azure Portal](https://portal.azure.com/) en meld u aan.

1. Als u **Marketplace** in een recente sessie hebt bezocht, selecteert u het pictogram tussen de beschikbare opties. Als dat niet het geval is, zoekt u naar _Marketplace_.

    :::image type="content" source="media/marketplace.png" alt-text="Pictogram van Marketplace.":::

1. Op de pagina **Marketplace** ziet u twee opties op basis van het type abonnement dat u wilt. U kunt u registreren voor een abonnement op basis van betalen per gebruik of op basis van een toezegging. Betalen per gebruik is openbaar beschikbaar. Het toezeggingsabonnement is beschikbaar voor klanten die zijn goedgekeurd voor een privé-aanbieding.

   - Voor klanten die kiezen voor **betalen naar gebruik**, zoekt u _Apache Kafka in Confluent Cloud_. Selecteer de aanbieding voor Apache Kafka in Confluent Cloud.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="zoeken naar Azure Marketplace-aanbieding.":::

   - Voor klanten die kiezen voor een **toezegging** selecteert u de koppeling naar **Persoonlijke aanbiedingen weergeven**. Voor de toezegging moet u zich registreren voor een minimaal te besteden bedrag. Gebruik deze optie alleen als u weet dat u de service gedurende lange tijd nodig hebt.

     :::image type="content" source="media/view-private-offers.png" alt-text="persoonlijke aanbiedingen weergeven.":::

     Zoek naar _Apache Kafka in Confluent Cloud_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="persoonlijke aanbieding selecteren.":::

## <a name="create-resource"></a>Bron maken

Nadat u de aanbieding voor Apache Kafka in Confluent Cloud hebt geselecteerd, kunt u de toepassing gaan instellen.

1. Als u in de vorige sectie persoonlijke aanbiedingen hebt geselecteerd, zijn er voor abonnementstypen twee opties:

    - Confluent Cloud - betalen per gebruik
    - Toezegging - voor een toezeggingsabonnement

   Als u geen persoonlijke aanbiedingen hebt geselecteerd, is betalen naar gebruik de enige optie.

   Kies het abonnement dat u wilt gebruiken en selecteer **Instellen en abonneren**.

    :::image type="content" source="media/setup-subscribe.png" alt-text="Instellen en abonneren.":::

1. Op de basispagina **Confluent Cloud-resource maken** geeft u de volgende waarden op. Wanneer u klaar bent, selecteert u **Volgende: Tags**.

    :::image type="content" source="media/setup-basics.png" alt-text="Formulier voor het instellen van Confluent Cloud-resource.":::

    | Eigenschap | Beschrijving |
    | ---- | ---- |
    | **Abonnement** | In de vervolgkeuzelijst selecteert u het Azure-abonnement voor de implementatie. U moet de rol _Eigenaar_ of _Inzender_ hebben. |
    | **Resourcegroep** | Geef op of u een nieuwe resourcegroep wilt maken of een bestaande resourcegroep wilt gebruiken. Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. Zie [Overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md) voor meer informatie. |
    | **Naam Confluent-organisatie** | Geef de SaaS-resource (Software-as-a-Service) een naam. |
    | **Regio** | Selecteer een van de volgende regio's in het vervolgkeuzemenu: <br/><br/> Australië - oost, Canada - centraal, VS - centraal, US - oost, US - oost 2, Frankrijk - centraal, EU - noord, Azië - zuidoost, VK - zuid, VS - west-centraal, Europa - west, US - west 2 |
    | **Plannen** | Selecteer **Betalen per gebruik** of **Toezegging**. |
    | **Factureringstermijn** | Vooraf ingevuld op basis van het geselecteerde abonnement. |
    | **Prijs** | Vooraf ingevuld op basis van het geselecteerde Confluent-abonnement. |

1. Geef bij **Tags** de **naam** en **waarde** paren op voor tags die u op de resource wilt toepassen. Nadat u de tags hebt ingevoerd, selecteert u **Beoordelen en maken**.

    :::image type="content" source="media/setup-tags.png" alt-text="Projecttags toevoegen.":::

1. Controleer de instellingen die u hebt opgegeven. Selecteer **Maken** wanneer u klaar bent.

1. Het duurt een paar minuten om de resource te maken. U kunt de implementatiestatus weergeven in **Meldingen**. Nadat de implementatie is voltooid, selecteert u de resource om de pagina **Overzicht** weer te geven.

    :::image type="content" source="media/deployment-status.png" alt-text="Implementatiestatus.":::

   Als er een fout optreedt, raadpleegt u [Problemen met Apache Kafka voor Confluent Cloud oplossen](troubleshoot.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Confluent Cloud-resource beheren](manage.md)
