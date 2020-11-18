---
title: Problemen oplossen met beperkte Kerberos-overdracht-app proxy
description: Problemen met Kerberos-beperkte overdrachts configuraties voor toepassings proxy oplossen
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/23/2019
ms.author: kenwith
ms.reviewer: asteen, japere
ms.openlocfilehash: c28e79c9a6f8c489a97d360c4fe142d431b5ab5d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656544"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Problemen met Kerberos-beperkte overdrachts configuraties voor toepassings proxy oplossen

De beschik bare methoden voor het bereiken van eenmalige aanmelding voor gepubliceerde toepassingen kunnen variëren van de ene toepassing naar de andere. Een optie die Azure Active Directory (Azure AD)-toepassings proxy biedt standaard een Kerberos-beperkte delegering (KCD). U kunt voor uw gebruikers een connector configureren om beperkte Kerberos-verificatie uit te voeren op back-end-toepassingen.

De procedure voor het inschakelen van KCD is eenvoudig. Het vereist niet meer dan een algemene uitleg van de verschillende onderdelen en verificatie stroom die SSO ondersteunen. Maar soms werkt KCD SSO niet zoals verwacht. U hebt goede informatie bronnen nodig om deze scenario's op te lossen.

In dit artikel vindt u één referentie punt voor het oplossen van problemen met een aantal van de meest voorkomende problemen. Het behandelt ook de diagnose van complexere implementatie problemen.

In dit artikel worden de volgende veronderstellingen aangebracht:

- De implementatie van Azure AD-toepassingsproxy per aan de [slag met toepassings proxy](application-proxy-add-on-premises-application.md) en algemene toegang tot niet-KCD toepassingen werkt zoals verwacht.
- De gepubliceerde doel toepassing is gebaseerd op Internet Information Services (IIS) en de micro soft-implementatie van Kerberos.
- De server-en toepassingshost bevinden zich in één Azure Active Directory domein. Voor gedetailleerde informatie over scenario's tussen domeinen en forests raadpleegt u het [technisch document KCD](https://aka.ms/KCDPaper).
- De onderwerp-toepassing wordt gepubliceerd in een Azure-Tenant waarbij vooraf-verificatie is ingeschakeld. Gebruikers worden naar verwachting geverifieerd bij Azure via verificatie op basis van formulieren. Uitgebreide scenario's voor client verificatie vallen niet onder dit artikel. Ze kunnen op een bepaald moment in de toekomst worden toegevoegd.

## <a name="prerequisites"></a>Vereisten

Azure AD-toepassingsproxy kan worden geïmplementeerd in veel soorten infra structuren of omgevingen. De architecturen variëren van organisatie tot organisatie. De meest voorkomende oorzaken van problemen met KCD zijn niet de omgevingen. Bij eenvoudige onjuiste configuratie of algemene fouten worden de meeste problemen veroorzaakt.

Daarom is het raadzaam om ervoor te zorgen dat u voldoet aan alle vereisten in [het gebruik van KCD SSO met de toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md) voordat u begint met het oplossen van problemen. Let op de sectie over het configureren van beperkte Kerberos-delegering op 2012R2. Dit proces maakt gebruik van een andere benadering voor het configureren van KCD op eerdere versies van Windows. Wees ook mindful van deze overwegingen:

- Het is niet ongebruikelijk dat een domein lidserver een dialoog venster met een beveiligd kanaal opent met een specifieke domein controller (DC). Vervolgens kan de server op elk gewenst moment overstappen op een ander dialoog venster. Connector-hosts zijn dus niet beperkt tot communicatie met alleen specifieke lokale site-Dc's.
- Scenario's tussen domeinen zijn afhankelijk van verwijzingen die een connector host naar Dc's sturen die zich buiten het lokale netwerk perimeter kunnen bevinden. In dergelijke gevallen is het net zo belang rijk dat ook verkeer wordt verzonden naar Dc's die andere respectieve domeinen vertegenwoordigen. Als dat niet het geval is, mislukt de overdracht.
- Vermijd waar mogelijk actieve IP'S of id-apparaten tussen connector-hosts en Dc's plaatsen. Deze apparaten zijn soms te verstorend en veroorzaken een conflict met het kern RPC-verkeer.

Test delegering in eenvoudige scenario's. Hoe meer variabelen u introduceert, hoe meer u mogelijk moet concurreren met. Om tijd te besparen, kunt u het testen beperken tot één connector. Aanvullende connectors toevoegen nadat het probleem is opgelost.

Sommige omgevings factoren kunnen ook bijdragen aan een probleem. Als u deze factoren wilt vermijden, minimaliseert u de architectuur zo veel mogelijk tijdens het testen. Onjuist geconfigureerde interne firewall-Acl's zijn bijvoorbeeld gebruikelijk. Verzend, indien mogelijk, al het verkeer van een connector rechtstreeks naar de Dc's en de back-end-toepassing.

U kunt connectors het beste zo dicht mogelijk bij hun doelen plaatsen. Een firewall die inline is tijdens het testen, voegt onnodige complexiteit toe en kan uw onderzoek verlengen.

Wat wordt een KCD-probleem weer gegeven? Er zijn verschillende algemene aanwijzingen dat KCD SSO mislukt. De eerste teken van een probleem wordt weer gegeven in de browser.

![Scherm afbeelding met een voor beeld van een onjuiste K C D-configuratie fout, met de fout ' onjuiste Kerberos-beperkte overdracht... ' aangeduid.](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Voor beeld: autorisatie is mislukt vanwege ontbrekende machtigingen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Beide installatie kopieën tonen hetzelfde symptoom: SSO-fout. Gebruikers toegang tot de toepassing is geweigerd.

## <a name="troubleshooting"></a>Problemen oplossen

Hoe u problemen oplost, hangt af van het probleem en de symptomen die u ziet. Bekijk de volgende artikelen voordat u verder gaat. Ze bieden nuttige informatie over het oplossen van problemen:

- [Problemen en foutberichten met Application Proxy oplossen](application-proxy-troubleshoot.md)
- [Kerberos-fouten en-symptomen](application-proxy-troubleshoot.md#kerberos-errors)
- [Werken met SSO wanneer on-premises en Cloud-identiteiten niet identiek zijn](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Als u op dit punt hebt geklikt, bestaat het belangrijkste probleem. Als u wilt beginnen, moet u de stroom scheiden in de volgende drie fasen die u kunt oplossen.

### <a name="client-pre-authentication"></a>Pre-authenticatie van client

De externe gebruiker die via een browser wordt geverifieerd bij Azure. De mogelijkheid om vooraf te verifiëren bij Azure is vereist voor de functie KCD SSO. Test en los deze mogelijkheid op als er problemen zijn. De fase vooraf verificatie is niet gerelateerd aan KCD of de gepubliceerde toepassing. Het is eenvoudig om eventuele verschillen te corrigeren door te controleren of de Sanity in azure bestaat. Controleer ook of het niet is uitgeschakeld of geblokkeerd. Het fout bericht in de browser is beschrijvend genoeg om de oorzaak te verklaren. Als u niet zeker weet, raadpleegt u andere artikelen over probleem oplossing van micro soft om te controleren.

### <a name="delegation-service"></a>Overdrachts service

De Azure proxy-connector die een Kerberos-service ticket voor gebruikers ontvangt van een Kerberos-Key Distribution Center (KCD).

De externe communicatie tussen de client en de Azure-front-end heeft geen invloed op KCD. Deze communicatie zorgt er alleen voor dat KCD werkt. De Azure proxy-service is een geldige gebruikers-ID die wordt gebruikt voor het ophalen van een Kerberos-ticket. Zonder deze ID kan KCD niet worden uitgevoerd en mislukt.

Zoals eerder vermeld, biedt de browser fout berichten een aantal goede aanwijzingen voor de oorzaak van het mislukken van dingen. Noteer de activiteits-ID en tijds tempel in het antwoord. Deze informatie helpt u bij het correleren van het gedrag aan werkelijke gebeurtenissen in het gebeurtenis logboek van Azure proxy.

![Voor beeld: onjuiste KCD-configuratie fout](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

De corresponderende vermeldingen in het gebeurtenis logboek worden weer gegeven als gebeurtenis 13019 of 12027. De gebeurtenis logboeken van de connector in **toepassingen en services zoeken,** &gt; **wordt de micro soft** &gt; **AadApplicationProxy** - &gt; **connector** - &gt; **beheerder** geregistreerd.

![Gebeurtenis 13019 van het gebeurtenis logboek van de toepassings proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Gebeurtenis 12027 van het gebeurtenis logboek van de toepassings proxy](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Gebruik een **A** -record in uw interne DNS voor het adres van de toepassing, niet een **CNAME**.
1. Bevestig dat de host van de connector het recht heeft om te delegeren aan de SPN van het aangewezen doel account. Bevestig dat **een verificatie protocol gebruiken** is geselecteerd. Zie het artikel over SSO- [configuratie](application-proxy-configure-single-sign-on-with-kcd.md)voor meer informatie.
1. Controleer of er slechts één exemplaar van de SPN bestaat in azure AD. Probleem `setspn -x` vanaf een opdracht prompt op een domeinlid die lid is van een domein.
1. Controleer of er een domein beleid wordt afgedwongen waardoor de [maximale grootte van de uitgegeven Kerberos-tokens](/archive/blogs/askds/maxtokensize-and-windows-8-and-windows-server-2012)wordt beperkt. Met dit beleid wordt voor komen dat de connector een token krijgt als dit buitensporig te groot is.

Een netwerk tracering die de uitwisselingen tussen de connector-host en een domein KDC vastlegt, is de volgende beste stap om meer details op laag niveau te verkrijgen over de problemen. Zie het [artikel grondige problemen oplossen](https://aka.ms/proxytshootpaper)voor meer informatie.

Als ticketing goed lijkt, ziet u een gebeurtenis in de logboeken waarin wordt aangegeven dat de verificatie is mislukt omdat de toepassing een 401 heeft geretourneerd. Deze gebeurtenis geeft aan dat de doel toepassing uw ticket heeft afgewezen. Ga naar de volgende fase.

### <a name="target-application"></a>Doel toepassing

De gebruiker van het Kerberos-ticket dat door de connector wordt verschaft. In deze fase wordt verwacht dat de connector een Kerberos-service ticket naar de back-end heeft verzonden. Dit ticket is een koptekst in de eerste toepassings aanvraag.

1. Door de interne URL van de toepassing te gebruiken die in de portal is gedefinieerd, controleert u of de toepassing rechtstreeks toegankelijk is vanuit de browser op de host van de connector. U kunt zich vervolgens aanmelden. Meer informatie vindt u op de pagina connector **problemen oplossen** .
1. Controleer nog steeds op de connector-host of de verificatie tussen de browser en de toepassing Kerberos gebruikt. Ga in dat geval op een van de volgende manieren te werk:
1. Voer DevTools (**F12**) uit in Internet Explorer of gebruik [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) van de host van de connector. Ga naar de toepassing met behulp van de interne URL. Inspecteer de aangeboden WWW-autorisatie headers die worden geretourneerd in het antwoord van de toepassing om er zeker van te zijn dat Negotiate of Kerberos aanwezig is.

   - De volgende Kerberos-blob die in het antwoord van de browser naar de toepassing wordt geretourneerd, begint met **YII**. Deze letters geven aan dat Kerberos actief is. Micro soft NT LAN Manager (NTLM), aan de andere kant, begint altijd met **TlRMTVNTUAAB**, die een NTLM-beveiligings provider (NTLMSSP) leest bij het decoderen van base64. Als u **TlRMTVNTUAAB** ziet aan het begin van de blob, is Kerberos niet beschikbaar. Als u **TlRMTVNTUAAB** niet ziet, is Kerberos waarschijnlijk beschikbaar.

      > [!NOTE]
      > Als u Fiddler gebruikt, moet u voor deze methode de uitgebreide beveiliging tijdelijk uitschakelen voor de configuratie van de toepassing in IIS.

      ![Venster browser netwerk inspectie](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - De BLOB in deze afbeelding begint niet met **TIRMTVNTUAAB**. In dit voor beeld is Kerberos beschikbaar en wordt de Kerberos-BLOB niet gestart met **YII**.

1. Verwijder tijdelijk NTLM van de lijst providers op de IIS-site. Open de app rechtstreeks vanuit Internet Explorer op de connector-host. NTLM bevindt zich niet meer in de lijst met providers. U kunt de toepassing openen door alleen Kerberos te gebruiken. Als de toegang mislukt, is er mogelijk een probleem met de configuratie van de toepassing. Kerberos-verificatie werkt niet.

   - Als Kerberos niet beschikbaar is, controleert u de verificatie-instellingen van de toepassing in IIS. Zorg ervoor dat **Negotiate** bovenaan wordt weer gegeven, met NTLM alleen. Als u **niet onderhandelen**, **Kerberos of Negotiate** of **PKU2U** ziet, moet u alleen door gaan als Kerberos functioneel is.

     ![Windows-verificatie providers](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - Als Kerberos en NTLM aanwezig zijn, schakelt u de vooraf-verificatie voor de toepassing tijdelijk uit in de portal. Probeer het vanaf internet te openen met behulp van de externe URL. U wordt gevraagd om te verifiëren. U kunt dit doen met hetzelfde account dat is gebruikt in de vorige stap. Als dat niet het geval is, is er een probleem met de back-end-toepassing, niet KCD.
   - Schakel vooraf-verificatie in de portal opnieuw in. Verifieer via Azure door te proberen verbinding te maken met de toepassing via de externe URL. Als SSO mislukt, ziet u een verboden fout bericht in de browser en gebeurtenis 13022 in het logboek:

     *Micro soft AAD Application proxy connector kan de gebruiker niet verifiëren omdat de back-endserver reageert op Kerberos-verificatie pogingen met een HTTP 401-fout.*

      ![Hiermee wordt HTTTP 401 verboden fout weer gegeven](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Controleer de IIS-toepassing. Zorg ervoor dat de geconfigureerde groep van toepassingen en de SPN zijn geconfigureerd voor gebruik van hetzelfde account in azure AD. Navigeer in IIS, zoals wordt weer gegeven in de volgende afbeelding:

      ![Configuratie venster IIS-toepassing](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Nadat u de identiteit hebt weten, controleert u of dit account is geconfigureerd met de betreffende SPN. Een voorbeeld is `setspn –q http/spn.wacketywack.com`. Voer de volgende tekst in een opdracht prompt in:

      ![Hiermee wordt het SetSPN-opdracht venster weer gegeven](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Controleer de SPN die is gedefinieerd voor de instellingen van de toepassing in de portal. Zorg ervoor dat dezelfde SPN die is geconfigureerd voor het doel-Azure AD-account wordt gebruikt door de app-groep van de toepassing.

      ![SPN-configuratie in de Azure Portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Ga naar IIS en selecteer de optie **Configuratie-editor** voor de toepassing. Navigeer naar **System. webserver/Security/Authentication/windowsAuthentication**. Zorg ervoor dat de waarde **UseAppPoolCredentials** is ingesteld op **True**.

      ![Optie voor de referentie van de IIS-configuratie app-groepen](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Wijzig deze waarde in **True**. Verwijder alle Kerberos-tickets in de cache van de back-endserver door de volgende opdracht uit te voeren:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Zie [de Kerberos-client ticket cache voor alle sessies leegmaken](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf)voor meer informatie.

Als u de kernelmodus hebt ingeschakeld, worden de prestaties van Kerberos-bewerkingen verbeterd. Het is ook mogelijk dat het ticket voor de aangevraagde service wordt ontsleuteld met behulp van het computer account. Dit account wordt ook wel het lokale systeem genoemd. Stel deze waarde in op **True** om KCD te verbreekt wanneer de toepassing wordt gehost op meer dan één server in een farm.

- Schakel als extra controle ook **uitgebreide** beveiliging uit. In sommige scenario's is **uitgebreide** beveiliging KCD wanneer het is ingeschakeld in specifieke configuraties. In dergelijke gevallen is een toepassing gepubliceerd als een submap van de standaard website. Deze toepassing is alleen geconfigureerd voor anonieme verificatie. Alle dialoog vensters worden grijs weer gegeven, waarmee wordt voorgesteld dat onderliggende objecten geen actieve instellingen overnemen. Als dat mogelijk is, wordt u aangeraden deze waarde te testen, maar niet te verg eten om deze in te **scha kelen**.

  Met deze extra controle wordt u op schema gezet om uw gepubliceerde toepassing te gebruiken. U kunt extra connectors instellen die ook zijn geconfigureerd voor delegeren. Lees voor meer informatie de uitgebreide technische [procedure voor het oplossen van problemen met de Azure-AD-toepassingsproxy](https://aka.ms/proxytshootpaper).

Als u nog steeds geen voortgang kunt maken, kan micro soft-ondersteuning u helpen. Een ondersteunings ticket direct in de portal maken. Een technicus neemt contact met u op.

## <a name="other-scenarios"></a>Andere scenario's

- Azure-toepassing proxy een Kerberos-ticket aanvraagt voordat het verzoek naar een toepassing wordt verzonden. Sommige toepassingen van derden vallen niet zoals deze verificatie methode. Deze toepassingen verwachten dat er meer conventionele onderhandelingen worden uitgevoerd. De eerste aanvraag is anoniem, waardoor de toepassing kan reageren met de verificatie typen die het ondersteunt via een 401. Dit type Kerberos-onderhandeling kan worden ingeschakeld met behulp van de stappen die in dit document worden beschreven: [beperkte Kerberos-overdracht voor eenmalige aanmelding](application-proxy-configure-single-sign-on-with-kcd.md).
- Verificatie met meerdere hops wordt doorgaans gebruikt in scenario's waarbij een toepassing wordt gelaagd, met een back-end en front-end, waarbij beide verificatie vereisen, zoals SQL Server Reporting Services. Als u het scenario met meerdere hops wilt configureren, raadpleegt u het ondersteunings artikel [Kerberos-beperkte overdracht vereist mogelijk protocol overgang in scenario's met meerdere hops](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Volgende stappen

[KCD configureren voor een beheerd domein](../../active-directory-domain-services/deploy-kcd.md).