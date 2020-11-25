---
title: Azure CDN-assets beveiligen met verificatie op basis van tokens | Microsoft Docs
description: Meer informatie over het gebruik van token verificatie om de toegang tot uw Azure CDN-assets te beveiligen.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mazha
ms.openlocfilehash: 21ef06f37e6840df08b1477f9c0ff24f6e15d1a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95978031"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Azure CDN-assets beveiligen met token verificatie

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Overzicht

Token verificatie is een mechanisme waarmee u kunt voor komen dat de Azure-Content Delivery Network (CDN) assets levert aan niet-geautoriseerde clients. Token verificatie wordt doorgaans uitgevoerd om *hotlinking* te voor komen, waarbij een andere website, zoals een berichten bord, uw assets zonder toestemming gebruikt. Hotlinking kan invloed hebben op de leverings kosten van uw inhoud. Door token verificatie in CDN in te scha kelen, worden aanvragen door de CDN Edge-server geverifieerd voordat de CDN de inhoud levert. 

## <a name="how-it-works"></a>Uitleg

Token verificatie verifieert of aanvragen worden gegenereerd door een vertrouwde site door aanvragen te vereisen die een token waarde bevatten die gecodeerde informatie over de aanvrager bevat. Inhoud wordt alleen aan een aanvrager geleverd als de gecodeerde gegevens voldoen aan de vereisten. anders worden aanvragen geweigerd. U kunt de vereisten instellen met behulp van een of meer van de volgende para meters:

- Land/regio: aanvragen die afkomstig zijn van de landen/regio's die zijn opgegeven door hun [land/regio code](/previous-versions/azure/mt761717(v=azure.100))toestaan of weigeren.
- URL: alleen aanvragen toestaan die overeenkomen met het opgegeven item of pad.
- Host: aanvragen die gebruikmaken van de opgegeven hosts in de aanvraag header toestaan of weigeren.
- Verwijzende site: de aanvraag toestaan of weigeren van de opgegeven verwijzings site.
- IP-adres: alleen aanvragen toestaan die afkomstig zijn van een specifiek IP-adres of IP-subnet.
- Protocol: aanvragen toestaan of weigeren op basis van het protocol dat wordt gebruikt om de inhoud aan te vragen.
- Verloop tijd: wijs een datum en tijd toe om ervoor te zorgen dat een koppeling alleen gedurende een beperkte periode geldig blijft.

Zie de gedetailleerde configuratie voorbeelden voor elke para meter bij het instellen van [token verificatie](#setting-up-token-authentication)voor meer informatie.

>[!IMPORTANT] 
> Als token autorisatie is ingeschakeld voor een pad op dit account, is de modus standaard-cache de enige modus die kan worden gebruikt voor het opslaan van de query in de teken reeks cache. Zie [Cachegedrag in Azure CDN bepalen met queryreeksen](cdn-query-string-premium.md) voor meer informatie.

## <a name="reference-architecture"></a>Referentiearchitectuur

In het volgende werk stroom diagram wordt beschreven hoe het CDN token verificatie gebruikt om te werken met uw web-app.

![Verificatie werk stroom voor CDN-token](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Token validatie logica op het CDN-eind punt
    
Het volgende stroom diagram beschrijft hoe Azure CDN een client aanvraag valideert wanneer token verificatie is geconfigureerd op het CDN-eind punt.

![Validatie logica van CDN-token](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Token verificatie instellen

1. Blader vanuit het [Azure Portal](https://portal.azure.com)naar uw CDN-profiel en selecteer vervolgens **beheren** om de aanvullende portal te starten.

    ![Beheer knop voor CDN-profiel](./media/cdn-token-auth/cdn-manage-btn.png)

2. Beweeg de muis aanwijzer over **http groot** en selecteer vervolgens **token auth** in de flyout. U kunt de versleutelings sleutel en versleutelings parameters vervolgens als volgt instellen:

   1. Maak een of meer versleutelings sleutels. Een versleutelings sleutel is hoofdletter gevoelig en kan een wille keurige combi natie van alfanumerieke tekens bevatten. Andere typen tekens, inclusief spaties, zijn niet toegestaan. De maximale lengte is 250 tekens. Om ervoor te zorgen dat uw versleutelings sleutels wille keurig zijn, is het raadzaam deze te maken met behulp van het [openssl-hulp programma](https://www.openssl.org/). 

      Het hulp programma OpenSSL heeft de volgende syntaxis:

      ```rand -hex <key length>```

      Bijvoorbeeld:

      ```OpenSSL> rand -hex 32``` 

      Om uitval tijd te voor komen, moet u een primaire en een back-upsleutel maken. Een back-upsleutel biedt ononderbroken toegang tot uw inhoud wanneer uw primaire sleutel wordt bijgewerkt.
    
   2. Voer een unieke versleutelings sleutel in het vak **primaire sleutel** in en voer eventueel een back-upsleutel in het vak **back-upsleutel** in.

   3. Selecteer de minimale versleutelings versie voor elke sleutel in de lijst **minimale encryptie versie** en selecteer vervolgens **Update**:
      - **V2**: geeft aan dat de sleutel kan worden gebruikt voor het genereren van versie 2,0-en 3,0-tokens. Gebruik deze optie alleen als u overstapt van een verouderde versie 2,0-versleutelings sleutel naar een versie 3,0-sleutel.
      - **V3**: (aanbevolen) geeft aan dat de sleutel alleen kan worden gebruikt voor het genereren van versie 3,0-tokens.

      ![Configuratie sleutel voor de CDN-token verificatie](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Gebruik het hulp programma voor versleuteling om versleutelings parameters in te stellen en een token te genereren. Met het versleutelings programma kunt u aanvragen toestaan of weigeren op basis van de verval tijd, het land/de regio, de verwijzing, het protocol en het IP-adres van de client (in een wille keurige combi natie). Hoewel er geen limiet is voor het aantal en de combi natie van para meters die kunnen worden gecombineerd om een token te vormen, is de totale lengte van een token beperkt tot 512 tekens. 

      ![Hulp programma voor CDN-versleuteling](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Voer waarden in voor een of meer van de volgende versleutelings parameters in het gedeelte **versleutelings Programma's** : 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Parameternaam</th> 
      >   <th>Beschrijving</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Wijst een verloop tijd toe aan een token, waarna het token verloopt. Aanvragen die zijn verzonden na de verloop tijd, worden geweigerd. Deze para meter maakt gebruik van een UNIX-tijds tempel dat is gebaseerd op het aantal seconden sinds de standaard-UNIX-epoche van `1/1/1970 00:00:00 GMT` . (U kunt online-hulpprogram ma's gebruiken om te converteren tussen de standaard tijd en de Unix-tijd.)> 
      >    Als u bijvoorbeeld wilt dat het token verloopt op `12/31/2016 12:00:00 GMT` , voert u de Unix-Time Stamp waarde in `1483185600` . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Met kunt u tokens aanpassen aan een bepaald activum of pad. Hiermee beperkt u de toegang tot aanvragen waarvan de URL met een specifiek relatief pad begint. Url's zijn hoofdletter gevoelig. Voer meerdere paden in door elk pad met een komma te scheiden. Voeg geen spaties toe. Afhankelijk van uw vereisten kunt u verschillende waarden instellen om een ander toegangs niveau te bieden.> 
      >    Voor de URL `http://www.mydomain.com/pictures/city/strasbourg.png` zijn bijvoorbeeld deze aanvragen toegestaan voor de volgende invoer waarden: 
      >    <ul>
      >       <li>Invoer waarde `/` : alle aanvragen zijn toegestaan.</li>
      >       <li>Invoer waarde `/pictures` , de volgende aanvragen zijn toegestaan: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Invoer waarde `/pictures/` : alleen aanvragen met het `/pictures/` pad zijn toegestaan. Bijvoorbeeld `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Invoer waarde `/pictures/city/strasbourg.png` : alleen aanvragen voor dit specifieke pad en activum zijn toegestaan.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Alleen aanvragen die afkomstig zijn van een of meer opgegeven landen/regio's, worden toegestaan. Aanvragen die afkomstig zijn van alle andere landen/regio's, worden geweigerd. Gebruik een [ISO 3166-land/regio code](/previous-versions/azure/mt761717(v=azure.100)) van twee letters voor elk land/regio en scheid deze met een komma. Voeg geen spatie toe. Als u bijvoorbeeld alleen toegang wilt toestaan vanaf de Verenigde Staten en Frank rijk, voert u in `US,FR` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Weigert aanvragen die afkomstig zijn uit een of meer opgegeven landen/regio's. Aanvragen die afkomstig zijn uit alle andere landen/regio's zijn toegestaan. De implementatie is hetzelfde als de para meter <b>ec_country_allow</b> . Als een land-/regionummer aanwezig is in zowel de para meter <b>ec_country_allow</b> als <b>ec_country_deny</b> , heeft de para meter <b>ec_country_allow</b> prioriteit.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Alleen aanvragen van de opgegeven verwijzings toestaan. Een verwijzende site identificeert de URL van de webpagina die is gekoppeld aan de bron die wordt aangevraagd. Neem het protocol niet op in de parameter waarde.> 
      >    De volgende typen invoer zijn toegestaan:
      >    <ul>
      >       <li>Een hostnaam of hostnaam en een pad.</li>
      >       <li>Meerdere verwijzende sites. Als u meerdere verwijzende sites wilt toevoegen, scheidt u elke verwijzing naar een komma. Voeg geen spatie toe. Als u een verwijzende waarde opgeeft, maar de verwijzende gegevens worden niet in de aanvraag verzonden door de browser configuratie, wordt de aanvraag standaard geweigerd.</li> 
      >       <li>Aanvragen met ontbrekende of lege verwijzende informatie. Standaard worden deze typen aanvragen geblokkeerd door de para meter <b>ec_ref_allow</b> . Als u deze aanvragen wilt toestaan, typt u de tekst ' ontbreekt ' of voert u een lege waarde in (met behulp van een afsluitende komma).</li> 
      >       <li>Subdomeinen. Als u subdomeinen wilt toestaan, voert u een asterisk ( \* ) in. Als u bijvoorbeeld alle subdomeinen van wilt toestaan `contoso.com` , voert u in `*.contoso.com` .</li>
      >    </ul> 
      >    Als u bijvoorbeeld toegang wilt verlenen voor aanvragen van `www.contoso.com` , voert u alle subdomeinen onder `contoso2.com` en aanvragen met lege of ontbrekende verwijzende sites in `www.contoso.com,*.contoso.com,missing` .</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Hiermee worden aanvragen van de opgegeven verwijzings bronnen geweigerd. De implementatie is hetzelfde als de para meter <b>ec_ref_allow</b> . Als een verwijzende site aanwezig is in zowel de para meter <b>ec_ref_allow</b> als <b>ec_ref_deny</b> , heeft de para meter <b>ec_ref_allow</b> prioriteit.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Alleen aanvragen van het opgegeven protocol zijn toegestaan. Geldige waarden zijn `http` , `https` of `http,https` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Hiermee worden aanvragen van het opgegeven protocol geweigerd. De implementatie is hetzelfde als de para meter <b>ec_proto_allow</b> . Als er in de para meters <b>ec_proto_allow</b> en <b>ec_proto_deny</b> een protocol aanwezig is, heeft de para meter <b>ec_proto_allow</b> prioriteit.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Hiermee beperkt u de toegang tot het opgegeven IP-adres van de aanvrager. Zowel IPV4 als IPV6 worden ondersteund. U kunt één IP-adres of IP-adressen voor aanvragen opgeven die zijn gekoppeld aan een specifiek subnet. `11.22.33.0/22`Hiermee kunnen bijvoorbeeld aanvragen van IP-adressen 11.22.32.1 naar 11.22.35.254 worden toegestaan.</td>
      > </tr>
      > </table>

   5. Wanneer u klaar bent met het invoeren van versleutelings parameter waarden, selecteert u een sleutel die u wilt versleutelen (als u een primaire en een back-upsleutel hebt gemaakt) in de lijst **sleutel tot versleuteling** .
    
   6. Selecteer een versleutelings versie in de lijst met **versleutelings versies** : **v2** voor versie 2 of **v3** voor versie 3 (aanbevolen). 

   7. Selecteer **versleutelen** om het token te genereren.

      Nadat het token is gegenereerd, wordt het weer gegeven in het vak **gegenereerde token** . Als u het token wilt gebruiken, voegt u dit toe als een query reeks aan het einde van het bestand in het URL-pad. Bijvoorbeeld `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Test eventueel uw token met het hulp programma voor ontsleutelen zodat u de para meters van uw token kunt weer geven. Plak de token waarde in het vak **te ontsleutelen token** . Selecteer de versleutelings sleutel die u wilt gebruiken in de lijst met te ontsleutelen **sleutel** en selecteer vervolgens **ontsleutelen**.

      Nadat het token is ontsleuteld, worden de bijbehorende para meters weer gegeven in het vak **oorspronkelijke para meters** .

   9. Desgewenst kunt u het type antwoord code aanpassen dat wordt geretourneerd wanneer een aanvraag wordt geweigerd. Selecteer **ingeschakeld** en selecteer vervolgens de antwoord code in de lijst met **antwoord codes** . De naam van de **header** wordt automatisch ingesteld op de **locatie**. Selecteer **Opslaan** om de nieuwe respons code te implementeren. Voor bepaalde antwoord codes moet u ook de URL van uw fout pagina in het vak **header waarde** invoeren. De respons code **403** (verboden) is standaard geselecteerd. 

3. Onder **http large** selecteert u **regel engine**. U gebruikt de regel Engine om paden te definiëren om de functie toe te passen, de functie voor token verificatie in te scha kelen en aanvullende mogelijkheden voor token authenticatie in te scha kelen. Zie voor meer informatie [regel engine verwijzing](./cdn-verizon-premium-rules-engine-reference.md).

   1. Selecteer een bestaande regel of maak een nieuwe regel om het activum of pad te definiëren waarvoor u token verificatie wilt Toep assen. 
   2. Als u verificatie op basis van tokens voor een regel wilt inschakelen, selecteert u **[token auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm)** in de lijst **onderdelen** en selecteert u **ingeschakeld**. Selecteer **bijwerken** als u een regel bijwerkt of **toevoegen** als u een regel maakt.
        
      ![Voor beeld van token verificatie van de CDN-regels](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. In de regel engine kunt u ook aanvullende functies met betrekking tot token authenticatie inschakelen. Als u een van de volgende functies wilt inschakelen, selecteert u deze in de lijst met **onderdelen** en selecteert u **ingeschakeld**.
    
   - **[Weigerings code voor token auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm)**: bepaalt het type reactie dat wordt geretourneerd naar een gebruiker wanneer een aanvraag wordt geweigerd. De regels die hier zijn ingesteld, overschrijven de antwoord code die is ingesteld in het gedeelte **aangepaste afhandeling** van de op tokens gebaseerde verificatie pagina.

   - **[Token verificatie negeren URL-Case](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm)**: Hiermee wordt bepaald of de URL die wordt gebruikt om het token te valideren, hoofdletter gevoelig is.

   - **[Token auth para meter](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm)**: de naam van de query teken reeks parameter token verificatie die wordt weer gegeven in de aangevraagde URL. 
        
     ![Voor beeld van verificatie-instellingen voor het engine voor CDN-regels](./media/cdn-token-auth/cdn-rules-engine2.png)

5. U kunt uw token aanpassen door bron code te openen in [github](https://github.com/VerizonDigital/ectoken).
   Beschik bare talen zijn:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Prijzen van Azure CDN-functies en-providers

Zie [Azure CDN product functies](cdn-features.md)voor meer informatie over functies. Zie [Content Delivery Network prijzen](https://azure.microsoft.com/pricing/details/cdn/)voor meer informatie over prijzen.