---
title: 'Zelfstudie: Pakketopname uitvoeren Azure Virtual WAN site-naar-site-verbindingen'
description: In deze zelfstudie leert u hoe u pakketopnames kunt uitvoeren op Virtual WAN site-naar-site-VPN Gateway.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: dbe7e06484797063ed4122ee3fdde625dc66c41f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107879147"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Pakketopname uitvoeren op Azure Virtual WAN site-naar-site-VPN Gateway 

Connectiviteits- en prestatieproblemen zijn vaak complex. Het kan veel tijd en moeite kosten om de oorzaak van het probleem te beperken. Pakketopname kan u helpen het bereik van een probleem te beperken tot bepaalde delen van het netwerk. Het kan u helpen te bepalen of het probleem zich aan de klantzijde van het netwerk, de Azure-zijde van het netwerk of ergens ertussenin voordeed. Nadat u het probleem hebt beperkt, is het efficiënter om fouten op te sporen en herstel te ondernemen.

In het volgende artikel wordt beschreven hoe u een pakketopname start en stopt op Azure Virtual WAN site-naar-site-VPN Gateway. Deze functionaliteit is momenteel alleen beschikbaar via PowerShell.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt gebruiken, moet u de volgende configuratie al hebben ingesteld in uw omgeving:

* Een Virtual WAN, virtuele hub en een site-naar-site-VPN Gateway geïmplementeerd in de virtuele hub. Mogelijk hebt u ook verbindingen die VPN-sites verbinden met uw site-naar-site-VPN Gateway.


### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>De omgeving instellen

Zorg ervoor dat u zich in de juiste abonnementscontext hebt door het volgende script uit te voeren in PowerShell. Dit zorgt ervoor dat u bent aangemeld bij een gebruiker die machtigingen heeft om het pakket vast te leggen op de site-naar-site-VPN Gateway.

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> Een opslagaccount maken

U moet een opslagaccount maken onder uw Azure-abonnement om de resultaten van uw pakketopname op te slaan. Raadpleeg dit [document voor](.././storage/common/storage-account-create.md) instructies over het maken van een opslagaccount.

Nadat u uw account hebt aangemaakt, voert u de volgende opdrachten uit om een SAS-URL (Shared Access Signature) te genereren. De resultaten van uw pakketopname worden opgeslagen via deze URL.
   ```azurepowershell-interactive
  $rgname = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storeNAme
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzureStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
   ```

## <a name="start-the-packet-capture"></a>De pakketopname starten

Als u de pakketopname wilt starten, hebt u twee opties: het vastleggen van pakketten op één VPN-verbinding of op de hele site-naar-site-VPN Gateway. Houd er rekening mee dat als u ervoor kiest om vast te leggen op VPN-verbindingen, u alleen captures op 6 verbindingen tegelijk kunt uitvoeren.

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>Pakketopname op een site-naar-site-VPN Gateway (alle verbindingen)

Voer de volgende opdrachten uit. De naam van de site-naar-site-VPN Gateway kunt u vinden door naar uw virtuele hub te gaan en te klikken op VPN (site-naar-site) onder Connectiviteit.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="Afbeelding van Virtual WAN gatewaynaam." lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>Pakketopname van specifieke site-naar-site-VPN-verbindingen

>[!NOTE]
> Houd er rekening mee dat u slechts een pakketopname kunt uitvoeren op vijf VPN-verbindingen tegelijk.


Voer de volgende opdrachten uit. De naam van de site-naar-site-VPN-verbindingen vindt u door naar uw virtuele hub te gaan en te klikken op VPN (site-naar-site) onder Connectiviteit. Navigeer vervolgens naar de VPN-site waar u de pakketopname wilt uitvoeren en klik op de drie puntjes aan de rechterkant. Klik **op VPN-verbinding** bewerken in het menu dat wordt weergegeven.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="Afbeelding van het vinden van namen van VPN-verbindingen." lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

De naam van de koppelingen die zijn verbonden met een specifieke VPN-site vindt u door te klikken op de VPN-site uit de vorige sectie en naar de tabel **Koppelingen te** kijken. 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="Afbeelding van hoe u de naam van de VPN-koppeling kunt vinden." lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">” -Sasurl $sasurl 
   ```

## <a name="optional-specifying-filters"></a>Optioneel: Filters opgeven

Er zijn enkele algemeen beschikbare hulpprogramma's voor pakketopname. Het verkrijgen van relevante pakketopnamen met deze hulpprogramma's kan omslachtig zijn, met name in scenario's met veel verkeer. Ter vereenvoudiging van uw pakketopnamen kunt u filters voor uw pakketopname opgeven om u te richten op specifiek gedrag. Hieronder vindt u de beschikbare parameters:

>[!NOTE]
> Voor TracingFlags en TCPFlags kunt u meerdere protocollen opgeven door de numerieke waarden op te voegen voor de protocollen die u wilt vastleggen (hetzelfde als een logische OR). Als u bijvoorbeeld alleen ESP- en OPVN-pakketten wilt vastleggen, geeft u een TracingFlag-waarde op van 8 +1 = 9.  

| Parameter | Beschrijving | Standaardwaarden | Beschikbare waarden|
|--- |--- | --- | ---|
| TracingFlags | Geheel getal dat bepaalt welke typen pakketten worden vastgelegd | 11 (ESP, IKE, OVPN) | ESP = 1 IKE = 2 OPVN = 8 |
| TCPFlags | Geheel getal dat bepaalt welke typen TCP-pakketten worden vastgelegd | 0 (geen) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16,TUR = 32, ECE = 64, CWR = 128| 
| MaxPacketBufferSize|Maximale grootte van een vastgelegd pakket in bytes. Pakketten worden afgekapt als ze groter zijn dan de opgegeven waarde. |120|Alle|
| MaxFileSize |Maximale grootte van het vastleggen van bestanden in Mb. Vastleggen worden opgeslagen in een cirkelvormige buffer, zodat overloop wordt verwerkt op een FIFO-manier (oudere pakketten worden eerst verwijderd)|100|Alle|
|SourceSubnets|Pakketten uit de opgegeven CIDR-reeksen worden vastgelegd. Opgegeven als een matrix.|[] (alle IPv4-adressen)|Matrix van door komma's gescheiden IPV4-subnetten|
| DestinationSubnets |Pakketten die bestemd zijn voor de opgegeven CIDR-reeksen worden vastgelegd. Opgegeven als een matrix. |[] (alle IPv4-adressen)|Matrix van door komma's gescheiden IPV4-subnetten|
|SourcePort |Pakketten met de bron in de opgegeven reeksen worden vastgelegd. Opgegeven als een matrix.|[] (alle poorten)|Matrix van door komma's gescheiden poorten|
|DestinationPort |Pakketten met een bestemming in de opgegeven bereiken worden vastgelegd. Opgegeven als een matrix.|[] (alle poorten)|Matrix van door komma's gescheiden poorten|
| CaptureSingleDirectionTrafficOnly |Indien waar, wordt slechts één richting van een bidirectionele stroom in de pakketopname weer geven. Hiermee worden alle mogelijke combinatie van IP en poorten vast leggen.|Waar|Waar, Onwaar|
|Protocol|Een matrix met gehele getallen die overeenkomen met IANA-protocollen. |[] (alle protocollen)| Alle protocollen die hier worden [gevonden](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) |

Hieronder vindt u een voorbeeld van pakketopname met behulp van een filterreeks. U kunt de parameters aanpassen aan uw behoeften op basis van de voorgaande tabel.  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -Sasurl $sasurl -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>Het vastleggen van pakketten stoppen
Het wordt aanbevolen dat u de pakketopname ten minste 600 seconden laat uitvoeren. Vanwege synchronisatieproblemen tussen meerdere onderdelen op het pad, bieden kortere pakketopnamen mogelijk geen volledige gegevens. Wanneer u klaar bent om het vastleggen van pakketten te stoppen, moet u de volgende opdracht uitvoeren.

De parameters zijn vergelijkbaar met de parameters in de sectie Een pakketopname starten. De SAS-URL is gegenereerd in [de sectie Een opslagaccount](#createstorage) maken.

### <a name="gateway-level-packet-capture"></a>Pakketopname op gatewayniveau

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sas
   ```

### <a name="connection-level-packet-captures"></a>Pakketopnamen op verbindingsniveau

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sas
   ```

## <a name="viewing-your-packet-capture"></a>Uw pakketopname weergeven

Navigeer Azure Portal naar het opslagaccount dat is gemaakt in 'Een opslagaccount maken' klik op uw container en download het bestand. Gegevensbestanden voor pakketopname worden gegenereerd in PCAP-indeling. U kunt Wireshark of een andere algemeen beschikbare toepassing gebruiken om PCAP-bestanden te openen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Virtual WAN:

> [!div class="nextstepaction"]
> * [Veelgestelde vragen over Virtual WAN](virtual-wan-faq.md)