---
title: 'Azure AD Connect: vereisten en hardware | Microsoft Docs'
description: In dit artikel worden de vereisten en de hardwarevereisten voor Azure AD Connect beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/05/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 032b1ca945cf729f8a6682cf71d26a716b1e8863
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172343"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Vereisten voor Azure AD Connect
In dit artikel worden de vereisten en de hardwarevereisten voor Azure Active Directory (Azure AD)-verbinding beschreven.

## <a name="before-you-install-azure-ad-connect"></a>Voordat u Azure AD Connect installeert
Voordat u Azure AD Connect installeert, zijn er enkele dingen die u nodig hebt.

### <a name="azure-ad"></a>Azure AD
* U hebt een Azure AD-Tenant nodig. U krijgt er een met een [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/). U kunt een van de volgende portals gebruiken voor het beheren van Azure AD Connect:
  * Het [Azure Portal](https://portal.azure.com).
  * De [Office-Portal](https://portal.office.com).
* [Voeg en controleer het domein](../fundamentals/add-custom-domain.md) dat u wilt gebruiken in azure AD. Als u bijvoorbeeld van plan bent om contoso.com te gebruiken voor uw gebruikers, moet u ervoor zorgen dat dit domein is geverifieerd en dat u niet alleen het standaard domein contoso.onmicrosoft.com gebruikt.
* Een Azure AD-Tenant staat standaard 50.000-objecten toe. Wanneer u uw domein verifieert, neemt de limiet toe tot 300.000 objecten. Als u nog meer objecten in azure AD nodig hebt, kunt u een ondersteunings aanvraag openen om de limiet nog verder te verg root. Als u meer dan 500.000 objecten nodig hebt, hebt u een licentie nodig, zoals Microsoft 365, Azure AD Premium of Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>Uw on-premises gegevens voorbereiden
* Gebruik [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) om fouten te identificeren, zoals duplicaten en opmaak problemen in uw Directory voordat u synchroniseert met Azure AD en Microsoft 365.
* Bekijk de [optionele synchronisatie functies die u in azure AD kunt inschakelen](how-to-connect-syncservice-features.md)en bepaal welke functies u moet inschakelen.

### <a name="on-premises-active-directory"></a>On-premises Active Directory
* De Active Directory schema versie en het forest-functionaliteits niveau moeten Windows Server 2003 of hoger zijn. Op de domein controllers kan elke versie worden uitgevoerd, mits aan de vereisten voor schema versie en forestniveau wordt voldaan.
* Als u het *terugschrijven* van het functie wachtwoord wilt gebruiken, moeten de domein controllers zich op Windows Server 2012 of hoger bevallen.
* De domein controller die door Azure AD wordt gebruikt, moet beschrijfbaar zijn. Het gebruik van een alleen-lezen domein controller (RODC) *wordt niet ondersteund* en Azure AD Connect geen schrijf omleidingen volgen.
* On-premises forests of domeinen gebruiken met behulp van ' dotted ' (naam bevat een punt ' '. ') NetBIOS-namen worden *niet ondersteund*.
* U wordt aangeraden [de Prullenbak van Active Directory in te scha kelen](how-to-connect-sync-recycle-bin.md).

### <a name="powershell-execution-policy"></a>PowerShell-uitvoeringsbeleid
Azure Active Directory Connect ondertekende Power shell-scripts worden uitgevoerd als onderdeel van de installatie. Zorg ervoor dat het Power shell-uitvoerings beleid uitvoering van scripts toestaat.

Het aanbevolen uitvoerings beleid tijdens de installatie is ' RemoteSigned '.

Zie [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7)voor meer informatie over het instellen van het Power shell-uitvoerings beleid.


### <a name="azure-ad-connect-server"></a>Azure AD Connect server
De Azure AD Connect-server bevat essentiële identiteits gegevens. Het is belang rijk dat beheerders toegang tot deze server goed is beveiligd. Volg de richt lijnen bij het [beveiligen van bevoegde toegang](/windows-server/identity/securing-privileged-access/securing-privileged-access). 

De Azure AD Connect-server moet worden behandeld als een onderdeel van de laag 0 zoals beschreven in het model van de [Active Directory-administratieve laag](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) . 

Zie [Aanbevolen procedures voor het beveiligen van Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory)voor meer informatie over het beveiligen van uw Active Directory-omgeving.

#### <a name="installation-prerequisites"></a>Installatievereisten

- Azure AD Connect moet worden geïnstalleerd op een domein van Windows Server 2012 of hoger. 
- Azure AD Connect kan niet worden geïnstalleerd op Small Business Server of Windows Server Essentials vóór 2019 (Windows Server Essentials 2019 wordt ondersteund). De server moet Windows Server Standard of hoger gebruiken. 
- Op de Azure AD Connect-server moet een volledige GUI zijn geïnstalleerd. Het installeren van Azure AD Connect op Windows Server Core wordt niet ondersteund. 
- De Azure AD Connect-server moet Power shell transcriptie niet hebben groepsbeleid ingeschakeld als u de Azure AD Connect wizard gebruikt voor het beheren van de configuratie van Active Directory Federation Services (AD FS). U kunt Power shell transcriptie inschakelen als u de wizard Azure AD Connect gebruikt voor het beheren van de synchronisatie configuratie. 
- Als AD FS wordt geïmplementeerd: 
    - De servers waarop AD FS of Web Application proxy zijn geïnstalleerd, moeten Windows Server 2012 R2 of later zijn. Windows Remote Management moet zijn ingeschakeld op deze servers voor installatie op afstand. 
    - U moet TLS/SSL-certificaten configureren. Zie [SSL/TLS-protocollen en coderings suites beheren voor AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) en [SSL-certificaten beheren in AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap)voor meer informatie.
    - U moet naam omzetting configureren. 
- Als voor uw globale beheerders MFA is ingeschakeld, https://secure.aadcdn.microsoftonline-p.com *moet* de URL zich in de lijst met vertrouwde sites bekomen. U wordt gevraagd deze site toe te voegen aan de lijst met vertrouwde websites wanneer u wordt gevraagd om een MFA-Challenge en deze wordt niet eerder toegevoegd. U kunt Internet Explorer gebruiken om het toe te voegen aan uw vertrouwde sites.

#### <a name="harden-your-azure-ad-connect-server"></a>De beveiliging van uw Azure AD Connect-server 
We raden u aan uw Azure AD Connect-server te beveiligen om het beveiligings risico te verminderen voor dit kritieke onderdeel van uw IT-omgeving. Aan de hand van deze aanbevelingen kunnen enkele beveiligings Risico's voor uw organisatie worden verholpen.

- Behandel Azure AD Connect hetzelfde als een domein controller en andere resources van laag 0. Zie [Active Directory Administrative tier model](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)(Engelstalig) voor meer informatie.
- Beperk beheerders toegang tot de Azure AD Connect-server tot alleen domein Administrators of andere nauw keurig beheerde beveiligings groepen.
- Maak een [speciaal account voor alle mede werkers met privileged Access](/windows-server/identity/securing-privileged-access/securing-privileged-access). Beheerders moeten niet surfen op het web, hun e-mail adres controleren en dagelijkse productiviteits taken uitvoeren met accounts met veel bevoegdheden.
- Volg de richt lijnen voor het [beveiligen van bevoegde toegang](/windows-server/identity/securing-privileged-access/securing-privileged-access). 
- Het gebruik van NTLM-verificatie met de AADConnect-server weigeren. Hier volgen enkele manieren om dit te doen: [NTLM beperken op de AADConnect-server](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) en [NTLM beperken op een domein](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)
- Zorg ervoor dat elke computer een uniek wacht woord voor de lokale beheerder heeft. Zie [Local Administrator Password Solution (verval)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps) voor meer informatie. kan unieke wille keurige wacht woorden configureren op elk werk station en de server slaat ze op Active Directory beveiligd door een ACL. Alleen in aanmerking komende geautoriseerde gebruikers kunnen de wacht woorden van deze lokale beheerders accounts lezen of aanvragen. U kunt het verval verkrijgen voor gebruik op werk stations en servers vanuit het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=46899). Aanvullende richt lijnen voor het gebruik van een omgeving met verval en privileged Access Workstation (Paw's) vindt u in [operationele standaarden op basis van het schone bron principe](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Implementeer toegewezen [privileged Access workstations](/windows-server/identity/securing-privileged-access/privileged-access-workstations) voor alle mede werkers met uitgebreide toegang tot de gegevens systemen van uw organisatie. 
- Volg deze [aanvullende richt lijnen](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface) om de kwets baarheid van uw Active Directory omgeving te verminderen.


### <a name="sql-server-used-by-azure-ad-connect"></a>SQL Server gebruikt door Azure AD Connect
* Azure AD Connect vereist een SQL Server-database voor het opslaan van identiteitsgegevens. Standaard is een SQL Server 2012 Express LocalDB (een lichte versie van SQL Server Express) geïnstalleerd. SQL Server Express heeft een maximale grootte van 10 GB waarmee u ongeveer 100.000 objecten kunt beheren. Als u een groter volume aan Directory objecten wilt beheren, moet u de installatie wizard naar een andere installatie van SQL Server. Het type SQL Server installatie kan invloed hebben [op de prestaties van Azure AD Connect](./plan-connect-performance-factors.md#sql-database-factors).
* Als u een andere installatie van SQL Server gebruikt, zijn deze vereisten van toepassing:
  * Azure AD Connect ondersteunt alle versies van SQL Server van 2012 (met het nieuwste Service Pack) tot SQL Server 2019. Azure SQL Database *wordt niet ondersteund* als data base.
  * U moet een niet-hoofdletter gevoelige SQL-sortering gebruiken. Deze sorteringen worden aangeduid met een \_ CI_ in hun naam. Het gebruik van een niet-hoofdletter gevoelige sortering die wordt geïdentificeerd door \_ CS_ in hun naam *wordt niet ondersteund*.
  * U kunt slechts één synchronisatie-engine per SQL-exemplaar hebben. Het delen van een SQL-exemplaar met FIM/MIM Sync, DirSync of Azure AD Sync *wordt niet ondersteund*.

### <a name="accounts"></a>Accounts
* U moet een Azure AD Global Administrator-account hebben voor de Azure AD-Tenant waarmee u wilt integreren. Dit account moet een *school-of organisatie account* zijn en kan geen *Microsoft-account* zijn.
* Als u [snelle instellingen](reference-connect-accounts-permissions.md#express-settings-installation) gebruikt of als u een upgrade uitvoert van DirSync, moet u een beheerders account voor de onderneming hebben voor uw on-premises Active Directory.
* Als u het installatiepad van aangepaste instellingen gebruikt, hebt u meer opties. Zie [aangepaste installatie-instellingen](reference-connect-accounts-permissions.md#custom-installation-settings)voor meer informatie.

### <a name="connectivity"></a>Connectiviteit
* De Azure AD Connect-server moet de DNS-omzetting voor intranet en Internet hebben. De DNS-server moet namen kunnen omzetten naar uw on-premises Active Directory en de Azure AD-eind punten.
* Als u firewalls op uw intranet hebt en u poorten wilt openen tussen de Azure AD Connect servers en uw domein controllers, raadpleegt u [Azure AD Connect poorten](reference-connect-ports.md) voor meer informatie.
* Als uw proxy of firewall beperkingen heeft voor de toegang tot Url's, moeten de Url's die worden beschreven in [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) adresbereiken worden geopend. Zie ook [Safelist de Azure Portal url's op uw firewall of proxy server](../../azure-portal/azure-portal-safelist-urls.md?tabs=public-cloud).
  * Als u de micro soft-Cloud in Duitsland of de Microsoft Azure Government Cloud gebruikt, raadpleegt u [Azure AD Connect Sync Service instances-overwegingen](reference-connect-instances.md) voor url's.
* Azure AD Connect (versie 1.1.614.0 en After) maakt standaard gebruik van TLS 1,2 voor het versleutelen van de communicatie tussen de synchronisatie-engine en Azure AD. Als TLS 1,2 niet beschikbaar is in het onderliggende besturings systeem, wordt Azure AD Connect incrementeel terugvallen op oudere protocollen (TLS 1,1 en TLS 1,0).
* Vóór versie 1.1.614.0 maakt Azure AD Connect standaard gebruik van TLS 1,0 voor het versleutelen van de communicatie tussen de synchronisatie-engine en Azure AD. Als u wilt overschakelen naar TLS 1,2, volgt u de stappen in [tls 1,2 inschakelen voor Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Als u een uitgaande proxy gebruikt om verbinding te maken met internet, moet u de volgende instelling in het **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** bestand toevoegen voor de installatie wizard en Azure AD Connect synchroniseren om verbinding te kunnen maken met internet en Azure AD. Deze tekst moet aan de onderkant van het bestand worden opgegeven. In deze code vertegenwoordigt *&lt; proxyAddress attribuut &gt;* het werkelijke IP-adres of de hostnaam van de proxy.

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Als voor uw proxy server verificatie is vereist, moet het [Service account](reference-connect-accounts-permissions.md#adsync-service-account) zich in het domein bevinden. Gebruik het installatiepad aangepaste instellingen om een [aangepast Service account](how-to-connect-install-custom.md#install-required-components)op te geven. U hebt ook een andere wijziging in machine.config. Met deze wijziging in machine.config reageert de installatie wizard en de synchronisatie-engine op verificatie aanvragen van de proxy server. In alle pagina's van de wizard installatie, met uitzonde ring van de pagina **configureren** , worden de referenties van de aangemelde gebruiker gebruikt. Op de pagina **configureren** aan het einde van de installatie wizard wordt de context overgeschakeld naar het [Service account](reference-connect-accounts-permissions.md#adsync-service-account) dat u hebt gemaakt. De machine.config sectie moet er als volgt uitzien:

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Als de configuratie van de proxy in een bestaande configuratie wordt uitgevoerd, moet de **Microsoft Azure AD-synchronisatie service** één keer opnieuw worden gestart om de Azure AD Connect de proxy configuratie te lezen en het gedrag bij te werken. 
* Wanneer Azure AD Connect een webverzoek verzendt naar Azure AD als onderdeel van adreslijst synchronisatie, kan het tot vijf minuten duren voordat Azure AD reageert. Het is gebruikelijk dat proxy servers een time-outconfiguratie voor verbinding inactief hebben. Zorg ervoor dat de configuratie is ingesteld op ten minste 6 minuten of langer.

Raadpleeg MSDN over het [standaard proxy-element](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)voor meer informatie.
Zie [verbindings problemen oplossen](tshoot-connect-connectivity.md)voor meer informatie over problemen met de connectiviteit.

### <a name="other"></a>Anders
Optioneel: gebruik een test gebruikers account om de synchronisatie te controleren.

## <a name="component-prerequisites"></a>Onderdeel vereisten
### <a name="powershell-and-net-framework"></a>Power shell en .NET Framework
Azure AD Connect is afhankelijk van micro soft power shell en .NET Framework 4.5.1. U hebt deze versie of een nieuwere versie op uw server nodig. Voer, afhankelijk van uw Windows Server-versie, de volgende acties uit:

* Windows Server 2012 R2
  * Micro soft power shell wordt standaard geïnstalleerd. Geen actie vereist.
  * .NET Framework 4.5.1 en latere versies worden aangeboden via Windows Update. Zorg ervoor dat u de meest recente updates voor Windows Server hebt geïnstalleerd in het configuratie scherm.
* Windows Server 2012
  * De nieuwste versie van micro soft power shell is beschikbaar in Windows Management Framework 4,0, beschikbaar in het [micro soft Download centrum](https://www.microsoft.com/downloads).
  * .NET Framework 4.5.1 en latere versies zijn beschikbaar in het [micro soft Download centrum](https://www.microsoft.com/downloads).


### <a name="enable-tls-12-for-azure-ad-connect"></a>TLS 1,2 inschakelen voor Azure AD Connect
Vóór versie 1.1.614.0 maakt Azure AD Connect standaard gebruik van TLS 1,0 voor het versleutelen van de communicatie tussen de synchronisatie engine-server en Azure AD. U kunt .NET-toepassingen zo configureren dat TLS 1,2 standaard op de server wordt gebruikt. Zie [micro soft security advisory 2960358](/security-updates/SecurityAdvisories/2015/2960358)(Engelstalig) voor meer informatie over TLS 1,2.

1. Zorg ervoor dat u de .NET 4.5.1-hotfix hebt geïnstalleerd voor uw besturings systeem. Zie [micro soft-beveiligings advies 2960358](/security-updates/SecurityAdvisories/2015/2960358)voor meer informatie. Mogelijk hebt u al deze hotfix of een latere versie geïnstalleerd op uw server.

1. Voor alle besturings systemen stelt u deze register sleutel in en start u de server opnieuw op.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. Als u ook TLS 1,2 wilt inschakelen tussen de sync engine-server en een externe SQL Server, moet u ervoor zorgen dat u de vereiste versies hebt geïnstalleerd voor [TLS 1,2-ondersteuning voor Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Vereisten voor Federatie-installatie en-configuratie
### <a name="windows-remote-management"></a>Windows Remote Management
Wanneer u Azure AD Connect gebruikt om AD FS of de Web Application proxy (WAP) te implementeren, moet u de volgende vereisten controleren:

* Als de doel server lid is van een domein, moet u ervoor zorgen dat Windows Remote Managed is ingeschakeld.
  * Gebruik de opdracht in een Power shell-opdracht venster met verhoogde bevoegdheden `Enable-PSRemoting –force` .
* Als de doel server een WAP-computer is die niet aan een domein is toegevoegd, zijn er een aantal aanvullende vereisten:
  * Op de doel computer (WAP-machine):
    * Zorg ervoor dat de Windows Remote Management/WS-Management-service (WinRM) wordt uitgevoerd via de module Services.
    * Gebruik de opdracht in een Power shell-opdracht venster met verhoogde bevoegdheden `Enable-PSRemoting –force` .
  * Op de computer waarop de wizard wordt uitgevoerd (als de doel computer niet lid is van een domein of een niet-vertrouwd domein is):
    * Gebruik de opdracht in een Power shell-opdracht venster met verhoogde bevoegdheden `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate` .
    * In Server beheer:
      * Voeg een DMZ WAP-host toe aan een machine groep. **Selecteer in Server beheer de** optie  >  **servers toevoegen** en gebruik vervolgens het tabblad **DNS** .
      * Klik op het tabblad **Serverbeheer alle servers** met de rechter muisknop op de WAP-server en selecteer **beheren als**. Voer lokale (niet-domein) referenties in voor de WAP-computer.
      * Als u externe Power shell-connectiviteit wilt valideren, klikt u op het tabblad **Serverbeheer alle servers** met de rechter muisknop op de WAP-server en selecteert u **Windows Power shell**. Er moet een externe Power shell-sessie worden geopend om ervoor te zorgen dat externe Power shell-sessies kunnen worden ingesteld.

### <a name="tlsssl-certificate-requirements"></a>Vereisten voor TLS/SSL-certificaat
* U wordt aangeraden hetzelfde TLS/SSL-certificaat te gebruiken voor alle knoop punten van uw AD FS-Farm en alle Web Application proxy-servers.
* Het certificaat moet een x509-certificaat zijn.
* U kunt een zelfondertekend certificaat gebruiken op Federatie servers in een test omgeving. Voor een productie omgeving wordt u aangeraden het certificaat van een open bare certificerings instantie te verkrijgen.
  * Als u een certificaat gebruikt dat niet openbaar wordt vertrouwd, controleert u of het certificaat dat is geïnstalleerd op elke Web Application proxy-server, wordt vertrouwd op zowel de lokale server als op alle Federatie servers.
* De identiteit van het certificaat moet overeenkomen met de naam van de Federation service (bijvoorbeeld sts.contoso.com).
  * De identiteit is een SAN-extensie (alternatieve naam voor onderwerp) van het type dNSName of, als er geen SAN-vermeldingen zijn, wordt de onderwerpnaam opgegeven als een algemene naam.
  * Er kunnen meerdere SAN-items aanwezig zijn in het certificaat dat een van deze vermeldingen bevat die overeenkomen met de naam van de Federation-service.
  * Als u van plan bent Workplace Join te gebruiken, is een extra SAN vereist met de waarde **Enterprise registratie.** gevolgd door het achtervoegsel van de user principal name (UPN) van uw organisatie, bijvoorbeeld enterpriseregistration.contoso.com.
* Certificaten gebaseerd op CNG-sleutels (Next-Generation) en Ksp's (Key Storage providers) worden niet ondersteund. Als gevolg hiervan moet u een certificaat gebruiken dat is gebaseerd op een CSP (Cryptographic Service Provider) en niet een SLEUTELARCHIEFPROVIDER.
* Joker tekens worden ondersteund.

### <a name="name-resolution-for-federation-servers"></a>Naam omzetting voor Federatie servers
* Stel DNS-records in voor de naam van de AD FS (bijvoorbeeld sts.contoso.com) voor zowel het intranet (de interne DNS-server) als het extranet (open bare DNS via uw domein registratie functie). Zorg ervoor dat u een records en niet CNAME-records gebruikt voor de intranet-DNS-record. Het gebruik van een record is vereist voor een juiste werking van Windows-verificatie vanaf de computer die lid is van het domein.
* Als u meer dan één AD FS server of Web Application proxy-server implementeert, moet u ervoor zorgen dat u uw load balancer hebt geconfigureerd en dat de DNS-records voor de AD FS naam (bijvoorbeeld sts.contoso.com) verwijzen naar het load balancer.
* Voor geïntegreerde Windows-verificatie voor het werken met browser toepassingen die gebruikmaken van Internet Explorer in uw intranet, moet u ervoor zorgen dat de naam van de AD FS (bijvoorbeeld sts.contoso.com) wordt toegevoegd aan de intranet zone in Internet Explorer. Deze vereiste kan worden beheerd via groepsbeleid en worden geïmplementeerd op alle computers die lid zijn van een domein.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect ondersteunende onderdelen
Azure AD Connect installeert de volgende onderdelen op de server waarop Azure AD Connect is geïnstalleerd. Deze lijst is voor een eenvoudige snelle installatie. Als u ervoor kiest om een andere SQL Server te gebruiken op de pagina **synchronisatie Services installeren** , wordt SQL Express LocalDB niet lokaal geïnstalleerd.

* Azure AD Connect Health (Engelstalig)
* Microsoft SQL Server 2012-opdracht regel Programma's
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 native client
* Pakket voor herdistributie van micro soft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Hardwarevereisten voor Azure AD Connect
De volgende tabel bevat de minimale vereisten voor de Azure AD Connect Sync-computer.

| Aantal objecten in Active Directory | CPU | Geheugen | Grootte van harde schijf |
| --- | --- | --- | --- |
| Minder dan 10.000 |1,6 GHz |4 GB |70 GB |
| 10000 – 50.000 |1,6 GHz |4 GB |70 GB |
| 50.000 – 100000 |1,6 GHz |16 GB |100 GB |
| Voor 100.000 of meer objecten is de volledige versie van SQL Server vereist | | | |
| 100000 – 300000 |1,6 GHz |32 GB |300 GB |
| 300000 – 600.000 |1,6 GHz |32 GB |450 GB |
| Meer dan 600.000 |1,6 GHz |32 GB |500 GB |

De minimale vereisten voor computers met AD FS-of Web Application proxy-servers zijn:

* CPU: Dual Core 1,6 GHz of hoger
* Geheugen: 2 GB of hoger
* Azure VM: a2-configuratie of hoger

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).