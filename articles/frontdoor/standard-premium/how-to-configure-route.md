---
title: Route voor Azure-front-deur configureren
description: In dit artikel wordt beschreven hoe u een route configureert tussen uw domeinen en bron groepen.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 94c22ffd423c32ba5f489298267464ca36abaecd
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098922"
---
# <a name="configure-an-azure-front-door-standardpremium-route"></a>Een Azure front deur Standard/Premium-route configureren

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

In dit artikel wordt elk van de instellingen beschreven die worden gebruikt bij het maken van een Azure voor-deur (AFD)-route voor een bestaand eind punt. Nadat u een aangepast domein en een oorspronkelijke oorsprong hebt toegevoegd aan uw bestaande Azure front deur-eind punt, moet u de route configureren om de koppeling tussen domeinen en oorsprong te definiëren om het verkeer tussen beide te routeren.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voordat u een Azure front-deur route kunt configureren, moet u ten minste één oorsprongs groep en één aangepast domein binnen het huidige eind punt hebben gemaakt. 

Zie [een nieuwe Azure front-deur standaard/Premium-basis groep maken](how-to-create-origin.md)om een oorspronkelijke groep in te stellen. 

## <a name="create-a-new-azure-front-door-standardpremium-route"></a>Een nieuwe standaard route voor de Azure front-deur/Premium maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure front deur Standard/Premium-profiel.

1. Selecteer **eindpunt beheerder** onder **instellingen**.
   
    :::image type="content" source="../media/how-to-configure-route/select-endpoint-manager.png" alt-text="Scherm afbeelding van de instellingen voor de eindpunt beheerder van de voor deur." lightbox="../media/how-to-configure-route/select-endpoint-manager-expanded.png":::

1. Selecteer vervolgens **eind punt bewerken** voor het eind punt dat u wilt configureren voor de route.
   
    :::image type="content" source="../media/how-to-configure-route/select-edit-endpoint.png" alt-text="Scherm opname van het selecteren van het eind punt bewerken.":::

1. De pagina **eind punt bewerken** wordt weer gegeven. Selecteer **+ toevoegen** voor routes.
    
    :::image type="content" source="../media/how-to-configure-route/select-add-route.png" alt-text="Scherm opname van de pagina een eind punt voor het bewerken van een route toevoegen.":::    
    
1. Voer op de pagina **route toevoegen** de volgende informatie in of Selecteer deze.

    :::image type="content" source="../media/how-to-configure-route/add-route-page.png" alt-text="Scherm afbeelding van de pagina een route toevoegen." lightbox="../media/how-to-configure-route/add-route-page-expanded.png"::: 

    | Instelling | Waarde |
    | --- | --- |
    | Naam | Voer een unieke naam in voor de nieuwe route. |   
    | Domain| Selecteer een of meer domeinen die zijn gevalideerd en niet aan een andere route zijn gekoppeld |
    | Overeenkomende patronen  | Configureer alle patronen van het URL-pad dat door deze route wordt geaccepteerd. U kunt dit bijvoorbeeld instellen om `/images/*` alle aanvragen op de URL te accepteren `www.contoso.com/images/*` . AFD probeert het verkeer te bepalen op basis van exacte overeenkomsten eerst, als er geen exacte overeenkomst paden zijn en zoek vervolgens naar een Joker teken dat overeenkomt. Als er geen routerings regels met een overeenkomend pad worden gevonden, kunt u de aanvraag afwijzen en een 400: fout HTTP-antwoord voor een ongeldige aanvraag retour neren. |
    | Geaccepteerde protocollen | Geef de protocollen op die door Azure front-deur moeten worden geaccepteerd wanneer de client de aanvraag doet. |
    | Omleiden | Opgeven of HTTPS wordt afgedwongen voor de inkomende aanvraag met de HTTP-aanvraag |
    | Oorspronkelijke groep | Selecteer de oorspronkelijke groep waarnaar moet worden doorgestuurd wanneer de back-up naar de oorspronkelijke aanvraag plaatsvindt. |
    | Bronpad | Voer het pad in naar de resources die u in de cache wilt opslaan. Als u de caching van een resource in het domein wilt toestaan, laat u deze instelling leeg. |
    | Doorstuurprotocol | Selecteer het protocol dat wordt gebruikt voor het door sturen van de aanvraag. |
    | Caching | Selecteer deze optie om het opslaan van statische inhoud in de cache in te scha kelen met Azure front-deur. |
    | Regel | Selecteer regel sets die op deze route worden toegepast. Voor meer informatie over het configureren van regels, Zie [een regelset configureren voor Azure front-deur](how-to-configure-rule-set.md) | 

1. Selecteer **toevoegen** om de nieuwe route te maken. De route wordt weer gegeven in de lijst met routes voor het eind punt.
    
    :::image type="content" source="../media/how-to-configure-route/route-list-page.png" alt-text="Scherm opname van de lijst met routes.":::  
    
## <a name="clean-up-resources"></a>Resources opschonen

Als u een route wilt verwijderen wanneer u deze niet meer nodig hebt, selecteert u de route en selecteert u vervolgens **verwijderen**. 

:::image type="content" source="../media/how-to-configure-route/route-delete-page.png" alt-text="Scherm opname van het verwijderen van een route.":::  

## <a name="next-steps"></a>Volgende stappen
Voor meer informatie over aangepaste domeinen gaat u verder met de zelf studie voor het toevoegen van een aangepast domein aan uw Azure front deur-eind punt.

> [!div class="nextstepaction"]
> [Een aangepast domein toevoegen]()
