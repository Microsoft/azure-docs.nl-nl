---
title: Een preview-doel groep voor uw beheerde service-aanbieding toevoegen
description: Meer informatie over het toevoegen van een preview-doel groep voor uw beheerde service aanbieding in het micro soft partner centrum.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97918277"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>Een preview-doel groep voor uw beheerde service-aanbieding toevoegen

In dit artikel wordt beschreven hoe u een preview-doel groep configureert voor een beheerde service aanbieding in de commerciële Marketplace met behulp van partner centrum. De preview-doel groep kan uw aanbieding bekijken voordat deze live gaat.

## <a name="define-a-preview-audience"></a>Een preview-doel groep definiëren

Op de pagina **voor beeld van doel groep** kunt u een beperkt publiek definiëren die uw Managed Service-aanbieding kan controleren voordat u deze live publiceert naar het bredere publiek van de Marketplace. U definieert de preview-doel groep met behulp van Azure-abonnement-Id's, samen met een optionele beschrijving voor elk. Geen van deze velden kan worden gezien door klanten. U kunt uw Azure-abonnements-ID vinden op de pagina **abonnementen** op de Azure Portal.

Voeg ten minste één Azure-abonnements-ID afzonderlijk toe (Maxi maal 10) of door een CSV-bestand te uploaden (Maxi maal 100) om te bepalen wie uw aanbieding kan bekijken voordat deze Live wordt gepubliceerd. Als uw aanbieding al Live is, kunt u nog steeds een preview-doel opgeven voor het testen van updates voor uw aanbieding.

> [!NOTE]
> Een *Preview* -doel groep wijkt af van een *persoonlijke* doel groep. Een preview-doel groep is toegang tot uw aanbieding toegestaan voordat deze Live in de online winkels wordt gepubliceerd. Ze kunnen alle plannen bekijken en valideren, met inbegrip van degenen die alleen beschikbaar zijn voor een privé publiek nadat uw aanbieding volledig is gepubliceerd op de Marketplace. U kunt een plan alleen beschikbaar maken voor een privé publiek. Een persoonlijke doel groep (gedefinieerd in het tabblad Beschik baarheid van een plan) heeft exclusieve toegang tot een bepaald abonnement.

## <a name="add-email-addresses-manually"></a>E-mail adressen hand matig toevoegen

1. Voeg op de pagina **voor beeld van doel groep** één Azure-abonnement-id toe en een optionele beschrijving in de meegeleverde vakken.
2. Als u een ander e-mail adres wilt toevoegen, selecteert u de koppeling **id toevoegen (Maxi maal 10)** .
3. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad.

## <a name="add-email-addresses-using-a-csv-file"></a>E-mail adressen toevoegen met behulp van een CSV-bestand

1. Selecteer op **de pagina voor beeld van doel groep** voorvertonen de koppeling **doel groep exporteren (CSV)** .
2. Open het CSV-bestand. Voer in de kolom id de Azure **-abonnements-** id's in die u wilt toevoegen aan de preview-doel groep.
3. In de kolom **Beschrijving** hebt u de optie om een beschrijving voor elk item toe te voegen.
4. In de kolom **type** voegt u **SubscriptionId** toe aan elke rij met een id.
5. Sla het bestand op als een CSV-bestand.
6. Selecteer op de pagina **voor beeld van doel groep** de koppeling **doel groep importeren (CSV)** .
7. Selecteer **Ja** in het dialoog venster **bevestigen** en upload het CSV-bestand.
8. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad.

## <a name="next-steps"></a>Volgende stappen

* [Plannen maken](create-managed-service-offer-plans.md)
