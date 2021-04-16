---
title: 'Quickstart: Een Azure DNS-zone en -record maken - Azure PowerShell'
titleSuffix: Azure DNS
description: Informatie over het maken van een DNS-zone en -record in Azure DNS. Dit is een stapsgewijze snelstartgids voor het maken en beheren van uw eerste DNS-zone en -record met behulp van Azure PowerShell.
services: dns
author: rohinkoul
ms.author: rohink
ms.date: 10/20/2020
ms.topic: quickstart
ms.service: dns
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9c457b435ca17ce9dc86e1a83a4d1a56d9623b17
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537871"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-powershell"></a>Snelstart: Een Azure DNS-zone en -record maken met behulp van Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze snelstartgids maakt u uw eerste DNS-zone en -record met behulp van Azure PowerShell. U kunt deze stappen ook uitvoeren met [Azure Portal](dns-getstarted-portal.md) of de [Azure CLI](dns-getstarted-cli.md). 

Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Als u uw domein wilt hosten in Azure DNS, moet u een DNS-zone maken voor die domeinnaam. Alle DNS-records voor uw domein worden vervolgens gemaakt binnen deze DNS-zone. Tot slot moet u de naamservers voor het domein configureren om de DNS-zone te publiceren naar internet. Deze stappen worden hieronder allemaal beschreven.

Azure DNS ondersteunt ook het maken van persoonlijke domeinen. Zie [Aan de slag met privézones in Azure DNS met behulp van PowerShell](private-dns-getstarted-powershell.md) voor stapsgewijze instructies over het maken van uw eerste DNS-privézone en -record.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell lokaal geïnstalleerd of Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>De resourcegroep maken

Voordat u de DNS-zone maakt, maakt u een resourcegroep die de DNS-zone gaat bevatten:

```powershell
New-AzResourceGroup -name MyResourceGroup -location "eastus"
```

## <a name="create-a-dns-zone"></a>Een DNS-zone maken

Een DNS-zone wordt gemaakt met de cmdlet `New-AzDnsZone`. In het volgende voorbeeld wordt een DNS-zone gemaakt met de naam *contoso.xyz* in de resourcegroep *MyResourceGroup*. Gebruik het voorbeeld om een DNS-zone te maken door de waarden te vervangen door uw eigen waarden.

```powershell
New-AzDnsZone -Name contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="create-a-dns-record"></a>Een DNS-record maken

U kunt recordsets maken met behulp van de cmdlet `New-AzDnsRecordSet`. In het volgende voorbeeld wordt een record gemaakt met de relatieve naam 'www' in de DNS-zone 'contoso.xyz' in resourcegroep 'MyResourceGroup'. De volledig gekwalificeerde naam van de recordset is 'www.contoso.xyz'. Het recordtype is 'A', met IP-adres '10.10.10.10' en de TTL is 3600 seconden.

```powershell
New-AzDnsRecordSet -Name www -RecordType A -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -Ttl 3600 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "10.10.10.10")
```

## <a name="view-records"></a>Records weergeven

Als u de DNS-records wilt weergeven in uw zone, gebruikt u:

```powershell
Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup
```

## <a name="test-the-name-resolution"></a>De naamomzetting testen

Nu u een testzone hebt met daarin een DNS-record, kunt u de naamomzetting testen met het hulpprogramma *nslookup*. 

**DNS-naamomzetting testen:**

1. Voer de volgende cmdlet uit om de lijst met naamservers voor uw zone op te halen:

   ```azurepowershell
   Get-AzDnsRecordSet -ZoneName contoso.xyz -ResourceGroupName MyResourceGroup -RecordType ns
   ```

1. Kopieer een van de naamservernamen uit de uitvoer van de vorige stap.

1. Open een opdrachtprompt en voer de volgende opdracht uit:

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   Bijvoorbeeld:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   Er verschijnt een scherm dat er ongeveer als volgt uitziet:

   ![In de schermafbeelding ziet u een opdrachtpromptvenster met een n s lookup-opdracht en waarden voor Server, Adres, Naam en Adres.](media/dns-getstarted-portal/nslookup.PNG)

De hostnaam **www\.contoso.xyz** wordt overeenkomstig uw configuratie omgezet in **10.10.10.10**. Met dit resultaat wordt gecontroleerd of de naamomzetting juist werkt.

## <a name="clean-up-resources"></a>Resources opschonen

Als u ze niet langer nodig hebt, kunt u alle resources die u in deze snelstartgids hebt gemaakt verwijderen door de resourcegroep te verwijderen:

```powershell
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Nu u uw eerste DNS-zone en -record hebt gemaakt met behulp van Azure PowerShell, kunt u records voor een web-app maken in een aangepast domein.

> [!div class="nextstepaction"]
> [DNS-records voor een web-app in een aangepast domein maken](./dns-web-sites-custom-domain.md)
