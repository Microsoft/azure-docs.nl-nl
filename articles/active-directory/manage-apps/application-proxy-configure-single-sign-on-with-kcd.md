---
title: Op Kerberos gebaseerde eenmalige aanmelding (SSO) in Azure Active Directory met toepassings proxy
description: In dit artikel wordt beschreven hoe u eenmalige aanmelding kunt bieden met behulp van Azure Active Directory-toepassingsproxy.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: a4fdd8d16854e76cdf20d27a6048694c01de8499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99253880"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-sso-to-your-apps-with-application-proxy"></a>Beperkte Kerberos-overdracht voor eenmalige aanmelding (SSO) voor uw apps met toepassings proxy

U kunt eenmalige aanmelding bieden voor on-premises toepassingen die zijn gepubliceerd via toepassings proxy die zijn beveiligd met geïntegreerde Windows-verificatie. Voor deze toepassingen is een Kerberos-ticket vereist voor toegang. Toepassings proxy maakt gebruik van Kerberos-beperkte delegering (KCD) ter ondersteuning van deze toepassingen. 

U kunt eenmalige aanmelding inschakelen voor uw toepassingen met behulp van geïntegreerde Windows-authenticatie (IWA) door de machtigingen voor toepassings proxy connectors in Active Directory te geven om gebruikers te imiteren. De connectors gebruiken deze machtiging voor het verzenden en ontvangen van tokens namens hen.

## <a name="how-single-sign-on-with-kcd-works"></a>Hoe eenmalige aanmelding met beperkte Kerberos-delegering werkt
In dit diagram wordt de stroom uitgelegd wanneer een gebruiker toegang probeert te krijgen tot een on-premises toepassing die gebruikmaakt van IWA.

![Micro soft AAD-verificatie stroom diagram](./media/application-proxy-configure-single-sign-on-with-kcd/authdiagram.png)

1. De gebruiker voert de URL in om toegang te krijgen tot de on-premises toepassing via Application Proxy.
2. Application Proxy leidt de aanvraag om naar Azure AD-verificatieservices om vooraf een verificatie uit te voeren. Op dit punt past Azure AD de betreffende verificatie en het relevante verificatiebeleid, zoals meervoudige verificatie, toe. Als de gebruiker is gevalideerd, wordt door Azure AD een token gemaakt en verzonden naar de gebruiker.
3. De gebruiker geeft het token door aan Application Proxy.
4. De toepassings proxy valideert het token en haalt de UPN (User Principal Name) van de toepassing op, waarna de connector de UPN en de SPN (Service Principal Name) ophaalt via een dubbel geverifieerd beveiligd kanaal.
5. De connector voert onderhandelingen met Kerberos-beperkte delegering (KCD) uit met de on-premises AD en imiteert de gebruiker om een Kerberos-token aan de toepassing te krijgen.
6. Active Directory verzendt het Kerberos-token voor de toepassing naar de connector.
7. De connector verzendt de oorspronkelijke aanvraag naar de toepassings server met behulp van het Kerberos-token dat is ontvangen van AD.
8. De toepassing stuurt het antwoord naar de connector, die vervolgens wordt teruggestuurd naar de service toepassings proxy en uiteindelijk aan de gebruiker.

## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat met eenmalige aanmelding voor IWA-toepassingen, moet u ervoor zorgen dat uw omgeving klaar is met de volgende instellingen en configuraties:

* Uw apps, zoals share point web apps, zijn ingesteld op het gebruik van geïntegreerde Windows-authenticatie. Zie [Enable support for Kerberos Authentication](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759186(v=ws.11))(Engelstalig) voor meer informatie, of voor share point Zie [Kerberos-verificatie plannen in share point 2013](/SharePoint/security-for-sharepoint-server/kerberos-authentication-planning).
* Al uw apps hebben [service-principal-namen](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* De server waarop de-connector wordt uitgevoerd en de server waarop de app wordt uitgevoerd, zijn lid van het domein en een deel van hetzelfde domein of vertrouwende domeinen. Zie [een computer toevoegen aan een domein](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dd807102(v=ws.11))voor meer informatie over het toevoegen van domeinen aan een domein.
* De server die de connector uitvoert, heeft toegang om het kenmerk TokenGroupsGlobalAndUniversal voor gebruikers te lezen. Deze standaard instelling is mogelijk van invloed op de beveiligings beveiliging van de omgeving.

### <a name="configure-active-directory"></a>Active Directory configureren
De configuratie van de Active Directory varieert, afhankelijk van of de connector van de toepassings proxy en de toepassings server zich in hetzelfde domein bevinden.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Connector en toepassings server in hetzelfde domein
1. Ga in Active Directory naar **hulpprogram ma's**  >  **gebruikers en computers**.
2. Selecteer de server waarop de connector wordt uitgevoerd.
3. Klik met de rechter muisknop en selecteer **Eigenschappen**  >  **delegeren**.
4. Selecteer **Deze computer mag alleen aan opgegeven services delegeren**. 
5. Selecteer **Elk verificatieprotocol gebruiken**.
6. Onder **Services waaraan dit account gedelegeerde referenties kan geven** , voegt u de waarde voor de SPN-identiteit van de toepassings server toe. Hierdoor kan de Application proxy-connector gebruikers in AD imiteren op basis van de toepassingen die in de lijst zijn gedefinieerd.

   ![Scherm opname van connector-SVR venster Eigenschappen](./media/application-proxy-configure-single-sign-on-with-kcd/properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Connector en toepassings server in verschillende domeinen
1. Zie [Kerberos-beperkte delegering over domeinen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831477(v=ws.11))voor een lijst met vereisten voor het werken met KCD in verschillende domeinen.
2. Gebruik de `principalsallowedtodelegateto` eigenschap van het service account (computer of toegewezen domein gebruikers account) van de webtoepassing om Kerberos-verificatie overdracht van de toepassings proxy (connector) in te scha kelen. De toepassings server wordt uitgevoerd in de context van `webserviceaccount` en de overdracht van de server is `connectorcomputeraccount` . Voer de onderstaande opdrachten uit op een domein controller (waarop Windows Server 2012 R2 of hoger wordt uitgevoerd) in het domein van `webserviceaccount` . Gebruik platte namen (niet-UPN) voor beide accounts.

   Als het `webserviceaccount` een computer account is, gebruikt u deze opdrachten:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Als het `webserviceaccount` een gebruikers account is, gebruikt u deze opdrachten:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Eenmalige aanmelding configureren 
1. Publiceer uw toepassing volgens de instructies in [toepassingen publiceren met toepassings proxy](application-proxy-add-on-premises-application.md). Zorg ervoor dat u **Azure Active Directory** selecteert als **methode voor verificatie** vooraf.
2. Als uw toepassing wordt weer gegeven in de lijst met bedrijfs toepassingen, selecteert u deze en klikt u op **eenmalige aanmelding**.
3. Stel de modus voor eenmalige aanmelding in op **geïntegreerde Windows-verificatie**.  
4. Voer de **interne toepassings-SPN** van de toepassings server in. In dit voor beeld is de SPN voor de gepubliceerde toepassing http/www. contoso. com. Deze SPN moet zich bevinden in de lijst met services waarnaar de connector gedelegeerde referenties kan presen teren. 
5. Kies de **Gedelegeerde aanmeldingsidentiteit** voor de connector die u namens uw gebruikers wilt gebruiken. Zie [werken met verschillende on-premises en Cloud-identiteiten](#working-with-different-on-premises-and-cloud-identities) voor meer informatie

   ![Geavanceerde toepassings configuratie](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  

## <a name="sso-for-non-windows-apps"></a>Eenmalige aanmelding voor niet-Windows-apps

De Kerberos-delegatie stroom in azure AD-toepassingsproxy gestart wanneer de gebruiker door Azure AD wordt geverifieerd in de Cloud. Zodra de aanvraag on-premises arriveert, verzendt de Azure AD-toepassingsproxy-connector een Kerberos-ticket namens de gebruiker door interactie met de lokale Active Directory. Dit proces wordt aangeduid als Kerberos-beperkte delegering (KCD). 

In de volgende fase wordt een aanvraag verzonden naar de back-end-toepassing met dit Kerberos-ticket. 

Er zijn verschillende mechanismen die definiëren hoe het Kerberos-ticket in dergelijke aanvragen worden verzonden. De meeste niet-Windows-servers verwachten deze te ontvangen in een vorm van het SPNEGO-token. Dit mechanisme wordt ondersteund op Azure AD-toepassingsproxy, maar is standaard uitgeschakeld. Een connector kan worden geconfigureerd voor het SPNEGO-of standaard-Kerberos-token, maar niet voor beide.

Als u een connector computer voor SPNEGO configureert, moet u ervoor zorgen dat alle andere connectors in die connector groep ook zijn geconfigureerd met SPNEGO. Toepassingen die een standaard Kerberos-token verwachten, moeten worden gerouteerd via andere connectors die niet zijn geconfigureerd voor SPNEGO. Sommige webtoepassingen accepteren beide indelingen zonder dat er wijzigingen in de configuratie zijn vereist. 
 

SPNEGO inschakelen:

1. Open een opdracht prompt die wordt uitgevoerd als Administrator.
2. Voer vanaf de opdracht prompt de volgende opdrachten uit op de connector servers die SPNEGO nodig hebben.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Niet-Windows-apps hebben meestal gebruikers namen of SAM-account namen in plaats van e-mail adressen van het domein. Als die situatie van toepassing is op uw toepassingen, moet u het veld gedelegeerde aanmeldings-id configureren om uw Cloud identiteiten te verbinden met uw toepassings identiteiten. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Werken met verschillende on-premises en Cloud-identiteiten
Toepassings proxy gaat ervan uit dat gebruikers exact dezelfde identiteit hebben in de Cloud en on-premises. Maar in sommige omgevingen, vanwege bedrijfs beleid of toepassings afhankelijkheden, moeten organisaties wellicht alternatieve Id's gebruiken om zich aan te melden. In dergelijke gevallen kunt u KCD nog steeds gebruiken voor eenmalige aanmelding. Configureer een **gedelegeerde aanmeldings-id** voor elke toepassing om aan te geven welke identiteit moet worden gebruikt bij het uitvoeren van eenmalige aanmelding.  

Met deze mogelijkheid kunnen veel organisaties die verschillende on-premises en Cloud-identiteiten hebben, SSO van de Cloud naar on-premises apps hebben zonder dat de gebruikers verschillende gebruikers namen en wacht woorden hoeven in te voeren. Dit omvat organisaties die:

* Meerdere domeinen intern ( joe@us.contoso.com , joe@eu.contoso.com ) en één domein in de Cloud () hebben joe@contoso.com .
* Hebben intern een niet-Routeer bare domein naam ( joe@contoso.usa ) en een legal in de Cloud.
* Domein namen niet intern gebruiken (Joe)
* Gebruik verschillende aliassen on-premises en in de Cloud. Bijvoorbeeld joe-johns@contoso.com versus joej@contoso.com  

Met toepassings proxy kunt u selecteren welke identiteit u wilt gebruiken om het Kerberos-ticket te verkrijgen. Deze instelling is per toepassing. Sommige van deze opties zijn geschikt voor systemen die geen e-mailadres indeling accepteren, anderen zijn ontworpen voor alternatieve aanmelding.

![Scherm opname van de para meter gedelegeerde aanmeldings-id](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Als gedelegeerde aanmeldings-id wordt gebruikt, is de waarde mogelijk niet uniek in alle domeinen of forests in uw organisatie. U kunt dit probleem vermijden door deze toepassingen twee keer te publiceren met twee verschillende connector groepen. Aangezien elke toepassing een andere gebruikers doelgroep heeft, kunt u de connectors koppelen aan een ander domein.

Als de **naam van het on-premises SAM-account** wordt gebruikt voor de aanmeldings identiteit, moet de computer die als host fungeert voor de connector worden toegevoegd aan het domein waarin het gebruikers account zich bevindt.

### <a name="configure-sso-for-different-identities"></a>Eenmalige aanmelding voor verschillende identiteiten configureren
1. Configureer Azure AD Connect instellingen zodat de hoofd identiteit het e-mail adres (e-mail) is. Dit wordt gedaan als onderdeel van het aanpassings proces door het veld UPN ( **User Principal name** ) in de synchronisatie-instellingen te wijzigen. Deze instellingen bepalen ook hoe gebruikers zich aanmelden bij Office365, Windows10-apparaten en andere toepassingen die Azure AD gebruiken als hun identiteits opslag.  
   ![Scherm opname van gebruikers voor het identificeren van de vervolg keuzelijst User Principal name](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Selecteer in de configuratie-instellingen van de toepassing die u wilt wijzigen de **gedelegeerde aanmeldings-id** die moet worden gebruikt:

   * Principal-naam van gebruiker (bijvoorbeeld joe@contoso.com )
   * Alternatieve Principal-naam van gebruiker (bijvoorbeeld joed@contoso.local )
   * Het deel van de gebruikers naam van de principal-naam van de gebruiker (bijvoorbeeld Joe)
   * Het deel van de gebruikers naam van een alternatieve Principal-naam van gebruiker (bijvoorbeeld joed)
   * Naam van on-premises SAM-account (is afhankelijk van de configuratie van de domein controller)

### <a name="troubleshooting-sso-for-different-identities"></a>Problemen met SSO voor verschillende identiteiten oplossen
Als er een fout optreedt in het SSO-proces, wordt dit weer gegeven in het gebeurtenis logboek van de connector computer, zoals wordt uitgelegd in [probleem oplossing](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Maar in sommige gevallen wordt de aanvraag verzonden naar de back-end-toepassing terwijl deze toepassing in verschillende HTTP-antwoorden antwoordt. Het oplossen van deze problemen moet beginnen met het onderzoeken van het gebeurtenis nummer 24029 op de connector computer in het gebeurtenis logboek van de toepassings proxy sessie. De gebruikers-id die is gebruikt voor delegering wordt weer gegeven in het veld gebruiker binnen de gebeurtenis Details. Als u het sessie logboek wilt inschakelen, selecteert u **analyse logboeken en fout opsporing weer geven** in het menu Beeld van Logboeken.

## <a name="next-steps"></a>Volgende stappen

* [Een toepassings proxy toepassing configureren voor het gebruik van beperkte Kerberos-delegering](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Problemen met toepassingsproxy oplossen (Engelstalig artikel)](application-proxy-troubleshoot.md)