---
title: Verificatie met eenmalige wachtwoord code voor B2B-gast gebruikers-Azure AD
description: Eenmalige e-mail wachtwoord verificatie gebruiken om B2B-gast gebruikers te verifiëren zonder dat hiervoor een Microsoft-account nodig is.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7961997c6a6736c154b6217ee3f21682d0c4c3fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101688464"
---
# <a name="email-one-time-passcode-authentication"></a>Verificatie met eenmalige e-mail wachtwoord code

In dit artikel wordt beschreven hoe u eenmalige verificatie via e-mail voor B2B-gast gebruikers inschakelt. Met de functie voor eenmalige e-mail code worden B2B-gast gebruikers geverifieerd wanneer ze niet kunnen worden geverifieerd via andere manieren, zoals Azure AD, een Microsoft-account (MSA) of Google Federatie. Met authenticatie op basis van eenmalige wachtwoord code hoeft u geen Microsoft-account te maken. Wanneer de gast gebruiker een uitnodiging heeft ingewisseld of een gedeelde resource opent, kunnen ze een tijdelijke code aanvragen, die wordt verzonden naar hun e-mail adres. Vervolgens voeren ze deze code in om door te gaan met aanmelden.

![Overzichts diagram van het wacht woord voor eenmalige e-mail](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **Vanaf 2021 oktober** wordt de functie voor eenmalige e-mail wachtwoord code ingeschakeld voor alle bestaande tenants en standaard ingeschakeld voor nieuwe tenants. Als u niet wilt toestaan dat deze functie automatisch wordt ingeschakeld, kunt u deze uitschakelen. Zie [eenmalige E-mail uitschakelen onderstaande wachtwoord code](#disable-email-one-time-passcode) .
> - De e-mail wachtwoord instellingen voor eenmalige e-mail zijn verplaatst naar de Azure Portal van **externe samenwerkings instellingen** naar **alle id-providers**.

> [!NOTE]
> Eenmalige wachtwoord code gebruikers moeten zich aanmelden met behulp van een koppeling die de Tenant context bevat (bijvoorbeeld `https://myapps.microsoft.com/?tenantid=<tenant id>` of `https://portal.azure.com/<tenant id>` , of in het geval van een geverifieerd domein `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). Directe koppelingen naar toepassingen en bronnen werken ook zolang ze de context van de Tenant bevatten. Gast gebruikers kunnen zich momenteel niet aanmelden met eind punten die geen Tenant context hebben. Als u bijvoorbeeld gebruikt `https://myapps.microsoft.com` , treedt er `https://portal.azure.com` een fout op.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Gebruikers ervaring voor eenmalige wachtwoord code gast gebruikers

Wanneer de functie voor eenmalige e-mail wachtwoord code is ingeschakeld, gebruiken recent uitgenodigde gebruikers [die aan bepaalde voor waarden voldoen](#when-does-a-guest-user-get-a-one-time-passcode) , een verificatie met een eenmalige wachtwoord code. Gast gebruikers die een uitnodiging hebben ingewisseld voordat een e-mail bericht met een tijd wachtwoord code is ingeschakeld, blijven dezelfde verificatie methode gebruiken.

Met verificatie met eenmalige wachtwoord code kan de gast gebruiker de uitnodiging inwisselen door te klikken op een directe koppeling of door de uitnodiging-e-mail te gebruiken. In beide gevallen geeft een bericht in de browser aan dat een code wordt verzonden naar het e-mail adres van de gast gebruiker. De gast gebruiker selecteert **Verzend code**:

   ![Scherm afbeelding met de knop code verzenden](media/one-time-passcode/otp-send-code.png)

Er wordt een wachtwoord code verzonden naar het e-mail adres van de gebruiker. De gebruiker haalt de wachtwoord code op uit het e-mail bericht en voert deze in het browser venster in:

   ![Scherm opname met de code pagina invoeren](media/one-time-passcode/otp-enter-code.png)

De gast gebruiker is nu geverifieerd en ze kunnen de gedeelde bron zien of zich blijven aanmelden.

> [!NOTE]
> Eenmalige wachtwoord codes zijn 30 minuten geldig. Na 30 minuten is de specifieke eenmalige wachtwoord code niet meer geldig en moet de gebruiker een nieuwe wacht woord aanvragen. Gebruikers sessies verlopen na 24 uur. Daarna ontvangt de gast gebruiker een nieuwe wachtwoord code wanneer hij of zij toegang tot de bron heeft. Sessie verloop biedt extra beveiliging, vooral wanneer een gast gebruiker het bedrijf verlaat of geen toegang meer nodig heeft.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Wanneer krijgt een gast gebruiker een eenmalige wachtwoord code?

Wanneer een gast gebruiker een uitnodiging heeft ingewisseld of een koppeling gebruikt naar een resource die met hen is gedeeld, ontvangt deze een eenmalige wachtwoord code als:

- Ze hebben geen Azure AD-account
- Ze hebben geen Microsoft-account
- De uitnodigende Tenant heeft geen Google-Federatie ingesteld voor @gmail.com en @googlemail.com gebruikers

Op het moment van de uitnodiging is er geen indicatie dat de gebruiker die u uitnodigt, gebruikmaakt van verificatie met een eenmalige wachtwoord code. Maar wanneer de gast gebruiker zich aanmeldt, is verificatie met een eenmalige wachtwoord code de terugval methode als er geen andere verificatie methoden kunnen worden gebruikt.

U kunt zien of een gast gebruiker zich verifieert met een eenmalige wachtwoord code door de **bron** eigenschap in de gegevens van de gebruiker weer te geven. Ga in het Azure Portal naar **Azure Active Directory**  >  **gebruikers** en selecteer vervolgens de gebruiker om de pagina Details te openen.

![Scherm afbeelding met een eenmalige wachtwoord code gebruiker met bron waarde van OTP](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Wanneer een gebruiker een eenmalige wachtwoord code inwisselt en later een MSA-, Azure AD-account of een ander federatief account ontvangt, worden ze nog steeds geverifieerd met een eenmalige wachtwoord code. Als u de verificatie methode wilt bijwerken, kunt u het gebruikers account van de gast verwijderen en opnieuw uitnodigen.

### <a name="example"></a>Voorbeeld

Gast gebruiker teri@gmail.com wordt uitgenodigd voor fabrikam, waarvoor geen Google Federation is ingesteld. Teri heeft geen Microsoft-account. Er wordt een eenmalige wachtwoord code voor verificatie ontvangen.

## <a name="disable-email-one-time-passcode"></a>Wachtwoord code voor eenmalige e-mail uitschakelen

Vanaf 2021 oktober wordt de functie voor eenmalige e-mail wachtwoord code ingeschakeld voor alle bestaande tenants en standaard ingeschakeld voor nieuwe tenants. Micro soft biedt op dat moment geen ondersteuning meer voor het aflossen van uitnodigingen door het maken van niet-beheerde (' virale ' of ' just-in-time ') Azure AD-accounts en tenants voor B2B-samenwerkings scenario's. De functie voor eenmalige e-mail wachtwoord code wordt ingeschakeld, omdat deze een naadloze terugval verificatie methode biedt voor uw gast gebruikers. U hebt echter de mogelijkheid om deze functie uit te scha kelen als u deze niet wilt gebruiken.

> [!NOTE]
>
> Als de functie voor eenmalige e-mail wachtwoord code is ingeschakeld in uw Tenant en u deze uitschakelt, kunnen gast gebruikers die een eenmalige wachtwoord code hebben ingewisseld, zich niet aanmelden. U kunt de gast gebruiker verwijderen en opnieuw uitnodigen om zich opnieuw aan te melden met een andere verificatie methode.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>De functie voor eenmalige e-mail wachtwoord code uitschakelen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als een globale Azure AD-beheerder.

2. Selecteer in het navigatiedeelvenster de service **Azure Active Directory**.

3. Selecteer **externe identiteiten**  >  **alle id-providers**.

4. Selecteer **eenmalige e-mail** en selecteer **eenmalige wachtwoord code voor gasten uitschakelen**.

   > [!NOTE]
   > De e-mail wachtwoord instellingen voor eenmalige e-mail zijn verplaatst naar de Azure Portal van **externe samenwerkings instellingen** naar **alle id-providers**.
   > Als er een wissel knop wordt weer gegeven in plaats van de opties voor eenmalige e-mail wachtwoord code, betekent dit dat u eerder hebt ingeschakeld, uitgeschakeld of hebt gekozen voor de preview van de functie. Selecteer **Nee** om de functie uit te scha kelen.
   >
   >![E-mail eenmalige wachtwoord code in-/uitschakelen uitgeschakeld](media/one-time-passcode/enable-email-otp-disabled.png)

5. Selecteer **Opslaan**.

## <a name="note-for-public-preview-customers"></a>Opmerking voor open bare preview-klanten

Als u eerder hebt gekozen voor de open bare preview-versie van het e-mail wachtwoord voor de e-mail, is de datum van oktober 2021 voor automatische activering niet van toepassing op u, zodat uw gerelateerde bedrijfs processen niet worden beïnvloed. Daarnaast ziet u in de Azure Portal, onder de **wachtwoord code voor het eenmalige e-mail adres voor gasten** , de optie voor het **automatisch inschakelen van e-mail eenmalige wachtwoord code voor gasten vanaf oktober 2021**. In plaats daarvan wordt de volgende **Ja** -of **geen** wissel knop weer geven:

![E-mail met eenmalige wachtwoord code](media/one-time-passcode/enable-email-otp-opted-in.png)

Als u de functie echter wilt uitschakelen en wilt toestaan dat deze automatisch kan worden ingeschakeld in oktober 2021, kunt u terugkeren naar de standaard instellingen met behulp van het [bron type configuratie van de Microsoft Graph-API-e-mail verificatie methode](/graph/api/resources/emailauthenticationmethodconfiguration). Nadat u de standaard instellingen hebt hersteld, zijn de volgende opties beschikbaar onder **e-mail eenmalige wachtwoord code voor gasten**:

![Eenmalige E-mail inschakelen wacht woord voor wachtwoord registratie](media/one-time-passcode/email-otp-options.png)

- **Eenmalige e-mail wachtwoord voor gasten automatisch inschakelen vanaf oktober 2021**. Prijs Als de functie voor eenmalige e-mail wachtwoord code nog niet is ingeschakeld voor uw Tenant, wordt deze automatisch ingeschakeld vanaf oktober 2021. Er is geen verdere actie nodig als u de functie op dat moment wilt inschakelen. Als u de functie al hebt ingeschakeld of uitgeschakeld, is deze optie niet beschikbaar.

- **Eenmalige e-mail wachtwoord instellen voor gasten die nu effectief** zijn. Hiermee schakelt u de functie e-mail eenmalige wachtwoord code in voor uw Tenant.

- **Eenmalige e-mail wachtwoord voor gasten uitschakelen**. Hiermee schakelt u de functie e-mail eenmalige wachtwoord code voor uw Tenant uit en voor komt u dat de functie wordt ingeschakeld in oktober 2021.

## <a name="note-for-azure-us-government-customers"></a>Opmerking voor klanten van de Amerikaanse overheid van Azure

De functie voor eenmalige e-mail wachtwoord code is standaard uitgeschakeld in de Azure-Cloud voor de Amerikaanse overheid.  

 ![E-mail eenmalige wachtwoord code uitgeschakeld](media/one-time-passcode/enable-email-otp-disabled.png)

De functie voor eenmalige e-mail wachtwoord code in de Azure-Cloud voor de Amerikaanse overheid inschakelen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale Azure AD-beheerder.
2. Selecteer in het navigatiedeelvenster de service **Azure Active Directory**.
3. Instellingen voor **organisatie relaties** selecteren   >  ****.

   > [!NOTE]
   > - Als u geen **organisatie relaties** ziet, zoekt u naar ' externe identiteiten ' in de zoek balk aan de bovenkant.

4. Selecteer **eenmalige e-mail wachtwoord code** en selecteer vervolgens **Ja**.
5. Selecteer **Opslaan**.

Zie [Azure Amerikaanse overheids Clouds](current-limitations.md#azure-us-government-clouds)voor meer informatie over huidige beperkingen.