---
title: De eigenschappen van uw SaaS-aanbieding configureren in micro soft Partner Center
description: Meer informatie over het configureren van de eigenschappen voor uw software as a Service (SaaS) micro soft Commercial Marketplace-aanbieding in Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 7d90c9b9b2c9aa97083e17d5ab7d20fc6b471658
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94492120"
---
# <a name="how-to-configure-your-saas-offer-properties"></a>De eigenschappen van uw SaaS-aanbod configureren

In dit artikel wordt beschreven hoe u de eigenschappen voor een SaaS-aanbieding (Software as a Service) in de micro soft Commercial Marketplace kunt configureren.

Op het tabblad **Eigenschappen** definieert u de categorieën en branches die van toepassing zijn op uw aanbieding, uw app-versie en juridische contracten. Zorg ervoor dat u volledige en nauw keurige Details opgeeft over uw aanbieding op deze pagina, zodat deze correct wordt weer gegeven en aan de juiste set klanten wordt aangeboden.

## <a name="select-a-category-for-your-offer"></a>Selecteer een categorie voor uw aanbieding

Selecteer onder **categorie** ten minste één en Maxi maal twee categorieën voor het groeperen van uw aanbieding in de juiste Zoek gebieden voor Marketplace. Op basis van de gekozen categorieën bepalen we welke online winkels uw aanbieding vermelden: Azure Marketplace, Microsoft AppSource of beide.

## <a name="select-industries-optional"></a>Branches selecteren (optioneel)

Onder **branches** kunt u Maxi maal twee branches en Maxi maal twee subbranches (ook wel verticalen genoemd) selecteren voor elke branche. Deze branches worden gebruikt om uw aanbieding weer te geven wanneer klanten hun zoek opdracht op branches en subsectoren filteren in de online winkel.

> [!NOTE]
> Als uw aanbieding niet specifiek is voor de branche, laat u deze sectie leeg.

1. Selecteer de koppeling **+ Industries** onder **branches**.
1. Selecteer een branche in de lijst met **sectoren** .
1. Selecteer ten minste één en Maxi maal twee verticale waarden in de lijst met **subsectors** . Gebruik de CTRL-toets om meerdere subbranches te selecteren.
1. Als u nog een branche en verticaal wilt toevoegen, selecteert u **+ branches** en herhaalt u stap 1 tot en met 3.

## <a name="specify-an-app-version-optional"></a>Een app-versie opgeven (optioneel)

 Voer in het vak **App-versie** een versie nummer in. De App-versie wordt gebruikt in de AppSource Marketplace om het versie nummer van uw aanbieding te identificeren.

## <a name="provide-terms-and-conditions"></a>Voor waarden opgeven

Geef onder **juridisch** recht voor waarden op voor uw aanbieding. U hebt hiervoor twee opties:

- [Het standaard contract gebruiken met optionele wijzigingen](#use-the-standard-contract)
- [Gebruik uw eigen voor waarden](#use-your-own-terms-and-conditions)

Zie voor meer informatie over het standaard contract en de optionele wijzigingen het [standaard contract voor de micro soft Commercial Marketplace](standard-contract.md). U kunt het [standaard](https://go.microsoft.com/fwlink/?linkid=2041178) -PDF-bestand downloaden (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

### <a name="use-the-standard-contract"></a>Het standaard contract gebruiken

Micro soft biedt een standaard contract dat u voor uw aanbiedingen kunt gebruiken in de commerciële Marketplace om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te reduceren. Wanneer u uw software onder het Standard-contract aanbiedt, hoeven klanten slechts één keer te lezen en te accepteren en hoeft u geen aangepaste voor waarden te maken.

1. Selecteer het selectie vakje **het standaard contract voor commerciële Marketplace van micro soft gebruiken** .

   ![Illustreert het selectie vakje het standaard contract voor commerciële Marketplace van micro soft gebruiken.](partner-center-portal/media/use-standard-contract.png)
1. In het **bevestigings** dialoogvenster selecteert u **accepteren**. Mogelijk moet u omhoog schuiven om het weer te geven.
1. Selecteer **concept opslaan** voordat u doorgaat.

   > [!NOTE]
   > Nadat u een aanbieding hebt gepubliceerd met het standaard contract voor de commerciële Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. U kunt uw oplossing aanbieden onder het Standard-contract met optionele wijzigingen of onder uw eigen voor waarden.

### <a name="add-amendments-to-the-standard-contract-optional"></a>Wijzigingen aan het standaard contract toevoegen (optioneel)

Er zijn twee soorten wijzigingen beschikbaar: *universeel* en *aangepast*.

#### <a name="add-universal-amendment-terms"></a>Universele wijzigings voorwaarden toevoegen

Voer in de voor **waarden voor universele wijzigingen in het standaard contract voor de commerciële Marketplace van micro soft** de voor waarden in voor Universal-wijzigingen. U kunt een onbeperkt aantal tekens in dit vak invoeren. Deze voor waarden worden weer gegeven aan klanten in AppSource, Azure Marketplace en/of Azure Portal tijdens de detectie-en aankoop stroom.

#### <a name="add-one-or-more-custom-amendments"></a>Een of meer aangepaste wijzigingen toevoegen

1. Onder aanpassingen **aan het standaard contract voor de commerciële Marketplace van micro soft**, selecteert u de koppeling **aangepaste wijzigings termijn toevoegen (max. 10)** .
1. Voer in het vak **aangepaste wijzigings voorwaarden** uw wijzigings voorwaarden in.
1. Voer in het vak **Tenant-id** een Tenant-id in. Alleen klanten die zijn gekoppeld aan de Tenant-Id's die u voor deze aangepaste voor waarden opgeeft, worden weer gegeven in de inkoop stroom van de aanbieding in de Azure Portal.
   > [!TIP]
   > Een Tenant-ID identificeert uw klant in Azure. U kunt uw klant vragen voor deze id en ze kunnen deze vinden door naar [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Eigenschappen** te gaan. De waarde van de Directory-ID is de Tenant-ID (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e` ). U kunt ook de Tenant-ID van de organisatie zoeken met behulp van de domein naam-URL op [Wat is mijn Microsoft Azure en Office 365-Tenant-id?](https://www.whatismytenantid.com/).
1. Voer desgewenst in het vak **Beschrijving** een beschrijvende beschrijving in voor de Tenant-id. Deze beschrijving helpt u bij het identificeren van de klant die u wilt richten op de wijziging.
1. Als u nog een Tenant-ID wilt toevoegen, selecteert u de koppeling **toevoegen van de Tenant-id van een klant** en herhaalt u stap 3 en 4. U kunt Maxi maal 20 Tenant-Id's toevoegen.
1. Herhaal de stappen 1 tot en met 5 om een andere wijzigings termijn toe te voegen. U kunt Maxi maal tien aangepaste voor waarden per aanbieding opgeven. 
2. Selecteer **concept opslaan** voordat u doorgaat.

### <a name="use-your-own-terms-and-conditions"></a>Gebruik uw eigen voor waarden

U kunt ervoor kiezen om uw eigen voor waarden op te geven in plaats van het standaard contract. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

1. Zorg ervoor dat het selectie vakje **het standaard contract voor commerciële Marketplace van micro soft gebruiken** is uitgeschakeld onder **juridisch**.
1. Voer in het vak voor **waarden** maxi maal 10.000 tekens tekst in.
1. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad **aanbieding**.

## <a name="next-steps"></a>Volgende stappen

- [Details van uw SaaS-aanbieding configureren](create-new-saas-offer-listing.md)
