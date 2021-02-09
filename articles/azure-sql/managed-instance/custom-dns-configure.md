---
title: Aangepaste DNS
titleSuffix: Azure SQL Managed Instance
description: In dit onderwerp worden configuratie opties beschreven voor een aangepaste DNS met Azure SQL Managed instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 07/17/2019
ms.openlocfilehash: a54907dd3f7b3fbc06033624f14b12de14d9afb9
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831498"
---
# <a name="configure-a-custom-dns-for-azure-sql-managed-instance"></a>Een aangepaste DNS configureren voor Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed instance moet worden geïmplementeerd in een [virtueel Azure-netwerk (VNet)](../../virtual-network/virtual-networks-overview.md). Er zijn enkele scenario's (bijvoorbeeld Database Mail, servers die zijn gekoppeld aan andere SQL Server-exemplaren in uw cloud of hybride omgeving) waarvoor de namen van particuliere hosts moeten worden omgezet vanuit een SQL Managed Instance. In dit geval moet u een aangepast DNS configureren in Azure. 

Omdat SQL Managed instance dezelfde DNS gebruikt voor de interne werking, configureert u de aangepaste DNS-server zodat deze open bare domein namen kan omzetten.

> [!IMPORTANT]
> Gebruik altijd een Fully Qualified Domain Name (FQDN) voor de e-mail server, voor het SQL Server exemplaar en voor andere services, zelfs als ze zich in uw privé-DNS-zone bevinden. Gebruik bijvoorbeeld `smtp.contoso.com` voor uw e-mail server omdat deze `smtp` niet correct kan worden omgezet. Als u een gekoppelde server of replicatie wilt maken die verwijst naar SQL Server Vm's binnen hetzelfde virtuele netwerk, hebt u ook een FQDN en een standaard-DNS-achtervoegsel nodig. Bijvoorbeeld `SQLVM.internal.cloudapp.net`. Zie [naam omzetting die gebruikmaakt van uw eigen DNS-server](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)voor meer informatie.

> [!IMPORTANT]
> Het bijwerken van de DNS-servers van het virtuele netwerk heeft geen invloed op het SQL Managed instance. Zie de [instelling DNS-servers voor het virtuele netwerk synchroniseren op het virtuele cluster voor SQL-beheerde exemplaren](synchronize-vnet-dns-servers-setting-on-virtual-cluster.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure SQL Managed instance?](sql-managed-instance-paas-overview.md)voor een overzicht.
- Zie [een beheerd exemplaar maken](instance-create-quickstart.md)voor een zelf studie waarin wordt getoond hoe u een nieuw beheerd exemplaar maakt.
- Zie [vnet-configuratie voor beheerde instanties](connectivity-architecture-overview.md)voor meer informatie over het configureren van een vnet voor een beheerd exemplaar.
