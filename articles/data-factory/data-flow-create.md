---
title: Een toewijzings gegevens stroom maken
description: Een gegevens stroom voor Azure Data Factory toewijzing maken
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: eaf36cc2690b3c0f8922c05432b3197b4ff30d9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93026051"
---
# <a name="create-azure-data-factory-data-flow"></a>Gegevensstroom voor Azure Data Factory maken

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Het toewijzen van gegevens stromen in ADF biedt een manier om gegevens op schaal te transformeren zonder dat hiervoor benodigde code hoeft te worden uitgevoerd. U kunt een gegevens transformatie taak ontwerpen in de ontwerp functie voor gegevens stromen door een reeks trans formaties te maken. Begin met een wille keurig aantal bron transformaties gevolgd door de stappen voor gegevens transformatie. Voltooi vervolgens uw gegevens stroom met Sink om uw resultaten in een bestemming te brengen.

Ga aan de slag door eerst een nieuwe v2-Data Factory te maken op basis van de Azure Portal. Nadat u de nieuwe fabriek hebt gemaakt, klikt u op de tegel & controle maken om de Data Factory-gebruikers interface te starten.

![Scherm afbeelding toont het nieuwe data factory deel venster met v2 geselecteerd voor versie.](media/data-flow/v2portal.png "gegevens stroom maken")

Zodra u zich in de Data Factory-gebruikers interface bevindt, kunt u voorbeeld gegevens stromen gebruiken. De voor beelden zijn beschikbaar in de galerie met ADF-sjablonen. Maak in ADF "pijp lijn van sjabloon" en selecteer de categorie gegevens stroom in de sjabloon galerie.

![Scherm afbeelding toont het tabblad gegevens stroom met de geselecteerde gegevens stroom transformeren van gegevens.](media/data-flow/template.png "gegevens stroom maken")

U wordt gevraagd om uw Azure Blob Storage-account gegevens in te voeren.

![In de scherm afbeelding ziet u de gegevens stroom deel venster gegevens transformeren, waarin u gebruikers invoer kunt invoeren.](media/data-flow/template2.png "gegevens stroom maken 2")

[De gegevens die voor deze steek proeven worden gebruikt, kunt u hier vinden](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Down load de voorbeeld gegevens en sla de bestanden op in de Azure Blob Storage-accounts, zodat u de voor beelden kunt uitvoeren.

## <a name="create-new-data-flow"></a>Nieuwe gegevens stroom maken

Gebruik de knop voor het maken van een resource plus teken in de ADF-gebruikers interface om gegevens stromen te maken.

![Scherm afbeelding toont gegevens stroom die is geselecteerd in het menu Factory resources.](media/data-flow/newresource.png "Nieuwe resource")

## <a name="next-steps"></a>Volgende stappen

Begin met het bouwen van uw gegevens transformatie met een [bron transformatie](data-flow-source.md).
