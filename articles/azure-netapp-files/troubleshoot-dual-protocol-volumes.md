---
title: Problemen met Dual-protocol volumes voor Azure NetApp Files oplossen | Microsoft Docs
description: Hierin worden fout berichten en oplossingen beschreven die u kunnen helpen bij het oplossen van problemen met dubbele protocollen voor Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/22/2021
ms.author: b-juche
ms.openlocfilehash: fb4233a87231dddb1e3cb2777ac2ef53a61f833e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726612"
---
# <a name="troubleshoot-dual-protocol-volumes"></a>Problemen met volumes van dubbele protocollen oplossen

In dit artikel worden oplossingen beschreven voor fout situaties die u mogelijk hebt tijdens het maken of beheren van Dual-protocol volumes.

## <a name="error-conditions-and-resolutions"></a>Fout voorwaarden en oplossingen

|     Fout voorwaarden    |     Oplossing    |
|-|-|
| LDAP via TLS is ingeschakeld en het maken van een Dual-protocol volume mislukt met de fout `This Active Directory has no Server root CA Certificate` .    |     Als deze fout optreedt wanneer u een Dual-protocol volume maakt, moet u ervoor zorgen dat het basis-CA-certificaat is geüpload in uw NetApp-account.    |
| Het maken van het volume met twee protocollen mislukt met de fout `Failed to validate LDAP configuration, try again after correcting LDAP configuration` .    |  De PTR-record (pointer) van de computer van de AD-host ontbreekt mogelijk op de DNS-server. U moet een zone voor reverse lookup maken op de DNS-server en vervolgens een PTR-record van de AD-hostcomputer toevoegen in de zone voor reverse lookup. <br> Stel dat het IP-adres van de AD-machine is `1.1.1.1` , de hostnaam van de AD-machine (zoals gevonden met behulp van de `hostname` opdracht) `AD1` en de domein naam `contoso.com` .  De PTR-record die is toegevoegd aan de zone voor reverse lookup moet zijn `1.1.1.1`  ->  `contoso.com` .   |
| Het maken van het volume met twee protocollen mislukt met de fout `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 1.1.1.1, port 88 using TCP\\n**[ 950] FAILURE` . |  Deze fout geeft aan dat het AD-wacht woord onjuist is wanneer Active Directory is gekoppeld aan het NetApp-account. Werk de AD-verbinding bij met het juiste wacht woord en probeer het opnieuw. |
| Het maken van het volume met twee protocollen mislukt met de fout `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` . |   Deze fout geeft aan dat DNS niet bereikbaar is. De reden hiervoor is dat de DNS-IP onjuist is of dat er een probleem is met het netwerk. Controleer het DNS-IP-adres dat is ingevoerd in de AD-verbinding en controleer of het IP-adres juist is. <br> Zorg er ook voor dat de AD en het volume zich in dezelfde regio bevinden en in hetzelfde VNet. Als ze zich in verschillende VNETs bevinden, moet u ervoor zorgen dat VNet-peering tot stand is gebracht tussen de twee VNets.|
| De machtiging wordt geweigerd bij het koppelen van een Dual-protocol volume. | Een volume met dubbele protocollen ondersteunt zowel de NFS-als de SMB-protocollen.  Wanneer u probeert toegang te krijgen tot het gekoppelde volume op het UNIX-systeem, probeert het systeem de UNIX-gebruiker toe te wijzen die u gebruikt voor een Windows-gebruiker. Als er geen toewijzing wordt gevonden, treedt de fout ' permission denied ' op. <br> Deze situatie is ook van toepassing wanneer u de gebruiker ' root ' gebruikt voor de toegang. <br> Om het probleem ' toestemming geweigerd ' te vermijden, moet u ervoor zorgen dat Windows Active Directory bevat `pcuser` voordat u toegang krijgt tot het koppel punt. Als u toevoegt `pcuser` nadat het probleem ' toestemming geweigerd ' is aangetroffen, wacht u 24 uur totdat de cache vermelding is gewist voordat u de toegang opnieuw probeert. |

## <a name="next-steps"></a>Volgende stappen  

* [Een volume met dubbele protocollen maken](create-volumes-dual-protocol.md)
* [Een NFS-client voor Azure NetApp Files configureren](configure-nfs-clients.md)
