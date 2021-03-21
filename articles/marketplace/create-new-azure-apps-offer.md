---
title: Een Azure-toepassings aanbieding maken in de commerciële Marketplace
description: Meer informatie over het maken van een nieuwe Azure-toepassings aanbieding voor het aanbieden of verkopen van Azure Marketplace, of via het programma Cloud Solution Provider (CSP) met behulp van de portal voor commerciële Marketplace in het micro soft partner centrum.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370075"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>Een Azure-toepassings aanbieding maken in de commerciële Marketplace

Als commerciële Marketplace-Uitgever kunt u een Azure-toepassings aanbieding maken zodat potentiële klanten uw oplossing kunnen kopen. In dit artikel wordt het proces beschreven voor het maken van een Azure-toepassings aanbieding voor de micro soft Commercial Marketplace.

Als u dit nog niet hebt gedaan, kunt u [een Azure-toepassings aanbieding plannen voor de commerciële Marketplace](plan-azure-application-offer.md). Het biedt de resources en helpt u bij het verzamelen van de gegevens en activa die u nodig hebt bij het maken van uw aanbieding.

## <a name="create-a-new-offer"></a>Een nieuwe aanbieding maken

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecteer in het navigatie menu de optie **commerciële Marketplace**-  >  **overzicht**.

1. Selecteer op de pagina overzicht **+ nieuwe aanbieding**  >  **Azure-toepassing**.

    ![Illustreert het navigatie menu.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. Voer in het dialoog venster **nieuwe aanbieding** een **aanbiedings-id** in. Dit is een unieke id voor elke aanbieding in uw account. Deze ID is zichtbaar in de URL van de aanbieding voor commerciële Marketplace en Azure Resource Manager sjablonen, indien van toepassing. Als u bijvoorbeeld test-aanbieding-1 in dit vak invoert, is het webadres van de aanbieding `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

     * Elke aanbieding in uw account moet een unieke aanbiedings-ID hebben.
     * Gebruik alleen kleine letters en cijfers. Dit kan afbreek streepjes en onderstrepings tekens bevatten, maar mag niet langer zijn dan 50.
     * De aanbiedings-ID kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

1. Voer een **alias** voor de aanbieding in. Dit is de naam die wordt gebruikt voor de aanbieding in Partner Center.

     * Deze naam is alleen zichtbaar in partner centrum en verschilt van de naam van de aanbieding en andere waarden die aan klanten worden weer gegeven.
     * De aanbiedings alias kan niet worden gewijzigd nadat u **maken** hebt geselecteerd.

1. Selecteer  **maken** om de aanbieding te genereren en door te gaan.

## <a name="configure-your-azure-application-offer-setup-details"></a>Details van de installatie van uw Azure-toepassings aanbieding configureren

Op het tabblad installatie van de **aanbieding** , onder **installatie Details**, kiest u of u een test drive wilt configureren. U maakt ook verbinding met uw CRM-systeem (Customer Relationship Management) met uw commerciële Marketplace-aanbieding.

### <a name="enable-a-test-drive-optional"></a>Een test drive inschakelen (optioneel)

Een test drive is een fantastische manier om uw aanbieding aan potentiële klanten te laten presen teren door hen gedurende een vast aantal uur toegang te geven tot een vooraf geconfigureerde omgeving. Het bieden van een test drive resulteert in een verhoogde conversie frequentie en genereert uiterst gekwalificeerde leads. Zie [test station](plan-azure-application-offer.md#test-drive)voor meer informatie over test stations.

#### <a name="to-enable-a-test-drive"></a>Een test drive inschakelen

- Schakel onder **test station** het selectie vakje **een test drive inschakelen** in.

### <a name="customer-lead-management"></a>Beheer van leads van klanten

Verbind uw Customer Relationship Management-systeem (CRM) met uw commerciële Marketplace-aanbieding zodat u contact gegevens van klanten kunt ontvangen wanneer een klant rente uitbrengt of uw product implementeert.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>De verbindings Details configureren in het partner centrum

1. Selecteer onder **leads van klanten** de koppeling **verbinding maken** .
1. Selecteer in het dialoog venster **verbindings Details** een doel voor de lead in de lijst.
1. Vul de velden in die worden weer gegeven. Raadpleeg de volgende artikelen voor gedetailleerde stappen:

   - [Uw aanbieding configureren voor het verzenden van leads naar de Azure-tabel](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Uw aanbieding configureren voor het verzenden van leads naar Dynamics 365 Customer engagement](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (voorheen Dynamics CRM Online)
   - [Uw aanbieding configureren voor het verzenden van leads naar een HTTPS-eind punt](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Uw aanbieding configureren voor het verzenden van leads naar Marketo](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Uw aanbieding configureren voor het verzenden van leads naar Sales Force](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Als u de configuratie die u hebt ingevoerd, wilt valideren, selecteert u de koppeling **valideren** , indien van toepassing.
1. Selecteer **verbinding maken** om het dialoog venster te sluiten.
1. Selecteer **concept opslaan** voordat u doorgaat naar het volgende tabblad: eigenschappen.

> [!NOTE]
> Zorg ervoor dat de verbinding met de doel locatie up-to-date blijft, zodat er geen leads verloren gaan. Zorg ervoor dat u deze verbindingen bijwerkt wanneer er iets is gewijzigd.

## <a name="next-steps"></a>Volgende stappen

- [De eigenschappen van uw Azure-toepassing-aanbod configureren](create-new-azure-apps-offer-properties.md)
