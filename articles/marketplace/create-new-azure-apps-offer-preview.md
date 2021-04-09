---
title: Een preview-doel groep voor uw Azure-toepassing aanbieding toevoegen
description: Meer informatie over het toevoegen van een preview-doel groep voor uw Azure-toepassings aanbieding in Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370086"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>Een preview-doel groep voor uw Azure-toepassing aanbieding toevoegen

In dit artikel wordt beschreven hoe u een preview-doel groep configureert voor een Azure-toepassing aanbieding in de commerciële Marketplace. U moet een preview-doel groep definiëren die uw aanbieding kan bekijken voordat deze live gaat.

## <a name="define-a-preview-audience"></a>Een preview-doel groep definiëren

Op de pagina **voor beeld van doel groep** kunt u een beperkt publiek definiëren die uw Azure-toepassing-aanbieding kan controleren voordat u deze live publiceert naar het bredere publiek van de Marketplace. U definieert de preview-doel groep met behulp van Azure-abonnement-Id's, samen met een optionele beschrijving voor elk. Geen van deze velden kan worden gezien door klanten. U kunt uw Azure-abonnements-ID vinden op de pagina **abonnementen** in azure Portal.

Voeg mini maal één en Maxi maal 10 Azure-abonnement-Id's toe (Maxi maal 10) of door een CSV-bestand te uploaden (Maxi maal 100) om te bepalen wie uw aanbieding kan bekijken voordat deze Live wordt gepubliceerd. Als uw aanbieding al Live is, kunt u nog steeds een preview-doel opgeven voor het testen van wijzigingen in de aanbieding of updates voor uw aanbieding.

> [!NOTE]
> Een preview-doel groep wijkt af van een persoonlijke doel groep. Een preview-doel groep is toegang tot uw aanbieding toegestaan voordat deze Live in de online winkels wordt gepubliceerd. Ze kunnen alle plannen bekijken en valideren, met inbegrip van de abonnementen die alleen beschikbaar zijn voor een privé publiek nadat uw aanbieding volledig naar de Marketplace is gepubliceerd. U kunt een plan alleen beschikbaar maken voor een privé publiek. Een persoonlijke doel groep (gedefinieerd in het tabblad **Beschik baarheid** van een plan) heeft exclusieve toegang tot een bepaald abonnement.

### <a name="add-email-addresses-manually"></a>E-mail adressen hand matig toevoegen

1. Voeg op de pagina **voor beeld van doel groep** één Azure-abonnement-id toe en een optionele beschrijving in de meegeleverde vakken.
1. Als u een ander e-mail adres wilt toevoegen, selecteert u de koppeling **id toevoegen (Maxi maal 10)** .
1. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad: technische configuratie.
1. Ga naar de [volgende stappen](#next-steps).

### <a name="add-email-addresses-using-the-csv-file"></a>E-mail adressen toevoegen met het CSV-bestand

1. Selecteer op de pagina **voor beeld van doel groep** de koppeling **doel groep exporteren (CSV)** .
1. Open de. CSV-bestand in een toepassing, zoals micro soft Excel.
1. In het. Het CSV-bestand in de kolom id voert u de id van het Azure-abonnement in dat u aan de preview **-** doel groep wilt toevoegen.
1. In de kolom **Beschrijving** kunt u desgewenst een beschrijving voor elk e-mail adres toevoegen.
1. Voeg in de kolom **type** **SubscriptionID** toe aan elke rij met een e-mail adres.
1. Sla het bestand op als een. CSV-bestand.
1. Selecteer op de pagina **voor beeld van doel groep** de koppeling **doel groep importeren (CSV)** .
1. Selecteer **Ja** in het **bevestigings** venster.
1. Selecteer de. CSV-bestand en selecteer **openen**.
1. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad: technische configuratie.

## <a name="next-steps"></a>Volgende stappen

- [Technische gegevens voor uw Azure-toepassing aanbieding toevoegen](create-new-azure-apps-offer-technical.md)
