---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 10/15/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8b3c1992a1cff18390f9d1332103e0650af418e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97347715"
---
## <a name="prepare-your-web-app"></a>Uw web-app voorbereiden

Als u aangepaste TLS-/SSL-bindingen wilt maken of clientcertificaten wilt inschakelen voor uw App Service-app, moet uw [App Service-plan](https://azure.microsoft.com/pricing/details/app-service/) zich bevinden in de laag **Basic**, **Standard**, **Premium** of **Isolated**. In deze stap zorgt u ervoor dat de web-app zich in de ondersteunde prijscategorie bevindt.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Open [Azure Portal](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navigeer naar uw web-app

Zoek en selecteer **App Services**.

![App Services selecteren](./media/app-service-ssl-prepare-app/app-services.png)

Selecteer op de pagina **App Services** de naam van uw web-app.

![Schermopname van de App Services-pagina in Azure Portal met een lijst van alle actieve web-apps, waarbij de eerste app in de lijst is gemarkeerd.](./media/app-service-ssl-prepare-app/select-app.png)

U bevindt zich op de beheerpagina van uw web-app.  

### <a name="check-the-pricing-tier"></a>Controleer de prijscategorie

Scrol in de navigatiebalk links van de web-app-pagina naar het gedeelte **Instellingen** en selecteer **Opschalen (App Service-plan)**.

![Menu Opschalen](./media/app-service-ssl-prepare-app/scale-up-menu.png)

Controleer of de web-app zich niet in de laag **F1** of **D1** bevindt. De huidige categorie van de web-app is gemarkeerd met een donkerblauw vak.

![Controleer prijscategorie](./media/app-service-ssl-prepare-app/check-pricing-tier.png)

Aangepaste SSL wordt niet ondersteund in de laag **F1** of **D1**. Als u omhoog moet schalen, volgt u de stappen in het volgende gedeelte. Sluit anders de pagina **Omhoog schalen** en sla de sectie [Uw App Service-plan omhoog schalen](#scale-up-your-app-service-plan) over.

### <a name="scale-up-your-app-service-plan"></a>Uw App Service-plan omhoog schalen

Selecteer een van de lagen die niet gratis zijn (**B1**, **B2**, **B3** of een laag in de categorie **Productie**). Klik op **Aanvullende opties bekijken** voor aanvullende opties.

Klik op **Toepassen**.

![Prijscategorie kiezen](./media/app-service-ssl-prepare-app/choose-pricing-tier.png)

Wanneer u de volgende melding ziet, is de schaalbewerking voltooid.

![Melding voor omhoog schalen](./media/app-service-ssl-prepare-app/scale-notification.png)

