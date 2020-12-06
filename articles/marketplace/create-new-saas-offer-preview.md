---
title: Een preview-doel groep voor uw SaaS-aanbieding toevoegen in micro soft Commercial Marketplace
description: Het toevoegen van een preview-doel groep voor uw software als een service (SaaS)-aanbieding in het micro soft partner centrum.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 041df9b77e85f09d2cf680773edc995c6288acaa
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746417"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>Een preview-doel groep voor uw SaaS-aanbieding toevoegen

Wanneer u uw SaaS-aanbieding (Software as a Service) maakt in het partner centrum, moet u een preview-doel groep definiëren die uw aanbieding kan bekijken voordat deze live gaat. In dit artikel wordt uitgelegd hoe u een preview-doel groep kunt configureren.

> [!NOTE]
> Als u ervoor kiest om trans acties onafhankelijk te verwerken, wordt deze optie niet weer geven. Ga in plaats daarvan naar [de verkoop van uw SaaS-aanbieding](create-new-saas-offer-marketing.md).

## <a name="define-a-preview-audience"></a>Een preview-doel groep definiëren

Op het tabblad **voor beeld van doel groep** kunt u een beperkt publiek definiëren die uw SaaS-aanbieding kan controleren voordat u deze live publiceert naar het bredere publiek van de Marketplace. U kunt uitnodigingen verzenden naar e-mail adressen van micro soft-accounts (MSA) of Azure Active Directory (Azure AD). Voeg mini maal één en Maxi maal 10 e-mail adressen hand matig toe of importeer Maxi maal 20 met een CSV-bestand. U kunt de lijst met voor beelden van doel groepen op elk gewenst moment bijwerken.

> [!NOTE]
> Een preview-doel groep wijkt af van een persoonlijke doel groep. Een preview-doel groep is toegang tot uw aanbieding toegestaan voordat deze Live in de online winkels wordt gepubliceerd. U kunt er ook voor kiezen om een plan te maken en dit alleen beschikbaar te maken voor een privé doel groep. Privé plannen worden beschreven in [de procedure voor het maken van plannen voor uw SaaS-aanbieding](create-new-saas-offer-plans.md).

### <a name="add-email-addresses-manually"></a>E-mail adressen hand matig toevoegen

1. Voeg op de pagina **voor beeld van doel groep** een enkel e-mail adres voor Azure AD of MSA toe en een optionele beschrijving in de meegeleverde vakken.
1. Als u een ander e-mail adres wilt toevoegen, selecteert u de koppeling **nog een E-mail toevoegen** .
1. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad: **technische configuratie**.
1. Ga door met [het toevoegen van technische gegevens voor uw SaaS-aanbieding](create-new-saas-offer-technical.md).

### <a name="add-email-addresses-using-the-csv-file"></a>E-mail adressen toevoegen met het CSV-bestand

1. Selecteer op de pagina **voor beeld van doel groep** de koppeling **doel groep exporteren (CSV)** .
1. Open de. CSV-bestand in een toepassing, zoals micro soft Excel.
1. In het. Het CSV-bestand in de kolom **id** voert u de e-mail adressen in die u wilt toevoegen aan de preview-doel groep.
1. In de kolom **Beschrijving** kunt u desgewenst een beschrijving voor elk e-mail adres toevoegen.
1. Voeg in de kolom **type** **MixedAadMsaId** toe aan elke rij met een e-mail adres.
1. Sla het bestand op als een. CSV-bestand.
1. Selecteer op de pagina **voor beeld van doel groep** de koppeling **doel groep importeren (CSV)** .
1. Selecteer **Ja** in het **bevestigings** venster.
1. Selecteer de. CSV-bestand en selecteer **openen**.
1. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad: **technische configuratie**.

## <a name="next-steps"></a>Volgende stappen

- [Technische gegevens voor uw SaaS-aanbieding toevoegen](create-new-saas-offer-technical.md)
