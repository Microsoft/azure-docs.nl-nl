---
title: Op rollen gebaseerd toegangs beheer van Azure-Custom Vision
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u toegangs beheer op basis van rollen voor uw Custom Vision-projecten kunt configureren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: d265b6698a87c1c651dff1413cf48dd4984cd606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94616889"
---
# <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure

Custom Vision biedt ondersteuning voor op rollen gebaseerd toegangsbeheer voor Azure (Azure RBAC), een autorisatiesysteem voor het beheren van afzonderlijke toegang tot Azure-resources. Met behulp van Azure RBAC kunt u verschillende team leden verschillende machtigings niveaus voor uw Custom Vision projecten toewijzen. Zie de [documentatie van Azure RBAC](../../role-based-access-control/index.yml)voor meer informatie over Azure RBAC.

## <a name="add-role-assignment-to-custom-vision-resource"></a>Roltoewijzing toevoegen aan Custom Vision resource

U kunt Azure RBAC toewijzen aan een Custom Vision bron. Als u toegang wilt verlenen aan een Azure-resource, voegt u een roltoewijzing toe.
1. Selecteer in [Azure Portal](https://ms.portal.azure.com/) de optie **Alle services**. 
1. Selecteer vervolgens de **Cognitive Services** en navigeer naar uw specifieke Custom Vision training-resource.
   > [!NOTE]
   > U kunt ook Azure RBAC instellen voor volledige resource groepen, abonnementen of beheer groepen. Hiertoe selecteert u het gewenste bereik niveau en gaat u vervolgens naar het gewenste item (bijvoorbeeld **resource groepen** selecteren en klikt u vervolgens op de gewenste resource groep).
1. Selecteer **toegangs beheer (IAM)** in het navigatie deel venster aan de linkerkant.
1. Selecteer het **tabblad roltoewijzingen om de roltoewijzingen** voor dit bereik weer te geven.
1. Selecteer **Toevoegen** -> **Roltoewijzing toevoegen**.
1. Selecteer in de vervolg keuzelijst **rol** een rol die u wilt toevoegen.
1. Selecteer een gebruiker, groep, Service-Principal of beheerde identiteit in de **selectie** lijst. Als de beveiligingsprincipal niet in de lijst wordt weer gegeven, kunt u het selectie vakje typen om in de map te zoeken naar weergave namen, e-mail adressen en object-id's.
1. Selecteer **Opslaan** om de rol toe te wijzen.

Binnen een paar minuten wordt het doel toegewezen aan de geselecteerde rol bij het geselecteerde bereik.

## <a name="custom-vision-role-types"></a>Custom Vision typen rollen

Gebruik de volgende tabel om de toegangs behoeften voor uw Custom Vision-resources te bepalen.

|Rol  |Machtigingen  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | Volledige toegang tot de projecten, inclusief de mogelijkheid om een project te maken, bewerken of verwijderen.        |
|`Cognitive Services Custom Vision Trainer`     | Volledige toegang, behalve de mogelijkheid om een project te maken of te verwijderen. Trainers kunnen projecten bekijken en bewerken en de modellen trainen, publiceren, publiceren ongedaan maken of exporteren.        |
|`Cognitive Services Custom Vision Labeler`     | De mogelijkheid om trainings afbeeldingen te uploaden, te bewerken of te verwijderen en tags te maken, toe te voegen, te verwijderen of te verwijderen. Met labelers kunnen projecten worden weer gegeven, maar kunnen ze niet worden bijgewerkt dan de afbeeldingen en tags van de training.         |
|`Cognitive Services Custom Vision Deployment`     | De mogelijkheid om de modellen te publiceren, ongedaan te maken of te exporteren. Met deployers kunnen projecten worden weer gegeven, maar er kan geen project, trainings afbeeldingen of tags worden bijgewerkt.        |
|`Cognitive Services Custom Vision Reader`     | De mogelijkheid om projecten weer te geven. Lezers kunnen geen wijzigingen aanbrengen.        |