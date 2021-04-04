---
title: RDG integreren met Azure AD MFA NPS-extensie-Azure Active Directory
description: Uw Extern bureaublad-gateway-infra structuur integreren met Azure AD MFA met de Network Policy Server extensie voor Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b0150c18f2c007ed104d34daacd49ab03131a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743374"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Uw Extern bureaublad-gateway-infra structuur integreren met behulp van de Network Policy Server (NPS)-extensie en Azure AD

In dit artikel vindt u informatie over het integreren van uw Extern bureaublad-gateway-infra structuur met Azure AD Multi-Factor Authentication (MFA) met behulp van de Network Policy Server (NPS)-extensie voor Microsoft Azure.

Met de uitbrei ding van de Network Policy Server (NPS) voor Azure kunnen klanten de verificatie van Remote Authentication Dial-In User Service (RADIUS)-client beveiligen met behulp van de Cloud [multi-factor Authentication (MFA)](./concept-mfa-howitworks.md)van Azure. Deze oplossing biedt verificatie in twee stappen voor het toevoegen van een tweede beveiligingslaag aan gebruikers aanmeldingen en trans acties.

In dit artikel vindt u stapsgewijze instructies voor het integreren van de NPS-infra structuur met Azure AD MFA met de NPS-extensie voor Azure. Hiermee wordt beveiligde verificatie ingeschakeld voor gebruikers die zich willen aanmelden bij een Extern bureaublad-gateway.

> [!NOTE]
> Dit artikel mag niet worden gebruikt met MFA-server implementaties en mag alleen worden gebruikt met Azure AD MFA-implementaties (op basis van de Cloud).

Network Policy and Access Services (NPS) biedt organisaties de mogelijkheid om het volgende te doen:

* Definieer centrale locaties voor het beheer en de controle van netwerk aanvragen door op te geven wie verbinding kan maken, welke tijden voor de duur van verbindingen zijn toegestaan, hoe lang de verbindingen zijn en welk beveiligings niveau clients moeten gebruiken om verbinding te maken, enzovoort. In plaats van deze beleids regels op elke VPN-of Extern bureaublad (RD)-gateway server op te geven, kunnen deze beleids regels eenmaal op een centrale locatie worden opgegeven. Het RADIUS-protocol biedt de gecentraliseerde verificatie, autorisatie en accounting (AAA).
* Netwerk toegangs beveiliging (NAP) client Health Policies instellen en afdwingen om te bepalen of apparaten onbeperkte of beperkte toegang tot netwerk bronnen worden verleend.
* Bieden een manier om verificatie en autorisatie af te dwingen voor toegang tot draadloze 802.1 x-compatibele en Ethernet-switches.

Organisaties gebruiken meestal NPS (RADIUS) om het beheer van VPN-beleid te vereenvoudigen en te centraliseren. Veel organisaties gebruiken echter ook NPS om het beheer van RD-bureaublad verbindings autorisatie beleid (RD Cap's) te vereenvoudigen en te centraliseren.

Organisaties kunnen NPS ook integreren met Azure AD MFA om de beveiliging te verbeteren en een hoge mate van naleving te bieden. Dit helpt ervoor te zorgen dat gebruikers verificatie in twee stappen tot stand brengen om zich aan te melden bij de Extern bureaublad-gateway. Gebruikers kunnen alleen toegang krijgen als ze hun combi natie van gebruikers naam en wacht woord opgeven en informatie die de gebruiker heeft in hun besturings element. Deze informatie moet worden vertrouwd en kan niet eenvoudig worden gedupliceerd, zoals een celadres, een vast nummer, een toepassing op een mobiel apparaat, enzovoort. RDG ondersteunt momenteel telefoon gesprekken en push meldingen van micro soft Authenticator-app-methoden voor twee ledige. Zie de sectie [bepalen welke verificatie methoden uw gebruikers kunnen gebruiken](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)voor meer informatie over ondersteunde verificatie methoden.

Vóór de beschik baarheid van de NPS-extensie voor Azure moesten klanten die verificatie in twee stappen willen implementeren voor geïntegreerde NPS-en Azure AD MFA-omgevingen, een afzonderlijke MFA-server configureren en onderhouden in de on-premises omgeving, zoals beschreven in [extern bureaublad-gateway en Azure multi-factor Authentication-server met RADIUS](howto-mfaserver-nps-rdg.md).

De beschik baarheid van de NPS-extensie voor Azure biedt organisaties de keuze om een on-premises op basis van MFA-oplossing of een op de cloud gebaseerde MFA-oplossing te implementeren voor de beveiliging van RADIUS-client verificatie.

## <a name="authentication-flow"></a>Verificatiestroom

Gebruikers kunnen alleen toegang krijgen tot netwerk bronnen via een Extern bureaublad-gateway als ze voldoen aan de voor waarden die zijn opgegeven in het beleid voor extern bureau blad-verbindingen (RD CAP) en één extern bureau blad-bron autorisatie beleid (RD RAP). RD Cap's opgeven wie gemachtigd is om verbinding te maken met extern bureau blad-gateways. RD Rap's Specificeer de netwerk bronnen, zoals externe Bureau bladen of externe apps, waarmee de gebruiker verbinding mag maken via de RD-gateway.

Een RD-gateway kan worden geconfigureerd voor het gebruik van een centraal beleids Archief voor RD Cap's. RD Rap's kan geen centraal beleid gebruiken, aangezien deze worden verwerkt op de RD-gateway. Een voor beeld van een RD-gateway dat is geconfigureerd voor het gebruik van een centraal beleids Archief voor RD Cap's is een RADIUS-client naar een andere NPS-server die fungeert als de centrale beleids opslag.

Wanneer de NPS-extensie voor Azure is geïntegreerd met de NPS en Extern bureaublad-gateway, is de geslaagde verificatie stroom als volgt:

1. De Extern bureaublad-gateway-server ontvangt een verificatie aanvraag van een extern-bureaublad gebruiker om verbinding te maken met een bron, zoals een Extern bureaublad-sessie. De Extern bureaublad-gateway-server fungeert als een RADIUS-client en converteert de aanvraag naar een RADIUS-Access-Request bericht en verzendt het bericht naar de RADIUS-server (NPS) waarop de NPS-extensie is geïnstalleerd.
1. De combi natie van gebruikers naam en wacht woord wordt in Active Directory gecontroleerd en de gebruiker is geverifieerd.
1. Als aan alle voor waarden die zijn opgegeven in de NPS-verbindings aanvraag en aan het netwerk beleid wordt voldaan (bijvoorbeeld het tijdstip van de dag of het lidmaatschap van groepslid maatschappen), activeert de NPS-extensie een aanvraag voor secundaire verificatie met Azure AD MFA.
1. Azure AD MFA communiceert met Azure AD, haalt de details van de gebruiker op en voert de secundaire verificatie uit met ondersteunde methoden.
1. Wanneer de MFA-Challenge is geslaagd, communiceert Azure AD MFA het resultaat naar de NPS-extensie.
1. De NPS-server, waar de uitbrei ding is geïnstalleerd, verzendt een RADIUS-Access-Accept bericht voor het RD CAP-beleid naar de Extern bureaublad-gateway-server.
1. De gebruiker krijgt toegang tot de aangevraagde netwerk bron via de RD-gateway.

## <a name="prerequisites"></a>Vereisten

In deze sectie vindt u informatie over de vereisten die nodig zijn voordat u Azure AD MFA integreert met de Extern bureaublad-gateway. Voordat u begint, moet u aan de volgende vereisten voldoen.  

* Extern bureaublad-services-infra structuur (RDS)
* Azure AD MFA-licentie
* Windows Server-software
* De rol NPS (Network Policy and Access Services)
* Azure Active Directory gesynchroniseerd met on-premises Active Directory
* Azure Active Directory GUID-ID

### <a name="remote-desktop-services-rds-infrastructure"></a>Extern bureaublad-services-infra structuur (RDS)

U moet een werkende Extern bureaublad-services-infra structuur (RDS) hebben. Als dat niet het geval is, kunt u deze infra structuur snel maken in azure met behulp van de volgende Quick Start-sjabloon: [create extern bureaublad sessie verzamelings implementatie](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Als u hand matig een on-premises RDS-infra structuur voor test doeleinden wilt maken, volgt u de stappen voor het implementeren van een.
**Meer informatie**: [RDS implementeren met Azure Quick](/windows-server/remote/remote-desktop-services/rds-in-azure) start en [eenvoudige implementatie van RDS-infra structuur](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-ad-mfa-license"></a>Azure AD MFA-licentie

Vereist is een licentie voor Azure AD MFA, die beschikbaar is via Azure AD Premium of andere bundels die deze bevatten. Op verbruik gebaseerde licenties voor Azure AD MFA, zoals per gebruiker of per verificatie licentie, zijn niet compatibel met de NPS-extensie. Zie [Azure AD-multi-factor Authentication ophalen](concept-mfa-licensing.md)voor meer informatie. Voor test doeleinden kunt u een proef abonnement gebruiken.

### <a name="windows-server-software"></a>Windows Server-software

Voor de NPS-extensie is Windows Server 2008 R2 SP1 of hoger vereist, terwijl de NPS-functie service is geïnstalleerd. Alle stappen in deze sectie zijn uitgevoerd met behulp van Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>De rol NPS (Network Policy and Access Services)

De functie Service NPS levert de RADIUS-server-en client functionaliteit en de Network Access Policy Health-Service. Deze rol moet worden geïnstalleerd op ten minste twee computers in uw infra structuur: de Extern bureaublad-gateway en een andere lidserver of domein controller. De rol is standaard al aanwezig op de computer die is geconfigureerd als de Extern bureaublad-gateway.  U moet ook de NPS-rol op ten minste op een andere computer, zoals een domein controller of lidserver, installeren.

Zie [een NAP-status beleids server installeren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10))voor meer informatie over het installeren van de NPS-functie service Windows Server 2012 of ouder. Zie [Aanbevolen procedures voor NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10))voor een beschrijving van best practices voor NPS, met inbegrip van de aanbeveling voor het installeren van NPS op een domein controller.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory gesynchroniseerd met on-premises Active Directory

On-premises gebruikers moeten worden gesynchroniseerd met Azure AD en zijn ingeschakeld voor MFA om de NPS-extensie te kunnen gebruiken. In deze sectie wordt ervan uitgegaan dat on-premises gebruikers worden gesynchroniseerd met Azure AD met behulp van AD Connect. Zie [uw on-premises Directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor meer informatie over Azure AD Connect.

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory GUID-ID

Als u de NPS-extensie wilt installeren, moet u de GUID van Azure AD weten. Hieronder vindt u instructies voor het zoeken van de GUID van de Azure AD.

## <a name="configure-multi-factor-authentication"></a>Multi-Factor Authentication configureren

Deze sectie bevat instructies voor het integreren van Azure AD MFA met de Extern bureaublad-gateway. Als beheerder moet u de Azure AD MFA-service configureren voordat gebruikers hun multi-factor devices of toepassingen zelf kunnen registreren.

Volg de stappen in aan [de slag met Azure AD-multi-factor Authentication in de Cloud](howto-mfa-getstarted.md) om MFA in te scha kelen voor uw Azure AD-gebruikers.

### <a name="configure-accounts-for-two-step-verification"></a>Accounts configureren voor verificatie in twee stappen

Zodra een account voor MFA is ingeschakeld, kunt u zich niet meer aanmelden bij bronnen waarvoor het MFA-beleid geldt, totdat u een vertrouwd apparaat hebt geconfigureerd om te gebruiken voor de tweede verificatie factor en u hebt geverifieerd met verificatie in twee stappen.

Volg de stappen in [Wat biedt Azure AD multi-factor Authentication voor mij?](../user-help/multi-factor-authentication-end-user-first-time.md) u kunt uw apparaten met uw gebruikers account goed begrijpen en configureren voor MFA.

> [!IMPORTANT]
> Het aanmeldings gedrag voor Extern bureaublad-gateway biedt geen optie om een verificatie code in te voeren met Azure AD Multi-Factor Authentication. U moet een gebruikers account configureren voor telefonische verificatie of de Microsoft Authenticator-app met push meldingen.
>
> Als een van deze twee verificatie methoden niet is geconfigureerd voor een gebruiker, kunnen ze de Azure AD-Multi-Factor Authentication uitdaging niet volt ooien en zich aanmelden bij de Extern bureaublad-gateway.

## <a name="install-and-configure-nps-extension"></a>NPS-extensie installeren en configureren

Deze sectie bevat instructies voor het configureren van de RDS-infra structuur voor het gebruik van Azure AD MFA voor client verificatie met de Extern bureaublad-gateway.

### <a name="acquire-azure-active-directory-tenant-id"></a>Azure Active Directory Tenant-ID ophalen

Als onderdeel van de configuratie van de NPS-extensie moet u beheerders referenties en de Azure AD-ID voor uw Azure AD-Tenant opgeven. Voer de volgende stappen uit om de Tenant-ID op te halen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als globale beheerder van de Azure-Tenant.
1. Selecteer in het menu Azure Portal **Azure Active Directory** of zoek en selecteer **Azure Active Directory** op een wille keurige pagina.
1. Op de pagina **overzicht** wordt de *informatie* over de Tenant weer gegeven. Selecteer het pictogram **kopiëren** naast de *Tenant-id*, zoals wordt weer gegeven in de volgende voorbeeld scherm afbeelding:

   ![De Tenant-ID ophalen uit de Azure Portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>De NPS-extensie installeren

Installeer de NPS-extensie op een server waarop de rol Network Policy and Access Services (NPS) is geïnstalleerd. Deze functie fungeert als de RADIUS-server voor uw ontwerp.

> [!IMPORTANT]
> Installeer de NPS-extensie niet op uw Extern bureaublad-gateway-server (RDG). De RDG-server gebruikt niet het RADIUS-protocol met de bijbehorende client, waardoor de extensie de MFA niet kan interpreteren en uitvoeren.
>
> Wanneer de RDG-server en de NPS-server met de NPS-extensie verschillende servers zijn, gebruikt RDG intern NPS om te communiceren met andere NPS-servers en wordt RADIUS als protocol gebruikt om correct te communiceren.

1. Down load de [NPS-extensie](https://aka.ms/npsmfa).
1. Kopieer het uitvoer bare installatie bestand (NpsExtnForAzureMfaInstaller.exe) naar de NPS-server.
1. Dubbel klik op de NPS-server op **NpsExtnForAzureMfaInstaller.exe**. Klik op **uitvoeren** als u hierom wordt gevraagd.
1. Bekijk in het dialoog venster NPS-uitbrei ding voor Azure AD MFA Setup de licentie voorwaarden van de software, Controleer **Ik ga akkoord met de licentie voorwaarden** en klik op **installeren**.
1. Klik in het dialoog venster NPS-extensie voor Azure AD MFA Setup op **sluiten**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Certificaten configureren voor gebruik met de NPS-extensie met behulp van een Power shell-script

Vervolgens moet u certificaten configureren voor gebruik door de NPS-extensie om te zorgen voor veilige communicatie en zekerheid. De NPS-onderdelen bevatten een Windows Power shell-script waarmee een zelfondertekend certificaat wordt geconfigureerd voor gebruik met NPS.

Met het script worden de volgende acties uitgevoerd:

* Hiermee maakt u een zelfondertekend certificaat
* De open bare sleutel van het certificaat koppelen aan Service-Principal in azure AD
* Hiermee slaat u het certificaat op in het archief van de lokale computer
* Hiermee wordt toegang verleend tot de persoonlijke sleutel van het certificaat aan de netwerk gebruiker
* Hiermee wordt Network Policy Server-service opnieuw gestart

Als u uw eigen certificaten wilt gebruiken, moet u de open bare sleutel van uw certificaat koppelen aan de Service-Principal in azure AD, enzovoort.

Als u het script wilt gebruiken, geeft u de extensie op met uw Azure AD-beheerders referenties en de Azure AD-Tenant-ID die u eerder hebt gekopieerd. Voer het script uit op elke NPS-server waarop u de NPS-extensie hebt geïnstalleerd. Ga daarna als volgt te werk:

1. Open een Windows Power shell-prompt met beheerders rechten.
1. Typ bij de Power shell-prompt `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` en druk op **Enter**.
1. Typ `.\AzureMfaNpsExtnConfigSetup.ps1` en druk op **Enter**. Met het script wordt gecontroleerd of de Azure Active Directory Power shell-module is geïnstalleerd. Als dat niet is geïnstalleerd, wordt de module door het script geïnstalleerd.

   ![AzureMfaNpsExtnConfigSetup.ps1 uitvoeren in azure AD Power shell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Nadat het script de installatie van de Power shell-module heeft gecontroleerd, wordt het dialoog venster Azure Active Directory Power shell-module weer gegeven. Voer in het dialoog venster uw Azure AD-beheerders referenties en-wacht woord in en klik op **Aanmelden**.

   ![Verifiëren met Azure AD in Power shell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Plak, wanneer u hierom wordt gevraagd, de *Tenant-id* die u eerder hebt gekopieerd naar het klem bord en druk op **Enter**.

   ![De Tenant-ID in Power shell invoeren](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Het script maakt een zelfondertekend certificaat en voert andere configuratie wijzigingen uit. De uitvoer moet lijken op de afbeelding die hieronder wordt weer gegeven.

   ![Uitvoer van Power shell met zelfondertekend certificaat](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>NPS-onderdelen configureren op Extern bureaublad-gateway

In deze sectie configureert u de Extern bureaublad-gateway verbindings autorisatie beleid en andere RADIUS-instellingen.

De verificatie stroom vereist dat RADIUS-berichten worden uitgewisseld tussen de Extern bureaublad-gateway en de NPS-server waarop de NPS-extensie is geïnstalleerd. Dit betekent dat u de RADIUS-client instellingen moet configureren op zowel Extern bureaublad-gateway als de NPS-server waarop de NPS-extensie is geïnstalleerd.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Extern bureaublad-gateway beleid voor verbindings autorisatie configureren voor het gebruik van een centraal archief

Extern bureaublad verbindings autorisatie beleid (RD Cap's) opgeven van de vereisten voor het maken van verbinding met een Extern bureaublad-gateway-server. RD Cap's kan lokaal worden opgeslagen (standaard) of kunnen worden opgeslagen in een centraal RD CAP archief waarop NPS wordt uitgevoerd. Als u de integratie van Azure AD MFA met RDS wilt configureren, moet u het gebruik van een centraal archief opgeven.

1. Open **Serverbeheer** op de RD-gateway-server.
1. Klik in het menu op **extra**, wijs **extern bureaublad-services** aan en klik vervolgens op **extern bureaublad-gatewaybeheer**.
1. Klik in de RD-gatewaybeheer met de rechter muisknop op **\[ Server naam \] (lokaal)** en klik op **Eigenschappen**.
1. Selecteer in het dialoog venster Eigenschappen het tabblad **RD CAP archief** .
1. Op het tabblad RD CAP archief selecteert u **centrale server waarop NPS wordt uitgevoerd**. 
1. In het veld **Geef een naam of IP-adres op voor de server waarop NPS wordt uitgevoerd** , typt u het IP-adres of de server naam van de server waarop u de NPS-extensie hebt geïnstalleerd.

   ![Voer de naam of het IP-adres van de NPS-server in](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klik op **Add**.
1. Voer in het dialoog venster **gedeeld geheim** een gedeeld geheim in en klik vervolgens op **OK**. Zorg ervoor dat u dit gedeelde geheim vastlegt en de record beveiligd opslaat.

   >[!NOTE]
   >Gedeeld geheim wordt gebruikt om een vertrouwens relatie tussen de RADIUS-servers en-clients tot stand te brengen. Maak een lang en complex geheim.
   >

   ![Een gedeeld geheim maken om een vertrouwens relatie tot stand te brengen](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Klik op **OK** om het dialoogvenster te sluiten.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>RADIUS-time-outwaarde configureren voor Extern bureaublad-gateway NPS

Om ervoor te zorgen dat er tijd is om referenties van gebruikers te valideren, verificatie in twee stappen uit te voeren, reacties te ontvangen en te reageren op RADIUS-berichten, moet u de waarde voor de RADIUS-time-out aanpassen.

1. Open Serverbeheer op de RD-gateway-server. Klik in het menu op **extra** en klik vervolgens op **Network Policy Server**.
1. Vouw **RADIUS-clients en-servers** uit in de NPS-console **(lokaal)** en selecteer **externe RADIUS-server**.

   ![Network Policy Server-beheer console met externe RADIUS-server](./media/howto-mfa-nps-extension-rdg/image12.png)

1. Dubbel klik in het detail venster op **TS-Gateway server groep**.

   >[!NOTE]
   >Deze RADIUS-server groep is gemaakt tijdens het configureren van de centrale server voor NPS-beleid. De RD-gateway RADIUS-berichten worden doorgestuurd naar deze server of groep servers, als er meerdere in de groep zijn.
   >

1. Selecteer in het dialoog venster **Eigenschappen van TS-Gateway server groep** het IP-adres of de naam van de NPS-server die u hebt geconfigureerd voor het opslaan van RD cap's en klik vervolgens op **bewerken**.

   ![Selecteer het IP-adres of de naam van de NPS-server die u eerder hebt geconfigureerd](./media/howto-mfa-nps-extension-rdg/image13.png)

1. Selecteer in het dialoog venster **RADIUS-server bewerken** het tabblad **taak verdeling** .
1. Op het tabblad **taak verdeling** , in het **aantal seconden zonder reactie voordat een aanvraag als een neergezet veld wordt beschouwd** , wijzigt u de standaard waarde van 3 in een waarde tussen 30 en 60 seconden.
1. Wijzig in het **aantal seconden tussen aanvragen wanneer de server wordt geïdentificeerd als niet-beschikbaar** veld de standaard waarde van 30 seconden op een waarde die gelijk is aan of groter is dan de waarde die u in de vorige stap hebt opgegeven.

   ![Instellingen voor de time-out van de RADIUS-server bewerken op het tabblad taak verdeling](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klik twee keer op **OK** om de dialoog vensters te sluiten.

### <a name="verify-connection-request-policies"></a>Beleid voor verbindings aanvragen controleren

Wanneer u de RD-gateway configureert voor het gebruik van een centraal beleids Archief voor het verbindings autorisatie beleid, wordt de RD-gateway standaard geconfigureerd voor het door sturen van CAP-aanvragen aan de NPS-server. De NPS-server waarop de Azure AD MFA-extensie is geïnstalleerd, verwerkt de RADIUS-toegangs aanvraag. De volgende stappen laten zien hoe u het standaard beleid voor verbindings aanvragen kunt controleren.  

1. Vouw op het RD-gateway in de NPS-console (lokaal) **beleid** uit en selecteer **beleid voor verbindings aanvragen**.
1. Dubbel klik op **TS-gateway autorisatie beleid**.
1. Klik in het dialoog venster **Eigenschappen van TS-gateway autorisatie beleid** op het tabblad **instellingen** .
1. Klik op het tabblad **instellingen** onder verbindings aanvraag door sturen op **verificatie**. RADIUS-client is geconfigureerd voor het door sturen van aanvragen voor authenticatie.

   ![Verificatie-instellingen configureren die de Server groep opgeven](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klik op **Annuleren**.

>[!NOTE]
> Voor meer informatie over het maken van een beleid voor verbindings aanvragen raadpleegt u het artikel, Configureer de documentatie voor [beleid voor verbindings aanvragen](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) voor dezelfde. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>NPS configureren op de server waarop de NPS-extensie is geïnstalleerd

De NPS-server waarop de NPS-uitbrei ding is geïnstalleerd, moet RADIUS-berichten kunnen uitwisselen met de NPS-server op de Extern bureaublad-gateway. Als u deze uitwisseling van berichten wilt inschakelen, moet u de NPS-onderdelen configureren op de server waarop de NPS-extensie service is geïnstalleerd.

### <a name="register-server-in-active-directory"></a>Server registreren bij Active Directory

Als u in dit scenario goed wilt werken, moet de NPS-server worden geregistreerd in Active Directory.

1. Open **Serverbeheer** op de NPS-server.
1. Klik in Serverbeheer op **extra** en klik vervolgens op **Network Policy Server**.
1. Klik in de Network Policy Server-console met de rechter muisknop op **NPS (lokaal)** en klik vervolgens op **Server registreren in Active Directory**.
1. Klik twee keer op **OK** .

   ![De NPS-server registreren in Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Houd de console geopend voor de volgende procedure.

### <a name="create-and-configure-radius-client"></a>RADIUS-client maken en configureren

De Extern bureaublad-gateway moet worden geconfigureerd als een RADIUS-client voor de NPS-server.

1. Klik met de rechter muisknop op **RADIUS-clients** op de NPS-server waarop de NPS-extensie is geïnstalleerd, in de NPS- console **(lokale)** .

   ![Een nieuwe RADIUS-client maken in de NPS-console](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Geef in het dialoog venster **nieuwe RADIUS-client** een beschrijvende naam op, zoals _Gateway_, en het IP-adres of de DNS-naam van de Extern bureaublad-gateway-server.
1. In het **gedeelde geheim** en de velden **Bevestig het gedeelde geheim** voert u hetzelfde geheim in dat u eerder hebt gebruikt.

   ![Een beschrijvende naam en IP-of DNS-adres configureren](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klik op **OK** om het dialoog venster nieuwe RADIUS-client te sluiten.

### <a name="configure-network-policy"></a>Netwerk beleid configureren

U herinnert dat de NPS-server met de Azure AD MFA-extensie de aangewezen centrale beleids opslag voor het verbindings autorisatie beleid (CAP) is. Daarom moet u een CAP op de NPS-server implementeren om geldige verbindings aanvragen te autoriseren.  

1. Open op de NPS-server de NPS-console (lokaal), vouw **beleid** uit en klik op **netwerk beleidsregels**.
1. Klik met de rechter muisknop op **verbindingen met andere toegangs servers** en klik op **beleid dupliceren**.

   ![De verbinding met andere toegangs servers beleid dupliceren](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Klik met de rechter muisknop op **kopie van verbindingen met andere toegangs servers** en klik op **Eigenschappen**.
1. Voer in het dialoog venster **kopie van verbindingen naar andere toegangs servers** in het vak **beleids naam** een geschikte naam in, zoals _RDG_CAP_. Controleer of **beleid is ingeschakeld** en selecteer **toegang verlenen**. Selecteer eventueel in het **type netwerk toegangs server** **extern bureaublad-gateway**, of u kunt het wijzigen als niet **opgegeven**.

   ![Het beleid een naam geven, toegang inschakelen en verlenen](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klik op het tabblad **beperkingen** en controleer **clients toestaan verbinding te maken zonder te onderhandelen over een verificatie methode**.

   ![Verificatie methoden wijzigen zodat clients verbinding kunnen maken](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Klik eventueel op het tabblad **voor waarden** en voeg voor waarden toe waaraan moet worden voldaan om de verbinding te machtigen, bijvoorbeeld voor lidmaatschap van een specifieke Windows-groep.

   ![Geef desgewenst verbindings voorwaarden op](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klik op **OK**. Wanneer u wordt gevraagd om het bijbehorende Help-onderwerp weer te geven, klikt u op **Nee**.
1. Zorg ervoor dat het nieuwe beleid boven aan de lijst staat, dat het beleid is ingeschakeld en dat er toegang wordt verleend.

   ![Verplaats uw beleid naar de bovenkant van de lijst](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Configuratie controleren

Als u de configuratie wilt controleren, moet u zich aanmelden bij de Extern bureaublad-gateway met een geschikte RDP-client. Zorg ervoor dat u een account gebruikt dat is toegestaan door uw verbindings autorisatie beleid en is ingeschakeld voor Azure AD MFA.

Zoals in de onderstaande afbeelding wordt weer gegeven, kunt u de pagina **webtoegang extern bureaublad** gebruiken.

![Testen in Extern bureaublad Web Access](./media/howto-mfa-nps-extension-rdg/image25.png)

Als u uw referenties voor primaire authenticatie hebt ingevoerd, wordt in het dialoog venster Extern bureaublad verbinding maken de status van het initiëren van de externe verbinding weer gegeven, zoals hieronder wordt aangegeven. 

Als u met de secundaire verificatie methode die u eerder hebt geconfigureerd in azure AD MFA hebt geverifieerd, bent u verbonden met de resource. Als de secundaire verificatie mislukt, hebt u echter geen toegang tot de bron. 

![Verbinding met extern bureaublad het starten van een externe verbinding](./media/howto-mfa-nps-extension-rdg/image26.png)

In het onderstaande voor beeld wordt de verificator-app op een Windows Phone gebruikt om de secundaire verificatie te bieden.

![Voor beeld Windows Phone Authenticator-app met verificatie](./media/howto-mfa-nps-extension-rdg/image27.png)

Zodra u bent geverifieerd met de secundaire verificatie methode, bent u als normaal aangemeld bij de Extern bureaublad-gateway. Omdat u echter een secundaire verificatie methode moet gebruiken met behulp van een mobiele app op een vertrouwd apparaat, is het aanmeldings proces veiliger dan het geval zou zijn.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Logboeken Logboeken weer geven voor geslaagde aanmeldings gebeurtenissen

Als u de geslaagde aanmeldings gebeurtenissen in de Windows Logboeken-Logboeken wilt weer geven, kunt u de volgende Windows Power shell-opdracht geven om een query uit te voeren op de Windows Terminal Services-en Windows-beveiligings Logboeken.

Gebruik de volgende Power shell-opdrachten voor het uitvoeren van een query op geslaagde aanmeldings gebeurtenissen in de gateway-operationele logboeken _(gebeurtenis Viewer\Applications en services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Met deze opdracht worden Windows-gebeurtenissen weer gegeven die aangeven dat de gebruiker voldoet aan de vereisten van het bron autorisatie beleid (RD RAP) en of toegang is verleend.

![Gebeurtenissen weer geven met Power shell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Met deze opdracht worden de gebeurtenissen weer gegeven die worden weer gegeven wanneer de gebruiker aan de vereisten voor verbindings autorisatie beleid voldoet.

![het verbindings autorisatie beleid weer geven met behulp van Power shell](./media/howto-mfa-nps-extension-rdg/image29.png)

U kunt dit logboek ook bekijken en filteren op gebeurtenis-Id's, 300 en 200. Als u geslaagde aanmeldings gebeurtenissen in de logboeken van de beveiligings logboeken wilt opvragen, gebruikt u de volgende opdracht:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Deze opdracht kan worden uitgevoerd op de centrale NPS of de RD-gateway-server.

![Voor beeld van geslaagde aanmeldings gebeurtenissen](./media/howto-mfa-nps-extension-rdg/image30.png)

U kunt ook het beveiligings logboek of de aangepaste weer gave voor services voor netwerk beleid en-toegang bekijken, zoals hieronder wordt weer gegeven:

![Services voor netwerk beleid en-toegang Logboeken](./media/howto-mfa-nps-extension-rdg/image31.png)

Op de server waarop u de NPS-extensie voor Azure AD MFA hebt geïnstalleerd, vindt u Logboeken toepassings logboeken die specifiek zijn voor de uitbrei ding op het _Logs\Microsoft\AzureMfa van toepassingen en services_.

![AuthZ-toepassings Logboeken Logboeken](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Probleemoplossings gids

Als de configuratie niet werkt zoals verwacht, moet u eerst controleren of de gebruiker is geconfigureerd voor het gebruik van Azure AD MFA. Laat de gebruiker verbinding maken met de [Azure Portal](https://portal.azure.com). Als gebruikers worden gevraagd om secundaire verificatie en kunnen worden geverifieerd, kunt u een onjuiste configuratie van Azure AD MFA elimineren.

Als Azure AD MFA werkt voor de gebruiker (s), moet u de relevante gebeurtenis logboeken controleren. Dit zijn onder andere de beveiligings gebeurtenis, het functioneren van de gateway en de Azure AD MFA-logboeken die in de vorige sectie worden besproken.

Hieronder vindt u een voorbeeld uitvoer van een beveiligings logboek met een mislukte aanmeldings gebeurtenis (gebeurtenis-ID 6273).

![Voor beeld van een mislukte aanmeldings gebeurtenis](./media/howto-mfa-nps-extension-rdg/image33.png)

Hieronder vindt u een verwante gebeurtenis uit de AzureMFA-logboeken:

![Voor beeld van Azure AD MFA-logboek in Logboeken](./media/howto-mfa-nps-extension-rdg/image34.png)

Als u geavanceerde opties voor probleem oplossing wilt uitvoeren, raadpleegt u de logboek bestanden van de NPS-data base-indeling waarin de NPS-service is geïnstalleerd. Deze logboek bestanden worden gemaakt in de map _%systemroot%\System32\Logs_ als tekst bestanden met door komma's gescheiden waarden.

Zie [NPS data base Format-logboek bestanden interpreteren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10))voor een beschrijving van deze logboek bestanden. De vermeldingen in deze logboek bestanden kunnen lastig zijn om te interpreteren zonder ze te importeren in een spread sheet of een Data Base. U kunt verschillende IAS-parsers online vinden om u te helpen bij het interpreteren van de logboek bestanden.

In de onderstaande afbeelding ziet u de uitvoer van een gedownloade [shareware-toepassing](https://www.deepsoftware.com/iasviewer).

![Voor beeld van shareware-app IAS-parser](./media/howto-mfa-nps-extension-rdg/image35.png)

Ten slotte kunt u een protocol analyse gebruiken, zoals [micro soft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide), voor extra probleemoplossings opties.

In de onderstaande afbeelding van micro soft Message Analyzer wordt het netwerk verkeer weer gegeven dat is gefilterd op het RADIUS-protocol dat de gebruikers naam **CONTOSO\AliceC** bevat.

![Micro soft Message Analyzer met gefilterd verkeer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Volgende stappen

[Azure AD-Multi-Factor Authentication ophalen](concept-mfa-licensing.md)

[Extern bureaublad-gateway en Azure Multi-Factor Authentication-server met behulp van RADIUS](howto-mfaserver-nps-rdg.md)

[Uw on-premises directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)