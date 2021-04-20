---
title: Overzicht van de ontwikkelaarsportal in Azure API Management
titleSuffix: Azure API Management
description: Meer informatie over de ontwikkelaarsportal in API Management een aanpasbare website, waar API-consumenten uw API's kunnen verkennen.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: aaf02affce924232deb56bf7694771c838b0b323
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739598"
---
# <a name="overview-of-the-developer-portal"></a>Overzicht van de ontwikkelaarsportal

De ontwikkelaarsportal is een automatisch gegenereerde, volledig aanpasbare website met de documentatie van uw API's. Hier kunnen API-consumenten uw API's ontdekken, leren hoe ze deze kunnen gebruiken, toegang kunnen aanvragen en uitproberen.

Zoals in dit artikel is geïntroduceerd, kunt u de ontwikkelaarsportal aanpassen en uitbreiden voor uw specifieke scenario's. 

![API Management-ontwikkelaarsportal](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Migratie vanuit de verouderde portal

> [!IMPORTANT]
> De verouderde ontwikkelaarsportal is nu afgeschaft en ontvangt alleen beveiligingsupdates. U kunt deze zoals gebruikelijk blijven gebruiken tot de buitengebruikstelling in oktober 2023, wanneer de portal wordt verwijderd uit alle API Management Services.

Migratie naar de nieuwe ontwikkelaarsportal wordt beschreven in het [speciale documentatieartikel](developer-portal-deprecated-migration.md).

## <a name="customization-and-styling"></a>Aanpassing en stijl

De ontwikkelaarsportal kan worden aangepast en vorm geven via de ingebouwde visuele editor voor slepen en neerzetten. Zie [deze zelfstudie](api-management-howto-developer-portal-customize.md) voor meer informatie.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Uitbreidbaarheid

Uw API Management bevat een ingebouwde, altijd up-to-date, **beheerde ontwikkelaarsportal.** U hebt er toegang toe vanuit de Azure Portal interface.

Als u deze wilt uitbreiden met aangepaste logica, die niet out-of-the-box wordt ondersteund, kunt u de codebasis ervan wijzigen. De codebasis van de portal is [beschikbaar in een GitHub-opslagplaats.](https://github.com/Azure/api-management-developer-portal) U kunt bijvoorbeeld een nieuwe widget implementeren die kan worden geïntegreerd met een ondersteuningssysteem van derden. Wanneer u nieuwe functionaliteit implementeert, kunt u een van de volgende opties kiezen:

- **De resulterende portal** zelf hosten buiten uw API Management service. Wanneer u de portal zelf host, wordt u de onderhouder en bent u verantwoordelijk voor de upgrades. Ondersteuning voor Azure is alleen beperkt tot de basisinstallatie van [zelf-hostende portals.](developer-portal-self-host.md)
- Open een pull-aanvraag voor het API Management team om nieuwe functionaliteit samen te voegen met **de** codebasis van de beheerde portal.

Raadpleeg de [GitHub-opslagplaats](https://github.com/Azure/api-management-developer-portal) en de zelfstudie voor het implementeren van een widget voor meer informatie en instructies over [de uit te voeren extensibility.](developer-portal-implement-widgets.md) In de [zelfstudie voor het](api-management-howto-developer-portal-customize.md) aanpassen van de beheerde portal  wordt u door het beheervenster van de portal geleid. Dit is gebruikelijk voor beheerde en **zelf-hostbare** versies.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over de nieuwe ontwikkelaarsportal:

- [De beheerde ontwikkelaarsportal openen en aanpassen](api-management-howto-developer-portal-customize.md)
- [Zelf-hostende versie van de portal instellen](developer-portal-self-host.md)
- [Uw eigen widget implementeren](developer-portal-implement-widgets.md)

Blader door andere resources:

- [GitHub-opslagplaats met de broncode](https://github.com/Azure/api-management-developer-portal)
- [Veelgestelde vragen over de ontwikkelaarsportal](developer-portal-faq.md)
