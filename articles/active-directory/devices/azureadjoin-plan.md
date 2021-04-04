---
title: De implementatie van Azure Active Directory-deelname plannen
description: Hierin worden de stappen beschreven die nodig zijn voor het implementeren van aan Azure AD gekoppelde apparaten in uw omgeving.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3acaf4929158b24ff50655aa18c05b41aeec4b53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435447"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Procedure: uw Azure AD-koppelings implementatie plannen

Met Azure AD-deelname kunt u rechtstreeks lid worden van Azure AD, zonder dat u hoeft lid te worden van on-premises Active Directory terwijl uw gebruikers productief en veilig blijven. Azure AD-deelname is voor het bedrijf gereed voor implementaties op schaal en binnen het bereik.   

In dit artikel vindt u de informatie die u nodig hebt om de implementatie van Azure AD-deelname te plannen.
 
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met de [Inleiding tot Apparaatbeheer in azure Active Directory](./overview.md).

## <a name="plan-your-implementation"></a>Uw implementatie plannen

Als u de implementatie van Azure AD-deelname wilt plannen, moet u vertrouwd zijn met:

> [!div class="checklist"]
> - Uw scenario's controleren
> - Uw identiteits infrastructuur controleren
> - Uw Apparaatbeheer beoordelen
> - Aandachtspunten voor toepassingen en bronnen
> - Inzicht in uw inrichtings opties
> - Enter prise State roaming configureren
> - Voorwaardelijke toegang configureren

## <a name="review-your-scenarios"></a>Uw scenario's controleren 

Hoewel hybride deelname aan Azure AD voor bepaalde scenario's kan worden aangeraden, kunt u met Azure AD-deelname overschakelen naar een Cloud-eerste model met Windows. Als u van plan bent om uw apparaten te beheren en de IT-kosten te verlagen, biedt Azure AD-deelname een uitstekende basis om deze doel stellingen te bereiken.  
 
U moet Azure AD-deelname overwegen als uw doel stellingen worden uitgelijnd met de volgende criteria:

- U gaat Microsoft 365 als productiviteits pakket voor uw gebruikers.
- U wilt apparaten beheren met een beheer oplossing voor Cloud apparaten.
- U wilt het inrichten van apparaten vereenvoudigen voor geografisch gedistribueerde gebruikers.
- U wilt de infra structuur van uw toepassing moderniseren.

## <a name="review-your-identity-infrastructure"></a>Uw identiteits infrastructuur controleren  

Azure AD-deelname werkt met zowel beheerde als federatieve omgevingen.  

### <a name="managed-environment"></a>Beheerde omgeving

Een beheerde omgeving kan worden geïmplementeerd via een [wachtwoord-hash-synchronisatie](../hybrid/how-to-connect-password-hash-synchronization.md) of door [middel van verificatie](../hybrid/how-to-connect-pta-quick-start.md) met naadloze eenmalige aanmelding.

Voor deze scenario's is het niet nodig om een federatieve server te configureren voor authenticatie.

### <a name="federated-environment"></a>Federatieve omgeving

Een gefedereerde omgeving moet een id-provider hebben die zowel WS-Trust als WS-Fed protocollen ondersteunt:

- **WS-voeder:** Dit protocol is vereist om een apparaat toe te voegen aan Azure AD.
- **WS-Trust:** Dit protocol is vereist om u aan te melden bij een toegevoegd Azure AD-apparaat.

Wanneer u AD FS gebruikt, moet u de volgende WS-Trust-eind punten inschakelen: `/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Als uw ID-provider deze protocollen niet ondersteunt, werkt Azure AD-deelname niet systeem eigen. 

>[!NOTE]
> Azure AD join's werkt momenteel niet met [AD FS 2019, geconfigureerd met externe verificatie providers als de primaire verificatie methode](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary). Azure AD-koppeling wordt standaard ingesteld op wachtwoord verificatie als primaire methode, wat resulteert in verificatie fouten in dit scenario


### <a name="smartcards-and-certificate-based-authentication"></a>Smart Cards en verificatie op basis van certificaten

U kunt geen smart cards of verificatie op basis van certificaten gebruiken om apparaten toe te voegen aan Azure AD. Smart Cards kunnen echter worden gebruikt om u aan te melden bij apparaten die lid zijn van Azure AD als u AD FS hebt geconfigureerd.

**Aanbeveling:** Implementeer Windows hello voor bedrijven voor sterke, wacht woord-less verificatie voor Windows 10-apparaten.

### <a name="user-configuration"></a>Gebruikers configuratie

Als u gebruikers maakt in uw:

- **On-premises Active Directory** moet u deze synchroniseren met Azure AD met behulp van [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md). 
- **Azure AD**, er is geen aanvullende installatie vereist.

On-premises Upn's die verschillen van Azure AD-Upn's, worden niet ondersteund op apparaten die zijn toegevoegd aan Azure AD. Als uw gebruikers een lokale UPN gebruiken, moet u overschakelen naar het gebruik van de primaire UPN in azure AD.

UPN-wijzigingen worden alleen ondersteund bij het starten van de update voor Windows 10 2004. Gebruikers op apparaten met deze update hebben geen problemen nadat ze hun Upn's hebben gewijzigd. Voor apparaten met een update van Windows 10 2004 hebben gebruikers problemen met eenmalige aanmelding en voorwaardelijke toegang op hun apparaten. Ze moeten zich aanmelden bij Windows via de tegel ' andere gebruiker ' met hun nieuwe UPN om dit probleem op te lossen. 

## <a name="assess-your-device-management"></a>Uw Apparaatbeheer beoordelen

### <a name="supported-devices"></a>Ondersteunde apparaten

Azure AD-deelname:

- Is alleen van toepassing op Windows 10-apparaten. 
- Is niet van toepassing op eerdere versies van Windows of andere besturings systemen. Als u Windows 7/8.1-apparaten hebt, moet u een upgrade uitvoeren naar Windows 10 voor het implementeren van Azure AD-deelname.
- Wordt ondersteund voor FIPS-compatibele TPM 2,0, maar wordt niet ondersteund voor TPM 1,2. Als uw apparaten FIPS-compatibele TPM 1,2 hebben, moet u ze uitschakelen voordat u verdergaat met Azure AD-deelname. Micro soft biedt geen hulpprogram ma's voor het uitschakelen van de FIPS-modus voor Tpm's omdat deze afhankelijk is van de TPM-fabrikant. Neem contact op met uw OEM voor ondersteuning.
 
**Aanbeveling:** Gebruik altijd de nieuwste Windows 10-versie om te profiteren van bijgewerkte functies.

### <a name="management-platform"></a>Beheer platform

Apparaatbeheer voor apparaten die zijn toegevoegd aan Azure AD is gebaseerd op een MDM-platform, zoals intune, en MDM-Csp's. Windows 10 heeft een ingebouwde MDM-agent die geschikt is voor alle compatibele MDM-oplossingen.

> [!NOTE]
> Groeps beleid wordt niet ondersteund op apparaten die zijn toegevoegd aan Azure AD omdat ze niet zijn verbonden met on-premises Active Directory. Het beheer van apparaten die zijn toegevoegd aan Azure AD is alleen mogelijk via MDM

Er zijn twee benaderingen voor het beheren van apparaten die zijn toegevoegd aan Azure AD:

- **Alleen MDM** : een apparaat wordt exclusief beheerd door een MDM-provider zoals intune. Alle beleids regels worden geleverd als onderdeel van het MDM-inschrijvings proces. Voor Azure AD Premium-of EMS-klanten is MDM-registratie een automatische stap die deel uitmaakt van een Azure AD-deelname.
- **Co-beheer** : een apparaat wordt beheerd door een MDM-provider en SCCM. In deze benadering wordt de SCCM-agent geïnstalleerd op een MDM-beheerd apparaat om bepaalde aspecten te beheren.

Als u groeps beleid gebruikt, evalueert u uw GPO en MDM-beleid-pariteit met behulp van [Groepsbeleid Analytics](/mem/intune/configuration/group-policy-analytics) in micro soft Endpoint Manager. 

Bekijk ondersteunde en niet-ondersteunde beleids regels om te bepalen of u een MDM-oplossing kunt gebruiken in plaats van groeps beleid. Voor niet-ondersteunde beleids regels kunt u het volgende overwegen:

- Zijn de niet-ondersteunde beleids regels vereist voor apparaten of gebruikers die zijn toegevoegd aan Azure AD?
- Zijn de niet-ondersteunde beleids regels van toepassing op een Cloud implementatie?

Als uw MDM-oplossing niet beschikbaar is via de app-galerie van Azure AD, kunt u deze toevoegen volgens het proces dat wordt beschreven in [Azure Active Directory integratie met MDM](/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Via co-beheer kunt u SCCM gebruiken om bepaalde aspecten van uw apparaten te beheren terwijl er beleids regels worden geleverd via uw MDM-platform. Microsoft Intune kunt co-beheer met SCCM. Zie [Wat is co-beheer?](/configmgr/core/clients/manage/co-management-overview)voor meer informatie over co-beheer voor Windows 10-apparaten. Als u een ander MDM-product dan intune gebruikt, neemt u contact op met uw MDM-provider op toepasselijke scenario's voor co-beheer.

**Aanbeveling:** Overweeg alleen MDM-beheer voor apparaten die zijn toegevoegd aan Azure AD.

## <a name="understand-considerations-for-applications-and-resources"></a>Aandachtspunten voor toepassingen en bronnen

We raden u aan om toepassingen van on-premises naar de cloud te migreren voor een betere gebruikers ervaring en toegangs beheer. Apparaten die zijn toegevoegd aan Azure AD kunnen echter naadloos toegang bieden tot zowel on-premises als Cloud toepassingen. Zie [hoe SSO to on-premises resources werkt op apparaten die zijn toegevoegd aan Azure AD](azuread-join-sso.md)voor meer informatie.

De volgende secties geven een lijst van overwegingen voor verschillende soorten toepassingen en bronnen.

### <a name="cloud-based-applications"></a>Cloud toepassingen

Als een toepassing wordt toegevoegd aan de Azure AD-App-galerie, krijgen gebruikers SSO via aan Azure AD gekoppelde apparaten. Er is geen aanvullende configuratie vereist. Gebruikers krijgen SSO op zowel micro soft Edge-als Chrome-browsers. Voor Chrome moet u de [extensie voor Windows 10-accounts](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)implementeren. 

Alle Win32-toepassingen die:

- Vertrouw op web account manager (WAM) voor token aanvragen ook SSO op aan Azure AD gekoppelde apparaten. 
- Vertrouw niet op WAM kunnen gebruikers vragen om verificatie. 

### <a name="on-premises-web-applications"></a>On-premises webtoepassingen

Als uw apps aangepaste ingebouwde en/of on-premises worden gehost, moet u deze toevoegen aan de vertrouwde sites van uw browser voor het volgende:

- Geïntegreerde Windows-verificatie inschakelen 
- Geef gebruikers een niet-prompt-SSO-ervaring. 

Als u AD FS gebruikt, raadpleegt u [eenmalige aanmelding controleren en beheren met AD FS](/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Aanbeveling:** Overweeg in de cloud te hosten (bijvoorbeeld Azure) en te integreren met Azure AD voor een betere ervaring.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>On-premises toepassingen die gebruikmaken van verouderde protocollen

Gebruikers ontvangen SSO van apparaten die lid zijn van Azure AD als het apparaat toegang heeft tot een domein controller. 

**Aanbeveling:** Implementeer [Azure AD-App proxy](../manage-apps/application-proxy.md) om veilige toegang in te scha kelen voor deze toepassingen.

### <a name="on-premises-network-shares"></a>On-premises netwerk shares

Uw gebruikers hebben SSO van aan Azure AD gekoppelde apparaten wanneer een apparaat toegang heeft tot een on-premises domein controller.

### <a name="printers"></a>Printers

Voor printers moet u [hybride Cloud afdrukken](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) implementeren voor het detecteren van printers op apparaten die zijn toegevoegd aan Azure AD. 

Hoewel printers niet automatisch kunnen worden gedetecteerd in een omgeving in de Cloud, kunnen uw gebruikers ook het UNC-pad van de printer gebruiken om ze rechtstreeks toe te voegen. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>On-premises toepassingen die afhankelijk zijn van computer verificatie

Apparaten die zijn toegevoegd aan Azure AD, bieden geen ondersteuning voor on-premises toepassingen die afhankelijk zijn van computer authenticatie. 

**Aanbeveling:** Overweeg deze toepassingen buiten gebruik te stellen en te verplaatsen naar hun moderne alternatieven.

### <a name="remote-desktop-services"></a>Externe bureaubladservices

Voor verbinding met extern bureau blad met een aan Azure AD gekoppelde apparaten moet de hostcomputer lid zijn van Azure AD of lid zijn van een hybride Azure AD. Extern bureau blad vanaf een niet-samengevoegd of niet-Windows-apparaat wordt niet ondersteund. Zie [verbinding maken met een externe Azure AD-computer](/windows/client-management/connect-to-remote-aadj-pc) voor meer informatie.

Als u Windows 10 2004 Update start, kunnen gebruikers ook extern bureau blad van een Azure AD-geregistreerde Windows 10-apparaat op een aan Azure AD toegevoegd apparaat gebruiken. 

## <a name="understand-your-provisioning-options"></a>Inzicht in uw inrichtings opties
**Opmerking**: aan Azure AD gekoppelde apparaten kunnen niet worden geïmplementeerd met het hulp programma voor systeem voorbereiding (Sysprep) of vergelijk bare Imaging-hulpprogram ma's

U kunt Azure AD-deelname inrichten met behulp van de volgende methoden:

- **Selfservice in OOBE/instellingen** : in de selfservice modus gaan gebruikers door met het Azure AD-deelname proces tijdens Windows out of Box Experience (OOBE) of Windows-instellingen. Zie [uw werk apparaat lid maken van het netwerk van uw organisatie](../user-help/user-help-join-device-on-network.md)voor meer informatie. 
- **Windows auto pilot** : met Windows Automatische pilot kunt u apparaten vooraf configureren voor een probleemloze ervaring in OOBE voor het uitvoeren van een Azure AD-deelname. Zie voor meer informatie het [overzicht van Windows auto pilot](/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Bulk inschrijving** : bij bulk registratie kan een beheerder een Azure AD-deelname door middel van een bulk inrichting hulp programma gebruiken om apparaten te configureren. Zie [bulk Registratie voor Windows-apparaten](/intune/windows-bulk-enroll)voor meer informatie.
 
Hier volgt een vergelijking van deze drie benaderingen 
 
| Element | Self-service instellen | Windows Autopilot | Bulkinschrijving |
| --- | --- | --- | --- |
| Gebruikers interactie vereist voor het instellen van | Ja | Ja | Nee |
| IT-inspanningen vereisen | Nee | Ja | Ja |
| Toepasselijke stromen | OOBE-&-instellingen | Alleen OOBE | Alleen OOBE |
| Lokale beheerdersrechten voor de primaire gebruiker | Standaard ja, | Configureerbaar | Nee |
| OEM-ondersteuning van apparaat vereisen | Nee | Ja | Nee |
| Ondersteunde versies | 1511 + | 1709 + | 1703 + |
 
Kies uw implementatie benadering of benaderingen door de bovenstaande tabel te controleren en de volgende aandachtspunten voor het aannemen van een van beide benaderingen te controleren:  

- Zijn uw gebruikers technisch gebruik om de instellingen zelf door te lopen? 
   - Self-service kan het beste worden gebruikt voor deze gebruikers. Overweeg Windows auto pilot om de gebruikers ervaring te verbeteren.  
- Zijn uw gebruikers op afstand of binnen bedrijfs locatie? 
   - Self-service of auto pilot werkt het beste voor externe gebruikers voor een probleemloze installatie. 
- Krijgt u de voor keur aan een door de gebruiker gestuurde of door de beheerder beheerde configuratie? 
   - Bulk inschrijving werkt beter voor door de beheerder gestuurde implementatie om apparaten in te stellen voordat ze aan de gebruikers worden door gegeven.     
- Koopt u apparaten van 1-2 OEM'S of hebt u een brede distributie van OEM-apparaten?  
   - Als u een product koopt van beperkte Oem's die ook automatische pilot ondersteunen, kunt u profiteren van de nauwere integratie met auto pilot. 

## <a name="configure-your-device-settings"></a>Apparaatinstellingen configureren

Met de Azure Portal kunt u de implementatie van aan Azure AD gekoppelde apparaten in uw organisatie beheren. Als u de gerelateerde instellingen wilt configureren, selecteert u op de **pagina Azure Active Directory** `Devices > Device settings` .

### <a name="users-may-join-devices-to-azure-ad"></a>Gebruikers mogen apparaten aan Azure AD toevoegen

Stel deze optie in op **Alles** of **geselecteerd** op basis van het bereik van uw implementatie en voor wie u een Azure AD-apparaat wilt instellen dat kan worden toegevoegd. 

![Gebruikers mogen apparaten aan Azure AD toevoegen](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Extra lokale beheerders voor apparaten die zijn toegevoegd aan Azure AD

Kies **geselecteerd** en selecteer de gebruikers die u wilt toevoegen aan de lokale groep Administrators op alle aan Azure AD gekoppelde apparaten. 

![Extra lokale beheerders voor apparaten die zijn toegevoegd aan Azure AD](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Multi-factor Authentication vereisen voor het toevoegen van apparaten

Selecteer **Ja** als u gebruikers wilt verplichten MFA uit te voeren bij het toevoegen van apparaten aan Azure AD. Voor de gebruikers die apparaten toevoegen aan Azure AD via MFA, wordt het apparaat zelf een 2e factor.

![Multi-factor Authentication vereisen voor het toevoegen van apparaten](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Uw mobiliteits instellingen configureren

Voordat u uw mobiliteits instellingen kunt configureren, moet u mogelijk eerst een MDM-provider toevoegen.

**Een MDM-provider toevoegen**:

1. Klik op de **pagina Azure Active Directory** in de sectie **beheren** op `Mobility (MDM and MAM)` . 
1. Klik op **toepassing toevoegen**.
1. Selecteer uw MDM-provider in de lijst.

   :::image type="content" source="./media/azureadjoin-plan/04.png" alt-text="Scherm afbeelding van de Azure Active Directory een toepassings pagina toe te voegen. Er worden verschillende M D M-providers weer gegeven." border="false":::

Selecteer uw MDM-provider om de gerelateerde instellingen te configureren. 

### <a name="mdm-user-scope"></a>Gebruikersbereik van MDM

Selecteer **een aantal** of **Alles** op basis van het bereik van uw implementatie. 

![Gebruikersbereik van MDM](./media/azureadjoin-plan/05.png)

Op basis van uw bereik gebeurt een van de volgende situaties: 

- **Gebruiker bevindt zich in MDM-bereik**: als u een Azure AD Premium abonnement hebt, wordt MDM-inschrijving samen met Azure AD-deelname geautomatiseerd. Alle gebruikers met een bereik moeten een juiste licentie voor uw MDM hebben. Als MDM-inschrijving in dit scenario mislukt, wordt de Azure AD-koppeling ook teruggedraaid.
- **Gebruiker bevindt zich niet in MDM-bereik**: als gebruikers zich niet in MDM-bereik bevinden, wordt Azure AD-deelname voltooid zonder MDM-registratie. Dit resulteert in een onbeheerd apparaat.

### <a name="mdm-urls"></a>MDM-URL's

Er zijn drie Url's die betrekking hebben op uw MDM-configuratie:

- URL voor MDM-gebruiksvoorwaarden
- URL voor MDM-detectie 
- URL voor MDM-naleving

:::image type="content" source="./media/azureadjoin-plan/06.png" alt-text="Scherm afbeelding van een deel van de configuratie van Azure Active Directory M D M, met U R L-velden voor de gebruiks voorwaarden van M D M, detectie en naleving." border="false":::

Elke URL heeft een vooraf gedefinieerde standaardwaarde. Als deze velden leeg zijn, neemt u contact op met uw MDM-provider voor meer informatie.

### <a name="mam-settings"></a>MAM-instellingen

MAM is niet van toepassing op Azure AD-deelname. 

## <a name="configure-enterprise-state-roaming"></a>Enter prise State roaming configureren

Als u status roaming naar Azure AD wilt inschakelen zodat gebruikers hun instellingen op verschillende apparaten kunnen synchroniseren, raadpleegt u [Enterprise State roaming inschakelen in azure Active Directory](enterprise-state-roaming-enable.md). 

**Aanbeveling**: Schakel deze instelling in, zelfs voor hybride apparaten die aan Azure AD zijn toegevoegd.

## <a name="configure-conditional-access"></a>Voorwaardelijke toegang configureren

Als u een MDM-provider hebt geconfigureerd voor uw aan Azure AD gekoppelde apparaten, markeert de provider het apparaat als compatibel zodra het apparaat wordt beheerd. 

![Compatibel apparaat](./media/azureadjoin-plan/46.png)

U kunt deze implementatie gebruiken om [beheerde apparaten te vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een nieuw Windows 10-apparaat toevoegen aan Azure ad tijdens een eerste uitvoering](azuread-joined-devices-frx.md) 
>  [Uw werk apparaat toevoegen aan het netwerk van uw organisatie](../user-help/user-help-join-device-on-network.md)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
