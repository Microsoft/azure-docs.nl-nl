---
title: Implementatie tokens opnieuw instellen in statische Web Apps van Azure (preview)
description: Tokens opnieuw instellen op een statische Web Apps-site van Azure
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745538"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Implementatie tokens opnieuw instellen in statische Web Apps van Azure (preview)

Wanneer u een nieuwe Azure static Web Apps-site maakt, genereert Azure een token dat wordt gebruikt om de toepassing te identificeren tijdens de implementatie. Tijdens het inrichten wordt dit token opgeslagen als een geheim in de GitHub-opslag plaats. In dit artikel wordt uitgelegd hoe u dit token kunt gebruiken en beheren.

Normaal gesp roken hoeft u zich geen zorgen te maken over het implementatie token, maar hier volgen enkele redenen waarom u het token zou moeten ophalen of opnieuw instellen.

* **Inbreuk op tokens**: Stel uw token opnieuw in als deze wordt blootgesteld aan een externe partij.
* **Implementeren vanuit een afzonderlijke github-opslag plaats**: als u hand matig implementeert vanuit een afzonderlijke github-opslag plaats, moet u het implementatie token instellen in de nieuwe opslag plaats.

## <a name="prerequisites"></a>Vereisten

- Een bestaande GitHub-opslag plaats die is geconfigureerd met statische Azure-Web Apps.
- Zie [uw eerste statische app bouwen](getting-started.md) als u er nog geen hebt.

## <a name="reset-a-deployment-token"></a>Een implementatie token opnieuw instellen

1. Klik op de koppeling **implementatie token beheren** op de pagina _overzicht_ van uw Azure static web apps-site.

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="Implementatie token beheren":::

1. Klik op de knop **token opnieuw instellen** .

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="Implementatie token opnieuw instellen":::

1. Nadat u een nieuw token in het veld _implementatie token_ hebt weer gegeven, kopieert u het token door te klikken op **kopiëren naar het klem bord** -pictogram.


## <a name="update-a-secret-in-the-github-repository"></a>Een geheim bijwerken in de GitHub-opslag plaats

Als u de geautomatiseerde implementatie wilt blijven uitvoeren, moet u na het opnieuw instellen van een token de nieuwe waarde instellen in de bijbehorende GitHub-opslag plaats.

1. Navigeer naar de opslag plaats van uw project op GitHub en klik op het tabblad **instellingen** .
1. Klik op het menu-item **geheimen** . U vindt een geheim dat is gegenereerd tijdens het inrichten van een statische web-app met de naam _AZURE_STATIC_WEB_APPS_API_TOKEN_... in het gedeelte _opslagplaats geheimen_ .

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="Archief geheimen weer geven":::

    > [!NOTE]
    > Als u de Azure static Web Apps-site hebt gemaakt voor meerdere vertakkingen van deze opslag plaats, ziet u meerdere _AZURE_STATIC_WEB_APPS_API_TOKEN_... geheimen in deze lijst. Selecteer het juiste schema door te voldoen aan de bestands naam die wordt weer gegeven in het veld _werk stroom bewerken_ op het tabblad _overzicht_ van de statische web apps-site.

1. Klik op de knop **bijwerken** om de editor te openen.
1. **Plak de waarde** van het implementatie token in het veld _waarde_ .
1. Klik op **geheim bijwerken**.

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="Geheim van opslag plaats bijwerken":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Publiceren vanuit een statische sitegenerator](publish-gatsby.md)
