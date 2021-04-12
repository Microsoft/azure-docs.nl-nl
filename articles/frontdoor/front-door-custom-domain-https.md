---
title: 'Zelfstudie: HTTPS in een aangepast domein configureren voor Azure Front Door | Microsoft Docs'
description: In deze zelfstudie leert u hoe u HTTPS kunt in- en uitschakelen in uw configuratie van Azure Front Door voor een aangepast domein.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: duau
ms.openlocfilehash: d2c8d4179dbaa44929031ce7e14b597b145ed72a
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067602"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Zelfstudie: HTTPS configureren in een aangepast Front Door-domein

In deze zelfstudie ziet u hoe u het HTTPS-protocol inschakelt voor een aangepast domein dat onder de sectie Front-endhosts is gekoppeld aan Front Door. Door gebruik te maken van het HTTPS-protocol in uw aangepaste domein (bijvoorbeeld https: \/ /www.contoso.com), zorgt u ervoor dat uw gevoelige gegevens veilig worden afgeleverd via TLS/SSL-versleuteling wanneer ze via internet worden verzonden. Wanneer uw webbrowser is verbonden met een website via HTTPS, wordt het beveiligingscertificaat van de website gevalideerd en wordt er gecontroleerd of het certificaat is uitgegeven door een legitieme certificeringsinstantie. Via dit proces zijn uw webtoepassingen beveiligd tegen aanvallen.

Azure Front Door ondersteunt HTTPS voor een standaardhostnaam van Front Door, zonder dat er aanpassingen nodig zijn. Als u bijvoorbeeld een Front Door maakt (zoals https:`https://contoso.azurefd.net`), wordt HTTPS automatisch ingeschakeld voor aanvragen naar `https://contoso.azurefd.net`. Als u echter het aangepaste domein ' www.contoso.com ' vrijmaakt, moet u ook HTTPS inschakelen voor deze frontend-host.   

Enkele belangrijke kenmerken van de aangepaste HTTPS-functie zijn:

- Geen extra kosten: er zijn geen kosten voor het ophalen of vernieuwen van certificaten en geen extra kosten voor HTTPS-verkeer. 

- Eenvoudig inschakelen: inrichten met één klik is beschikbaar in [Azure Portal](https://portal.azure.com). U kunt ook REST API of andere hulpprogramma’s voor ontwikkelaars gebruiken om de functie in te schakelen.

- Volledig certificaatbeheer is beschikbaar: alle aanschaf en beheer van certificaten wordt voor u afgehandeld. Certificaten worden automatisch ingericht en verlengd vóór de verval datum, waardoor de Risico's van service onderbreking worden verwijderd vanwege een verlopen certificaat.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Het HTTPS-protocol inschakelen in uw aangepast domein
> - Een met AFD beheerd certificaat gebruiken 
> - Uw eigen certificaat gebruiken, dat wil zeggen, een aangepast TLS/SSL-certificaat
> - Het domein valideren
> - Het HTTPS-protocol uitschakelen in uw aangepast domein


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u de stappen in deze zelfstudie kunt voltooien, moet u een Front Door maken waaraan ten minste één aangepast domein is toegevoegd. Zie [Zelfstudie: Een aangepast domein toevoegen aan uw Front Door](front-door-custom-domain.md) voor meer informatie.

## <a name="tlsssl-certificates"></a>TLS/SSL-certificaten

Als u het HTTPS-protocol wilt inschakelen voor het veilig leveren van inhoud aan een aangepast Front Door-domein, moet u een TLS/SSL-certificaat gebruiken. U kunt ervoor kiezen om een certificaat te gebruiken dat wordt beheerd door Azure Front Door, maar u kunt ook uw eigen certificaat gebruiken.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>Optie 1 (standaard): gebruik een certificaat dat wordt beheerd door Front Door

Wanneer u een certificaat gebruikt dat wordt beheerd door Azure Front Door, kan de HTTPS-functie met een paar muisklikken worden ingeschakeld. Azure Front Door verwerkt certificaatbeheertaken van begin tot eind. Denk hierbij aan taken zoals het kopen en vernieuwen van certificaten. Zodra u de functie hebt ingeschakeld, wordt de procedure onmiddellijk gestart. Als het aangepaste domein al is toegewezen aan de standaardfront-endhost van Front Door (`{hostname}.azurefd.net`), is geen verdere actie vereist. Front Door verwerkt de stappen en voltooit uw aanvraag automatisch. Als uw aangepaste domein echter elders is toegewezen, moet u e-mail gebruiken om uw domeinbezit te valideren.

Volg deze stappen om HTTPS in te schakelen in een aangepast domein:

1. Blader in de [Azure-portal](https://portal.azure.com) naar uw profiel van **Front Door**.

2. Selecteer in de lijst met front-endhosts de host met het aangepaste domein waarvoor u HTTPS wilt inschakelen.

3. Onder de para graaf HTTPS van het **domein** selecteert u **ingeschakeld** en selecteert u de **voor deur** die wordt beheerd als de bron van het certificaat.

4. Selecteer Opslaan.

5. Ga door met [het valideren van het domein](#validate-the-domain).

> [!NOTE]
> Voor door AFD beheerde certificaten geldt de limiet van 64 tekens van DigiCert. De validatie mislukt als deze limiet wordt overschreden.

! ERAAN Het inschakelen van HTTPS via een door de voor deur beheerd certificaat wordt niet ondersteund voor de Apex/root-domeinen (bijvoorbeeld: contoso.com). U kunt uw eigen certificaat gebruiken voor dit scenario.  Ga door met de optie 2 voor meer informatie.

### <a name="option-2-use-your-own-certificate"></a>Optie 2: gebruik uw eigen certificaat

U kunt uw eigen certificaat gebruiken voor het inschakelen van de HTTPS-functie. Dit proces verloopt via een integratie met Azure Key Vault, waarmee u uw certificaten veilig kunt opslaan. Azure front deur gebruikt dit beveiligde mechanisme om uw certificaat op te halen en er zijn een paar extra stappen vereist. Wanneer u uw TLS/SSL-certificaat maakt, moet u dat doen met een toegestane certificeringsinstantie (CA). Als u een niet-toegestane CA gebruikt, wordt uw aanvraag geweigerd. Zie [Toegestane certificeringsinstanties voor het inschakelen van aangepaste HTTPS in Azure Front Door](front-door-troubleshoot-allowed-ca.md) voor een lijst met toegestane certificeringsinstanties.

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Voorbereiden van uw Azure Key Vault-account en -certificaat
 
1. Azure Key Vault: u moet een Azure Key Vault-account hebben onder hetzelfde abonnement als de Front Door die u wilt inschakelen voor aangepaste HTTPS. Maak een Azure Key Vault-account als u er nog geen hebt.

> [!WARNING]
> Azure Front Door ondersteunt momenteel alleen Key Vault-accounts in hetzelfde abonnement als de Front Door-configuratie. Als u een sleutelkluis kiest van een ander abonnement dan de Front Door, treedt er een fout op.

2. Azure Key Vault-certificaten: als u al in het bezit bent van een certificaat, kunt u dit rechtstreeks uploaden naar uw Azure Key Vault-account of u kunt via Azure Key Vault direct een certificaat maken bij een van de partnercertificeringsinstanties waarmee Azure Key Vault is geïntegreerd. Upload uw certificaat als een **certificaat**-object in plaats van een **geheim**.

> [!NOTE]
> Voor uw eigen TLS/SSL-certificaat biedt Front Door geen ondersteuning voor certificaten met EC-cryptografie-algoritmen.

#### <a name="register-azure-front-door"></a>Azure Front Door registreren

Registreer de service-principal voor Azure Front Door als een app in uw Azure Active Directory via PowerShell.

> [!NOTE]
> Voor deze actie zijn globale beheerdersmachtigingen vereist en de actie moet maar **eenmaal** per tenant worden uitgevoerd.

1. Installeer zo nodig [Azure PowerShell](/powershell/azure/install-az-ps) in PowerShell op uw lokale computer.

2. Voer in PowerShell de volgende opdracht uit:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Azure Front Door toegang verlenen tot uw sleutelkluis
 
Geef Azure Front Door toegang tot de certificaten in uw Azure Key Vault-account.

1. Selecteer in uw Key Vault-account onder instellingen **Toegangsbeleid**, selecteer daarna **Nieuwe toevoegen** om een nieuw beleid te maken.

2. Zoek bij **Principal selecteren** naar **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** en kies **Microsoft.Azure.Frontdoor**. Klik op **Selecteren**.

3. Selecteer in **Geheime machtigingen** de optie **Ophalen** om Front Door het certificaat te laten ophalen.

4. Selecteer in **Certificaatmachtigingen** de optie **Ophalen** om Front Door het certificaat te laten ophalen.

5. Selecteer **OK**. 

    Azure Front Door heeft nu toegang tot deze sleutelkluis en de certificaten die in deze sleutelkluis zijn opgeslagen.
 
#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Het certificaat voor implementatie door Azure Front Door selecteren
 
1. Ga terug naar uw Front Door in de portal. 

2. Selecteer in de lijst met aangepaste domeinen het aangepaste domein waarvoor u HTTPS wilt inschakelen.

    De pagina **Aangepast domein** wordt weergegeven.

3. Kies onder Certificaatbeheertype **Mijn eigen certificaat gebruiken**. 

4. Azure Front Door vereist dat het abonnement van het Key Vault-account hetzelfde is als dat van uw Front Door. Selecteer een sleutel kluis, geheim en geheime versie.

    Azure Front Door geeft de volgende gegevens weer: 
    - De sleutelkluis-accounts voor uw abonnement-ID. 
    - De geheimen onder de geselecteerde sleutel kluis. 
    - De beschik bare geheime versies.

    > [!NOTE]
    >  Als u het certificaat automatisch naar de nieuwste versie wilt draaien als er een nieuwere versie van het certificaat beschikbaar is in uw Key Vault, stelt u de geheime versie in op meest recente. Als er een specifieke versie is geselecteerd, moet u de nieuwe versie hand matig opnieuw selecteren voor het draaien van certificaten. Het duurt Maxi maal 24 uur voordat de nieuwe versie van het certificaat/geheim wordt geïmplementeerd. 
 
5. Wanneer u uw eigen certificaat gebruikt, is domein validatie niet vereist. Blijf [wachten op doorgifte](#wait-for-propagation).

## <a name="validate-the-domain"></a>Het domein valideren

Als u al een aangepast domein gebruikt dat wordt toegewezen aan uw aangepaste eind punt met een CNAME-record of als u uw eigen certificaat gebruikt, gaat u door naar [aangepast domein is toegewezen aan de voor deur](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). Als het item met de CNAME-record voor uw domein niet meer bestaat of het subdomein afdverify bevat, gaat u naar [aangepast domein is niet toegewezen aan de voor deur](#custom-domain-is-not-mapped-to-your-front-door).

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>Er is geen aangepast domein toegewezen aan uw Front Door met een CNAME-record

Toen u een aangepast domein toevoegde aan de front-endhosts van uw Front Door, hebt u een CNAME-record gemaakt in de DNS-tabel van uw domeinregistrar om het domein toe te wijzen aan de standaardhostnaam .azurefd.net van uw Front Door. Als de CNAME-record nog bestaat en niet het subdomein afdverify bevat, gebruikt de DigiCert-certificerings instantie deze om automatisch het eigendom van uw aangepaste domein te valideren. 

Als u uw eigen certificaat gebruikt, is domein validatie niet vereist.

Uw CNAME-record moet de volgende indeling hebben, waarbij *Naam* de naam van het aangepaste domein is en *Waarde* de standaardhostnaam .azurefd.net van uw Front Door:

| Naam            | Type  | Waarde                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

Zie [Create the CNAME DNS record](../cdn/cdn-map-content-to-custom-domain.md) (De CNAME DNS-record maken) voor meer informatie over CNAME-records.

Als de CNAME-record de juiste indeling heeft, wordt de naam van het aangepaste domein automatisch geverifieerd met DigiCert en wordt er een toegewezen certificaat voor uw domeinnaam gemaakt. U ontvangt via DigiCert geen verificatie-e-mail en u hoeft uw aanvraag niet goed te keuren. Het certificaat is één jaar geldig en wordt, vóórdat het verloopt, automatisch vernieuwd. Blijf [wachten op doorgifte](#wait-for-propagation). 

Automatische validatie duurt meestal een paar minuten. Als het domein na een uur nog niet is gevalideerd, opent u een ondersteuningsticket.

>[!NOTE]
>Als u beschikt over een CAA-record (Certificate Authority Authorization) bij uw DNS-provider, moet deze DigiCert bevatten als een geldige CA. Met een CAA-record kunnen domeineigenaars bij hun DNS-provider opgeven welke CA’s zijn geautoriseerd om certificaten te verlenen voor hun domein. Als een CA een bestelling ontvangt voor een certificaat voor een domein met een CAA-record, en deze CA wordt niet vermeld als een geautoriseerde verlener, mag de CA het certificaat niet verlenen aan dit domein of subdomein. Zie [CAA-records beheren](https://support.dnsimple.com/articles/manage-caa-record/) voor meer informatie over het beheren van CAA-records. Zie [CAA-record Helper](https://sslmate.com/caa/) voor een hulpprogramma voor CAA-records.

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>Het aangepaste domein is niet toegewezen aan uw Front Door

Als de CNAME-record voor uw eindpunt niet meer bestaat of als deze het subdomein afdverify bevat, volgt u de rest van de instructies in deze stap.

Nadat u HTTPS hebt ingeschakeld voor uw aangepaste domein, wordt met de DigiCert-CA het eigendom van het domein gevalideerd door contact op te nemen met de bijbehorende registrator, op basis van de [WHOIS](http://whois.domaintools.com/)-registratiegegevens van het domein. Contact verloopt via het e-mailadres (standaard) of het telefoonnummer dat staat vermeld in de WHOIS-registratie. U moet de domeinvalidatie voltooien voordat HTTPS actief is voor uw aangepaste domein. U hebt zes werkdagen de tijd om het domein goed te keuren. Aanvragen die niet binnen zes werk dagen zijn goedgekeurd, worden automatisch geannuleerd. 

![WHOIS-record](./media/front-door-custom-domain-https/whois-record.png)

DigiCert stuurt ook een verificatie-e-mail naar andere e-mail adressen. Als de WHOIS-registratiegegevens privé zijn, verifieert u dat u direct kunt goedkeuren vanaf een van de volgende adressen:

admin@&lt;uw-domeinnaam.com&gt;  
administrator@&lt;uw-domeinnaam.com&gt;  
webmaster@&lt;uw-domeinnaam.com&gt;  
hostmaster@&lt;uw-domeinnaam.com&gt;  
postmaster@&lt;uw-domeinnaam.com&gt;  

U ontvangt binnen enkele minuten een e-mailbericht, vergelijkbaar met het bericht in het volgende voorbeeld, waarin u wordt gevraagd om de aanvraag goed te keuren. Als u een spam filter gebruikt, voegt u toe admin@digicert.com aan de allowlist. Als u na 24 uur nog geen e-mailbericht hebt ontvangen, neemt u contact op met Microsoft Ondersteuning.

Wanneer u de goedkeurings koppeling selecteert, wordt u omgeleid naar een online goedkeurings formulier. Volg de instructies op het formulier. U hebt twee verificatieopties:

- U kunt alle toekomstige bestellingen goedkeuren die met hetzelfde account zijn geplaatst voor hetzelfde hoofddomein, bijvoorbeeld contoso.com. Deze methode wordt aanbevolen als u van plan bent om meer aangepaste domeinen voor hetzelfde hoofd domein toe te voegen.

- U kunt alleen de specifieke hostnaam goedkeuren die wordt gebruikt in deze aanvraag. Voor volgende aanvragen is extra goed keuring vereist.

Na goedkeuring wordt het certificaat voor de naam van het aangepaste domein met DigiCert voltooid. Het certificaat is één jaar geldig en wordt, vóórdat het verloopt, automatisch vernieuwd.

## <a name="wait-for-propagation"></a>Wachten op doorgifte

Nadat de domeinnaam is gevalideerd, duurt het maximaal 6 tot 8 uur voordat de HTTPS-functie van het aangepaste domein is geactiveerd. Wanneer het proces is voltooid, is de aangepaste HTTPS-status in Azure Portal ingesteld op **Ingeschakeld** en zijn de vier bewerkingsstappen in het dialoogvenster Aangepast domein gemarkeerd als Voltooid. Het aangepaste domein is nu klaar voor gebruik van HTTPS.

### <a name="operation-progress"></a>Bewerkingsvoortgang

In de volgende tabel wordt de bewerkingsvoortgang weergegeven die plaatsvindt nadat u HTTPS hebt ingeschakeld. Als u HTTPS hebt ingeschakeld, worden vier bewerkingsstappen weergegeven in het dialoogvenster Aangepast domein. Wanneer elke stap actief wordt, worden er meer details van substap weer gegeven onder de stap terwijl deze wordt uitgevoerd. Niet al deze substappen worden uitgevoerd. Nadat een stap is voltooid, wordt naast deze stap een groen vinkje weergegeven. 

| Bewerkingsstap | Details van bewerkingssubstap | 
| --- | --- |
| 1 Aanvraag verzenden | Aanvraag verzenden |
| | De HTTPS-aanvraag wordt verzonden. |
| | De HTTPS-aanvraag is verzonden. |
| 2 Domeinvalidatie | Het domein wordt automatisch gevalideerd als de CNAME is toegewezen aan de default. azurefd.net frontend-host van uw voor deur. In alle andere gevallen wordt er een verificatieaanvraag verzonden naar het e-mailadres dat wordt vermeld in de registratierecord (WHOIS-registrator) van uw domein. Verifieer het domein zo snel mogelijk. |
| | Uw domeineigendom is gevalideerd. |
| | Validatieaanvraag voor eigendom van het domein is verlopen (de klant heeft waarschijnlijk niet binnen zes dagen gereageerd). HTTPS wordt niet ingeschakeld voor uw domein. * |
| | Validatieaanvraag voor eigendom van het domein is geweigerd door de klant. HTTPS wordt niet ingeschakeld voor uw domein. * |
| 3 Certificaat inrichten | De certificeringsinstantie verleent momenteel het certificaat dat nodig is om HTTPS in te schakelen in uw domein. |
| | Het certificaat is verleend en wordt momenteel geïmplementeerd naar uw Front Door. Dit proces duurt maximaal één uur. |
| | Het certificaat is geïmplementeerd naar uw Front Door. |
| 4 Voltooien | HTTPS is ingeschakeld in uw domein. |

\* Dit bericht wordt niet weergegeven tenzij er een fout is opgetreden. 

Als er een fout optreedt voordat de aanvraag is verzonden, wordt het volgende foutbericht weergegeven:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

1. *Wie is de certificaatprovider en welk type certificaat is gebruikt?*

    Er wordt een gereserveerd/specifiek certificaat, geleverd via Digicert, gebruikt voor uw aangepaste domein. 

2. *Gebruikt u IP of SNI TLS/SSL?*

    Azure Front Door maakt gebruik van SNI TLS/SSL.

3. *Wat moet ik doen als ik geen verificatie-e-mail voor het domein heb ontvangen van DigiCert?*

    Als u een CNAME-vermelding voor uw aangepaste domein hebt dat rechtstreeks naar de hostnaam van het eind punt wijst (en u niet de afdverify-subdomeinnaam gebruikt), ontvangt u geen e-mail verificatie van domein. Validatie wordt dan automatisch uitgevoerd. In andere gevallen waarbij u geen CNAME-vermelding hebt en binnen 24 uur geen e-mail hebt ontvangen, neemt u contact op met Microsoft Ondersteuning.

4. *Is het gebruik van een SAN-certificaat minder veilig dan wanneer ik een toegewezen certificaat gebruik?*
    
    Voor een SAN-certificaat gelden dezelfde standaarden voor versleuteling en beveiliging als voor een toegewezen certificaat. Alle verleende TLS/SSL-certificaten maken gebruik van SHA-256 voor verbeterde serverbeveiliging.

5. *Heb ik een CAA-record nodig bij mijn DNS-provider?*

    Nee, er is op dit moment geen autorisatie record voor de certificerings instantie vereist. Als u er echter wel een hebt, moet deze DigiCert bevatten als een geldige CA.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u het HTTPS-protocol in uw aangepaste domein ingeschakeld. Als u uw aangepaste domein niet meer wilt gebruiken met HTTPS, kunt u HTTPS uitschakelen door de volgende stappen uit te voeren:

### <a name="disable-the-https-feature"></a>De HTTPS-functie uitschakelen 

1. Blader in [Azure Portal](https://portal.azure.com) naar de configuratie van **Azure Front Door**.

2. Selecteer in de lijst met frontend-hosts het aangepaste domein waarvoor u HTTPS wilt uitschakelen.

3. Klik op **Uitgeschakeld** om HTTPS uit te schakelen. Klik vervolgens op **Opslaan**.

### <a name="wait-for-propagation"></a>Wachten op doorgifte

Nadat de HTTPS-functie voor aangepaste domeinen is uitgeschakeld, duurt het maximaal 6 tot 8 uur voordat dit is doorgevoerd. Wanneer het proces is voltooid, wordt de aangepaste HTTPS-status in de Azure Portal ingesteld op **uitgeschakeld** en worden de drie bewerkings stappen in het dialoog venster aangepast domein gemarkeerd als voltooid. Het aangepaste domein kan niet meer gebruikmaken van HTTPS.

#### <a name="operation-progress"></a>Bewerkingsvoortgang

In de volgende tabel wordt de bewerkingsvoortgang weergegeven die plaatsvindt nadat u HTTPS hebt uitgeschakeld. Als u HTTPS hebt uitgeschakeld, worden drie bewerkingsstappen weergegeven in het dialoogvenster Aangepast domein. Wanneer elke stap actief wordt, worden er meer details weer gegeven onder de stap. Nadat een stap is voltooid, wordt naast deze stap een groen vinkje weergegeven. 

| Bewerkingsvoortgang | Bewerkingsdetails | 
| --- | --- |
| 1 Aanvraag verzenden | De aanvraag verzenden |
| 2 Inrichting van het certificaat ongedaan maken | Certificaat verwijderen |
| 3 Voltooien | Certificaat is verwijderd |

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

* Een certificaat uploaden naar Key Vault.
* Een domein valideren.
* Schakel HTTPS in voor uw aangepaste domein.

Ga verder met de volgende zelf studie voor meer informatie over het instellen van een beleid voor geografische filtering voor uw voor deur.

> [!div class="nextstepaction"]
> [Een geofilteringsbeleid instellen](front-door-geo-filtering.md)