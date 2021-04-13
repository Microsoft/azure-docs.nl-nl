---
title: Marketing eigenschappen
description: Een beschrijving van de verschillende marketing velden die in de portal worden verzameld en hoe deze worden weer gegeven in de Azure Certified-stuurprogrammacatalogus
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: template-concept
ms.openlocfilehash: 262616ca82e9c06baec0d7a1b81a0e3dff2ed8db
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304508"
---
# <a name="marketing-properties"></a>Marketing eigenschappen

In het proces van het [toevoegen van de details van uw apparaat](tutorial-02-adding-device-details.md)moet u marketing gegevens opgeven die worden weer gegeven in de [Azure Certified-stuurprogrammacatalogus](https://devicecatalog.azure.com). Deze informatie wordt verzameld binnen de Azure Certified-Portal tijdens het verzenden van de certificering en wordt gebruikt als filter parameters in de catalogus. In dit artikel vindt u een overzicht van de velden die in de portal worden verzameld en hoe deze in de catalogus worden weer gegeven. Na het lezen van dit artikel moeten partners beter begrijpen welke informatie tijdens het certificerings proces moet worden verstrekt om het product in de catalogus het beste te kunnen vertegenwoordigen.

![Overzicht van PDP](./media/concepts-marketing/pdp-overview.png)

## <a name="azure-certified-device-catalog-product-tile"></a>Product tegel voor Azure Certified Device Catalog

Bezoekers van de catalogus zullen eerst met uw apparaat communiceren als een catalogus product tegel op de zoek pagina. Dit geeft een basis overzicht van het apparaat en de certificeringen die zijn toegekend.

![Product tegel sjabloon](./media/concepts-marketing/product-tile.png)

| Veld | Description                  | Waar toevoegen in de portal                |
|---------------|-------------------------|----------------------------------|
| Apparaatnaam | Open bare naam van het gecertificeerde apparaat         | Tabblad basis informatie over de details van het apparaat|
| Bedrijfsnaam| Open bare naam van uw bedrijf  | Kan niet worden bewerkt in de portal. Geëxtraheerd uit de MPN-account naam |
| Product foto  | Afbeelding van uw apparaat met minimale resolutie 200p x 200p  | Marketing Details |
| Certificerings classificatie  | Verplicht certificerings label voor het Azure-gecertificeerde apparaat en optionele certificerings badges  | Tabblad basis informatie over de details van het apparaat. De juiste tests moeten worden door gegeven in de sectie test verbinden &. |

## <a name="product-description-page-information"></a>Informatie over de product beschrijvings pagina

Zodra een klant op de tegel van de catalogus-zoek pagina heeft geklikt, wordt deze naar de pagina product beschrijving van het apparaat genavigeerd. Dit is waar het meren deel van de gegevens die tijdens het certificerings proces worden verstrekt, wordt gevonden.

Boven aan de pagina product beschrijving worden de belangrijkste kenmerken beschreven, waarvan sommige al zijn gebruikt voor de product tegel.

![PDP-bovenste balk](./media/concepts-marketing/pdp-top.png)

| Veld | Description                  | Waar toevoegen in de portal                |
|---------------|-------------------------|----------------------------------|
| Apparaatklasse | Classificatie van de vorm factor en het primaire doel van uw apparaat ([meer informatie](./resources-glossary.md))       | Tabblad basis informatie over de details van het apparaat|
| Apparaattype | De classificatie van het apparaat op basis van de gereedheid voor implementatie ([meer informatie](./resources-glossary.md)) | Tabblad basis informatie over de details van het apparaat |
| Geografische Beschik baarheid | Regio's die uw apparaat beschikbaar is voor aankoop  | Marketing Details |
| Besturingssystemen  | Besturings systeem (s) die door uw apparaat worden ondersteund  | Tabblad product details van de details van het apparaat |
| Doel branches  | Top 3 van de branches waarvoor uw apparaat is geoptimaliseerd  | Marketing Details |
| Productbeschrijving  | Vrije-tekst veld voor u om uw marketing beschrijving van uw product te schrijven. Zo kunt u details vastleggen die niet in de portal worden vermeld, of een extra context toevoegen voor de voor delen van het gebruik van uw apparaat. | Marketing Details|

De rest van de pagina is gericht op het weer geven van de technische specificaties van uw apparaat in tabel vorm, zodat uw klant beter inzicht krijgt in uw product. Voor het gemak wordt de informatie die boven aan de pagina wordt weer gegeven hier ook weer gegeven. De rest van de tabel wordt beschreven door de onderdelen die zijn opgegeven in de portal.

![PDP-onderste pagina](./media/concepts-marketing/pdp-bottom.png)

| Veld | Description                  | Waar toevoegen in de portal                |
|---------------|-------------------------|----------------------------------|
| Onderdeeltype | Classificatie van de vorm factor en het primaire doel van uw apparaat ([meer informatie](./resources-glossary.md))       | Product details van de details van het apparaat|
| Onderdeel naam| De naam van het onderdeel dat u beschrijft | Product details van de details van het apparaat |
| Aanvullende onderdeel informatie | Aanvullende hardwarespecificaties, zoals inbegrepen Sens oren, connectiviteit, accelerators, enzovoort.  | Aanvullende onderdeel gegevens van de details van het apparaat ([meer informatie](./how-to-using-the-components-feature.md))  |
| Tekst van afhankelijkheid van apparaat | Door de partner verschafte tekst met een beschrijving van de verschillende afhankelijkheden die het product nodig heeft om verbinding te maken met Azure ([meer informatie](./how-to-indirectly-connected-devices.md))   | De sectie opmerkingen van de klant op het tabblad Afhankelijkheden van de apparaatgegevens |
| Afhankelijkheids koppeling van apparaat  | Koppeling naar een gecertificeerd apparaat dat voor uw huidige product is vereist  | Tabblad Afhankelijkheden van de details van het apparaat |

## <a name="shop-links"></a>Shop links
Beide zijn beschikbaar op de pagina product tegel en product beschrijving is een winkel knop. Wanneer u op de klant klikt, wordt er een venster geopend waarmee u een distributeur kunt selecteren (Maxi maal 5 distributeurs kunnen worden vermeld). Zodra deze is geselecteerd, wordt de klant omgeleid naar de door de partner opgegeven URL.

![Afbeelding van de pop-upervaring van een productie](./media/concepts-marketing/shop.png)

| Veld | Description                  | Waar toevoegen in de portal                |
|---------------|-------------------------|----------------------------------|
| Naam van Distributor | De naam van de Distributor die uw product verkoopt | Marketing Details|
| Apparaat ophalen| Koppeling naar de externe website van de klant om het apparaat aan te schaffen (of om een citaat van de Distributor aan te vragen). Dit kan hetzelfde zijn als de pagina van de fabrikant als de Distributor hetzelfde is als de fabrikant van het apparaat. Als een aankoop pagina niet beschikbaar is, wordt dit omgeleid naar de pagina van de Distributor voor de klant om rechtstreeks contact met hen op te nemen.  | URL van product pagina van distributie server in marketing Details. Als er geen inkoop pagina beschikbaar is, wordt de koppeling standaard naar de Distributor-URL in marketing details weer gegeven. |

## <a name="external-links"></a>Externe koppelingen
Op de pagina product beschrijving vindt u koppelingen naar de site of bestanden van de partner die de klant beter inzicht in het product bieden. Ze worden weer gegeven aan de bovenkant van de pagina, onder de tekst van de product beschrijving. Welke koppelingen worden weer gegeven, is afhankelijk van de verschillende apparaattypen en certificerings Programma's.

| Koppeling | Beschrijving                  | Waar toevoegen in de portal                |
|---------------|-------------------------|----------------------------------|
| Aan de slag-hand leiding * | PDF-bestand met gebruikers instructies voor het maken van verbinding met en het gebruik van uw apparaat met Azure-Services | De sectie hand leiding aan de slag van de portal toevoegen|
| Pagina van de fabrikant *|Koppeling naar de pagina van de fabrikant. Deze pagina is mogelijk de specifieke product pagina voor uw apparaat of voor de start pagina van het bedrijf als een marketing pagina niet beschikbaar is. | Marketing pagina van de fabrikant in marketing Details |
| Apparaatmodel | Open bare DTDL-modellen voor IoT-Plug en Play oplossingen  | Kan niet worden bewerkt in de portal. Het model van het apparaat moet worden geüpload naar de ([open bare model opslagplaats](https://aka.ms/modelrepo)  |
| Bron code van apparaat | URL naar apparaat-bron code voor apparaattypen van de dev kit| Tabblad basis informatie over de details van het apparaat  |

 **Vereist voor alle gepubliceerde apparaten*

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe we de informatie gebruiken die u tijdens de certificering opgeeft, bent u klaar om uw apparaat te certificeren. Begin uw certificerings project of ga terug naar de detail fase apparaat om uw eigen marketing gegevens toe te voegen.

- [Start uw certificerings traject](./tutorial-00-selecting-your-certification.md)
- [Details van apparaat toevoegen](./tutorial-02-adding-device-details.md)
