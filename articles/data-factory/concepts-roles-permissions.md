---
title: Rollen en machtigingen voor Azure Data Factory
description: Hierin worden de rollen en machtigingen beschreven die nodig zijn om gegevens fabrieken te maken en om met onderliggende resources te werken.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.openlocfilehash: cec5df9a5046e912ab8542c91bde4344affa0925
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100364474"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Rollen en machtigingen voor Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


In dit artikel worden de functies beschreven die nodig zijn voor het maken en beheren van Azure Data Factory resources, en de machtigingen die door die rollen worden verleend.

## <a name="roles-and-requirements"></a>Functies en vereisten

Als u Data Factory-exemplaren wilt maken, moet het gebruikers account waarmee u zich bij Azure aanmeldt, lid zijn van de rol *Inzender* , de rol van *eigenaar* of een *beheerder* van het Azure-abonnement. Als u de machtigingen die u in het abonnement hebt, wilt bekijken, gaat u naar Azure Portal, selecteert u rechtsboven uw gebruikersnaam en selecteert u vervolgens **Machtigingen**. Als u toegang tot meerdere abonnementen hebt, moet u het juiste abonnement selecteren. 

Als u onderliggende resources wilt maken en beheren voor Data Factory, waaronder gegevenssets, gekoppelde services, pijplijnen, triggers en integratieruntimes, zijn de volgende vereisten van toepassing:
- Als u onderliggende resources in de Azure Portal wilt maken en beheren, moet u behoren tot de rol **Data Factory Inzender** op het niveau van de **resource groep** of hoger.
- Voor het maken en beheren van onderliggende resources met PowerShell of de SDK is de rol **Inzender** op minimaal het resourceniveau voldoende.

Zie het artikel [Rollen toevoegen](../cost-management-billing/manage/add-change-subscription-administrator.md) voor voorbeelden van instructies voor het toevoegen van een gebruiker aan een rol.

## <a name="set-up-permissions"></a>Machtigingen instellen

Nadat u een Data Factory hebt gemaakt, wilt u mogelijk andere gebruikers laten werken met de data factory. Als u deze toegang wilt verlenen aan andere gebruikers, moet u deze toevoegen aan de ingebouwde rol **Data Factory Inzender** voor de **resource groep** die de Data Factory bevat.

### <a name="scope-of-the-data-factory-contributor-role"></a>Bereik van de rol Data Factory Inzender

Door het lidmaatschap van de rol **Data Factory Inzender** kunnen gebruikers het volgende doen:
- Gegevens fabrieken en onderliggende resources maken, bewerken en verwijderen, waaronder gegevens sets, gekoppelde services, pijp lijnen, triggers en Integration Runtimes.
- Resource Manager-sjablonen implementeren. Implementatie van Resource Manager is de implementatie methode die wordt gebruikt door Data Factory in het Azure Portal.
- App Insights-waarschuwingen voor een data factory beheren.
- Ondersteunings tickets maken.

Zie [Data Factory rol Inzender](../role-based-access-control/built-in-roles.md#data-factory-contributor)voor meer informatie over deze rol.

### <a name="resource-manager-template-deployment"></a>Implementatie van Resource Manager-sjabloon

Met de rol **Data Factory Inzender** , op het niveau van de resource groep of hoger, kunnen gebruikers Resource Manager-sjablonen implementeren. Als gevolg hiervan kunnen leden van de rol Resource Manager-sjablonen gebruiken voor het implementeren van zowel gegevens fabrieken als hun onderliggende resources, inclusief gegevens sets, gekoppelde services, pijp lijnen, triggers en Integration Runtimes. Als u lidmaatschap van deze rol gebruikt, kan de gebruiker geen andere resources maken.

Machtigingen voor Azure opslag plaatsen en GitHub zijn onafhankelijk van Data Factory machtigingen. Als gevolg hiervan kan een gebruiker met opslag plaats-machtigingen die alleen lid is van de rol lezer, Data Factory onderliggende resources bewerken en wijzigingen door voeren in de opslag plaats, maar kan deze wijzigingen niet publiceren.


> [!IMPORTANT]
> Met de implementatie van de Resource Manager-sjabloon met de rol **Data Factory Inzender** worden uw machtigingen niet verhoogd. Als u bijvoorbeeld een sjabloon implementeert waarmee een virtuele machine van Azure wordt gemaakt en u geen machtiging hebt om virtuele machines te maken, mislukt de implementatie met een autorisatie fout.

   In de context van publiceren worden de machtigingen **micro soft. DataFactory/factories/schrijven** toegepast op de volgende modi.
- Deze machtiging is alleen vereist in de modus Live wanneer de klant de algemene para meters wijzigt.
- Deze machtiging is altijd vereist in de Git-modus, aangezien na elke keer dat de klant wordt gepubliceerd, het object Factory met de laatste doorvoer-ID moet worden bijgewerkt.

### <a name="custom-scenarios-and-custom-roles"></a>Aangepaste scenario's en aangepaste rollen

Soms moet u mogelijk verschillende toegangs niveaus toekennen voor verschillende gebruikers van data factory. Bijvoorbeeld:
- U hebt mogelijk een groep nodig waar gebruikers alleen machtigingen hebben voor een specifieke data factory.
- Het is ook mogelijk dat u een groep hebt waar gebruikers alleen een data factory (of fabrieken) kunnen bewaken, maar niet kan wijzigen.

U kunt deze aangepaste scenario's verzorgen door aangepaste rollen te maken en gebruikers toe te wijzen aan deze rollen. Zie [aangepaste rollen in azure](..//role-based-access-control/custom-roles.md)voor meer informatie over aangepaste rollen.

Hier volgen enkele voor beelden van hoe u met aangepaste rollen kunt profiteren:

- Hiermee kan een gebruiker een data factory in een resource groep maken, bewerken of verwijderen vanuit de Azure Portal.

  Wijs de ingebouwde rol **Data Factory Inzender** toe op het niveau van de resource groep voor de gebruiker. Als u toegang wilt verlenen tot een data factory in een abonnement, wijst u de rol toe op abonnements niveau.

- Een gebruiker kan een data factory weer geven (lezen) en bewaken, maar niet bewerken of wijzigen.

  Wijs de ingebouwde rol **lezer** toe aan de Data Factory resource voor de gebruiker.

- Laat een gebruiker één data factory in de Azure Portal bewerken.

  Voor dit scenario zijn twee roltoewijzingen vereist.

  1. Wijs de ingebouwde rol **Inzender** op het Data Factory niveau toe.
  2. Maak een aangepaste rol met de machtiging  **micro soft. resources/Deployments/**. Wijs deze aangepaste rol toe aan de gebruiker op het niveau van de resource groep.

- Hiermee kan een gebruiker de verbinding in een gekoppelde service testen of een voor beeld van de gegevens in een gegevensset bekijken

    Maak een aangepaste rol met machtigingen voor de volgende acties: **micro soft. DataFactory/factorions/getFeatureValue/Read** en **micro soft. DataFactory/factories/getDataPlaneAccess/Action**. Wijs deze aangepaste rol toe aan de data factory resource voor de gebruiker.

- Laat een gebruiker een data factory bijwerken vanuit Power shell of de SDK, maar niet in de Azure Portal.

  Wijs de ingebouwde rol **Inzender** toe aan de Data Factory resource voor de gebruiker. Met deze rol kan de gebruiker de resources in de Azure Portal zien, maar de gebruiker heeft geen toegang tot de knoppen  **publiceren** en **publiceren** .


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over rollen in azure- [definities van functie-begrippen](../role-based-access-control/role-definitions.md)

- Meer informatie over de rol **Inzender voor Data Factory** - [Data Factory rol](../role-based-access-control/built-in-roles.md#data-factory-contributor).
