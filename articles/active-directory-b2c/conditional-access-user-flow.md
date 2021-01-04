---
title: Voorwaardelijke toegang toevoegen aan een gebruikersstroom in Azure AD B2C
description: Meer informatie over het toevoegen van voorwaardelijke toegang aan uw Azure AD B2C-gebruikersstromen. Configureer instellingen voor meervoudige verificatie (MFA) en beleid voor voorwaardelijke toegang in uw gebruikersstromen om beleid af te dwingen en riskante aanmeldingen te herstellen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d6d5ab13c8997dffee42a053ba498376ccbcb6d8
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585255"
---
# <a name="add-conditional-access-to-user-flows-in-azure-active-directory-b2c"></a>Voorwaardelijke toegang toevoegen aan gebruikersstromen in Azure Active Directory B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Voorwaardelijke toegang kan worden toegevoegd aan uw Azure Active Directory B2C-gebruikersstromen om riskante aanmeldingen voor uw toepassingen te beheren. Dankzij de integratie van identiteitsbeveiliging en voorwaardelijke toegang in Azure AD B2C kunt u beleidsregels instellen waarmee riskante aanmeldingen worden geïdentificeerd en beleidsregels worden afgedwongen die verdere actie van de gebruiker of beheerder vereisen. Dit zijn de onderdelen voor het inschakelen van voorwaardelijke toegang in Azure AD B2C-gebruikersstromen:

- **Gebruikersstroom**. Maak een gebruikersstroom waarmee de gebruiker wordt begeleid via het aanmeldings- en registratieproces. Geef in de instellingen van de gebruikersstroom aan of het beleid voor voorwaardelijke toegang moet worden geactiveerd wanneer een gebruiker de gebruikersstroom volgt.
- **Toepassing die gebruikers doorstuurt naar de gebruikersstroom**. Configureer uw app voor het doorsturen van gebruikers naar de juiste gebruikersstroom voor registratie en aanmelding door het eindpunt van de gebruikersstroom in de app op te geven.
- **Beleid voor voorwaardelijke toegang**. [Maak een beleid voor voorwaardelijke toegang](conditional-access-identity-protection-setup.md) en geef de apps op waarop u het beleid wilt toepassen. Wanneer de gebruiker de gebruikersstroom voor aanmelden of registreren voor uw app volgt, gebruikt het beleid voor voorwaardelijke toegang signalen voor identiteitsbeveiliging om riskante aanmeldingen te identificeren en wordt zo nodig de juiste herstelactie gepresenteerd.

Voorwaardelijke toegang wordt ondersteund in de meest recente versies van gebruikersstromen. U kunt beleid voor voorwaardelijke toegang toevoegen aan nieuwe gebruikersstromen terwijl u ze maakt, maar u kunt ze ook toevoegen aan bestaande gebruikers, zolang de versie ondersteuning biedt voor voorwaardelijke toegang. Wanneer u voorwaardelijke toegang toevoegt aan een bestaande gebruikersstroom, zijn er twee instellingen die u moet controleren:

- **Meervoudige verificatie (MFA)** : Gebruikers kunnen nu gebruikmaken van een eenmalige code via SMS of voice, of een eenmalig wachtwoord via e-mail voor meervoudige verificatie. MFA-instellingen zijn onafhankelijk van de instellingen voor voorwaardelijke toegang. U kunt MFA instellen op **Altijd aan** zodat MFA altijd vereist is, ongeacht de instelling van de voorwaardelijke toegang. U kunt ook MFA instellen op **Voorwaardelijk**, zodat MFA alleen vereist is wanneer een beleid voor voorwaardelijke toegang vereist is.

- **Voorwaardelijke toegang**: Deze instelling moet altijd **Aan** zijn. Normaal gesproken schakelt u deze instelling alleen **Uit** tijdens het oplossen van problemen of migratie of voor verouderde implementaties.

Meer informatie over [Identiteitsbeveiliging en voorwaardelijke toegang](conditional-access-identity-protection-overview.md) in Azure AD B2C, of kijk [Hoe u deze instelt](conditional-access-identity-protection-setup.md).

## <a name="prerequisites"></a>Vereisten

- Azure AD B2C Premium 2 is vereist voor het maken van beleidsregels voor riskante aanmelding. Premium P1-tenants kunnen locatie-, app-of groepsbeleidsregels maken.
- Voor test doeleinden kunt u [de test-webtoepassing registreren](tutorial-register-applications.md) `https://jwt.ms`, dit is een webtoepassing die eigendom is van Microsoft die de gedecodeerde inhoud van een token weergeeft (de inhoud van het token verlaat nooit uw browser). 
- Als u een riskante aanmelding wilt simuleren, downloadt u de TOR-browser en probeert u zich aan te melden bij het eindpunt van de gebruikersstroom.
- Met behulp van de volgende instellingen [een beleid voor voorwaardelijke toegang maken](conditional-access-identity-protection-setup.md):
   
  - Voor **Gebruikers en groepen** selecteert u de testgebruiker (selecteer niet **Alle gebruikers** of kunt u zich niet meer aanmelden).
  - Kies voor **Cloud-apps of -acties** **Apps selecteren** en kies vervolgens uw Relying Party-toepassing.
  - Selecteer als voorwaarden **Aanmeldingsrisico** en **Hoge** **Medium** en **Lage** risiconiveaus.
  - Kies **Toegang blokkeren** voor **Verlenen**.

      ![Risicodetectie](media/conditional-access-identity-protection-setup/test-conditional-access-policy.png)

::: zone pivot="b2c-user-flow"

## <a name="add-conditional-access-to-a-new-user-flow"></a>Voorwaardelijke toegang toevoegen aan een nieuwe gebruikersstroom

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer onder **Beleid** de optie **Gebruikersstromen** en selecteer vervolgens **Nieuwe gebruikersstroom**.
1. Selecteer op de pagina **Een gebruikersstroom maken** het type gebruikersstroom.
1. Onder **Selecteer een versie** selecteert u **Aanbevolen** en vervolgens **Maken**. ([Meer informatie](user-flow-versions.md) over gebruikersstroomversies.)

    ![De pagina Gebruikersstroom maken in de Azure-portal met eigenschappen gemarkeerd](./media/tutorial-create-user-flows/select-version.png)

1. Voer een **Naam** in voor de gebruikersstroom. Bijvoorbeeld *signupsignin1*.
1. Selecteer in de sectie **Id-providers** de id-providers die u voor deze gebruikersstroom wilt toestaan.
2. Selecteer in de sectie **Meervoudige verificatie** de gewenste **MFA-methode** en selecteer vervolgens onder **MFA afdwingen** **Voorwaardelijk (aanbevolen)** .
 
   ![Meervoudige verificatie configureren](media/conditional-access-user-flow/configure-mfa.png)

1. Schakel in de sectie **Voorwaardelijke toegang** het selectievakje **Beleid voor voorwaardelijke toegang afdwingen** in.

   ![Instellingen voor voorwaardelijke toegang configureren](media/conditional-access-user-flow/configure-conditional-access.png)

1. Kies in de sectie **Gebruikerskenmerken en claims** de claims en kenmerken van de gebruiker die u tijdens de registratie wilt verzamelen en verzenden. Selecteer bijvoorbeeld **Meer weergeven** en kies vervolgens kenmerken en claims voor **Land/regio** en **Weergavenaam**. Selecteer **OK**.

    ![Selectiepagina voor gebruikerskenmerken en claims met drie claims geselecteerd](./media/conditional-access-user-flow/configure-user-attributes-claims.png)

1. Klik op **Maken** om de gebruikersstroom toe te voegen. Het voorvoegsel *B2C_1* wordt automatisch voor de naam geplaatst.

## <a name="add-conditional-access-to-an-existing-user-flow"></a>Voorwaardelijke toegang toevoegen aan een bestaande gebruikersstroom

> [!NOTE]
> De bestaande gebruikersstroom moet een versie zijn die voorwaardelijke toegang ondersteunt. Deze gebruikersstroomversies zijn gelabeld **Aanbevolen**.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.

1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.

1. Selecteer onder **Beleid** **Gebruikersstromen**. Selecteer daarna de gebruikersstroom.

1. Selecteer **Eigenschappen** en zorg dat de gebruikersstroom voorwaardelijke toegang ondersteunt door **Eigenschappen** te selecteren en de instelling genaamd **Voorwaardelijke toegang** te zoeken.
 
   ![MFA en voorwaardelijke toegang configureren in Eigenschappen](media/conditional-access-user-flow/add-conditional-access.png)

1. Selecteer in de sectie **Meervoudige verificatie** de gewenste **MFA-methode** en selecteer vervolgens onder **MFA afdwingen** **Voorwaardelijk (aanbevolen)** .
 
1. Schakel in de sectie **Voorwaardelijke toegang** het selectievakje **Beleid voor voorwaardelijke toegang afdwingen** in.

1. Selecteer **Opslaan**.

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

Als u voorwaardelijke toegang in uw gebruikersstroom wilt testen, [maakt u een beleid voor voorwaardelijke toegang](conditional-access-identity-protection-setup.md) en schakelt u voorwaardelijke toegang in uw gebruikersstroom in zoals hierboven wordt beschreven. 


### <a name="run-the-user-flow"></a>De gebruikersstroom uitvoeren

1. Selecteer de gebruikersstroom die u hebt gemaakt om de overzichtspagina te openen en selecteer **Gebruikersstroom uitvoeren**. Selecteer *webapp1* onder **Toepassing**. De **antwoord-URL** moet `https://jwt.ms` weergeven.

   ![De pagina Gebruikersstroom uitvoeren in de portal met de knop Gebruikersstroom uitvoeren gemarkeerd](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Kopieer de URL onder **Eindpunt voor gebruikersstroom uitvoeren**.

1. Als u een riskante aanmelding wilt simuleren, opent u de [Tor-Browser](https://www.torproject.org/download/) en gebruikt u de URL die u in de preview-stap hebt gekopieerd om u aan te melden bij de geregistreerde app.

1. Voer de gevraagde informatie in op de aanmeldingspagina en probeer u aan te melden. Het token wordt geretourneerd aan `https://jwt.ms` en moet worden weergegeven. In het gedecodeerde token van jwt.ms moet u zien dat de aanmelding is geblokkeerd:

   ![Een geblokkeerde aanmelding testen](media/conditional-access-identity-protection-setup/test-blocked-sign-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-conditional-access-to-your-policy"></a>Voorwaardelijke toegang toevoegen aan uw beleid

In [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access) vindt u een voorbeeld van beleid voor voorwaardelijke toegang.

::: zone-end

## <a name="next-steps"></a>Volgende stappen

[Pas de gebruikersinterface aan in een Azure AD B2C-gebruikersstroom](customize-ui-with-html.md)
