---
title: Lead beheer in Sales Force-micro soft Commercial Marketplace
description: Meer informatie over het gebruik van Sales Force voor het configureren van leads voor Microsoft AppSource en Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 73caf848ab5c6f8e973469066ce4612a075a52f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489315"
---
# <a name="configure-lead-management-for-salesforce"></a>Beheer van leads voor Sales Force configureren

In dit artikel wordt beschreven hoe u uw Sales Force-systeem zo instelt dat verkoop leads van uw aanbiedingen worden verwerkt in Microsoft AppSource en Azure Marketplace.

> [!NOTE]
> Azure Marketplace biedt geen ondersteuning voor vooraf gevulde lijsten, zoals een lijst met waarden voor het veld **land** . Zorg ervoor dat er geen lijsten zijn ingesteld voordat u doorgaat. U kunt ook een [https-eind punt](./commercial-marketplace-lead-management-instructions-https.md) of een [Azure-tabel](./commercial-marketplace-lead-management-instructions-azure-table.md) configureren om leads te ontvangen.

## <a name="set-up-your-salesforce-system"></a>Uw Sales Force-systeem instellen

1. Meld u aan bij Sales Force.
1. Navigeer naar de instellingen van **webnaar-lead** . 
    
    Als u de Sales Force Lighting-ervaring gebruikt
    1. Selecteer **Setup** op de start pagina van Sales Force.

       ![Setup van Sales Force](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Ga op de pagina **Setup** naar **platform tools**  >  **functie instellingen**  >  **marketing**  >  **Web-to-lead**.

        ![Sales Force-webto-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Als u de klassieke Sales Force-ervaring gebruikt:

    1. Selecteer **Setup** op de start pagina van Sales Force.

       ![Klassieke instellingen voor Sales Force](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Selecteer op de pagina **Setup** de optie **Build**  >  **Customize**  >  **leads**  >  **Web-to-lead**.

        ![Sales Force-klassiek: Web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   De resterende stappen zijn hetzelfde voor beide Sales Force-ervaringen.

1. Selecteer op de pagina voor het instellen van de **Web-naar-lead** de knop voor het maken van een **Web-naar-lead-formulier** .
1. Selecteer op de **pagina Web-to-lead instellen** **de optie een formulier voor webtoegang maken**.

    ![Setup van Sales Force Web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. Zorg er bij het **maken van een web-naar-lead-formulier** voor dat de `Include reCAPTCHA in HTML` instelling is uitgeschakeld en selecteer **genereren**.

    ![Sales Force een formulier deel venster voor het maken van een web-naar-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Er wordt een aantal HTML-tekst weer gegeven. Zoek naar de tekst ' OID ' en kopieer de **waarde ' OID '** uit de HTML-tekst (alleen de tekst tussen aanhalings tekens) en sla deze op. Plak deze waarde in het veld **organisatie-id** op de portal voor publiceren.

    ![Sales Force maken van een web-naar-lead-formulier met de HTML OID-waarde](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Selecteer **voltooid**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Uw aanbieding configureren voor het verzenden van leads naar Sales Force

Wanneer u klaar bent om de informatie over het beheer van leads voor uw aanbieding te configureren in de portal voor publiceren, voert u de volgende stappen uit.

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).

1. Selecteer uw aanbieding en ga naar het tabblad **installatie** van de aanbieding.

1. Selecteer in het gedeelte **klant leads** de optie **verbinding maken**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Leads van klanten":::

1. Selecteer in het pop-upvenster **verbindings Details** de optie **Sales Force** voor de **doel bestemming** en plak de `oid` waarde vanuit het formulier web-naar-lead dat u in het veld **organisatie-id** hebt gemaakt.

    ![Pop-upvenster verbindings Details het vak e-mail adres van contact persoon valideren](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. Voer onder **contact-e-mail** de e-mail adressen in voor personen in uw bedrijf die e-mail meldingen moeten ontvangen wanneer er een nieuwe lead wordt ontvangen. U kunt meerdere e-mail berichten opgeven door deze te scheiden met een punt komma.

1. Selecteer **OK**.

Selecteer **valideren** om ervoor te zorgen dat u verbinding hebt gemaakt met een doel van de lead. Als dat lukt, hebt u een test lead in de doel locatie van de lead.

>[!NOTE]
>U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen.
