---
title: Overzicht van Service Health | Microsoft Docs
description: Meer informatie over hoe Service Health u een aanpasbaar dash board biedt, waarmee u de status van uw Azure-Services kunt bijhouden in de regio's waar u ze gebruikt.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 8246b0ab93b95c13858e4ff96d0f24b255d05e55
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90967796"
---
# <a name="service-health-overview"></a>Overzicht van Service Health

Service Health biedt u een aanpasbaar dash board waarmee u de status van uw Azure-Services kunt bijhouden in de regio's waar u ze gebruikt. In dit dash board kunt u actieve gebeurtenissen volgen, zoals lopende Service problemen, gepland gepland onderhoud of relevante status adviezen. Wanneer gebeurtenissen inactief worden, worden deze tot 90 dagen in uw status geschiedenis geplaatst. Ten slotte kunt u het Service Health-dash board gebruiken om service status waarschuwingen te maken en te beheren die u proactief waarschuwen wanneer Service problemen van invloed zijn.

## <a name="service-health-events"></a>Service Health gebeurtenissen

Service Health houdt vier typen status gebeurtenissen bij die van invloed kunnen zijn op uw resources:

1. **Service problemen** : problemen in de Azure-Services die nu van invloed zijn op u. 
2. **Gepland onderhoud** : aanstaande onderhouds werkzaamheden die in de toekomst van invloed kunnen zijn op de beschik baarheid van uw services.  
3. **Status adviseur** -wijzigingen in Azure-Services die uw aandacht vereisen. Voor beelden hiervan zijn de afschaffing van Azure-functies of upgrade vereisten (bijvoorbeeld upgrades naar een ondersteund PHP-Framework).
4. **Beveiligings adviezen** : beveiligings-gerelateerde meldingen of schendingen die van invloed kunnen zijn op de beschik baarheid van uw Azure-Services.

> [!NOTE]
> Voor het weer geven van Service Health gebeurtenissen moeten gebruikers [de rol van lezer krijgen](../role-based-access-control/role-assignments-portal.md) voor een abonnement.

## <a name="get-started-with-service-health"></a>Aan de slag met Service Health

Als u het dash board van Service Health wilt starten, selecteert u de tegel Service Health in het dash board van de portal. Als u de tegel eerder hebt verwijderd of als u aangepast dash board gebruikt, zoekt u naar Service Health-Service in "meer services" (linksonder op uw dash board).

![Aan de slag met Service Health](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Bekijk de huidige problemen die van invloed zijn op uw services

In de weer gave **Service problemen** ziet u lopende problemen in Azure-Services die van invloed zijn op uw resources. U kunt begrijpen wanneer het probleem is begonnen en welke services en regio's worden beïnvloed. U kunt ook de meest recente update lezen om te begrijpen wat Azure doet om het probleem op te lossen. 

[![Service probleem beheren](./media/service-health-overview/azure-service-health-overview-2.png)](./media/service-health-overview/azure-service-health-overview-2.png#lightbox)

Kies het tabblad **mogelijke impact** om de specifieke lijst met resources te bekijken waarvan u de eigenaar bent die mogelijk van invloed is op het probleem. U kunt een CSV-lijst van deze resources downloaden om te delen met uw team.

[![Service probleem beheren-impact](./media/service-health-overview/azure-service-health-overview-4.png)](./media/service-health-overview/azure-service-health-overview-4.png#lightbox)

## <a name="see-emerging-issues-which-may-impact-your-services"></a>Bekijk opkomende problemen die van invloed kunnen zijn op uw services

Er zijn situaties waarin uitgebreide service problemen kunnen worden geplaatst op de [Azure-status pagina](https://status.azure.com) voordat de beoogde communicatie kan worden verzonden naar klanten met een impact. Om ervoor te zorgen dat Azure Service Health een uitgebreid overzicht geeft van de problemen die van invloed kunnen zijn op de problemen met de actieve Azure-status pagina, worden deze Service Health als *opkomende problemen*. Wanneer een gebeurtenis actief is op de Azure-status pagina, wordt er een banner voor opkomende problemen weer gegeven in Service Health. Klik op de banner om de volledige details van het probleem weer te geven.

![Opkomende service probleem](./media/service-health-overview/azure-service-health-emerging-issue.png)

## <a name="get-links-and-downloadable-explanations"></a>Koppelingen en download bare uitleg ophalen 

U kunt een koppeling voor het probleem verkrijgen om te gebruiken in uw probleem beheersysteem. U kunt een PDF-bestand en soms CSV-bestanden downloaden om te delen met personen die geen toegang hebben tot de Azure Portal.   

[![Service probleem beheren-probleem beheer](./media/service-health-overview/azure-service-health-overview-3.png)](./media/service-health-overview/azure-service-health-overview-3.png#lightbox)

## <a name="get-support-from-microsoft"></a>Ondersteuning krijgen van Microsoft

Neem contact op met de ondersteuning als uw resource in een slechte staat blijft, zelfs nadat het probleem is opgelost.  Gebruik de ondersteunings koppelingen aan de rechter kant van de pagina.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Een persoonlijke status toewijzing vastmaken aan uw dash board

Filter Service Health om uw bedrijfs kritieke abonnementen, regio's en resource typen weer te geven. Sla het filter op en maak een aangepaste wereldkaart met statusmeldingen vast aan uw portaldashboard. 

[![Persoonlijke status toewijzing filteren](./media/service-health-overview/azure-service-health-overview-6a.png)](./media/service-health-overview/azure-service-health-overview-6a.png#lightbox)

![Een persoonlijke status kaart vastmaken](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Service status waarschuwingen configureren

Service Health integreert met Azure Monitor om u te waarschuwen via e-mails, SMS-berichten en webhook-meldingen wanneer uw bedrijfs kritieke resources worden beïnvloed. Stel een waarschuwing voor een activiteiten logboek in voor de juiste service status gebeurtenis. Hiermee stuurt u een waarschuwing naar de juiste personen in uw organisatie met behulp van actie groepen. Zie [Configure alerts for service Health](./alerts-activity-log-service-notifications-portal.md) voor meer informatie.

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Volgende stappen

Stel waarschuwingen in zodat u op de hoogte wordt gesteld van status problemen. Zie [Aanbevolen procedures voor het instellen van Azure service Health-waarschuwingen](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s)voor meer informatie. 
