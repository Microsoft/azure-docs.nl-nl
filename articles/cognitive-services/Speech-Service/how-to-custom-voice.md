---
title: De synthese verbeteren met de aangepaste Voice-Speech-Service
titleSuffix: Azure Cognitive Services
description: Aangepaste spraak is een set online-hulpprogram ma's waarmee u een herken bare, eenmalige stem kunt maken voor uw merk. Alles wat u nodig hebt om aan de slag te gaan zijn een aantal audio bestanden en de bijbehorende transcripties. Volg de onderstaande koppelingen om aan de slag te gaan met het maken van een aangepaste spraak-naar-tekst-ervaring.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: eff51c8568ce82c9d8d21bff7a2ba079c291679c
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007292"
---
# <a name="get-started-with-custom-voice"></a>Aan de slag met Custom Voice

[Aangepaste spraak](https://aka.ms/customvoice) is een set online-hulpprogram ma's waarmee u een herken bare, eenmalige stem kunt maken voor uw merk. Alles wat u nodig hebt om aan de slag te gaan zijn een aantal audio bestanden en de bijbehorende transcripties. Volg de onderstaande koppelingen om te beginnen met het maken van een aangepaste tekst-naar-spraak-ervaring.

## <a name="whats-in-custom-voice"></a>Wat is er in aangepaste spraak?

Voordat u begint met een aangepaste spraak, hebt u een Azure-account en een spraak service-abonnement nodig. Zodra u een account hebt gemaakt, kunt u uw gegevens voorbereiden, uw modellen trainen en testen, de spraak kwaliteit evalueren en uiteindelijk uw aangepaste spraak model implementeren.

In het onderstaande diagram worden de stappen beschreven voor het maken van een aangepast spraak model met behulp van de [aangepaste Voice Portal](https://aka.ms/customvoice). Gebruik de koppelingen voor meer informatie.

![Diagram van aangepaste spraak architectuur](media/custom-voice/custom-voice-diagram.png)

1. [Abonneren en een project maken](#set-up-your-azure-account) : een Azure-account maken en een spraak service-abonnement maken. Dit uniforme abonnement geeft u toegang tot spraak naar tekst, tekst naar spraak, spraak omzetting en de aangepaste Voice Portal. Maak vervolgens uw eerste aangepaste spraak project met behulp van uw abonnement voor de spraak service.

2. [Gegevens](how-to-custom-voice-create-voice.md#upload-your-datasets) uploaden (audio en tekst) uploaden met behulp van de aangepaste spraak portal of aangepaste spraak-API. Vanuit de portal kunt u scores voor uitspraak en signaal-naar-ruis-verhoudingen onderzoeken en evalueren. Zie [gegevens voorbereiden voor aangepaste spraak](how-to-custom-voice-prepare-data.md)voor meer informatie.

3. [Train uw model](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – gebruik uw gegevens om een aangepast spraak model voor tekst naar spraak te maken. U kunt een model in verschillende talen trainen. Wanneer u klaar bent, kunt u het model testen en, als u tevreden bent met het resultaat, het model implementeren.

4. [Uw model implementeren](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) : Maak een aangepast eind punt voor uw spraak model voor tekst naar spraak en gebruik dit voor spraak synthese in uw producten, hulpprogram ma's en toepassingen.

## <a name="custom-neural-voices"></a>Aangepaste Neural stemmen

Aangepaste spraak ondersteunt momenteel zowel de standaard-als de Neural-laag. Aangepaste neurale Voice biedt gebruikers de mogelijkheid om hoogwaardige stem modellen te bouwen, terwijl ze minder gegevens nodig hebben en maat regelen bieden om u te helpen AI-verantwoording te implementeren. We raden u aan aangepaste Neural-stem te gebruiken om realistischere stemmen te ontwikkelen voor meer natuurlijke conversatie-interfaces en uw klanten en eind gebruikers in staat te stellen om te profiteren van de nieuwste technologie voor tekst naar spraak, op een verantwoordelijke manier. Meer [informatie over aangepaste Neural-stem](https://aka.ms/CNV-Transparency-Note). 

> [!NOTE]
> Als onderdeel van de toezeg ging van micro soft om verantwoordelijk AI te ontwerpen, hebben we het gebruik van aangepaste Neural-stem beperkt. U krijgt mogelijk pas toegang tot de technologie nadat uw toepassingen zijn gecontroleerd en u hebt vastgelegd dat u deze kunt gebruiken in overeenstemming met onze nalevings AI-principes. Lees hier meer over ons [beleid over het beperken van toegang en de](https://aka.ms/gating-overview) [toepassing](https://aka.ms/customneural). De [talen](language-support.md#customization) en [regio's](regions.md#custom-voices) die worden ondersteund voor de Standard-en Neural-versie van aangepaste spraak, verschillen. Controleer de details voordat u begint.  

## <a name="set-up-your-azure-account"></a>Uw Azure-account instellen

Er is een abonnement op de spraak service vereist voordat u de Custom Speech Portal kunt gebruiken om een aangepast model te maken. Volg deze instructies voor het maken van een spraak service-abonnement in Azure. Als u geen Azure-account hebt, kunt u zich aanmelden voor een nieuwe.  

Zodra u een Azure-account en een spraak service-abonnement hebt gemaakt, moet u zich aanmelden bij de aangepaste Voice Portal en verbinding maken met uw abonnement.

1. Haal uw abonnements sleutel voor uw spraak service op uit de Azure Portal.
2. Meld u aan bij de [aangepaste Voice Portal](https://aka.ms/custom-voice).
3. Selecteer uw abonnement en maak een spraak project.
4. Als u wilt overschakelen naar een ander spraak abonnement, gebruikt u het tandwiel-pictogram in de bovenste navigatie balk.

> [!NOTE]
> U moet een F0 of een S0 Speech Service-sleutel hebben gemaakt in azure voordat u de service kunt gebruiken. Aangepaste Neural-Voice ondersteunt alleen de S0-laag. 

## <a name="how-to-create-a-project"></a>Een project maken

Inhoud, zoals gegevens, modellen, testen en eind punten, zijn ingedeeld in **projecten** in de aangepaste Voice Portal. Elk project is specifiek voor een land/taal en het geslacht van de stem die u wilt maken. U kunt bijvoorbeeld een project maken voor een vrouwelijke stem voor het chat-bots van uw Call Center dat Engels gebruikt in de Verenigde Staten (' nl-US ').

Als u uw eerste project wilt maken, selecteert u het tabblad **tekst naar spraak/aangepast stem** en klikt u vervolgens op **Nieuw project**. Volg de instructies in de wizard om het project te maken. Nadat u een project hebt gemaakt, ziet u vier tabbladen: **gegevens**, **training**, **testen** en **implementatie**. Gebruik de koppelingen in de [volgende stappen](#next-steps) voor meer informatie over het gebruik van elk tabblad.

> [!IMPORTANT]
> De [aangepaste Voice Portal](https://aka.ms/custom-voice) is onlangs bijgewerkt. Als u eerdere gegevens, modellen, tests en gepubliceerde eind punten in de CRIS.ai-portal of met Api's hebt gemaakt, moet u een nieuw project maken in de nieuwe portal om verbinding met deze oude entiteiten te maken.

## <a name="how-to-migrate-to-custom-neural-voice"></a>Migreren naar Custom Neural Voice

Als u de niet-Neural (of standaard) aangepaste Voice gebruikt, kunt u het beste de migratie naar aangepaste Neural-stem uitvoeren, direct na de volgende stappen. Als u overstapt op aangepaste neurale Voice, kunt u realistischere stemmen ontwikkelen voor nog meer natuurlijke conversatie-interfaces en kunnen uw klanten en eind gebruikers profiteren van de nieuwste tekst-naar-spraak-technologie, op een verantwoordelijke manier. 

1. Lees hier meer over ons [beleid over het beperken van toegang en de](https://aka.ms/gating-overview) [toepassing](https://aka.ms/customneural). Houd er rekening mee dat de toegang tot de aangepaste Neural Voice-service is onderhevig aan de enige keuze van micro soft op basis van onze geschiktheids criteria. Klanten kunnen pas toegang krijgen tot de technologie nadat hun toepassing is [gecontroleerd en ze](https://microsoft.com/ai/responsible-ai) hebben doorgegaan met het gebruik van deze in overeenstemming met onze nalevings voorwaarden en de [gedrags code](https://aka.ms/custom-neural-code-of-conduct). 
2. Zodra uw toepassing is goedgekeurd, ontvangt u de toegang tot de training ' Neural '. Zorg ervoor dat u zich bij de [aangepaste Voice Portal](https://speech.microsoft.com/customvoice) aanmeldt met hetzelfde Azure-abonnement dat u in uw toepassing hebt verstrekt. 
    > [!IMPORTANT]
    > Voor het beschermen van stem talen en om training van spraak modellen te voor komen met niet-geautoriseerde opnamen of zonder de bevestiging van de Voice-talen, moet de klant een vastgelegde verklaring van de Voice-talen uploaden die zijn of haar toestemming verleent. Zorg er bij het voorbereiden van het opname script voor dat u deze zin opneemt. "I [Geef uw voor-en achternaam op] Houd er rekening mee dat de opnamen van mijn stem worden gebruikt door [de naam van het bedrijf te vermelden] om een synthetische versie van mijn stem te maken en te gebruiken."
    > Deze zin moet worden geüpload naar het tabblad **spraak-talen** als een mondelinge toestemmings bestand. Deze wordt gebruikt om te controleren of de opnamen in uw trainings gegevens sets worden uitgevoerd door dezelfde persoon die de toestemming doet.
3. Nadat het aangepaste Neural-spraak model is gemaakt, implementeert u het spraak model op een nieuw eind punt. Als u een nieuw aangepast spraak eindpunt wilt maken met uw Neural-spraak model, gaat u naar **tekst-naar-spraak > aangepaste spraak >-implementatie**. Selecteer **model implementeren** en voer een **naam** en **Beschrijving** in voor het aangepaste eind punt. Selecteer vervolgens het aangepaste Neural-spraak model dat u wilt koppelen aan dit eind punt en bevestig de implementatie.  
4. Werk uw code bij in uw apps als u een nieuw eind punt met een nieuw model hebt gemaakt. 

## <a name="next-steps"></a>Volgende stappen

- [Aangepaste spraak gegevens voorbereiden](how-to-custom-voice-prepare-data.md)
- [Een Custom Voice maken](how-to-custom-voice-create-voice.md)
- [Hand leiding: uw stem voorbeelden vastleggen](record-custom-voice-samples.md)
