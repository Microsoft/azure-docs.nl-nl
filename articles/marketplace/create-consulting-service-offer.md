---
title: Een advies service-aanbieding maken in micro soft Commercial Marketplace
description: Meer informatie over het maken van een nieuwe consulting service-aanbieding voor Microsoft AppSource of Azure Marketplace met behulp van het commerciële Marketplace-programma in micro soft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92754377"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>Een advies service-aanbieding maken in de commerciële Marketplace

In dit artikel wordt uitgelegd hoe u met partner Center een advies service-aanbieding voor de micro soft Commercial Marketplace maakt. 

## <a name="before-you-begin"></a>Voordat u begint

Als u een advies service-aanbod wilt publiceren, moet u voldoen aan bepaalde geschiktheids vereisten om expertise in uw veld te demonstreren. Als u dit nog niet hebt gedaan, kunt u [een advies service-aanbieding plannen voor de commerciële Marketplace](./plan-consulting-service-offer.md). Hierin worden de vereisten voor een advies service beschreven en de activa die u nodig hebt bij het maken van de aanbieding met partner Center.

## <a name="create-a-new-consulting-service-offer"></a>Een nieuwe advies service-aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
2.  Selecteer in het navigatie menu de optie **commerciële Marketplace**-  >  **overzicht**.
3.  Klik op het tabblad Overzicht en selecteer **+ New offer**  >  **Consulting Service**.

    ![Illustreert het navigatie menu.](./media/new-offer-consulting-service.png)

4. Voer in het dialoog venster **nieuwe aanbieding** een **aanbiedings-id** in. Deze ID is zichtbaar in de URL van de aanbieding voor commerciële Marketplace. Als u bijvoorbeeld test-aanbieding-1 in dit vak invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Elke aanbieding in uw account moet een unieke aanbiedings-ID hebben.
    * Gebruik alleen kleine letters en cijfers. De aanbiedings-ID kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50.
    * De aanbiedings-ID kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

5. Voer een **alias** voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center. Het is niet zichtbaar in de online winkels en wijkt af van de aanbiedings naam die voor klanten wordt weer gegeven.
6. Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="configure-lead-management"></a>Leadbeheer configureren

Verbind uw Customer Relationship Management-systeem (CRM) met uw commerciële Marketplace-aanbieding zodat u contact gegevens van klanten kunt ontvangen wanneer een klant geïnteresseerd is in uw consulting service. U kunt deze verbinding wijzigen op elk gewenst moment tijdens of na het maken van de aanbieding. Zie [leads van klanten van uw aanbieding voor commerciële Marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md)voor gedetailleerde richt lijnen.

Het beheer van leads configureren in Partner Center:

1.  Ga in partner centrum naar het tabblad **installatie** van de aanbieding.
2.  Selecteer onder **leads van klanten** de koppeling **verbinding maken** .
3.  Selecteer in het dialoog venster **verbindings Details** een doel voor de lead in de lijst.
4.  Vul de velden in die worden weer gegeven. Raadpleeg de volgende artikelen voor gedetailleerde stappen:

    * [Uw aanbieding configureren voor het verzenden van leads naar de Azure-tabel](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Uw aanbieding configureren voor het verzenden van leads naar Dynamics 365 Customer engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (voorheen Dynamics CRM Online)
    * [Uw aanbieding configureren voor het verzenden van leads naar een HTTPS-eind punt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Uw aanbieding configureren voor het verzenden van leads naar Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Uw aanbieding configureren voor het verzenden van leads naar Sales Force](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  Als u de configuratie die u hebt ingevoerd, wilt valideren, selecteert u de **koppeling valideren**.
6.  Wanneer u de verbindings gegevens hebt geconfigureerd, selecteert u **verbinding maken**.
7.  Selecteer **Concept opslaan**.

Nadat u uw aanbieding voor publicatie hebt verzonden in het partner centrum, zullen we de verbinding valideren en u een test lead sturen. Wanneer u een voor beeld van de aanbieding bekijkt voordat deze live gaat, test u uw lead verbinding door de aanbieding zelf te kopen in de preview-omgeving.

> [!TIP]
> Zorg ervoor dat de verbinding met de doel locatie van de lead bijgewerkt blijft, zodat u geen leads kwijtraakt.

## <a name="next-steps"></a>Volgende stappen

* [De eigenschappen van uw advies service-aanbieding configureren](./create-consulting-service-offer-properties.md)