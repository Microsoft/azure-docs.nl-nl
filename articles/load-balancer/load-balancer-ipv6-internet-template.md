---
title: Een Internet gerichte Load Balancer implementeren met IPv6-Azure-sjabloon
titleSuffix: Azure Load Balancer
description: Meer informatie over het implementeren van IPv6-ondersteuning voor Azure Load Balancer en virtuele machines met taak verdeling met behulp van een Azure-sjabloon.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure load balancer, dual stack, openbaar IP, systeem eigen IPv6, mobiel, IOT
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 415c95a441ac0cc6ed2dbf2d6a37f57d7a9e7341
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90562516"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Een Internet gerichte Load Balancer-oplossing met IPv6 implementeren met behulp van een sjabloon

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure-CLI](load-balancer-ipv6-internet-cli.md)
> * [Sjabloon](load-balancer-ipv6-internet-template.md)


>[!NOTE] 
>In dit artikel wordt een inleidende IPv6-functie beschreven waarmee Basic load balancers zowel IPv4-als IPv6-connectiviteit kunnen bieden. Uitgebreide IPv6-connectiviteit is nu beschikbaar met [IPv6 voor Azure VNETs](../virtual-network/ipv6-overview.md) , die IPv6-connectiviteit integreert met uw virtuele netwerken en die belang rijke functies bevat zoals regels voor IPv6-netwerk beveiligings groepen, door IPv6 door de gebruiker gedefinieerde route ring, IPv6-basis en standaard taak verdeling en meer.  IPv6 voor Azure VNETs is de aanbevolen standaard voor IPv6-toepassingen in Azure. Zie [IPv6 voor Azure VNET Power shell-implementatie](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)  

Azure Load Balancer is een Layer-4 (TCP, UDP) load balancer. De load balancer biedt hoge beschikbaarheid bij het verdelen van inkomend verkeer over gezonde service-exemplaren in cloudservices of virtuele machines in een load balancer-set. Azure Load Balancer kan deze services ook toepassen op meerdere poorten, meerdere IP-adressen of allebei.

## <a name="example-deployment-scenario"></a>Voor beeld van implementatie scenario

In het volgende diagram ziet u de taakverdelings oplossing die wordt geïmplementeerd met behulp van de voorbeeld sjabloon die in dit artikel wordt beschreven.

![In het diagram ziet u een voorbeeld scenario dat in dit artikel wordt gebruikt, met inbegrip van een werk station-client die is verbonden met een Azure Load Balancer via internet, waarmee verbinding wordt gemaakt met twee virtuele machines.](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

In dit scenario maakt u de volgende Azure-resources:

* een virtuele netwerk interface voor elke virtuele machine waaraan IPv4-en IPv6-adressen zijn toegewezen
* een Internet gerichte Load Balancer met een IPv4-en een openbaar IP-adres voor IPv6
* twee taakverdelings regels om de open bare Vip's toe te wijzen aan de privé-eind punten
* een Beschikbaarheidsset die de twee virtuele machines bevat
* twee virtuele machines (Vm's)

## <a name="deploying-the-template-using-the-azure-portal"></a>De sjabloon implementeren met behulp van de Azure Portal

In dit artikel wordt verwezen naar een sjabloon die is gepubliceerd in de [Azure Quick](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) start-sjablonen galerie. U kunt de sjabloon downloaden vanuit de galerie of de implementatie rechtstreeks vanuit de galerie starten in Azure. In dit artikel wordt ervan uitgegaan dat u de sjabloon hebt gedownload naar de lokale computer.

1. Open de Azure Portal en meld u aan met een account met machtigingen voor het maken van Vm's en netwerk bronnen binnen een Azure-abonnement. Tenzij u bestaande resources gebruikt, moet het account ook toestemming voor het maken van een resource groep en een opslag account hebben.
2. Klik in het menu op "+ nieuw" en typ "sjabloon" in het zoekvak. Selecteer Sjabloonimlementatie in de zoek resultaten.

    ![Scherm afbeelding toont de Azure Portal met nieuw en Sjabloonimlementatie geselecteerd.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Klik op de Blade alles op Sjabloonimlementatie.

    ![Scherm afbeelding toont Sjabloonimlementatie op Marketplace.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Klik op maken.

    ![Scherm afbeelding toont de beschrijving van Sjabloonimlementatie in de Marketplace.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Klik op sjabloon bewerken. De bestaande inhoud verwijderen en kopiëren/plakken in de volledige inhoud van het sjabloon bestand (met de begin-en eind {}) en klik vervolgens op opslaan.

    > [!NOTE]
    > Als u micro soft Internet Explorer gebruikt, ontvangt u een dialoog venster waarin wordt gevraagd of u toegang tot het Windows-klem bord wilt toestaan. Klik op toegang toestaan.

    ![Scherm afbeelding toont de geactiveerde stap van een aangepaste implementatie. Dit is een bewerkings sjabloon.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Klik op para meters bewerken. Geef op de Blade para meters de waarden op volgens de instructies in de sectie sjabloon parameters en klik vervolgens op Opslaan om de Blade para meters te sluiten. Selecteer in de Blade aangepaste implementatie uw abonnement, een bestaande resource groep of maak een. Als u een resource groep maakt, selecteert u een locatie voor de resource groep. Klik vervolgens op **juridische voor waarden** en vervolgens op **aanschaffen** voor de juridische voor waarden. De implementatie van de resources wordt gestart door Azure. Het duurt enkele minuten om alle resources te implementeren.

    ![In de scherm afbeelding ziet u de stappen in de aangepaste implementatie, beginnend met het invoeren van de parameter waarden van de sjabloon.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Zie de sectie [sjabloon parameters en variabelen](#template-parameters-and-variables) verderop in dit artikel voor meer informatie over deze para meters.

7. Als u de resources wilt zien die zijn gemaakt met de sjabloon, klikt u op Bladeren, schuift u omlaag in de lijst totdat u ' resource groepen ' ziet, klikt u op het item.

    ![Scherm afbeelding toont de Azure Portal met bladeren en resource groepen geselecteerd.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. Klik op de Blade resource groepen op de naam van de resource groep die u in stap 6 hebt opgegeven. Er wordt een lijst weer geven met alle resources die zijn geïmplementeerd. Als alles goed is geworden, moet het ' geslaagd ' onder de laatste implementatie zeggen. Als dat niet het geval is, moet u ervoor zorgen dat het account dat u gebruikt, gemachtigd is om de benodigde resources te maken.

    ![Scherm afbeelding toont de status van de laatste implementatie voor een resource groep, in dit voor beeld geslaagd.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Als u door de resource groepen bladert onmiddellijk nadat u stap 6 hebt voltooid, wordt in de laatste implementatie de status ' implementeren ' weer gegeven terwijl de resources worden geïmplementeerd.

9. Klik in de lijst met resources op ' myIPv6PublicIP '. U ziet dat deze een IPv6-adres onder IP-adres heeft en dat de DNS-naam de waarde is die u in stap 6 hebt opgegeven voor de para meter dnsNameforIPv6LbIP. Deze bron is het open bare IPv6-adres en de hostnaam die toegankelijk is voor Internet-clients.

    ![Scherm afbeelding toont het open bare IPv6-adres.](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Connectiviteit valideren

Wanneer de sjabloon is geïmplementeerd, kunt u de connectiviteit valideren door de volgende taken uit te voeren:

1. Meld u aan bij de Azure Portal en maak verbinding met elke virtuele machine die is gemaakt door de sjabloon implementatie. Als u een virtuele machine met Windows Server hebt geïmplementeerd, voert u ipconfig/all uit vanaf een opdracht prompt. U ziet dat de virtuele machines zowel IPv4-als IPv6-adressen hebben. Als u virtuele Linux-machines hebt geïmplementeerd, moet u het Linux-besturings systeem configureren voor het ontvangen van dynamische IPv6-adressen met behulp van de instructies voor uw Linux-distributie.
2. Vanuit een IPv6-client met Internet verbinding initieert een verbinding met het open bare IPv6-adres van de load balancer. Als u wilt controleren of de load balancer tussen de twee virtuele machines balanceert, kunt u op elk van de virtuele machines een webserver installeren, zoals micro soft Internet Information Services (IIS). De standaard webpagina op elke server kan de tekst ' Server0 ' of ' server1 ' bevatten om de pagina uniek te identificeren. Open vervolgens een Internet browser op een IPv6-client met Internet verbinding en blader naar de hostnaam die u hebt opgegeven voor de para meter dnsNameforIPv6LbIP van de load balancer om end-to-end IPv6-verbinding met elke virtuele machine te bevestigen. Als u de webpagina alleen van één server ziet, moet u mogelijk uw browser cache wissen. Open meerdere sessies voor persoonlijke navigatie. U ziet een reactie van elke server.
3. Vanuit een IPv4-client met Internet verbinding initieert een verbinding met het open bare IPv4-adres van de load balancer. Als u wilt controleren of de load balancer taak verdeling van de twee virtuele machines, kunt u met IIS testen, zoals wordt beschreven in stap 2.
4. Start vanaf elke virtuele machine een uitgaande verbinding naar een IPv6-of IPv4-verbonden Internet apparaat. In beide gevallen is het bron-IP-adres dat wordt weer gegeven door het doel apparaat het open bare IPv4-of IPv6-adressen van de load balancer.

> [!NOTE]
> ICMP voor IPv4 en IPv6 wordt geblokkeerd in het Azure-netwerk. Als gevolg hiervan zijn ICMP-hulpprogram ma's zoals ping altijd mislukt. Als u de connectiviteit wilt testen, gebruikt u een TCP-alternatief zoals TCPing of de Power shell Test-NetConnection-cmdlet. Houd er rekening mee dat de IP-adressen die in het diagram worden weer gegeven, voor beelden zijn van waarden die u mogelijk ziet. Aangezien de IPv6-adressen dynamisch worden toegewezen, worden de adressen die u ontvangt, verschillend en kunnen per regio verschillen. Het is ook gebruikelijk dat het open bare IPv6-adres op het load balancer begint met een ander voor voegsel dan de privé-IPv6-adressen in de back-end-pool.

## <a name="template-parameters-and-variables"></a>Sjabloon parameters en variabelen

Een Azure Resource Manager sjabloon bevat meerdere variabelen en para meters die u aan uw behoeften kunt aanpassen. Variabelen worden gebruikt voor vaste waarden die u niet wilt dat de gebruiker deze wijzigt. Para meters worden gebruikt voor waarden die door een gebruiker moeten worden opgegeven bij het implementeren van de sjabloon. De voorbeeld sjabloon is geconfigureerd voor het scenario dat in dit artikel wordt beschreven. U kunt deze aanpassen aan uw behoeften van uw omgeving.

De voorbeeld sjabloon die in dit artikel wordt gebruikt, bevat de volgende variabelen en para meters:

| Para meter/variabele | Notities |
| --- | --- |
| adminUsername |Geef de naam op van het beheerders account dat wordt gebruikt om u aan te melden bij de virtuele machines met. |
| adminPassword |Geef het wacht woord op voor het beheerders account dat wordt gebruikt om u aan te melden bij de virtuele machines met. |
| dnsNameforIPv4LbIP |Geef de naam op van de DNS-host die u wilt toewijzen als de open bare naam van de load balancer. Deze naam wordt omgezet in het open bare IPv4-adres van de load balancer. De naam moet een kleine letter zijn en overeenkomen met de regex: ^ [a-z] [a-z0-9-] {1,61} [a-z0-9] $. |
| dnsNameforIPv6LbIP |Geef de naam op van de DNS-host die u wilt toewijzen als de open bare naam van de load balancer. Deze naam wordt omgezet in het open bare IPv6-adres van de load balancer. De naam moet een kleine letter zijn en overeenkomen met de regex: ^ [a-z] [a-z0-9-] {1,61} [a-z0-9] $. Dit kan dezelfde naam zijn als het IPv4-adres. Wanneer een client een DNS-query voor deze naam verzendt, retourneert Azure zowel de A-als AAAA-records wanneer de naam wordt gedeeld. |
| vmNamePrefix |Geef het voor voegsel van de VM-naam op. De sjabloon voegt een getal (0, 1 enz.) toe aan de naam wanneer de virtuele machines worden gemaakt. |
| nicNamePrefix |Geef het voor voegsel van de netwerk interface naam op. De sjabloon voegt een getal (0, 1 enz.) toe aan de naam wanneer de netwerk interfaces worden gemaakt. |
| storageAccountName |Voer de naam van een bestaand opslag account in of geef de naam op van een nieuwe die moet worden gemaakt door de sjabloon. |
| availabilitySetName |Voer vervolgens de naam in van de beschikbaarheidsset die moet worden gebruikt met de virtuele machines |
| addressPrefix |Het adres voorvoegsel dat wordt gebruikt voor het definiëren van het adres bereik van de Virtual Network |
| subnetName |De naam van het subnet dat voor het VNet is gemaakt |
| subnetPrefix |Het adres voorvoegsel dat wordt gebruikt voor het definiëren van het adres bereik van het subnet |
| vnetName |Geef de naam op voor het VNet dat door de virtuele machines wordt gebruikt. |
| ipv4PrivateIPAddressType |De toewijzings methode die wordt gebruikt voor het privé-IP-adres (statisch of dynamisch) |
| ipv6PrivateIPAddressType |De toewijzings methode die wordt gebruikt voor het privé IP-adres (dynamisch). IPv6 ondersteunt alleen dynamische toewijzing. |
| numberOfInstances |Het aantal exemplaren met gelijke taak verdeling dat door de sjabloon wordt geïmplementeerd |
| ipv4PublicIPAddressName |Geef de DNS-naam op die u wilt gebruiken om te communiceren met het open bare IPv4-adres van de load balancer. |
| ipv4PublicIPAddressType |De toewijzings methode die wordt gebruikt voor het open bare IP-adres (statisch of dynamisch) |
| Ipv6PublicIPAddressName |Geef de DNS-naam op die u wilt gebruiken om te communiceren met het open bare IPv6-adres van de load balancer. |
| ipv6PublicIPAddressType |De toewijzings methode die wordt gebruikt voor het open bare IP-adres (dynamisch). IPv6 ondersteunt alleen dynamische toewijzing. |
| lbName |Geef de naam op van de load balancer. Deze naam wordt weer gegeven in de portal of wordt gebruikt wanneer ernaar wordt verwezen met een CLI-of Power shell-opdracht. |

De resterende variabelen in de sjabloon bevatten afgeleide waarden die worden toegewezen wanneer Azure de resources maakt. Wijzig deze variabelen niet.

## <a name="next-steps"></a>Volgende stappen

Zie [micro soft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)voor de JSON-syntaxis en-eigenschappen van een Load Balancer in een sjabloon.
