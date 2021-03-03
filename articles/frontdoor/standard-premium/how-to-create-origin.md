---
title: Een Azure front deur Standard/Premium-oorsprong (preview) instellen
description: In dit artikel wordt beschreven hoe u een oorsprong configureert met eindpunt beheerder.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ebc71ea2d354caf0c8f31b1231ecc1487237dd29
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741885"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>Een Azure front deur Standard/Premium-oorsprong (preview) instellen

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

In dit artikel wordt uitgelegd hoe u een basis-en Premium-oorsprong voor Azure front-deur maakt in een bestaande oorspronkelijke groep. 

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Voordat u een Azure front-deur standaard/Premium-oorsprong kunt maken, moet u ten minste één oorspronkelijke groep hebben gemaakt.

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>Een nieuwe basis-of Premium-oorsprong voor de Azure front-deur maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure front deur Standard/Premium-profiel.

1. Selecteer de **oorspronkelijke groep**. Selecteer vervolgens **+ toevoegen** om een nieuwe oorspronkelijke groep te maken.
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="Scherm afbeelding van de landings pagina van de oorspronkelijke groep.":::

1. Voer op de pagina **een oorsprongs groep toevoegen** een unieke **naam** in voor de nieuwe oorspronkelijke groep.

1. Selecteer vervolgens **+ een oorsprong toevoegen** om een nieuwe oorsprong aan deze oorspronkelijke groep toe te voegen. 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="Scherm opname van een originele pagina toevoegen.":::
  
    | Instelling | Waarde |
    | --- | --- |
    | Naam | Voer een unieke naam in voor de nieuwe oorsprong van de Azure front-deur. |   
    | Type oorsprong | Het type resource dat u wilt toevoegen. Azure front deur Standard/Premium ondersteunt automatische detectie van uw app-oorsprong vanuit app service, Cloud service of Storage. Als u een andere resource wilt in azure of een back-end die niet van Azure is, selecteert u **aangepaste host**. |
    | Hostnaam  | Als u geen **aangepaste host** hebt geselecteerd voor het klassetype Origin, selecteert u uw back-end in de vervolg keuzelijst. |
    | Header van oorsprong van host | Geef de waarde van de host-header op die voor elke aanvraag wordt verzonden naar de back-end. Zie [Origin host header](concept-origin.md#hostheader)(Engelstalig) voor meer informatie. |
    | HTTP-poort | Voer de waarde in voor de poort die door de oorsprong wordt ondersteund voor het HTTP-protocol. |
    | HTTPS-poort | Voer de waarde in voor de poort die door de oorsprong wordt ondersteund voor het HTTPS-protocol. |
    | Prioriteit | Wijs prioriteiten toe aan uw andere oorsprong wanneer u een primaire service oorsprong voor al het verkeer wilt gebruiken. Geef ook back-ups op als de primaire of de oorsprong van de back-up niet beschikbaar is. Zie [Priority](concept-origin.md#priority)voor meer informatie. |
    | Gewicht | Wijs gewichten aan uw verschillende oorsprong toe om verkeer te verdelen over een reeks oorsprong, hetzij gelijkmatig, hetzij op basis van gewichts coëfficiënten. Zie [gewichten](concept-origin.md#weighted)voor meer informatie. |
    | Status | Selecteer deze optie om de oorsprong in te scha kelen. |
    | Regel | Selecteer regel sets die op deze route worden toegepast. Voor meer informatie over het configureren van regels, Zie [een regelset configureren voor Azure front-deur](how-to-configure-rule-set.md) | 

    > [!IMPORTANT]
    > Tijdens de configuratie valideert Api's niet als de oorsprong niet toegankelijk is vanuit de front-deur omgevingen. Zorg ervoor dat de front deur uw oorsprong kan bereiken.

1. Selecteer **toevoegen** om de nieuwe oorsprong te maken. De gemaakte oorsprong moet worden weer gegeven in de lijst oorsprong met de groep.
  
    :::image type="content" source="../media/how-to-create-origin/origin-list-view.png" alt-text="Scherm opname van de oorsprong in de lijst weergave.":::

1. Selecteer **toevoegen** om de oorspronkelijke groep toe te voegen aan het huidige eind punt. De oorspronkelijke groep moet worden weer gegeven in het deel venster oorspronkelijke groep.

## <a name="clean-up-resources"></a>Resources opschonen
Als u een oorspronkelijke groep wilt verwijderen wanneer u deze niet meer nodig hebt, klikt u op de **..** . en selecteert u **verwijderen** in de vervolg keuzelijst.

:::image type="content" source="../media/how-to-create-origin/delete-origin-group.png" alt-text="Scherm opname van het verwijderen van een oorspronkelijke groep.":::

Als u een oorsprong wilt verwijderen wanneer u deze niet meer nodig hebt, klikt u op de **..** . en selecteert u **verwijderen** in de vervolg keuzelijst. 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="Scherm opname van het verwijderen van een oorsprong.":::

## <a name="next-steps"></a>Volgende stappen

Zie [een aangepast domein toevoegen](how-to-add-custom-domain.md) aan uw Azure front deur Standard/Premium-eind punt voor meer informatie over aangepaste domeinen.
