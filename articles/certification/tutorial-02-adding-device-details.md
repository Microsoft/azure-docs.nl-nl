---
title: Programma voor Azure Certified Device-zelf studie-Details van apparaten toevoegen
description: Een stapsgewijze hand leiding voor het toevoegen van apparaatgegevens aan uw project in de Azure Certified Device-Portal
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: f4f3d045a2530fa54d22bec789918454cba80097
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310424"
---
# <a name="tutorial-add-device-details"></a>Zelf studie: Details van het apparaat toevoegen

U hebt nu het project voor uw apparaat gemaakt en u bent klaar om het certificerings proces te starten. Eerst gaan we de gegevens van uw apparaat toevoegen. Dit zijn onder andere technische specificaties die uw klanten kunnen bekijken in de Azure Certified-stuurprogrammacatalogus en de marketing gegevens die ze zullen gebruiken om te kopen zodra ze een beslissing hebben genomen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Details van apparaat toevoegen met behulp van de functies onderdelen en afhankelijkheden
> * Een aan de slag-hand leiding voor uw apparaat uploaden
> * Marketing gegevens opgeven voor klanten om uw apparaat te kopen
> * U kunt eventueel ook bedrijfstak certificeringen identificeren

## <a name="prerequisites"></a>Vereisten

* U moet zijn aangemeld en een project voor uw apparaat hebben gemaakt in de [Azure-portal voor gecertificeerde apparaten](https://certify.azure.com). Raadpleeg de [zelf studie](tutorial-01-creating-your-project.md)voor meer informatie.
* U moet een aan de slag-hand leiding voor uw apparaat hebben in PDF-indeling. We bieden een aantal aan de slag-sjablonen die u kunt gebruiken, afhankelijk van het certificerings programma en de taal van uw voor keur. De sjablonen zijn beschikbaar op de locatie van de [aan de slag-sjablonen](https://aka.ms/GSTemplate "Aan de slag-sjablonen") github.

## <a name="adding-technical-device-details"></a>Details van technische apparaten toevoegen

In de eerste sectie van uw project pagina, met de naam gegevens van invoer apparaat, kunt u informatie opgeven over de kern mogelijkheden van uw apparaat, zoals apparaatnaam, beschrijving, processor, besturings systeem, connectiviteits opties, hardware-interfaces, branche protocollen, fysieke afmetingen en meer. Hoewel veel van de velden optioneel zijn, wordt de meeste van deze gegevens beschikbaar gesteld voor potentiële klanten in de Azure Certified-apparaatinstantie als u ervoor kiest om uw apparaat te publiceren nadat het is gecertificeerd.

1. Klik `Add` in de sectie Details van invoer apparaat op de pagina project overzicht om de sectie apparaatgegevens te openen. U ziet vijf secties die u kunt volt ooien.

![Afbeelding van de pagina project Details](./media/images/device-details-menu.png)

2. Controleer de informatie die u eerder hebt gegeven toen u het project maakte op het `Basics` tabblad.
1. Controleer de certificeringen die u met uw apparaat wilt Toep assen op het `Certifications` tabblad.
1. Open het `Product details` tabblad en selecteer ten minste één besturings systeem.
1. Voeg **ten minste** één afzonderlijk onderdeel toe dat uw apparaat beschrijft. U kunt [hier](how-to-using-the-components-feature.md)meer informatie over het gebruik van het onderdeel bekijken.
1. Klik op `Save`. U kunt vervolgens uw onderdeel apparaat bewerken en meer gedetailleerde informatie toevoegen.
1. Aanvullende details van apparaten weer geven die niet zijn vastgelegd door de onderdeel Details onder `Additional product details` .
1. Als u `Other` hebt gemarkeerd in een van de onderdeel velden of als u een speciale omstandigheid wilt markeren met het Azure-certificerings team, laat u in de sectie een duidelijke opmerking staan `Comments for reviewer` .
1. Gebruik het `Dependencies` tabblad om afhankelijkheden weer te geven als uw apparaat extra hardware of Services vereist om gegevens naar Azure te verzenden. U kunt [hier](how-to-indirectly-connected-devices.md)aanvullende richt lijnen weer geven voor de vermelding van afhankelijkheden.
1. Zodra u tevreden bent met de informatie die u hebt opgegeven, kunt u het `Review` tabblad voor een alleen-lezen overzicht van de volledige set met apparaatgegevens die zijn ingevoerd.
1. Klik `Project summary` boven aan de pagina om terug te gaan naar uw overzichts pagina.

![Pagina project Details bekijken](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>Een aan de slag-hand leiding uploaden

De aan de slag-hand leiding is een PDF-document waarmee u de installatie en configuratie en het beheer van uw product kunt vereenvoudigen. Het doel is om het voor klanten eenvoudig te maken om apparaten op Azure te verbinden en te ondersteunen met behulp van uw apparaat. Als onderdeel van het certificerings proces moeten onze partners **een** aan de slag-hand leiding bieden voor het meest relevante certificerings programma.

1. Controleer of u alle aangevraagde informatie hebt opgegeven in uw PDF aan de slag-hand leiding volgens de opgegeven [sjablonen](https://aka.ms/GSTemplate). De sjabloon die u gebruikt, moet worden bepaald door de certificerings badge waarmee u een toepassing wilt maken. (Een IoT-Plug en Play apparaat gebruikt bijvoorbeeld de sjabloon IoT Plug en Play. Voor apparaten die *alleen* worden toegepast op de basis certificerings instantie van Azure Certified, wordt gebruikgemaakt van de sjabloon Azure Certified device.)
1. Klik `Add` in het gedeelte hand leiding aan de slag van de pagina project samenvatting.

![Afbeelding van de knop GSG](./media/images/gsg-menu.png)

2. Klik op bestand kiezen om uw PDF te uploaden.
1. Bekijk het document in het voor beeld voor opmaak.
1. Sla uw Upload op door te klikken op de knop Opslaan.
1. Klik `Project summary` boven aan de pagina om terug te gaan naar uw overzichts pagina.

## <a name="providing-marketing-details"></a>Marketing gegevens opgeven

In dit gebied kunt u marketing gegevens van de klant voor uw apparaat aanbieden. Deze velden worden weer geven in de Azure Certified-stuurprogrammacatalogus als u ervoor kiest om uw gecertificeerd apparaat te publiceren.

1. Klik `Add` in het gedeelte Marketing Details toevoegen om de pagina marketing details te openen.

![Afbeelding van de sectie Marketing Details](./media/images/marketing-details.png)

1. Upload een product foto in JPEG-of PNG-indeling die wordt gebruikt in de catalogus.
1. Schrijf een korte beschrijving van uw apparaat die wordt weer gegeven op de pagina product beschrijving van de catalogus.
1. Geografische Beschik baarheid van uw apparaat aangeven.
1. Geef een koppeling op naar de marketing pagina van de fabrikant voor dit apparaat. Dit moet een koppeling zijn naar een site die aanvullende informatie over het apparaat bevat.
    > [!Note]
    > Zorg ervoor dat alle opgegeven Url's geldig zijn of actief zijn op het moment dat de publicatie na goed keuring wordt weer gegeven. *)

1. Geef Maxi maal drie doel-branches op waarvoor uw apparaat is geoptimaliseerd.
1. Geef informatie op van Maxi maal 5 distributeurs van uw apparaat. Dit kan de eigen site van de fabrikant omvatten.

    > [!Note]
    > Als er geen pagina-URL voor de distributie server is opgegeven, wordt voor de `Shop` knop in de catalogus standaard de koppeling opgegeven `Distributor page` die mogelijk niet specifiek is voor het apparaat. In het ideale geval moet de URL van de Distributor verwijzen naar een specifieke pagina waar een klant een apparaat kan kopen, maar niet verplicht is. Als de Distributor hetzelfde is als de fabrikant, is deze URL mogelijk hetzelfde als de marketing pagina van de fabrikant. *)

1. Klik `Save` om uw gegevens te bevestigen.
1. Klik `Project summary` boven aan de pagina om terug te gaan naar uw overzichts pagina.

## <a name="declaring-additional-industry-certifications"></a>Aanvullende industrie certificeringen declareren

U kunt ook extra branche certificeringen promoten die u hebt ontvangen voor uw apparaat. Deze certificeringen kunnen u helpen om meer duidelijkheid te geven over het beoogde gebruik van uw apparaat en kunnen worden doorzocht in de Azure Certified-stuurprogrammacatalogus.

1. Klik `Add` in de rubriek certificerings instanties opgeven.
1. Klik `Add a certification` om te selecteren in een lijst van de algemene industrie certificerings Programma's. Als uw product een certificering heeft die niet in onze lijst staat, kunt u een aangepaste teken reeks waarde opgeven door te selecteren `Other (please specify)` .
1. Geef eventueel een beschrijving of opmerkingen aan de revisor op. Deze opmerkingen zijn echter niet openbaar beschikbaar voor weer gave in de catalogus.
1. Klik `Save` om uw gegevens te bevestigen.
1. Klik `Project summary` boven aan de pagina om terug te gaan naar uw overzichts pagina.

## <a name="next-steps"></a>Volgende stappen

U hebt nu het proces voor het beschrijven van het apparaat voltooid. Dit helpt het Azure Certified Device Review-team en uw klant om uw product beter te begrijpen. Zodra u tevreden bent met de informatie die u hebt verstrekt, bent u nu klaar om over te stappen op de test fase van het certificerings proces.
> [!div class="nextstepaction"]
> [Zelf studie: uw apparaat testen](tutorial-03-testing-your-device.md)
