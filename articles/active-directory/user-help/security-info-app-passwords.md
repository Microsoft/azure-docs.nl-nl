---
title: App-wacht woorden maken op basis van de pagina met beveiligings gegevens (preview)-Azure AD
description: Maak automatisch gegenereerde wacht woorden (app-wacht woorden) voor gebruik met een niet-browser-app of een app die geen ondersteuning biedt voor twee ledige verificatie in uw organisatie. Dit app-wacht woord is gescheiden van een normaal wacht woord en kan worden ingesteld op de pagina met beveiligings gegevens.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 84588130788e9be8d3be52a8ea0f3988dce7b952
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91537001"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>App-wachtwoorden maken op de pagina Beveiligingsgegevens (preview)

Bepaalde apps, zoals Outlook 2010, bieden geen ondersteuning voor verificatie in twee stappen. Dit gebrek aan ondersteuning betekent dat als u in uw organisatie verificatie in twee stappen gebruikt, de app niet werkt. Om dit probleem op te lossen, kunt u een automatisch gegenereerd wacht woord maken voor gebruik met elke niet-browser-app, gescheiden van uw normale wacht woord.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Uw beheerder staat mogelijk niet toe dat u app-wacht woorden gebruikt. Als u app- **wacht woorden** niet ziet als een optie, zijn ze niet beschikbaar in uw organisatie.

Wanneer u app-wachtwoorden gebruikt, is het belangrijk om het volgende te onthouden:

- App-wacht woorden worden automatisch gegenereerd en moeten eenmaal per app worden gemaakt en ingevoerd.

- Er is een limiet van 40 wachtwoorden per gebruiker. Als u meer wachtwoorden wilt maken dan dat, wordt u gevraagd een bestaand wachtwoord te verwijderen voordat u het nieuwe wachtwoord kunt maken.

    >[!Note]
    >Office 2013-clients (inclusief Outlook) ondersteunen nieuwe verificatie protocollen en kunnen worden gebruikt met verificatie in twee stappen. Deze ondersteuning betekent dat als u verificatie in twee stappen hebt ingeschakeld, u geen app-wacht woorden meer nodig hebt voor Office 2013-clients. Zie voor meer informatie het artikel [hoe moderne verificatie werkt voor office 2013-en office 2016-client-apps](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) .

## <a name="create-new-app-passwords"></a>Nieuwe app-wacht woorden maken

Als u verificatie in twee stappen gebruikt met uw werk-of school account en de beheerder de beveiligings informatie heeft ingeschakeld, kunt u uw app-wacht woorden maken en verwijderen via de pagina met **beveiligings gegevens** .

>[!Note]
>Als uw beheerder de ervaring met beveiligings informatie niet heeft ingeschakeld, moet u de instructies en informatie volgen in de sectie [app-wacht woorden beheren voor verificatie in twee stappen](multi-factor-authentication-end-user-app-passwords.md) .

### <a name="to-create-a-new-app-password"></a>Een nieuw app-wacht woord maken

1. Meld u aan bij uw werk-of school account en ga vervolgens naar de https://myaccount.microsoft.com/ pagina.

    ![Mijn profiel pagina, met gemarkeerde koppelingen voor beveiligings gegevens](media/security-info/securityinfo-myprofile-page.png)

2. Selecteer **beveiligings gegevens** in het navigatie deel venster links of op de koppeling in het blok met **beveiligings gegevens** en selecteer vervolgens **methode toevoegen** op de pagina **beveiligings gegevens** .

    ![Pagina met beveiligings gegevens met de gemarkeerde optie methode toevoegen](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Selecteer op de pagina **een methode toevoegen** de optie **app-wacht woord** in de vervolg keuzelijst en selecteer vervolgens **toevoegen**.

    ![Het vak methode toevoegen, waarvoor het app-wacht woord is geselecteerd](media/security-info/securityinfo-myprofile-addpassword.png)

4. Typ de naam van de app waarvoor het app-wacht woord is vereist en selecteer **volgende**.

    ![Scherm opname van de pagina app-wacht woord, met de naam van de app die is ingevoerd.](media/security-info/securityinfo-myprofile-password-appname.png)

5. Kopieer de tekst in het vak **wacht** woord, plak het wacht woord in het gebied wacht woord van de app (in dit voor beeld Outlook 2010) en selecteer vervolgens **gereed**.

    ![Pagina app-wacht woord, met de naam van de app](media/security-info/securityinfo-myprofile-password-copytext.png)

    Het wacht woord wordt toegevoegd en u kunt zich met succes aanmelden bij uw app.

## <a name="delete-your-app-passwords"></a>Uw app-wacht woorden verwijderen

Als u een app die een app-wacht woord vereist, niet meer nodig hebt, kunt u het bijbehorende app-wacht woord verwijderen. Als u het app-wacht woord verwijdert, maakt u een van de beschik bare app-wachtwoord vlekken vrij voor gebruik in de toekomst.

>[!Important]
>Als u per ongeluk een app-wacht woord verwijdert, kunt u dit niet ongedaan maken. U moet een nieuw app-wacht woord maken en het opnieuw invoeren in de app. Volg hiervoor de stappen in de sectie [nieuwe app-wacht woorden maken](#create-new-app-passwords) van dit artikel.

### <a name="to-delete-an-app-password"></a>Een app-wacht woord verwijderen

1. Selecteer op de pagina **beveiligings gegevens** de koppeling **verwijderen** naast de optie **app-wacht woord** voor de specifieke app.

    ![Koppeling om de app-wachtwoord methode uit de beveiligings gegevens te verwijderen](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Selecteer **Ja** in het bevestigings venster om het **app-wacht woord** te verwijderen. Nadat het app-wacht woord is verwijderd, wordt het verwijderd uit de beveiligings gegevens en verdwijnt het van de pagina met **beveiligings gegevens** .

## <a name="for-more-information"></a>Voor meer informatie

- Voor meer informatie over de pagina met **beveiligings gegevens** en hoe u deze kunt instellen, raadpleegt u [overzicht van beveiligings gegevens](./security-info-setup-signin.md)