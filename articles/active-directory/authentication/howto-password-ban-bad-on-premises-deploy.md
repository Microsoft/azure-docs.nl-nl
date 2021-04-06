---
title: On-premises Azure AD-wachtwoord beveiliging implementeren
description: Meer informatie over het plannen en implementeren van Azure AD-wachtwoord beveiliging in een on-premises Active Directory Domain Services omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8cae19bd07e1cc87a0aaa25e47cf5f431d566ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653810"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>On-premises Azure Active Directory wachtwoord beveiliging plannen en implementeren

Gebruikers maken vaak wachtwoorden waarin woorden voorkomen die lokaal veel worden gebruikt, zoals de naam van een school, een sportvereniging of een beroemde persoon. Deze wachtwoorden zijn gemakkelijk te raden en zijn slecht bestand tegen zogenaamde woordenboekaanvallen. Voor het afdwingen van sterke wacht woorden in uw organisatie, biedt Azure Active Directory-wachtwoord beveiliging van Azure Active Directory een globale en aangepaste lijst met verboden wacht woorden. Een aanvraag voor het wijzigen van het wacht woord is mislukt als er een overeenkomst is gevonden in de lijst met verboden wacht woorden.

Om uw on-premises Active Directory Domain Services-omgeving (AD DS) te beveiligen, kunt u Azure AD-wachtwoord beveiliging installeren en configureren om met uw on-premises DC te werken. In dit artikel wordt beschreven hoe u de Azure AD-service voor wachtwoord beveiliging proxy en Azure AD Password Protection DC-agent in uw on-premises omgeving installeert en registreert.

Zie [Azure AD-wachtwoord beveiliging afdwingen voor Windows Server Active Directory](concept-password-ban-bad-on-premises.md)voor meer informatie over de werking van Azure AD-wachtwoord beveiliging in een on-premises omgeving.

## <a name="deployment-strategy"></a>Implementatie strategie

In het volgende diagram ziet u hoe de basis onderdelen van Azure AD-wachtwoord beveiliging samen werken in een on-premises Active Directory omgeving:

![Hoe de Azure AD-onderdelen voor wachtwoord beveiliging samen werken](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Het is een goed idee om te controleren hoe de software werkt voordat u deze implementeert. Zie [Conceptueel overzicht van Azure AD-wachtwoord beveiliging](concept-password-ban-bad-on-premises.md)voor meer informatie.

U wordt aangeraden implementaties in de *controle* modus te starten. De controle modus is de standaard instelling, waar wacht woorden nog kunnen worden ingesteld. Wacht woorden die worden geblokkeerd, worden vastgelegd in het gebeurtenis logboek. Nadat u de proxy servers en DC-agents in de controle modus hebt geïmplementeerd, controleert u de invloed die het wachtwoord beleid op gebruikers heeft wanneer het beleid wordt afgedwongen.

Tijdens de controle fase zien veel organisaties dat de volgende situaties van toepassing zijn:

* Ze moeten bestaande operationele processen verbeteren om veiligere wacht woorden te gebruiken.
* Gebruikers gebruiken vaak onbeveiligde wacht woorden.
* Ze moeten gebruikers informeren over de aanstaande wijziging in het afdwingen van beveiliging, mogelijke gevolgen hiervan en het kiezen van veiligere wacht woorden.

Het is ook mogelijk om een sterkere wachtwoord validatie te hebben die van invloed is op uw bestaande Active Directory implementatie van de domein controller. U kunt het beste ten minste één DC-promotie en één DC-degradatie vinden tijdens de evaluatie periode om dergelijke problemen op te lossen. Raadpleeg voor meer informatie de volgende artikelen:

* [Ntdsutil.exe kan geen zwak wacht woord voor de herstel modus van de Directory Services instellen](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [De replica promotie van de domein controller is mislukt vanwege een zwak wacht woord voor de herstel modus van de Directory Services](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [De degradatie van de domein controller is mislukt vanwege een zwak lokaal beheerders wachtwoord](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Nadat de functie gedurende een redelijke periode in de controle modus is uitgevoerd, kunt u de configuratie van de *controle* wijzigen naar *afdwingen* om veiligere wacht woorden te vereisen. Het is een goed idee om gedurende deze tijd extra bewaking te bewaken.

Het is belang rijk te weten dat Azure AD-wachtwoord beveiliging alleen wacht woorden kan valideren tijdens het wijzigen van het wacht woord of het instellen van bewerkingen. Wacht woorden die in Active Directory voorafgaand aan de implementatie van Azure AD-wachtwoord beveiliging zijn geaccepteerd en opgeslagen, worden nooit gevalideerd en blijven werken. Na verloop van tijd gaan alle gebruikers en accounts uiteindelijk gebruikmaken van Azure AD-wacht woorden voor wachtwoord beveiliging, omdat hun bestaande wacht woorden normaal verlopen. Accounts die zijn geconfigureerd met ' wacht woord nooit verloopt ', worden niet van deze account uitgesloten.

### <a name="multiple-forest-considerations"></a>Aandachtspunten voor meerdere forests

Er zijn geen aanvullende vereisten voor het implementeren van Azure AD-wachtwoord beveiliging op meerdere forests.

Elk forest is onafhankelijk geconfigureerd, zoals beschreven in de volgende sectie voor het [implementeren van on-premises Azure AD-wachtwoord beveiliging](#download-required-software). Elke Azure AD-wachtwoord beveiligings proxy kan alleen domein controllers ondersteunen van het forest waaraan deze is gekoppeld.

De Azure AD-software voor wachtwoord beveiliging in een forest is niet op de hoogte van wachtwoord beveiligings software die in andere forests is geïmplementeerd, ongeacht Active Directory vertrouwens configuraties.

### <a name="read-only-domain-controller-considerations"></a>Aandachtspunten voor alleen-lezen domein controller

Het wijzigen van wacht woorden of ingestelde gebeurtenissen worden niet verwerkt en bewaard op alleen-lezen domein controllers (RODC). In plaats daarvan worden ze doorgestuurd naar Beschrijf bare domein controllers. U hoeft de Azure AD-agent software voor wachtwoord beveiliging niet te installeren op RODC'S.

Verder wordt het niet ondersteund om de Azure AD-proxy service voor wachtwoord beveiliging uit te voeren op een alleen-lezen domein controller.

### <a name="high-availability-considerations"></a>Overwegingen voor hoge Beschik baarheid

De belangrijkste bezorgdheid voor wachtwoord beveiliging is de beschik baarheid van Azure AD-proxy servers voor wachtwoord beveiliging wanneer de Dc's in een forest proberen nieuwe beleids regels of andere gegevens vanuit Azure te downloaden. Elke Azure AD-agent voor wachtwoord beveiliging gebruikt een eenvoudig Round Robin-algoritme wanneer u bepaalt welke proxy server moet worden aangeroepen. Proxy servers die niet reageren, worden door de agent overgeslagen.

Voor de meeste volledig verbonden Active Directory implementaties met een goede replicatie van de status van de map en SYSVOL-map zijn er twee Azure AD-proxy servers voor wachtwoord beveiliging voldoende om Beschik baarheid te garanderen. Deze configuratie resulteert in het actueel downloaden van nieuwe beleids regels en andere gegevens. U kunt indien gewenst extra Azure AD-proxy servers voor wachtwoord beveiliging implementeren.

Het ontwerp van de DC-agent software van Azure AD-wachtwoord beveiliging vermindert de gebruikelijke problemen die zijn gekoppeld aan hoge Beschik baarheid. De Azure AD-agent voor wachtwoord beveiliging beheert een lokale cache van het laatst gedownloade wachtwoord beleid. Zelfs als alle geregistreerde proxy servers niet meer beschikbaar zijn, blijven de DC-agents voor Azure AD-wachtwoord beveiliging het wachtwoord beleid in de cache afdwingen.

Een redelijke update frequentie voor wachtwoord beleid in een grote implementatie is doorgaans dagen, niet uren of minder. Korte uitval van de proxy servers heeft dus geen invloed op de beveiliging van Azure AD-wacht woorden.

## <a name="deployment-requirements"></a>Implementatievereisten

Zie [Azure AD-licentie vereisten voor wachtwoord beveiliging](concept-password-ban-bad.md#license-requirements)voor meer informatie over licenties.

De volgende kern vereisten zijn van toepassing:

* Op alle machines, met inbegrip van domein controllers, waarop Azure AD-onderdelen voor wachtwoord beveiliging zijn geïnstalleerd, moet de universele C-runtime zijn geïnstalleerd.
    * U kunt de runtime ophalen door ervoor te zorgen dat u alle updates van Windows Update hebt. U kunt deze ook verkrijgen in een specifiek update pakket. Zie [Update voor Universal C runtime in Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)voor meer informatie.
* U hebt een account nodig met Active Directory domein beheerders rechten in het forest-hoofd domein om het Windows Server Active Directory forest te registreren bij Azure AD.
* De Key Distribution-service moet zijn ingeschakeld op alle domein controllers in het domein waarop Windows Server 2012 en hoger wordt uitgevoerd. Deze service wordt standaard ingeschakeld via hand matige trigger start.

* Er moet een netwerk verbinding zijn tussen ten minste één domein controller in elk domein en ten minste één server die als host fungeert voor de proxy service voor Azure AD-wachtwoord beveiliging. Deze connectiviteit moet toestaan dat de domein controller toegang krijgt tot de RPC-eindpunttoewijzer poort 135 en de RPC-server poort op de proxy service.
    * De RPC-server poort is standaard een dynamische RPC-poort, maar kan worden geconfigureerd voor het [gebruik van een statische poort](#static).
* Alle computers waarop de Azure AD-proxy service voor wachtwoord beveiliging wordt geïnstalleerd, moeten netwerk toegang hebben tot de volgende eind punten:

    |**Eindpunt**|**Doel**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Verificatie aanvragen|
    |`https://enterpriseregistration.windows.net`|Azure AD-functionaliteit voor wachtwoord beveiliging|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD-agent voor wachtwoord beveiliging DC

De volgende vereisten zijn van toepassing op de Azure AD-agent voor wachtwoord beveiliging:

* Op alle computers waarop de Azure AD-agent software voor wachtwoord beveiliging wordt geïnstalleerd, moet Windows Server 2012 of hoger worden uitgevoerd, met inbegrip van Windows Server Core-edities.
    * Het Active Directory domein of forest hoeft niet te zijn op het Windows Server 2012-domein functionaliteits niveau (DFL) of forestfunctionaliteitsniveau (FFL). Zoals vermeld in [ontwerp principes](concept-password-ban-bad-on-premises.md#design-principles), is er geen mini maal DFL of FFL vereist voor het uitvoeren van de DC-agent of proxy software.
* Op alle computers waarop de Azure AD-proxy service voor wachtwoord beveiliging wordt geïnstalleerd, moet .NET 4.7.2 zijn geïnstalleerd.
    * Als .NET 4.7.2 nog niet is geïnstalleerd, downloadt en voert u het installatie programma uit dat is gevonden op [het .NET Framework 4.7.2 offline-installatie programma voor Windows](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).
* Een Active Directory domein waarop de Azure AD-service voor wachtwoord beveiliging van de domein controller wordt uitgevoerd, moet Distributed File System replicatie (DFSR) voor SYSVOL-replicatie gebruiken.
   * Als DFSR nog niet wordt gebruikt voor uw domein, moet u migreren voordat u Azure AD-wachtwoord beveiliging installeert. Zie [SYSVOL-replicatie migratie handleiding: FRS naar DSF-replicatie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10)) voor meer informatie

    > [!WARNING]
    > De Azure AD-agent software voor wachtwoord beveiliging wordt momenteel geïnstalleerd op domein controllers in domeinen die nog steeds gebruikmaken van FRS (de voorafgaande technologie naar DFSR) voor SYSVOL-replicatie, maar de software werkt niet goed in deze omgeving.
    >
    > Bijkomende negatieve neven effecten omvatten afzonderlijke bestanden die niet worden gerepliceerd, en SYSVOL-herstel procedures die worden uitgevoerd, maar die niet op de achtergrond worden gerepliceerd.
    >
    > Migreer zo snel mogelijk uw domein om DFSR te gebruiken en de implementatie van Azure AD-wachtwoord beveiliging te blok keren. Toekomstige versies van de software worden automatisch uitgeschakeld wanneer ze worden uitgevoerd in een domein dat nog steeds gebruikmaakt van FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD-proxy service voor wachtwoord beveiliging

De volgende vereisten zijn van toepassing op de Azure AD-proxy service voor wachtwoord beveiliging:

* Op alle computers waarop de Azure AD-service voor wachtwoord beveiliging wordt geïnstalleerd, moet Windows Server 2012 R2 of hoger worden uitgevoerd, met inbegrip van Windows Server Core-edities.

    > [!NOTE]
    > De implementatie van de Azure AD-service voor wachtwoord beveiliging is een verplichte vereiste voor het implementeren van Azure AD-wachtwoord beveiliging, zelfs als de domein controller mogelijk uitgaande directe Internet connectiviteit heeft.

* Op alle computers waarop de Azure AD-proxy service voor wachtwoord beveiliging wordt geïnstalleerd, moet .NET 4.7.2 zijn geïnstalleerd.
    * Als .NET 4.7.2 nog niet is geïnstalleerd, downloadt en voert u het installatie programma uit dat is gevonden op [het .NET Framework 4.7.2 offline-installatie programma voor Windows](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).
* Alle computers die de Azure AD-proxy service voor wachtwoord beveiliging hosten, moeten worden geconfigureerd om domein controllers de mogelijkheid te geven zich aan te melden bij de proxy service. Deze mogelijkheid wordt bepaald via de toewijzing van de bevoegdheid toegang tot deze computer vanaf het netwerk.
* Alle computers die de Azure AD-proxy service voor wachtwoord beveiliging hosten, moeten zo worden geconfigureerd dat HTTP-verkeer van uitgaande TLS 1,2 is toegestaan.
* Een *globale beheerder* of *beveiligings beheerders* account voor het registreren van de Azure AD-service voor wachtwoord beveiliging proxy en het forest met Azure AD.
* Netwerk toegang moet zijn ingeschakeld voor de set van poorten en Url's die zijn opgegeven in de [installatie procedures van de toepassings proxy omgeving](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Vereisten voor de Microsoft Azure AD verbinding maken met de agent updater

De Microsoft Azure AD connect agent Updater-service wordt naast de Azure AD-proxy service voor wachtwoord beveiliging geïnstalleerd. Aanvullende configuratie is vereist om te kunnen werken met de Microsoft Azure AD connect agent Updater-Service:

* Als in uw omgeving een HTTP-proxy server wordt gebruikt, volgt u de richt lijnen die zijn opgegeven in [werken met bestaande on-premises proxy servers](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md).
* De Microsoft Azure AD de service connect agent Updater vereist ook de TLS 1,2-stappen die zijn opgegeven in de [TLS-vereisten](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements).

> [!WARNING]
> Azure AD-wachtwoord beveiligings proxy en Azure AD-toepassingsproxy verschillende versies van de Microsoft Azure AD connect agent Updater-service installeren. Dit is de reden waarom de instructies verwijzen naar de inhoud van de toepassings proxy. Deze verschillende versies zijn incompatibel wanneer ze naast elkaar worden geïnstalleerd en Hiermee wordt voor komen dat de agent Updater-Service contact opneemt met Azure voor software-updates, zodat u Azure AD-wachtwoord beveiligings proxy en toepassings proxy nooit op dezelfde computer hoeft te installeren.

## <a name="download-required-software"></a>Vereiste software downloaden

Er zijn twee vereiste installatie Programma's voor een on-premises Azure AD-implementatie voor wachtwoord beveiliging:

* Azure AD-agent voor wachtwoord beveiliging DC (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Azure AD-wachtwoord beveiligings proxy (*AzureADPasswordProtectionProxySetup.exe*)

Down load de installatie Programma's vanuit het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>De proxy service installeren en configureren

De Azure AD-proxy service voor wachtwoord beveiliging bevindt zich doorgaans op een lidserver in uw on-premises AD DS omgeving. Na de installatie communiceert de Azure AD-proxy service voor wachtwoord beveiliging met Azure AD om een kopie te onderhouden van de lijsten met globale en door de klant uitgesloten wacht woorden voor uw Azure AD-Tenant.

In de volgende sectie installeert u de Azure AD-wachtwoord beveiliging DC-agenten op domein controllers in uw on-premises AD DS omgeving. Deze DC-agents communiceren met de proxy service om de meest recente lijst met geblokkeerde wacht woorden te verkrijgen voor gebruik bij het verwerken van wachtwoord wijzigings gebeurtenissen binnen het domein.

Kies een of meer servers om de Azure AD-proxy service voor wachtwoord beveiliging te hosten. De volgende overwegingen zijn van toepassing op de server (s):

* Elke service kan alleen wachtwoord beleid bieden voor één forest. De hostmachine moet lid zijn van een domein in dat forest.
* U kunt de proxy service installeren in de hoofdmap of in het onderliggende domein of een combi natie hiervan.
* U hebt een netwerk verbinding nodig tussen ten minste één domein controller in elk domein van het forest en een proxy server voor wachtwoord beveiliging.
* U kunt de Azure AD-proxy service voor wachtwoord beveiliging uitvoeren op een domein controller om te testen, maar die domein controller vereist vervolgens Internet verbinding. Deze connectiviteit kan een beveiligings probleem zijn. We raden u aan deze configuratie alleen te testen.
* We raden ten minste twee Azure AD-wachtwoord beveiligings proxy servers per forest aan voor redundantie, zoals vermeld in de vorige sectie over [overwegingen met betrekking tot hoge Beschik baarheid](#high-availability-considerations).
* Het wordt niet ondersteund om de Azure AD-proxy service voor wachtwoord beveiliging uit te voeren op een alleen-lezen domein controller.

Voer de volgende stappen uit om de Azure AD-proxy service voor wachtwoord beveiliging te installeren:

1. Voer het software-installatie programma uit om de Azure AD-proxy service voor wachtwoord beveiliging te installeren `AzureADPasswordProtectionProxySetup.exe` .

    Opnieuw opstarten is niet vereist voor de software-installatie en kan worden geautomatiseerd met behulp van standaard MSI-procedures, zoals in het volgende voor beeld:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > De Windows Firewall-service moet worden uitgevoerd voordat u het `AzureADPasswordProtectionProxySetup.exe` pakket installeert om een installatie fout te voor komen.
    >
    > Als Windows Firewall is geconfigureerd om niet te worden uitgevoerd, kunt u de firewall service tijdelijk inschakelen en uitvoeren tijdens de installatie. De proxy software heeft na de installatie geen specifieke afhankelijkheid van Windows Firewall.
    >
    > Als u een firewall van derden gebruikt, moet deze nog steeds worden geconfigureerd om te voldoen aan de implementatie vereisten. Dit zijn onder andere het toestaan van binnenkomende toegang tot poort 135 en de proxy RPC-server poort. Zie de vorige sectie over [implementatie vereisten](#deployment-requirements)voor meer informatie.

1. De Azure AD-proxy software voor wachtwoord beveiliging bevat een nieuwe Power shell-module `AzureADPasswordProtection` . Met de volgende stappen worden verschillende cmdlets uitgevoerd vanuit deze Power shell-module.

    Als u deze module wilt gebruiken, opent u een Power shell-venster als beheerder en importeert u de nieuwe module als volgt:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Gebruik de volgende Power shell-opdracht om te controleren of de Azure AD-proxy service voor wachtwoord beveiliging wordt uitgevoerd:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Het resultaat moet de **status** *wordt uitgevoerd* weer geven.

1. De proxy service wordt uitgevoerd op de computer, maar heeft geen referenties om te communiceren met Azure AD. Registreer de Azure AD-proxy server voor wachtwoord beveiliging met Azure AD met behulp van de `Register-AzureADPasswordProtectionProxy` cmdlet.

    Deze cmdlet vereist *globale beheerder* of *beveiligings beheerder* referenties voor uw Azure-Tenant. Deze cmdlet moet ook worden uitgevoerd met een account met lokale Administrator bevoegdheden.

    Wanneer deze opdracht eenmaal is uitgevoerd, worden extra aanroepen ook uitgevoerd, maar zijn ze niet nodig.

    De `Register-AzureADPasswordProtectionProxy` cmdlet ondersteunt de volgende drie verificatie modi. De eerste twee modi ondersteunen Azure AD Multi-Factor Authentication, maar de derde modus niet.

    > [!TIP]
    > De eerste keer dat deze cmdlet wordt uitgevoerd voor een specifieke Azure-Tenant, kan er een merkbaar vertraging optreden. Als er geen fout wordt gerapporteerd, hoeft u zich geen zorgen te maken over deze vertraging.

     * Interactieve verificatie modus:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Deze modus werkt niet op Server Core-besturings systemen. Gebruik in plaats daarvan een van de volgende verificatie modi. Deze modus kan ook mislukken als verbeterde beveiliging van Internet Explorer is ingeschakeld. De tijdelijke oplossing is om die configuratie uit te scha kelen, de proxy te registreren en vervolgens weer in te scha kelen.

     * Verificatie modus apparaat-code:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Als u hierom wordt gevraagd, volgt u de koppeling om een webbrowser te openen en de verificatie code in te voeren.

     * Silent (op wacht woord gebaseerde) verificatie modus:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Deze modus mislukt als Azure AD Multi-Factor Authentication is vereist voor uw account. In dat geval gebruikt u een van de twee voor gaande verificatie modi, of gebruikt u in plaats daarvan een ander account waarvoor MFA niet vereist is.
        >
        > U ziet misschien ook MFA vereist als registratie van Azure-apparaten (die wordt gebruikt onder de voor vallen van Azure AD-wachtwoord beveiliging), is geconfigureerd om wereld wijd MFA te vereisen. Als tijdelijke oplossing voor deze vereiste kunt u een ander account gebruiken dat MFA ondersteunt met een van de twee vorige verificatie modi, of u kunt ook tijdelijk de MFA-vereiste voor de Azure Device Registration versoepelen.
        >
        > Als u deze wijziging wilt aanbrengen, zoekt en selecteert u **Azure Active Directory** in de Azure Portal en selecteert u vervolgens **apparaten > Apparaatinstellingen**. Set **vereisen multi-factor auth om apparaten toe te voegen** aan *Nee*. Zorg ervoor dat u deze instelling opnieuw configureert op *Ja* zodra de registratie is voltooid.
        >
        > U wordt aangeraden MFA-vereisten alleen voor test doeleinden te gebruiken.

    U hoeft momenteel niet de para meter *-ForestCredential* op te geven. deze is gereserveerd voor toekomstige functionaliteit.

    De registratie van de Azure AD-proxy service voor wachtwoord beveiliging is slechts één keer nodig in de levens duur van de service. Daarna voert de Azure AD-proxy service voor wachtwoord beveiliging automatisch andere nood zakelijk onderhoud uit.

1. Registreer nu het on-premises Active Directory forest met de benodigde referenties om te communiceren met Azure met behulp van de `Register-AzureADPasswordProtectionForest` Power shell-cmdlet.

    > [!NOTE]
    > Als er meerdere Azure AD-proxy servers voor wachtwoord beveiliging zijn geïnstalleerd in uw omgeving, maakt het niet uit welke proxy server u gebruikt om het forest te registreren.

    De cmdlet vereist ofwel *globale beheerder* of *beveiligings beheerder* referenties voor uw Azure-Tenant. Er zijn ook on-premises Active Directory Enter prise-beheerders bevoegdheden vereist. U moet deze cmdlet ook uitvoeren met een account met lokale Administrator bevoegdheden. Het Azure-account dat wordt gebruikt om het forest te registreren, kan afwijken van het on-premises Active Directory-account.
    
    Deze stap wordt eenmaal per forest uitgevoerd.

    De `Register-AzureADPasswordProtectionForest` cmdlet ondersteunt de volgende drie verificatie modi. De eerste twee modi ondersteunen Azure AD Multi-Factor Authentication, maar de derde modus niet.

    > [!TIP]
    > De eerste keer dat deze cmdlet wordt uitgevoerd voor een specifieke Azure-Tenant, kan er een merkbaar vertraging optreden. Als er geen fout wordt gerapporteerd, hoeft u zich geen zorgen te maken over deze vertraging.

     * Interactieve verificatie modus:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Deze modus werkt niet op Server Core-besturings systemen. Gebruik in plaats daarvan een van de volgende twee verificatie modi. Deze modus kan ook mislukken als verbeterde beveiliging van Internet Explorer is ingeschakeld. De tijdelijke oplossing is om die configuratie uit te scha kelen, het forest te registreren en vervolgens weer in te scha kelen.  

     * Verificatie modus apparaat-code:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Als u hierom wordt gevraagd, volgt u de koppeling om een webbrowser te openen en de verificatie code in te voeren.

     * Silent (op wacht woord gebaseerde) verificatie modus:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Deze modus mislukt als Azure AD Multi-Factor Authentication is vereist voor uw account. In dat geval gebruikt u een van de twee voor gaande verificatie modi, of gebruikt u in plaats daarvan een ander account waarvoor MFA niet vereist is.
        >
        > U ziet misschien ook MFA vereist als registratie van Azure-apparaten (die wordt gebruikt onder de voor vallen van Azure AD-wachtwoord beveiliging), is geconfigureerd om wereld wijd MFA te vereisen. Als tijdelijke oplossing voor deze vereiste kunt u een ander account gebruiken dat MFA ondersteunt met een van de twee vorige verificatie modi, of u kunt ook tijdelijk de MFA-vereiste voor de Azure Device Registration versoepelen.
        >
        > Als u deze wijziging wilt aanbrengen, zoekt en selecteert u **Azure Active Directory** in de Azure Portal en selecteert u vervolgens **apparaten > Apparaatinstellingen**. Set **vereisen multi-factor auth om apparaten toe te voegen** aan *Nee*. Zorg ervoor dat u deze instelling opnieuw configureert op *Ja* zodra de registratie is voltooid.
        >
        > U wordt aangeraden MFA-vereisten alleen voor test doeleinden te gebruiken.

       Deze voor beelden slagen alleen als de gebruiker die momenteel is aangemeld ook een Active Directory domein beheerder is voor het hoofd domein. Als dit niet het geval is, kunt u alternatieve domein referenties opgeven via de para meter *-ForestCredential* .

    Registratie van de Active Directory-forest is slechts eenmaal nodig in de levens duur van het forest. Daarna voeren de Azure AD-agenten voor wachtwoord beveiliging in het forest automatisch andere nood zakelijk onderhoud uit. Nadat `Register-AzureADPasswordProtectionForest` een forest is uitgevoerd, zijn er aanvullende aanroepen van de cmdlet geslaagd, maar dit is niet nodig.
    
    Hiervoor `Register-AzureADPasswordProtectionForest` moet ten minste één domein controller met Windows Server 2012 of hoger beschikbaar zijn in het domein van de Azure AD-wachtwoord beveiligings proxy server. De software van de DC-agent voor de Azure AD-wachtwoord beveiliging hoeft niet te worden geïnstalleerd op domein controllers vóór deze stap.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>De proxy service configureren om te communiceren via een HTTP-proxy

Als uw omgeving het gebruik van een specifieke HTTP-proxy vereist om met Azure te communiceren, gebruikt u de volgende stappen om de Azure AD-wachtwoord beveiligings service te configureren.

Maak een *AzureADPasswordProtectionProxy.exe.config* -bestand in de `%ProgramFiles%\Azure AD Password Protection Proxy\Service` map. Voeg de volgende inhoud toe:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Als voor uw HTTP-proxy verificatie is vereist, voegt u de *useDefaultCredentials* -code toe:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Vervang in beide gevallen door `http://yourhttpproxy.com:8080` het adres en de poort van uw specifieke HTTP-proxy server.

Als uw HTTP-proxy is geconfigureerd voor het gebruik van een autorisatie beleid, moet u toegang verlenen tot het Active Directory computer account van de computer die als host fungeert voor de proxy service voor wachtwoord beveiliging.

U wordt aangeraden de Azure AD-proxy service voor wachtwoord beveiliging te stoppen en opnieuw op te starten nadat u het *AzureADPasswordProtectionProxy.exe.config* -bestand hebt gemaakt of bijgewerkt.

De proxy service biedt geen ondersteuning voor het gebruik van specifieke referenties om verbinding te maken met een HTTP-proxy.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>De proxy service configureren om te Luis teren op een specifieke poort

De Azure AD-agent software voor wachtwoord beveiliging gebruikt RPC over TCP om te communiceren met de proxy service. De Azure AD-proxy service voor wachtwoord beveiliging luistert standaard naar een beschikbaar dynamisch RPC-eind punt. U kunt de service configureren om te Luis teren naar een specifieke TCP-poort, indien nodig door netwerk topologie of firewall vereisten in uw omgeving.

<a id="static" /></a>Als u de service wilt configureren om te worden uitgevoerd onder een statische poort, gebruikt u de `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet als volgt:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> U moet de Azure AD-proxy service voor wachtwoord beveiliging stoppen en opnieuw starten om deze wijzigingen van kracht te laten worden.

Als u de service wilt configureren voor uitvoering onder een dynamische poort, gebruikt u dezelfde procedure, maar stelt u *StaticPort* terug in op nul:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> U moet de Azure AD-proxy service voor wachtwoord beveiliging stoppen en opnieuw starten om deze wijzigingen van kracht te laten worden.

De Azure AD-proxy service voor wachtwoord beveiliging moet hand matig opnieuw worden opgestart na een wijziging in de poort configuratie. U hoeft de Azure AD-Agent service voor wachtwoord beveiliging niet opnieuw op domein controllers te starten nadat u deze configuratie wijzigingen hebt aangebracht.

Als u een query wilt uitvoeren voor de huidige configuratie van de service, gebruikt u de `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet zoals weer gegeven in het volgende voor beeld

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

In de volgende voorbeeld uitvoer ziet u dat de Azure AD-proxy service voor wachtwoord beveiliging een dynamische poort gebruikt:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>De DC-Agent service installeren

Voer het pakket uit om de Azure AD-Agent service voor wachtwoord beveiliging te installeren `AzureADPasswordProtectionDCAgentSetup.msi` .

U kunt de software-installatie automatiseren met behulp van standaard MSI-procedures, zoals wordt weer gegeven in het volgende voor beeld:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

De `/norestart` vlag kan worden wegge laten als u het installatie programma de computer liever automatisch opnieuw opstart.

Voor de software-installatie of het ongedaan maken van de installatie moet opnieuw worden opgestart. Deze vereiste is omdat wachtwoord filter-Dll's alleen worden geladen of verwijderd door het opnieuw opstarten.

De installatie van on-premises Azure AD-wachtwoord beveiliging is voltooid nadat de software van de DC-agent is geïnstalleerd op een domein controller en die computer opnieuw wordt opgestart. Er is geen andere configuratie vereist of mogelijk. Wachtwoord wijzigings gebeurtenissen voor de on-premises Dc's gebruiken de geconfigureerde lijst met verboden wacht woorden van Azure AD.

Zie [on-premises Azure AD-wachtwoord beveiliging inschakelen](howto-password-ban-bad-on-premises-operations.md)als u on-premises Azure AD-wachtwoord beveiliging wilt inschakelen voor de Azure portal of aangepaste verboden wacht woorden wilt configureren.

> [!TIP]
> U kunt de Azure AD-agent voor wachtwoord beveiliging van het domein installeren op een computer die nog geen domein controller is. In dit geval wordt de service gestart en uitgevoerd, maar blijft deze inactief totdat de computer is gepromoveerd tot een domein controller.

## <a name="upgrading-the-proxy-service"></a>De proxy service bijwerken

De Azure AD-proxy service voor wachtwoord beveiliging ondersteunt automatische upgrades. Bij automatische upgrade wordt de Microsoft Azure AD connect agent Updater-Service gebruikt, die naast de proxy service wordt geïnstalleerd. Automatische upgrade is standaard ingeschakeld en kan worden in-of uitgeschakeld met de `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.

De huidige instelling kan worden opgevraagd met behulp van de `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet. We raden u aan om de instelling voor automatische upgrades altijd in te scha kelen.

De `Get-AzureADPasswordProtectionProxy` cmdlet kan worden gebruikt om een query uit te kunnen maken van de software versie van alle geïnstalleerde Azure AD-wachtwoord beveiligings proxy servers in een forest.

### <a name="manual-upgrade-process"></a>Hand matig upgrade proces

Een hand matige upgrade wordt uitgevoerd door de meest recente versie van het installatie programma van de software uit te voeren `AzureADPasswordProtectionProxySetup.exe` . De nieuwste versie van de software is beschikbaar in het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=57071).

Het is niet vereist om de huidige versie van de Azure AD-proxy service voor wachtwoord beveiliging te verwijderen: het installatie programma voert een in-place upgrade uit. U hoeft niet opnieuw op te starten om de proxy service bij te werken. De software-upgrade kan worden geautomatiseerd met behulp van standaard MSI-procedures, zoals `AzureADPasswordProtectionProxySetup.exe /quiet` .

## <a name="upgrading-the-dc-agent"></a>De DC-agent bijwerken

Wanneer er een nieuwere versie van de Azure AD-agent software voor wachtwoord beveiliging beschikbaar is, wordt de upgrade uitgevoerd door de nieuwste versie van het software pakket uit te voeren `AzureADPasswordProtectionDCAgentSetup.msi` . De nieuwste versie van de software is beschikbaar in het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=57071).

Het is niet vereist om de huidige versie van de DC-agent software te verwijderen: het installatie programma voert een in-place upgrade uit. Opnieuw opstarten is altijd vereist bij het upgraden van de DC-agent software: deze vereiste wordt veroorzaakt door het basis gedrag van Windows.

De software-upgrade kan worden geautomatiseerd met behulp van standaard MSI-procedures, zoals `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` .

U kunt de vlag weglaten `/norestart` Als u het installatie programma de computer liever automatisch opnieuw opstart.

De `Get-AzureADPasswordProtectionDCAgent` cmdlet kan worden gebruikt om een query uit te kunnen maken van de software versie van alle geïnstalleerde Azure AD-wachtwoord beveiligings-DC-agents in een forest.

## <a name="next-steps"></a>Volgende stappen

Nu u de services hebt geïnstalleerd die u nodig hebt voor Azure AD-wachtwoord beveiliging op uw on-premises servers, [schakelt u on-premises Azure AD-wachtwoord beveiliging in het Azure Portal in](howto-password-ban-bad-on-premises-operations.md) om uw implementatie te volt ooien.
