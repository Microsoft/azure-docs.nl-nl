---
title: Een domein zone bestand importeren en exporteren voor Azure private DNS-Azure CLI
titleSuffix: Azure DNS
description: Meer informatie over het importeren en exporteren van een DNS-zone bestand naar Azure private DNS met behulp van Azure CLI
services: dns
author: duongau
ms.service: dns
ms.date: 03/16/2021
ms.author: duau
ms.topic: how-to
ms.openlocfilehash: 2e5babb85dbf4aa7da707e22be5eeaf3ae89972d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450988"
---
# <a name="import-and-export-a-private-dns-zone-file-for-azure-private-dns"></a>Een privé-DNS-zone bestand importeren en exporteren voor Azure private DNS

Dit artikel begeleidt u bij het importeren en exporteren van DNS-zone bestanden voor Azure DNS met behulp van de Azure CLI.

## <a name="introduction-to-dns-zone-migration"></a>Inleiding tot de migratie van een DNS-zone

Een DNS-zone bestand is een tekst bestand met details van elke Domain Name System (DNS)-record in de zone. Het volgt een standaard indeling, waardoor het geschikt is voor het overbrengen van DNS-records tussen DNS-systemen. Het gebruik van een zone bestand is een snelle, betrouw bare en gemakkelijke manier om een DNS-zone over te brengen naar of van Azure DNS.

Azure private DNS ondersteunt het importeren en exporteren van zone bestanden met behulp van de Azure-opdracht regel interface (CLI). Het importeren van zone bestanden wordt momenteel **niet** ondersteund via Azure PowerShell of de Azure Portal.

De Azure CLI is een platformoverschrijdende opdracht regel programma dat wordt gebruikt voor het beheren van Azure-Services. Het is beschikbaar voor de Windows-, Mac-en Linux-platformen op de [pagina Azure-down loads](https://azure.microsoft.com/downloads/). Ondersteuning voor meerdere platforms is belang rijk voor het importeren en exporteren van zone bestanden, omdat de meest voorkomende naam server software, [BIND](https://www.isc.org/downloads/bind/), doorgaans in Linux wordt uitgevoerd.

## <a name="obtain-your-existing-dns-zone-file"></a>Uw bestaande DNS-zone bestand ophalen

Voordat u een DNS-zone bestand importeert in Azure DNS, moet u een kopie van het zone bestand verkrijgen. De bron van dit bestand is afhankelijk van waar de DNS-zone momenteel wordt gehost.

* Als uw DNS-zone wordt gehost door een partner service (zoals een domein registratie, een toegewezen DNS-hosting provider of een alternatieve Cloud provider), moet die service de mogelijkheid bieden om het DNS-zone bestand te downloaden.
* Als uw DNS-zone wordt gehost op Windows DNS, is de standaardmap voor de zone bestanden **%systemroot%\System32\Dns**. Het volledige pad naar elk zone bestand wordt ook weer gegeven op het tabblad **Algemeen** van de DNS-console.
* Als uw DNS-zone wordt gehost met BIND, wordt de locatie van het zone bestand voor elke zone opgegeven in het BIND-configuratie bestand met de **naam. conf**.

## <a name="import-a-dns-zone-file-into-azure-private-dns"></a>Een DNS-zone bestand importeren in azure private DNS

Als u een zone bestand importeert, wordt er een nieuwe zone in azure private DNS gemaakt als deze nog niet bestaat. Als de zone al bestaat, moeten de record sets in het zone bestand worden samengevoegd met de bestaande record sets.

### <a name="merge-behavior"></a>Gedrag samen voegen

* Standaard worden bestaande en nieuwe record sets samengevoegd. Identieke records in een samengevoegde recordset worden ontdubbeld.
* Wanneer record sets worden samengevoegd, wordt de TTL (time to Live) van bestaande record sets gebruikt.
* SOA-para meters (start of Authority) (met uitzonde ring van `host` ) worden altijd opgehaald uit het geïmporteerde zone bestand. Voor de naam server record die is ingesteld op de zone Apex, wordt de TTL altijd opgehaald uit het geïmporteerde zone bestand.
* Een geïmporteerde CNAME-record vervangt geen bestaande CNAME-record met dezelfde naam.  
* Als er een conflict optreedt tussen een CNAME-record en een andere record met dezelfde naam, maar een ander type (ongeacht de bestaande of nieuwe), blijft de bestaande record behouden. 

### <a name="additional-information-about-importing"></a>Aanvullende informatie over importeren

De volgende opmerkingen bevatten aanvullende technische details over het importeren van de zone.

* De `$TTL` instructie is optioneel en wordt ondersteund. Als er geen `$TTL` instructie wordt gegeven, worden records zonder expliciete TTL geïmporteerd in een standaard-TTL van 3600 seconden. Wanneer twee records in dezelfde recordset verschillende TTLs opgeven, wordt de lagere waarde gebruikt.
* De `$ORIGIN` instructie is optioneel en wordt ondersteund. Als er geen `$ORIGIN` is ingesteld, wordt de gebruikte standaard waarde de zone naam die is opgegeven op de opdracht regel (plus het afsluitende '. ').
* De `$INCLUDE` and- `$GENERATE` instructies worden niet ondersteund.
* Deze record typen worden ondersteund: A, AAAA, CAA, CNAME, NZ, NS, SOA, SRV en TXT.
* De SOA-record wordt automatisch gemaakt door Azure DNS wanneer er een zone wordt gemaakt. Wanneer u een zone bestand importeert, worden alle SOA-para meters uit het zone bestand gehaald, *met uitzonde ring* van de `host` para meter. Deze para meter gebruikt de waarde die wordt verschaft door Azure DNS. Dit komt omdat deze para meter moet verwijzen naar de primaire naam server van Azure DNS.
* De naam server record die is ingesteld op de zone Apex wordt ook automatisch gemaakt door Azure DNS wanneer de zone wordt gemaakt. Alleen de TTL van deze recordset wordt geïmporteerd. Deze records bevatten de naam server namen die worden verschaft door Azure DNS. De record gegevens worden niet overschreven door de waarden die zijn opgenomen in het geïmporteerde zone bestand.
* Tijdens de open bare preview ondersteunt Azure DNS alleen TXT-records met één teken reeks. TXT-records met een teken reeks worden samengevoegd tot 255 tekens.

### <a name="cli-format-and-values"></a>CLI-indeling en-waarden

De indeling van de Azure CLI-opdracht voor het importeren van een DNS-zone is:

```azurecli
az network private-dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Waarden:

* `<resource group>` is de naam van de resource groep voor de zone in Azure DNS.
* `<zone name>` is de naam van de zone.
* `<zone file name>` is het pad of de naam van het zone bestand dat moet worden geïmporteerd.

Als er geen zone met deze naam bestaat in de resource groep, wordt deze voor u gemaakt. Als de zone al bestaat, worden de geïmporteerde record sets samengevoegd met bestaande record sets.

### <a name="import-a-zone-file"></a>Een zone bestand importeren

Een zone bestand voor de zone **contoso.com** importeren.

1. Als u er nog geen hebt, moet u een resource manager-resource groep maken.

    ```azurecli
    az group create --resource-group myresourcegroup -l westeurope
    ```

2. Als u de zone **contoso.com** uit het bestand **contoso.com.txt** wilt importeren in een nieuwe DNS-zone in de resource groep **myresourcegroup**, voert u de opdracht uit `az network private-dns zone import` .<BR>Met deze opdracht wordt het zone bestand geladen en geparseerd. De opdracht voert een reeks opdrachten uit op de Azure DNS-service om de zone en alle record sets in de zone te maken. De opdracht rapporteert de voortgang in het console venster, samen met eventuele fouten of waarschuwingen. Omdat record sets in de reeks worden gemaakt, kan het enkele minuten duren voordat een groot zone bestand is geïmporteerd.

    ```azurecli
    az network private-dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="verify-the-zone"></a>De zone controleren

Als u de DNS-zone wilt controleren nadat u het bestand hebt geïmporteerd, kunt u een van de volgende methoden gebruiken:

* U kunt de records weer geven met behulp van de volgende Azure CLI-opdracht:

    ```azurecli
    az network private-dns record-set list -g myresourcegroup -z contoso.com
    ```

* U kunt gebruiken `nslookup` om de naam omzetting voor de records te controleren. Omdat de zone nog niet is gedelegeerd, moet u de juiste Azure DNS naam servers expliciet opgeven. In het volgende voor beeld ziet u hoe de naam server namen worden opgehaald die aan de zone zijn toegewezen. U ziet ook hoe u een query kunt uitvoeren op de www-record met behulp van `nslookup` .

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Een DNS-zone bestand exporteren van Azure DNS

De indeling van de Azure CLI-opdracht voor het exporteren van een DNS-zone is:

```azurecli
az network private-dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Waarden:

* `<resource group>` is de naam van de resource groep voor de zone in Azure DNS.
* `<zone name>` is de naam van de zone.
* `<zone file name>` is het pad of de naam van het zone bestand dat moet worden geëxporteerd.

Net als bij het importeren van de zone moet u zich eerst aanmelden, uw abonnement kiezen en de Azure CLI configureren voor het gebruik van de Resource Manager-modus.

### <a name="to-export-a-zone-file"></a>Een zone bestand exporteren

Als u de bestaande Azure DNS zone **contoso.com** in de resource groep **myresourcegroup** wilt exporteren naar het bestand **contoso.com.txt** (in de huidige map), voert u uit `azure network private-dns zone export` . Met deze opdracht wordt de Azure DNS-service aangeroepen om record sets in de zone op te sommen en de resultaten te exporteren naar een zone bestand dat met BIND compatibel is.

```azurecli
az network private-dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [beheren van record sets en records](./private-dns-getstarted-cli.md) in uw DNS-zone.
