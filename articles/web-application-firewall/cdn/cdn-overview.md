---
title: Wat is Azure Web Application Firewall op Azure CDN?
description: Lees hoe Azure Web Application Firewall op de Azure CDN-service uw webtoepassingen beschermt tegen kwaadaardige aanvallen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: victorh
ms.openlocfilehash: cd1f21f150094094a6bf856b04a64a02963f35a8
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521710"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network-from-microsoft"></a>Azure Web Application firewall op Azure Content Delivery Network van micro soft

Azure WAF (Web Application Firewall) op Azure CDN (Content Delivery Network) van Microsoft biedt gecentraliseerde beveiliging voor uw webinhoud. WAF beschermt uw webservices tegen veelvoorkomende aanvallen en beveiligingsproblemen. De firewall houdt uw service maximaal beschikbaar voor uw gebruikers en helpt u te voldoen aan de nalevingsvereisten.

> [!IMPORTANT]
> WAF op Azure CDN van Microsoft is momenteel in openbare preview en wordt aangeboden met een preview-SLA. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben.  Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

WAF op Azure CDN is een wereldwijde en gecentraliseerde oplossing. De service is wereldwijd geïmplementeerd op locaties aan de randen van het Azure-netwerk. WAF stopt kwaadaardige aanvallen dicht bij de aanvalsbronnen, voordat ze uw oorsprong bereiken. U krijgt wereldwijde beveiliging op schaal zonder prestatieverlies. 

U kunt een WAF-beleid eenvoudig koppelen aan elk gewenst CDN-eindpunt in uw abonnement. Nieuwe regels kunnen binnen enkele minuten worden geïmplementeerd, zodat u snel kunt reageren op veranderende bedreigingspatronen.

![Azure Web Application Firewall](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>WAF-beleid en -regels

U kunt een WAF-beleid configureren en dit beleid aan een of meer CDN-eindpunten koppelen voor beveiliging. Een WAF-beleid bestaat uit twee typen beveiligingsregels:

- aangepaste regels die u kunt maken.

- beheerde regelsets; dit zijn verzamelingen door Azure beheerde, vooraf geconfigureerde regels.

Als beide typen regels aanwezig zijn, worden aangepaste regels eerst verwerkt en daarna de regels in een beheerde regelset. Een regel bestaat uit een voorwaarde voor overeenkomst, een prioriteit en een actie. Dit zijn de ondersteunde actietypen: *TOESTAAN*, *BLOKKEREN*, *LOGBOEK* en *OMLEIDEN*. U kunt een volledig aangepast beleid maken dat voldoet aan uw specifieke vereisten voor toepassingsbeveiliging door beheerde en aangepaste regels te combineren.

Regels in een beleid worden verwerkt in een prioriteitsvolgorde. Prioriteit is een uniek getal dat de volgorde van de te verwerken regels bepaalt. Een lager getal geeft een hogere prioriteit aan, en deze regels worden eerder geëvalueerd dan regels met een hogere waarde. Zodra een overeenkomst met een regel is gevonden, wordt op de aanvraag de actie toegepast die is gedefinieerd in de regel. Zodra een dergelijke overeenkomst is verwerkt, worden regels met lagere prioriteiten niet meer verwerkt.

Er kan altijd maar één WAF-beleid tegelijk zijn gekoppeld aan een webtoepassing die wordt gehost op Azure CDN. U kunt echter wel een CDN-eindpunt definiëren zonder er een WAF-beleid aan te koppelen. Als er een WAF-beleid aanwezig is, wordt dit gerepliceerd naar al onze randlocaties om over de hele wereld een consistent beveiligingsbeleid te garanderen.

## <a name="waf-modes"></a>WAF-modi

Een WAF-beleid kan in de volgende twee modi worden uitgevoerd:

- *Detectiemodus*: Wanneer u het beleid uitvoert in de detectiemodus, doet WAF niets meer dan het controleren en vastleggen van de aanvraag en de overeenkomende WAF-regel in WAF-logboeken. U kunt logboekregistratie voor diagnostische gegevens inschakelen voor CDN. Wanneer u de portal gebruikt, gaat u naar de sectie **Diagnostische gegevens**.

- *Preventiemodus*: In de preventiemodus voert WAF de opgegeven actie uit als een aanvraag overeenkomt met een regel. Als er een overeenkomst wordt gevonden, worden er geen verdere regels met een lagere prioriteit geëvalueerd. Alle overeenkomende aanvragen worden ook in de WAF-logboeken vastgelegd.

## <a name="waf-actions"></a>WAF-acties

U kunt een van de volgende acties kiezen wanneer een aanvraag overeenkomt met de voorwaarden van een regel:

- *Toestaan*: De aanvraag gaat door de WAF en wordt doorgestuurd naar de back-end. Deze aanvraag kan niet worden geblokkeerd door regels met een lagere prioriteit.
- *Blokkeren*: De aanvraag wordt geblokkeerd en WAF stuurt een reactie naar de client zonder de aanvraag door te sturen naar de back-end.
- *Logboek*:  De aanvraag wordt vastgelegd in de WAF-logboeken en WAF gaat verder met het evalueren van regels met een lagere prioriteit.
- *Omleiden*: WAF leidt de aanvraag om naar de opgegeven URI. De opgegeven URI geeft een instelling op beleidsniveau aan. Als deze actie is geconfigureerd, worden alle aanvragen die overeenkomen met de actie *Omleiden* naar die URI verzonden.

## <a name="waf-rules"></a>WAF-regels

Een WAF-beleid kan bestaan uit twee typen beveiligingsregels:

- *aangepaste regels*: regels die u zelf maakt 
- *beheerde regelsets*: Door Azure beheerde vooraf geconfigureerde set regels

### <a name="custom-rules"></a>Aangepaste regels

Aangepaste regels kunnen vergelijkingsregels en regels voor volumebeheer bevatten.

U kunt de volgende aangepaste vergelijkingsregels configureren:

- *IP-acceptatielijst en IP-blokkeringslijst*: U kunt de toegang tot uw webtoepassingen beheren op basis van een lijst met IP-adressen of IP-adresbereiken van clients. Zowel IPv4- als IPv6-adrestypen worden ondersteund. Deze lijst kan worden geconfigureerd om aanvragen te blokkeren of toe te staan waarbij de bron-IP overeenkomt met een IP-adres in de lijst.

- *Toegangsbeheer op basis van geografische regio*: U kunt de toegang tot uw webtoepassingen beheren op basis van de landcode die is gekoppeld aan het IP-adres van een client.

- *Toegangsbeheer op basis van HTTP-parameters*: U kunt regels baseren op tekenreeksovereenkomsten in parameters voor HTTP/HTTPS-aanvragen, zoals  queryreeksen, POST-argumenten, aanvraag-URI, aanvraagheader en hoofdtekst van aanvraag.

- *Toegangsbeheer op basis van aanvraagmethode*: U kunt regels baseren op de HTTP-aanvraagmethode van de aanvraag, zoals GET, PUT of HEAD.

- *Groottebeperking*: U kunt regels baseren op de lengte van specifieke delen van een aanvraag, zoals queryreeks, URI of aanvraagtekst.

Een regel voor volumebeperking voorkomt abnormaal hoog verkeer van een client-IP-adres.

- *Regels voor volumebeperking*: U kunt bijvoorbeeld een drempel configureren voor het aantal webaanvragen dat is toegestaan vanaf een client-IP-adres gedurende één minuut. Deze regel is verschillend van een regel voor toestaan/blokkeren op basis van een IP-lijst, waarmee alle aanvragen van een client-IP-adres worden toegestaan of geblokkeerd. Volumelimieten kunnen worden gecombineerd met aanvullende voorwaarden voor overeenkomsten, zoals HTTP(S)-parameterovereenkomsten voor nauwkeurige volumeregeling.

### <a name="azure-managed-rule-sets"></a>Door Azure beheerde regelsets

Door Azure beheerde regelsets bieden een eenvoudige manier om beveiliging te implementeren op basis van een gemeenschappelijke set beveiligingsbedreigingen. Aangezien deze regelsets worden beheerd door Azure, worden de regels zo nodig bijgewerkt om ook bescherming te bieden tegen nieuwe aanvalshandtekeningen. De door Azure beheerde standaardregelset bevat regels voor de volgende bedreigingscategorieën:

- Cross-site scripting
- Java-aanvallen
- Opnemen van lokaal bestand
- PHP-injectieaanvallen
- Uitvoeren van opdrachten op afstand
- Opnemen van extern bestand
- Sessievastlegging
- Beveiliging tegen SQL-injecties
- Protocolaanvallen

Het versienummer van de standaardregelset wordt verhoogd wanneer er nieuwe aanvalshandtekeningen worden toegevoegd aan de regelset.
De standaardregelset is standaard ingeschakeld in de *detectie* modus in uw WAF-beleid. U kunt afzonderlijke regels in de standaardregelset uit- of inschakelen om te voldoen aan de vereisten van uw toepassing. U kunt ook specifieke acties (TOESTAAN, BLOKKEREN, LOGBOEK en OMLEIDEN) instellen per regel. De standaardactie voor de beheerde standaardregelset is *blokkeren*.

Aangepaste regels worden altijd toegepast voordat regels in de standaardregelset worden geëvalueerd. Als een aanvraag overeenkomt met een aangepaste regel, wordt de bijbehorende regelactie toegepast. De aanvraag wordt geblokkeerd of doorgegeven aan de back-end. Er worden geen andere aangepaste regels verwerkt, evenmin als de regels in de standaardregelset. U kunt ook de standaardregelset uit uw WAF-beleid verwijderen.

## <a name="configuration"></a>Configuratie

U kunt alle WAF-regeltypen configureren en implementeren met behulp van de Azure-portal, REST-API's, Azure Resource Manager-sjablonen en Azure PowerShell.

## <a name="monitoring"></a>Bewaking

Bewaking voor WAF met CDN is geïntegreerd met Azure Monitor om waarschuwingen bij te houden en gemakkelijk trends in verkeer te ontdekken.

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: WAF-beleid maken met Azure CDN met behulp van de Azure-portal](waf-cdn-create-portal.md)
