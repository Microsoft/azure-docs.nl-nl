---
title: 'Zelfstudie: Een aliasrecord maken dat naar een resourcerecord in een zone verwijst'
titleSuffix: Azure DNS
description: In deze zelfstudie leert u hoe u een Azure DNS-aliasrecord zo configureert dat deze verwijst naar een resourcerecord in de zone.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: rohink
ms.openlocfilehash: 2b122a34cfd382a58f7680743d3a1cb1ae598fd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76939253"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Zelfstudie: Een aliasrecord maken dat naar een resourcerecord voor een zone verwijst

Aliasrecords kunnen naar andere recordsets van hetzelfde type verwijzen. U kunt bijvoorbeeld een DNS CNAME-recordset hebben als alias voor een andere CNAME-recordset van hetzelfde type. Dit is handig als u wilt dat sommige recordsets zich gedragen als aliassen en andere als niet-aliassen.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een aliasrecord maken voor een resourcerecord in de zone.
> * De aliasrecord testen.


Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
U moet een beschikbare domeinnaam hebben die u in Azure DNS kunt hosten om te testen. U moet het volledige beheer over dit domein hebben. Volledig beheer betekent ook de mogelijkheid om naamserverrecords (NS) voor het domein in te stellen.

Zie voor instructies voor het hosten van uw domein in Azure DNS [Zelfstudie: Uw domein in Azure DNS hosten](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Een aliasrecord maken

Een aliasrecord maken dat verwijst naar een resourcerecord in de zone.

### <a name="create-the-target-resource-record"></a>De doelresourcerecord maken
1. Selecteer uw Azure DNS-zone om de zone te openen.
2. Selecteer **Recordset**.
3. Typ in het tekstvak **Naam****server**.
4. Selecteer voor het **Type** de optie **A**.
5. Typ in het tekstvak **IP-ADRES****10.10.10.10**.
6. Selecteer **OK**.

### <a name="create-the-alias-record"></a>De aliasrecord maken
1. Selecteer uw Azure DNS-zone om de zone te openen.
2. Selecteer **Recordset**.
3. Typ in het tekstvak **Naam****test**.
4. Selecteer voor het **Type** de optie **A**.
5. Selecteer **Ja** bij het selectievakje **Aliasrecordset**. Selecteer vervolgens de optie **Zonerecordset**.
6. Selecteer voor de **Zonerecordset** de record **server**.
7. Selecteer **OK**.

## <a name="test-the-alias-record"></a>Het aliasrecord testen

1. Start uw favoriete nslookup-hulpprogramma. U kunt ook naar [https://network-tools.com/nslook](https://network-tools.com/nslook) bladeren.
2. Stel het querytype voor A-records in en zoek **test.\<your domain name\>** op. Het antwoord is **10.10.10.10**.
3. Wijzig de A-record van de **server** in de Azure Portal naar **10.11.11.11**.
4. Wacht een paar minuten en gebruik nslookup daarna opnieuw voor de **test**-record. Het antwoord is **10.11.11.11**.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcerecords **server** en **test** in uw zone als u de resources die u in deze zelfstudie hebt gemaakt, niet meer nodig hebt.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een aliasrecord maakt om naar een resourcerecord in de zone te verwijzen. Als u meer wilt weten over Azure DNS en web-apps gaat u verder met de zelfstudie voor web-apps.

> [!div class="nextstepaction"]
> [DNS-records voor een web-app in een aangepast domein maken](./dns-web-sites-custom-domain.md)
