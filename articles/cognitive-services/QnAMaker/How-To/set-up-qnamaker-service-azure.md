---
title: Een QnA Maker-service instellen-QnA Maker
description: Voordat u QnA Maker Knowledge bases kunt maken, moet u eerst een QnA Maker service in azure instellen. Iedereen met een machtiging voor het maken van nieuwe resources in een abonnement kan een QnA Maker-service instellen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: b6ab131c0fa81609b956de53f2b15d445e8979dd
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219264"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker-resources beheren

Voordat u QnA Maker Knowledge bases kunt maken, moet u eerst een QnA Maker service in azure instellen. Iedereen met een machtiging voor het maken van nieuwe resources in een abonnement kan een QnA Maker-service instellen.

Een uitgevulde uitleg van de volgende concepten is handig voordat u de resource maakt:

* [QnA Maker resources](../Concepts/azure-resources.md)
* [Sleutels ontwerpen en publiceren](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Een nieuwe QnA Maker-service maken

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

Met deze procedure maakt u de Azure-resources die nodig zijn voor het beheren van de inhoud van de Knowledge Base. Nadat u deze stappen hebt voltooid, vindt u de _abonnements_ sleutels op de pagina **sleutels** voor de resource in de Azure Portal.

1. Meld u aan bij de Azure Portal en [Maak een QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) -resource.

1. Selecteer **maken** nadat u de voor waarden hebt gelezen:

    ![Een nieuwe QnA Maker-service maken](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Selecteer in **QnA Maker** de juiste lagen en regio's:

    ![Een nieuwe QnA Maker prijs categorie en regio's voor services maken](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Voer in het veld **naam** een unieke naam in om deze QnA Maker service aan te duiden. Deze naam duidt ook het QnA Maker-eind punt aan waaraan uw kennis grondslagen worden gekoppeld.
    * Kies het **abonnement** waarmee de QnA Maker resource wordt geïmplementeerd.
    * Selecteer de **prijs categorie** voor de QnA Maker-beheer Services (Portal-en beheer-api's). Bekijk [meer informatie over de prijzen van de SKU](https://aka.ms/qnamaker-pricing).
    * Een nieuwe **resource groep** maken (aanbevolen) of een bestaande gebruiken om deze QnA Maker-resource te implementeren. QnA Maker maakt verschillende Azure-resources. Wanneer u een resource groep maakt om deze resources te bevatten, kunt u deze resources eenvoudig vinden, beheren en verwijderen op basis van de naam van de resource groep.
    * Selecteer een **locatie voor de resource groep**.
    * Kies de **prijs categorie voor zoeken** in de Azure Cognitive Search-service. Als de optie gratis laag niet beschikbaar is (grijs weer gegeven), betekent dit dat u al een gratis service hebt geïmplementeerd via uw abonnement. In dat geval moet u beginnen met de laag basis. Zie de [prijs informatie voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).
    * Kies de **Zoek locatie** waar u Azure Cognitive Search-indexen wilt implementeren. Beperkingen voor het opslaan van klant gegevens kunnen helpen bij het bepalen van de locatie die u kiest voor Azure Cognitive Search.
    * Voer in het veld **app-naam** een naam in voor uw Azure app service-exemplaar.
    * Standaard App Service standaard ingesteld op de standaard laag (S1). U kunt het abonnement wijzigen nadat u het hebt gemaakt. Meer informatie over [app service prijzen](https://azure.microsoft.com/pricing/details/app-service/).
    * Kies de **locatie** van de Website waar app service worden geïmplementeerd.

        > [!NOTE]
        > De **Zoek locatie** kan afwijken van de **locatie** van de website.

    * Kies of u **Application Insights** wilt inschakelen. Als **Application Insights** is ingeschakeld, verzamelt QnA Maker telemetrie in verkeer, chat logboeken en fouten.
    * Kies de **app Insights-locatie** waar de Application Insights resource wordt geïmplementeerd.
    * Voor kosten besparingen kunt u enkele, maar niet alle Azure-resources die zijn gemaakt voor QnA Maker [delen](configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource) .

1. Nadat alle velden zijn gevalideerd, selecteert u **maken**. Het proces kan een paar minuten duren.

1. Nadat de implementatie is voltooid, ziet u de volgende resources die zijn gemaakt in uw abonnement:

   ![Resource heeft een nieuwe QnA Maker service gemaakt](../media/qnamaker-how-to-setup-service/resources-created.png)

    De resource met het _Cognitive Services_ type heeft uw _abonnements_ sleutels.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

Met deze procedure maakt u de Azure-resources die nodig zijn voor het beheren van de inhoud van de Knowledge Base. Nadat u deze stappen hebt voltooid, vindt u de *abonnements* sleutels op de pagina **sleutels** voor de resource in de Azure Portal.

1. Meld u aan bij de Azure Portal en [Maak een QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) -resource.

1. Selecteer **maken** nadat u de voor waarden hebt gelezen:

    ![Een nieuwe QnA Maker-service maken](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Schakel in **QnA Maker** het selectie vakje Managed (preview) in en selecteer de juiste lagen en regio's:

    ![Een nieuwe QnA Maker beheerde service maken-prijs categorie en regio's](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png)

    * Kies het **abonnement** waarmee de QnA Maker resource wordt geïmplementeerd.
    * Maak een nieuwe **resource groep** (aanbevolen) of gebruik een bestaande. voor het implementeren van deze QnA Maker Managed (preview)-resource. QnA Maker Managed (preview) maakt enkele Azure-resources. Wanneer u een resource groep maakt om deze resources te bevatten, kunt u deze resources eenvoudig vinden, beheren en verwijderen op basis van de naam van de resource groep.
    * Voer in het veld **naam** een unieke naam in om deze QnA Maker Managed (Preview-Service) aan te duiden. 
    * Kies de **locatie** waar u de beheerde service voor QnA Maker (preview) wilt implementeren. De beheer-Api's en het service-eind punt worden gehost op deze locatie. 
    * Selecteer de **prijs categorie** voor de QnA Maker Managed (preview)-service (gratis voor preview). Bekijk [meer informatie over de prijzen van de SKU](https://aka.ms/qnamaker-pricing).
    * Kies de **Zoek locatie** waar u Azure Cognitive Search-indexen wilt implementeren. Beperkingen voor het opslaan van klant gegevens kunnen helpen bij het bepalen van de locatie die u kiest voor Azure Cognitive Search.
    * Kies de **prijs categorie voor zoeken** in de Azure Cognitive Search-service. Als de optie gratis laag niet beschikbaar is (grijs weer gegeven), betekent dit dat u al een gratis service hebt geïmplementeerd via uw abonnement. In dat geval moet u beginnen met de laag basis. Zie de [prijs informatie voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).

1. Nadat alle velden zijn gevalideerd, selecteert u **controleren + maken**. Het proces kan een paar minuten duren.

1. Nadat de implementatie is voltooid, ziet u de volgende resources die zijn gemaakt in uw abonnement:

    ![Resource heeft een nieuwe beheerde QnA Maker-service (preview) gemaakt](../media/qnamaker-how-to-setup-service/resources-created-v2.png)

    De resource met het _Cognitive Services_ type heeft uw _abonnements_ sleutels.

---

## <a name="upgrade-azure-resources"></a>Azure-resources upgraden

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

### <a name="upgrade-qna-maker-sku"></a>QnA Maker SKU bijwerken

Als u meer vragen en antwoorden in uw Knowledge Base wilt hebben, moet u de prijs categorie van QnA Maker service upgraden naar uw huidige laag.

Een upgrade uitvoeren van de QnA Maker Management SKU:

1. Ga in de Azure Portal naar uw QnA Maker-resource en selecteer **prijs categorie**.

    ![QnA Maker resource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Kies de juiste SKU en druk op **selecteren**.

    ![QnA Maker prijzen](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)
    
### <a name="upgrade-app-service"></a>App Service bijwerken

Als uw Knowledge Base meer aanvragen van uw client-app wil leveren, moet u uw App Service prijs categorie bijwerken.

U kunt App Service [schalen](../../../app-service/manage-scale-up.md) of uitschalen.

Ga naar de App Service resource in de Azure Portal en selecteer de optie **Omhoog schalen** of **uitschalen** .

![QnA Maker App Service schaal](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>De Azure Cognitive Search-service upgraden

Als u een groot aantal knowledge bases wilt hebben, moet u de prijscategorie van de Azure Cognitive Search-service upgraden.

Op dit moment kunt u geen in-place upgrade van de SKU voor Azure Search uitvoeren. U kunt echter een nieuwe Azure Search-resource met de gewenste SKU maken, de gegevens herstellen naar de nieuwe resource en deze vervolgens koppelen aan de QnA Maker-stack. Voer hiervoor de volgende stappen uit:

1. Maak een nieuwe Azure-Zoek resource in het Azure Portal en selecteer de gewenste SKU.

    ![QnA Maker Azure Search-resource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Herstel de indexen van de oorspronkelijke Azure Search-resource naar de nieuwe. Raadpleeg de [voorbeeldcode voor back-up herstellen](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Nadat de gegevens zijn hersteld, gaat u naar uw nieuwe Azure Search-resource, selecteert u **sleutels** en noteert u de **naam** en de **beheerders sleutel**:

    ![QnA Maker Azure-Zoek sleutels](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Als u de nieuwe Azure Search-resource wilt koppelen aan de QnA Maker stack, gaat u naar het QnA Maker App Service-exemplaar.

    ![App Service instantie QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Selecteer **Toepassingsinstellingen** en wijzig de instellingen in de velden **AzureSearchName** en **AzureSearchAdminKey** uit stap 3.

    ![Instelling voor QnA Maker App Service](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Start het App Service-exemplaar opnieuw op.

    ![Het QnA Maker App Service-exemplaar opnieuw starten](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
### <a name="inactivity-policy-for-free-search-resources"></a>Inactiviteits beleid voor gratis Zoek bronnen

Als u geen QnA Maker-resource gebruikt, moet u alle resources verwijderen. Als u geen ongebruikte resources verwijdert, zal uw kennis basis niet meer werken als u een gratis Zoek resource hebt gemaakt.

Gratis Zoek bronnen worden na 90 dagen verwijderd zonder dat er een API-aanroep wordt ontvangen.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

### <a name="upgrade-the-azure-cognitive-search-service"></a>De Azure Cognitive Search-service upgraden

Als u een groot aantal knowledge bases wilt hebben, moet u de prijscategorie van de Azure Cognitive Search-service upgraden.

Op dit moment kunt u geen in-place upgrade van de SKU voor Azure Search uitvoeren. U kunt echter een nieuwe Azure Search-resource met de gewenste SKU maken, de gegevens herstellen naar de nieuwe resource en deze vervolgens koppelen aan de QnA Maker-stack. Voer hiervoor de volgende stappen uit:

1. Maak een nieuwe Azure-Zoek resource in het Azure Portal en selecteer de gewenste SKU.

    ![QnA Maker Azure Search-resource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Herstel de indexen van de oorspronkelijke Azure Search-resource naar de nieuwe. Raadpleeg de [voorbeeldcode voor back-up herstellen](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Als u de nieuwe Azure Search-resource wilt koppelen aan de service voor QnA Maker Managed (preview), raadpleegt u het onderstaande onderwerp.

### <a name="inactivity-policy-for-free-search-resources"></a>Inactiviteits beleid voor gratis Zoek bronnen

Als u geen QnA Maker-resource gebruikt, moet u alle resources verwijderen. Als u geen ongebruikte resources verwijdert, zal uw kennis basis niet meer werken als u een gratis Zoek resource hebt gemaakt.

Gratis Zoek bronnen worden na 90 dagen verwijderd zonder dat er een API-aanroep wordt ontvangen.

---

## <a name="delete-azure-resources"></a>Azure-resources verwijderen

Als u een van de Azure-resources verwijdert die voor uw QnA Maker Knowledge bases worden gebruikt, werken de kennis bases niet meer. Voordat u een resource verwijdert, moet u de kennis bases exporteren vanaf de pagina **instellingen** .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [app service](../../../app-service/index.yml) en de [Zoek service](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Meer informatie over hoe u met anderen kunt ontwerpen](../index.yml)
