---
title: Ondersteuning voor het programma voor commerciële Marketplace in het partner centrum
description: Meer informatie over de ondersteunings opties voor het programma voor commerciële Marketplace in Partner Center, inclusief het indienen van een ondersteunings aanvraag.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: a1726b29c153bf680d29fe821ac34aa958064335
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879238"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>Ondersteuning voor het Commercial Marketplace-programma in Partner Center

Micro soft biedt ondersteuning voor een groot aantal producten en services. Het vinden van het juiste ondersteunings team is belang rijk om te zorgen voor een passend en tijdig antwoord. Houd rekening met de volgende scenario's, waarmee u uw query naar het juiste team kunt routeren:

- Als u een uitgever bent en een vraag hebt van een klant, vraagt u uw klant om ondersteuning te vragen met behulp van de ondersteunings koppelingen in de [Azure Portal](https://portal.azure.com/).
- Als u een uitgever bent en een beveiligings probleem hebt gedetecteerd met een toepassing die wordt uitgevoerd op Azure, raadpleegt u [een ondersteunings ticket voor beveiligings gebeurtenissen registreren](../security/fundamentals/event-support-ticket.md). Uitgevers moeten verdachte beveiligings gebeurtenissen rapporteren, met inbegrip van beveiligings incidenten en beveiligings problemen van hun Azure Marketplace-software en service aanbiedingen, op de eerste mogelijkheid.
- Als u een uitgever bent en een vraag hebt met betrekking tot uw app of service, raadpleegt u de volgende ondersteunings opties.

## <a name="get-help-or-open-a-support-ticket"></a>Hulp krijgen of een ondersteunings ticket openen

1. Meld u aan met uw werk account. Als u dit nog niet hebt gedaan, moet u [een partner centrum-account maken](partner-center-portal/create-account.md).

1. Selecteer in het menu in de rechter bovenhoek van de pagina het **ondersteunings** pictogram. Het deel venster **Help en ondersteuning** wordt weer gegeven aan de rechter kant van de pagina.

1. Voor hulp bij de commerciële Marketplace selecteert u **commerciële Marketplace**.

   ![Vervolg keuzemenu ondersteuning](./media/support/commercial-marketplace-support-pane.png)

1. Voer in het vak **samen vatting van probleem** een korte beschrijving van het probleem in.

1. Voer in het vak **probleem type** een van de volgende handelingen uit:

    - **Optie 1**: Voer tref woorden in zoals: Marketplace, Azure-app, Saas-aanbieding, account beheer, Lead beheer, implementatie probleem, uitbetaling of co-sell-aanbod migratie. Selecteer vervolgens een probleem type in de aanbevolen lijst die wordt weer gegeven.

    - **Optie 2**: Selecteer **Bladeren in onderwerpen** in de **categorie** lijst en selecteer vervolgens **commerciële Marketplace**. Selecteer vervolgens het juiste **onderwerp** en **subonderwerp**.

1. Nadat u het gewenste onderwerp hebt gevonden, selecteert u **oplossingen controleren**.

    ![Volgende stap](./media/support/next-step.png)

De volgende opties worden weer gegeven:

- Als u een ander onderwerp wilt selecteren, klikt u op **een ander probleem selecteren**.
- Bekijk de aanbevolen stappen en documenten, indien beschikbaar, om het probleem op te lossen.

    ![Aanbevolen oplossingen](./media/support/recommended-solutions.png)

Als u uw antwoord niet kunt vinden in de zelf-Help, selecteert u **Details van probleem opgeven**. Vul alle vereiste velden in om het oplossings proces te versnellen en selecteer vervolgens **verzenden**.

>[!Note]
>Als u zich niet hebt aangemeld bij het partner centrum, moet u zich mogelijk aanmelden voordat u een ticket kunt maken.

## <a name="track-your-existing-support-requests"></a>Uw bestaande ondersteunings aanvragen bijhouden

Als u uw open en gesloten tickets wilt bekijken, selecteert u in het navigatie menu de optie **commerciële Marketplace**-  >  **ondersteuning**.

## <a name="record-issue-details-with-a-har-file"></a>Details van het probleem vastleggen met een HAR-bestand

Als ondersteuning voor agents om het probleem op te lossen, kunt u een HTTP-Archief indeling (HAR) koppelen aan uw ondersteunings ticket. HAR-bestanden zijn logboeken van netwerk aanvragen in een webbrowser.

> [!WARNING]
> HAR-bestanden kunnen gevoelige gegevens over uw partner centrum-account opnemen.

### <a name="microsoft-edge-and-google-chrome"></a>Micro soft Edge en Google Chrome

Een HAR-bestand genereren met behulp van **micro soft Edge** of **Google Chrome**:

1. Ga naar de webpagina waar het probleem zich voordoet.
2. Selecteer in de rechter bovenhoek van het venster het pictogram met het weglatings teken en vervolgens **meer hulpprogram ma's** voor  >  **ontwikkel aars**. U kunt op F12 drukken als een snelkoppeling.
3. Selecteer in het deel venster ontwikkel hulpprogramma's het tabblad **netwerk** .
4. Selecteer **registreren netwerk logboek stoppen** en **wissen** om bestaande logboeken te verwijderen. Het record pictogram wordt grijs weer gegeven.

    ![Bestaande logboeken verwijderen in micro soft Edge of Google Chrome](media/support/chromium-stop-clear-session.png)

5. Selecteer **registreren netwerk logboek** om te beginnen met de opname. Wanneer u de opname start, wordt het pictogram van de record rood weer.

    ![Het starten van de opname in micro soft Edge of Google Chrome](media/support/chromium-start-session.png)

6. Reproduceer het probleem dat u wilt oplossen.
7. Nadat u het probleem hebt gereproduceerd, selecteert u **opname netwerk logboek stoppen**.
8. Selecteer **exporteren har**, gemarkeerd met een pijl-omlaag en sla het bestand op.

    ![Een HAR-bestand exporteren in micro soft Edge of Google Chrome](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

Een HAR-bestand genereren met behulp van **Mozilla Firefox**:

1. Ga naar de webpagina waar het probleem zich voordoet.
1. Selecteer in de rechter bovenhoek van het venster het pictogram met het weglatings teken en schakel vervolgens de **webontwikkelaar**  >  **in**. U kunt op F12 drukken als een snelkoppeling.
1. Selecteer het tabblad **netwerk** en selecteer vervolgens **wissen** om de bestaande logboeken te verwijderen.

    ![Bestaande logboeken verwijderen in Mozilla Firefox](media/support/firefox-clear-session.png)

1. Reproduceer het probleem dat u wilt oplossen.
1. Nadat u het probleem hebt gereproduceerd, selecteert u **har exporteren/importeren**  >  **Alles opslaan als har**.

    ![Een HAR-bestand exporteren in Mozilla Firefox](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

Een HAR-bestand genereren met behulp van **Safari**:

1. Schakel de ontwikkel hulpprogramma's in Safari in: Selecteer **Safari**  >  **voor keuren**. Ga naar het tabblad **Geavanceerd** en selecteer vervolgens het **menu ontwikkelen weer geven in de menu balk**.
1. Ga naar de webpagina waar het probleem zich voordoet.
1. Selecteer **ontwikkelen** en selecteer vervolgens **Web-Inspector weer geven**.
1. Selecteer het tabblad **netwerk** en selecteer vervolgens **netwerk items wissen** om bestaande logboeken te verwijderen.

    ![Bestaande logboeken verwijderen in Safari](media/support/safari-clear-session.png)

1. Reproduceer het probleem dat u wilt oplossen.
1. Nadat u het probleem hebt gereproduceerd, selecteert u **exporteren** en slaat u het bestand op.

    ![Een HAR-bestand exporteren in Safari](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](partner-center-portal/update-existing-offer.md)