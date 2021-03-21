---
title: Beveiligings headers configureren met de regel instellingen voor de Azure front-deur standaard/Premium (preview-versie)
description: Dit artikel bevat richt lijnen voor het gebruik van regel sets voor het configureren van beveiligings headers.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c08ba57f43969bb2f0ee9c66b6cb4e92879ed258
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099108"
---
# <a name="configure-security-headers-with-azure-front-door-standardpremium-preview-rule-set"></a>Beveiligings headers configureren met de regel instellingen voor de Azure front-deur standaard/Premium (preview-versie)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

In dit artikel wordt uitgelegd hoe u beveiligings headers implementeert om te voor komen dat beveiligings problemen op basis van browsers zoals HTTP strict-Trans Port-Security (HSTS), X-XSS-beveiliging, Content-Security-Policy of X-frame-Options. Kenmerken op basis van beveiliging kunnen ook worden gedefinieerd met cookies.

In het volgende voor beeld ziet u hoe u een Content-Security-Policy header toevoegt aan alle inkomende aanvragen die overeenkomen met het pad in de route. Hier worden alleen scripts van onze vertrouwde site, **https://apiphany.portal.azure-api.net** , toegestaan om op onze toepassing te worden uitgevoerd.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Voordat u beveiligings headers configureren kunt configureren, moet u eerst een front-deur maken. Raadpleeg [Snelstartgids: Een Front Door maken](create-front-door-portal.md) voor meer informatie.
* Lees hoe u [een regelset instelt](how-to-configure-rule-set.md) als u de functie regelset niet eerder hebt gebruikt.

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Een Content-Security-Policy-koptekst toevoegen in de Azure-portal

1. Ga naar het Standard/Premium-profiel voor de Azure front-deur en selecteer de **regel set** onder **instellingen.**

1. Selecteer **toevoegen** om een nieuwe regelset toe te voegen. Geef een **naam** op voor de regel en geef een **naam** op voor de regel. Selecteer **een actie toevoegen** en selecteer vervolgens **antwoord header**.

1. Stel de **operator in op toevoegen om deze** header toe te voegen als reactie op alle inkomende aanvragen voor deze route.

1. Voeg de naam van de header toe: **Content-Security-Policy** en definieer de waarden die deze header moet accepteren. In dit scenario kiezen we *' script-src ' zelf https://apiphany.portal.azure-api.net*'.

1. Wanneer u alle regels hebt toegevoegd die u wilt configureren, vergeet dan niet om de regelset te koppelen aan een route. Deze stap is *vereist* om de regelset in staat te stellen actie te ondernemen. 

> [!NOTE]
> In dit scenario hebben we geen [voorwaarden voor overeenkomst](concept-rule-set-match-conditions.md) aan de regel toegevoegd. Deze regel wordt toegepast op alle inkomende aanvragen die overeenkomen met het pad dat in de bijbehorende route is gedefinieerd. Als deze alleen wilt toepassen op een subset van die aanvragen, moet u uw specifieke **voorwaarden voor overeenkomst** aan deze regel toevoegen.

## <a name="clean-up-resources"></a>Resources opschonen

### <a name="deleting-a-rule"></a>Een regel verwijderen

In de voor gaande stappen hebt u de header Content-Security-Policy geconfigureerd met Rule set. Als u geen regel meer wilt, kunt u de naam van de regelset selecteren en vervolgens regel verwijderen selecteren. 

### <a name="deleting-a-rule-set"></a>Een regelset verwijderen

Als u een regelset wilt verwijderen, moet u deze loskoppelen van alle routes voordat u deze verwijdert. Raadpleeg [uw regelset configureren](how-to-configure-rule-set.md)voor gedetailleerde richt lijnen voor het verwijderen van een regel set.

## <a name="next-steps"></a>Volgende stappen

Zie [Web Application firewall en front deur](../../web-application-firewall/afds/afds-overview.md)voor meer informatie over het configureren van een firewall voor webtoepassingen voor uw voor deur.
