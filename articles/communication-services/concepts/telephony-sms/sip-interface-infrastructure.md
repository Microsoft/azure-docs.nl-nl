---
title: Vereisten voor de infra structuur van de SIP-interface-Azure Communication Services
description: Raadpleeg de vereisten voor de infra structuur voor de configuratie van de SIP-interface van Azure Communication Services
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 02/09/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a94aa0a0deea14cca2b558c602ff7e35ca0ba81f
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102487374"
---
# <a name="sip-interface-infrastructure-requirements"></a>Vereisten voor de infra structuur van de SIP-interface 

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

 
In dit artikel worden de connectiviteits gegevens van de SIP-interface (infra structuur, licenties en SBC) beschreven waarmee u rekening moet houden bij het plannen van de implementatie van de bewerkings-host.


## <a name="infrastructure-requirements"></a>Vereisten voor de infrastructuur
De vereisten voor de infra structuur voor de ondersteunde SBCs-, domein-en andere netwerk connectiviteits vereisten voor de implementatie van de SIP-interface worden weer gegeven in de volgende tabel:  

|Infrastructuur vereiste|U hebt het volgende nodig|
|:--- |:--- |
|Sessie rand controller (SBC)|Een ondersteunde SBC. Zie [ondersteunde SBCs](#supported-session-border-controllers-sbcs)voor meer informatie.|
|Telefonie-Trunks die zijn verbonden met de SBC|Een of meer telefoon schachten die zijn verbonden met de SBC. Aan de ene kant maakt de SBC verbinding met de Azure communication service via de SIP-interface. De SBC kan ook verbinding maken met Telephony-entiteiten van derden, zoals PBXs, analoge telefoon adapters, enzovoort. Een PSTN-connectiviteits optie die is verbonden met de SBC, werkt. (Voor de configuratie van de PSTN-trunks naar de SBC, verwijzen wij u naar de SBC-leveranciers of-trunk-providers.)|
|Azure-abonnement|Een Azure-abonnement dat u gebruikt voor het maken van een ACS-resource en de configuratie en verbinding met de SBC.|
|Toegangs token voor communicatie Services|Als u aanroepen wilt maken, hebt u een geldig toegangs token met de `voip` Scope nodig. [Toegangs tokens](../identity-model.md#access-tokens) bekijken|
|Openbaar IP-adres voor de SBC|Een openbaar IP-adres dat kan worden gebruikt om verbinding te maken met de SBC. Op basis van het type SBC kan de SBC NAT gebruiken.|
|FQDN-naam (Fully Qualified Domain Name) voor de SBC|Een FQDN voor de SBC, waarbij het domein gedeelte van de FQDN niet overeenkomt met geregistreerde domeinen in uw Microsoft 365 of Office 365-organisatie. Zie voor meer informatie [SBC-domein namen](#sbc-domain-names).|
|Open bare DNS-vermelding voor de SBC |Een open bare DNS-vermelding die de SBC-FQDN aan het open bare IP-adres toewijst. |
|Openbaar vertrouwd certificaat voor de SBC |Een certificaat voor de SBC dat moet worden gebruikt voor alle communicatie met de SIP-interface. Zie [openbaar vertrouwd certificaat voor de SBC](#public-trusted-certificate-for-the-sbc)voor meer informatie.|
|IP-adressen en poorten van de firewall voor SIP-Signa lering en-media |De SBC communiceert met de volgende services in de Cloud:<br/><br/>SIP-proxy, die de Signa lering afhandelt<br/>Media processor, waarmee media worden verwerkt<br/><br/>Deze twee services hebben afzonderlijke IP-adressen in Microsoft Cloud, die verderop in dit document worden beschreven.


## <a name="sbc-domain-names"></a>SBC-domein namen

Klanten zonder Office 365 kunnen elke domein naam gebruiken waarvoor ze een openbaar certificaat kunnen verkrijgen.

De volgende tabel bevat voor beelden van DNS-namen die zijn geregistreerd voor de Tenant, of de naam kan worden gebruikt als een Fully Qualified Domain Name (FQDN) voor de SBC en voor beelden van geldige FQDN-namen:

|DNS-naam|Kan worden gebruikt voor SBC FQDN|Voor beelden van FQDN-namen|
|:--- |:--- |:--- |
contoso.com|Ja|**Geldige namen:**<br/>sbc1.contoso.com<br/>ssbcs15.contoso.com<br/>europe.contoso.com|
|contoso.onmicrosoft.com|Nee|Het gebruik van *. onmicrosoft.com-domeinen wordt niet ondersteund voor SBC-namen

Als u een Office 365-klant bent, mag de SBC-domein naam niet overeenkomen met de geregistreerde domeinen van de Office 365-Tenant. Hieronder ziet u een voor beeld van een samen werking tussen Office 365 en Azure communication service:

|Domein geregistreerd in Office 365|Voor beelden van SBC FQDN in teams|Voor beelden van SBC FQDN-namen in ACS|
|:--- |:--- |:--- |
**contoso.com** (domein op tweede niveau)|**SBC.contoso.com** (naam in het domein van het tweede niveau)|**SBC.ACS.contoso.com** (naam in het domein van het derde niveau)<br/>**SBC.fabrikam.com** (elke naam binnen een ander domein)|
|**o365.contoso.com** (domein van het derde niveau)|**SBC.o365.contoso.com** (naam in het domein van het derde niveau)|**SBC.contoso.com** (naam in het domein van het tweede niveau)<br/>**SBC.ACS.o365.contoso.com** (naam in het domein van het vierde niveau)<br/>**SBC.fabrikam.com** (elke naam binnen een ander domein)

SBC-koppeling werkt op het niveau van de communicatie Services, wat betekent dat u veel SBCs kunt koppelen aan één communicatie Services-resource, maar dat u een enkele SBC niet kunt koppelen aan meer dan één communicatie Services-resource. Er zijn unieke SBC-FQDN-naam is vereist voor het koppelen van verschillende bronnen.

## <a name="public-trusted-certificate-for-the-sbc"></a>Openbaar vertrouwd certificaat voor de SBC

Micro soft raadt u aan om het certificaat voor de SBC aan te vragen door een aanvraag voor certificerings handtekeningen (CSR) te genereren. Voor specifieke instructies over het genereren van een CSR voor een SBC raadpleegt u de Interconnect-instructies of documentatie van uw SBC-leveranciers. 

  > [!NOTE]
  > Voor de meeste certificerings instanties (Ca's) moet de grootte van de persoonlijke sleutel ten minste 2048 zijn. Houd dit in acht wanneer u de CSR genereert.

Het certificaat moet de SBC-FQDN hebben als algemene naam (CN) of het veld alternatieve naam voor onderwerp (SAN). Het certificaat moet rechtstreeks worden uitgegeven door een certificerings instantie, niet van een tussen provider.

De SIP-interface van communicatie Services ondersteunt ook een Joker teken in de CN en/of SAN en het Joker teken moet voldoen aan Standard [RFC http via TLS](https://tools.ietf.org/html/rfc2818#section-3.1). 

Een voor beeld wordt gebruikt `\*.contoso.com` dat overeenkomt met de SBC FQDN `sbc.contoso.com` , maar niet overeenkomt met `sbc.test.contoso.com` .

Het certificaat moet worden gegenereerd door een van de volgende basis certificerings instanties:

- AffirmTrust
- AddTrust externe CA-basis
- Baltimore Cyber Trust Root *
- Buypass
- Cyber Trust
- Klasse 3 open bare primaire certificerings instantie
- Comodo beveiligde basis-CA
- Deutsche Telekom 
- DigiCert Global Root CA
- DigiCert High Assurance EV-basis-CA
- Belast
- GlobalSign
- Go Daddy
- GeoTrust
- VeriSign, Inc. 
- SSL.com
- Starfield
- Symantec Enter prise Mobile-hoofdmap voor micro soft 
- SwissSign
- CA voor Thawte-tijds tempel
- Trustwave
- TeliaSonera 
- T-Systems International GmbH (Deutsche Telekom)
- QuoVadis

Micro soft werkt aan het toevoegen van extra certificerings instanties op basis van aanvragen van klanten. 

## <a name="sip-signaling-fqdns"></a>SIP-Signa lering: FQDN ' ' 

De verbindings punten voor de SIP-interface van communicatie Services zijn de volgende drie FQDN-naam:

- **SIP.pstnhub.Microsoft.com** – globale FQDN – moet eerst worden uitgevoerd. Wanneer de SBC een aanvraag verzendt om deze naam om te zetten, retour neren de Microsoft Azure DNS-servers een IP-adres dat verwijst naar het primaire Azure-Data Center dat aan de SBC is toegewezen. De toewijzing is gebaseerd op de prestatie gegevens van de data centers en geografische nabijheid van het SBC. Het IP-adres dat wordt geretourneerd, komt overeen met de primaire FQDN.
- **Sip2.pstnhub.Microsoft.com** : secundaire FQDN: wordt geografisch toegewezen aan de tweede prioriteits regio.
- **sip3.pstnhub.Microsoft.com** – tertiaire FQDN – geografisch wijst toe aan de derde prioriteits regio.

Het plaatsen van deze drie FQDN in de juiste volg orde is vereist voor:

- Optimale ervaring bieden (minder geladen en dichtst bij het SBC-Data Center dat is toegewezen door query's uit te voeren op de eerste FQDN).
- Geef een failover op wanneer er verbinding wordt gemaakt met een Data Center dat een tijdelijk probleem ondervindt. Zie [failover-mechanisme](#failover-mechanism-for-sip-signaling) hieronder voor meer informatie.  

De FQDN-namen – sip.pstnhub.microsoft.com, sip2.pstnhub.microsoft.com en sip3.pstnhub.microsoft.com – worden omgezet in een van de volgende IP-adressen:

- `52.114.148.0`
- `52.114.132.46`
- `52.114.75.24` 
- `52.114.76.76` 
- `52.114.7.24` 
- `52.114.14.70`
- `52.114.16.74`
- `52.114.20.29`

Open firewall poorten voor deze IP-adressen om binnenkomend en uitgaand verkeer naar en van de adressen voor Signa lering toe te staan. Als uw firewall DNS-namen ondersteunt, wordt de FQDN-naam `sip-all.pstnhub.microsoft.com` omgezet in al deze IP-adressen. 

## <a name="sip-signaling-ports"></a>SIP-Signa lering: poorten

Gebruik de volgende poorten voor de SIP-interface van communicatie Services:

|Verkeer|Van|Tot|Bronpoort|Doelpoort|
|:--- |:--- |:--- |:--- |:--- |
|SIP/TLS|SIP-proxy|SBC|1024 – 65535|Gedefinieerd op de SBC (alleen voor Office 365 GCC High/DoD-poort 5061 moet worden gebruikt)|
SIP/TLS|SBC|SIP-proxy|Gedefinieerd op de SBC|5061|

### <a name="failover-mechanism-for-sip-signaling"></a>Failover-mechanisme voor SIP-Signa lering

Met SBC wordt een DNS-query uitgevoerd om sip.pstnhub.microsoft.com op te lossen. Op basis van de SBC-locatie en de metrische gegevens voor de prestaties van data centers is het primaire Data Center geselecteerd. Als het primaire Data Center een probleem ondervindt, probeert de SBC de sip2.pstnhub.microsoft.com, die wordt omgezet in het tweede toegewezen Data Center en, in zeldzame gevallen dat data centers in twee regio's niet beschikbaar zijn, de SBC nieuwe poging tot de laatste FQDN (sip3.pstnhub.microsoft.com), die het tertiaire datacenter-IP verschaft.

## <a name="media-traffic-ip-and-port-ranges"></a>Media verkeer: IP-en poortbereiken

Het media verkeer loopt van en naar een afzonderlijke service met de naam media processor. Op het moment van de publicatie van de media processor voor ACS kan elk IP-adres van Azure worden gebruikt. Down load [de volledige lijst met adressen](https://www.microsoft.com/download/details.aspx?id=56519).

### <a name="port-range"></a>Poortbereik
Het poort bereik van de media processors wordt weer gegeven in de volgende tabel: 

|Verkeer|Van|Tot|Bronpoort|Doelpoort|
|:--- |:--- |:--- |:--- |:--- |
|UDP-SRTP|Media processor|SBC|3478-3481 en 49152 – 53247|Gedefinieerd op de SBC|
|UDP-SRTP|SBC|Media processor|Gedefinieerd op de SBC|3478-3481 en 49152 – 53247|

  > [!NOTE]
  > Micro soft raadt ten minste twee poorten per gelijktijdige aanroep op de SBC aan.


## <a name="media-traffic-media-processors-geography"></a>Media verkeer: Geografie van media processors

Het media verkeer loopt via onderdelen die media processors worden genoemd. Media processors worden in dezelfde data centers geplaatst als SIP-proxy's. Er zijn ook extra media processors om media stroom te optimaliseren. Het is bijvoorbeeld niet mogelijk een SIP-proxy onderdeel nu in Australië (SIP-stromen via Singapore of Hongkong SAR), maar we hebben de media processor lokaal in Australië. De nood zaak van de media processors lokaal wordt bepaald door de latentie die wij ervaren door het verzenden van interlokale verkeer, bijvoorbeeld Australië naar Singapore of Hong Kong SAR. Hoewel latentie in het voor beeld van verkeer van Australië naar Hong Kong SAR of Singapore aanvaardbaar is om goede gesprek kwaliteit voor SIP-verkeer te behouden, is dit niet het geval bij Real-time media verkeer.

Locaties waar zowel de SIP-proxy als de media processor onderdelen zijn geïmplementeerd:
- VS (twee in VS West en VS Oost-data centers)
- Europa (Amsterdam en Dublin data centers)
- Azië (Singapore en Hong Kong SAR-data centers)
- Australië (AU-oost en Zuidoost-data centers)

Locaties waar alleen media processors worden geïmplementeerd (SIP-stromen via het dichtstbijzijnde Data Center dat hierboven wordt vermeld):
- Japan (JP-Oost en West-data centers)


## <a name="media-traffic-codecs"></a>Media verkeer: codecs

### <a name="leg-between-sbc-and-cloud-media-processor-or-microsoft-teams-client"></a>Leg tussen de SBC-en Cloud media-processor of de micro soft teams-client.
Is van toepassing op zowel niet-overbypass-als niet-bypass-cases.

De direct routerings interface op het been tussen de sessie rand controller en de Cloud media processor kan gebruikmaken van de volgende codecs:

- ZIJDE, G. 711, G. 722, G. 729

U kunt het gebruik van de specifieke codec op de sessie rand controller afdwingen door ongewenste codecs uit de aanbieding uit te sluiten.

### <a name="leg-between-acs-sdk-app-and-cloud-media-processor"></a>Leg tussen de ACS SDK-app en de Cloud media processor

De arm tussen de Cloud media processor en ACS SDK-app van zijde of G. 722 wordt gebruikt. De codec-keuze in dit gedeelte is gebaseerd op micro soft-algoritmen, waarbij rekening wordt gehouden met meerdere para meters. 

## <a name="supported-session-border-controllers-sbcs"></a>Ondersteunde sessie rand controllers (SBCs)

De certificering wordt uitgevoerd. Ondertussen kunnen klanten teams van [gecertificeerde sessie-controllers](/MicrosoftTeams/direct-routing-border-controllers)gebruiken. 

## <a name="next-steps"></a>Volgende stappen

### <a name="conceptual-documentation"></a>Conceptuele documentatie

- [Telefonie concept](./telephony-concept.md)
- [Typen telefoonnummers in Azure Communication Services](./plan-solution.md)
- [Prijzen](../pricing.md)

### <a name="quickstarts"></a>Quickstarts

- [Bellen naar telefoon](../../quickstarts/voice-video-calling/pstn-call.md)