---
title: Een QnA Maker-service instellen-QnA Maker
description: Voordat u QnA Maker Knowledge bases kunt maken, moet u eerst een QnA Maker service in azure instellen. Iedereen met een machtiging voor het maken van nieuwe resources in een abonnement kan een QnA Maker-service instellen.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 4e09f9b8564c9319e68984df1c0f8db7a496a6d0
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584801"
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
    * Voor kosten besparingen kunt u enkele, maar niet alle Azure-resources die zijn gemaakt voor QnA Maker [delen](#configure-qna-maker-to-use-different-cognitive-search-resource) .

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

## <a name="find-authoring-keys-in-the-azure-portal"></a>Zoek sleutels zoeken in de Azure Portal

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

U kunt uw ontwerp sleutels bekijken en opnieuw instellen in de Azure Portal, waar u de QnA Maker resource hebt gemaakt. Deze sleutels kunnen worden aangeduid als abonnements sleutels.

1. Ga naar de QnA Maker resource in de Azure Portal en selecteer de resource met het _Cognitive Services_ type:

    ![QnA Maker Resource lijst](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Ga naar **sleutels en eind punt**:

    ![QnA Maker Managed (preview)-abonnements sleutel](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="find-query-endpoint-keys-in-the-qna-maker-portal"></a>Zoek eindpunt sleutels zoeken in de QnA Maker Portal

Het eind punt bevindt zich in dezelfde regio als de resource omdat de eindpunt sleutels worden gebruikt voor het aanroepen van de Knowledge Base.

Eindpunt sleutels kunnen worden beheerd vanuit de [QnA Maker Portal](https://qnamaker.ai).

1. Meld u aan bij de [QnA Maker-Portal](https://qnamaker.ai), ga naar uw profiel en selecteer vervolgens service- **instellingen**:

    ![Eindpunt sleutel](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Uw sleutels weer geven of herstellen:

    > [!div class="mx-imgBorder"]
    > ![Eindpunt sleutel beheer](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Vernieuw uw sleutels als u denkt dat ze zijn aangetast. Hiervoor kunnen eventueel bijbehorende wijzigingen in de client toepassing of bot code worden vereist.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

U kunt uw ontwerp sleutels bekijken en opnieuw instellen in de Azure Portal, waar u de resource voor QnA Maker Managed (preview) hebt gemaakt. Deze sleutels kunnen worden aangeduid als abonnements sleutels.

1. Ga naar de resource QnA Maker Managed (preview) in de Azure Portal en selecteer de resource met het *Cognitive Services* type:

    ![Resource lijst voor door QnA Maker beheerde (preview-versie)](../media/qnamaker-how-to-key-management/qnamaker-v2-resource-list.png)

2. Ga naar **sleutels en eind punt**:

    ![QnA Maker Managed (preview)-abonnements sleutel](../media/qnamaker-how-to-key-management/subscription-key-v2.png)

### <a name="update-the-resources"></a>De resources bijwerken

Meer informatie over het bijwerken van de resources die worden gebruikt door uw Knowledge Base. QnA Maker Managed (preview) is **gratis** tijdens de preview-versie. 

---

### <a name="recommended-settings-for-network-isolation"></a>Aanbevolen instellingen voor netwerk isolatie

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

1. Beveilig de cognitieve service resource van open bare toegang door [het virtuele netwerk te configureren](../../cognitive-services-virtual-networks.md?tabs=portal).
2. Beveilig App Service (QnA runtime) van open bare toegang.

   ##### <a name="add-ips-to-app-service-allowlist"></a>Ip's toevoegen aan App Service allowlist

    * Alleen verkeer van de cognitieve service Ip's toestaan. Deze zijn al opgenomen in de service tag `CognitiveServicesManagement` . Dit is vereist voor het ontwerpen van Api's (Create/update KB) om de app service te kunnen aanroepen en Azure Search-service dienovereenkomstig bij te werken. Bekijk [meer informatie over service tags.](../../../virtual-network/service-tags-overview.md)
    * Zorg ervoor dat u ook andere toegangs punten als bot-service, QnA Maker-Portal (mogelijk uw Corpnet), enzovoort kunt gebruiken. voor de voor spelling ' GenerateAnswer ' API-toegang.
    * Voer de volgende stappen uit om de IP-adresbereiken toe te voegen aan een allowlist:

      * Down load [de IP-bereiken voor alle service Tags](https://www.microsoft.com/download/details.aspx?id=56519).
      * Selecteer de IP-adressen van ' CognitiveServicesManagement '.
      * Navigeer naar het gedeelte netwerken van uw App Service-bron en klik op de optie toegangs beperking configureren om de IP-adressen toe te voegen aan een allowlist.

    We hebben ook een geautomatiseerd script om hetzelfde te doen voor uw App Service. U kunt het [Power shell-script vinden voor het configureren van een allowlist](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) op github. U moet abonnements-id, resource groep en werkelijke App Service naam invoeren als script parameters. Wanneer het script wordt uitgevoerd, worden de IP-adressen automatisch toegevoegd aan App Service allowlist.

    ##### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>App Service Environment voor het hosten van QnA Maker configureren App Service
    De App Service Environment (ASE) kan worden gebruikt om QnA Maker app service te hosten. Volg de onderstaande stappen:

    1. Maak een App Service Environment en markeer deze als ' Extern '. Volg de [zelf studie](../../../app-service/environment/create-external-ase.md) voor instructies.
    2.  Maak een app-service in de App Service Environment.
        * Controleer de configuratie voor de app service en voeg ' PrimaryEndpointKey ' toe als een toepassings instelling. De waarde voor ' PrimaryEndpointKey ' moet worden ingesteld op ' \<app-name\> -PrimaryEndpointKey '. De naam van de app wordt gedefinieerd in de app service-URL. Als de app service-URL bijvoorbeeld ' mywebsite.myase.p.azurewebsite.net ' is, is de app-naam ' MyWebSite '. In dit geval moet de waarde voor ' PrimaryEndpointKey ' worden ingesteld op ' mywebsite-PrimaryEndpointKey '.
        * Maak een Azure Search-service.
        * Zorg ervoor dat Azure Search en app-instellingen op de juiste wijze zijn geconfigureerd. 
          Volg deze [zelf studie](../reference-app-service.md?tabs=v1#app-service).
    3.  De netwerk beveiligings groep die is gekoppeld aan de App Service Environment, bijwerken
        * Werk vooraf gemaakte regels voor inkomende beveiliging bij volgens uw vereisten.
        * Voeg een nieuwe regel voor binnenkomende beveiliging met bron als servicetag en bron service-tag toe als ' CognitiveServicesManagement '.
    4.  Maak een QnA Maker cognitieve service-exemplaar (micro soft. CognitiveServices/accounts) met behulp van Azure Resource Manager, waarbij QnA Maker eind punt moet worden ingesteld op het App Service eind punt dat hierboven is gemaakt (https://mywebsite.myase.p.azurewebsite.net).
    
3. Cognitive Search configureren als een persoonlijk eind punt in een VNET

    Wanneer een zoek instantie wordt gemaakt tijdens het maken van een QnA Maker bron, kunt u Cognitive Search voor het ondersteunen van een persoonlijke eindpunt configuratie die volledig is gemaakt in het VNet van de klant.

    Alle resources moeten in dezelfde regio worden gemaakt om een persoonlijk eind punt te kunnen gebruiken.

    * QnA Maker resource
    * nieuwe Cognitive Search resource
    * nieuwe Virtual Network resource

    Voer de volgende stappen uit in de [Azure Portal](https://portal.azure.com):

    1. Maak een [QnA Maker resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).
    1. Maak een nieuwe Cognitive Search resource waarvan de eindpunt verbinding (gegevens) is ingesteld op _privé_. Maak de resource in dezelfde regio als de QnA Maker resource die u in stap 1 hebt gemaakt. Meer informatie over het [maken van een Cognitive Search resource](../../../search/search-create-service-portal.md)en vervolgens met behulp van deze koppeling rechtstreeks naar de [pagina maken van de resource](https://ms.portal.azure.com/#create/Microsoft.Search)gaan.
    1. Maak een nieuwe [Virtual Network resource](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM).
    1. Configureer het VNET op de app service-resource die u hebt gemaakt in stap 1 van deze procedure.
        1. Maak een nieuwe DNS-vermelding in het VNET voor een nieuwe Cognitive Search resource die u in stap 2 hebt gemaakt. naar het IP-adres van de Cognitive Search.
    1. [Koppel de app service aan de nieuwe Cognitive Search resource die u](#configure-qna-maker-to-use-different-cognitive-search-resource) in stap 2 hebt gemaakt. Vervolgens kunt u de oorspronkelijke Cognitive Search resource verwijderen die u in stap 1 hebt gemaakt.

    Maak uw eerste Knowledge Base in de [QnA Maker Portal](https://www.qnamaker.ai/).

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

1. Beveilig de cognitieve service resource van open bare toegang door [het virtuele netwerk te configureren](../../cognitive-services-virtual-networks.md?tabs=portal).
2. [Maak persoonlijke eind punten](../reference-private-endpoint.md) aan de Azure Search resource.

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

## <a name="configure-azure-resources"></a>Azure-resources configureren

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>QnA Maker configureren voor het gebruik van verschillende Cognitive Search resource

Als u een QnA-service en de bijbehorende afhankelijkheden (zoals zoeken) maakt via de portal, wordt er een zoek service voor u gemaakt en gekoppeld aan de QnA Maker-service. Nadat deze resources zijn gemaakt, kunt u de App Service-instelling bijwerken om een eerder bestaande zoek service te gebruiken en de toepassing verwijderen die u zojuist hebt gemaakt.

De **app service** resource van QnA Maker maakt gebruik van de Cognitive Search resource. Als u de Cognitive Search resource wilt wijzigen die door QnA Maker wordt gebruikt, moet u de instelling wijzigen in de Azure Portal.

1. Haal de **beheerders sleutel** en de **naam** op van de Cognitive Search resource die u QnA Maker wilt gebruiken.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en zoek de **app service** die aan uw QnA Maker-resource zijn gekoppeld. Beide met dezelfde naam hebben.

1. Selecteer **instellingen** en vervolgens **configuratie**. Hiermee worden alle bestaande instellingen voor de App Service van de QnA Maker weer gegeven.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van Azure Portal App Service configuratie-instellingen worden weer gegeven](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Wijzig de waarden voor de volgende sleutels:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Als u de nieuwe instellingen wilt gebruiken, moet u de app service opnieuw starten. Selecteer **overzicht** en selecteer **opnieuw opstarten**.

    > [!div class="mx-imgBorder"]
    > ![Scherm opname van Azure Portal App Service opnieuw opstarten nadat de configuratie-instellingen zijn gewijzigd](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Als u een QnA-service maakt via Azure Resource Manager sjablonen, kunt u alle resources maken en de App Service maken om een bestaande zoek service te gebruiken.

Meer informatie over het configureren van de App Service [Toepassings instellingen](../../../app-service/configure-common.md#configure-app-settings).

### <a name="get-the-latest-runtime-updates"></a>Down load de meest recente runtime-updates

De QnAMaker-runtime maakt deel uit van het Azure App Service-exemplaar dat wordt geïmplementeerd wanneer u [een QnAMaker-service](./set-up-qnamaker-service-azure.md) in de Azure Portal maakt. Er worden regel matig updates uitgevoerd voor de runtime. Het QnA Maker App Service-exemplaar bevindt zich in de modus automatisch bijwerken na de site-extensie release van april 2019 (versie 5 +). Deze update is ontworpen om te zorgen dat er geen downtime is tijdens de upgrade.

U kunt uw huidige versie controleren op https://www.qnamaker.ai/UserSettings . Als uw versie ouder is dan versie 5. x, moet u App Service opnieuw opstarten om de meest recente updates toe te passen:

1. Ga naar de QnAMaker-service (resource groep) in het [Azure Portal](https://portal.azure.com).

    > [!div class="mx-imgBorder"]
    > ![Azure-resource groep QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecteer de App Service instantie en open de sectie **overzicht** .

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service-instantie](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. Start App Service opnieuw. Het update proces moet binnen een paar seconden worden voltooid. Alle afhankelijke toepassingen of botsingen die gebruikmaken van deze QnAMaker-service, zijn niet beschikbaar voor eind gebruikers tijdens de herstart periode.

    ![Het QnAMaker App Service-exemplaar opnieuw starten](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>De instelling niet-actieve app service configureren om een time-out te voor komen

De app service, die de QnA Maker Voorspellings runtime voor een gepubliceerde kennis database gebruikt, heeft een time-outconfiguratie voor inactiviteit, die standaard automatisch een time-out krijgt als de service niet actief is. In het geval van QnA Maker betekent dit dat uw prediction runtime generateAnswer-API af en toe na Peri Oden van geen verkeer kan worden uitgevoerd.

Als u de app voor Voorspellings eindpunt wilt laden, zelfs wanneer er geen verkeer is, stelt u de niet-actief in op altijd aan.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zoek en selecteer de app service van uw QnA Maker-resource. Deze heeft dezelfde naam als de QnA Maker resource, maar heeft een ander **type** app service.
1. Zoek **instellingen** en selecteer **configuratie**.
1. In het deel venster configuratie selecteert u **algemene instellingen**, vervolgens **altijd zoeken in** **en selecteert u** als waarde.

    > [!div class="mx-imgBorder"]
    > ![Selecteer in het deel venster configuratie de optie * * algemene instellingen * *, zoek * * always on * * en selecteer * * op * * als waarde.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Selecteer **Opslaan** om de configuratie op te slaan.
1. U wordt gevraagd of u de app opnieuw wilt starten voor het gebruik van de nieuwe instelling. Selecteer **Doorgaan**.

Meer informatie over het configureren van de App Service [algemene instellingen](../../../app-service/configure-common.md#configure-general-settings).

### <a name="business-continuity-with-traffic-manager"></a>Bedrijfs continuïteit met Traffic Manager

Het hoofd doel van het plan voor bedrijfs continuïteit is het maken van een robuust eind punt van de Knowledge Base, waardoor er geen verdere tijd is voor de bot of de toepassing die deze gebruikt.

> [!div class="mx-imgBorder"]
> ![QnA Maker BCP-abonnement](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Het idee op hoog niveau zoals hierboven wordt weer gegeven, is als volgt:

1. Stel twee parallelle [QnA Maker Services](set-up-qnamaker-service-azure.md) in in [Azure gekoppelde regio's](../../../best-practices-availability-paired-regions.md).

1. [Back-up maken](../../../app-service/manage-backup.md) van uw primaire QnA Maker app-service en deze [herstellen](../../../app-service/web-sites-restore.md) in de secundaire installatie. Dit zorgt ervoor dat beide instellingen werken met dezelfde hostnaam en sleutels.

1. Zorg ervoor dat de primaire en secundaire Azure Search-indexen synchroon blijven. Gebruik het GitHub-voor beeld [hier](https://github.com/pchoudhari/QnAMakerBackupRestore) om te zien hoe u een back-up maakt van Azure-indexen.

1. Maak een back-up van de Application Insights met [doorlopend exporteren](../../../azure-monitor/app/export-telemetry.md).

1. Zodra de primaire en secundaire Stacks zijn ingesteld, gebruikt u [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) om de twee eind punten te configureren en een routerings methode in te stellen.

1. U moet een Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), maken van het certificaat voor uw Traffic Manager-eind punt. [BIND het TLS/SSL-certificaat](../../../app-service/configure-ssl-bindings.md) in uw app-Services.

1. Ten slotte gebruikt u het Traffic Manager-eind punt in uw bot of app.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>QnA Maker Managed (preview)-service configureren voor het gebruik van verschillende Cognitive Search resource

Als u een QnA-service (preview) en de bijbehorende afhankelijkheden (zoals zoeken) maakt via de portal, wordt er een zoek service voor u gemaakt en gekoppeld aan de QnA Maker beheerde service (preview). Nadat deze resources zijn gemaakt, kunt u de zoek service bijwerken op het tabblad **configuratie** .

1. Ga naar de service Managed QnA Maker (preview) in de Azure Portal.

1. Selecteer **configuratie** en selecteer de Azure Cognitive Search-service die u wilt koppelen aan uw QnA Maker Managed (preview)-service.

    ![Scherm afbeelding van de configuratie pagina van QnA Maker Managed (preview)](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. Klik op **Opslaan**.

> [!NOTE]
> Als u de Azure Search-service wijzigt die aan QnA Maker is gekoppeld, verliest u de toegang tot alle Knowledge bases die al aanwezig zijn. Zorg ervoor dat u de bestaande kennis grondslagen exporteert voordat u de Azure Search service wijzigt.

---

## <a name="delete-azure-resources"></a>Azure-resources verwijderen

Als u een van de Azure-resources verwijdert die voor uw QnA Maker Knowledge bases worden gebruikt, werken de kennis bases niet meer. Voordat u een resource verwijdert, moet u de kennis bases exporteren vanaf de pagina **instellingen** .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [app service](../../../app-service/index.yml) en de [Zoek service](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Meer informatie over hoe u met anderen kunt ontwerpen](../index.yml)
