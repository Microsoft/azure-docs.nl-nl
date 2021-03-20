---
title: IP-adres van beheer eindpunt detecteren
titleSuffix: Azure SQL Managed Instance
description: Meer informatie over het ophalen van het open bare IP-eind punt voor het Azure SQL Managed instance Management endpoint en het controleren van de ingebouwde firewall beveiliging
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: a9a2b904bd7526f00a8f8a5d013be0c1e42e38a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91617363"
---
# <a name="determine-the-management-endpoint-ip-address---azure-sql-managed-instance"></a>Het IP-adres van het beheer eindpunt bepalen-Azure SQL Managed instance 
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Het virtuele Azure SQL Managed instance-cluster bevat een beheer eindpunt dat door Azure wordt gebruikt voor beheer bewerkingen. Het beheer eindpunt wordt beveiligd met een ingebouwde firewall op het netwerk niveau en de verificatie op basis van wederzijdse certificaten op toepassings niveau. U kunt het IP-adres van het beheer eindpunt bepalen, maar u hebt geen toegang tot dit eind punt.

Als u het IP-adres van het beheer wilt bepalen, voert u een [DNS-zoek opdracht](/windows-server/administration/windows-commands/nslookup) uit op de FQDN van het SQL-beheerde exemplaar: `mi-name.zone_id.database.windows.net` . Hiermee wordt een DNS-vermelding geretourneerd die er als volgt uitziet `trx.region-a.worker.vnet.database.windows.net` . U kunt vervolgens een DNS-zoek opdracht uitvoeren op deze FQDN met ". vnet" verwijderd. Hiermee wordt het IP-adres van het beheer geretourneerd. 

Deze Power shell-code doet dit allemaal voor u als u vervangt door \<MI FQDN\> de DNS-vermelding van SQL Managed instance: `mi-name.zone_id.database.windows.net` :
  
``` powershell
  $MIFQDN = "<MI FQDN>"
  resolve-dnsname $MIFQDN | select -first 1  | %{ resolve-dnsname $_.NameHost.Replace(".vnet","")}
```

Zie voor meer informatie over SQL Managed instance en Connectivity de [Azure SQL Managed instance connectivity-architectuur](connectivity-architecture-overview.md).
