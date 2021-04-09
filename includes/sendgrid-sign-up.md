---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 5bc00f4de95d22eec71f9b1b2504b00f506232dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99215456"
---
### <a name="to-sign-up-for-a-sendgrid-account"></a>Aanmelden voor een SendGrid-account
1. Meld u aan bij [Azure Portal][Azure portal].
2. Selecteer in het menu Azure Portal of de start pagina de optie **een resource maken**.

    ![Scherm afbeelding van het menu Azure Portal waarvoor de optie een resource maken is geselecteerd.][command-bar-new]
3. Zoek en selecteer **SendGrid**.

    ![Scherm afbeelding van het venster Azure Portal Marketplace met ' SendGr ' in het zoekvak en SendGrid geselecteerd in de zoek resultaten.][sendgrid-store]
4. Vul het registratieformulier in en selecteer **Maken**.

    ![Scherm afbeelding van het dialoog venster een nieuw SendGrid-account maken met de velden naam, wacht woord, abonnement en resource groep ingevuld.][sendgrid-create]
5. Voer een **naam** in om uw SendGrid-service in uw Azure-instellingen te herkennen. Namen moeten tussen de 1 en 100 tekens lang zijn en mogen alleen alfanumerieke tekens, streepjes, punten en onderstrepingstekens bevatten. De naam moet uniek zijn in uw lijst met geabonneerde Azure Store-items.
6. Voer uw **wachtwoord** in en bevestig dit.
7. Kies uw **abonnement**.
8. Maak een nieuwe **resourcegroep** of selecteer een bestaande.
9. Selecteer in het gedeelte **Prijscategorie** het SendGrid-abonnement waar u zich voor wilt aanmelden.

    ![Scherm afbeelding van het dialoog venster een nieuw SendGrid-account maken met de sectie uw prijs categorie kiezen openen][sendgrid-pricing]
10. Voer een **promotiecode** in als u er een hebt.
11. Voer uw **contact gegevens** in.
12. Lees en accepteer de **juridische bepalingen**.
13. Nadat u uw aankoop hebt bevestigd, ziet u een pop-upvenster **implementatie is voltooid** en ziet u uw account in de lijst.

    ![Scherm afbeelding van de pagina SendGrid-accounts met het nieuwe account dat wordt weer gegeven ContosoSendGrid.][all-resources]

    Nadat u uw aankoop hebt voltooid en op de knop **Beheren** hebt geklikt om de verificatieprocedure van het e-mailadres te starten, ontvangt u een e-mail van SendGrid waarin u wordt gevraagd uw account te verifiëren. Als u dit e-mail bericht niet ontvangt of problemen ondervindt met het verifiëren van uw account, raadpleegt u onze veelgestelde vragen.

    ![Scherm afbeelding van de pagina ContosoSendGrid-account met de knop beheren gemarkeerd.][manage]

    **U kunt maximaal 100 e-mailberichten per dag verzenden totdat u uw account hebt geverifieerd.**

    Als u uw abonnement wilt wijzigen of de SendGrid-contactinstellingen wilt bekijken, klikt u op de naam van uw SendGrid-service om het SendGrid Marketplace-dashboard te openen.

    ![Scherm opname die laat zien dat de instellingen pagina voor het ContosoSendGrid-account wordt geopend door alle instellingen op de pagina ContosoSendGrid-account te selecteren.][settings]

    Als u een e-mail met SendGrid wilt verzenden, moet u uw API-sleutel invoeren.

### <a name="to-find-your-sendgrid-api-key"></a>Uw SendGrid-API-sleutel vinden
1. Klik op **Beheren**.

    ![Scherm afbeelding van de pagina ContosoSendGrid-account met de knop beheren gemarkeerd.][manage]
2. Selecteer op uw SendGrid-dashboard **Instellingen** en vervolgens **API-sleutels** in het menu aan de linkerkant.

    ![Scherm opname van het SendGrid-dash board waarin de vervolg keuzelijst instellingen is geopend en de API-sleutels zijn geselecteerd.][api-keys]

3. Klik op **API-sleutel maken**.

    ![Scherm afbeelding van de API-sleutels en de knop API-sleutel maken is geselecteerd.][general-api-key]
4. Geef ten minste de **naam van de sleutel** op, verleen volledige toegang tot **Mail verzenden** en selecteer vervolgens **Opslaan**.

    ![Scherm opname van het scherm nieuwe algemene API-sleutel toevoegen met E-mail verzenden ingesteld op volledige toegang, geplande verzen dingen ingesteld op geen toegang en de knop Opslaan gemarkeerd.][access]
5. Uw API wordt op dit moment één keer weergegeven. Zorg ervoor dat u de API veilig opslaat.

### <a name="to-find-your-sendgrid-credentials"></a>Uw SendGrid-referenties vinden
1. Klik op het sleutelpictogram om uw **gebruikersnaam** te vinden.

    ![Scherm afbeelding van de pagina ContosoSendGrid-account met het sleutel pictogram gemarkeerd.][key]
2. Het wachtwoord is door u gekozen tijdens de installatie. Als u wijzigingen wilt aanbrengen, selecteert u **Wachtwoord wijzigen** of **Wachtwoord opnieuw instellen**.

Als u uw instellingen voor de bezorging van e-mail wilt beheren, klikt u op de knop **Beheren**. Dit wordt omgeleid naar uw SendGrid-dash board.

![Scherm afbeelding van de pagina ContosoSendGrid-account met de knop beheren gemarkeerd.][manage]

Ga voor meer informatie over het verzenden van e-mail via SendGrid naar het overzicht van de [e-mail-API][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
