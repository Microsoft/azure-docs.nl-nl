---
title: Prestaties verbeteren door bestanden te comprimeren in azure front deur Standard/Premium (preview)
description: Meer informatie over het verbeteren van de snelheid van de bestands overdracht en het verg Roten van de prestaties van de pagina belasting door uw bestanden in de Azure-deur te comprimeren.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 4b526d82465862b1c0d27aed6443c6d7199bfb5b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098991"
---
# <a name="improve-performance-by-compressing-files-in-azure-front-door-standardpremium-preview"></a>Prestaties verbeteren door bestanden te comprimeren in azure front deur Standard/Premium (preview)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Bestands compressie is een doel treffende methode om de snelheid van de bestands overdracht te verbeteren en de prestaties van de pagina belasting te verg Roten. De compressie vermindert de grootte van het bestand voordat het wordt verzonden door de server. Bestands compressie kan de bandbreedte kosten verlagen en biedt uw gebruikers een betere ervaring.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
Er zijn twee manieren om bestands compressie in te scha kelen:

- Compressie inschakelen op de oorspronkelijke server. Azure front-deur geeft de gecomprimeerde bestanden door en levert deze aan clients die deze aanvragen.
- Het inschakelen van compressie rechtstreeks op de Azure front-deur-POP-servers (*compressie aan de vliegen*). In dit geval comprimeert Azure front deur de bestanden en verzendt deze naar de eind gebruikers.

> [!IMPORTANT]
> Wijzigingen in de configuratie van Azure front-deur duren 10 minuten voor het door geven van het hele netwerk. Als u voor de eerste keer compressie voor uw CDN-eind punt instelt, moet u 1-2 uur wachten voordat u het probleem oplost om te controleren of de compressie-instellingen zijn door gegeven aan alle Pop's.

## <a name="enabling-compression"></a>Compressie inschakelen

> [!Note]
> In azure front-deur maakt compressie deel uit van caching in route **inschakelen** . Alleen als u **opslaan in cache inschakelt**, kunt u gebruikmaken van compressie in azure front-deur.

U kunt compressie op de volgende manieren inschakelen:
* Wanneer u opslaan in cache inschakelt, kunt u tijdens snel maken-compressie inschakelen.
* Tijdens aangepaste Create-caching en compressie inschakelen wanneer u een route wilt toevoegen. 
* In de route van Endpoint Manager.
* Op de pagina optimalisatie.

### <a name="enable-compression-in-endpoint-manager"></a>Compressie inschakelen in Endpoint Manager

1. Ga op de pagina standaard profiel voor Azure front deur/Premium naar **eindpunt beheer** en selecteer het eind punt waarvoor u compressie wilt inschakelen.

1. Selecteer **eind punt bewerken** en selecteer vervolgens de **route** waarvoor u compressie wilt inschakelen. 

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-1.png" alt-text="Scherm afbeelding van de landings pagina van de eindpunt beheerder." lightbox="../media/how-to-compression/front-door-compression-endpoint-manager-1-expanded.png":::   

1. Zorg ervoor dat **cache inschakelen** is ingeschakeld en schakel het selectie vakje in om compressie in te **scha kelen**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Schakel compressie in Endpoint Manager in.":::   

1. Selecteer **bijwerken** om de configuratie op te slaan.

### <a name="enable-compression-in-optimization"></a>Compressie inschakelen in optimalisatie

1. Ga op de pagina standaard profiel voor Azure front deur/Premium naar **optimalisaties** onder instellingen. Vouw het eind punt uit om de lijst met routes weer te geven. 

1. Selecteer de drie puntjes naast de **route** waarvoor compressie is *uitgeschakeld*. Selecteer vervolgens **route configureren**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-optimization-1.png" alt-text="Scherm van compressie inschakelen op de pagina optimalisatie." lightbox="../media/how-to-compression/front-door-compression-optimization-1-expanded.png"::: 

1. Zorg ervoor dat **cache inschakelen** is ingeschakeld en schakel het selectie vakje in om compressie in te **scha kelen**.

     :::image type="content" source="../media/how-to-compression/front-door-compression-endpoint-manager-2.png" alt-text="Scherm opname van het inschakelen van compressie in Endpoint Manager."::: 

1. Klik op **Update**.

## <a name="modify-compression-content-type"></a>Type compressie-inhoud wijzigen

U kunt de standaard lijst met MIME-typen wijzigen op de pagina optimalisaties.

1. Ga op de pagina standaard profiel voor Azure front deur/Premium naar **optimalisaties** onder instellingen. Selecteer vervolgens de **route** waarvoor compressie is *ingeschakeld*.

1. Selecteer de drie puntjes naast de **route** waarvoor compressie is *ingeschakeld*. Selecteer vervolgens **gecomprimeerde bestands typen weer geven**.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type.png" alt-text="Scherm afbeelding van optimalisatie pagina." lightbox="../media/how-to-compression/front-door-compression-edit-content-type-expanded.png"::: 

1. Verwijder standaard indelingen of selecteer **toevoegen** om nieuwe inhouds typen toe te voegen.

   :::image type="content" source="../media/how-to-compression/front-door-compression-edit-content-type-2.png" alt-text="Scherm afbeelding van de pagina bestands compressie aanpassen."::: 

1. Selecteer **Opslaan** om de compressie-instellingen bij te werken.

## <a name="disabling-compression"></a>Compressie uitschakelen

U kunt compressie op de volgende manieren uitschakelen:
* Schakel compressie uit in de route van Endpoint Manager.
* Schakel compressie in optimalisatie pagina uit.

### <a name="disable-compression-in-endpoint-manager"></a>Compressie uitschakelen in Endpoint Manager

1. Ga op de pagina standaard profiel voor Azure front deur/Premium naar **eindpunt beheerder** onder instellingen. Selecteer het eind punt waarvoor u compressie wilt uitschakelen.

1. Selecteer **eind punt bewerken** en selecteer vervolgens de **route** waarvoor u compressie wilt uitschakelen. Schakel het selectie vakje **compressie inschakelen** uit.

1. Selecteer **bijwerken** om de configuratie op te slaan.

### <a name="disable-compression-in-optimizations"></a>Compressie uitschakelen in optimalisaties

1. Ga op de pagina standaard profiel voor Azure front deur/Premium naar **optimalisaties** onder instellingen. Selecteer vervolgens de **route** waarvoor compressie is *ingeschakeld*.

1. Selecteer de drie puntjes naast de **route** waarvoor compressie is *ingeschakeld* en selecteer vervolgens *route configureren*.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization.png" alt-text="Scherm afbeelding van compressie uitschakelen in optimalisatie pagina."::: 

1. Schakel het selectie vakje **compressie inschakelen** uit.

    :::image type="content" source="../media/how-to-compression/front-door-disable-compression-optimization-2.png" alt-text="Scherm afbeelding van de pagina route bijwerken voor het uitschakelen van compressie."::: 

1. Selecteer **bijwerken** om de configuratie op te slaan.

## <a name="compression-rules"></a>Compressie regels

In azure front-deur worden alleen in aanmerking komende bestanden gecomprimeerd. Om in aanmerking te komen voor compressie, moet een bestand:
* Een MIME-type zijn 
* Groter zijn dan 1 KB
* Kleiner zijn dan 8 MB

Deze profielen ondersteunen de volgende compressie coderingen:
* gzip (GNU-zip)
* brotli 

Als de aanvraag meer dan één compressie type ondersteunt, heeft brotli-compressie prioriteit.

Wanneer een aanvraag voor een activum gzip-compressie specificeert en de aanvraag resulteert in een cache Missing, wordt de gzip-functie door de Azure front-deur rechtstreeks op de POP-server gebruikt. Daarna wordt het gecomprimeerde bestand vanuit de cache verwerkt.

Als de oorsprong gebruikmaakt van een CTE (gesegmenteerde overdrachts codering) voor het verzenden van gecomprimeerde gegevens naar de Azure front deur POP, worden de antwoord groottes van meer dan 8 MB niet ondersteund. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het configureren van uw eerste [regelset](how-to-configure-rule-set.md)
- Meer informatie over de [voor waarden voor de regel instellingen](concept-rule-set-match-conditions.md)
- Meer informatie over de [regel reeks voor Azure front-deur](concept-rule-set.md)
