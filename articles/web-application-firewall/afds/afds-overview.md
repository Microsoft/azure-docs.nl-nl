---
title: Wat is Azure Web Application Firewall voor Azure Front Door?
description: Lees hoe Azure Web Application Firewall voor de service Azure Front Door uw webtoepassingen beschermt tegen kwaadaardige aanvallen.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 05/22/2020
ms.author: victorh
ms.openlocfilehash: 0e3073c491b251a1dbb505d4656de9c6a1e0ac7f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048490"
---
# <a name="azure-web-application-firewall-on-azure-front-door"></a>Web Application Firewall voor Azure Front Door

Azure Web Application Firewall (WAF) voor Azure Front Door biedt gecentraliseerde beveiliging voor uw web-apps. WAF beschermt uw webservices tegen veelvoorkomende aanvallen en beveiligingsproblemen. De firewall houdt uw service maximaal beschikbaar voor uw gebruikers en helpt u te voldoen aan de nalevingsvereisten.

WAF voor Front Door is een wereldwijde en gecentraliseerde oplossing. De service is wereldwijd geïmplementeerd op locaties aan de randen van het Azure-netwerk. Webtoepassingen waarvoor WAF is ingeschakeld, controleren elke inkomende aanvraag die door Front Door wordt afgeleverd aan de rand van het netwerk. 

WAF voorkomt kwaadaardige aanvallen dicht bij de aanvalsbronnen, voordat ze uw virtuele netwerk binnenkomen. U krijgt wereldwijde beveiliging op schaal zonder prestatieverlies. U kunt eenvoudig een WAF-beleid koppelen aan een Front Door-profiel in uw abonnement. Nieuwe regels kunnen binnen enkele minuten worden geïmplementeerd, zodat u snel kunt reageren op veranderende bedreigingspatronen.

![Azure Web Application Firewall](../media/overview/wafoverview.png)

Azure front-deur introduceert [twee nieuwe sku's in Preview](../../frontdoor/standard-premium/overview.md): voor deur standaard en voor-deur Premium-SKU. WAF is systeem eigen geïntegreerd met de voor deur Premium-SKU met volledige mogelijkheden. Voor de standaard-SKU voor de voor deur worden alleen [aangepaste regels](#custom-authored-rules) ondersteund.

## <a name="waf-policy-and-rules"></a>WAF-beleid en -regels

U kunt een [WAF-beleid](waf-front-door-create-portal.md) configureren en dat beleid ter beveiliging aan een of meer front-ends van Front Door koppelen. Een WAF-beleid bestaat uit twee typen beveiligingsregels:

- aangepaste regels die door de klant zijn gemaakt

- beheerde regelsets; een verzameling door Azure beheerde, vooraf geconfigureerde set regels

Als beide typen regels aanwezig zijn, worden aangepaste regels eerst verwerkt en daarna de regels in een beheerde regelset. Een regel bestaat uit een voorwaarde voor overeenkomst, een prioriteit en een actie. Dit zijn de ondersteunde actietypen: TOESTAAN, BLOKKEREN, LOGBOEK en OMLEIDEN. U kunt een volledig aangepast beleid maken dat voldoet aan uw specifieke vereisten voor toepassingsbeveiliging door beheerde en aangepaste regels te combineren.

Regels in een beleid worden verwerkt in een prioriteitsvolgorde. Prioriteit is een uniek geheel getal dat de volgorde bepaalt van de te verwerken regels. Een lager geheel getal geeft een hogere prioriteit aan, en deze regels worden eerder geëvalueerd dan regels met een hoger geheel getal. Zodra een overeenkomst met een regel is gevonden, wordt op de aanvraag de actie toegepast die is gedefinieerd in de regel. Zodra een dergelijke overeenkomst is verwerkt, worden regels met lagere prioriteiten niet meer verwerkt.

Er kan altijd maar één WAF-beleid tegelijk zijn gekoppeld aan een webtoepassing die wordt geleverd door Front Door. U kunt echter wel een configuratie Front Door definiëren zonder er een WAF-beleid aan te koppelen. Als er een WAF-beleid aanwezig is, wordt dit gerepliceerd naar al onze randlocaties om over de hele wereld een consistent beveiligingsbeleid te garanderen.

## <a name="waf-modes"></a>WAF-modi

Een WAF-beleid kan in de volgende twee modi worden uitgevoerd:

- **Detectiemodus**: Wanneer u het beleid uitvoert in de detectiemodus, doet WAF niets meer dan het controleren en vastleggen van de aanvraag en de overeenkomende WAF-regel in WAF-logboeken. U kunt logboekregistratie voor diagnostische gegevens inschakelen voor Front Door. Wanneer u de portal gebruikt, gaat u naar de sectie **Diagnostische gegevens**.

- **Preventiemodus**: In de preventiemodus voert WAF de opgegeven actie uit als een aanvraag overeenkomt met een regel. Als er een overeenkomst wordt gevonden, worden er geen verdere regels met een lagere prioriteit geëvalueerd. Alle overeenkomende aanvragen worden ook in de WAF-logboeken vastgelegd.

## <a name="waf-actions"></a>WAF-acties

WAF-klanten kunnen ervoor kiezen om een van de beschikbare acties uit te voeren wanneer een aanvraag overeenkomt met de voorwaarden van een regel:

- **Toestaan:**  De aanvraag wordt doorgegeven aan de WAF en wordt doorgestuurd naar de back-end. Deze aanvraag kan niet worden geblokkeerd door regels met een lagere prioriteit.
- **Blokkeren:** De aanvraag wordt geblokkeerd en WAF stuurt een reactie naar de client zonder de aanvraag door te sturen naar de back-end.
- **Logboek:**  De aanvraag wordt vastgelegd in de WAF-logboeken en WAF gaat verder met het evalueren van regels met een lagere prioriteit.
- **Omleiden:** WAF leidt de aanvraag om naar de opgegeven URI. De opgegeven URI geeft een instelling op beleidsniveau aan. Als deze actie is geconfigureerd, worden alle aanvragen die overeenkomen met de actie **Omleiden** naar die URI verzonden.

## <a name="waf-rules"></a>WAF-regels

Een WAF-beleid kan bestaan uit twee typen beveiligingsregels: aangepaste regels, opgesteld door de klant, en beheerde regelsets, door Azure beheerde, vooraf geconfigureerde sets met regels.

### <a name="custom-authored-rules"></a>Aangepaste regels

U kunt als volgt aangepaste regels configureren voor WAF:

- **Lijst met toegestane en geblokkeerde IP-adressen:** U kunt de toegang tot uw webtoepassingen beheren op basis van een lijst met IP-adressen of IP-adresbereiken van clients. Zowel IPv4- als IPv6-adrestypen worden ondersteund. Deze lijst kan worden geconfigureerd om aanvragen te blokkeren of toe te staan waarbij de bron-IP overeenkomt met een IP-adres in de lijst.

- **Toegangsbeheer op basis van geografische regio:** U kunt de toegang tot uw webtoepassingen beheren op basis van de landcode die is gekoppeld aan het IP-adres van een client.

- **Toegangsbeheer op basis van HTTP-parameters:** U kunt regels baseren op tekenreeksovereenkomsten in parameters voor HTTP/HTTPS-aanvragen, zoals  queryreeksen, POST-argumenten, aanvraag-URI, aanvraagheader en hoofdtekst van aanvraag.

- **Toegangsbeheer op basis van aanvraagmethode:** U kunt regels baseren op de HTTP-aanvraagmethode van de aanvraag, zoals GET, PUT of HEAD.

- **Groottebeperking:** U kunt regels baseren op de lengte van specifieke delen van een aanvraag, zoals queryreeks, URI of aanvraagtekst.

- **Regels voor volumebeperking:** Een regel voor volumebeperking is bedoeld om abnormaal hoog verkeer van een client-IP-adres te voorkomen. U kunt bijvoorbeeld een drempel configureren voor het aantal webaanvragen dat is toegestaan vanaf een client-IP gedurende één minuut. Deze regel is verschillend van een regel voor toestaan/blokkeren op basis van een IP-lijst, waarmee alle aanvragen van een client-IP worden toegestaan of geblokkeerd. Volumelimieten kunnen worden gecombineerd met aanvullende voorwaarden voor overeenkomsten, zoals HTTP(S)-parameterovereenkomsten voor nauwkeurige volumeregeling.

### <a name="azure-managed-rule-sets"></a>Door Azure beheerde regelsets

Door Azure beheerde regelsets bieden een eenvoudige manier om beveiliging te implementeren op basis van een gemeenschappelijke set beveiligingsbedreigingen. Aangezien dergelijke regelsets worden beheerd door Azure, worden de regels zo nodig bijgewerkt om ook bescherming te bieden tegen nieuwe aanvalshandtekeningen. De standaardregelset die door Azure wordt beheerd, bevat regels voor de volgende bedreigingscategorieën:

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
De standaardregelset is standaard ingeschakeld in de detectiemodus in uw WAF-beleid. U kunt afzonderlijke regels in de standaardregelset uit- of inschakelen om te voldoen aan de vereisten van uw toepassing. U kunt ook specifieke acties (TOESTAAN, BLOKKEREN, LOGBOEK en OMLEIDEN) instellen per regel.

Soms kan het nodig zijn om bepaalde kenmerken van aanvragen weg te laten uit een WAF-evaluatie. Een bekend voorbeeld is door Active Directory ingevoegde tokens die worden gebruikt voor verificatie. U kunt daarom een uitsluitingslijst configureren voor een beheerde regel, regelgroep of voor de complete regelset.  

De standaardactie is BLOKKEREN. Daarnaast kunnen in hetzelfde WAF-beleid aangepaste regels worden geconfigureerd als u een van de vooraf geconfigureerde regels in de standaardregelset wilt omzeilen.

Aangepaste regels worden altijd toegepast voordat regels in de standaardregelset worden geëvalueerd. Als een aanvraag overeenkomt met een aangepaste regel, wordt de bijbehorende regelactie toegepast. De aanvraag wordt geblokkeerd of doorgegeven aan de back-end. Er worden geen andere aangepaste regels verwerkt, evenmin als de regels in de standaardregelset. U kunt ook de standaardregelset uit uw WAF-beleid verwijderen.

### <a name="bot-protection-rule-set-preview"></a>Regelset voor beveiliging tegen bots (preview-versie)

U kunt een beheerde set regels voor de beveiliging tegen bots inschakelen om aangepaste acties te nemen op aanvragen van bots uit bekende categorieën. 

Er worden drie bot-categorieën ondersteund: Slecht, goed en onbekend. Bot-handtekeningen worden beheerd en dynamisch bijgewerkt door het WAF-platform.

Slechte bots zijn bots met schadelijke IP-adressen en bots die hun identiteit hebben vervalst. De IP-adressen zijn afkomstig uit de feed Bedreigingsinformatie van Microsoft en worden iedere uur bijgewerkt. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) voorziet de feed van gegevens en wordt gebruikt door verschillende services, waaronder Azure Security Center.

Goede bots bevatten gevalideerde zoekmachines. Onbekende categorieën bevatten extra bot-groepen die zichzelf als bots hebben geïdentificeerd. Denk hierbij aan market analyzer, feed fetchers en agents voor het verzamelen van gegevens. 

Onbekende bots worden geclassificeerd via gepubliceerde gebruikersagenten zonder verdere validatie. U kunt aangepaste acties instellen om verschillende typen bots te blokkeren, toestaan, vast te leggen in een logboek of om te leiden.

![Regelset voor beveiliging tegen bots](../media/afds-overview/botprotect2.png)

> [!IMPORTANT]
> De set met regels voor beveiliging tegen bots is momenteel in openbare preview en wordt aangeboden met een preview-SLA. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben.  Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Als bot-beveiliging is ingeschakeld, worden inkomende aanvragen die overeenkomen met bot-regels vastgelegd in het logboek FrontdoorWebApplicationFirewallLog. U kunt WAF-logboeken raadplegen vanuit een opslagaccount, Event Hub of Log Analytics.

## <a name="configuration"></a>Configuratie

U kunt alle WAF-regeltypen configureren en implementeren met behulp van de Azure-portal, REST-API's, Azure Resource Manager-sjablonen en Azure PowerShell.

## <a name="monitoring"></a>Bewaking

Bewaking voor WAF in Front door is geïntegreerd met Azure Monitor om waarschuwingen bij te houden en gemakkelijk trends in verkeer te ontdekken.

## <a name="next-steps"></a>Volgende stappen

- Lees meer over [Web Application Firewall voor Azure Application Gateway](../ag/ag-overview.md)