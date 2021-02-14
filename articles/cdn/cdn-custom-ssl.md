---
title: 'Zelfstudie: HTTPS op een aangepast Azure CDN-domein configureren'
description: In deze zelfstudie leert u hoe u HTTPS kunt inschakelen en uitschakelen op het Azure CDN-eindpunt van een aangepast domein.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 61ba50f8ec9e1de18238160b23096670753cffd6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367500"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Zelfstudie: HTTPS op een aangepast Azure CDN-domein configureren

In deze zelfstudie ziet u hoe u het HTTPS-protocol inschakelt voor een aangepast domein dat is gekoppeld aan een Azure CDN-eindpunt. 

Het HTTPS-protocol in uw aangepaste domein (bijvoorbeeld https: \/ /www.contoso.com), zorgt ervoor dat uw gevoelige gegevens veilig worden bezorgd via TLS/SSL. Wanneer uw webbrowser is verbonden via HTTPS, valideert de browser het certificaat van de website. De browser controleert of deze is uitgegeven door een legitieme certificerings instantie. Via dit proces zijn uw webtoepassingen beveiligd tegen aanvallen.

Azure CDN biedt standaard ondersteuning voor HTTPS voor een hostnaam van een CDN-eindpunt. Als u een CDN-eindpunt maakt (bijvoorbeeld https:\//contoso.azureedge.net), wordt HTTPS automatisch ingeschakeld.  

Enkele belangrijke kenmerken van de aangepaste HTTPS-functie zijn:

- Geen extra kosten: er zijn geen kosten voor het ophalen of vernieuwen van certificaten en geen extra kosten voor HTTPS-verkeer. U betaalt alleen voor de uitgaande GB van het CDN.

- Eenvoudig inschakelen: inrichten met één klik is beschikbaar in [Azure Portal](https://portal.azure.com). U kunt ook REST API of andere hulpprogramma’s voor ontwikkelaars gebruiken om de functie in te schakelen.

- Volledig certificaatbeheer is beschikbaar: 
    * alle aanschaf en beheer van certificaten wordt voor u afgehandeld. 
    * Certificaten worden automatisch ingericht en verlengd vóór de verval datum.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Het HTTPS-protocol inschakelen in uw aangepast domein
> - Een CDN-beheerd certificaat gebruiken 
> - Uw eigen certificaat gebruiken
> - Het domein valideren
> - Het HTTPS-protocol uitschakelen in uw aangepast domein.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Voordat u de stappen in deze zelfstudie kunt voltooien, moet u eerst een CDN-profiel en ten minste één CDN-eindpunt maken. Zie voor meer informatie [Snelstartgids: Een Azure CDN-profiel en een eindpunt maken](cdn-create-new-endpoint.md).

Een Azure CDN aangepast domein koppelen aan uw CDN-eind punt. Zie [Zelfstudie: Een aangepast domein toevoegen aan uw Azure CDN-eindpunt](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> CDN-beheerde certificaten zijn niet beschikbaar voor root-of apex-domeinen. Als uw Azure CDN aangepast domein een root- of apex-domein is, moet u de functie Uw eigen certificaat gebruiken. 
>

---

## <a name="tlsssl-certificates"></a>TLS/SSL-certificaten

Als u HTTPS wilt inschakelen op een Azure CDN aangepast domein, gebruikt u een TLS/SSL-certificaat. U kiest voor het gebruik van een certificaat dat wordt beheerd door Azure CDN of uw certificaat gebruiken.

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Optie 1 (standaard): HTTPS met een door CDN beheerd certificaat inschakelen](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Azure CDN beheert certificaat beheer taken, zoals aanschaf en verlenging. Zodra u de functie hebt ingeschakeld, wordt de procedure onmiddellijk gestart. 

Als het aangepaste domein al aan het CDN-eind punt is toegewezen, is er geen verdere actie nodig. Azure CDN verwerkt de stappen en voltooit uw aanvraag automatisch.

Als uw aangepaste domein ergens anders wordt toegewezen, gebruikt u e-mail om uw domein eigendom te valideren.

Volg deze stappen om HTTPS in te schakelen in een aangepast domein:

1. Ga naar het [Azure Portal](https://portal.azure.com) om een certificaat te vinden dat wordt beheerd door uw Azure CDN. Zoek en selecteer **CDN-profielen**. 

2. Kies uw profiel:
    * **Azure CDN Standard van Microsoft**
    * **Azure CDN Standard van Akamai**
    * **Azure CDN Standard van Verizon**
    * **Azure CDN Premium van Verizon**

3. Selecteer in de lijst met CDN-eindpunten het eindpunt met het aangepaste domein.

    ![Lijst met eindpunten](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    De pagina **Eindpunt** wordt weergegeven.

4. Selecteer in de lijst met aangepaste domeinen het aangepaste domein waarvoor u HTTPS wilt inschakelen.

    ![Schermopname toont de pagina Aangepast domein met de optie voor het Gebruik van mijn eigen certificaat.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    De pagina **Aangepast domein** wordt weergegeven.

5. Kies onder Certificaatbeheertype **CDN-beheerd**.

6. Selecteer **Aan** om HTTPS in te schakelen.

    ![HTTP-status voor aangepast domein](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Ga door met [het valideren van het domein](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificate"></a>[Optie 2: HTTPS met uw eigen certificaat inschakelen](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Deze optie is alleen beschikbaar bij **Azure CDN van Microsoft** en **Azure CDN van Verizon** -profielen. 
>
 
U kunt uw eigen certificaat gebruiken voor het inschakelen van de HTTPS-functie. Dit proces verloopt via een integratie met Azure Key Vault, waarmee u uw certificaten veilig kunt opslaan. Azure CDN gebruikt dit beveiligde mechanisme om uw certificaat op te halen en er zijn een paar extra stappen vereist. Wanneer u uw TLS/SSL-certificaat maakt, moet u dat doen met een toegestane certificeringsinstantie (CA). Als u een niet-toegestane CA gebruikt, wordt uw aanvraag geweigerd. Zie [Toegestane certificeringsinstanties voor het inschakelen van aangepaste HTTPS op Azure CDN](cdn-troubleshoot-allowed-ca.md) voor een lijst met toegestane certificeringsinstanties. Voor **Azure CDN van Verizon** wordt elke geldige certificerings instantie geaccepteerd. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Voorbereiden van uw Azure Key Vault-account en -certificaat
 
1. Azure Key Vault: u moet een Azure Key Vault-account hebben onder hetzelfde abonnement als het Azure CDN-profiel en de CDN-eindpunten die u wilt inschakelen voor aangepaste HTTPS. Maak een Azure Key Vault-account, als u dit niet hebt.
 
2. Azure Key Vault certificaten: als u een certificaat hebt, kunt u dit rechtstreeks uploaden naar uw Azure Key Vault-account. Als u nog geen certificaat hebt, maakt u rechtstreeks een nieuw certificaat via Azure Key Vault.

### <a name="register-azure-cdn"></a>Azure CDN registreren

Registreer Azure CDN als een app in Azure Active Directory via PowerShell.

1. Installeer zo nodig [Azure PowerShell](/powershell/azure/install-az-ps) op uw lokale computer.

2. Voer in PowerShell de volgende opdracht uit:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** is de service-principal voor **micro soft. AzureFrontDoor-CDN**.

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Azure CDN toegang verlenen tot uw sleutelkluis
 
Geef Azure CDN toegang tot de certificaten (geheimen) in uw Azure Key Vault-account.

1. Selecteer **toegangs beleid** in de sleutel kluis in de sectie **instellingen** . Selecteer in het rechterdeel venster **+ toegangs beleid toevoegen**:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="Een beleid voor sleutel kluis toegang maken voor CDN" border="true":::

2. Selecteer op de pagina **toegangs beleid toevoegen** de optie **geen geselecteerd** naast **Principal selecteren**. Voer op de pagina **Principal** **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** in. Selecteer **micro soft. AzureFrontdoor-CDN**.  Kies **selecteren**:

2. In **hoofd selecteren**, zoek naar **205478C0-bd83-4e1b-a9d6-db63a3e1e1c8**, kies **micro soft. AzureFrontDoor-CDN**. Kies **Selecteren**.

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="De service-principal van Azure CDN selecteren" border="true":::
    
3. Selecteer **certificaat machtigingen**. Schakel de selectie vakjes voor **ophalen** en **lijst** in om CDN-machtigingen in staat te stellen de certificaten op te halen en weer te geven.

4. Selecteer **geheime machtigingen**. Schakel de selectie vakjes voor **Get** en **List** in om CDN-machtigingen toe te staan om de geheimen op te halen en weer te geven:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="Machtigingen voor CDN selecteren in de sleutel kluis" border="true":::

5. Selecteer **Toevoegen**. 

> [!NOTE]
> Azure CDN heeft nu toegang tot deze sleutelkluis en de certificaten (geheimen) die in deze sleutelkluis zijn opgeslagen. Elk CDN-exemplaar dat in dit abonnement is gemaakt, heeft toegang tot de certificaten in deze sleutel kluis. 

 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Het certificaat voor implementatie door Azure CDN selecteren
 
1. Ga terug naar de Azure CDN-portal en selecteer het profiel en CDN-eindpunt voor het inschakelen van aangepaste HTTPS. 

2. Selecteer in de lijst met aangepaste domeinen het aangepaste domein waarvoor u HTTPS wilt inschakelen.

    De pagina **Aangepast domein** wordt weergegeven.

3. Kies onder Certificaatbeheertype **Mijn eigen certificaat gebruiken**. 

    ![Uw certificaat configureren](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Selecteer een sleutelkluis, certificaat (geheim) en certificaatversie.

    Azure CDN noemt de volgende informatie: 
    - De sleutelkluis-accounts voor uw abonnement-ID. 
    - De certificaten (geheimen) onder de geselecteerde sleutelkluis. 
    - De beschikbare certificaatversies. 
 
5. Selecteer **Aan** om HTTPS in te schakelen.
  
6. Wanneer u uw certificaat gebruikt, is domein validatie niet vereist. Blijf [wachten op doorgifte](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>Het domein valideren

Als u een aangepast domein gebruikt dat is toegewezen aan uw aangepaste eind punt met een CNAME-record of als u uw eigen certificaat gebruikt, gaat u door met [aangepast domein toegewezen aan uw CDN-eind punt](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). 

Als het item met de CNAME-record voor uw eind punt niet meer bestaat of het subdomein cdnverify bevat, gaat u verder naar [aangepast domein niet toegewezen aan uw CDN-eind punt](#custom-domain-isnt-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Er is een aangepast domein toegewezen aan uw CDN-eindpunt met een CNAME-record

Wanneer u een aangepast domein aan uw eind punt hebt toegevoegd, hebt u een CNAME-record gemaakt in de DNS-domein registratie die is toegewezen aan de hostnaam van uw CDN-eind punt. 

Als de CNAME-record nog bestaat en niet het subdomein cdnverify bevat, gebruikt de CA van de DigiCert deze om automatisch het eigendom van uw aangepaste domein te valideren. 

Als u uw eigen certificaat gebruikt, is domein validatie niet vereist.

De CNAME-record moet de volgende indeling hebben:

* *Naam* is uw aangepaste domein naam.
* *Waarde* is de hostnaam van uw CDN-eind punt.

| Naam            | Type  | Waarde                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Zie [Create the CNAME DNS record](./cdn-map-content-to-custom-domain.md) (De CNAME DNS-record maken) voor meer informatie over CNAME-records.

Als uw CNAME-record de juiste indeling heeft, controleert DigiCert automatisch uw aangepaste domein naam en maakt een certificaat voor uw domein. U ontvangt via DigiCert geen verificatie-e-mail en u hoeft uw aanvraag niet goed te keuren. Het certificaat is één jaar geldig en wordt, vóórdat het verloopt, automatisch vernieuwd. Blijf [wachten op doorgifte](#wait-for-propagation). 

Automatische validatie duurt meestal een paar minuten. Als het domein na 24 uur nog niet is gevalideerd, opent u een ondersteuningsticket.

>[!NOTE]
>Als u beschikt over een CAA-record (Certificate Authority Authorization) bij uw DNS-provider, moet deze DigiCert bevatten als een geldige CA. Met een CAA-record kunnen domeineigenaars bij hun DNS-provider opgeven welke CA’s zijn geautoriseerd om certificaten te verlenen voor hun domein. Als een CA een bestelling ontvangt voor een certificaat voor een domein met een CAA-record, en deze CA wordt niet vermeld als een geautoriseerde verlener, mag de CA het certificaat niet verlenen aan dit domein of subdomein. Zie [CAA-records beheren](https://support.dnsimple.com/articles/manage-caa-record/) voor meer informatie over het beheren van CAA-records. Zie [CAA-record Helper](https://sslmate.com/caa/) voor een hulpprogramma voor CAA-records.

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>Het aangepaste domein is niet toegewezen aan uw CDN-eind punt

>[!NOTE]
>Als u **Azure CDN van Akamai** gebruikt, moet de volgende CNAME worden ingesteld om automatische domeinvalidatie in te schakelen. "_acme-challenge.&lt;custom domain hostname&gt; -> CNAME -> &lt;custom domain hostname&gt;.ak-acme-challenge.azureedge.net"

Als de CNAME-recordvermelding voor uw eindpunt niet meer bestaat of als deze het subdomein cdnverify bevat, volgt u de rest van de instructies in deze stap.

Via DigiCert wordt ook een verificatie-e-mail verzonden naar de volgende e-mailadressen. Verifieer of u direct kunt goedkeuren vanaf een van de volgende adressen:

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

U ontvangt binnen een paar minuten een e-mail om de aanvraag goed te keuren. Als u een spam filter gebruikt, voegt u toe verification@digicert.com aan de acceptatie lijst. Als u na 24 uur nog geen e-mailbericht hebt ontvangen, neemt u contact op met Microsoft Ondersteuning.
    
![E-mailbericht voor domeinvalidatie](./media/cdn-custom-ssl/domain-validation-email.png)

Wanneer u de goedkeurings koppeling selecteert, wordt u omgeleid naar het volgende formulier voor online goed keuring: 
    
![Formulier voor domeinvalidatie](./media/cdn-custom-ssl/domain-validation-form.png)

Volg de instructies op het formulier. U hebt twee verificatieopties:

- U kunt alle toekomstige bestellingen goedkeuren die met hetzelfde account zijn geplaatst voor hetzelfde hoofddomein, bijvoorbeeld contoso.com. Deze methode wordt aanbevolen als u van plan bent om andere aangepaste domeinen voor hetzelfde hoofd domein toe te voegen.

- U kunt alleen de specifieke hostnaam goedkeuren die wordt gebruikt in deze aanvraag. Voor latere aanvragen is een andere goed keuring vereist.

Na goedkeuring wordt het certificaat voor de naam van het aangepaste domein met DigiCert voltooid. Het certificaat is één jaar geldig en wordt, vóórdat het verloopt, automatisch vernieuwd.

## <a name="wait-for-propagation"></a>Wachten op doorgifte

Nadat de domeinnaam is gevalideerd, duurt het maximaal 6 tot 8 uur voordat de HTTPS-functie van het aangepaste domein is geactiveerd. Wanneer het proces is voltooid, wordt de aangepaste HTTPS-status in het Azure Portal gewijzigd in **ingeschakeld**. De vier bewerkings stappen in het dialoog venster aangepast domein zijn gemarkeerd als voltooid. Het aangepaste domein is nu klaar voor gebruik van HTTPS.

![Dialoogvenster HTTPS inschakelen](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Bewerkingsvoortgang

In de volgende tabel wordt de bewerkingsvoortgang weergegeven die plaatsvindt nadat u HTTPS hebt ingeschakeld. Als u HTTPS hebt ingeschakeld, worden vier bewerkingsstappen weergegeven in het dialoogvenster Aangepast domein. Wanneer elke stap actief wordt, worden er details van andere substaps weer gegeven onder de stap terwijl deze wordt uitgevoerd. Niet al deze substappen worden uitgevoerd. Nadat een stap is voltooid, wordt naast deze stap een groen vinkje weergegeven. 

| Bewerkingsstap | Details van bewerkingssubstap | 
| --- | --- |
| 1 Aanvraag verzenden | Aanvraag verzenden |
| | De HTTPS-aanvraag wordt verzonden. |
| | De HTTPS-aanvraag is verzonden. |
| 2 Domeinvalidatie | Het domein wordt automatisch gevalideerd als het CNAME-bestand is toegewezen aan het CDN-eind punt. In andere gevallen wordt een verificatieaanvraag verzonden naar het e-mailadres dat wordt vermeld in de registratierecord (WHOIS-registrator) van uw domein.|
| | Uw domeineigendom is gevalideerd. |
| | Validatieaanvraag voor eigendom van het domein is verlopen (de klant heeft waarschijnlijk niet binnen zes dagen gereageerd). HTTPS wordt niet ingeschakeld voor uw domein. * |
| | Validatieaanvraag voor eigendom van het domein is geweigerd door de klant. HTTPS wordt niet ingeschakeld voor uw domein. * |
| 3 Certificaat inrichten | De certificeringsinstantie verleent momenteel het certificaat dat nodig is om HTTPS in te schakelen in uw domein. |
| | Het certificaat is verleend en wordt momenteel geïmplementeerd in het CDN-netwerk. Dit duurt maximaal 6 uur. |
| | Het certificaat is geïmplementeerd in het CDN-netwerk. |
| 4 Voltooien | HTTPS is ingeschakeld in uw domein. |

\* Dit bericht wordt niet weergegeven tenzij er een fout is opgetreden. 

Als er een fout optreedt voordat de aanvraag is verzonden, wordt het volgende foutbericht weergegeven:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Resources opschonen - HTTPS uitschakelen

In deze sectie leert u hoe u HTTPS kunt uitschakelen voor uw aangepaste domein.

### <a name="disable-the-https-feature"></a>De HTTPS-functie uitschakelen 

1. Zoek en selecteer **CDN-profielen** in het [Azure Portal](https://portal.azure.com). 

2. Kies uw **Azure CDN Standard van Microsoft**, **Azure CDN Standard van Verizon** of **Azure CDN Premium van Verizon**-profiel.

3. Klik in de lijst met eindpunten op het eindpunt met uw aangepaste domein.

4. Klik op het aangepaste domein waarvoor u HTTPS wilt uitschakelen.

    ![Lijst met aangepaste domeinen](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Klik op **Uit** om HTTPS uit te schakelen, klik vervolgens op **Toepassen**.

    ![Dialoogvenster Aangepaste HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Wachten op doorgifte

Nadat de HTTPS-functie voor aangepaste domeinen is uitgeschakeld, duurt het maximaal 6 tot 8 uur voordat dit is doorgevoerd. Wanneer het proces is voltooid, wordt de aangepaste HTTPS-status in de Azure Portal gewijzigd in **uitgeschakeld**. De drie bewerkings stappen in het dialoog venster aangepast domein zijn gemarkeerd als voltooid. Het aangepaste domein kan niet meer gebruikmaken van HTTPS.

![Dialoogvenster HTTPS uitschakelen](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Bewerkingsvoortgang

In de volgende tabel wordt de bewerkingsvoortgang weergegeven die plaatsvindt nadat u HTTPS hebt uitgeschakeld. Nadat u HTTPS hebt uitgeschakeld, worden er drie bewerkings stappen weer gegeven in het dialoog venster aangepast domein. Wanneer een stap actief wordt, worden details weer gegeven onder de stap. Nadat een stap is voltooid, wordt naast deze stap een groen vinkje weergegeven. 

| Bewerkingsvoortgang | Bewerkingsdetails | 
| --- | --- |
| 1 Aanvraag verzenden | De aanvraag verzenden |
| 2 Inrichting van het certificaat ongedaan maken | Certificaat verwijderen |
| 3 Voltooien | Certificaat is verwijderd |

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

1. *Wie is de certificaatprovider en welk type certificaat is gebruikt?*

    Een toegewezen certificaat dat door Digicert wordt gegeven, wordt gebruikt voor uw aangepaste domein voor:
    
    * **Azure CDN van Verizon**
    * **Azure CDN van micro soft**

2. *Gebruikt u IP of SNI TLS/SSL?*

    Zowel **Azure CDN van Verizon** als **Azure CDN Standard van Microsoft** gebruikt SNI TLS/SSL.

3. *Wat moet ik doen als ik geen verificatie-e-mail voor het domein heb ontvangen van DigiCert?*

    Als u het subdomein *cdnverify* niet gebruikt en uw CNAME-vermelding voor de hostnaam van uw eind punt is, ontvangt u geen e-mail verificatie van domein.
     
    Validatie wordt dan automatisch uitgevoerd. In andere gevallen waarbij u geen CNAME-vermelding hebt en binnen 24 uur geen e-mail hebt ontvangen, neemt u contact op met Microsoft Ondersteuning.

4. *Is het gebruik van een SAN-certificaat minder veilig dan wanneer ik een toegewezen certificaat gebruik?*
    
    Voor een SAN-certificaat gelden dezelfde standaarden voor versleuteling en beveiliging als voor een toegewezen certificaat. Alle verleende TLS/SSL-certificaten maken gebruik van SHA-256 voor verbeterde serverbeveiliging.

5. *Heb ik een CAA-record nodig bij mijn DNS-provider?*

    De autorisatie record voor de certificerings instantie is momenteel niet vereist. Als u er echter wel een hebt, moet deze DigiCert bevatten als een geldige CA.

6. *Vanaf 20 juni 2018 gebruikt Azure CDN van Verizon standaard een toegewezen certificaat met SNI TLS/SSL. Wat gebeurt er met mijn bestaande aangepaste domeinen die gebruik maken van een SAN-certificaat (Subject Alternative Names) en TLS/SSL op basis van IP?*

    Uw bestaande domeinen worden de komende maanden geleidelijk gemigreerd naar één certificaat, als Microsoft analyseert dat alleen SNI-clientaanvragen aan uw toepassing worden gericht. 
    
    Als niet-SNI-clients worden gedetecteerd, blijven uw domeinen in het SAN-certificaat met TLS/SSL op basis van IP. Aanvragen voor uw service of clients die niet SNI zijn, worden niet beïnvloed.

7. *Hoe werkt het vernieuwen van certificaten met Bring Your Own Certificate?*

    Upload uw nieuwe certificaat naar Azure-sleutel kluis om ervoor te zorgen dat er een nieuwere certificaat wordt geïmplementeerd op de PoP-infra structuur. Kies in uw TLS-instellingen op Azure CDN de nieuwste certificaat versie en selecteer Opslaan. Azure CDN zal vervolgens uw nieuwe bijgewerkte certificaat doorgeven. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> - Het HTTPS-protocol inschakelen in uw aangepast domein
> - Een CDN-beheerd certificaat gebruiken 
> - Uw eigen certificaat gebruiken
> - Het domein valideren
> - Het HTTPS-protocol uitschakelen in uw aangepast domein.

Ga naar de volgende zelfstudie voor meer informatie over het configureren van caching op het CDN-eindpunt.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure CDN-regels voor opslaan in cache instellen](cdn-caching-rules-tutorial.md)