---
title: Pagina-inhoud in de ontwikkelaars Portal in API Management wijzigen
titleSuffix: Azure API Management
description: Informatie over het bewerken van pagina-inhoud in de ontwikkelaarsportal in Azure API Management.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: vlvinogr
editor: ''
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: vlvinogr
ms.openlocfilehash: ebf2cbd430339378a09d10d91ad61327d24842e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75430638"
---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>De inhoud en opmaak van pagina’s bewerken in de ontwikkelaarsportal in Azure API Management
Er zijn die manieren waarop u de ontwikkelaarsportal in Azure API Management kunt aanpassen:

* [De inhoud van statische pagina's en pagina-indelingselementen bewerken][modify-content-layout] (uitgelegd in deze handleiding)
* [De stijlen bijwerken die worden gebruikt voor pagina-elementen in de ontwikkelaarsportal][customize-styles]
* [De sjablonen bewerken die worden gebruikt voor pagina's die worden gegenereerd door de portal][portal-templates] (bijvoorbeeld API-documenten, producten, gebruikersverificatie, enzovoort.)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="structure-of-developer-portal-pages"></a><a name="page-structure"> </a>Structuur van pagina's in de ontwikkelaarsportal

De ontwikkelaarsportal is gebaseerd op een inhoudsbeheersysteem. De indeling van elke pagina is opgebouwd op basis van een reeks kleine pagina-elementen die ook wel widgets worden genoemd:

![Paginastructuur in de ontwikkelaarsportal][api-management-customization-widget-structure]

Alle widgets kunnen worden bewerkt.
* De belangrijkste inhoud die voor elke pagina anders is, staat in de widget Inhoud. Als u een pagina bewerkt, bewerkt u eigenlijk de inhoud van deze widget.
* Alle elementen van pagina-indeling staan in de overige widgets. Als u wijzigingen aanbrengt aan deze widgets, worden die toegepast op alle pagina's. Deze widgets heten ook wel indelingswidgets.

Voor de alledaagse paginabewerkingen bewerkt u meestal alleen de widget Inhoud, die op elke afzonderlijke pagina andere inhoud bevat.

## <a name="modifying-the-contents-of-a-layout-widget"></a><a name="modify-layout-widget"> </a>De inhoud van een indelingswidget wijzigen

De Developer-portal is toegankelijk via de Azure Portal.

1. Klik op **Developer portal** op de werkbalk van uw API Management-exemplaar.
2. Als u de inhoud van widgets wilt bewerken, klikt u op het pictogram met de twee verfkwasten van het portalmenu **Developer** aan de linkerkant.
3. Als u de inhoud van de koptekst wilt wijzigen, schuift u naar de sectie **Koptekst** in de lijst links.

    De widgets kunnen worden bewerkt vanuit de velden.
4. Wanneer u klaar bent om uw wijzigingen te publiceren, klikt u op **Publiceren** onderaan de pagina.

U zou nu de nieuwe header op elke pagina moeten zien binnen de ontwikkelaarsportal.

## <a name="next-steps"></a><a name="next-steps"> </a>Volgende stappen
* [De stijlen bijwerken die worden gebruikt voor pagina-elementen in de ontwikkelaarsportal][customize-styles]
* [De sjablonen bewerken die worden gebruikt voor pagina's die worden gegenereerd door de portal][portal-templates] (bijvoorbeeld API-documenten, producten, gebruikersverificatie, enzovoort.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png
