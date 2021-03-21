---
title: Gebruiksvoorwaarden vereisen voor voorwaardelijke toegang - Azure Active Directory
description: In deze quickstart leert u hoe u kunt vereisen dat gebruikers eerst uw gebruiksvoorwaarden accepteren voordat ze toegang tot geselecteerde cloud-apps krijgen via voorwaardelijke toegang van Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab484e8caaffaf57f19f1fcd1e65f4b8e723f86
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077894"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Quickstart: Vereisen dat gebruiksvoorwaarden worden geaccepteerd voor toegang tot cloud-apps

Voordat u gebruikers bepaalde cloud-apps in uw omgeving laat openen, kunt u eerst toestemming van ze vragen door ze uw gebruiksvoorwaarden te laten accepteren. Voorwaardelijke toegang van Azure Active Directory (Azure AD) biedt het volgende:

- Een eenvoudige methode voor het configureren van gebruiksvoorwaarden
- De mogelijkheid om het accepteren van uw gebruiksvoorwaarden te vereisen via een beleid voor voorwaardelijke toegang  

In deze quickstart ontdekt u hoe u een [beleid voor voorwaardelijke toegang voor Azure AD](./overview.md) kunt configureren waarbij gebruiksvoorwaarden moeten worden geaccepteerd voor een geselecteerde cloud-app in uw omgeving.

:::image type="content" source="./media/require-tou/5555.png" alt-text="Schermopname van de Azure Portal. Een deelvenster waarin een beleid met de naam 'Voorwaarden vereisen voor Isabella' wordt gedefinieerd en wordt weergegeven." border="false":::

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van het scenario in deze quickstart hebt u het volgende nodig:

- **Toegang tot een Azure AD Premium-versie**: voorwaardelijke toegang van Azure AD is een Azure AD Premium-functie.
- **Een testaccount onder de naam Isabella Simonsen**: als u niet weet hoe u een testaccount moet maken, raadpleegt u [Cloudgebruikers toevoegen](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Uw aanmelding testen

Het doel van deze stap is om een indruk te krijgen van de aanmeldingservaring zonder een beleid voor voorwaardelijke toegang.

**Uw aanmelding testen:**

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als Isabella Simonsen.
1. Meld u af.

## <a name="create-your-terms-of-use"></a>Uw gebruiksvoorwaarden maken

In deze sectie vindt u de stappen om een voorbeeld van uw gebruiksvoorwaarden te maken. Tijdens het maken van de gebruiksvoorwaarden selecteert u een waarde voor **Afdwingen met sjablonen voor beleid voor voorwaardelijke toegang**. Als u **Aangepast beleid** selecteert, wordt zodra uw gebruiksvoorwaarden zijn gemaakt het dialoogvenster om een nieuw beleid voor voorwaardelijke toegang te maken geopend.

**U kunt als volgt uw gebruiksvoorwaarden maken:**

1. Maak een nieuw document in Microsoft Word.
1. Typ **My terms of use** (Mijn gebruiksvoorwaarden) en sla het document op uw computer op als **mytou.pdf**.
1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als globale beheerder, beveiligingsbeheerder of beheerder voor voorwaardelijke toegang.
1. Klik in de Azure-portal in de linkernavigatiebalk op **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Klik op de pagina **Azure Active Directory** in de sectie **Beveiliging** op de optie **Voorwaardelijke toegang**.

   ![Voorwaardelijke toegang](./media/require-tou/03.png)

1. Klik in de sectie **Beheren** op **Gebruiksvoorwaarden**.

   :::image type="content" source="./media/require-tou/04.png" alt-text="Schermopname van het gedeelte 'Beheren' op de Azure Active Directory-pagina. Het item 'Gebruiksvoorwaarden' is gemarkeerd." border="false":::

1. Klik in het menu bovenaan op **Nieuwe voorwaarden**.

   :::image type="content" source="./media/require-tou/05.png" alt-text="Schermopname van een menu op de Azure Active Directory-pagina. Het item 'Nieuwe voorwaarden' is gemarkeerd." border="false":::

1. Op de pagina **Nieuwe gebruiksvoorwaarden**:

   :::image type="content" source="./media/require-tou/112.png" alt-text="Schermnaam van de pagina 'Nieuwe gebruiksvoorwaarden', met de naam, de weergavenaam, het document, de taal, de voorwaardelijke toegang en de wisselknop voor uitgebreidere voorwaarden gemarkeerd." border="false":::

   1. Voer in het tekstvak **Naam** **My TOU** in.
   1. Typ **My TOU** in het vak **Weergavenaam**.
   1. Upload het PDF-bestand met uw gebruiksvoorwaarden.
   1. Selecteer **Engels** als **Taal**.
   1. Selecteer **Aan** bij de optie **Gebruikers verplichten de gebruiksvoorwaarden uit te vouwen**.
   1. Selecteer bij **Afdwingen met sjablonen voor beleid voor voorwaardelijke toegang** de optie **Aangepast beleid**.
   1. Klik op **Create**.

## <a name="create-your-conditional-access-policy"></a>Uw beleid voor voorwaardelijke toegang maken

In deze sectie wordt beschreven hoe u het vereiste beleid voor voorwaardelijke toegang maakt. In het scenario in deze quickstart wordt het volgende gebruikt:

- De Azure-portal als tijdelijke aanduiding voor een cloud-app waarvoor uw gebruiksvoorwaarden moeten worden geaccepteerd. 
- Uw voorbeeldgebruiker om het beleid voor voorwaardelijke toegang te testen.  

Stel in uw beleid het volgende in:

| Instelling | Waarde |
| --- | --- |
| Gebruikers en groepen | Isabella Simonsen |
| Cloud-apps | Microsoft Azure-beheer |
| Toegang verlenen | My TOU |

:::image type="content" source="./media/require-tou/1234.png" alt-text="Schermopname van een deelvenster in de Azure Portal dat een beleid definieert. Pijlen geven aan dat het beleid toegang verleent tot 'Mijn voorwaarden' en een gebruiker en app bevat." border="false":::

**Uw beleid voor voorwaardelijke toegang configureren:**

1. Typ op de pagina **Nieuw** in het tekstvak **Naam** **Require TOU for Isabella** (Gebruiksvoorwaarden vereisen voor Isabella).

   ![Naam](./media/require-tou/71.png)

1. Klik in de sectie **Toewijzing** op **Gebruikers en groepen**.

   :::image type="content" source="./media/require-tou/06.png" alt-text="Schermopname van het gedeelte 'Toewijzingen' in een deelvenster in de Azure Portal dat een beleid definieert. Het item 'Gebruikers en groepen' is zichtbaar, maar er is niets geselecteerd." border="false":::

1. Op de pagina **Gebruikers en groepen**:

   :::image type="content" source="./media/require-tou/24.png" alt-text="Schermopname van het tabblad 'Opnemen' op de pagina 'Gebruikers en groepen'. 'Gebruikers en groepen selecteren' en 'Gebruikers en groepen' zijn geselecteerd. 'Selecteren' is gemarkeerd." border="false":::

   1. Klik op **Gebruikers en groepen selecteren** en selecteer vervolgens **Gebruikers en groepen**.
   1. Klik op **Selecteren**.
   1. Selecteer op de pagina **Selecteren** de optie **Isabella Simonsen** en klik vervolgens op **Selecteren**.
   1. Klik op de pagina **Gebruikers en groepen** op **Gereed**.
1. Klik op **Cloud-apps**.

   :::image type="content" source="./media/require-tou/08.png" alt-text="Schermopname van het gedeelte 'Toewijzingen' in een deelvenster in de Azure Portal dat een beleid definieert. Het item 'Cloud-apps' is zichtbaar, maar er is niets geselecteerd." border="false":::

1. Op de pagina **Cloud-apps**:

   ![Cloud-apps selecteren](./media/require-tou/26.png)

   1. Klik op **Apps selecteren**.
   1. Klik op **Selecteren**.
   1. Selecteer op de pagina **Selecteren** de optie **Microsoft Azure Management** en klik vervolgen op **Selecteren**.
   1. Klik op de pagina **Cloud-apps** op **Gereed**.
1. Klik in de sectie **Besturingselementen voor toegang** op **Toekennen**.

   ![Besturingselementen voor toegang](./media/require-tou/10.png)

1. Op de pagina **Toekennen**:

   ![Verlenen](./media/require-tou/111.png)

   1. Selecteer **Toegang verlenen**.
   1. Selecteer **My TOU** (Mijn gebruiksvoorwaarden).
   1. Klik op **Selecteren**.
1. Klik in de sectie **Beleid inschakelen** op **Aan**.

   ![Beleid inschakelen](./media/require-tou/18.png)

1. Klik op **Create**.

## <a name="evaluate-a-simulated-sign-in"></a>Een gesimuleerde aanmelding evalueren

Nu u uw beleid voor voorwaardelijke toegang hebt geconfigureerd, wilt u vast weten of het werkt zoals u wilt. Als eerste stap kunt u het What If-beleidshulpmiddel voor voorwaardelijke toegang gebruiken om een aanmelding van uw testgebruiker te simuleren. De simulatie schat de impact van deze aanmelding op uw beleid in en genereert een simulatierapport.  

Als u het **What If**-hulpprogramma voor het evalueren van beleid wilt initialiseren, stelt u het volgende in:

- **Isabella Simonsen** als gebruiker
- **Microsoft Azure Management** als cloud-app

Klik op **What If** om een simulatierapport te maken waarin het volgende wordt weergegeven:

- **Require TOU for Isabella** (Gebruiksvoorwaarden vereisen voor Isabella). onder **Beleidsregels die worden toegepast**
- **My TOU** (Mijn gebruiksvoorwaarden) als **Besturingselementen toekennen**.

![What If-beleidshulpmiddel](./media/require-tou/79.png)

**Uw beleid voor voorwaardelijke toegang evalueren:**

1. Klik op de pagina beleid [Voorwaardelijke toegang – Beleid](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) in het menu aan de bovenaan op **What If**.  

   ![What If](./media/require-tou/14.png)

1. Klik op **Gebruikers**, selecteer **Isabella Simonsen** en klik vervolgens op **Selecteren**.

   ![Gebruiker](./media/require-tou/15.png)

1. Een cloud-app selecteren:

   :::image type="content" source="./media/require-tou/16.png" alt-text="Schermopname van de sectie 'Cloud-apps'. De tekst geeft aan dat er één app is geselecteerd." border="false":::

   1. Klik op **Cloud-apps**.
   1. Klik op de **pagina Cloud-apps** op **Apps selecteren**.
   1. Klik op **Selecteren**.
   1. Selecteer op de pagina **Selecteren** de optie **Microsoft Azure Management** en klik vervolgen op **Selecteren**.
   1. Klik op de pagina Cloud-apps op **Gereed**.
1. Klik op **What If**.

## <a name="test-your-conditional-access-policy"></a>Uw beleid voor voorwaardelijke toegang testen

In de vorige sectie hebt u geleerd hoe u een gesimuleerde aanmelding kunt evalueren. Naast deze simulatie kunt u het beste ook het beleid voor voorwaardelijke toegang testen om er zeker van te zijn dat het werkt zoals verwacht.

Als u uw beleid wilt testen, meldt u zich aan bij de [Azure-portal](https://portal.azure.com) met uw **Isabella Simonsen**-testaccount. Er zou een dialoogvenster moeten worden weergegeven waarin u wordt gevraagd de gebruiksvoorwaarden te accepteren.

:::image type="content" source="./media/require-tou/57.png" alt-text="Schermopname van een dialoogvenster met de titel 'Gebruiksvoorwaarden voor Identity Security Protection' met de knoppen 'Weigeren' en 'Accepteren' en een knop met de naam 'Mijn voorwaarden'." border="false":::

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de testgebruiker en het beleid voor voorwaardelijke toegang, wanneer deze niet langer nodig zijn:

- Zie [Gebruikers verwijderen uit Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user) als u niet weet hoe u een Azure AD-gebruiker verwijdert.
- Als u uw beleid wilt verwijderen, selecteert u het beleid en klikt u op **Verwijderen** op de werkbalk voor snelle toegang.

    :::image type="content" source="./media/require-tou/33.png" alt-text="Schermopname van een beleid met de naam 'MFA vereisen voor Azure Portal-gebruikers. Het contextmenu is zichtbaar, met de optie 'Verwijderen' gemarkeerd." border="false":::

- Als u de gebruiksvoorwaarden wilt verwijderen, selecteert u deze en klikt u vervolgens in de werkbalk bovenaan op **Gebruiksvoorwaarden verwijderen**.

    :::image type="content" source="./media/require-tou/29.png" alt-text="Schermopname van een deel van een tabel met documenten voor gebruiksvoorwaarden. Het document 'Mijn voorwaarden' is zichtbaar. In het menu is 'Voorwaarden verwijderen' gemarkeerd." border="false":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meervoudige verificatie vereisen voor specifieke apps](../authentication/tutorial-enable-azure-mfa.md)