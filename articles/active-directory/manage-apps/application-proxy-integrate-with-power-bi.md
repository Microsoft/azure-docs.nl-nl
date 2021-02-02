---
title: Externe toegang tot Power BI met Azure AD-toepassingsproxy inschakelen
description: Behandelt de basis principes voor het integreren van een on-premises Power BI met Azure AD-toepassingsproxy.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/25/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9f0959ff51714e8a7e69f58a3e3abeab71e8359
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259369"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Externe toegang tot Power BI - Mobiel met Azure AD-toepassingsproxy inschakelen

In dit artikel wordt beschreven hoe u Azure AD-toepassingsproxy kunt gebruiken om de Power BI mobiele app in te scha kelen om verbinding te maken met Power BI Report Server (PBIRS) en SQL Server Reporting Services (SSRS) 2016 en hoger. Via deze integratie hebben gebruikers die zich buiten het bedrijfs netwerk bevinden, toegang tot hun Power BI-rapporten vanuit de mobiele app van Power BI en worden ze beschermd door Azure AD-verificatie. Deze beveiliging omvat [beveiligings voordelen](application-proxy-security.md#security-benefits) , zoals voorwaardelijke toegang en multi-factor Authentication.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u rapport Services en [ingeschakelde toepassings proxy](application-proxy-add-on-premises-application.md)al hebt geïmplementeerd.

- Voor het inschakelen van de toepassings proxy moet u een connector installeren op een Windows-Server en de [vereisten](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) volt ooien zodat de connector kan communiceren met Azure AD-Services.
- Bij het publiceren van Power BI wordt u aangeraden hetzelfde interne en externe domeinen te gebruiken. Zie [werken met aangepaste domeinen in toepassings proxy](./application-proxy-configure-custom-domain.md)voor meer informatie over aangepaste domeinen.
- Deze integratie is beschikbaar voor de **Power bi-mobiel IOS-en Android** -toepassing.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Stap 1: Configureer Kerberos-beperkte delegering (KCD)

Voor on-premises toepassingen die gebruikmaken van Windows-authenticatie, kunt u eenmalige aanmelding (SSO) met het Kerberos-verificatie protocol en een functie genaamd Kerberos-beperkte delegering (KCD) behaalt. Als KCD is geconfigureerd, kan de Application proxy connector een Windows-token voor een gebruiker verkrijgen, zelfs als de gebruiker zich niet rechtstreeks bij Windows heeft aangemeld. Zie overzicht van beperkte [Kerberos-delegering](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)) en [beperkte Kerberos-overdracht voor eenmalige aanmelding bij uw apps met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md)voor meer informatie over KCD.

Wat Reporting Services betreft, hoeft er niet veel te worden geconfigureerd. Zorg ervoor dat u een geldige SPN (Service Principal Name) hebt waarmee de juiste Kerberos-verificatie kan worden uitgevoerd. Zorg er ook voor dat de Reporting Services-server is ingeschakeld voor Negotiate-verificatie.

Ga door met de volgende stappen om KCD voor Reporting Services in te stellen.

### <a name="configure-the-service-principal-name-spn"></a>De SPN (Service Principal Name) configureren

De SPN is een unieke id voor een service die Kerberos-verificatie gebruikt. U moet ervoor zorgen dat er een juiste HTTP-SPN aanwezig is voor uw rapport server. Zie [Een Service Principal Name (SPN) voor een rapportserver registreren](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server) voor meer informatie over het configureren van de juiste Service Principal Name (SPN) voor de rapportserver.
U kunt controleren of de SPN is toegevoegd door de opdracht setspn uit te voeren met de optie-L. Zie [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx)voor meer informatie over deze opdracht.

### <a name="enable-negotiate-authentication"></a>Negotiate-verificatie inschakelen

Als u een rapport server wilt inschakelen om Kerberos-verificatie te gebruiken, configureert u het verificatie type van de rapport server in RSWindowsNegotiate. Configureer deze instelling met behulp van het rsreportserver.config-bestand.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Zie [Een Reporting Services-configuratiebestand wijzigen](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config) en [Windows-verificatie op een rapportserver configureren](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server) voor meer informatie.

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Zorg ervoor dat de connector wordt vertrouwd voor delegering naar de SPN die is toegevoegd aan het account van de groep van toepassingen van de Reporting Services
Configureer KCD zodat de Azure AD-toepassingsproxy-Service gebruikers identiteiten kan delegeren aan het account van de groep van toepassingen van de Reporting Services. Configureer KCD zodanig dat de Application Proxy Connector Kerberos-tickets ophaalt voor uw gebruikers die zijn geverifieerd in Azure AD. Vervolgens geeft die server de context door aan de doel toepassing of Reporting Services in dit geval.

Als u KCD wilt configureren, herhaalt u de volgende stappen voor elke connector computer:

1. Meld u aan bij een domein controller als een domein beheerder en open **Active Directory gebruikers en computers**.
2. Zoek de computer waarop de connector wordt uitgevoerd.
3. Dubbelklik op de computer en selecteer het tabblad **Delegatie**.
4. Stel de delegatie-instellingen in op **Deze computer mag alleen delegeren aan de opgegeven services**. Selecteer vervolgens **elk verificatie protocol gebruiken**.
5. Selecteer **Toevoegen** en vervolgens **Gebruikers of Computers**.
6. Voer het service account in dat u gebruikt voor Reporting Services. Dit is het account waaraan u de SPN hebt toegevoegd in de Reporting Services-configuratie.
7. Klik op **OK**. Klik op **OK** om de wijzigingen op te slaan.

Zie voor meer informatie [Kerberos-beperkte delegatie voor eenmalige aanmelding bij uw apps met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Stap 2: Publiceer Report Services via Azure AD-toepassingsproxy

Nu kunt u Azure AD-toepassingsproxy gaan configureren.

1. Publiceer Report Services via toepassings proxy met de volgende instellingen. Zie [Toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) voor stapsgewijze instructies voor het publiceren van een toepassing via een toepassingsproxy.
   - **Interne URL**: Voer de URL naar de rapport server in die de connector kan bereiken in het bedrijfs netwerk. Zorg dat deze URL bereikbaar is vanaf de server waarop de connector is geïnstalleerd. Een best practice is het gebruik van een domein op het hoogste niveau, zoals `https://servername/`, om problemen te voorkomen met subpaden die zijn gepubliceerd via toepassingsproxy. Gebruik bijvoorbeeld `https://servername/` en niet `https://servername/reports/` of `https://servername/reportserver/`.
     > [!NOTE]
     > U kunt het beste een beveiligde HTTPS-verbinding met de rapport server gebruiken. Zie [SSL-verbindingen configureren op een rapportserver in de native modus](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) voor meer informatie over hoe u dit doet.
   - **Externe URL**: Voer de open bare URL in waarmee de Power bi mobiele app verbinding maakt. Als er een aangepast domein wordt gebruikt, kan het er bijvoorbeeld uitzien als `https://reports.contoso.com`. Als u een aangepast domein wilt gebruiken, uploadt u een certificaat voor het domein en laat u een DNS-record naar het standaarddomein msappproxy.net voor uw toepassing wijzen. Zie [Aangepaste domeinen configureren met Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md) voor gedetailleerde stappen.

   - **Methode voor verificatie vooraf**: Azure Active Directory

2. Nadat de app is gepubliceerd, configureert u de instellingen voor eenmalige aanmelding met de volgende stappen:

   a. Selecteer op de toepassingspagina in de Portal de optie **Eenmalige aanmelding**.

   b. Selecteer bij **Modus Eenmalige aanmelding** de optie **Geïntegreerde Windows-verificatie**.

   c. Stel **SPN voor interne toepassing** in op de waarde die u eerder hebt ingesteld.

   d. Kies de **Gedelegeerde aanmeldingsidentiteit** voor de connector die u namens uw gebruikers wilt gebruiken. Zie [Werken met verschillende on-premises en cloud-identiteiten](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities) voor meer informatie.

   e. Klik op **Opslaan** om uw wijzigingen op te slaan.

Ga naar **de sectie gebruikers en groepen** en wijs gebruikers toe om toegang te krijgen tot deze toepassing om het instellen van uw toepassing te volt ooien.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Stap 3: de antwoord-URI voor de toepassing wijzigen

Voordat de Power BI mobiele app verbinding kan maken en toegang kan krijgen tot Report Services, moet u de registratie van de toepassing configureren die automatisch voor u is gemaakt in stap 2.

1. Selecteer in het **Overzicht** van Azure Active Directory de optie **App-registraties**.
2. Zoek op het tabblad **alle toepassingen** naar de toepassing die u in stap 2 hebt gemaakt.
3. Selecteer de toepassing en vervolgens **Verificatie**.
4. Voeg de volgende omleidings-URI's toe op basis van het platform dat u gebruikt.

   Bij het configureren van de app voor Power BI-Mobiel **IOS**, voegt u de volgende omleidings-uri's van het type public client (mobiel & bureau blad) toe:
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   Bij het configureren van de app voor Power BI-Mobiel **Android**, voegt u de volgende omleidings-uri's van het type public client (mobiel & bureau blad) toe:
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > De toepassing werkt alleen correct als de omleidings-URI's zijn toegevoegd. Als u de app configureert voor zowel Power BI-Mobiel iOS als Android, voegt u de volgende omleidings-URI van het type open bare-client (mobiele & bureau blad) toe aan de lijst met omleidings-Uri's die zijn geconfigureerd voor iOS: `urn:ietf:wg:oauth:2.0:oob` .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Stap 4: verbinding maken vanuit de Power BI-Mobiel-app

1. Maak in de mobiele app van Power BI verbinding met uw Reporting Services-exemplaar. Als u dit wilt doen, voert u de **externe URL** in voor de toepassing die u via de toepassings proxy hebt gepubliceerd.

   ![Mobiele app Power BI met externe URL](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Selecteer **Verbinding maken**. U wordt omgeleid naar de aanmeldings pagina van Azure Active Directory.

3. Voer geldige referenties in voor uw gebruiker en selecteer **Aanmelden**. U ziet de elementen van de Reporting Services-server.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Stap 5: intune-beleid voor beheerde apparaten configureren (optioneel)

U kunt Microsoft Intune gebruiken om de client-apps te beheren die het personeel van uw bedrijf gebruikt. Met intune kunt u gebruikmaken van mogelijkheden zoals gegevens versleuteling en aanvullende toegangs vereisten. Zie intune app Management voor meer informatie over app-beheer via intune. Voer de volgende stappen uit om de Power BI mobiele toepassing in te scha kelen voor gebruik met het intune-beleid.

1. Ga naar **Azure Active Directory** en vervolgens op **app-registraties**.
2. Selecteer de toepassing die u in stap 3 hebt geconfigureerd bij het registreren van uw systeem eigen client toepassing.
3. Selecteer op de pagina van de toepassing **API-machtigingen**.
4. Klik op **een machtiging toevoegen**.
5. Onder **api's die mijn organisatie gebruikt**, zoekt u naar ' micro soft Mobile Application Management ' en selecteert u deze.
6. De machtiging **DeviceManagementManagedApps. readwrite** toevoegen aan de toepassing
7. Klik op toestemming van de **beheerder verlenen** om de toegang tot de toepassing te verlenen.
8. Configureer het intune-beleid dat u wilt, door te verwijzen naar [het maken en toewijzen van beveiligings beleid voor apps](/intune/app-protection-policies).

## <a name="troubleshooting"></a>Problemen oplossen

Als de toepassing een fout pagina retourneert nadat u meer dan een paar minuten een rapport hebt geladen, moet u mogelijk de time-outinstelling wijzigen. Standaard ondersteunt toepassings proxy toepassingen die tot 85 seconden duren om op een aanvraag te reageren. Als u deze instelling wilt verlengen tot 180 seconden, selecteert u de time- **out voor de** back-end in de pagina app-proxy-instellingen voor de toepassing. Zie [Power bi aanbevolen procedures voor rapporten](/power-bi/power-bi-reports-performance)voor tips over het maken van snelle en betrouw bare rapporten.

Azure AD-toepassingsproxy gebruiken om de Power BI mobiele app in te scha kelen om verbinding te maken met on-premises Power BI Report Server wordt niet ondersteund met beleids regels voor voorwaardelijke toegang waarvoor de micro soft Power BI-app als goedgekeurde client-app vereist is.

## <a name="next-steps"></a>Volgende stappen

- [Systeem eigen client toepassingen inschakelen voor interactie met proxy toepassingen](application-proxy-configure-native-client-application.md)
- [On-premises rapportserverrapporten en KPI's weergeven in de mobiele Power BI-apps](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
