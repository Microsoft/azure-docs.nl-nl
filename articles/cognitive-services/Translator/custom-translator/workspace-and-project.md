---
title: Wat is een werk ruimte en project? -Aangepaste vertaler
titleSuffix: Azure Cognitive Services
description: In dit artikel worden de verschillen tussen een werk ruimte en een project beschreven, evenals project categorieën en labels voor de Custom Translator-service.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 0abda0307e3cdbd0e73e131bee072172375198eb
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898050"
---
# <a name="what-is-a-custom-translator-workspace"></a>Wat is een aangepaste Translator-werk ruimte?

Een werk ruimte is een werk ruimte voor het opstellen en bouwen van uw aangepaste Vertaal systeem. Een werk ruimte kan meerdere projecten, modellen en documenten bevatten. Alle werkzaamheden in het aangepaste conversie programma bevinden zich in een specifieke werk ruimte.

De werk ruimte is persoonlijk voor u en de personen die u uitnodigt in uw werk ruimte. Niet-uitgenodigde personen hebben geen toegang tot de inhoud van uw werk ruimte. U kunt zoveel mensen uitnodigen als u wilt in uw werk ruimte en de toegang op elk gewenst moment wijzigen of verwijderen. U kunt ook een nieuwe werk ruimte maken. Een werk ruimte bevat standaard geen projecten of documenten die zich in uw andere werk ruimten bevinden.

## <a name="what-is-a-custom-translator-project"></a>Wat is een aangepast Vertaal project?

Een project is een wrapper voor een model, documenten en tests. Elk project bevat automatisch alle documenten die naar die werk ruimte worden geüpload die het juiste taal paar hebben. Als u bijvoorbeeld zowel een Engels naar een Spaans project als een Spaans naar Engels project hebt, worden dezelfde documenten in beide projecten opgenomen. Aan elk project is een CategoryID gekoppeld die wordt gebruikt bij het uitvoeren van een query op de [v3 API](../reference/v3-0-translate.md?tabs=curl) voor vertalingen. CategoryID is de para meter die wordt gebruikt voor het ophalen van vertalingen van een aangepast systeem dat is gebouwd met aangepaste vertaler.

## <a name="project-categories"></a>Project Categorieën

De categorie identificeert het domein: het gebied met de terminologie en de stijl die u wilt gebruiken: voor uw project. Kies de categorie die het meest relevant is voor uw documenten. In sommige gevallen is uw keuze van de categorie rechtstreeks van invloed op het gedrag van de aangepaste vertaler.

Er zijn twee sets basis lijn modellen. Ze zijn algemeen en technologie. Als de categorie **technologie** is geselecteerd, worden de technologie basislijn modellen gebruikt. Voor een andere categorie selectie worden de algemene basis lijn modellen gebruikt. Het technologie basis model is goed in het technologie domein, maar de lagere kwaliteit wordt weer gegeven als de zinnen die voor de vertaling worden gebruikt, niet binnen het technologie domein vallen. We suggereren dat klanten categorie technologie alleen willen selecteren als zinnen strikt binnen het technologie domein vallen.

In dezelfde werk ruimte kunt u projecten maken voor hetzelfde taal paar in verschillende categorieën. Aangepaste vertaler voor komt het maken van een dubbel project met dezelfde taal paar en categorie. Als u een label toepast op uw project, kunt u deze beperking vermijden. Gebruik geen labels tenzij u Vertaal systemen voor meerdere clients bouwt, omdat het toevoegen van een uniek label aan uw project wordt weer gegeven in de CategorieId van uw projecten.

## <a name="project-labels"></a>Project etiketten

Met aangepaste vertaler kunt u een project label toewijzen aan uw project. Het project label onderscheidt tussen meerdere projecten met hetzelfde taal paar en dezelfde-categorie. Vermijd het gebruik van project etiketten als best practice, tenzij dat nodig is.

Het project label wordt gebruikt als onderdeel van de CategoryID. Als het project label wordt in-of uitgesteld op hetzelfde niveau is ingesteld voor projecten, delen projecten met dezelfde categorie en *verschillende* taal paren hetzelfde CategoryID. Deze methode is handig omdat u of uw klant kunt overschakelen tussen talen wanneer u de Text Translator-API gebruikt zonder dat u zich zorgen hoeft te maken over een CategoryID dat uniek is voor elk project.

Als ik bijvoorbeeld vertalingen in het technologie domein wil inschakelen van Engels naar Frans en van Frans naar Engels, zou ik twee projecten maken: één voor Engels- \> Frans en één voor Frans- \> Engels. Ik zou dezelfde categorie (technologie) voor beide opgeven en het project label leeg laten. De CategoryID voor beide projecten zou overeenkomen, dus kan ik de API voor zowel Engelse als Franse vertalingen opvragen zonder mijn categorie nummer te wijzigen.

Als u een taal serviceprovider bent en meerdere klanten wilt voorzien van verschillende modellen die hetzelfde categorie-en taal paar behouden, kunt u een project label gebruiken om onderscheid te maken tussen klanten.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over [training en model](training-and-model.md) om te weten hoe u efficiënt een omzettings model bouwt.