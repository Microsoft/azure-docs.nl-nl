---
title: Een SaaS-aanbieding maken in micro soft Commercial Marketplace
description: Meer informatie over het maken van een nieuwe SaaS-aanbieding (Software as a Service) voor het aanbieden of verkopen van Microsoft AppSource, Azure Marketplace of via het programma Cloud Solution Provider (CSP) met behulp van de portal voor commerciële Marketplace in het micro soft partner centrum.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: f652858f22e26eb7167a0cc7a2cfb8ef4b600ace
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047297"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>Een SaaS-aanbieding maken in de commerciële Marketplace

Als commerciële Marketplace-Uitgever kunt u een SaaS-aanbieding (Software as a Service) maken zodat potentiële klanten uw technische oplossing op basis van SaaS kunnen kopen. In dit artikel wordt het proces beschreven voor het maken van een SaaS-aanbieding voor de micro soft Commercial Marketplace.

## <a name="before-you-begin"></a>Voordat u begint

Als u dit nog niet hebt gedaan, kunt u [een SaaS-aanbieding plannen voor de commerciële Marketplace](plan-saas-offer.md). Hierin worden de technische vereisten voor uw SaaS-app uitgelegd en worden de gegevens en assets beschreven die u nodig hebt bij het maken van uw aanbieding. Tenzij u van plan bent een eenvoudige vermelding (**contact opnemen met** uw aanbiedings optie) in de commerciële Marketplace te publiceren, moet uw SaaS-toepassing voldoen aan de technische vereisten rond verificatie.

> [!IMPORTANT]
> We raden u aan om een afzonderlijke ontwikkelings-en test-aanbieding (DEV) en een afzonderlijke productie-aanbod (PROD) te maken. In dit artikel wordt beschreven hoe u een productie-aanbod maakt. Zie [een ontwikkel-en test aanbieding maken](create-saas-dev-test-offer.md)voor meer informatie over het maken van een ontwikkel aanbod.

## <a name="create-a-new-saas-offer"></a>Een nieuwe SaaS-aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
1. Selecteer in het navigatie menu de optie **commerciële Marketplace**-  >  **overzicht**.
1. Selecteer op het tabblad **overzicht** **+ nieuwe aanbieding**  >  **software als een service**.

   :::image type="content" source="media/new-offer-saas.png" alt-text="Illustreert het navigatie menu en de nieuwe lijst met aanbiedingen.":::

1. Voer in het dialoog venster **nieuwe aanbieding** een **aanbiedings-id** in. Deze ID is zichtbaar in de URL van de aanbieding voor commerciële Marketplace en Azure Resource Manager sjablonen, indien van toepassing. Als u bijvoorbeeld **test-aanbieding-1** in dit vak invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
   + Elke aanbieding in uw account moet een unieke aanbiedings-ID hebben.
   + Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50.
   + De aanbiedings-ID kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

1. Voer een **alias** voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

   + Deze naam is niet zichtbaar in de commerciële Marketplace en verschilt van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.
   + De aanbiedings alias kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.
1. Selecteer **maken** om de aanbieding te genereren en door te gaan.

## <a name="configure-your-saas-offer-setup-details"></a>Details van de installatie van SaaS-aanbiedingen configureren

Op het tabblad installatie van de **aanbieding** , onder **installatie Details**, kiest u of u uw aanbieding via micro soft wilt verkopen of uw trans acties afzonderlijk wilt beheren. Aanbiedingen die worden verkocht via micro soft worden aangeduid als behandel bare _aanbiedingen_, wat betekent dat micro soft de uitwisseling van geld voor een software licentie voor de uitgever vereenvoudigt. Zie [opties weer](plan-saas-offer.md#listing-options) geven en [de publicatie optie bepalen](determine-your-listing-type.md)voor meer informatie over deze opties.

1. Als u door micro soft wilt verkopen en ons trans acties voor u wilt laten ondersteunen, selecteert u **Ja**. Ga door om [een test drive in te scha kelen](#enable-a-test-drive-optional).

1. Als u uw aanbieding via de commerciële Marketplace en de trans acties afzonderlijk wilt aanbieden, selecteert u **Nee** en gaat u vervolgens op een van de volgende manieren te werk:
   + Als u een gratis abonnement voor uw aanbieding wilt opgeven, selecteert u **nu downloaden (gratis)**. Voer vervolgens in het vak **aanbiedings-URL** die wordt weer gegeven, de URL in (te beginnen met *http* of *https*) waar klanten een proef versie met één klik kunnen krijgen met [behulp van Azure Active Directory (Azure AD)](azure-ad-saas.md). Bijvoorbeeld `https://contoso.com/saas-app`.
   + Als u een gratis proef versie van 30 dagen wilt, selecteert u **gratis proef versie** en voert u in het vak **proef-URL** die wordt weer gegeven, de URL in (te beginnen met *http* of *https*), waar klanten toegang hebben tot uw gratis proef versie met [beAzure Active Directory hulp van een verificatie met één klik (Azure AD)](azure-ad-saas.md). Bijvoorbeeld `https://contoso.com/trial/saas-app`.
   + Als u wilt dat potentiële klanten contact met u opnemen om uw aanbieding te kopen, selecteert u **contact met mij opnemen**.

### <a name="enable-a-test-drive-optional"></a>Een test drive inschakelen (optioneel)

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen gedurende een vast aantal uur toegang te geven tot een vooraf geconfigureerde omgeving. Het bieden van een test drive resulteert in een verhoogde conversie frequentie en genereert uiterst gekwalificeerde leads. Zie [Wat is een test drive?](./what-is-test-drive.md)voor meer informatie over test stations.

> [!TIP]
> Een test drive wijkt af van een gratis proef versie. U kunt een test drive, een gratis proef versie of beide aanbieden. Ze bieden klanten uw oplossing voor een vaste periode. Een test drive bevat echter ook een zelf doorgeleide rond leiding door de belangrijkste functies en voor delen van uw product die worden getoond in een scenario met een praktijk implementatie.

**Een test drive inschakelen**
1.  Schakel onder **test station** het selectie vakje **een test drive inschakelen** in.
1.  Selecteer in de lijst die wordt weer gegeven het test drive type.

### <a name="configure-lead-management"></a>Leadbeheer configureren

Verbind uw Customer Relationship Management-systeem (CRM) met uw commerciële Marketplace-aanbieding zodat u contact gegevens van klanten kunt ontvangen wanneer een klant rente uitbrengt of uw product implementeert. U kunt deze verbinding wijzigen op elk gewenst moment tijdens of na het maken van de aanbieding.

> [!NOTE]
> U moet Lead beheer configureren als u uw aanbieding via micro soft wilt verkopen of als u de optie **contact opnemen met mij** hebt geselecteerd. Zie [leads van klanten van uw aanbieding voor commerciële Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md)voor gedetailleerde richt lijnen.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>De verbindings Details configureren in het partner centrum

1.  Selecteer onder **leads van klanten** de koppeling **verbinding maken** .
1. Selecteer in het dialoog venster **verbindings Details** een doel voor de lead in de lijst.
1. Vul de velden in die worden weer gegeven. Raadpleeg de volgende artikelen voor gedetailleerde stappen:

   - [Uw aanbieding configureren voor het verzenden van leads naar de Azure-tabel](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Uw aanbieding configureren voor het verzenden van leads naar Dynamics 365 Customer engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (voorheen Dynamics CRM Online)
   - [Uw aanbieding configureren voor het verzenden van leads naar een HTTPS-eind punt](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Uw aanbieding configureren voor het verzenden van leads naar Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Uw aanbieding configureren voor het verzenden van leads naar Sales Force](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Als u de configuratie die u hebt ingevoerd, wilt valideren, selecteert u de koppeling **valideren** .
1. Selecteer **OK** om het dialoog venster te sluiten.

## <a name="next-steps"></a>Volgende stappen

- [De eigenschappen van uw SaaS-aanbod configureren](create-new-saas-offer-properties.md)