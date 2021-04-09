---
title: Azure AD beveiligde hybride toegang met F5 VPN | Microsoft Docs
description: Zelf studie voor het Azure Active Directory van SSO-integratie (single sign-on) met F5 BIG-IP voor wacht woord-minder VPN
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e177f1ce55d803f54bb2553078441557e5c191
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730884"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>Zelf studie voor het Azure Active Directory van eenmalige aanmelding met F5 BIG-IP voor wacht woord-minder VPN

In deze zelf studie leert u hoe u F5's BIG-IP-gebaseerde Secure Socket Layer-oplossing voor virtueel particulier netwerk (SSL) integreert met Azure Active Directory (AD) voor Secure Hybrid Access (SHA).

Het integreren van een BIG-IP SSL-VPN met Azure AD biedt [veel belang rijke voor delen](f5-aad-integration.md), waaronder:

- Verbeterd beheer van nul door [Azure AD vooraf-authenticatie en autorisatie](../../app-service/overview-authentication-authorization.md)

- [Verificatie zonder wacht woord voor de VPN-service](https://www.microsoft.com/security/business/identity/passwordless)

- Identiteiten en toegang beheren vanuit één besturings vlak: de [Azure Portal](https://portal.azure.com/#home)

Ondanks deze geweldige waarde voegt de klassieke VPN-verbinding echter wel voor op het principe van een netwerk perimeter, waar vertrouwd zich bevindt en niet vertrouwd de buiten. Dit model is niet langer effectief bij het bereiken van een echte postuur, omdat bedrijfs middelen niet langer worden beperkt tot de wanden van een Enter prise Data Center, maar in omgevingen met meerdere clouds zonder vaste grenzen. Daarom raden we onze klanten aan om over te stappen op een meer identiteits gerichte aanpak bij [het beheren van de toegang per toepassing](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md).

## <a name="scenario-description"></a>Scenariobeschrijving

In dit scenario wordt het APM-exemplaar van het BIG IP-adres van de SSL-VPN-service geconfigureerd als een SAML-service provider (SP) en wordt Azure AD de vertrouwde SAML-IDP. Dit zorgt voor verificatie vooraf. Eenmalige aanmelding (SSO) van Azure AD wordt vervolgens verstrekt via verificatie op basis van claims naar de BIG-IP APM, waardoor een naadloze VPN-toegangs ervaring wordt geboden.

![Installatie kopie toont SSL-VPN-architectuur](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>Alle voorbeeld reeksen of waarden waarnaar in deze hand leiding wordt verwezen, moeten worden vervangen door die voor uw eigen omgeving.

## <a name="prerequisites"></a>Vereisten

Eerdere ervaring of kennis van F5 BIG-IP is echter niet nodig, maar u hebt het volgende nodig:

- Een gratis Azure AD- [abonnement](https://azure.microsoft.com/trial/get-started-active-directory/) of hoger

- Gebruikers identiteiten moeten worden [gesynchroniseerd van de on-premises Directory](../hybrid/how-to-connect-sync-whatis.md) naar Azure AD.

- Een account met [machtigingen](../roles/permissions-reference.md#application-administrator) voor de Azure AD-toepassings beheerder

- Een bestaande BIG-IP-infra structuur met route ring van client verkeer naar en van de BIG-IP of het [implementeren van een Big-IP-editie in azure](f5-bigip-deployment-guide.md).

- Er moet een record voor de in BIG-IP gepubliceerde VPN-service aanwezig zijn in open bare DNS of het localhost-bestand van een test client tijdens het testen.

- Het BIG-IP-adres moet worden ingericht met de vereiste SSL-certificaten voor het publiceren van services via HTTPS.

Als u vertrouwd bent met [F5 BIG-IP terminologie](https://www.f5.com/services/resources/glossary) , kunt u ook inzicht krijgen in de verschillende onderdelen waarnaar wordt verwezen in de zelf studie.

>[!NOTE]
>Azure is voortdurend in ontwikkeling, waardoor u geen nuances vindt tussen de instructies in deze hand leiding en wat u in de Azure Portal ziet. Scherm afbeeldingen zijn van BIG-IP-V15, maar blijven relatief gelijk van v 13.1.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Voeg F5 BIG-IP toe vanuit de Azure AD-galerie

Bij het instellen van een SAML Federation-vertrouwens relatie tussen het BIG-IP-adres kan Azure AD BIG-IP de pre-authenticatie en [voorwaardelijke toegang](../conditional-access/overview.md) tot Azure AD afleveren voordat toegang wordt verleend aan de gepubliceerde VPN-service.

1. Meld u aan bij de Azure AD-Portal met een account met beheerders rechten voor de toepassing

2. Selecteer in het navigatie deel venster links de **Azure Active Directory-service**

3. Ga naar **bedrijfs toepassingen** en selecteer in het bovenste lint **nieuwe toepassing**.

4. Zoek in de galerie naar F5 en selecteer **F5 BIG-IP apm Azure AD-integratie**.

5. Geef een naam op voor de toepassing, gevolgd door **toevoegen/maken** , zodat deze wordt toegevoegd aan uw Tenant. De gebruiker kan de naam zien als een pictogram in de portals van Azure en Office 365-toepassing. De naam moet overeenkomen met die specifieke service. Bijvoorbeeld VPN.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

1. Ga met de nieuwe eigenschappen van de F5-toepassing in de weer gave naar  >  **eenmalige aanmelding** beheren

2. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**. Sla de vraag om de instellingen voor eenmalige aanmelding op te slaan door nee te selecteren **, ik wil later opslaan**.

3. Selecteer in het menu **eenmalige aanmelding instellen met SAML** het pictogram voor de **eenvoudige SAML-configuratie** om de volgende details op te geven:

   - Vervang de vooraf gedefinieerde **ID-URL** door de URL voor de gepubliceerde Big-IP-service. Bijvoorbeeld: `https://ssl-vpn.contoso.com`

   - Doe hetzelfde met het tekstvak **antwoord-URL** , met inbegrip van het SAML-eindpunt pad. Bijvoorbeeld: `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - In deze configuratie wordt de toepassing alleen uitgevoerd in een door IDP gestarte modus, waarbij Azure AD de gebruiker een SAML-bevestiging geeft voordat deze wordt omgeleid naar de SAML-service voor BIG-IP. Voor apps die de gestarte modus IDP niet ondersteunen, geeft u de **aanmeldings-URL** op voor de Service Big-IP SAML. Bijvoorbeeld `https://ssl-vpn.contoso.com`.

   - Voor de afmeldings-URL voert u het BIG-IP APM-eind punt voor eenmalige afmelding (SLO) pended door de host-header van de service die wordt gepubliceerd. Bijvoorbeeld: `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   Als u een SLO-URL opgeeft, zorgt u ervoor dat een gebruikers sessie wordt beëindigd aan beide uiteinden, het BIG-IP-adres en Azure AD, nadat de gebruiker zich afmeldt. Geavanceerd APM biedt ook een [optie](https://support.f5.com/csp/article/K12056) voor het beëindigen van alle sessies bij het aanroepen van een specifieke toepassings-URL.

![Afbeelding toont de basis configuratie van SAML](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>Van TMOS V16 het SAML SLO-eind punt is gewijzigd in/SAML/SP/profile/redirect/SLO

4. Selecteer **Opslaan** voordat u het configuratie menu van SAML afsluit en sla de SSO-test prompt over.

Bekijk de eigenschappen van de **gebruikers kenmerken & sectie claims** , omdat Azure AD deze verleent aan gebruikers voor APM-verificatie met een Big IP-adres.

![Afbeelding toont de gebruikers kenmerken claims](media/f5-sso-vpn/user-attributes-claims.png)

U kunt ook een andere specifieke claim toevoegen die wordt verwacht door uw BIG-IP-gepubliceerde service, terwijl de claims die naast de standaard set zijn gedefinieerd, alleen worden uitgegeven als deze bestaan in azure AD, als gevulde kenmerken. Op dezelfde manier moeten Directory [rollen of groepslid](../hybrid/how-to-connect-fed-group-claims.md) maatschappen ook worden gedefinieerd op basis van een gebruikers object in azure AD voordat ze als een claim kunnen worden uitgegeven.

![Afbeelding toont koppeling voor downloaden van federatieve meta gegevens](media/f5-sso-vpn/saml-signing-certificate.png)

SAML-handtekening certificaten die zijn gemaakt door Azure AD hebben een levens duur van drie jaar. Daarom moet u beheren met behulp van gepubliceerde Azure AD-richt lijnen.

### <a name="azure-ad-authorization"></a>Azure AD-autorisatie

Azure AD geeft standaard alleen tokens aan gebruikers die toegang tot een service hebben gekregen.

1. Selecteer **gebruikers en groepen** in de configuratie weergave van de toepassing.

2. Selecteer **+ gebruiker toevoegen** en selecteer in het menu toewijzing toevoegen de optie **gebruikers en groepen** .

3. Voeg in het dialoog venster **gebruikers en groepen** de groepen gebruikers toe die zijn gemachtigd voor toegang tot het VPN, gevolgd door  >  **toewijzen** selecteren

![Afbeelding toont toevoegen van gebruikers koppeling ](media/f5-sso-vpn/add-user-link.png)

4. Hiermee wordt het onderdeel Azure AD van de SAML Federation-vertrouwens relatie voltooid. De BIG-IP APM kan nu worden ingesteld voor het publiceren van de SSL-VPN-service en is geconfigureerd met een bijbehorende set eigenschappen voor het volt ooien van de vertrouwens relatie voor SAML vooraf-authenticatie.

## <a name="big-ip-apm-configuration"></a>APM-configuratie BIG-IP

### <a name="saml-federation"></a>SAML-Federatie

In het volgende gedeelte maakt u de BIG-IP SAML-service provider en bijbehorende SAML IDP-objecten die zijn vereist om federeren de VPN-service met Azure AD te volt ooien.

1. Ga naar **de**  >  lokale SP-services van de **Federation**  >  **SAML-service provider**  >   en selecteer **maken**

![Afbeelding toont de configuratie van BIG-IP-SAML](media/f5-sso-vpn/bigip-saml-configuration.png)

2. Voer een **naam** en dezelfde **Entiteits-ID** in die u eerder in azure AD hebt gedefinieerd en de FQDN van de host die wordt gebruikt om verbinding te maken met de toepassing

![Afbeelding toont het maken van een nieuwe SAML SP-service](media/f5-sso-vpn/create-new-saml-sp.png)

   SP **name** -instellingen zijn alleen vereist als de entiteit-id niet exact overeenkomt met het hostnaam-gedeelte van de gepubliceerde URL of als het zich niet in de normale URL-indeling op basis van een host bevindt. Geef het externe schema en de hostnaam op van de toepassing die wordt gepubliceerd als de entiteit-ID is `urn:ssl-vpn:contosoonline` .

3. Schuif omlaag om het nieuwe **SAML-SP-object** te selecteren en selecteer BIND/Unbind **IDP connectes**.

![Afbeelding toont het maken van Federatie met de lokale SP-service](media/f5-sso-vpn/federation-local-sp-service.png)

4. Selecteer **nieuwe IDP-connector maken** en selecteer in de vervolg keuzelijst selecteren **uit meta gegevens**

![Afbeelding toont nieuwe IDP-connector maken](media/f5-sso-vpn/create-new-idp-connector.png)

5. Blader naar het XML-bestand met federatieve meta gegevens dat u eerder hebt gedownload en geef de naam van een **ID-provider** op voor het APM-object dat de externe SAML-IDP vertegenwoordigt.

6. Selecteer **nieuwe rij toevoegen** om de nieuwe Azure AD External IDP-connector te selecteren.

![Afbeelding toont externe IDP-connector](media/f5-sso-vpn/external-idp-connector.png)

7. Selecteer **bijwerken** om het SAML-SP object te binden aan het SAML IDP-object en selecteer vervolgens **OK**.

![Installatie kopie toont de SAML-IDP met behulp van SP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>WebTop-configuratie

Met de volgende stappen kan de SSL-VPN-verbinding worden aangeboden aan gebruikers via een eigen webportal van een BIG-IP-adres.

1. Ga naar **toegang tot**  >    >  **WebTop lijsten** van webdoppen en selecteer **maken**.

2. Geef een naam op voor de portal en stel het type in op **Full**. Bijvoorbeeld `Contoso_webtop`.

3. Pas de resterende voor keuren aan en selecteer vervolgens **voltooid**.

![Installatie kopie toont de WebTop-configuratie](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>VPN-configuratie

De VPN-mogelijkheid bestaat uit verschillende elementen, waarbij elk een ander aspect van de algehele service wordt beheerd.

1. Ga naar de IPv4-lease groepen voor **toegang tot**  >  **connectiviteit/VPN**-  >  **netwerk toegang (VPN)**  >   en selecteer **maken**.

2. Geef een naam op voor de groep IP-adressen die worden toegewezen aan VPN-clients. Bijvoorbeeld Contoso_vpn_pool

3. Stel type in op **IP-adres bereik** en geef een begin-en eind-IP op, gevolgd door **toevoegen** en **voltooid**.

![Installatie kopie toont de VPN-configuratie](media/f5-sso-vpn/vpn-configuration.png)

Een lijst met netwerk toegang is van toepassing op de service met IP-en DNS-instellingen van de VPN-groep, machtigingen voor gebruikers Routering en kan indien nodig ook toepassingen starten.

1. Ga naar **toegangs**  >  **verbindingen/VPN: netwerk toegangs lijsten (VPN)**  >   en selecteer **maken**.

2. Geef een naam op voor de VPN-toegangs lijst en het bijschrift, bijvoorbeeld contoso-VPN, gevolgd door **voltooid**.

![Installatie kopie toont de VPN-configuratie in de lijst met netwerk toegang](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. Selecteer in het bovenste lint **netwerk instellingen** en voeg de onderstaande instellingen toe:

   • **Ondersteunde IP-versie**: IPv4

   • **IPv4-lease groep**: Selecteer de VPN-groep die u eerder hebt gemaakt, bijvoorbeeld Contoso_vpn_pool

![Afbeelding toont de contoso VPN-groep](media/f5-sso-vpn/contoso-vpn-pool.png)

   De opties voor client instellingen kunnen worden gebruikt om beperkingen af te dwingen voor de manier waarop het client verkeer wordt gerouteerd wanneer een VPN tot stand is gebracht.

4. Selecteer **voltooid** en ga naar het tabblad DNS/hosts om de instellingen toe te voegen:

   • **IPv4 Primary name server**: IP van de DNS-server van uw omgeving

   • **DNS standaard domein achtervoegsel**: het domein achtervoegsel voor deze specifieke VPN-verbinding. Bijvoorbeeld contoso.com

![Afbeelding toont standaard domein achtervoegsel](media/f5-sso-vpn/domain-suffix.png)

[F5 artikel](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) bevat informatie over het aanpassen van de overige instellingen op basis van uw voor keur.

Een BIG-IP-verbindings profiel is nu vereist voor het configureren van de instellingen voor elk van de VPN-client typen die door de VPN-service moeten worden ondersteund. Bijvoorbeeld Windows, OSX en Android.

1. Ga naar connectiviteits profielen voor **toegang**  >  **/VPN**  >    >   en selecteer **toevoegen**.

2. Geef een profiel naam op en stel het bovenliggende profiel in op **/common/connectivity**, bijvoorbeeld Contoso_VPN_Profile.

![Afbeelding toont nieuw connectiviteits profiel maken](media/f5-sso-vpn/create-connectivity-profile.png)

F5's- [documentatie](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) biedt meer informatie over client ondersteuning.

## <a name="access-profile-configuration"></a>Configuratie van toegangs profielen

Als de VPN-objecten zijn geconfigureerd, is een toegangs beleid vereist om de service in te scha kelen voor SAML-verificatie.

1. Ga naar **toegangs profielen**  >  **/beleids regels** voor toegang  >  **(per sessie beleid)** en selecteer **maken**

2. Geef een profiel naam en voor het profiel type **Alles** selecteren, bijvoorbeeld Contoso_network_access

3. Schuif omlaag om ten minste één taal toe te voegen aan de lijst **Geaccepteerde talen** en selecteer **voltooid**

![Afbeelding toont algemene eigenschappen](media/f5-sso-vpn/general-properties.png)

4. Selecteer **bewerken** in het veld Per-Session-beleid van het nieuwe toegangs profiel, zodat de editor van het Visual-beleid wordt gestart op een afzonderlijk browser tabblad.

![Installatie kopie toont beleid per sessie](media/f5-sso-vpn/per-session-policy.png)

5. Selecteer het **+** teken en selecteer in het pop-upvenster **verificatie**  >  **SAML auth**  >  **item toevoegen**.

6. Selecteer in de configuratie van de SAML-verificatie-SP het VPN SAML SP-object dat u eerder hebt gemaakt, gevolgd door **Opslaan**.

![Afbeelding toont SAML-verificatie](media/f5-sso-vpn/saml-authentication.png)

7. Selecteer **+** deze optie voor de geslaagde vertakking van SAML-verificatie.

8. Selecteer op het tabblad toewijzing de optie **Geavanceerde resource toewijzing** gevolgd door **item toevoegen**

![Afbeelding toont voor schot van resource toewijzing](media/f5-sso-vpn/advance-resource-assign.png)

9. Selecteer in het pop-upvenster **nieuwe vermelding** en vervolgens **toevoegen/verwijderen**.

10. Selecteer in het onderliggende venster **netwerk toegang** en selecteer vervolgens het netwerk toegangs profiel dat u eerder hebt gemaakt

![Afbeelding toont het toevoegen van een nieuwe netwerk toegangs vermelding](media/f5-sso-vpn/add-new-entry.png)

11. Ga naar het tabblad **WebTop** en voeg het WebTop-object toe dat u eerder hebt gemaakt.

![Afbeelding toont het toevoegen van WebTop-object](media/f5-sso-vpn/add-webtop-object.png)

12. Selecteer **Update** , gevolgd door **Opslaan**.

13. Selecteer de koppeling in het vak boven afwijzen om de geslaagde vertakking zo te wijzigen dat deze vervolgens **kan** worden **opgeslagen**.

![Afbeelding toont nieuwe editor voor visueel beleid](media/f5-sso-vpn/vizual-policy-editor.png)

14. Deze instellingen door voeren door **toegangs beleid Toep assen** te selecteren en het tabblad editor voor visuele beleids regels te sluiten.

![Afbeelding toont nieuw beheer van toegangs beleid](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>De VPN-service publiceren

Voor alle instellingen op locatie vereist de APM nu een front-end-virtuele server om te Luis teren naar clients die verbinding maken met het VPN.

1. Selecteer een virtuele server lijst voor het **lokale verkeer** van  >  **virtuele servers**  >   en selecteer **maken**.

2. Geef een **naam** op voor de virtuele VPN-server, bijvoorbeeld **VPN_Listener**.

3. Geef de virtuele server met een ongebruikt **IP-doel adres** dat route ring heeft voor het ontvangen van client verkeer

4. Stel de service poort in op **443 https** en zorg ervoor dat de **status wordt weer** gegeven

![Afbeelding toont nieuwe virtuele server](media/f5-sso-vpn/new-virtual-server.png)

5. Stel het **http-profiel** in op http en voeg het SSL-Profiel (client) toe voor het open bare SSL-certificaat dat u hebt ingericht als onderdeel van de vereisten.

![Afbeelding toont SSL-profiel](media/f5-sso-vpn/ssl-profile.png)

6. Stel onder toegangs beleid het **toegangs profiel** en het **verbindings profiel** in om de gemaakte VPN-objecten te gebruiken.

![Afbeelding toont toegangs beleid](media/f5-sso-vpn/access-policy.png)

7. Selecteer **voltooid** wanneer u klaar bent.

8.  Uw SSL-VPN-service wordt nu via SHA gepubliceerd en toegankelijk via de bijbehorende URL of door middel van toepassings portals van micro soft.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Het einde van wacht woorden, wacht woordloos](https://www.microsoft.com/security/business/identity/passwordless)

- [Wat is voorwaardelijke toegang?](../conditional-access/overview.md)

- [Micro soft Zero Trust Framework om extern werk in te scha kelen](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Vijf stappen voor volledige toepassings integratie met Azure AD](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>Volgende stappen

Open een browser op een externe Windows-client en blader naar de URL van de **Big-IP VPN-service**. Nadat u bent geverifieerd bij Azure AD, ziet u de BIG-IP WebTop-Portal en het Azure Launcher.

![Installatie kopie toont het VPN-start programma](media/f5-sso-vpn/vpn-launcher.png)

Als u de VPN-tegel selecteert, wordt de client met een grote IP-Edge geïnstalleerd en wordt een VPN-verbinding tot stand gebracht die is geconfigureerd voor SHA.
De F5-VPN-toepassing moet ook worden weer gegeven als doel bron in voorwaardelijke toegang van Azure AD. Bekijk onze [richt lijnen](../conditional-access/concept-conditional-access-policies.md) voor het maken van beleid voor voorwaardelijke toegang en het inschakelen van gebruikers voor Azure AD [-wacht woord-less-verificatie](https://www.microsoft.com/security/business/identity/passwordless).