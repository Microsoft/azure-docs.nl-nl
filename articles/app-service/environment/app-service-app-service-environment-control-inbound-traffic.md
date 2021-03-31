---
title: Beheer van inkomend verkeer v1
description: Meer informatie over het beheren van inkomend verkeer naar een App Service Environment. Dit document is alleen bedoeld voor klanten die gebruikmaken van de oudere V1-ASE.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fe9326ea9ebd5afe981b7ba6c34b1a5d51e084b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962057"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>Inkomend verkeer naar een App Service Environment beheren
## <a name="overview"></a>Overzicht
Een App Service Environment kan worden gemaakt in een Azure Resource Manager virtueel netwerk **of** een [virtueel netwerk][virtualnetwork] **in het klassieke** implementatie model.  Er kan een nieuw virtueel netwerk en een nieuw subnet worden gedefinieerd op het moment dat een App Service Environment wordt gemaakt. In plaats daarvan kan een App Service Environment worden gemaakt in een bestaand virtueel netwerk en een reeds bestaand subnet.  Vanaf 2016 juni kan as ook worden geïmplementeerd in virtuele netwerken die gebruikmaken van open bare adresbereiken of RFC1918-adres ruimten (privé adressen).  Zie [How to Create a app service Environment][HowToCreateAnAppServiceEnvironment]voor meer informatie.

Maak altijd een App Service Environment binnen een subnet. Een subnet biedt een netwerk grens die kan worden gebruikt voor het vergren delen van inkomend verkeer achter upstream-apparaten en-services. Met deze installatie kunnen alleen specifieke IP-adressen van de upstream worden geaccepteerd voor HTTP-en HTTPS-verkeer.

Binnenkomend en uitgaand netwerk verkeer op een subnet wordt beheerd met behulp van een [netwerk beveiligings groep][NetworkSecurityGroups]. Als u inkomend verkeer wilt beheren, maakt u netwerk beveiligings regels in een netwerk beveiligings groep. Wijs vervolgens het subnet met de App Service Environment toe aan de netwerk beveiligings groep.

Zodra u een netwerk beveiligings groep aan een subnet toewijst, wordt binnenkomend verkeer naar apps in de App Service Environment toegestaan of geblokkeerd op basis van de regels voor toestaan en weigeren die zijn gedefinieerd in de netwerk beveiligings groep.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>Binnenkomende netwerk poorten die worden gebruikt in een App Service Environment
Voordat u binnenkomend netwerk verkeer met een netwerk beveiligings groep vergrendelt, moet u de set van de vereiste en optionele netwerk poorten die worden gebruikt door een App Service Environment weten.  Het per ongeluk sluiten van verkeer naar sommige poorten kan leiden tot verlies van functionaliteit in een App Service Environment.

De volgende lijst bevat de poorten die worden gebruikt door een App Service Environment. Alle poorten zijn **TCP**, tenzij anders duidelijk vermeld:

* 454: de  **vereiste poort** die wordt gebruikt door de Azure-infra structuur voor het beheren en onderhouden van app service omgevingen via TLS.  Verkeer naar deze poort niet blok keren.  Deze poort is altijd gebonden aan het open bare VIP van een ASE.
* 455: de  **vereiste poort** die wordt gebruikt door de Azure-infra structuur voor het beheren en onderhouden van app service omgevingen via TLS.  Verkeer naar deze poort niet blok keren.  Deze poort is altijd gebonden aan het open bare VIP van een ASE.
* 80: standaard poort voor binnenkomend HTTP-verkeer naar apps die worden uitgevoerd in App Service plannen in een App Service Environment.  Op een ILB-ASE is deze poort gebonden aan het ILB-adres van de ASE.
* 443: standaard poort voor binnenkomend TLS-verkeer naar apps die worden uitgevoerd in App Service plannen in een App Service Environment.  Op een ILB-ASE is deze poort gebonden aan het ILB-adres van de ASE.
* 21: besturings kanaal voor FTP.  Deze poort kan veilig worden geblokkeerd als FTP niet wordt gebruikt.  Op een ILB-ASE kan deze poort worden gebonden aan het ILB-adres voor een ASE.
* 990: besturings kanaal voor FTPS.  Deze poort kan veilig worden geblokkeerd als FTPS niet wordt gebruikt.  Op een ILB-ASE kan deze poort worden gebonden aan het ILB-adres voor een ASE.
* 10001-10020: gegevens kanalen voor FTP.  Net als bij het besturings kanaal kunnen deze poorten veilig worden geblokkeerd als FTP niet wordt gebruikt.  Op een ILB-ASE kan deze poort worden gebonden aan het ILB-adres van de ASE.
* 4016: wordt gebruikt voor fout opsporing op afstand met Visual Studio 2012.  Deze poort kan veilig worden geblokkeerd als de functie niet wordt gebruikt.  Op een ILB-ASE is deze poort gebonden aan het ILB-adres van de ASE.
* 4018: wordt gebruikt voor fout opsporing op afstand met Visual Studio 2013.  Deze poort kan veilig worden geblokkeerd als de functie niet wordt gebruikt.  Op een ILB-ASE is deze poort gebonden aan het ILB-adres van de ASE.
* 4020: wordt gebruikt voor fout opsporing op afstand met Visual Studio 2015.  Deze poort kan veilig worden geblokkeerd als de functie niet wordt gebruikt.  Op een ILB-ASE is deze poort gebonden aan het ILB-adres van de ASE.

## <a name="outbound-connectivity-and-dns-requirements"></a>Vereisten voor uitgaande verbindingen en DNS
Voor een goede werking van App Service Environment is ook uitgaande toegang tot verschillende eind punten vereist. Een volledige lijst van de externe eind punten die worden gebruikt door een ASE vindt u in de sectie ' vereiste netwerk verbinding ' van het artikel [netwerk configuratie voor ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

App Service omgevingen vereisen een geldige DNS-infra structuur die is geconfigureerd voor het virtuele netwerk.  Als de DNS-configuratie wordt gewijzigd na het maken van een App Service Environment, kunnen ontwikkel aars een App Service Environment dwingen om de nieuwe DNS-configuratie op te halen.  Als u het opnieuw opstarten van een doorlopende omgeving geactiveerd met het pictogram **opnieuw opstarten** , wordt de nieuwe DNS-configuratie door de omgeving gekozen. (Het pictogram **opnieuw starten** bevindt zich boven aan de blade app service Environment-beheer in de [Azure Portal][NewPortal].)

Het is ook raadzaam dat alle aangepaste DNS-servers op het vnet vooraf worden ingesteld voordat een App Service Environment wordt gemaakt.  Als de DNS-configuratie van een virtueel netwerk wordt gewijzigd tijdens het maken van een App Service Environment, mislukt het maken van de App Service Environment.  Als er een aangepaste DNS-server is die onbereikbaar is of niet beschikbaar is aan het andere uiteinde van een VPN-gateway, mislukt het proces voor het maken van App Service Environment ook.

## <a name="creating-a-network-security-group"></a>Een netwerk beveiligings groep maken
Raadpleeg de volgende [informatie][NetworkSecurityGroups]voor meer informatie over de werking van netwerk beveiligings groepen.  Het Azure Service Management-voor beeld hieronder raakt op hooglichten van netwerk beveiligings groepen. In het voor beeld wordt een netwerk beveiligings groep geconfigureerd en toegepast op een subnet dat een App Service Environment bevat.

**Opmerking:** Netwerk beveiligings groepen kunnen grafisch worden geconfigureerd met behulp van de [Azure Portal](https://portal.azure.com) of via Azure PowerShell.

Netwerk beveiligings groepen worden eerst gemaakt als een zelfstandige entiteit die is gekoppeld aan een abonnement. Aangezien netwerk beveiligings groepen worden gemaakt in een Azure-regio, maakt u de netwerk beveiligings groep in dezelfde regio als de App Service Environment.

De volgende opdracht laat zien hoe u een netwerk beveiligings groep maakt:

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

Zodra een netwerk beveiligings groep is gemaakt, worden er een of meer regels voor netwerk beveiliging aan toegevoegd.  Omdat de set regels na verloop van tijd kan veranderen, moet u het Nummerings schema dat wordt gebruikt voor regel prioriteiten, afnemen. Met deze procedure kunt u eenvoudig extra regels invoegen in de loop van de tijd.

In het onderstaande voor beeld verleent een regel expliciet toegang tot de beheer poorten die nodig zijn voor de Azure-infra structuur om een App Service Environment te beheren en te onderhouden.  Alle beheer verkeer loopt via TLS en wordt beveiligd door client certificaten. Hoewel de poorten zijn geopend, zijn ze niet toegankelijk voor andere entiteiten dan een Azure-beheer infrastructuur.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

Wanneer u de toegang tot poort 80 en 443 vergrendelt om een App Service Environment achter upstream-apparaten of-services te verbergen, moet u het IP-adres van de upstream onthouden.  Als u bijvoorbeeld een Web Application Firewall (WAF) gebruikt, heeft de WAF zijn eigen IP-adres of-adressen. De WAF gebruikt deze voor het door geven van verkeer naar een downstream App Service Environment.  U moet dit IP-adres gebruiken in de para meter *SourceAddressPrefix* van een netwerk beveiligings regel.

In het onderstaande voor beeld is inkomend verkeer van een specifiek upstream-IP-adres expliciet toegestaan.  Het adres *1.2.3.4* wordt gebruikt als tijdelijke aanduiding voor het IP-adres van een upstream-WAF.  Wijzig de waarde zodat deze overeenkomt met het adres dat wordt gebruikt door uw upstream-apparaat of-service.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

Als FTP-ondersteuning gewenst is, gebruikt u de volgende regels als sjabloon om toegang te verlenen tot de FTP-besturings poort en gegevens kanaal poorten.  Aangezien FTP een stateful protocol is, is het wellicht niet mogelijk om FTP-verkeer via een traditioneel HTTP/HTTPS-firewall of proxy apparaat te routeren.  In dit geval moet u de *SourceAddressPrefix* instellen op een andere waarde, zoals het IP-adres bereik van ontwikkel aars-of implementatie computers waarop FTP-clients worden uitgevoerd. 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

(**Opmerking:**  het poort bereik van het gegevens kanaal kan tijdens de preview-periode veranderen.)

Als externe fout opsporing met Visual Studio wordt gebruikt, demonstreert de volgende regels hoe u toegang kunt verlenen.  Er is een afzonderlijke regel voor elke ondersteunde versie van Visual Studio, aangezien elke versie een andere poort gebruikt voor fout opsporing op afstand.  Net als bij FTP-toegang kan het verkeer voor externe fout opsporing niet goed stromen via een traditioneel WAF of proxy apparaat.  De *SourceAddressPrefix* kan in plaats daarvan worden ingesteld op het IP-adres bereik van ontwikkel machines met Visual Studio.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>Een netwerk beveiligings groep toewijzen aan een subnet
Een netwerk beveiligings groep heeft een standaard beveiligings regel waarmee de toegang tot alle externe verkeer wordt geweigerd. Wanneer u deze regel combineert met de bovenstaande netwerk beveiligings regels, kan alleen verkeer van bronbereiken die zijn gekoppeld aan een actie *toestaan* verkeer verzenden naar apps die worden uitgevoerd in een app service environment.

Nadat een netwerk beveiligings groep is gevuld met beveiligings regels, wijst u deze toe aan het subnet met de App Service Environment.  De opdracht Assignment verwijst naar twee namen: de naam van het virtuele netwerk waar de App Service Environment zich bevindt en de naam van het subnet waarop de App Service Environment is gemaakt.  

In het onderstaande voor beeld ziet u een netwerk beveiligings groep die wordt toegewezen aan een subnet en een virtueel netwerk:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

De toewijzing is een langlopende bewerking en het kan een paar minuten duren voordat het proces is voltooid. Zodra de toewijzing van de netwerk beveiligings groep is gelukt, krijgt alleen binnenkomend verkeer dat overeenkomt met regels voor *toestaan* , apps in de app service environment.

Voor de volledigheid ziet u in het volgende voor beeld hoe u de netwerk beveiligings groep verwijdert en loskoppelt van het subnet:

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>Speciale overwegingen voor expliciete IP-SSL
Als een app is geconfigureerd met een expliciet IP-SSL-adres ( *alleen* van toepassing op as die een open bare VIP hebben), in plaats van het standaard IP-adres van de app service Environment, worden zowel http-als HTTPS-verkeer naar het subnet overgebracht via andere poorten dan poorten 80 en 443.

Als u wilt zoeken naar het afzonderlijke paar poorten dat door elk IP-SSL-adres wordt gebruikt, gaat u naar de portal en bekijkt u de Blade Details van de App Service Environment UX.  Selecteer **alle**  >  **IP-adressen** van instellingen.  De Blade **IP-adressen** bevat een tabel met alle expliciet geconfigureerde IP-SSL-adressen voor de app service environment. De Blade bevat ook het speciale poort paar dat wordt gebruikt voor het routeren van HTTP-en HTTPS-verkeer dat is gekoppeld aan elk IP-SSL-adres.  Gebruik dit poort paar voor de DestinationPortRange-para meters bij het configureren van regels in een netwerk beveiligings groep.

Wanneer een app op een ASE is geconfigureerd voor het gebruik van IP-SSL, kunnen externe klanten zich geen zorgen maken over de speciale poort paar toewijzing.  Verkeer naar de apps loopt normaal naar het geconfigureerde IP-SSL-adres.  De vertaling naar het speciale poort paar gebeurt automatisch intern tijdens de laatste etappe van het routerings verkeer in het subnet dat de ASE bevat. 

## <a name="getting-started"></a>Aan de slag
Zie [Inleiding tot app service Environment][IntroToAppServiceEnvironment]om aan de slag te gaan met app service omgevingen.

Zie voor meer informatie [veilig verbinding maken met back-end-bronnen via een app service Environment][SecurelyConnecttoBackend].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->