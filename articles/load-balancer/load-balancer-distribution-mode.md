---
title: Azure Load Balancer distributie modus configureren
titleSuffix: Azure Load Balancer
description: In dit artikel gaat u aan de slag met het configureren van de distributie modus voor Azure Load Balancer om de bron-IP-affiniteit te ondersteunen.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: aa2d2cda59f23ef0b961ccac60b119996423688e
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682313"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>De distributie modus voor Azure Load Balancer configureren

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Distributie modus op basis van hash

De standaarddistributiemodus van Azure Load Balancer is een hash met vijf tuples. 

De tuple bestaat uit het volgende:
* **Bron-IP**
* **Bronpoort**
* **Doel-IP**
* **Doelpoort**
* **Protocol type**

De hash wordt gebruikt om verkeer toe te wijzen aan de beschik bare servers. De algoritme biedt persistentie alleen binnen een transport sessie. Pakketten die zich in dezelfde sessie bevinden, worden omgeleid naar hetzelfde Data Center-IP achter het eind punt met gelijke taak verdeling. Wanneer de client een nieuwe sessie start vanuit hetzelfde bron-IP-adres, wordt de bron poort gewijzigd en wordt het verkeer naar een ander Data Center-eind punt getransporteerd.

![5-tuple hash-gebaseerde distributie modus](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modus voor bron-IP-affiniteit

De load balancer kan ook worden geconfigureerd met behulp van de distributie modus voor de bron-IP-affiniteit. Deze distributiemodus wordt ook wel sessieaffiniteit of client-IP-affiniteit genoemd. In de modus wordt gebruikgemaakt van een twee-tuple (bron-IP en doel-IP) of drie Tuples (bron-IP, doel-IP en type protocol) om verkeer toe te wijzen aan de beschik bare servers. Met behulp van bron-IP-affiniteit gaan verbindingen die vanaf dezelfde client computer worden gestart naar hetzelfde Data Center-eind punt.

In de volgende afbeelding ziet u een configuratie met twee Tuples. U ziet hoe de twee Tuples worden uitgevoerd via de load balancer naar Virtual Machine 1 (VM1). VM1 wordt vervolgens een back-up gemaakt door VM2 en VM3.

![Distributie modus voor twee tuple-sessie affiniteit](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Met de modus voor bron-IP-affiniteit is een incompatibiliteit tussen Azure Load Balancer en Extern bureaublad-gateway (RD-gateway) opgelost. Met deze modus kunt u een RD-gateway-Farm bouwen in één Cloud service.

Een ander use-case-scenario is het uploaden van media. Het uploaden van gegevens gebeurt via UDP, maar het besturings element wordt bereikt via TCP:

* Een client start een TCP-sessie naar het open bare adres met taak verdeling en wordt omgeleid naar een specifieke DIP. Het kanaal blijft actief voor het bewaken van de verbindings status.
* Er wordt een nieuwe UDP-sessie van dezelfde client computer gestart voor hetzelfde open bare eind punt met gelijke taak verdeling. De verbinding wordt omgeleid naar hetzelfde DIP-eind punt als de vorige TCP-verbinding. Het uploaden van media kan worden uitgevoerd met een hoge door Voer terwijl een besturings kanaal wordt onderhouden via TCP.

> [!NOTE]
> Wanneer een set met gelijke taak verdeling wordt gewijzigd door een virtuele machine te verwijderen of toe te voegen, wordt de distributie van client aanvragen opnieuw berekend. U kunt niet afhankelijk zijn van nieuwe verbindingen van bestaande clients om op dezelfde server te eindigen. Bovendien kan het gebruik van de distributie modus voor bron-IP-affiniteit een ongelijke verdeling van verkeer veroorzaken. Clients die worden uitgevoerd achter proxy's, kunnen worden gezien als één unieke client toepassing.

## <a name="configure-source-ip-affinity-settings"></a>Instellingen voor bron-IP-affiniteit configureren

### <a name="azure-portal"></a>Azure Portal

U kunt de configuratie van de distributie modus wijzigen door de taakverdelings regel in de portal te wijzigen.

1. Meld u aan bij de Azure Portal en zoek de resource groep met de load balancer die u wilt wijzigen door te klikken op **resource groepen**.
2. Klik in het scherm overzicht van load balancer onder **instellingen** op **taakverdelings regels** .
3. Klik in het scherm taakverdelings regels op de taakverdelings regel waarvan u de distributie modus wilt wijzigen.
4. Onder de regel wordt de distributie modus gewijzigd door de vervolg keuzelijst **sessie persistentie** te wijzigen.  De volgende opties zijn beschikbaar:
    
    * **Geen (op hash gebaseerd)** : Hiermee geeft u op dat opeenvolgende aanvragen van dezelfde client door elke virtuele machine kunnen worden verwerkt.
    * **Client-IP (bron-IP-affiniteit 2-tuple)** -geeft aan dat opeenvolgende aanvragen van hetzelfde client-IP-adres worden verwerkt door dezelfde virtuele machine.
    * **Client-IP en-protocol (bron-IP-affiniteit 3-tuple)** : Hiermee geeft u op dat opeenvolgende aanvragen van dezelfde combi natie van client-IP-adres en-protocol worden verwerkt door dezelfde virtuele machine.

5. Kies de distributie modus en klik vervolgens op **Opslaan**.

### <a name="azure-powershell"></a>Azure PowerShell

Voor virtuele machines die met Resource Manager zijn geïmplementeerd, gebruikt u Power shell om de distributie-instellingen voor Load Balancer te wijzigen voor een bestaande regel voor taak verdeling. De volgende opdracht werkt de distributie modus bij: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Gebruik Azure PowerShell voor klassieke virtuele machines om de distributie-instellingen te wijzigen. Een Azure-eind punt toevoegen aan een virtuele machine en de load balancer distributie modus configureren:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Stel de waarde van het `LoadBalancerDistribution` element in voor de hoeveelheid taak verdeling vereist. Geef sourceIP op voor de taak verdeling van twee Tuples (bron-IP en doel-IP). Geef sourceIPProtocol op voor de taak verdeling voor drie Tuples (bron-IP, doel-IP en protocol type). Geef geen op voor het standaard gedrag van de taak verdeling van vijf Tuples.

Een eind punt configuratie voor load balancer distributie modus ophalen met behulp van de volgende instellingen:

```azurepowershell
PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
LBSetName : MyLoadBalancedSet
LocalPort : 80
Name : HTTP
Port : 80
Protocol : tcp
Vip : 65.52.xxx.xxx
ProbePath :
ProbePort : 80
ProbeProtocol : tcp
ProbeIntervalInSeconds : 15
ProbeTimeoutInSeconds : 31
EnableDirectServerReturn : False
Acl : {}
InternalLoadBalancerName :
IdleTimeoutInMinutes : 15
LoadBalancerDistribution : sourceIP
```

Wanneer het `LoadBalancerDistribution` element niet aanwezig is, gebruikt Azure Load Balancer het standaard algoritme van vijf Tuples.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Distributie modus configureren voor de eindpuntset met gelijke taak verdeling

Als eind punten deel uitmaken van een eindpuntset met gelijke taak verdeling, moet de distributie modus worden geconfigureerd op de eindpuntset met gelijke taak verdeling:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Distributie modus voor Cloud Services-eind punten configureren

Gebruik de Azure SDK voor .NET 2,5 om uw Cloud service bij te werken. De eindpunt instellingen voor Cloud Services worden gemaakt in het. csdef-bestand. Een upgrade van de implementatie is vereist om de load balancer distributie modus voor een Cloud Services-implementatie bij te werken.

Hier volgt een voor beeld van een csdef-wijziging voor de instellingen van het eind punt:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>API-voor beeld

In het volgende voor beeld ziet u hoe u de load balancer distributie modus opnieuw kunt configureren voor een opgegeven set met gelijke taak verdeling in een implementatie. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Distributie modus wijzigen voor geïmplementeerde set met gelijke taak verdeling

Gebruik het klassieke Azure-implementatie model om een bestaande implementatie configuratie te wijzigen. Voeg de `x-ms-version` header toe en stel de waarde in op versie 2014-09-01 of hoger.

#### <a name="request"></a>Aanvraag

```http
POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
Content-Type: application/xml
```

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
            <Port> port-assigned-to-probe </Port>
            <Protocol> probe-protocol </Protocol>
            <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
            <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

Zoals eerder beschreven, stelt `LoadBalancerDistribution` u het element in op sourceIP voor twee-tuple-affiniteit, sourceIPProtocol voor drie-tuple-affiniteit of geen voor geen affiniteit (5-tuple-affiniteit).

#### <a name="response"></a>Antwoord

```http
HTTP/1.1 202 Accepted
Cache-Control: no-cache
Content-Length: 0
Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
x-ms-servedbyregion: ussouth2
x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
Date: Thu, 16 Oct 2014 22:49:21 GMT
```

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Azure Load Balancer](load-balancer-overview.md)
* [Aan de slag met het configureren van een Internet gerichte load balancer](quickstart-load-balancer-standard-public-powershell.md)
* [TCP-time-outinstellingen voor inactiviteit voor de load balancer configureren](load-balancer-tcp-idle-timeout.md)
