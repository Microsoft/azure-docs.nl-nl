---
title: Wachtwoord terugschrijven in Azure Active Directory inschakelen
description: In deze zelfstudie leert u hoe het terugschrijven van self-service voor wachtwoordherstel in Azure AD kunt inschakelen met Azure AD Connect, voor het synchroniseren van wijzingen met een on-premises Active Directory Domain Services-omgeving.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 444ca19732921b336cae32a9b1eb5755a08e4bd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97028050"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Zelfstudie: Terugschrijven van self-service voor wachtwoordherstel in Azure Active Directory inschakelen voor on-premises omgeving

Met self-service voor wachtwoordherstel (SSPR) in Azure Active Directory (Azure AD) kunnen gebruikers hun wachtwoord bijwerken of hun account ontgrendelen in een webbrowser. In een hybride omgeving waarin Azure AD is gekoppeld aan een on-premises Active Directory Domain Services-omgeving (AD DS), kan dit scenario ertoe leiden dat wachtwoorden verschillend zijn in de twee directory’s.

Wachtwoord terugschrijven wordt gebruikt om wachtwoordwijzigingen in Azure AD terug te synchroniseren met uw on-premises AD DS-omgeving. Azure AD Connect biedt een beveiligd mechanisme voor het terugzenden van deze wachtwoordwijzigingen naar een bestaande on-premises directory van Azure AD.

> [!IMPORTANT]
> In deze zelfstudie wordt uitgelegd hoe een beheerder het terugschrijven van self-service voor wachtwoordherstel kan inschakelen voor een on-premises omgeving. Als u een eindgebruiker bent die al is geregistreerd voor self-service voor wachtwoordherstel en u weer toegang tot uw account wilt hebben, gaat u naar https://aka.ms/sspr.
>
> Als uw IT-team u de mogelijkheid niet heeft gegeven uw eigen wachtwoord opnieuw in te stellen, kunt u contact opnemen met de helpdesk voor meer informatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De benodigde machtigingen voor het terugschrijven van wachtwoorden configureren
> * De optie Wachtwoord terugschrijven inschakelen in Azure AD Connect
> * Wachtwoord terugschrijven inschakelen in Azure AD SSPR

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u de volgende resources en machtigingen nodig:

* Een werkende Azure AD-tenant waarop minimaal een Azure AD Premium P1- of -proeflicentie is ingeschakeld.
    * [Maak er gratis een](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) indien nodig.
    * Zie [Licentievereisten voor Azure AD SSPR](concept-sspr-licensing.md) voor meer informatie.
* Een account met de bevoegdheden van een *globale beheerder*.
* Azure Active Directory geconfigureerd voor de self-service voor wachtwoordherstel.
    * Voltooi indien nodig [de vorige zelfstudie om Azure Active Directory SSPR in te schakelen](tutorial-enable-sspr.md).
* Een bestaande on-premises AD DS-omgeving die is geconfigureerd met een actuele versie van Azure AD Connect.
    * Configureer indien nodig Azure AD Connect met de instellingen [Express](../hybrid/how-to-connect-install-express.md) of [Aangepast](../hybrid/how-to-connect-install-custom.md).
    * Om wachtwoord terugschrijven te kunnen gebruiken, moeten uw domeincontrollers Windows Server 2012 of hoger zijn.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Accountmachtigingen configureren voor Azure AD Connect

Met Azure AD Connect kunt u gebruikers, groepen en referenties synchroniseren tussen een on-premises AD DS-omgeving en Azure Active Directory. Meestal wordt Azure AD Connect geïnstalleerd op een computer met Windows 2012 of hoger, die deel uitmaakt van het on-premises AD DS-domein.

Om terugschrijven met SSPR goed te laten werken, moeten voor het account dat is opgegeven in Azure AD Connect de juiste machtigingen en opties zijn ingesteld. Als u niet zeker weet welke account momenteel in gebruik is, opent u Azure AD Connect en selecteert u de optie **Huidige configuratie weergeven**. Het account waaraan u machtigingen moet geven wordt vermeld onder **Gesynchroniseerde mappen**. Voor het account moeten de volgende machtigingen en opties zijn ingesteld:

* **Wachtwoord opnieuw instellen**
* **Schrijfmachtigingen** voor `lockoutTime`
* **Schrijfmachtigingen** voor `pwdLastSet`
* **Uitgebreide rechten** voor Verlopen wachtwoord terugzetten op het hoofdobject van *elk domein* in dat forest, indien dit nog niet is ingesteld.

Als u deze machtigingen niet toewijst, lijkt terugschrijven correct geconfigureerd, maar krijgen de gebruikers te maken met fouten bij het beheren van hun on-premises wachtwoorden in de cloud. Machtigingen moeten zijn toegepast op **dit object en alle onderliggende objecten** voordat de optie om verlopen wachtwoorden te herstellen verschijnt.  

> [!TIP]
>
> Als wachtwoorden voor sommige gebruikersaccounts niet worden teruggeschreven naar on-premises map, moet u ervoor zorgen dat overname niet is uitgeschakeld voor het account in de on-premises AD DS-omgeving. Schrijfrechten voor wachtwoorden moeten worden toegepast op onderliggende objecten om deze functie correct te laten werken.

Voer de volgende stappen uit om de juiste machtigingen voor het terugschrijven van wachtwoorden in te stellen:

1. Open in uw on-premises AD DS-omgeving **Active Directory: gebruikers en computers** met een account met de juiste *machtigingen voor domeinbeheer*.
1. Zorg ervoor dat in het menu **Weergave** de optie **Geavanceerde functies** is ingeschakeld.
1. Selecteer in het linkerdeelvenster met de rechtermuisknop het object dat staat voor de hoofdmap van het domein en selecteer **Eigenschappen** > **Beveiliging** > **Geavanceerd**.
1. Selecteer op het tabblad **Machtigingen** de optie **Toevoegen**.
1. Selecteer voor **Principal** het account waarop de machtigingen moeten worden toegepast (het account dat wordt gebruikt door Azure AD Connect).
1. Selecteer in de vervolgkeuzelijst **Van toepassing op** de optie **Onderliggende gebruikersobjecten**.
1. Selecteer onder *Machtigingen* het selectievakje voor de volgende optie:
    * **Wachtwoord opnieuw instellen**
1. Selecteer onder *Eigenschappen* de selectievakjes voor de volgende opties. Blader door de lijst om deze opties te vinden, die mogelijk al standaard zijn ingeschakeld:
    * **Write lockoutTime**
    * **Write pwdLastSet**

    [ ![De juiste machtigingen instellen in Actieve gebruikers en computers voor het account dat wordt gebruikt door Azure AD Connect](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png) ](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Wanneer u klaar bent, selecteert u **Toepassen/OK** om de wijzigingen toe te passen en eventuele geopende dialoogvensters te sluiten.

Wanneer u machtigingen bijwerkt, kan het tot een uur of langer duren voordat deze machtigingen worden gerepliceerd naar alle objecten in uw directory.

Het wachtwoordbeleid in de on-premises AD DS-omgeving kan verhinderen dat het opnieuw instellen van het wachtwoord opnieuw op de juiste manier wordt verwerkt. Voor een efficiënte werking van het terugschrijven van wachtwoorden moet het groepsbeleid voor *Minimale wachtwoordduur* zijn ingesteld op 0. Deze instelling vindt u onder **Computerconfiguratie > Beleid > Windows-instellingen > Beveiligingsinstellingen > Accountbeleid** in `gpedit.msc`.

Wanneer u het groepsbeleid bijwerkt, wacht u totdat het bijgewerkte beleid is gerepliceerd of gebruikt u de opdracht `gpupdate /force`.

> [!Note]
> Wachtwoord terugschrijven moet zijn ingesteld op 0 om ervoor te zorgen dat wachtwoorden onmiddellijk worden gewijzigd. Als gebruikers echter voldoen aan het on-premises beleid en de *Minimale wachtwoordduur* is ingesteld op een waarde die groter is dan nul, wordt het terugschrijven van wachtwoorden nog steeds uitgevoerd nadat het on-premises beleid is geëvalueerd.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Wachtwoord terugschrijven inschakelen in Azure AD Connect

Een van de configuratieopties in Azure AD Connect is voor het terugschrijven van wachtwoorden. Wanneer deze optie is ingeschakeld, worden na wijzigingen van wachtwoorden in Azure AD Connect de bijgewerkte referenties opnieuw gesynchroniseerd naar de on-premises AD DS-omgeving.

Als u terugschrijven van self-service voor wachtwoordherstel wilt inschakelen, schakelt u eerst de optie voor terugschrijven in Azure AD Connect in. Voer vanaf de Azure AD Connect-server de volgende stappen uit:

1. Meld u aan bij de Azure AD Connect-server en start u de configuratiewizard **Azure AD Connect**.
1. Selecteer **Configureren** op de **welkomstpagina**.
1. Op de pagina **Extra taken** selecteert u **Synchronisatieopties aanpassen** en vervolgens **Volgende**.
1. Op de pagina **Verbinding maken met Azure AD** voert u de referenties van een globale beheerder voor uw Azure-tenant in en selecteert u **Volgende**.
1. Op de pagina's **Verbinding maken met directory´s** en **domein/OE filteren** selecteert u **Volgende**.
1. Op de pagina **Optionele functies** schakelt u het selectievakje in naast **Wachtwoord terugschrijven** en selecteert u **Volgende**.

    ![Azure AD Connect configureren voor wachtwoord terugschrijven](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. Op de pagina **Gereed om te configureren** selecteert u **Configureren** wacht u tot het proces is voltooid.
1. Als u ziet dat de configuratie is voltooid, selecteert u **Afsluiten**.

## <a name="enable-password-writeback-for-sspr"></a>Wachtwoord terugschrijven voor SSPR inschakelen

Wanneer terugschrijven van wachtwoorden is ingeschakeld in Azure AD Connect, moet u Azure AD SSPR configureren voor terugschrijven. Wanneer u SSPR inschakelt voor wachtwoord terugschrijven, worden bijgewerkte wachtwoorden van gebruikers die hun wachtwoord hebben gewijzigd of opnieuw ingesteld ook gesynchroniseerd met de on-premises AD DS-omgeving.

Voer de volgende stappen uit om wachtwoord terugschrijven in SSPR in te schakelen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met een globale-beheerdersaccount.
1. Zoek naar en selecteer **Azure Active Directory**, klik op **Wachtwoord opnieuw instellen** en kies vervolgens **On-premises integratie**.
1. Stel de optie **Wachtwoorden terugschrijven naar uw on-premises directory?** in op *Ja*.
1. Stel de optie **Gebruikers toestaan accounts te ontgrendelen zonder hun wachtwoord opnieuw in te stellen?** in op *Ja*.

    ![Self-service voor wachtwoordherstel voor Azure Active Directory inschakelen voor wachtwoord terugschrijven](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Selecteer **Opslaan** wanneer u klaar bent.

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet langer gebruik wilt maken van de SSPR-terugschrijffunctionaliteit die u als onderdeel van deze zelfstudie hebt geconfigureerd, voert u de volgende stappen uit:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek naar en selecteer **Azure Active Directory**, klik op **Wachtwoord opnieuw instellen** en kies vervolgens **On-premises integratie**.
1. Stel **Wachtwoorden terugschrijven naar uw on-premises directory?** in op *Nee*.
1. Stel de optie **Gebruikers toestaan accounts te ontgrendelen zonder hun wachtwoord opnieuw in te stellen?** in op *Nee*.

Als u helemaal geen wachtwoordfunctionaliteit meer wilt gebruiken, voert u de volgende stappen uit vanaf uw Azure AD Connect-server:

1. Meld u aan bij de Azure AD Connect-server en start u de configuratiewizard **Azure AD Connect**.
1. Selecteer **Configureren** op de **welkomstpagina**.
1. Op de pagina **Extra taken** selecteert u **Synchronisatieopties aanpassen** en vervolgens **Volgende**.
1. Op de pagina **Verbinding maken met Azure AD** voert u de referenties van een globale beheerder voor uw Azure-tenant in en selecteert u **Volgende**.
1. Op de pagina's **Verbinding maken met directory´s** en **domein/OE filteren** selecteert u **Volgende**.
1. Op de pagina **Optionele functies** schakelt u het selectievakje uit naast **Wachtwoord terugschrijven** en selecteert u **Volgende**.
1. Op de pagina **Gereed om te configureren** selecteert u **Configureren** wacht u tot het proces is voltooid.
1. Als u ziet dat de configuratie is voltooid, selecteert u **Afsluiten**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u terugschrijven van Azure AD SSPR naar een on-premises AD DS-omgeving ingeschakeld. U hebt geleerd hoe u:

> [!div class="checklist"]
> * De benodigde machtigingen voor het terugschrijven van wachtwoorden configureren
> * De optie Wachtwoord terugschrijven inschakelen in Azure AD Connect
> * Wachtwoord terugschrijven inschakelen in Azure AD SSPR

> [!div class="nextstepaction"]
> [Risico's beoordelen bij het aanmelden](tutorial-risk-based-sspr-mfa.md)
