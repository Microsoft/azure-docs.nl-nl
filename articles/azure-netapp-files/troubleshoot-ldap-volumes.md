---
title: Problemen met LDAP-volumes oplossen voor Azure NetApp Files | Microsoft Docs
description: Hierin worden oplossingen beschreven voor fout condities die u mogelijk hebt bij het configureren van LDAP-volumes voor Azure NetApp Files.
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
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: eea3f691bc6d91948dc73b4a02c89abfac12d384
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498988"
---
# <a name="troubleshoot-ldap-volume-issues"></a>Problemen met LDAP-volumes oplossen

In dit artikel worden oplossingen beschreven voor fout situaties die u mogelijk hebt tijdens het configureren van LDAP-volumes.

## <a name="errors-and-resolutions-for-ldap-volumes"></a>Fouten en oplossingen voor LDAP-volumes

|     Fout voorwaarden    |     Oplossingen    |
|-|-|
| Fout bij het maken van een SMB-volume met ldapEnabled als waar: <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | U kunt geen SMB-volume maken waarop LDAP is ingeschakeld. <br> Maak SMB-volumes met LDAP uitgeschakeld. |
| Fout bij het bijwerken van de waarde van de ldapEnabled-para meter voor een bestaand volume: <br> `Error Message: ldapEnabled parameter is not allowed to update` |  U kunt de instelling van de LDAP-optie niet wijzigen nadat u een volume hebt gemaakt. <br> Werk de instelling van de LDAP-optie niet bij op een gemaakt volume. Zie [configureren voegt LDAP toe met uitgebreide groepen voor NFS-volume toegang](configure-ldap-extended-groups.md) voor meer informatie. |
| Fout bij het maken van een NFS-volume met LDAP-functionaliteit: <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  Deze fout treedt op omdat de DNS-server niet bereikbaar is. <br> <ul><li> Controleer of u de juiste site (bereik van de site) voor Azure NetApp Files hebt geconfigureerd. </li><li> De oorzaak dat DNS onbereikbaar is, kan een onjuist IP-adres of netwerk problemen zijn. Controleer het DNS IP-adres dat is ingevoerd in de AD-verbinding om er zeker van te zijn dat het juist is. </li><li> Zorg ervoor dat de AD en het volume zich in dezelfde regio en hetzelfde VNet bevinden. Als ze zich in verschillende VNets bevinden, moet u ervoor zorgen dat VNet-peering tot stand is gebracht tussen de twee VNets.</li></ul> |
| Fout bij het maken van het volume vanuit een moment opname: <br> `Aggregate does not exist` | Azure NetApp Files biedt geen ondersteuning voor het inrichten van een nieuw, LDAP-ingeschakeld volume van een moment opname die hoort bij een LDAP-uitgeschakeld volume. <br> Maak een nieuw LDAP-uitgeschakeld volume op basis van de opgegeven moment opname. |

## <a name="next-steps"></a>Volgende stappen  

* [Configureren voegt LDAP toe met uitgebreide groepen voor NFS-volume toegang](configure-ldap-extended-groups.md)
* [Een NFS-volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Een NFSv3-en SMB-volume (Dual-Protocol) maken voor Azure NetApp Files](create-volumes-dual-protocol.md)