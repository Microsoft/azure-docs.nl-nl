---
title: Verificatie van een een time-wachtwoordcode voor B2B-gastgebruikers - Azure AD
description: Een een time-time wachtwoordcode e-mail gebruiken om B2B-gastgebruikers te verifiëren zonder dat er een Microsoft-account.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b4089559b341dd268928b1f150b6fc173869ead
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529925"
---
# <a name="email-one-time-passcode-authentication"></a>Verificatie met een een time-time wachtwoordcode per e-mail

In dit artikel wordt beschreven hoe u verificatie van een een time-mail wachtwoordcode inschakelen voor B2B-gastgebruikers. De functie voor een een time-wachtwoordcode voor e-mail verifieert B2B-gastgebruikers wanneer ze niet kunnen worden geverifieerd via andere middelen, zoals Azure AD, een Microsoft-account (MSA) of Google-federatie. Met een een time-wachtwoordcodeverificatie hoeft u geen wachtwoordcode te Microsoft-account. Wanneer gastgebruikers een uitnodiging inwisselen of toegang krijgen tot een gedeelde resource, kunnen ze een tijdelijke code aanvragen die wordt verzonden naar hun e-mailadres. Vervolgens voeren ze deze code in om zich verder aan te melden.

![Overzichtsdiagram voor een een time-wachtwoordcode e-mailen](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **Vanaf oktober 2021** wordt de functie voor een een time-mail wachtwoordcode ingeschakeld voor alle bestaande tenants en standaard ingeschakeld voor nieuwe tenants. Als u niet wilt dat deze functie automatisch wordt in schakelen, kunt u deze uitschakelen. Zie [E-mail een een time-time wachtwoordcode uitschakelen](#disable-email-one-time-passcode) hieronder.
> - De instellingen voor een een time-wachtwoordcode voor e-mail zijn in de Azure Portal van Externe **samenwerkingsinstellingen** verplaatst **naar Alle id-providers.**

> [!NOTE]
> Gebruikers van een een time-wachtwoordcode moeten zich aanmelden met een koppeling die de tenantcontext bevat (bijvoorbeeld of , of in het geval van `https://myapps.microsoft.com/?tenantid=<tenant id>` `https://portal.azure.com/<tenant id>` een geverifieerd domein, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com` ). Directe koppelingen naar toepassingen en resources werken ook zolang ze de tenantcontext bevatten. Gastgebruikers kunnen zich momenteel niet aanmelden met eindpunten die geen tenantcontext hebben. Als u bijvoorbeeld `https://myapps.microsoft.com` gebruikt, `https://portal.azure.com` resulteert dit in een fout.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Gebruikerservaring voor gastgebruikers met een een time-wachtwoordcode

Wanneer de functie voor een een time-time [](#when-does-a-guest-user-get-a-one-time-passcode) wachtwoordcode voor e-mail is ingeschakeld, gebruiken nieuw uitgenodigde gebruikers die aan bepaalde voorwaarden voldoen, een een time-wachtwoordcodeverificatie. Gastgebruikers die een uitnodiging hebben ingewisseld voordat een een time-time wachtwoordcode per e-mail is ingeschakeld, blijven dezelfde verificatiemethode gebruiken.

Met een een time-time wachtwoordcodeverificatie kan de gastgebruiker uw uitnodiging inwisselen door op een directe koppeling te klikken of via de uitnodigings-e-mail. In beide gevallen geeft een bericht in de browser aan dat er een code wordt verzonden naar het e-mailadres van de gastgebruiker. De gastgebruiker selecteert **Code verzenden:**

   ![Schermopname van de knop Code verzenden](media/one-time-passcode/otp-send-code.png)

Er wordt een wachtwoordcode verzonden naar het e-mailadres van de gebruiker. De gebruiker haalt de wachtwoordcode op uit het e-mailbericht en voert deze in het browservenster in:

   ![Schermopname van de pagina Code invoeren](media/one-time-passcode/otp-enter-code.png)

De gastgebruiker is nu geverifieerd en kan de gedeelde resource zien of zich blijven aanmelden.

> [!NOTE]
> Een een time-wachtwoordcodes zijn 30 minuten geldig. Na 30 minuten is die specifieke een time-wachtwoordcode niet meer geldig en moet de gebruiker een nieuwe aanvragen. Gebruikerssessies verlopen na 24 uur. Na die tijd ontvangt de gastgebruiker een nieuwe wachtwoordcode wanneer deze toegang heeft tot de resource. Verlopen van sessies biedt extra beveiliging, met name wanneer een gastgebruiker het bedrijf verlaat of geen toegang meer nodig heeft.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Wanneer krijgt een gastgebruiker een een keer een wachtwoordcode?

Wanneer een gastgebruiker een uitnodiging inwisselt of een koppeling gebruikt naar een resource die met hem of haar is gedeeld, ontvangt deze een een time-wachtwoordcode als:

- Ze hebben geen Azure AD-account
- Ze hebben geen Microsoft-account
- De uitnodigende tenant heeft geen Google-federatie ingesteld voor @gmail.com - en @googlemail.com -gebruikers

Op het moment van uitnodiging is er geen indicatie dat de gebruiker die u uitnodigt, een een time-time wachtwoordcodeverificatie gebruikt. Maar wanneer de gastgebruiker zich meldt, is verificatie met een een time-wachtwoordcode de terugvalmethode als er geen andere verificatiemethoden kunnen worden gebruikt.

U kunt zien of een gastgebruiker zich verifieert met behulp van een een time-wachtwoordcodes door de eigenschap **Source** te bekijken in de details van de gebruiker. Ga in Azure Portal naar **Azure Active Directory** Gebruikers en selecteer vervolgens de  >  gebruiker om de detailpagina te openen.

![Schermopname van een een time-wachtwoordcodegebruiker met de bronwaarde OTP](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Wanneer een gebruiker een een time-wachtwoordcode inwisselt en later een MSA, Een Azure AD-account of een ander federatief account verkrijgt, wordt deze nog steeds geverifieerd met behulp van een een time-wachtwoordcode. Als u de verificatiemethode van de gebruiker wilt bijwerken, kunt u de [inwisselstatus opnieuw instellen.](reset-redemption-status.md)

### <a name="example"></a>Voorbeeld

teri@gmail.comGastgebruiker wordt uitgenodigd voor Fabrikam, waarvoor geen Google-federatie is ingesteld. Teri heeft geen Microsoft-account. Ze ontvangen een een time-wachtwoordcode voor verificatie.

## <a name="disable-email-one-time-passcode"></a>Een een time-time wachtwoordcode voor e-mail uitschakelen

Vanaf oktober 2021 wordt de functie voor een een time-mail wachtwoordcode ingeschakeld voor alle bestaande tenants en standaard ingeschakeld voor nieuwe tenants. Op dat moment biedt Microsoft geen ondersteuning meer voor het inwisselen van uitnodigingen door het maken van onmanaged ('viral' of 'just-in-time') Azure AD-accounts en -tenants voor B2B-samenwerkingsscenario's. De functie voor een een time-wachtwoordcode voor e-mail wordt inschakelen omdat deze een naadloze terugvalmethode biedt voor uw gastgebruikers. U kunt deze functie echter uitschakelen als u ervoor kiest deze niet te gebruiken.

> [!NOTE]
>
> Als de functie voor eenmalige wachtwoordcode voor e-mail is ingeschakeld in uw tenant en u deze uit schakelen, kunnen gastgebruikers die een eenmalige wachtwoordcode hebben ingewisseld, zich niet aanmelden. U kunt [de inwisselstatus opnieuw instellen,](reset-redemption-status.md) zodat ze zich opnieuw kunnen aanmelden met een andere verificatiemethode.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>De functie voor een een time-wachtwoordcode per e-mail uitschakelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/) globale beheerder van Azure AD.

2. Selecteer in het navigatiedeelvenster de service **Azure Active Directory**.

3. Selecteer **External Identities**  >  **Alle id-providers.**

4. Selecteer **Een een time-wachtwoordcode e-mailen** selecteer vervolgens **E-mail een time-wachtwoordcode uitschakelen voor gasten.**

   > [!NOTE]
   > De instellingen voor een een-time wachtwoordcodes per e-mail zijn in de Azure Portal **verplaatst** van Instellingen voor externe samenwerking naar **Alle id-providers.**
   > Als u een schakelknop ziet in plaats van de opties voor een een time-time wachtwoordcode voor e-mail, betekent dit dat u eerder de preview van de functie hebt ingeschakeld, uitgeschakeld of hebt gekozen. Selecteer **Nee om** de functie uit te schakelen.
   >
   >![Een een time-time wachtwoordcode in-/uitschakelen](media/one-time-passcode/enable-email-otp-disabled.png)

5. Selecteer **Opslaan**.

## <a name="note-for-public-preview-customers"></a>Opmerking voor klanten met een openbare preview

Als u eerder hebt gekozen voor de openbare preview van de een time-time wachtwoordcode, is de datum van oktober 2021 voor het inschakelen van automatische functies niet op u van toepassing, waardoor uw gerelateerde bedrijfsprocessen niet worden beïnvloed. Daarnaast ziet u in de Azure Portal onder de eigenschappen Een een **time-time** wachtwoordcode voor gasten e-mailen de optie E-mail automatisch inschakelen voor gasten vanaf oktober **2021** niet meer. In plaats daarvan ziet u de volgende schakelknop **Ja** **of** Nee:

![Een een time-time wachtwoordcode in- of uit te geven](media/one-time-passcode/enable-email-otp-opted-in.png)

Als u de functie echter liever wilt uitschakelen en deze automatisch wilt inschakelen in oktober 2021, kunt u de standaardinstellingen [](/graph/api/resources/emailauthenticationmethodconfiguration)herstellen met behulp van het configuratiebrontype van de e-mailverificatiemethode van de Microsoft Graph-API. Nadat u de standaardinstellingen hebt teruggedraaid, zijn de volgende opties beschikbaar onder **Een een time-time wachtwoordcode e-mailen voor gasten:**

![Een een time-time wachtwoordcode voor e-mail inschakelen](media/one-time-passcode/email-otp-options.png)

- **Schakel vanaf oktober 2021** automatisch een een time-time wachtwoordcode voor e-mail in voor gasten. (Standaard) Als de functie voor een een time-time wachtwoordcode voor e-mail nog niet is ingeschakeld voor uw tenant, wordt deze functie automatisch ingeschakeld vanaf oktober 2021. Er is geen verdere actie nodig als u wilt dat de functie op dat moment is ingeschakeld. Als u de functie al hebt ingeschakeld of uitgeschakeld, is deze optie niet beschikbaar.

- **Schakel een een time-time wachtwoordcode in voor gasten die nu van kracht zijn.** Schakelt de e-mailfunctie voor een een time-time wachtwoordcode in voor uw tenant.

- **Een een time-time wachtwoordcode voor gasten uitschakelen.** Hiermee schakelt u de functie voor eenmalige wachtwoordcode voor uw tenant uit en wordt voorkomen dat de functie in oktober 2021 wordt in schakelen.

## <a name="note-for-azure-us-government-customers"></a>Opmerking voor Azure-klanten voor de Amerikaanse overheid

De functie voor een een time-wachtwoordcode voor e-mail is standaard uitgeschakeld in de cloud van Azure US Government. Uw partners kunnen zich niet aanmelden, tenzij deze functie is ingeschakeld. In tegenstelling tot de openbare Cloud van Azure biedt de Cloud van Azure voor de Amerikaanse overheid geen ondersteuning voor het inwisselen van uitnodigingen met selfservice Azure Active Directory accounts.

 ![Een een time-time wachtwoordcode e-mail uitgeschakeld](media/one-time-passcode/enable-email-otp-disabled.png)

De functie voor een een time-wachtwoordcode voor e-mail inschakelen in de Azure US Government-cloud:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder van Azure AD.
2. Selecteer in het navigatiedeelvenster de service **Azure Active Directory**.
3. Selecteer **Organisatierelaties**   >  **Alle id-providers.**

   > [!NOTE]
   > - Als u Organisatierelaties niet **ziet,** zoekt u naar 'External Identities' in de zoekbalk bovenaan.

4. Selecteer **Een een time-wachtwoordcode e-mailen** selecteer vervolgens **Ja.**
5. Selecteer **Opslaan**.

Zie Azure [US Government-clouds](current-limitations.md#azure-us-government-clouds)voor meer informatie over de huidige beperkingen.
