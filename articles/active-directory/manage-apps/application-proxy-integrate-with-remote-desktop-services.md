---
title: Extern bureaublad publiceren met Azure Active Directory-toepassingsproxy
description: Bevat informatie over het configureren van een app-proxy met Extern bureaublad-services (RDS)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: bfe8af8c30bbc2bc66c363fbd85f6764a48c28a1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488065"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Remote Desktop publiceren met Azure AD-toepassingsproxy

Extern bureaublad service en Azure AD-toepassingsproxy samen werken om de productiviteit te verbeteren van werk nemers die zich niet op het bedrijfs netwerk bevinden. 

De doel groep voor dit artikel is:
- Klanten van de huidige toepassings proxy die meer toepassingen willen aanbieden aan hun eind gebruikers door on-premises toepassingen te publiceren via Extern bureaublad-services.
- Huidige Extern bureaublad-services klanten die de kwets baarheid van hun implementatie willen beperken door gebruik te maken van Azure AD-toepassingsproxy. Dit scenario bevat een set verificatie met twee stappen en voorwaardelijke toegangs controles voor RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Hoe toepassings proxy in de standaard-RDS-implementatie past

Een standaard-RDS-implementatie omvat diverse Extern bureaublad functie Services die worden uitgevoerd op Windows Server. Er zijn meerdere implementatie opties voor het bekijken van de [extern bureaublad-services architectuur](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture). In tegens telling tot andere opties voor RDS-implementatie, heeft de [RDS-implementatie met Azure AD-toepassingsproxy](/windows-server/remote/remote-desktop-services/Desktop-hosting-logical-architecture) (weer gegeven in het volgende diagram) een permanente uitgaande verbinding van de server waarop de connector service wordt uitgevoerd. Andere implementaties verlaten open binnenkomende verbindingen via een load balancer.

![Toepassings proxy bevindt zich tussen de RDS-VM en het open bare Internet](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

In een RDS-implementatie worden de extern bureau blad-webfunctie en de RD-gateway-rol op internet computers uitgevoerd. Deze eind punten worden weer gegeven om de volgende redenen:
- RD Web biedt de gebruiker een openbaar eind punt om zich aan te melden en de verschillende on-premises toepassingen en Desk tops te bekijken waartoe ze toegang hebben. Wanneer u een resource selecteert, wordt er een RDP-verbinding gemaakt met behulp van de systeem eigen app op het besturings systeem.
- RD-gateway komt in de foto wanneer een gebruiker de RDP-verbinding heeft gestart. Het RD-gateway verwerkt het versleutelde RDP-verkeer via internet en zet het om naar de on-premises server waarmee de gebruiker verbinding maakt. In dit scenario is het verkeer dat de RD-gateway ontvangt, afkomstig van de Azure-AD-toepassingsproxy.

>[!TIP]
>Als u RDS nog niet eerder hebt geïmplementeerd of meer informatie wilt hebben voordat u begint, leert u hoe u [RDS naadloos kunt implementeren met Azure Resource Manager en Azure Marketplace](/windows-server/remote/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Vereisten

- Zowel het extern bureau blad-web als de RD-gateway-eind punten moeten zich op dezelfde computer bevinden en met een gemeen schappelijke basis. Extern bureau blad-web en-RD-gateway worden gepubliceerd als één toepassing met toepassings proxy, zodat u een eenmalige aanmelding kunt hebben tussen de twee toepassingen.

- U moet al een [RDS](/windows-server/remote/remote-desktop-services/rds-in-azure)-toepassing hebben geïmplementeerd en een [toepassings proxy hebben ingeschakeld](application-proxy-add-on-premises-application.md).

- Uw eind gebruikers moeten een compatibele browser gebruiken om verbinding te maken met RD Web of de extern bureau blad-webclient. Zie [ondersteuning voor client configuraties](#support-for-other-client-configurations)voor meer informatie.

- Bij het publiceren van extern bureau blad-webtoepassingen, is het raadzaam om dezelfde interne en externe FQDN te gebruiken. Als de interne en externe FQDN-namen verschillend zijn, moet u de vertaling van de aanvraag header uitschakelen om te voor komen dat de client ongeldige koppelingen ontvangt.

- Als u RD Web op Internet Explorer gebruikt, moet u de ActiveX-invoeg toepassing voor RDS inschakelen.

- Als u de extern bureau blad-webclient gebruikt, moet u de connector versie van de toepassings proxy [1.5.1975 of hoger](./application-proxy-release-version-history.md)gebruiken.

- Gebruikers kunnen alleen verbinding maken met resources die in het deel venster **RemoteApp en Bureau bladen** zijn gepubliceerd voor de pre-verificatie stroom van Azure AD. Gebruikers kunnen geen verbinding maken met een bureau blad met behulp van het deel venster **verbinding maken met een externe computer** .

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Het gezamenlijke RDS-en toepassings proxy scenario implementeren

Nadat u RDS-en Azure-AD-toepassingsproxy voor uw omgeving hebt ingesteld, volgt u de stappen om de twee oplossingen te combi neren. In deze stappen wordt uitgelegd hoe u de twee webgerichte RDS-eind punten (extern bureau blad-web en-RD-gateway) als toepassingen publiceert en vervolgens verkeer doorstuurt op uw RDS om door de toepassings proxy te gaan.

### <a name="publish-the-rd-host-endpoint"></a>Het eind punt van de extern bureau blad-host publiceren

1. [Publiceer een nieuwe toepassing voor toepassings proxy](application-proxy-add-on-premises-application.md) met de volgende waarden:
   - Interne URL: `https://\<rdhost\>.com/` , waarbij `\<rdhost\>` de algemene hoofdmap is die extern bureau blad-Web en RD-gateway delen.
   - Externe URL: dit veld wordt automatisch ingevuld op basis van de naam van de toepassing, maar u kunt deze wijzigen. De gebruikers gaan naar deze URL wanneer ze toegang krijgen tot RDS.
   - Methode voor verificatie vooraf: Azure Active Directory
   - URL-headers vertalen: Nee
2. Gebruikers toewijzen aan de gepubliceerde extern bureau blad-toepassing. Zorg ervoor dat alle gebruikers toegang hebben tot RDS.
3. Wijzig de methode voor eenmalige aanmelding voor de toepassing als **eenmalige aanmelding voor Azure AD is uitgeschakeld**.

   >[!Note]
   >Uw gebruikers wordt gevraagd eenmaal te authenticeren bij Azure AD en eenmaal naar de extern bureau blad, maar ze hebben eenmalige aanmelding voor RD-gateway.

4. Selecteer **Azure Active Directory** en klik vervolgens op **app-registraties**. Kies uw app in de lijst.
5. Selecteer onder **beheren** de optie **huis stijl**.
6. Werk het URL-veld van de **Start pagina** zo aan dat deze naar uw extern bureau blad-webeindpunt verwijst (bijvoorbeeld `https://\<rdhost\>.com/RDWeb` ).

### <a name="direct-rds-traffic-to-application-proxy"></a>Direct RDS-verkeer naar toepassings proxy

Maak verbinding met de RDS-implementatie als beheerder en wijzig de RD-gateway server naam voor de implementatie. Deze configuratie zorgt ervoor dat verbindingen via de Azure AD-toepassingsproxy-service worden door lopen.

1. Maak verbinding met de RDS-server waarop de RD Connection Broker-rol wordt uitgevoerd.
2. Start **Serverbeheer**.
3. Selecteer **extern bureaublad-services** in het deel venster aan de linkerkant.
4. Selecteer **Overzicht**.
5. Selecteer in de sectie implementatie overzicht de vervolg keuzelijst en kies **implementatie-eigenschappen bewerken**.
6. Wijzig op het tabblad RD-gateway het veld **Server naam** in de externe URL die u hebt ingesteld voor het eind punt van de extern bureau blad-host in de toepassings proxy.
7. Wijzig het veld **aanmeldings methode** in **wachtwoord verificatie**.

   ![Eigenschappen scherm voor de implementatie van RDS](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Voer deze opdracht uit voor elke verzameling. Vervang *\<yourcollectionname\>* en *\<proxyfrontendurl\>* door uw eigen gegevens. Met deze opdracht maakt u eenmalige aanmelding mogelijk tussen extern bureau blad en RD-gateway en optimaliseert u de prestaties:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Bijvoorbeeld:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >De bovenstaande opdracht maakt gebruik van een apostroffen in ' nrequire '.

9. Voer de volgende opdracht uit om de wijziging van de aangepaste RDP-eigenschappen te controleren en de inhoud van het RDP-bestand weer te geven die wordt gedownload uit RDWeb voor deze verzameling:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Nu u Extern bureaublad hebt geconfigureerd, heeft Azure AD-toepassingsproxy het Internet gerichte onderdeel van RDS. U kunt de andere open bare Internet gerichte eind punten op uw extern bureau blad-en RD-gateway computers verwijderen.

### <a name="enable-the-rd-web-client"></a>De extern bureau blad-webclient inschakelen
Als u ook wilt dat gebruikers de extern bureau blad-webclient kunnen gebruiken, volgt u de stappen in [de Extern bureaublad-webclient instellen voor uw gebruikers](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin) om dit in te scha kelen.

Met de Extern bureaublad-webclient kunnen gebruikers toegang krijgen tot de Extern bureaublad-infra structuur van uw organisatie via een HTML5-compatibele webbrowser, zoals micro soft Edge, Internet Explorer 11, Google Chrome, Safari of Mozilla Firefox (v 55.0 en hoger).

## <a name="test-the-scenario"></a>Het scenario testen

Test het scenario met Internet Explorer op een computer met Windows 7 of 10.

1. Ga naar de externe URL die u hebt ingesteld, of Zoek uw toepassing in het [deel venster MyApps](https://myapps.microsoft.com).
2. U wordt gevraagd om u te verifiëren bij Azure Active Directory. Gebruik een account dat u hebt toegewezen aan de toepassing.
3. U wordt gevraagd om u te verifiëren bij RD Web.
4. Zodra de RDS-verificatie is gelukt, kunt u het gewenste bureau blad of toepassing selecteren en aan de slag gaan.

## <a name="support-for-other-client-configurations"></a>Ondersteuning voor andere client configuraties

De configuratie die in dit artikel wordt beschreven, is voor toegang tot RDS via RD Web of de extern bureau blad-webclient. Als dat nodig is, kunt u echter andere besturings systemen of browsers ondersteunen. Het verschil bevindt zich in de verificatie methode die u gebruikt.

| Verificatiemethode | Ondersteunde client configuratie |
| --------------------- | ------------------------------ |
| Verificatie vooraf    | Extern bureau blad-webtoepassingen 7/10 met Internet Explorer * of [Edge chroom IE-modus](/deployedge/edge-ie-mode) + RDS ActiveX-invoeg toepassing |
| Verificatie vooraf    | Extern bureau blad-webclient-HTML5-compatibele webbrowser, zoals micro soft Edge, Internet Explorer 11, Google Chrome, Safari of Mozilla Firefox (v 55.0 en hoger) |
| Voer | Elk ander besturings systeem dat ondersteuning biedt voor de Microsoft Extern bureaublad-toepassing |

* De onderchroom modus van de onderrand is vereist wanneer de portal mijn apps wordt gebruikt voor toegang tot de app Extern bureaublad.  

De stroom vóór verificatie biedt meer beveiligings voordelen dan de passthrough-stroom. Met verificatie vooraf kunt u gebruikmaken van Azure AD-verificatie functies, zoals eenmalige aanmelding, voorwaardelijke toegang en verificatie in twee stappen voor uw on-premises resources. U moet er ook voor zorgen dat alleen geverifieerd verkeer uw netwerk bereikt.

Als u Passthrough-verificatie wilt gebruiken, zijn er slechts twee wijzigingen in de stappen die in dit artikel worden beschreven:
1. Stel in [het eind punt voor de extern bureau blad-host](#publish-the-rd-host-endpoint) stap 1 de methode voor verificatie vooraf in op **passthrough**.
2. Sla stap 8 volledig over in [direct RDS-verkeer naar toepassings proxy](#direct-rds-traffic-to-application-proxy).

## <a name="next-steps"></a>Volgende stappen
- [Externe toegang tot SharePoint inschakelen met Azure AD-toepassingsproxy](application-proxy-integrate-with-sharepoint-server.md)
- [Beveiligings overwegingen voor het extern openen van apps met behulp van Azure AD-toepassingsproxy](application-proxy-security.md)
- [Aanbevolen procedures voor taak verdeling meerdere app-servers](application-proxy-high-availability-load-balancing.md#best-practices-for-load-balancing-among-multiple-app-servers)
