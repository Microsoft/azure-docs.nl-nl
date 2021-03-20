---
title: De eigenschappen van uw Azure-toepassing-aanbod configureren
description: Meer informatie over het configureren van de eigenschappen voor uw Azure-toepassings aanbieding in Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 6f92c138a02c9dbdc1d22ca04c733cfbee69dcd0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94488516"
---
# <a name="how-to-configure-your-azure-application-offer-properties"></a>De eigenschappen van uw Azure-toepassing-aanbod configureren

In dit artikel wordt beschreven hoe u de eigenschappen voor een Azure-toepassing aanbieding in de commerciële Marketplace kunt configureren.

Op de pagina **Eigenschappen** definieert u de categorieën die van toepassing zijn op uw aanbieding en juridische contracten. Zorg ervoor dat u volledige en nauw keurige Details opgeeft over uw aanbieding op deze pagina, zodat deze correct wordt weer gegeven en aan de juiste set klanten wordt aangeboden.

## <a name="select-a-category-for-your-offer"></a>Selecteer een categorie voor uw aanbieding

Selecteer onder **Categorieën** de koppeling **Categorieën** en kies ten minste één en Maxi maal twee categorieën voor het groeperen van uw aanbieding in de juiste Zoek gebieden voor commerciële Marketplace. Selecteer Maxi maal twee subcategorieën voor elke primaire en secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert u **niet van toepassing**.

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

Er zijn twee soorten wijzigingen beschikbaar: _universeel_ en _aangepast_.

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
1. Selecteer **concept opslaan** voordat u doorgaat.

### <a name="use-your-own-terms-and-conditions"></a>Gebruik uw eigen voor waarden

U kunt ervoor kiezen om uw eigen voor waarden op te geven in plaats van het standaard contract. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

1. Zorg ervoor dat het selectie vakje **het standaard contract voor commerciële Marketplace van micro soft gebruiken** is uitgeschakeld onder **juridisch**.
1. Voer in het vak voor **waarden** maxi maal 10.000 tekens tekst in.
1. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad: aanbieding aanbieden.

## <a name="next-steps"></a>Volgende stappen

- [De details van uw Azure-toepassing-aanbieding configureren](create-new-azure-apps-offer-listing.md)
