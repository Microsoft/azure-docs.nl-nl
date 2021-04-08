---
title: QnA Maker-service configureren-QnA Maker
description: Dit document bevat een overzicht van geavanceerde configuraties voor uw QnA Maker-resources.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102220128"
---
# <a name="configure-qna-maker-resources"></a>QnA Maker-resources configureren

De gebruiker kan QnA Maker configureren voor het gebruik van een andere cognitieve Zoek resource. Ze kunnen ook app service-instellingen configureren als ze QnA Maker GA gebruiken.

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
