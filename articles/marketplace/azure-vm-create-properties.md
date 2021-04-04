---
title: Eigenschappen van de aanbieding van virtuele machines op Azure Marketplace configureren
description: Meer informatie over het configureren van de eigenschappen voor de aanbieding van virtuele machines op Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 281553782774c31ec8cfaf614542fd739c4d3dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629526"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>Eigenschappen van de aanbieding van virtuele machines configureren

Op de pagina **Eigenschappen** (selecteren in het menu links in partner centrum) definieert u de categorieën die worden gebruikt voor het groeperen van de aanbieding van de virtuele machine (VM) op Azure Marketplace, uw toepassings versie en de juridische contracten die uw aanbieding ondersteunen.

## <a name="select-a-category"></a>Een categorie selecteren

Selecteer categorieën en subcategorieën om uw aanbieding te plaatsen in de juiste Azure Marketplace-Zoek gebieden. Zorg ervoor dat u later in de aanbieding beschrijving leest hoe uw aanbod deze categorieën ondersteunt.

- Selecteer een primaire categorie.
- Als u een tweede optionele categorie (secundair) wilt toevoegen, selecteert u de koppeling **+ Categorieën** .
- Selecteer Maxi maal twee subcategorieën voor de primaire en/of secundaire categorie. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert u **niet van toepassing**. Gebruik Ctrl + klikken om een tweede subcategorie te selecteren.

Bekijk de volledige lijst met categorieën en subcategorieën in [Aanbevolen procedures voor aanbiedingen](gtm-offer-listing-best-practices.md). Virtuele machine aanbiedingen worden altijd weer gegeven onder de **reken** categorie op Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Voor waarden opgeven

Geef onder **juridisch** recht voor waarden op voor uw aanbieding. U hebt hiervoor twee opties:

- [Het standaard contract gebruiken met optionele wijzigingen](#use-the-standard-contract)
- [Gebruik uw eigen voor waarden](#use-your-own-terms-and-conditions)

Zie voor meer informatie over het standaard contract en de optionele wijzigingen het [standaard contract voor de micro soft Commercial Marketplace](standard-contract.md). U kunt het [standaard](https://go.microsoft.com/fwlink/?linkid=2041178) -PDF-bestand downloaden (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

### <a name="use-the-standard-contract"></a>Het standaard contract gebruiken

Micro soft biedt een standaard contract dat u voor uw aanbiedingen kunt gebruiken in de commerciële Marketplace om het aankoop proces voor klanten te vereenvoudigen en de juridische complexiteit voor software leveranciers te reduceren. Wanneer u uw software onder het Standard-contract aanbiedt, hoeven klanten slechts één keer te lezen en te accepteren en hoeft u geen aangepaste voor waarden te maken.

1. Schakel het selectie vakje **het standaard contract voor commerciële Marketplace van micro soft gebruiken** in.

   ![Illustreert het selectie vakje het standaard contract voor commerciële Marketplace van micro soft gebruiken.](partner-center-portal/media/use-standard-contract.png)

1. In het **bevestigings** dialoogvenster selecteert u **accepteren**. Afhankelijk van de grootte van het scherm moet u mogelijk omhoog schuiven om het te zien.
1. Selecteer **concept opslaan** voordat u doorgaat.

   > [!NOTE]
   > Nadat u een aanbieding hebt gepubliceerd met het standaard contract voor de commerciële Marketplace, kunt u uw eigen aangepaste voor waarden niet gebruiken. U kunt uw oplossing aanbieden onder het Standard-contract met optionele wijzigingen of onder uw eigen voor waarden.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Wijzigingen aan het standaard contract toevoegen (optioneel)

Er zijn twee soorten wijzigingen beschikbaar: *universeel* en *aangepast*.

##### <a name="add-universal-amendment-terms"></a>Universele wijzigings voorwaarden toevoegen

Voer in de voor **waarden voor universele wijzigingen in het standaard contract voor de commerciële Marketplace van micro soft** de voor waarden in voor Universal-wijzigingen. U kunt een onbeperkt aantal tekens in dit vak invoeren. Deze voor waarden worden weer gegeven aan klanten in AppSource, Azure Marketplace en/of Azure Portal tijdens de detectie-en aankoop stroom.

##### <a name="add-one-or-more-custom-amendments"></a>Een of meer aangepaste wijzigingen toevoegen

1. Onder aanpassingen **aan het standaard contract voor de commerciële Marketplace van micro soft**, selecteert u de koppeling **aangepaste wijzigings termijn toevoegen (max. 10)** .
2. Voer uw **aangepaste wijzigings voorwaarden** in het vak in.
3. Voer de **Tenant-id** in het vak in. Alleen klanten die zijn gekoppeld aan de Tenant-Id's die u voor deze aangepaste voor waarden opgeeft, worden weer gegeven in de inkoop stroom van de aanbieding in de Azure Portal.

   > [!TIP]
   > Een Tenant-ID identificeert uw klant in Azure. U kunt uw klant vragen voor deze id en ze kunnen deze vinden door naar [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **Eigenschappen** te gaan. De waarde van de Directory-ID is de Tenant-ID (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e` ). U kunt ook de Tenant-ID van de organisatie zoeken met behulp van de domein naam-URL op [Wat is mijn Microsoft Azure en Office 365-Tenant-id?](https://www.whatismytenantid.com/).

4. Voer desgewenst een **Beschrijving** in voor de Tenant-id. Deze beschrijving helpt u bij het identificeren van de klant die u wilt richten op de wijziging.
5. Als u nog een Tenant-ID wilt toevoegen, selecteert u de koppeling **toevoegen van de Tenant-id van een klant (Maxi maal 10)** en herhaalt u stap 3 en 4. U kunt Maxi maal 20 Tenant-Id's toevoegen.
6. Herhaal de stappen 1 tot en met 5 om een andere wijzigings termijn toe te voegen. U kunt Maxi maal tien aangepaste voor waarden per aanbieding opgeven.
7. Selecteer **concept opslaan** voordat u doorgaat.

### <a name="use-your-own-terms-and-conditions"></a>Gebruik uw eigen voor waarden

U kunt ervoor kiezen om uw eigen voor waarden op te geven in plaats van het standaard contract. Klanten moeten deze voor waarden accepteren voordat ze uw aanbieding kunnen proberen.

1. Schakel onder **juridisch** het selectie vakje **gebruik het standaard contract voor commerciële Marketplace van micro soft** uit.
1. Voer in het vak voor **waarden** maxi maal 10.000 tekens tekst in.

   > [!NOTE]
   > Als u een langere beschrijving nodig hebt, voert u één webadres in dat verwijst naar waar uw voor waarden kunnen worden gevonden. Het wordt weer gegeven voor klanten als een actieve koppeling.

1. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad in het menu links Navigator, **aanbieding aanbieden**.

## <a name="next-steps"></a>Volgende stappen

- [Vermelding voor een VM-aanbieding configureren](azure-vm-create-listing.md)
