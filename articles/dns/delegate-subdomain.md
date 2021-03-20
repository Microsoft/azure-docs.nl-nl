---
title: Een subdomein-Azure DNS delegeren
description: Met dit leer traject kunt u aan de slag gaan met het delegeren van een Azure DNS subdomein.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 2/7/2019
ms.author: rohink
ms.openlocfilehash: add7674771fd19f6029a94c46624006f0cf30f1a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011537"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Een Azure DNS subdomein delegeren

U kunt de Azure Portal gebruiken om een DNS-subdomein te delegeren. Als u bijvoorbeeld eigenaar bent van het domein contoso.com, kunt u een subdomein met de naam *engineering* delegeren naar een andere, afzonderlijke zone die u afzonderlijk kunt beheren vanuit de zone contoso.com.

Als u wilt, kunt u een subdomein delegeren met behulp van [Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Vereisten

Als u een Azure DNS subdomein wilt delegeren, moet u uw open bare domein eerst delegeren naar Azure DNS. Zie [een domein delegeren voor Azure DNS](./dns-delegate-domain-azure-dns.md) voor instructies over het configureren van uw naam servers voor delegering. Zodra uw domein is overgedragen aan uw Azure DNS zone, kunt u uw subdomein delegeren.

> [!NOTE]
> Contoso.com wordt in dit artikel als voor beeld gebruikt. Vervang uw eigen domeinnaam door contoso.com.

## <a name="create-a-zone-for-your-subdomain"></a>Een zone maken voor uw subdomein

Maak eerst de zone voor het **technische** subdomein.

1. Selecteer in de Azure Portal **een resource maken**.
2. Typ **DNS** in het zoekvak en selecteer **DNS-zone**.
3. Selecteer **Maken**.
4. Typ **engineering.contoso.com** in het tekstvak **naam** in het deel venster **DNS-zone maken** .
5. Selecteer de resource groep voor uw zone. Mogelijk wilt u dezelfde resource groep gebruiken als de bovenliggende zone om Vergelijk bare resources samen te brengen.
6. Klik op **Create**.
7. Nadat de implementatie is voltooid, gaat u naar de nieuwe zone.

## <a name="note-the-name-servers"></a>Noteer de naam servers

Noteer vervolgens de vier naam servers voor het subdomein van de technische afdeling.

Noteer in het deel venster **engineering** zone de vier naam servers voor de zone. U zult deze naam servers later gebruiken.

## <a name="create-a-test-record"></a>Een test record maken

Maak een **A** -record die moet worden gebruikt voor het testen. U kunt bijvoorbeeld een **www** a-record maken en deze configureren met een **10.10.10.10** IP-adres.

## <a name="create-an-ns-record"></a>Een NS-record maken

Maak vervolgens een NS-record (naam server) voor de **technische** zone.

1. Navigeer naar de zone voor het bovenliggende domein.
2. Selecteer **+ Recordset**.
3. Typ in het deel venster **recordset toevoegen** de tekst **engineering** in het tekstvak **naam** .
4. Selecteer bij **type** de optie **NS**.
5. Voer onder **naam server** de vier naam servers in die u eerder hebt vastgelegd in de **technische** zone.
6. Klik op **OK**.

## <a name="test-the-delegation"></a>De delegering testen

Gebruik Nslookup om de overdracht te testen.

1. Open een Powershell-venster.
2. Typ bij opdracht prompt `nslookup www.engineering.contoso.com.`
3. U ontvangt een niet-bindend antwoord met het adres **10.10.10.10**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [configureren van omgekeerde DNS voor services die worden gehost in azure](dns-reverse-dns-for-azure-services.md).