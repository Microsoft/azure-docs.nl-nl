---
title: Deelname aan een Hybrid Azure Active Directory-Azure Active Directory plannen
description: Lees hoe u hybride Azure Active Directory-gekoppelde apparaten kunt configureren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c4ed5dfee80c33009874361ae6b4d23ec00bc26
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573327"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Procedure: de implementatie van uw hybride Azure Active Directory-koppeling plannen

Op een vergelijk bare manier als een gebruiker is een apparaat een andere core-identiteit die u wilt beveiligen en gebruiken om uw resources op elk gewenst moment en vanaf elke locatie te beveiligen. Met een van de volgende methoden kunt u dit doel bereiken door apparaat-id's in azure AD te maken en beheren:

- Azure AD-koppeling
- Hybride Azure AD-koppeling
- Azure AD-registratie

Door uw apparaten naar Azure AD te brengen, optimaliseert u de productiviteit van uw gebruikers via eenmalige aanmelding (SSO) bij al uw on-premises bronnen en cloudbronnen. Op hetzelfde moment kunt u de toegang tot uw Cloud-en on-premises resources beveiligen met [voorwaardelijke toegang](../conditional-access/overview.md).

Als u een on-premises Active Directory-omgeving (AD) hebt en u uw AD-domein computers wilt toevoegen aan Azure AD, kunt u dit doen door hybride Azure AD join uit te voeren. In dit artikel vindt u de gerelateerde stappen voor het implementeren van een hybride Azure AD-koppeling in uw omgeving. 

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met de [Inleiding tot apparaat-id-beheer in azure Active Directory](./overview.md).

> [!NOTE]
> De mini maal vereiste domein controller versie voor Windows 10 Hybrid Azure AD join's is Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Uw implementatie plannen

Als u uw hybride implementatie van Azure AD wilt plannen, moet u vertrouwd zijn met:

> [!div class="checklist"]
> - Ondersteunde apparaten controleren
> - Bekijk de dingen die u moet weten
> - Gecontroleerde validatie van hybride Azure AD-deelname controleren
> - Selecteer uw scenario op basis van uw identiteits infrastructuur
> - On-premises AD-UPN-ondersteuning voor hybride Azure AD-deelname controleren

## <a name="review-supported-devices"></a>Ondersteunde apparaten controleren

Hybride Azure AD-deelname ondersteunt een breed scala aan Windows-apparaten. Omdat de configuratie voor apparaten met oudere versies van Windows extra of verschillende stappen vereist, worden de ondersteunde apparaten gegroepeerd in twee categorieën:

### <a name="windows-current-devices"></a>Windows-huidige apparaten

- Windows 10
- Windows Server 2016
  - **Opmerking**: Azure National Cloud-klanten hebben versie 1803
- Windows Server 2019

Voor apparaten met het Windows-besturings systeem wordt de ondersteunde versie vermeld in dit artikel [Windows 10 release-informatie](/windows/release-information/). Als best practice, raadt micro soft u aan om te upgraden naar de nieuwste versie van Windows 10.

### <a name="windows-down-level-devices"></a>Windows-apparaten op lagere niveaus

- Windows 8.1
- Windows 7-ondersteuning is beëindigd op 14 januari 2020. Zie [ondersteuning voor Windows 7 is beëindigd](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)voor meer informatie.
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Zie voor ondersteunings informatie over Windows Server 2008 en 2008 R2 [voor bereiding op Windows server 2008-ondersteuning](https://www.microsoft.com/cloud-platform/windows-server-2008).

Als eerste plannings stap moet u uw omgeving controleren en bepalen of u apparaten van het lagere niveau van Windows moet ondersteunen.

## <a name="review-things-you-should-know"></a>Bekijk de dingen die u moet weten

### <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's
- Hybride Azure AD-deelname wordt momenteel niet ondersteund als uw omgeving bestaat uit één AD-forest dat identiteits gegevens synchroniseert met meer dan één Azure AD-Tenant.

- Hybride Azure AD-samen voeging wordt niet ondersteund voor Windows Server met de rol domein controller (DC).

- Hybride Azure AD-samen voeging wordt niet ondersteund op Windows-apparaten op een lager niveau wanneer u Credential roaming of roaming tussen gebruikers profielen of een verplicht profiel gebruikt.

- Server Core-besturings systeem biedt geen ondersteuning voor elk type apparaatregistratie.

- Hulpprogramma voor migratie van gebruikersstatus (USMT) werkt niet met apparaatregistratie.  

### <a name="os-imaging-considerations"></a>Aandachtspunten voor installatie van besturings systeem
- Als u afhankelijk bent van het hulp programma voor systeem voorbereiding (Sysprep) en als u een installatie kopie van **vóór Windows 10 1809** gebruikt, moet u ervoor zorgen dat de installatie kopie niet afkomstig is van een apparaat dat al is geregistreerd bij Azure AD als hybride Azure AD-deelname.

- Als u een moment opname van een virtuele machine (VM) vertrouwt om extra Vm's te maken, moet u ervoor zorgen dat de moment opname niet afkomstig is van een VM die al is geregistreerd bij Azure AD als hybride Azure AD-deelname.

- Als u gebruikmaakt van [Unified Write Filter](/windows-hardware/customize/enterprise/unified-write-filter) en soort gelijke technologieën die wijzigingen in de schijf bij het opnieuw opstarten wissen, moeten ze worden toegepast nadat het apparaat is toegevoegd aan hybride Azure AD. Als u dergelijke technologieën inschakelt voordat hybride Azure AD-deelname is voltooid, wordt het apparaat tijdens elke herstart ontkoppeld

### <a name="handling-devices-with-azure-ad-registered-state"></a>Apparaten afhandelen met Azure AD-status geregistreerd
Als uw Windows 10-domein aangesloten apparaten zijn die zijn [geregistreerd](overview.md#getting-devices-in-azure-ad) bij uw Tenant, kan dit leiden tot een dubbele status van hybride Azure AD join en Azure AD geregistreerd apparaat. U wordt aangeraden om een upgrade uit te voeren naar Windows 10 1803 (met KB4489894 toegepast) of hoger om dit scenario automatisch te verhelpen. In versies van vóór 1803 moet u de Azure AD-status geregistreerd hand matig verwijderen voordat u hybride deelname van Azure AD inschakelt. In 1803 en hoger releases zijn de volgende wijzigingen aangebracht om deze dubbele status te voor komen:

- Alle bestaande Azure AD-geregistreerde statussen voor een gebruiker worden automatisch verwijderd <i>nadat het apparaat is toegevoegd aan hybride Azure AD en dezelfde gebruiker zich aanmeldt</i>. Als gebruiker A bijvoorbeeld een status van Azure AD heeft geregistreerd op het apparaat, wordt de dubbele status voor gebruiker A alleen opgeschoond wanneer gebruikers zich aanmelden bij het apparaat. Als er meerdere gebruikers op hetzelfde apparaat zijn, wordt de dubbele status afzonderlijk opgeruimd wanneer deze gebruikers zich aanmelden. Naast het verwijderen van de Azure AD-status geregistreerd, zal Windows 10 ook de registratie van het apparaat bij intune of andere MDM ongedaan maken, als de inschrijving is gebeurd als onderdeel van de Azure AD-registratie via automatische inschrijving.
- U kunt voor komen dat uw domein dat lid is van Azure AD, wordt geregistreerd door de volgende register waarde toe te voegen aan HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin" = dword: 00000001.
- Als u Windows hello voor bedrijven hebt geconfigureerd in Windows 10 1803, moet de gebruiker Windows hello voor bedrijven opnieuw instellen na het opschonen van de dubbele status. Dit probleem is opgelost met KB4512509

> [!NOTE]
> Hoewel Windows 10 automatisch de Azure AD-status geregistreerd lokaal verwijdert, wordt het apparaatobject in azure AD niet onmiddellijk verwijderd als het wordt beheerd door intune. U kunt het verwijderen van de geregistreerde status van Azure AD valideren door dsregcmd/status uit te voeren en te controleren of het apparaat niet als Azure AD is geregistreerd op basis van dat.

### <a name="additional-considerations"></a>Aanvullende overwegingen
- Als uw omgeving gebruikmaakt van Virtual Desktop Infrastructure (VDI), raadpleegt u [apparaat-id en desktop-virtualisatie](./howto-device-identity-virtual-desktop-infrastructure.md).

- Hybride Azure AD-deelname wordt ondersteund voor FIPS-compatibele TPM 2,0 en wordt niet ondersteund voor TPM 1,2. Als uw apparaten FIPS-compatibele TPM 1,2 hebben, moet u ze uitschakelen voordat u verdergaat met hybride Azure AD-deelname. Micro soft biedt geen hulpprogram ma's voor het uitschakelen van de FIPS-modus voor Tpm's omdat deze afhankelijk is van de TPM-fabrikant. Neem contact op met uw OEM voor ondersteuning. 

- Vanaf de release van Windows 10 1903 worden Tpm's 1,2 niet gebruikt in combi natie met hybride Azure AD-deelname en-apparaten met die Tpm's. dit wordt beschouwd als als ze geen TPM hebben.

- UPN-wijzigingen worden alleen ondersteund bij het starten van de update voor Windows 10 2004. Voor apparaten met een update van Windows 10 2004 hebben gebruikers problemen met eenmalige aanmelding en voorwaardelijke toegang op hun apparaten. Om dit probleem op te lossen, moet u het apparaat uit Azure AD loskoppelen (Voer ' dsregcmd/leave ' uit met verhoogde bevoegdheden) en opnieuw lid worden (automatisch uitgevoerd). Gebruikers die zich aanmelden met Windows hello voor bedrijven, hebben echter niet te maken met dit probleem.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Gecontroleerde validatie van hybride Azure AD-deelname controleren

Wanneer alle vereisten aanwezig zijn, worden Windows-apparaten automatisch geregistreerd als apparaten in uw Azure AD-Tenant. De status van deze apparaat-id's in azure AD wordt aangeduid als hybride Azure AD-deelname. Meer informatie over de concepten die in dit artikel worden behandeld, vindt u in het artikel [Inleiding tot apparaat-id-beheer in azure Active Directory](overview.md).

Organisaties willen mogelijk een gecontroleerde validatie van hybride Azure AD-deelname uitvoeren voordat ze in hun hele organisatie allemaal tegelijk worden ingeschakeld. Bekijk het artikel [gecontroleerde validatie van hybride Azure AD-deelname](hybrid-azuread-join-control.md) om te begrijpen hoe u dit kunt doen.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selecteer uw scenario op basis van uw identiteits infrastructuur

Hybride Azure AD-deelname werkt met zowel beheerde als federatieve omgevingen, afhankelijk van het feit of de UPN routeerbaar of niet-routeerbaar is. Zie de onderkant van de pagina voor de tabel op ondersteunde scenario's.  

### <a name="managed-environment"></a>Beheerde omgeving

Een beheerde omgeving kan worden geïmplementeerd via een [PHS (Password Hash Sync)](../hybrid/whatis-phs.md) of door [middel van verificatie (PTA)](../hybrid/how-to-connect-pta.md) met [naadloze eenmalige aanmelding](../hybrid/how-to-connect-sso.md).

Voor deze scenario's is het niet nodig om een federatieve server te configureren voor authenticatie.

> [!NOTE]
> [Cloud verificatie met behulp van gefaseerde implementatie](../hybrid/how-to-connect-staged-rollout.md) wordt alleen ondersteund bij het starten van Windows 10 1903 update

### <a name="federated-environment"></a>Federatieve omgeving

Een gefedereerde omgeving moet een id-provider hebben die de volgende vereisten ondersteunt. Als u een gefedereerde omgeving hebt met Active Directory Federation Services (AD FS), worden de onderstaande vereisten al ondersteund.

- **WIAORMULTIAUTHN-claim:** Deze claim is verwijst voor een hybride Azure AD-koppeling oplossen voor downlevel Windows-apparaten.
- **WS-Trust-protocol:** Dit protocol is vereist voor de verificatie van Windows Current Hybrid Azure AD gekoppelde apparaten met Azure AD. Wanneer u AD FS gebruikt, moet u de volgende WS-Trust-eind punten inschakelen: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **ADFS/Services/Trust/2005/windowstransport** of **ADFS/Services/Trust/13/windowstransport** moeten alleen worden ingeschakeld als intranet gerichte eind punten en mogen niet worden weer gegeven als een extranet gerichte eind punten via de Web Application proxy. Zie voor meer informatie over het uitschakelen van WS-Trust Windows eindpunten [Disable WS-Trust Windows endpoints on the proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Onder **Service** > **Eindpunten** in de AD FS-beheerconsole kunt u zien welke eindpunten zijn ingeschakeld.

> [!NOTE]
> Azure AD biedt geen ondersteuning voor Smart Cards of certificaten in beheerde domeinen.

Vanaf versie 1.1.819.0 bevat Azure AD Connect een wizard om hybride Azure AD-koppeling te configureren. Met de wizard kunt u het configuratieproces aanzienlijk vereenvoudigen. Als het installeren van de vereiste versie van Azure AD Connect geen optie voor u is, raadpleegt u [apparaatregistratie hand matig configureren](hybrid-azuread-join-manual.md). 

Ga op basis van het scenario dat overeenkomt met uw identiteits infrastructuur naar:

- [Hybride Azure Active Directory-koppeling configureren voor federatieve omgeving](hybrid-azuread-join-federated-domains.md)
- [Hybride Azure Active Directory-deelname configureren voor een beheerde omgeving](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>De UPN-ondersteuning van on-premises AD-gebruikers voor hybride Azure AD-deelname controleren

Soms kunnen de UPN-namen van uw on-premises AD-gebruikers afwijken van uw Azure AD-Upn's. In dergelijke gevallen biedt Windows 10 Hybrid Azure AD-deelname beperkte ondersteuning voor on-premises AD-Upn's op basis van de [verificatie methode](../hybrid/choose-ad-authn.md), het domein type en de Windows 10-versie. Er zijn twee typen on-premises AD-Upn's die in uw omgeving kunnen bestaan:

- Gebruikers-omleid bare UPN: een routeerbaar UPN heeft een geldig geverifieerd domein dat is geregistreerd bij een domein registratie service. Als contoso.com bijvoorbeeld het primaire domein in azure AD is, is contoso.org het primaire domein in on-premises AD eigendom van Contoso en wordt het [geverifieerd in azure AD](../fundamentals/add-custom-domain.md)
- UPN van niet-routeerbaar gebruikers: een niet-routeerbaare UPN heeft geen geverifieerd domein. Het is alleen van toepassing op het particuliere netwerk van uw organisatie. Als contoso.com bijvoorbeeld het primaire domein is in azure AD, contoso. local is het primaire domein in on-premises AD, maar dit is geen verifieerbaar domein op internet en wordt alleen gebruikt in het netwerk van contoso.

> [!NOTE]
> De informatie in deze sectie is alleen van toepassing op de UPN van een lokale gebruiker. Het is niet van toepassing op een on-premises domein achtervoegsel voor computers (bijvoorbeeld: Computer1. contoso. local).

De onderstaande tabel bevat gedetailleerde informatie over de ondersteuning voor deze on-premises AD-Upn's in Windows 10 Hybrid Azure AD-deelname

| Type on-premises AD-UPN | Domeintype | Windows 10-versie | Description |
| ----- | ----- | ----- | ----- |
| Bare | Federatief | Van 1703 release | Algemeen beschikbaar |
| Niet-routeerbaar | Federatief | Van 1803 release | Algemeen beschikbaar |
| Bare | Beheerd | Van 1803 release | Azure AD SSPR op Windows-vergrendelings scherm is algemeen beschikbaar. |
| Niet-routeerbaar | Beheerd | Niet ondersteund | |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Hybride Azure Active Directory-koppeling configureren voor federatieve omgeving](hybrid-azuread-join-federated-domains.md) 
>  [Hybride Azure Active Directory-deelname configureren voor een beheerde omgeving](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
