---
title: Eigenschappen van de aanbieding voor virtuele machines configureren op Azure Marketplace
description: Meer informatie over het configureren van aanbiedingseigenschappen voor virtuele machines op Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 5942368ba1709127b815a35676b716955c1bee8f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819084"
---
# <a name="configure-virtual-machine-offer-properties"></a>Eigenschappen van de aanbieding voor virtuele machines configureren

Op **de** pagina Eigenschappen (selecteer in het menu aan de linkerkant) definieert u de categorieën die worden gebruikt voor het groeperen van uw aanbieding voor virtuele machines (VM's) op Azure Marketplace, de versie van uw toepassing en de juridische contracten die uw aanbieding ondersteunen.

## <a name="select-a-category"></a>Een categorie selecteren

Selecteer categorieën en subcategorieën om uw aanbieding in de juiste Azure Marketplace plaatsen. Zorg ervoor dat u later in de beschrijving van de aanbieding beschrijft hoe uw aanbieding ondersteuning biedt voor deze categorieën.

- Selecteer een primaire categorie.
- Als u een tweede optionele categorie (Secundair) wilt toevoegen, selecteert u de **koppeling +Categorieën.**
- Selecteer maximaal twee subcategorieën voor de categorie Primair en/of Secundair. Als er geen subcategorie van toepassing is op uw aanbieding, selecteert **u Niet van toepassing.** Gebruik Ctrl+klikken om een tweede subcategorie te selecteren.

Zie de volledige lijst met categorieën en subcategorieën in [Best practices voor aanbiedingsvermeldingen.](gtm-offer-listing-best-practices.md) Aanbiedingen voor virtuele machines worden altijd weergegeven onder de **categorie Compute** op Azure Marketplace.

## <a name="provide-terms-and-conditions"></a>Voorwaarden verstrekken

Geef **onder Juridisch** de voorwaarden voor uw aanbieding op. U hebt hiervoor twee opties:

- [Het standaardcontract gebruiken met optionele wijzigingen](#use-the-standard-contract)
- [Uw eigen voorwaarden gebruiken](#use-your-own-terms-and-conditions)

Zie voor meer informatie over het standaardcontract en [optionele wijzigingen Standaardcontract voor de commerciële marketplace van Microsoft.](standard-contract.md) U kunt de [Standaardcontract](https://go.microsoft.com/fwlink/?linkid=2041178) PDF downloaden (zorg ervoor dat de pop-upblokkering is uitgeschakeld).

### <a name="use-the-standard-contract"></a>Het standaardcontract gebruiken

Om het inkoopproces voor klanten te vereenvoudigen en de juridische complexiteit voor softwareleveranciers te verminderen, biedt Microsoft een standaardcontract dat u kunt gebruiken voor uw aanbiedingen in de commerciële marketplace. Wanneer u uw software onder het standaardcontract aanbiedt, hoeven klanten deze slechts één keer te lezen en te accepteren en hoeft u geen aangepaste voorwaarden te maken.

1. Schakel het **selectievakje Use the Standaardcontract for Microsoft's commercial marketplace** in.

   ![Illustreert het selectievakje Use the Standaardcontract for Microsoft's commercial marketplace (De Standaardcontract gebruiken voor de commerciële marketplace van Microsoft).](partner-center-portal/media/use-standard-contract.png)

1. Selecteer **accepteren** in het dialoogvenster **Bevestiging.** Afhankelijk van de grootte van het scherm moet u mogelijk omhoog schuiven om het te zien.
1. Selecteer **Concept opslaan voordat** u doorgaat.

   > [!NOTE]
   > Nadat u een aanbieding hebt gepubliceerd met Standaardcontract voor de commerciële marketplace, kunt u uw eigen aangepaste voorwaarden niet gebruiken. Bied uw oplossing aan onder het standaardcontract met optionele wijzigingen of onder uw eigen voorwaarden.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Wijzigingen toevoegen aan het standaardcontract (optioneel)

Er zijn twee soorten wijzigingen beschikbaar: *universeel* en *aangepast.*

##### <a name="add-universal-amendment-terms"></a>Universele wijzigingsvoorwaarden toevoegen

Voer in **het vak Universele wijzigingsvoorwaarden voor** het standaardcontract voor de commerciële marketplace van Microsoft uw universele wijzigingsvoorwaarden in. U kunt een onbeperkt aantal tekens invoeren in dit vak. Deze voorwaarden worden weergegeven voor klanten in AppSource, Azure Marketplace en/of Azure Portal tijdens de detectie- en aankoopstroom.

##### <a name="add-one-or-more-custom-amendments"></a>Een of meer aangepaste wijzigingen toevoegen

1. Selecteer **onder Aangepaste wijzigingsvoorwaarden in de Standaardcontract** voor de commerciële marketplace van Microsoft de koppeling Aangepaste wijzigingstermijn toevoegen **(max. 10).**
2. Voer uw **aangepaste aangepaste voorwaarden** in het vak in.
3. Voer de **tenant-id** in het vak in. Alleen klanten die zijn gekoppeld aan de tenant-ID's die u voor deze aangepaste voorwaarden opgeeft, zien deze in de aankoopstroom van de aanbieding in de Azure Portal.

   > [!TIP]
   > Een tenant-id identificeert uw klant in Azure. U kunt uw klant om deze id vragen en deze vinden door naar eigenschappen [**https://portal.azure.com**](https://portal.azure.com)  >  **Azure Active Directory**  >  **gaan.** De waarde van de map-id is de tenant-id (bijvoorbeeld `50c464d3-4930-494c-963c-1e951d15360e` ). U kunt ook de tenant-id van de organisatie van uw klant op zoeken met behulp van hun domeinnaam-URL in Wat is mijn Microsoft Azure en [Office 365-tenant-id?](https://www.whatismytenantid.com/).

4. Voer desgewenst een beschrijvende beschrijving **in voor** de tenant-id. Deze beschrijving helpt u bij het identificeren van de klant op wie u de wijziging wilt richten.
5. Als u nog een tenant-id wilt toevoegen, selecteert u de koppeling Tenant-id van een klant **toevoegen (max. 10)** en herhaalt u stap 3 en 4. U kunt maximaal 20 tenant-ID's toevoegen.
6. Herhaal stap 1 tot en met 5 om nog een wijzigingstermijn toe te voegen. U kunt maximaal tien aangepaste wijzigingsvoorwaarden per aanbieding opgeven.
7. Selecteer **Concept opslaan voordat** u doorgaat.

### <a name="use-your-own-terms-and-conditions"></a>Uw eigen voorwaarden gebruiken

U kunt uw eigen voorwaarden verstrekken in plaats van het standaardcontract te gebruiken. Klanten moeten deze voorwaarden accepteren voordat ze uw aanbieding kunnen proberen.

1. Onder **Juridisch** wordt het selectievakje **Use the Standaardcontract for Microsoft's commercial marketplace** uit.
1. Voer in **het vak Voorwaarden** maximaal 10.000 tekens tekst in.

   > [!NOTE]
   > Als u een langere beschrijving nodig hebt, voert u één webadres in dat wijst naar de plaats waar uw voorwaarden kunnen worden gevonden. Deze wordt aan klanten weergegeven als een actieve koppeling.

1. Selecteer **Concept opslaan** voordat u verdergaat met het volgende tabblad in het menu aan de linkerkant, **Aanbiedingsvermelding.**

## <a name="next-steps"></a>Volgende stappen

- [Vermelding voor een VM-aanbieding configureren](azure-vm-create-listing.md)
