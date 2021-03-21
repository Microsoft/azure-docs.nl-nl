---
title: 'Zelfstudie: Beveiligingsheaders toevoegen met de engine voor regels - Azure Front Door'
description: In deze zelfstudie leert u hoe u een beveiligingskoptekst configureert via een Engine voor regels in Azure Front Door
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 1727193faa028a1d681f2a74df950afeb9570ec9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91270046"
---
# <a name="tutorial-add-security-headers-with-rules-engine"></a>Zelfstudie: Beveiligingsheaders toevoegen met de engine voor regels

In deze zelfstudie ziet u hoe u beveiligingskopteksten implementeert om beveiligingsproblemen in browsers te voorkomen, bijvoorbeeld HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy of X-Frame-Options. Kenmerken op basis van beveiliging kunnen ook worden gedefinieerd met cookies.

In het volgende voorbeeld ziet u hoe u een Content-Security-Policy-koptekst kunt toevoegen aan alle inkomende aanvragen die overeenkomen met het pad dat is opgegeven in de route waaraan de configuratie van uw regelengine is gekoppeld. Hier worden alleen scripts van onze vertrouwde site, **https://apiphany.portal.azure-api.net** , toegestaan om op onze toepassing te worden uitgevoerd.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een Content-Security-Policy configureren in de regelengine.

## <a name="prerequisites"></a>Vereisten

* Voordat u de stappen in deze zelfstudie kunt voltooien, moet u een Front Door maken. Raadpleeg [Snelstartgids: Een Front Door maken](quickstart-create-front-door.md) voor meer informatie.
* Als dit de eerste keer is dat u de regelenginefunctie gebruikt, raadpleegt u [Een regelengine instellen](front-door-tutorial-rules-engine.md).

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Een Content-Security-Policy-koptekst toevoegen in de Azure-portal

1. Klik op **Toevoegen** als u een nieuwe regel wilt toevoegen. Geef een naam op voor de regel en klik op **Een actie toevoegen** > **Antwoordheader**.

1. Stel de operator in op **Toevoegen** om deze header toe te voegen als antwoord op alle inkomende aanvragen voor deze route.

1. Voeg de naam van de header toe: **Content-Security-Policy** en definieer de waarden die deze header moet accepteren. In dit scenario kiezen we *"script-src 'self' https://apiphany.portal.azure-api.net."*

1. Wanneer u alle regels hebt toegevoegd die u voor uw configuratie wilt gebruiken, gaat u naar uw route van voorkeur en koppelt u de configuratie van uw regelengine aan uw routeregel. Deze stap is vereist om de regel te laten werken. 

![voorbeeld van portal](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> In dit scenario hebben we geen [voorwaarden voor overeenkomst](front-door-rules-engine-match-conditions.md) aan de regel toegevoegd. Deze regel wordt toegepast op alle inkomende aanvragen die overeenkomen met het pad dat in de routeregel is gedefinieerd. Als deze alleen wilt toepassen op een subset van die aanvragen, moet u uw specifieke **voorwaarden voor overeenkomst** aan deze regel toevoegen.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u beveiligingsheaders geconfigureerd met de regelengine. Als u de regel niet meer wilt, kunt u deze verwijderen door op Regel verwijderen te klikken.

:::image type="content" source="./media/front-door-rules-engine/rules-engine-delete-rule.png" alt-text="Regel verwijderen":::

## <a name="next-steps"></a>Volgende stappen

Ga verder met de volgende zelfstudie voor meer informatie over het configureren van een Web Application Firewall voor uw Front Door.

> [!div class="nextstepaction"]
> [Web Application Firewall en Front Door](front-door-waf.md)
