---
title: Azure Active Directory Pass-Through-verificatie beveiliging dieper uit | Microsoft Docs
description: In dit artikel wordt beschreven hoe de Pass-Through-verificatie van Azure Active Directory (Azure AD) uw on-premises accounts beschermt
services: active-directory
keywords: Azure AD Connect Pass-Through-verificatie, installeren van Active Directory, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a73c2b1be4b17136ba19e7efb71c2b21359fdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280142"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Uitgebreide kennis van Pass-Through-verificatie Azure Active Directory

In dit artikel vindt u een gedetailleerde beschrijving van de werking van Pass-Through-verificatie van Azure Active Directory (Azure AD). Het richt zich op de beveiligings aspecten van de functie. Dit artikel is van toepassing op beveiligings-en IT-beheerders, Chief-nalevings-en beveiligings ambtenaren en andere IT-professionals die verantwoordelijk zijn voor IT-beveiliging en naleving van kleine tot middel grote organisaties of grote ondernemingen.

De besproken onderwerpen zijn onder andere:
- Gedetailleerde technische informatie over het installeren en registreren van de verificatie agenten.
- Gedetailleerde technische informatie over de versleuteling van wacht woorden tijdens het aanmelden van gebruikers.
- De beveiliging van de kanalen tussen on-premises verificatie agenten en Azure AD.
- Gedetailleerde technische informatie over hoe u de verificatie agenten beveiligt.
- Andere onderwerpen met betrekking tot beveiliging.

## <a name="key-security-capabilities"></a>Belangrijkste beveiligings mogelijkheden

Dit zijn de belangrijkste beveiligings aspecten van deze functie:
- Het is gebouwd op een beveiligde multi-tenant architectuur die het aanmelden van aanmeldings aanvragen tussen tenants biedt.
- On-premises wacht woorden worden nooit in een wille keurige vorm opgeslagen in de Cloud.
- On-premises verificatie agenten die Luis teren naar en reageren op aanvragen voor wachtwoord validatie maken alleen uitgaande verbindingen vanuit uw netwerk. Er is geen vereiste om deze verificatie agenten te installeren in een perimeter netwerk (DMZ). Behandel als best practice alle servers met verificatie-agents als laag 0-systemen (Zie [naslag](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Alleen standaard poorten (80 en 443) worden gebruikt voor uitgaande communicatie van de verificatie agenten naar Azure AD. U hoeft geen binnenkomende poorten te openen in uw firewall. 
  - Poort 443 wordt gebruikt voor alle geverifieerde uitgaande communicatie.
  - Poort 80 wordt alleen gebruikt voor het downloaden van de certificaatintrekkingslijsten (Crl's) om ervoor te zorgen dat geen van de certificaten die door deze functie worden gebruikt, zijn ingetrokken.
  - Zie [Azure Active Directory Pass-Through-verificatie: Quick](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)start voor de volledige lijst met netwerk vereisten.
- Wacht woorden die gebruikers opgeven tijdens het aanmelden, worden versleuteld in de Cloud voordat de on-premises verificatie agenten deze accepteren voor validatie op basis van Active Directory.
- Het HTTPS-kanaal tussen Azure AD en de on-premises verificatie agent wordt beveiligd met wederzijdse verificatie.
- Beveiligt uw gebruikers accounts door naadloos samen te werken met [beleid voor voorwaardelijke toegang van Azure AD](../conditional-access/overview.md), inclusief multi-factor Authentication (MFA), het [blok keren van verouderde verificatie](../conditional-access/concept-conditional-access-conditions.md) en het filteren van aanvallen op basis van [Brute Force-wacht woorden](../authentication/howto-password-smart-lockout.md).

## <a name="components-involved"></a>Betrokken onderdelen

Zie het [vertrouwens centrum](https://azure.microsoft.com/support/trust-center/)voor algemene informatie over de operationele, service-en gegevens beveiliging van Azure AD. De volgende onderdelen zijn betrokken bij het gebruik van Pass-Through-verificatie voor gebruikers aanmelding:
- **Azure AD STS**: een stateless Security Token Service (STS) waarmee aanmeldings aanvragen worden verwerkt en beveiligings tokens worden uitgegeven aan gebruikers, clients of services, zoals vereist.
- **Azure service bus**: biedt communicatie via de Cloud met Enter prise Messa ging en relays communicatie waarmee u on-premises oplossingen kunt verbinden met de Cloud.
- **Azure AD Connect-Verificatie agent**: een on-premises onderdeel dat luistert naar en reageert op aanvragen voor wachtwoord validatie.
- **Azure SQL database**: bevat informatie over de verificatie agenten van uw Tenant, met inbegrip van de meta gegevens en versleutelings sleutels.
- **Active Directory**: on-premises Active Directory, waar uw gebruikers accounts en hun wacht woorden worden opgeslagen.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Installatie en registratie van de verificatie agenten

Verificatie agenten worden geïnstalleerd en geregistreerd bij Azure AD wanneer u:
   - [Pass-Through-verificatie inschakelen via Azure AD Connect](./how-to-connect-pta-quick-start.md#step-2-enable-the-feature)
   - [Meer verificatie agenten toevoegen om te zorgen voor een hoge Beschik baarheid van aanmeldings aanvragen](./how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) 
   
Het ophalen van een verificatie-agent omvat drie hoofd fasen:

1. Verificatie agent installeren
2. Registratie van verificatie agent
3. Initialisatie van verificatie agent

In de volgende secties worden deze fasen uitvoerig besproken.

### <a name="authentication-agent-installation"></a>Verificatie agent installeren

Alleen globale beheerders kunnen een verificatie agent (met behulp van Azure AD Connect of zelfstandig) installeren op een on-premises server. De installatie voegt twee nieuwe vermeldingen toe aan de  >  lijst **Program** ma's  >  **en onderdelen** van het configuratie scherm:
- De verificatie agent-toepassing zelf. Deze toepassing wordt uitgevoerd met [netwerk](/windows/win32/services/networkservice-account) toegangs rechten.
- De updater-toepassing die wordt gebruikt om de verificatie agent automatisch bij te werken. Deze toepassing wordt uitgevoerd met [LocalSystem](/windows/win32/services/localsystem-account) -bevoegdheden.

>[!IMPORTANT]
>Uit veiligheids oogpunt moeten beheerders de server waarop de PTA-agent wordt uitgevoerd, behandelen alsof het een domein controller is.  De PTA-agent servers moeten worden gehard over dezelfde regels als beschreven in [domein controllers beveiligen tegen aanvallen](/windows-server/identity/ad-ds/plan/security-best-practices/securing-domain-controllers-against-attack)

### <a name="authentication-agent-registration"></a>Registratie van verificatie agent

Nadat u de verificatie agent hebt geïnstalleerd, moet deze zichzelf registreren bij Azure AD. Azure AD wijst elke verificatie agent toe aan een uniek, digitaal identiteits certificaat dat kan worden gebruikt voor beveiligde communicatie met Azure AD.

De registratie procedure verbindt ook de verificatie agent met uw Tenant. Dit zorgt ervoor dat Azure AD weet dat deze specifieke verificatie agent het enige is dat is geautoriseerd voor het afhandelen van wachtwoord validatie aanvragen voor uw Tenant. Deze procedure wordt herhaald voor elke nieuwe verificatie agent die u registreert.

De verificatie agenten gebruiken de volgende stappen om zich te registreren bij Azure AD:

![Agent registratie](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Azure AD vraagt eerst om een globale beheerder zich aan te melden bij Azure AD met hun referenties. Tijdens het aanmelden wordt door de verificatie agent een toegangs token opgehaald dat kan worden gebruikt namens de globale beheerder.
2. De verificatie agent genereert vervolgens een sleutel paar: een open bare sleutel en een persoonlijke sleutel.
    - Het sleutel paar wordt gegenereerd via de standaard versleuteling van RSA 2048-bits.
    - De persoonlijke sleutel blijft op de on-premises server waar de verificatie agent zich bevindt.
3. De verificatie agent maakt een registratie aanvraag voor Azure AD via HTTPS, met de volgende onderdelen die in de aanvraag zijn opgenomen:
    - Het toegangs token dat is verkregen in stap 1.
    - De open bare sleutel die in stap 2 is gegenereerd.
    - Een aanvraag voor certificaat ondertekening (CSR of certificaat aanvraag). Deze aanvraag geldt voor een digitaal identiteits certificaat, met Azure AD als certificerings instantie (CA).
4. Azure AD valideert het toegangs token in de registratie aanvraag en controleert of de aanvraag afkomstig is van een globale beheerder.
5. Azure AD ondertekent en verzendt vervolgens een certificaat voor digitale identiteit terug naar de verificatie agent.
    - De basis-CA in azure AD wordt gebruikt voor het ondertekenen van het certificaat. 

      > [!NOTE]
      > Deze CA bevindt zich _niet_ in het archief met Windows-vertrouwde basis certificerings instanties.
    - De CA wordt alleen gebruikt door de functie Pass-Through-verificatie. De CA wordt alleen gebruikt voor het ondertekenen van Csr's tijdens de registratie van de verificatie agent.
    -  Geen van de andere Azure AD-Services gebruiken deze certificerings instantie.
    - Het onderwerp (Distinguished name of DN) van het certificaat wordt ingesteld op uw Tenant-ID. Deze DN is een unieke GUID waarmee uw Tenant wordt geïdentificeerd. Dit DN-bereik is het certificaat alleen voor gebruik met uw Tenant.
6. In azure AD wordt de open bare sleutel van de verificatie agent opgeslagen in een data base in Azure SQL Database, waartoe alleen Azure AD toegang heeft.
7. Het certificaat (uitgegeven in stap 5) wordt opgeslagen op de on-premises server in het Windows-certificaat archief (met name op de [CERT_SYSTEM_STORE_LOCAL_MACHINE](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_LOCAL_MACHINE) locatie). Het wordt gebruikt door de verificatie agent en de updater toepassingen.

### <a name="authentication-agent-initialization"></a>Initialisatie van verificatie agent

Wanneer de verificatie agent wordt gestart, is voor de eerste keer na de registratie of na het opnieuw opstarten van de server een manier nodig om veilig te communiceren met de Azure AD-service en om wachtwoord validatie aanvragen te accepteren.

![Agent-initialisatie](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Hier ziet u hoe verificatie agenten worden geïnitialiseerd:

1. De verificatie agent maakt een uitgaande Boots trap-aanvraag voor Azure AD. 
    - Deze aanvraag wordt ingediend via poort 443 en bevindt zich op een wederzijds geverifieerd HTTPS-kanaal. De aanvraag gebruikt hetzelfde certificaat dat is uitgegeven tijdens de registratie van de verificatie agent.
2. Azure AD reageert op de aanvraag door een toegangs sleutel op te geven voor een Azure Service Bus wachtrij die uniek is voor uw Tenant en die wordt geïdentificeerd door uw Tenant-ID.
3. De verificatie agent maakt een permanente uitgaande HTTPS-verbinding (via poort 443) naar de wachtrij. 
    - De verificatie agent is nu gereed om aanvragen voor wachtwoord validatie op te halen en af te handelen.

Als er meerdere verificatie agenten zijn geregistreerd bij uw Tenant, zorgt de initialisatie procedure ervoor dat elke verbinding met dezelfde Service Bus wachtrij verbindt. 

## <a name="process-sign-in-requests"></a>Aanmeldings aanvragen verwerken 

In het volgende diagram ziet u hoe Pass-Through-verificatie gebruikers aanmeldings aanvragen verwerkt.

![Aanmelden verwerken](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Pass Through-verificatie verwerkt een aanmeldings aanvraag van een gebruiker als volgt: 

1. Een gebruiker probeert toegang te krijgen tot een toepassing, bijvoorbeeld [Outlook Web app](https://outlook.office365.com/owa).
2. Als de gebruiker nog niet is aangemeld, wordt de browser door de toepassing omgeleid naar de aanmeldings pagina van Azure AD.
3. De Azure AD STS-service reageert terug met de aanmeldings pagina van de **gebruiker** .
4. De gebruiker voert de gebruikers naam in op de **aanmeldings pagina van de gebruiker** en selecteert vervolgens de knop **volgende** .
5. De gebruiker voert zijn wacht woord in op de **aanmeldings pagina van de gebruiker** en selecteert vervolgens de knop **Aanmelden** .
6. De gebruikers naam en het wacht woord worden verzonden naar Azure AD STS in een HTTPS POST-aanvraag.
7. Azure AD STS haalt open bare sleutels op voor alle verificatie agenten die zijn geregistreerd op uw Tenant van Azure SQL Database en versleutelt het wacht woord met behulp van deze.
    - Hiermee worden ' N ' versleutelde wachtwoord waarden gegenereerd voor ' N ' verificatie agenten die zijn geregistreerd op uw Tenant.
8. Azure AD STS plaatst de aanvraag voor wachtwoord validatie, die bestaat uit de gebruikers naam en het versleutelde wacht woord, naar de Service Bus wachtrij die specifiek is voor uw Tenant.
9. Omdat de geïnitialiseerde verificatie agenten permanent zijn verbonden met de Service Bus wachtrij, haalt een van de beschik bare verificatie agenten de aanvraag voor wachtwoord validatie op.
10. De verificatie agent zoekt de versleutelde wachtwoord waarde die specifiek is voor de open bare sleutel met behulp van een id en ontsleutelt deze met behulp van de bijbehorende persoonlijke sleutel.
11. De verificatie agent probeert de gebruikers naam en het wacht woord te valideren voor on-premises Active Directory met behulp van de [Win32-LogonUser-API](/windows/win32/api/winbase/nf-winbase-logonusera) waarbij de para meter **dwLogonType** is ingesteld op **LOGON32_LOGON_NETWORK**. 
    - Deze API is dezelfde API die wordt gebruikt door Active Directory Federation Services (AD FS) voor het aanmelden van gebruikers in een federatief aanmeldings scenario.
    - Deze API is afhankelijk van het standaard oplossings proces in Windows Server om de domein controller te vinden.
12. De verificatie agent ontvangt het resultaat van Active Directory, zoals geslaagd, gebruikers naam of wacht woord is onjuist of het wacht woord is verlopen.

   > [!NOTE]
   > Als de verificatie agent mislukt tijdens het aanmeldings proces, wordt de volledige aanmeldings aanvraag verwijderd. Er zijn geen aanmeldings aanvragen van een verificatie agent naar een andere verificatie agent on-premises. Deze agents communiceren alleen met de Cloud en niet met elkaar.
   
13. De verificatie agent stuurt het resultaat terug naar Azure AD STS via een uitgaand HTTPS-kanaal met wederzijdse authenticatie via poort 443. Wederzijdse verificatie maakt gebruik van het certificaat dat eerder aan de verificatie agent is verleend tijdens de registratie.
14. Met Azure AD STS wordt gecontroleerd of dit resultaat overeenkomt met de specifieke aanmeldings aanvraag voor uw Tenant.
15. Azure AD STS wordt voortgezet met de aanmeldings procedure zoals geconfigureerd. Als de validatie van het wacht woord is geslaagd, kan de gebruiker bijvoorbeeld worden aangevraagd om Multi-Factor Authentication of terug te sturen naar de toepassing.

## <a name="operational-security-of-the-authentication-agents"></a>Operationele beveiliging van de verificatie agenten

Om ervoor te zorgen dat Pass-Through-verificatie blijft functioneren, worden de certificaten van verificatie agenten periodiek door Azure AD vernieuwd. Azure AD activeert de vernieuwingen. De vernieuwingen worden niet geregeld door de verificatie agenten zelf.

![Operationele beveiliging](./media/how-to-connect-pta-security-deep-dive/pta4.png)

De vertrouwens relatie van een verificatie agent met Azure AD vernieuwen:

1. De verificatie agent pingt elke paar uur van Azure AD om te controleren of het tijd is om het certificaat te vernieuwen. Het certificaat wordt 30 dagen vóór de verval datum vernieuwd.
    - Deze controle wordt uitgevoerd via een wederzijds geverifieerd HTTPS-kanaal en gebruikt hetzelfde certificaat dat is uitgegeven tijdens de registratie.
2. Als de service aangeeft dat het tijd is om te verlengen, genereert de verificatie agent een nieuw sleutel paar: een open bare sleutel en een persoonlijke sleutel.
    - Deze sleutels worden gegenereerd via de standaard versleuteling van RSA 2048-bits.
    - De persoonlijke sleutel verlaat nooit de on-premises server.
3. De verificatie agent maakt vervolgens een certificaat voor vernieuwing van Azure AD via HTTPS, met de volgende onderdelen die in de aanvraag zijn opgenomen:
    - Het bestaande certificaat dat is opgehaald van de CERT_SYSTEM_STORE_LOCAL_MACHINE locatie in het Windows-certificaat archief. Er is geen globale beheerder bij deze procedure betrokken, dus er is geen toegangs token vereist namens de globale beheerder.
    - De open bare sleutel die in stap 2 is gegenereerd.
    - Een aanvraag voor certificaat ondertekening (CSR of certificaat aanvraag). Deze aanvraag is van toepassing op een nieuw certificaat met digitale identiteit, met Azure AD als certificerings instantie.
4. Azure AD valideert het bestaande certificaat in de aanvraag voor certificaat vernieuwing. Vervolgens wordt gecontroleerd of de aanvraag afkomstig is van een verificatie-agent die is geregistreerd op uw Tenant.
5. Als het bestaande certificaat nog geldig is, ondertekent Azure AD vervolgens een nieuw certificaat voor digitale identiteit en geeft het het nieuwe certificaat terug naar de verificatie agent. 
6. Als het bestaande certificaat is verlopen, verwijdert Azure AD de verificatie agent uit de lijst met geregistreerde verificatie agenten van uw Tenant. Vervolgens moet een globale beheerder een nieuwe verificatie agent hand matig installeren en registreren.
    - Gebruik de Azure AD-basis certificerings instantie om het certificaat te ondertekenen.
    - Stel het onderwerp (DN-naam of DN) van het certificaat in op uw Tenant-ID, een GUID waarmee uw Tenant uniek wordt geïdentificeerd. Het DN-bereik van het certificaat is alleen voor uw Tenant.
6. In azure AD wordt de nieuwe open bare sleutel van de verificatie agent opgeslagen in een data base in Azure SQL Database waartoe alleen toegang is. Ook wordt de oude open bare sleutel die is gekoppeld aan de verificatie agent, ongeldig gemaakt.
7. Het nieuwe certificaat (uitgegeven in stap 5) wordt vervolgens opgeslagen op de server in het Windows-certificaat archief (met name op de [CERT_SYSTEM_STORE_CURRENT_USER](/windows/win32/seccrypto/system-store-locations#CERT_SYSTEM_STORE_CURRENT_USER) locatie).
    - Omdat de procedure voor het vernieuwen van de vertrouwens relatie niet-interactief plaatsvindt (zonder de aanwezigheid van de globale beheerder), heeft de verificatie agent geen toegang meer om het bestaande certificaat op de CERT_SYSTEM_STORE_LOCAL_MACHINE locatie bij te werken. 
    
   > [!NOTE]
   > Met deze procedure wordt het certificaat zelf niet uit de CERT_SYSTEM_STORE_LOCAL_MACHINE locatie verwijderd.
8. Het nieuwe certificaat wordt vanaf dit punt gebruikt voor verificatie. Bij elke volgende vernieuwing van het certificaat wordt het certificaat vervangen door de locatie van de CERT_SYSTEM_STORE_LOCAL_MACHINE.

## <a name="auto-update-of-the-authentication-agents"></a>Automatische update van de verificatie agenten

De updater-toepassing werkt de verificatie agent automatisch bij wanneer een nieuwe versie (met fout correcties of prestatie verbeteringen) wordt uitgebracht. De updater-toepassing verwerkt geen aanvragen voor wachtwoord validatie voor uw Tenant.

Azure AD fungeert als host voor de nieuwe versie van de software als een ondertekend **Windows Installer pakket (MSI)**. Het MSI-bestand is ondertekend met behulp van [micro soft Authenticode](/previous-versions/windows/internet-explorer/ie-developer/platform-apis/ms537359(v=vs.85)) met SHA256 als Digest-algoritme. 

![Automatisch bijwerken](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Een verificatie agent automatisch bijwerken:

1. De updater-toepassing pingt elke uur Azure AD om te controleren of er een nieuwe versie van de verificatie agent beschikbaar is. 
    - Deze controle wordt uitgevoerd via een wederzijds geverifieerd HTTPS-kanaal door gebruik te maken van hetzelfde certificaat dat is uitgegeven tijdens de registratie. De verificatie agent en de updater delen het certificaat dat is opgeslagen op de server.
2. Als er een nieuwe versie beschikbaar is, retourneert Azure AD de ondertekende MSI terug naar de Updater.
3. De updater controleert of de MSI is ondertekend door micro soft.
4. De updater voert het MSI-bestand uit. Deze actie omvat de volgende stappen:

   > [!NOTE]
   > De updater wordt uitgevoerd met [lokale systeem](/windows/win32/services/localsystem-account) bevoegdheden.

    - Stopt de verificatie Agent-service
    - Installeert de nieuwe versie van de verificatie agent op de server
    - Hiermee wordt de verificatie Agent-service opnieuw gestart

>[!NOTE]
>Als er meerdere verificatie agenten zijn geregistreerd op uw Tenant, worden de certificaten niet door Azure AD vernieuwd of op hetzelfde moment bijgewerkt. In plaats daarvan voert Azure AD er één tegelijk uit om te zorgen voor hoge Beschik baarheid van aanmeldings aanvragen.
>


## <a name="next-steps"></a>Volgende stappen
- [Huidige beperkingen](how-to-connect-pta-current-limitations.md): meer informatie over welke scenario's worden ondersteund en wat niet.
- [Snelstartgids](how-to-connect-pta-quick-start.md): aan de slag met Azure AD Pass-Through-verificatie.
- [Migratie van AD FS naar Pass-](https://aka.ms/adfstoptadpdownload) through-verificatie: een gedetailleerde hand leiding voor het migreren van AD FS (of andere Federatie technologieën) naar Pass-Through-verificatie.
- [Slimme vergren deling](../authentication/howto-password-smart-lockout.md): Configureer de mogelijkheden voor slim vergren delen op uw Tenant voor het beveiligen van gebruikers accounts.
- [Hoe het werkt](how-to-connect-pta-how-it-works.md): Leer de basis beginselen van de werking van Azure AD Pass-Through-verificatie.
- [Veelgestelde vragen](how-to-connect-pta-faq.md): Hier vindt u antwoorden op veelgestelde vragen.
- [Problemen oplossen](tshoot-connect-pass-through-authentication.md): informatie over het oplossen van veelvoorkomende problemen met de functie Pass-Through-verificatie.
- [Naadloze SSO van Azure AD](how-to-connect-sso.md): meer informatie over deze complementaire functie.