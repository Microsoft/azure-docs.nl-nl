---
title: Migreren-Portal
description: Migreren naar Cloud Services (uitgebreide ondersteuning) met behulp van de Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 79889b08baa80dc67b30ae445004e37d9f9fe295
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286853"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>Migreren naar Cloud Services (uitgebreide ondersteuning) met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u de Azure Portal kunt gebruiken om te migreren van [Cloud Services (klassiek)](../cloud-services/cloud-services-choose-me.md) naar [Cloud Services (uitgebreide ondersteuning)](overview.md).

> [!IMPORTANT]
> Migratie van Cloud Services (klassiek) naar Cloud Services (uitgebreide ondersteuning) met behulp van het migratie programma is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

**Zorg ervoor dat u een beheerder bent voor het abonnement.**

Als u deze migratie wilt uitvoeren, moet u worden toegevoegd als een cobeheerder voor het abonnement in de [Azure Portal](https://portal.azure.com).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer in het menu **hub** de optie **abonnement**. Als u deze niet ziet, selecteert u **alle services**.
3. Zoek de juiste abonnements vermelding en Bekijk het veld **mijn rol** . Voor een cobeheerder moet de waarde *account beheerder* zijn.

Als u geen mede beheerder kunt toevoegen, neemt u contact op met een service beheerder of [mede beheerder](../role-based-access-control/classic-administrators.md) voor het abonnement om aan de slag te gaan.

**Registreren voor de resource provider voor migratie**

1. Registreer u bij de resource provider `Microsoft.ClassicInfrastructureMigrate` voor migratie en de preview-functie `Cloud Services` onder micro soft. Compute-naam ruimte met behulp van de [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider-1).  
1. Wacht vijf minuten voordat de registratie is voltooid en controleer vervolgens de status van de goed keuring. 

## <a name="migrate-your-cloud-service-resources"></a>Uw Cloud service Resources migreren

1. Ga naar de [blade Cloud Services (klassieke) Portal](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames). 
2. Selecteer de Cloud service die u wilt migreren.
3. Selecteer de Blade **migreren naar arm** .

    > [!NOTE]
    > Als u een Cloud Services (klassiek) migreert die zich in een virtueel netwerk (klassiek) bevindt, wordt er een banner bericht weer gegeven waarin u wordt gevraagd de Blade virtueel netwerk (klassiek) te verplaatsen.
    > U gaat naar de Blade virtueel netwerk (klassiek) voor het volt ooien van de migratie van zowel het virtuele netwerk (klassiek) als de Cloud Services (klassieke) implementaties.
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="Afbeelding toont het verplaatsen van een klassiek virtueel netwerk in de Azure Portal.":::
 

4. Valideer de migratie. 

    Als de validatie is geslaagd, worden alle implementaties ondersteund en klaar om te worden voor bereid.  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="Afbeelding toont de Blade migreren naar ARM in de Azure Portal.":::

    Als validatie mislukt, wordt een lijst met niet-ondersteunde scenario's weer gegeven en moet deze worden hersteld voordat de migratie kan worden voortgezet. 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="De afbeelding bevat een validatie fout in de Azure Portal.":::

5. Bereid u voor op de migratie.

    Als de voor bereiding is geslaagd, is de migratie klaar voor door voeren.
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="De afbeelding toont de validatie die wordt door gegeven in de Azure Portal.":::

    Als de voor bereiding mislukt, raadpleegt u de fout, verhelpt u eventuele problemen en voert u de voor bereiding opnieuw uit. 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="De afbeelding bevat een validatie fout.":::

      Na de voor bereiding zijn alle Cloud Services in een virtueel netwerk beschikbaar voor lees bewerkingen met behulp van Cloud Services (klassiek) en Cloud Services (uitgebreide ondersteuning) Azure Portal Blade. De implementatie van de Cloud service (uitgebreide ondersteuning) kan nu worden getest om goed te werken voordat de migratie wordt voltooid. 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="In de afbeelding worden test-Api's weer gegeven in de portal-Blade.":::

6.  **(Optioneel)** Migratie afbreken. 
    
    Als u ervoor kiest de migratie uit te voeren, gebruikt u de knop **afbreken** om de vorige stappen terug te draaien. De Cloud Services (klassieke) implementatie wordt vervolgens ontgrendeld voor alle ruwe bewerkingen.

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="In de afbeelding wordt de validatie weer gegeven.":::

    Als afbreken mislukt, selecteert u **opnieuw proberen afbreken**. Het probleem moet worden opgelost door opnieuw op te lossen. Zo niet, neem dan contact op met de ondersteuning. 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="Afbeelding toont fout bericht over validatie fout.":::

7.  Migratie door voeren.

    >[!IMPORTANT]
    > Zodra u de migratie doorvoert, is er geen optie om terug te draaien. 
    
    Typ Ja om de migratie te bevestigen en door te voeren. De migratie is nu voltooid. De migratie van de gemigreerde Cloud Services-implementatie (uitgebreide ondersteuning) is ontgrendeld voor alle bewerkingen. 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de sectie wijzigingen in de configuratie van de [migratie](in-place-migration-overview.md#post-migration-changes) voor een overzicht van de wijzigingen in de implementatie bestanden, Automation en andere kenmerken van de nieuwe implementatie van Cloud Services (uitgebreide ondersteuning). 
