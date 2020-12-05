---
title: Wachtwoord beleid maken en gebruiken in Azure AD Domain Services | Microsoft Docs
description: Meer informatie over hoe en waarom u een nauw keurig wachtwoord beleid kunt gebruiken voor het beveiligen en beheren van wacht woorden in een Azure AD DS beheerd domein.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: df132af1675b3f373fe1eab5685c5d2f07813445
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619229"
---
# <a name="password-and-account-lockout-policies-on-azure-active-directory-domain-services-managed-domains"></a>Wacht woord-en account vergrendelings beleid voor Azure Active Directory Domain Services beheerde domeinen

Als u de beveiliging van gebruikers in Azure Active Directory Domain Services (Azure AD DS) wilt beheren, kunt u verfijnde wachtwoord beleidsregels definiëren voor het beheren van account vergrendelings instellingen of minimale wachtwoord lengte en complexiteit. Er wordt een standaard beleid voor verfijnd gekorreld wacht woord gemaakt en toegepast op alle gebruikers in een door Azure AD DS beheerd domein. Om nauw keurige controle te bieden en te voldoen aan specifieke bedrijfs-of nalevings behoeften, kunnen extra beleids regels worden gemaakt en toegepast op specifieke groepen gebruikers.

In dit artikel wordt beschreven hoe u met behulp van de Active Directory-beheercentrum een nauw keurig wachtwoord beleid in azure AD DS maakt en configureert.

> [!NOTE]
> Wachtwoord beleid is alleen beschikbaar voor beheerde domeinen die zijn gemaakt met het Resource Manager-implementatie model. Voor oudere beheerde domeinen die zijn gemaakt met klassiek, [migreert u vanuit het klassieke virtuele netwerk model naar Resource Manager][migrate-from-classic].

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources en bevoegdheden nodig om dit artikel te volt ooien:

* Een actief Azure-abonnement.
  * Als u nog geen Azure-abonnement hebt, [maakt u een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Een Azure Active Directory-tenant die aan uw abonnement is gekoppeld, gesynchroniseerd met een on-premises map of een cloudmap.
  * [Maak zo nodig een Azure Active Directory-tenant][create-azure-ad-tenant] of [koppel een Azure-abonnement aan uw account][associate-azure-ad-tenant].
* Een door Azure Active Directory Domain Services beheerd domein dat in uw Azure AD-tenant is ingeschakeld en geconfigureerd.
  * Als dat nodig is, voltooit u de zelf studie voor het [maken en configureren van een Azure Active Directory Domain Services beheerd domein][create-azure-ad-ds-instance].
  * Het beheerde domein moet zijn gemaakt met behulp van het Resource Manager-implementatie model. Migreer, indien nodig, [van het klassieke virtuele netwerk model naar Resource Manager][migrate-from-classic].
* Een Windows Server Management-VM die is gekoppeld aan het beheerde domein.
  * Als dat nodig is, voltooit u de zelf studie voor het [maken van een beheer-VM][tutorial-create-management-vm].
* Een gebruikersaccount dat lid is van de *Azure AD DC-beheerdersgroep* in uw Azure AD-tenant.

## <a name="default-password-policy-settings"></a>Standaard instellingen voor wachtwoord beleid

Met een verfijnd wachtwoord beleid (FGPPs) kunt u specifieke beperkingen voor wacht woord-en account vergrendelings beleid Toep assen op verschillende gebruikers in een domein. Als u bijvoorbeeld accounts met bevoegdheden wilt beveiligen, kunt u strengere account vergrendelings instellingen Toep assen dan gewone accounts zonder privileges. U kunt meerdere FGPPs binnen een beheerd domein maken en de volg orde van prioriteit opgeven om ze toe te passen op gebruikers.

Raadpleeg de volgende artikelen voor meer informatie over wachtwoord beleid en het gebruik van het Active Directory-beheer centrum:

* [Meer informatie over het beleid voor een verfijnd wacht woord](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Verfijnd wachtwoord beleid configureren met het AD-beheer centrum](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

Beleids regels worden gedistribueerd via een groeps koppeling in een beheerd domein. alle wijzigingen die u aanbrengt, worden toegepast bij de volgende aanmelding van de gebruiker. Als u het beleid wijzigt, wordt een gebruikers account dat al is vergrendeld, niet ontgrendeld.

Wachtwoord beleid gedraagt zich enigszins anders, afhankelijk van de manier waarop de gebruikers account is gemaakt. Er zijn twee manieren om een gebruikers account te maken in azure AD DS:

* Het gebruikers account kan worden gesynchroniseerd vanuit Azure AD. Dit geldt ook voor Cloud gebruikers accounts die rechtstreeks in azure zijn gemaakt, en hybride gebruikers accounts die zijn gesynchroniseerd vanuit een on-premises AD DS omgeving met behulp van Azure AD Connect.
    * Het meren deel van gebruikers accounts in azure AD DS worden gemaakt via het synchronisatie proces van Azure AD.
* Het gebruikers account kan hand matig worden gemaakt in een beheerd domein en bestaat niet in azure AD.

Alle gebruikers, ongeacht hoe ze zijn gemaakt, hebben de volgende beleids regels voor account vergrendeling die worden toegepast door het standaard wachtwoord beleid in azure AD DS:

* **Account vergrendelings duur:** 30
* **Aantal mislukte aanmeldings pogingen toegestaan:** 5
* **Aantal mislukte aanmeldings pogingen opnieuw instellen na:** 30 minuten
* **Maximale wachtwoord duur (levens duur):** 90 dagen

Met deze standaard instellingen worden gebruikers accounts gedurende 30 minuten geblokkeerd als vijf ongeldige wacht woorden binnen twee minuten worden gebruikt. Accounts worden na 30 minuten automatisch ontgrendeld.

Account vergrendelingen worden alleen uitgevoerd binnen het beheerde domein. Gebruikers accounts worden alleen vergrendeld in azure AD DS en alleen door mislukte aanmeldings pogingen voor het beheerde domein. Gebruikers accounts die zijn gesynchroniseerd vanuit Azure AD of on-premises, worden niet vergrendeld in hun bron mappen, alleen in azure AD DS.

Als u een Azure AD-wachtwoord beleid hebt waarmee een maximale wachtwoord duur van meer dan 90 dagen wordt opgegeven, wordt de wachtwoord duur toegepast op het standaard beleid in azure AD DS. U kunt een aangepast wachtwoord beleid configureren om een andere maximale wachtwoord duur in azure AD DS te definiëren. Zorg ervoor dat u een korter maximale wachtwoord duur hebt geconfigureerd in een Azure AD DS wachtwoord beleid dan in azure AD of een on-premises AD DS omgeving. In dat geval kan het wacht woord van een gebruiker verlopen in azure AD DS voordat wordt gevraagd om te wijzigen in azure AD of een on-premises AD DS omgeving.

Voor gebruikers accounts die hand matig zijn gemaakt in een beheerd domein, worden de volgende aanvullende wachtwoord instellingen ook toegepast vanuit het standaard beleid. Deze instellingen zijn niet van toepassing op gebruikers accounts die zijn gesynchroniseerd vanuit Azure AD, omdat een gebruiker hun wacht woord niet rechtstreeks kan bijwerken in azure AD DS.

* **Minimale wachtwoord lengte (tekens):** 7
* **Wacht woorden moeten voldoen aan complexiteits vereisten**

U kunt de instellingen voor account vergrendeling en het wacht woord niet wijzigen in het standaard wachtwoord beleid. In plaats daarvan kunnen leden van de groep *Aad DC-Administrators* aangepaste wachtwoord beleid maken en deze configureren voor onderdrukking (prioriteit hebben boven) het standaard ingebouwde beleid, zoals wordt weer gegeven in de volgende sectie.

## <a name="create-a-custom-password-policy"></a>Een aangepast wachtwoord beleid maken

Bij het bouwen en uitvoeren van toepassingen in azure, wilt u mogelijk een aangepast wachtwoord beleid configureren. U kunt bijvoorbeeld een beleid maken om verschillende beleids instellingen voor account vergrendeling in te stellen.

Aangepaste wachtwoord beleid wordt toegepast op groepen in een beheerd domein. Met deze configuratie wordt het standaard beleid overschreven.

Als u een aangepast wachtwoord beleid wilt maken, gebruikt u de Active Directory-beheer Programma's van een VM die lid is van een domein. Met de Active Directory-beheercentrum kunt u bronnen weer geven, bewerken en maken in een beheerd domein, met inbegrip van organisatie-eenheden.

> [!NOTE]
> Als u een aangepast wachtwoord beleid in een beheerd domein wilt maken, moet u zijn aangemeld bij een gebruikers account dat lid is van de groep *Aad DC-Administrators* .

1. Selecteer in het Start scherm de optie **systeem beheer**. Er wordt een lijst met beschik bare beheer hulpprogramma's weer gegeven die in de zelf studie zijn geïnstalleerd om [een beheer-VM te maken][tutorial-create-management-vm].
1. Als u organisatie-eenheden wilt maken en beheren, selecteert u **Active Directory-beheercentrum** in de lijst met beheer Programma's.
1. Kies in het linkerdeel venster uw beheerde domein, zoals *aaddscontoso.com*.
1. Open de **systeem** container en vervolgens de **Password Settings Container**.

    Er wordt een ingebouwd wachtwoord beleid voor het beheerde domein weer gegeven. U kunt dit ingebouwde beleid niet wijzigen. Maak in plaats daarvan een aangepast wachtwoord beleid om het standaard beleid te onderdrukken.

    ![Een wachtwoord beleid maken in de Active Directory-beheercentrum](./media/password-policy/create-password-policy-adac.png)

1. Selecteer in het deel venster **taken** aan de rechter kant **nieuwe > wachtwoord instellingen**.
1. Voer in het dialoog venster **wacht woord instellingen maken** een naam in voor het beleid, zoals *MyCustomFGPP*.
1. Wanneer er meerdere wachtwoord beleidsregels bestaan, wordt het beleid met de hoogste prioriteit of prioriteit toegepast op een gebruiker. Hoe lager het getal, des te hoger de prioriteit. Het standaard wachtwoord beleid heeft een prioriteit van *200*.

    Stel de prioriteit voor uw aangepaste wachtwoord beleid in om de standaard waarde te overschrijven, zoals *1*.

1. Bewerk andere instellingen voor wachtwoord beleid naar wens. Onthoud de volgende belang rijke punten:

    * Instellingen zoals wachtwoord complexiteit, leeftijd of verloop tijd alleen voor gebruikers die hand matig zijn gemaakt in een beheerd domein.
    * Account vergrendelings instellingen zijn van toepassing op alle gebruikers, maar worden alleen van kracht binnen het beheerde domein en niet in azure AD zelf.

    ![Een aangepast beleid voor een verfijnd wacht woord maken](./media/password-policy/custom-fgpp.png)

1. Schakel **beveiligen tegen onopzettelijk verwijderen uit**. Als deze optie is geselecteerd, kunt u de FGPP niet opslaan.
1. Selecteer in de sectie **direct van toepassing op** de knop **toevoegen** . Selecteer in het dialoog venster **gebruikers of groepen selecteren** de knop **locaties** .

    ![Selecteer de gebruikers en groepen waarop u het wachtwoord beleid wilt Toep assen](./media/password-policy/fgpp-applies-to.png)

1. Wachtwoord beleid kan alleen worden toegepast op groepen. Vouw in het dialoog venster **locaties** de domein naam uit, zoals *aaddscontoso.com*, en selecteer vervolgens een OE, zoals **AADDC-gebruikers**. Als u een aangepaste OE hebt die een groep gebruikers bevat die u wilt Toep assen, selecteert u die organisatie-eenheid.

    ![Selecteer de organisatie-eenheid waarvan de groep deel uitmaakt](./media/password-policy/fgpp-container.png)

1. Typ de naam van de groep waarop u het beleid wilt Toep assen en selecteer vervolgens **Namen controleren** om te controleren of de groep bestaat.

    ![Zoek en selecteer de groep waarop u FGPP wilt Toep assen](./media/password-policy/fgpp-apply-group.png)

1. Selecteer op **OK** de naam van de groep die u hebt geselecteerd in het vak **rechtstreeks van toepassing op** om uw aangepaste wachtwoord beleid op te slaan.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over wachtwoord beleid en het gebruik van het Active Directory-beheer centrum:

* [Meer informatie over het beleid voor een verfijnd wacht woord](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10))
* [Verfijnd wachtwoord beleid configureren met het AD-beheer centrum](/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[migrate-from-classic]: migrate-from-classic-vnet.md
