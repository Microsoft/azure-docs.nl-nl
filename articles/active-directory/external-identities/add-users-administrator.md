---
title: B2B-samenwerkings gebruikers toevoegen in de Azure Portal-Azure AD
description: Laat zien hoe een beheerder gast gebruikers kan toevoegen aan hun directory vanuit een partner organisatie met behulp van Azure Active Directory (Azure AD) B2B-samen werking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2566b427777aec273df17863f06040de20649d97
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581844"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Azure Active Directory B2B-samenwerkings gebruikers toevoegen aan de Azure Portal

Als gebruiker aan wie een van de beperkte beheerders rollen is toegewezen, kunt u de Azure Portal gebruiken om B2B-samenwerkings gebruikers uit te nodigen. U kunt gast gebruikers uitnodigen voor de Directory, een groep of een toepassing. Nadat u een gebruiker via een van deze methoden hebt uitgenodigd, wordt het account van de uitgenodigde gebruiker toegevoegd aan Azure Active Directory (Azure AD) met het gebruikers type *gast*. De gast gebruiker moet vervolgens hun uitnodiging inwisselen om toegang te krijgen tot resources. Een uitnodiging van een gebruiker verloopt niet.

Nadat u een gast gebruiker aan de Directory hebt toegevoegd, kunt u de gast gebruiker een rechtstreekse koppeling sturen naar een gedeelde app, of de gast gebruiker kan klikken op de opname-URL in het e-mail bericht. Zie voor meer informatie over het inwisselings proces [uitnodiging voor B2B-samen werking](redemption-experience.md).

> [!IMPORTANT]
> Volg de stappen in de [procedure: de privacygegevens van uw organisatie toevoegen in azure Active Directory](../fundamentals/active-directory-properties-area.md) om de URL van de privacyverklaring van uw organisatie toe te voegen. Als onderdeel van de eerste keer dat een uitnodiging wordt inwisseld, moet een uitgenodigde gebruiker toestemming geven om door te gaan. 

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat de instellingen voor externe samen werking van uw organisatie zodanig zijn geconfigureerd dat u gasten kunt uitnodigen. Standaard kunnen alle gebruikers en beheerders gasten uitnodigen. Het externe samenwerkings beleid van uw organisatie kan echter worden geconfigureerd om te voor komen dat bepaalde soorten gebruikers of beheerders hun gasten uitnodigen. Als u wilt weten hoe u dit beleid kunt weer geven en instellen, raadpleegt u [externe samen werking van B2B inschakelen en beheren wie gasten kan uitnodigen](delegate-invitations.md).

## <a name="add-guest-users-to-the-directory"></a>Gast gebruikers toevoegen aan de Directory

Voer de volgende stappen uit om B2B-samenwerkings gebruikers toe te voegen aan de Directory:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een gebruiker aan wie een beperkte beheerdersrol of de rol van de gast-uitnodiging is toegewezen.
2. Zoek en selecteer **Azure Active Directory** op elke pagina.
3. Onder **Beheren**, selecteer **Gebruikers**.
4. Selecteer **Nieuwe gastgebruiker**.

   ![Laat zien waar een nieuwe gast gebruiker zich in de gebruikers interface bevindt](./media/add-users-administrator/new-guest-user-in-all-users.png) 
 
5. Selecteer op de pagina **Nieuwe gebruiker** de optie **Gebruiker uitnodigen** en voeg vervolgens de gegevens van de gastgebruiker toe. 

    > [!NOTE]
    > E-mail adressen van groepen worden niet ondersteund. Voer het e-mail adres voor een persoon in. Daarnaast kunnen gebruikers van e-mail providers een plus teken (+) en aanvullende tekst toevoegen aan hun e-mail adressen om te helpen bij zaken als het filteren van postvak in. Azure AD biedt momenteel echter geen ondersteuning voor plus symbolen in e-mail adressen. Als u de bezorgings problemen wilt voor komen, laat u het plus teken en eventuele tekens achter het symbool weg.

   - **Naam.** De voor- en achternaam van de gastgebruiker.
   - **E-mailadres (verplicht)** . Het e-mailadres van de gastgebruiker.
   - **Persoonlijk bericht (optioneel)** Neem een persoonlijk welkomstbericht op voor de gastgebruiker.
   - **Groepen**: U kunt de gastgebruiker aan een of meer bestaande groepen toevoegen, of u kunt dit later doen.
   - **Directory-rol**: Als u Azure AD-beheermachtigingen nodig hebt voor de gebruiker, kunt u ze aan een Azure AD-rol toevoegen. 

7. Selecteer **Uitnodigen** voor het automatisch verzenden van de uitnodiging voor de gastgebruiker. 
 
Nadat u de uitnodiging verzendt, wordt het gebruikersaccount automatisch toegevoegd aan de map als gast.


![B2B-gebruiker met gast gebruikers type](./media/add-users-administrator/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Gastgebruikers toevoegen aan een groep
Als u gebruikers van B2B-samen werking hand matig aan een groep wilt toevoegen, volgt u deze stappen:

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Zoek en selecteer **Azure Active Directory** op elke pagina.
3. Selecteer onder **beheren** de optie **groepen**.
4. Selecteer een groep (of klik op **nieuwe groep** om een nieuw item te maken). Het is een goed idee om in de groeps beschrijving te vermelden dat de groep B2B-gast gebruikers bevat.
5. Selecteer **Leden**. 
6. Voer een van de volgende handelingen uit:
   - Als de gast gebruiker al in de directory bestaat, zoekt u naar de B2B-gebruiker. Selecteer de gebruiker en klik vervolgens op **selecteren** om de gebruiker aan de groep toe te voegen.
   - Als de gast gebruiker zich nog niet in de map bevinden, kunt u deze in de groep uitnodigen door hun e-mail adres in het zoekvak te typen, een optioneel persoonlijk bericht te typen en vervolgens op **selecteren** te klikken. De uitnodiging gaat automatisch naar de uitgenodigde gebruiker.
     
     ![Knop uitnodigen toevoegen om gast leden toe te voegen](./media/add-users-administrator/GroupInvite.png)
   
U kunt ook dynamische groepen gebruiken met Azure AD B2B-samen werking. Zie [dynamische groepen en Azure Active Directory B2B Collaboration](use-dynamic-groups.md)(Engelstalig) voor meer informatie.

## <a name="add-guest-users-to-an-application"></a>Gastgebruikers toevoegen aan een toepassing

Voer de volgende stappen uit om B2B-samenwerkings gebruikers toe te voegen aan een toepassing:

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Zoek en selecteer **Azure Active Directory** op elke pagina.
3. Selecteer onder **beheren** de optie **bedrijfs toepassingen**  >  **alle toepassingen**.
4. Selecteer de toepassing waaraan u gast gebruikers wilt toevoegen.
5. Selecteer op het dash board van de toepassing **totale gebruikers** om het deel venster **gebruikers en groepen** te openen.

    ![Knop totaal aantal gebruikers om open gebruikers en groepen toe te voegen](./media/add-users-administrator/AppUsersAndGroups.png)

6. Selecteer **Gebruiker toevoegen**.
7. Onder **toewijzing toevoegen** selecteert u **gebruiker en groepen**.
8. Voer een van de volgende handelingen uit:
   - Als de gast gebruiker al in de directory bestaat, zoekt u naar de B2B-gebruiker. Selecteer de gebruiker, klik op **selecteren** en klik vervolgens op **toewijzen** om de gebruiker toe te voegen aan de app.
   - Als de gast gebruiker zich nog niet in de map bevinden, typt u het e-mail adres van de gebruiker onder **lid selecteren of een externe gebruiker uitnodigen**. Typ een optioneel persoonlijk bericht in het bericht venster. Klik in het bericht venster op **uitnodigen**.
           
       ![Scherm afbeelding waarin wordt uitgelegd waar het e-mail adres van de gebruiker moet worden toegevoegd, het gepersonaliseerde bericht en de knop voor de uitnodiging wordt gemarkeerd.](./media/add-users-administrator/AppInviteUsers.png)
   
      Klik op **selecteren** en klik vervolgens op **toewijzen** om de gebruiker toe te voegen aan de app. Er wordt automatisch een uitnodiging verzonden naar de uitgenodigde gebruiker.

9. De gast gebruiker wordt weer gegeven in de lijst **gebruikers en groepen** van de toepassing met de toegewezen rol van **standaard toegang**. Als u de rol wilt wijzigen, gaat u als volgt te werk:
   - Selecteer de gast gebruiker en selecteer vervolgens **bewerken**. 
   - Klik onder **toewijzing bewerken** op **rol selecteren** en selecteer de rol die u aan de geselecteerde gebruiker wilt toewijzen.
   - Klik op **Selecteren**.
   - Klik op **Toewijzen**.
 
## <a name="resend-invitations-to-guest-users"></a>Uitnodigingen voor gastgebruikers opnieuw verzenden

Als een gast gebruiker de uitnodiging nog niet heeft ingewisseld, kunt u de uitnodigings-e-mail opnieuw verzenden.

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com).
2. Zoek en selecteer **Azure Active Directory** op elke pagina.
3. Selecteer onder **Beheren** de optie **Gebruikers**.
5. Selecteer het gebruikersaccount.
6. Selecteer onder **beheren** de optie **profiel**.
7. Als de gebruiker de uitnodiging nog niet heeft geaccepteerd, wordt de **uitnodiging geaccepteerd** ingesteld op **Nee** in het gedeelte **identiteit** . Als u de uitnodiging opnieuw wilt verzenden, selecteert u **(beheren)**. Klik vervolgens op de pagina **uitnodigingen beheren** naast * * opnieuw uitnodigen? Selecteer **Ja** en selecteer **gereed**.

> [!NOTE]
> Als u een uitnodiging die de gebruiker oorspronkelijk naar een specifieke app heeft gericht, opnieuw verzendt, moet u weten dat de koppeling in de nieuwe uitnodiging in plaats daarvan de gebruiker in het toegangs paneel van het hoogste niveau heeft.
> Bovendien kunnen alleen gebruikers met de machtiging voor het uitnodigen van machtigingen uitnodigingen opnieuw verzenden.

## <a name="next-steps"></a>Volgende stappen

- Als u wilt weten hoe niet-Azure AD-beheerders B2B-gast gebruikers kunnen toevoegen, raadpleegt u [Hoe kunnen informatie werkers B2B-samenwerkings gebruikers toevoegen?](add-users-information-worker.md)
- Zie [de elementen van het e-mail bericht uitnodiging voor B2B-samen werking](invitation-email-elements.md)voor meer informatie over de uitnodigings-e-mail.
