---
title: 'Quickstart: Een privé-DNS-zone in Azure maken met behulp van de Azure-portal'
description: In deze quickstart maakt en test u een privé-DNS-zone en -record in Azure DNS. Dit is een stapsgewijze handleiding voor het maken en beheren van uw eerste privé-DNS-zone en -record met behulp van de Azure-portal.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- mode-portal
ms.openlocfilehash: 2115062db8615ca782cb987903e5ebfc83400cd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535452"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Snelstart: Een privé-DNS-zone in Azure maken met behulp van de Azure-portal

In deze quickstart wordt stapsgewijs beschreven hoe u uw eerste privé-DNS-zone en -record maakt met behulp van de Azure-portal.

Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Als u uw domein wilt hosten in Azure DNS, moet u een DNS-zone maken voor die domeinnaam. Alle DNS-records voor uw domein worden vervolgens gemaakt binnen deze DNS-zone. Als u een privé-DNS-zone wilt publiceren naar uw virtuele netwerk, geeft u de lijst met virtuele netwerken op die records in de zone mogen omzetten.  Deze worden *gekoppelde* virtuele netwerken genoemd. Als automatische registratie is ingeschakeld, werkt Azure DNS ook de zonerecords bij wanneer er een virtuele machine wordt gemaakt, het IP-adres ervan wordt gewijzigd of deze wordt verwijderd.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

U kunt deze quickstart desgewenst voltooien met behulp van [Azure PowerShell](private-dns-getstarted-powershell.md) of [Azure CLI](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Een privé-DNS-zone maken

In het volgende voorbeeld maakt u een DNS-zone met de naam **private.contoso.com** in de resourcegroep **MyAzureResourceGroup**.

Een DNS-zone bevat de DNS-vermeldingen voor een domein. Als u uw domein wilt hosten in Azure DNS, maakt u een DNS-zone voor deze domeinnaam.

![Zoekopdracht voor privé-DNS-zones](media/private-dns-portal/search-private-dns.png)

1. Typ in de zoekbalk van de portal in het zoektekstvak: **private dns zones**. En druk op **Enter**.
1. Selecteer **Privé-DNS-zone**.
2. Selecteer **Privé-DNS-zone maken**.

1. Typ of selecteer op de pagina **Privé-DNS-zone maken** de volgende waarden:

   - **Resourcegroep**: Selecteer **Nieuwe maken**, voer *MyAzureResourceGroup* in en selecteer **OK**. De naam van de resourcegroep moet uniek zijn binnen het Azure-abonnement.
   -  **Naam**: Typ voor dit voorbeeld: *private.contoso.com*.
1. Selecteer voor **Locatie van resourcegroep** de optie **VS - west-centraal**.

1. Selecteer **Controleren + maken**.

1. Selecteer **Maken**.

Het maken van de zone kan een paar minuten duren.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie moet u de volgende parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (selecteer bestaande resourcegroep) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | VS - west-centraal      |
| **\<IPv4-address-space>**   | 10.2.0.0\16          |
| **\<subnet-name>**          | MyAzureSubnet        |
| **\<subnet-address-range>** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Het virtuele netwerk koppelen

U maakt een koppeling met een virtueel netwerk om de privé-DNS-zone aan een virtueel netwerk te koppelen.

![Virtuele netwerkkoppeling toevoegen](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Open de resourcegroep **MyAzureResourceGroup** en selecteer de privézone **private.contoso.com**.
2. Selecteer in het linkerdeelvenster de optie **Virtuele netwerkkoppelingen**.
3. Selecteer **Toevoegen**.
4. Typ bij **Naam van koppeling**: **myLink**.
5. Selecteer voor **Virtuele netwerkkoppelingen** de optie **myVNet**.
6. Schakel het selectievakje **Automatische registratie inschakelen** in.
7. Selecteer **OK**.

## <a name="create-the-test-virtual-machines"></a>De virtuele testmachines maken

Maak nu twee virtuele machines, zodat u uw DNS-privézone kunt testen:

1. Selecteer linksboven op de portalpagina de optie **Een resource maken**, en selecteer vervolgens **Windows Server 2016 Datacenter**.
1. Selecteer **MyAzureResourceGroup** als de resourcegroep.
1. Typ als naam van de virtuele machine: **myVM01**.
1. Selecteer **VS - west-centraal** als de **Regio**.
1. Voer een naam in voor de gebruikersnaam van de beheerder.
2. Voer een wachtwoord in en bevestig het wachtwoord.
5. Selecteer voor **Openbare binnenkomende poorten** de optie **Geselecteerde poorten toestaan**. Selecteer vervolgens voor **Geselecteerde binnenkomende poorten** de optie **RDP (3389)** .
10. Accepteer de andere standaardinstellingen voor de pagina, en klik vervolgens op **Volgende: Schijven >** .
11. Accepteer de andere standaardinstellingen op de pagina **Schijven**, en klik vervolgens op **Volgende: Netwerken >** .
1. Zorg ervoor dat **myAzureVNet** is geselecteerd voor het virtuele netwerk.
1. Accepteer de andere standaardinstellingen voor de pagina, en klik vervolgens op **Volgende: Beheer >** .
2. Selecteer **Uit** voor **Diagnostische gegevens over opstarten**, accepteer de andere standaardinstellingen, en selecteer vervolgens **Controleren en maken**.
1. Controleer de instellingen en klik op **Maken**.

Herhaal deze stappen om nog een virtuele machine te maken, genaamd **myVM02**.

Het duurt enkele minuten voordat beide virtuele machines zijn voltooid.

## <a name="create-an-additional-dns-record"></a>Een extra DNS-record maken

 In het volgende voorbeeld wordt een record gemaakt met de relatieve naam **db** in de DNS-zone **private.contoso.com** in de resourcegroep **MyAzureResourceGroup**. De volledig gekwalificeerde naam van de recordset is **db.private.contoso.com**. Het recordtype is ‘A’, met het IP-adres **myVM01**.

1. Open de resourcegroep **MyAzureResourceGroup** en selecteer de privézone **private.contoso.com**.
2. Selecteer **+ Recordset**.
3. Typ bij **Naam**: **db**.
4. Typ voor **IP-adres** het IP-adres dat u ziet voor **myVM01**. Dit wordt automatisch geregistreerd wanneer de virtuele machine wordt opgestart.
5. Selecteer **OK**.

## <a name="test-the-private-zone"></a>De privézone testen

U kunt nu de naamomzetting voor uw privézone **private.contoso.com** testen.

### <a name="configure-vms-to-allow-inbound-icmp"></a>VM’s configureren voor het toestaan van inkomende ICMP

U kunt de pingopdracht gebruiken voor het testen van naamomzetting. Configureer de firewall dus op beide virtuele machines om inkomende ICMP-pakketten toe te staan.

1. Maak verbinding met myVM01 en open een Windows PowerShell-venster met beheerdersbevoegdheden.
2. Voer de volgende opdracht uit:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Herhaal voor myVM02.

### <a name="ping-the-vms-by-name"></a>De VM's pingen op naam

1. Ping vanuit de myVM02 Windows PowerShell-opdrachtprompt myVM01 met de naam van de automatisch geregistreerde hostnaam:
   ```
   ping myVM01.private.contoso.com
   ```
   De uitvoer ziet er ongeveer als volgt uit:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Ping nu de **db**-naam die u eerder hebt gemaakt:
   ```
   ping db.private.contoso.com
   ```
   De uitvoer ziet er ongeveer als volgt uit:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep **MyAzureResourceGroup**, als u deze niet meer nodig hebt om de resources die u in deze quickstart hebt gemaakt te verwijderen.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Scenario's voor privé-DNS-zones in Azure](private-dns-scenarios.md)
