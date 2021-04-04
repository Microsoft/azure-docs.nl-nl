---
title: 'Azure ExpressRoute privé-peering: IPsec-transport modus configureren-Windows-hosts'
description: IPsec-transport modus inschakelen tussen Azure Windows-Vm's en on-premises Windows-hosts via ExpressRoute-persoonlijke peering met behulp van Gpo's en organisatie-eenheden.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: cfcc515787cee2bc90a2100e84337a3c96219d8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017269"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>IPsec-transport modus configureren voor persoonlijke ExpressRoute-peering

Dit artikel helpt u bij het maken van IPsec-tunnels in de transport modus via ExpressRoute persoonlijke peering. De tunnel wordt gemaakt tussen virtuele Azure-machines met Windows-en on-premises Windows-hosts. Voor de stappen in dit artikel voor deze configuratie worden groeps beleidsobjecten gebruikt. Hoewel het mogelijk is om deze configuratie te maken zonder organisatie-eenheden (Ou's) en groeps beleidsobjecten (Gpo's). De combi natie van organisatie-eenheden en groeps beleidsobjecten helpt u bij het vereenvoudigen van het beheer van uw beveiligings beleid en biedt u de mogelijkheid om snel omhoog te schalen. Voor de stappen in dit artikel wordt ervan uitgegaan dat u al een Active Directory configuratie hebt en dat u bekend bent met het gebruik van Ou's en Gpo's.

## <a name="about-this-configuration"></a>Over deze configuratie

De configuratie in de volgende stappen maakt gebruik van één virtueel netwerk van Azure (VNet) met ExpressRoute-persoonlijke peering. Deze configuratie kan echter tot andere Azure VNets-en on-premises netwerken beslaan. In dit artikel vindt u informatie over het definiëren van een IPsec-versleutelings beleid dat u kunt Toep assen op een groep virtuele machines van Azure of op lokale hosts. Deze Azure-Vm's of on-premises hosts maken deel uit van dezelfde organisatie-eenheid. U configureert versleuteling tussen de virtuele machines van Azure (VM1 en VM2) en de on-premises host1 alleen voor HTTP-verkeer met de doel poort 8080. U kunt verschillende typen IPsec-beleid maken op basis van uw vereisten.

### <a name="working-with-ous"></a>Werken met organisatie-eenheden 

Het beveiligings beleid dat is gekoppeld aan een organisatie-eenheid wordt gepusht naar de computers via GPO. Een aantal voor delen van het gebruik van Ou's, in plaats van beleid toe te passen op één host, zijn:

* Het koppelen van een beleid aan een organisatie-eenheid garandeert dat computers die deel uitmaken van dezelfde organisatie-eenheid hetzelfde beleid krijgen.
* Als u het beveiligings beleid van de organisatie-eenheid wijzigt, worden de wijzigingen toegepast op alle hosts in de organisatie-eenheid.

### <a name="diagrams"></a>Diagrammen

In het volgende diagram ziet u de interconnectie en toegewezen IP-adres ruimte. De Azure-Vm's en de on-premises host worden uitgevoerd op Windows 2016. De virtuele machines van Azure en de on-premises host1 maken deel uit van hetzelfde domein. De virtuele machines van Azure en de on-premises hosts kunnen namen omzetten met behulp van DNS.

[![1]][1]

Dit diagram toont de IPsec-tunnels in transit in ExpressRoute-persoonlijke peering.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Werken met IPsec-beleid

In Windows wordt versleuteling gekoppeld aan het IPsec-beleid. IPsec-beleid bepaalt welk IP-verkeer wordt beveiligd en het beveiligings mechanisme dat wordt toegepast op de IP-pakketten.
**IPSec-beleid** bestaat uit de volgende items: **filter lijsten**, **filter acties** en **beveiligings regels**.

Bij het configureren van het IPsec-beleid is het belang rijk om inzicht te krijgen in de volgende begrippen voor het IPsec-beleid:

* **IPSec-beleid:** Een verzameling regels. Op elk gewenst moment kan slechts één beleid actief zijn (' toegewezen '). Elk beleid kan een of meer regels hebben, die allemaal tegelijk actief kunnen zijn. Aan een computer kan op een bepaald moment slechts één actief IPsec-beleid worden toegewezen. Binnen het IPsec-beleid kunt u echter meerdere acties definiëren die in verschillende situaties kunnen worden uitgevoerd. Elke set IPsec-regels is gekoppeld aan een filter lijst die van invloed is op het type netwerk verkeer waarop de regel van toepassing is.

* **Filter lijsten:** Filter lijsten zijn bundel van een of meer filters. Eén lijst kan meerdere filters bevatten. Een filter definieert of de communicatie wordt geblokkeerd, toegestaan of beveiligd is op basis van de volgende criteria: IP-adresbereiken, protocollen of zelfs specifieke poorten. Elk filter komt overeen met een bepaalde set voor waarden; Pakketten die bijvoorbeeld vanuit een bepaald subnet naar een bepaalde computer op een specifieke doel poort worden verzonden. Wanneer de netwerk voorwaarden overeenkomen met een of meer van deze filters, wordt de filter lijst geactiveerd. Elk filter wordt in een specifieke filter lijst gedefinieerd. Filters kunnen niet worden gedeeld tussen filter lijsten. Een gegeven filter lijst kan echter worden opgenomen in verschillende IPsec-beleids regels. 

* **Filter acties:** Een beveiligings methode definieert een reeks beveiligings algoritmen, protocollen en sleutel een computer die tijdens IKE-onderhandelingen wordt aangeboden. Filter acties zijn lijsten met beveiligings methoden, gerangschikt in volg orde van voor keur.  Wanneer een computer een IPsec-sessie onderhandelt, worden Voorst Ellen geaccepteerd of verzonden op basis van de beveiligings instelling die in de lijst met filter acties is opgeslagen.

* **Beveiligings regels:** Regels bepalen hoe en wanneer een IPsec-beleid communicatie beveiligt. Er wordt **gefilterde lijst** -en **filter acties** gebruikt voor het maken van een IPSec-regel voor het bouwen van de IPSec-verbinding. Elk beleid kan een of meer regels hebben, die allemaal tegelijk actief kunnen zijn. Elke regel bevat een lijst met IP-filters en een verzameling beveiligings acties die worden uitgevoerd bij een overeenkomst met die filter lijst:
  * IP-filter acties
  * Verificatiemethoden
  * IP-tunnel instellingen
  * Verbindingstypen

[![5]][5]

## <a name="before-you-begin"></a>Voordat u begint

Zorg ervoor dat u aan de volgende vereisten voldoet:

* U moet een werkende Active Directory configuratie hebben die u kunt gebruiken om groepsbeleid-instellingen te implementeren. Zie [Groepsbeleid-objecten](/previous-versions/windows/desktop/Policy/group-policy-objects)voor meer informatie over groeps beleidsobjecten.

* U moet een actief ExpressRoute-circuit hebben.
  * Zie [een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md)voor meer informatie over het maken van een ExpressRoute-circuit. 
  * Controleer of het circuit is ingeschakeld door de connectiviteits provider. 
  * Controleer of u persoonlijke Azure-peering voor uw circuit hebt geconfigureerd. In het artikel [Routering configureren](expressroute-howto-routing-arm.md) vindt u instructies voor routering. 
  * Controleer of u een VNet en een virtuele netwerk gateway hebt gemaakt en volledig hebt ingericht. Volg de instructies om [een virtuele netwerkgateway voor ExpressRoute te maken](expressroute-howto-add-gateway-resource-manager.md). Een virtuele netwerkgateway voor ExpressRoute maakt gebruik van het GatewayType 'ExpressRoute', niet VPN.

* De gateway van het virtuele ExpressRoute-netwerk moet zijn verbonden met het ExpressRoute-circuit. Zie [een VNet verbinden met een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)voor meer informatie.

* Controleer of de virtuele Azure Windows-machines zijn geïmplementeerd in het VNet.

* Controleer of er een verbinding is tussen de on-premises hosts en de virtuele machines van Azure.

* Controleer of de Azure Windows-Vm's en de on-premises hosts DNS kunnen gebruiken om namen correct te herleiden.

### <a name="workflow"></a>Werkstroom

1. Maak een groeps beleidsobject en koppel dit aan de organisatie-eenheid.
2. Definieer een IPsec- **filter actie**.
3. Definieer een IPsec- **filter lijst**.
4. Maak een IPsec-beleid met **beveiligings regels**.
5. Wijs het IPsec-groeps beleidsobject toe aan de organisatie-eenheid.

### <a name="example-values"></a>Voorbeeldwaarden

* **Domein naam:** ipsectest.com

* **OE:** IPSecOU

* **On-premises Windows-computer:** host1

* **Azure Windows-vm's:** VM1, VM2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. een groeps beleidsobject maken

1. Maak een nieuw groeps beleidsobject dat is gekoppeld aan een organisatie-eenheid door de module groepsbeleid-beheer te openen. Zoek vervolgens de organisatie-eenheid waaraan het groeps beleidsobject wordt gekoppeld. In het voor beeld heeft de OE de naam **IPSecOU**. 

   [![9]][9]
2. Selecteer in de module groepsbeleid-beheer de OE en klik met de rechter muisknop. Selecteer in de vervolg keuzelijst '**een groeps beleidsobject in dit domein maken en hier een koppeling...**'.

   [![10]][10]
3. Noem het groeps beleidsobject een intuïtieve naam, zodat u deze later eenvoudig kunt vinden. Selecteer **OK** om het groeps beleidsobject te maken en te koppelen.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. Schakel de groeps beleidsobject koppeling in

Om het groeps beleidsobject toe te passen op de OE, mag het GPO niet alleen worden gekoppeld aan de organisatie-eenheid, maar moet de koppeling ook worden ingeschakeld.

1. Zoek het groeps beleidsobject dat u hebt gemaakt, klik met de rechter muisknop en selecteer **bewerken** in de vervolg keuzelijst.
2. Selecteer **koppeling ingeschakeld** om het groeps beleidsobject toe te passen op de OE.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. de IP-filter actie definiëren

1. Klik in de vervolg keuzelijst met de rechter muisknop op **IP-beveiligings beleid op Active Directory** en selecteer vervolgens **IP-filter lijsten beheren en filter acties...**.

   [![15]][15]
2. Selecteer op het tabblad **filter acties beheren** de optie **toevoegen**.

   [![16]][16]

3. Selecteer **volgende** in de **wizard filter actie voor IP-beveiliging**.

   [![17]][17]
4. Noem de filter actie een intuïtieve naam, zodat u deze later kunt vinden. In dit voor beeld heeft de filter actie de naam **myEncryption**. U kunt ook een beschrijving toevoegen. Selecteer vervolgens **Volgende**.

   [![18]][18]
5. Met **onderhandelen over beveiliging** kunt u het gedrag definiëren als IPSec niet kan worden ingesteld met een andere computer. Selecteer **onderhandelen over beveiliging** en selecteer vervolgens **volgende**.

   [![19]][19]
6. Op de pagina **communiceren met computers die geen ondersteuning bieden voor IPSec** , selecteert u **niet-beveiligde communicatie toestaan** en selecteert u **volgende**.

   [![20]][20]
7. Selecteer op de pagina **IP-verkeer en beveiliging** de optie **aangepast** en selecteer vervolgens **instellingen...**.

   [![21]][21]
8. Selecteer op de pagina **aangepaste beveiligings methode instellingen** **gegevens integriteit en versleuteling (ESP): SHA1, 3DES**. Selecteer vervolgens **OK**.

   [![22]][22]
9. Op de pagina **filter acties beheren** kunt u zien dat het **myEncryption** -filter is toegevoegd. Selecteer **Sluiten**.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. een IP-filter lijst definiëren

Maak een filter lijst die versleuteld HTTP-verkeer opgeeft met doel poort 8080.

1. Gebruik een **IP-filter lijst** om te kwalificeren welke typen verkeer moeten worden versleuteld. Selecteer op het tabblad **IP-filter lijsten beheren** de optie **toevoegen** om een nieuwe IP-filter lijst toe te voegen.

   [![24]][24]
2. Typ in het veld **naam:** een naam voor de IP-filter lijst. Bijvoorbeeld **Azure-on-HTTP8080**. Selecteer vervolgens **toevoegen**.

   [![25]][25]
3. Selecteer op de pagina **Beschrijving en gespiegelde eigenschappen van IP-filter** de optie **gespiegeld**. De gespiegelde instelling komt overeen met pakketten die in beide richtingen worden verzonden, waardoor communicatie in twee richtingen mogelijk is. Selecteer vervolgens **Volgende**.

   [![26]][26]
4. Kies op de pagina **IP-verkeer bron** , uit de vervolg keuzelijst **bron adres:** **een specifiek IP-adres of subnet**. 

   [![27]][27]
5. Geef het **IP-adres of subnet** van het bron adres op: van het IP-verkeer en selecteer vervolgens **volgende**.

   [![28]][28]
6. Geef het **doel adres op:** IP-adres of subnet. Selecteer vervolgens **Volgende**.

   [![29]][29]
7. Selecteer op de pagina **IP-protocol type** de optie **TCP**. Selecteer vervolgens **Volgende**.

   [![30]][30]
8. Selecteer op de pagina **IP-protocol poort** **een van de poorten** en **tot deze poort:**. Typ **8080** in het tekstvak. Deze instellingen geven alleen het HTTP-verkeer op de doel poort 8080 wordt versleuteld. Selecteer vervolgens **Volgende**.

   [![31]][31]
9. De IP-filter lijst weer geven.  De configuratie van de IP-filter lijst **Azure-on-HTTP8080** activeert versleuteling voor al het verkeer dat voldoet aan de volgende criteria:

   * Elk bron adres in 10.0.1.0/24 (Azure Subnet2)
   * Elk doel adres in 10.2.27.0/25 (on-premises subnet)
   * TCP-protocol
   * Doel poort 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. de IP-filter lijst bewerken

Als u hetzelfde type verkeer van de on-premises host naar de Azure VM wilt versleutelen, hebt u een tweede IP-filter nodig. Volg dezelfde stappen die u hebt gebruikt voor het instellen van het eerste IP-filter en het maken van een nieuw IP-filter. De enige verschillen zijn het bron-en doel-subnet.

1. Als u een nieuw IP-filter wilt toevoegen aan de IP-filter lijst, selecteert u **bewerken**.

   [![33]][33]
2. Selecteer op de pagina **IP-filter lijst** de optie **toevoegen**.

   [![34]][34]
3. Maak een tweede IP-filter met behulp van de instellingen in het volgende voor beeld:

   [![35]][35]
4. Nadat u het tweede IP-filter hebt gemaakt, ziet de IP-filter lijst er als volgt uit:

   [![36]][36]

Als versleuteling vereist is tussen een on-premises locatie en een Azure-subnet om een toepassing te beveiligen. In plaats van de bestaande IP-filter lijst te wijzigen, kunt u een nieuwe IP-filter lijst toevoegen. Als u twee of meer IP-filter lijsten aan hetzelfde IPsec-beleid koppelt, beschikt u over meer flexibiliteit. U kunt een IP-filter lijst wijzigen of verwijderen zonder dat dit van invloed is op de andere IP-filter lijsten.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. een IPsec-beveiligings beleid maken 

Maak een IPsec-beleid met beveiligings regels.

1. Selecteer het **IPSecurity-beleid op Active Directory** dat is gekoppeld aan de organisatie-eenheid. Klik met de rechter muisknop en selecteer **IP-beveiligings beleid maken**.

   [![37]][37]
2. Geef het beveiligings beleid een naam. Bijvoorbeeld **beleid: Azure-on**. Selecteer vervolgens **Volgende**.

   [![38]][38]
3. Selecteer **volgende** zonder het selectie vakje in te scha kelen.

   [![39]][39]
4. Controleer of het selectie vakje **Eigenschappen bewerken** is geselecteerd en selecteer vervolgens **volt ooien**.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. het IPsec-beveiligings beleid bewerken

Voeg aan het IPsec-beleid de **IP-filter lijst** en **filter actie** toe die u eerder hebt geconfigureerd.

1. Selecteer **toevoegen** op het tabblad **regels** voor het beleid met http-eigenschappen.

   [![41]][41]
2. Op de pagina Welkom selecteert u **Volgende**.

   [![42]][42]
3. Een regel biedt de mogelijkheid om de IPsec-modus te definiëren: tunnel modus of transport modus.

   * In de tunnel modus wordt het oorspronkelijke pakket ingekapseld door een set IP-headers. De tunnel modus beveiligt de interne routerings informatie door de IP-header van het oorspronkelijke pakket te versleutelen. De tunnel modus is breed geïmplementeerd tussen gateways in scenario's met site-naar-site-VPN. De tunnel modus is in de meeste gevallen gebruikt voor end-to-end-versleuteling tussen hosts.

   * In de transport modus worden alleen de payload en ESP trailer versleuteld. de IP-header van het oorspronkelijke pakket is niet versleuteld. In de transport modus worden de IP-bron en IP-bestemming van de pakketten ongewijzigd.

   Selecteer **deze regel geeft geen tunnel** op en selecteer **volgende**.

   [![43]][43]
4. **Netwerk type** definieert welke netwerk verbinding aan het beveiligings beleid wordt gekoppeld. Selecteer **alle netwerk verbindingen** en selecteer vervolgens **volgende**.

   [![44]][44]
5. Selecteer de IP-filter lijst die u eerder hebt gemaakt,  **Azure-on-HTTP8080** en selecteer vervolgens **volgende**.

   [![45]][45]
6. Selecteer de bestaande filter actie **myEncryption** die u eerder hebt gemaakt.

   [![46]][46]
7. Windows ondersteunt vier verschillende soorten verificaties: Kerberos, certificaten, NTLMv2 en vooraf gedeelde sleutel. Omdat we werken met hosts die lid zijn van een domein, selecteert u **Active Directory standaard (Kerberos V5-protocol)** en selecteert u **volgende**.

   [![47]][47]
8. Het nieuwe beleid maakt de beveiligings regel: **Azure-on-HTTP8080**. Selecteer **OK**.

   [![48]][48]

Het IPsec-beleid vereist dat alle HTTP-verbindingen op de doel poort 8080 gebruikmaken van de IPsec-transport modus. Omdat HTTP een protocol met lees bare tekst is waarvoor het beveiligings beleid is ingeschakeld, zorgt ervoor dat de gegevens worden versleuteld wanneer ze worden overgedragen via de ExpressRoute-persoonlijke peering. IPsec-beleid voor Active Directory is complexer om te configureren dan Windows Firewall met geavanceerde beveiliging. Het biedt echter meer aanpassing van de IPsec-verbinding.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. Wijs het IPsec-groeps beleidsobject toe aan de OE

1. Het beleid weer geven. Het beveiligings groeps beleid is gedefinieerd, maar is nog niet toegewezen.

   [![49]][49]
2. Om het beveiligings groeps beleid toe te wijzen aan de OE **IPSecOU**, klikt u met de rechter muisknop op het beveiligings beleid en kiest u **toewijzen**.
   Aan elke computer-tht die deel uitmaakt van de OE wordt het beveiligings groeps beleid toegewezen.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Verkeer versleuteling controleren

Als u de versleutelings groep wilt uitchecken die is toegepast op de OE, installeert u IIS op alle virtuele machines van Azure en in de host1. Elke IIS is aangepast voor het beantwoorden van HTTP-aanvragen op poort 8080.
Als u versleuteling wilt controleren, kunt u een netwerksniffer (zoals wireshark) installeren op alle computers in de organisatie-eenheid.
Een Power shell-script werkt als een HTTP-client voor het genereren van HTTP-aanvragen op poort 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
In het volgende netwerk worden de resultaten weer gegeven voor on-premises host1 met weergave filter ESP zodat alleen het versleutelde verkeer overeenkomt:

[![51]][51]

Als u het Power shell-script on-premises (HTTP-client) uitvoert, wordt op de netwerk opname in de Azure-VM een soort gelijke tracering weer gegeven.

## <a name="next-steps"></a>Volgende stappen

Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "netwerk diagram IPSec-transport modus via ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "interessant IPSec-verkeer"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Windows IPSec-beleid"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "organisatie-eenheid in de Groepsbeleid"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "een groeps beleidsobject maken dat is gekoppeld aan de organisatie-eenheid"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "een naam toewijzen aan het groeps beleidsobject dat is gekoppeld aan de organisatie-eenheid"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "het groeps beleidsobject bewerken"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "IP-filter lijsten en filter acties beheren"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "filter actie toevoegen"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "actie wizard"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "filter actie naam"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "filter actie"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "Geef het gedrag op. er wordt een onbeveiligde verbinding tot stand gebracht"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "beveiligings mechanisme"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "aangepaste beveiligings methode"
[]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "lijst met 23 filter acties"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "een nieuwe IP-filter lijst toevoegen"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "http-verkeer toevoegen aan het IP-filter"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "overeenkomst pakket in beide richtingen"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "selectie van het bron-subnet"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png- "bron netwerk"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "doel netwerk"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png- "protocol"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "bron poort en doel poort"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png- "filter lijst"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "IP-filter lijst met http-verkeer"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "een tweede IP-filter toevoegen"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "IP-filter lijst-tweede vermelding"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "IP-filter lijst-tweede vermelding"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "het IP-beveiligings beleid maken"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "naam van het IPSec-beleid"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "IPSec-beleid wizard"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "bewerken van het IPSec-beleid"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "nieuwe beveiligings regel toevoegen aan het IPSec-beleid"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "een nieuwe beveiligings regel maken"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Transport modus"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png- "netwerk type"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "selectie van bestaande IP-filter lijst"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "selectie van bestaande filter actie"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "selectie van de verificatie methode"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "einde van het proces voor het maken van het beveiligings beleid"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "IPSec-beleid dat is gekoppeld aan het groeps beleidsobject maar niet is toegewezen"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "IPSec-beleid dat is toegewezen aan het groeps beleidsobject"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "vastleggen van versleuteld IPSec-verkeer"