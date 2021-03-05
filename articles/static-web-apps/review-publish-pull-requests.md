---
title: Pull-aanvragen in de pre-productie omgevingen in azure static controleren Web Apps
description: Meer informatie over het gebruik van pre-productie omgevingen om wijzigingen in de pull-aanvragen in azure static Web Apps te controleren.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: 1cb5f2f9ac941001efb18301f4a54bd0092920ba
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172737"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>Pull-aanvragen in de pre-productieomgevingen controleren in de preview-versie van Azure Static Web Apps

In dit artikel wordt beschreven hoe u pre-productie omgevingen gebruikt om wijzigingen te bekijken in toepassingen die zijn geïmplementeerd met [statische web apps van Azure](overview.md).

Een pre-productie omgeving (staging) is een volledig functionele, gefaseerde versie van uw toepassing die wijzigingen bevat die niet beschikbaar zijn in de productie.

Azure static Web Apps genereert een werk stroom voor GitHub acties in de opslag plaats. Wanneer een pull-aanvraag wordt gemaakt op basis van een vertakking die door de werk stroom wordt uitgevoerd, wordt de pre-productie omgeving gebouwd. De pre-productie omgeving faseert de app, stelt u in staat om beoordelingen uit te voeren voordat naar productie wordt gepusht.

Meerdere pre-productie omgevingen kunnen tegelijkertijd naast elkaar bestaan met behulp van statische Web Apps van Azure. Telkens wanneer u een pull-aanvraag voor de gevolgde vertakking maakt, wordt een gefaseerde versie met uw wijzigingen geïmplementeerd in een afzonderlijke pre-productie omgeving.

Er zijn veel voor delen van het gebruik van pre-productie omgevingen. U kunt bijvoorbeeld:

- Bekijk visuele wijzigingen tussen productie en fase ring. Bijvoorbeeld het weer geven van updates voor inhoud en lay-out.
- De wijzigingen in uw team demonstreren.
- Vergelijk verschillende versies van uw toepassing.
- Wijzigingen valideren met behulp van acceptatie tests.
- Voer Sanity controles uit voordat u implementeert voor productie.

> [!NOTE]
> Tijdens de preview-periode zijn [Maxi maal drie faserings omgevingen](quotas.md) toegestaan.

## <a name="prerequisites"></a>Vereisten

- Een bestaande GitHub-opslag plaats die is geconfigureerd met statische Azure-Web Apps. Zie [uw eerste statische app bouwen](getting-started.md) als u er nog geen hebt.

## <a name="make-a-change"></a>Een wijziging aanbrengen

Begin door een wijziging aan te brengen in uw opslag plaats. U kunt dit rechtstreeks doen op GitHub, zoals wordt weer gegeven in de volgende stappen.

1. Navigeer naar de opslag plaats van uw project op GitHub en klik vervolgens op de **vertakkings** knop om een nieuwe vertakking te maken.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="Nieuwe vertakking maken met behulp van de GitHub-interface":::]

    Typ de naam van een vertakking en klik op **Branch maken**.

1. Ga naar de map van uw _app_ en wijzig de tekst inhoud. U kunt bijvoorbeeld een titel of alinea wijzigen. Wanneer u het bestand dat u wilt bewerken hebt gevonden, klikt u op **bewerken** om de wijziging door te voeren.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="De knop bestand bewerken in de GitHub-interface":::

1. Nadat u de wijzigingen hebt aangebracht, klikt u op **wijzigingen door voeren** om uw wijzigingen door te voeren in de vertakking.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="Knop wijzigingen door voeren in de GitHub-interface":::

## <a name="create-a-pull-request"></a>Een pull-aanvraag maken

Maak vervolgens een pull-aanvraag van deze wijziging.

1. Open het tabblad **pull-aanvraag** van het project op github:

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="Tabblad pull-aanvraag in een GitHub-opslag plaats":::

1. Klik op de knop **& pull-aanvraag vergelijken** van uw vertakking.

1. U kunt eventueel enkele details over uw wijzigingen invullen en vervolgens op **pull-aanvraag maken** klikken.

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="Pull-aanvraag maken in GitHub":::

U kunt revisoren toewijzen en opmerkingen toevoegen om uw wijzigingen zo nodig te bespreken.

> [!NOTE]
> U kunt meerdere wijzigingen aanbrengen door nieuwe door voeringen naar uw vertakking te pushen. De pull-aanvraag wordt vervolgens automatisch bijgewerkt om alle wijzigingen weer te geven.

## <a name="review-changes"></a>Wijzigingen controleren

Nadat de pull-aanvraag is gemaakt, wordt de implementatie werk stroom [github acties](https://github.com/features/actions) uitgevoerd en worden uw wijzigingen geïmplementeerd in een pre-productie omgeving.

Zodra de werk stroom klaar is met het maken en implementeren van uw app, voegt de GitHub-bot een opmerking toe aan uw pull-aanvraag, die de URL van de pre-productie omgeving bevat. U kunt op deze link klikken om uw gefaseerde wijzigingen te zien.

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="Pull-aanvraag opmerking met de pre-productie-URL":::

Klik op de gegenereerde URL om de wijzigingen weer te geven.

Als u de URL nader bekeken ziet, kunt u zien dat deze als volgt is samengesteld: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net` .

Voor een bepaalde pull-aanvraag blijft de URL hetzelfde, zelfs als u nieuwe updates pusht. Naast de URL die constant blijft, wordt dezelfde pre-productie omgeving opnieuw gebruikt voor de levens duur van de pull-aanvraag.

## <a name="publish-changes"></a>Wijzigingen publiceren

Zodra de wijzigingen zijn goedgekeurd, kunt u uw wijzigingen in de productie publiceren door de pull-aanvraag samen te voegen.

Klik op **pull-aanvraag samen voegen**:

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="De knop pull-aanvraag samen voegen in de GitHub-interface":::

Bij het samen voegen worden uw wijzigingen in de getraceerde vertakking (de ' productie ' vertakking) gekopieerd. Vervolgens wordt de implementatie werk stroom gestart op de getraceerde vertakking en de wijzigingen zijn actief nadat uw toepassing opnieuw is gemaakt.

Als u de wijzigingen in de productie wilt controleren, opent u uw productie-URL om de live-versie van de website te laden.

## <a name="limitations"></a>Beperkingen

Gefaseerde versies van uw toepassing zijn momenteel openbaar op basis van hun URL, zelfs als uw GitHub-opslag plaats privé is.

> [!WARNING]
> Wees voorzichtig bij het publiceren van gevoelige inhoud naar gefaseerde versies, omdat de toegang tot de pre-productie omgevingen niet is beperkt.

Het aantal pre-productie omgevingen dat beschikbaar is voor elke app die met statische Web Apps wordt geïmplementeerd, is afhankelijk van de SKU-laag die u gebruikt. Met de gratis laag kunt u naast de productie omgeving bijvoorbeeld drie pre-productie omgevingen hebben.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een aangepast domein instellen](custom-domain.md)
