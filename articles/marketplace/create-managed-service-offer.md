---
title: Een beheerde service aanbieding maken in micro soft Commercial Marketplace
description: Meer informatie over het maken van een nieuwe beheerde service aanbieding voor Azure Marketplace met het Commercial Marketplace-programma in micro soft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97918272"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>Een beheerde service aanbieding voor de commerciële Marketplace maken

In dit artikel wordt uitgelegd hoe u een beheerde service aanbieding voor de micro soft Commercial Marketplace maakt met behulp van partner Center.

Als u een beheerd service aanbod wilt publiceren, moet u een goud of zilver micro soft-competentie hebben behaald in het Cloud platform. Als u dit nog niet hebt gedaan, kunt u [een beheerde service aanbieding voor de commerciële Marketplace plannen](./plan-managed-service-offer.md). Het helpt u bij het voorbereiden van de activa die u nodig hebt bij het maken van de aanbieding in Partner Center.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2. Selecteer in het navigatie menu de optie **commerciële Marketplace**-  >  **overzicht**.
3. Selecteer op het tabblad Overzicht de optie **+ nieuwe**  >  **beheerde service** voor aanbiedingen.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="Illustreert het navigatie menu.":::

4. Voer in het dialoog venster **nieuwe aanbieding** een **aanbiedings-id** in. Dit is een unieke id voor elke aanbieding in uw account. Deze ID is zichtbaar in de URL van de aanbieding voor commerciële Marketplace en Azure Resource Manager sjablonen, indien van toepassing. Als u bijvoorbeeld test-aanbieding-1 in dit vak invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Elke aanbieding in uw account moet een unieke aanbiedings-ID hebben.
    * Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50.
    * De aanbiedings-ID kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

5. Voer een **alias** voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center. Het is niet zichtbaar in de online winkels en wijkt af van de aanbiedings naam die voor klanten wordt weer gegeven.
6. Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="configure-lead-management"></a>Leadbeheer configureren

Verbind uw Customer Relationship Management-systeem (CRM) met uw commerciële Marketplace-aanbieding zodat u contact gegevens van klanten kunt ontvangen wanneer een klant geïnteresseerd is in uw consulting service. U kunt deze verbinding wijzigen op elk gewenst moment tijdens of na het maken van de aanbieding. Zie [leads van klanten van uw aanbieding voor commerciële Marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md)voor gedetailleerde richt lijnen.

Het beheer van leads configureren in Partner Center:

1. Ga in partner centrum naar het tabblad **installatie** van de aanbieding.
2. Selecteer onder **leads van klanten** de koppeling **verbinding maken** .
3. Selecteer in het dialoog venster **verbindings Details** een doel voor de lead in de lijst.
4. Vul de velden in die worden weer gegeven. Raadpleeg de volgende artikelen voor gedetailleerde stappen:

    * [Uw aanbieding configureren voor het verzenden van leads naar de Azure-tabel](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Uw aanbieding configureren voor het verzenden van leads naar Dynamics 365 Customer engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (voorheen Dynamics CRM Online)
    * [Uw aanbieding configureren voor het verzenden van leads naar een HTTPS-eind punt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Uw aanbieding configureren voor het verzenden van leads naar Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Uw aanbieding configureren voor het verzenden van leads naar Sales Force](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. Als u de configuratie die u hebt ingevoerd, wilt valideren, selecteert u de **koppeling valideren**.
6. Wanneer u de verbindings gegevens hebt geconfigureerd, selecteert u **verbinding maken**.
7. Selecteer **Concept opslaan**.

Nadat u uw aanbieding voor publicatie hebt verzonden in het partner centrum, zullen we de verbinding valideren en u een test lead sturen. Wanneer u een voor beeld van de aanbieding bekijkt voordat deze live gaat, test u uw lead verbinding door de aanbieding zelf te kopen in de preview-omgeving.

> [!TIP]
> Zorg ervoor dat de verbinding met de doel locatie van de lead bijgewerkt blijft, zodat u geen leads kwijtraakt.

## <a name="configure-offer-properties"></a>Eigenschappen van aanbieding configureren

Op de pagina eigenschappen van uw aanbieding in partner centrum definieert u de categorieën die van toepassing zijn op uw aanbieding en juridische contracten. Deze informatie zorgt ervoor dat uw beheerde service correct wordt weer gegeven in de online winkel en aan de juiste set klanten wordt aangeboden.

### <a name="select-a-category"></a>Een categorie selecteren

Selecteer onder **Categorieën** ten minste één en Maxi maal vijf categorieën voor het groeperen van uw aanbieding in de juiste Zoek gebieden voor commerciële markt plaatsen.

### <a name="provide-terms-and-conditions"></a>Voor waarden opgeven

Geef onder **Legal** uw voor waarden voor deze aanbieding op. Klanten moeten ze accepteren voordat ze de aanbieding kunnen gebruiken. U kunt ook de URL opgeven waar uw voor waarden kunnen worden gevonden.

Selecteer **concept opslaan** voordat u doorgaat.

## <a name="next-step"></a>Volgende stap

* [De aanbieding voor een beheerd service-aanbod configureren](./create-managed-service-offer-listing.md)