---
title: Problemen oplossen met NFSv 4.1 Kerberos-volume problemen voor Azure NetApp Files | Microsoft Docs
description: Hierin worden fout berichten en oplossingen beschreven die u kunnen helpen bij het oplossen van problemen met NFSv 4.1 Kerberos-volumes voor Azure NetApp Files.
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
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98134310"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>Problemen met NFSv 4.1 Kerberos volume oplossen 

In dit artikel worden oplossingen beschreven voor fout situaties die u mogelijk hebt tijdens het maken of beheren van NFSv 4.1 Kerberos-volumes. 

## <a name="error-conditions-and-resolutions"></a>Fout voorwaarden en oplossingen

|     Fout voorwaarden    |     Oplossingen    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp Files biedt geen ondersteuning voor Kerberos voor NFSv3-volumes. Kerberos wordt alleen ondersteund voor het NFSv 4.1-protocol.  |
|`This NetApp account has no configured Active Directory   connections`  |  Configureer Active Directory voor het NetApp-account met de velden **KDC IP-** en **ad-server naam**. Zie [de Azure Portal configureren](configure-kerberos-encryption.md#configure-the-azure-portal) voor instructies. |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp Files biedt geen ondersteuning voor het converteren van een NFSv 4.1-volume naar een Kerberos NFSv 4.1-volume en vice versa. |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  Voorbeeld: `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> Zorg ervoor dat de A/PTR-records correct zijn ingesteld en aanwezig zijn in de Active Directory voor de server naam `smb-test-64d9.contoso.com` . <br> Als in de NFS-client `nslookup` wordt `smb-test-64d9.contoso.com` omgezet in een IP-adres IP1 (dat wil zeggen `10.1.1.68` ), `nslookup` moet IP1 worden omgezet in slechts één record (dat wil zeggen `smb-test-64d9.contoso.com` ). `nslookup` de IP1 *mag* niet worden omgezet in meerdere namen. </li>  <li>Stel AES-256 in voor het NFS-computer account van het type `NFS-<Smb NETBIOS NAME>-<few random characters>` op AD met behulp van Power shell of de gebruikers interface. <br> Voorbeeldopdrachten: <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>Zorg ervoor dat de tijd van de NFS-client, AD en Azure NetApp Files Storage-software met elkaar is gesynchroniseerd en binnen een bereik van vijf minuten scheef is. </li>  <li>Down load het Kerberos-ticket op de NFS-client met behulp van de opdracht `kinit <administrator>` .</li> <li>Verminder de hostnaam van de NFS-client tot minder dan 15 tekens en voer de realm-koppeling opnieuw uit. </li><li>Start de NFS-client en de `rpcgssd` service als volgt opnieuw op. De opdracht kan variëren afhankelijk van het besturings systeem.<br> RHEL 7: <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8: <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu <br> (Start de `rpc-gssd` service opnieuw.) <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | Het probleem kan betrekking hebben op het probleem van de NFS-client. Start de NFS-client opnieuw op.    |
|`Hostname lookup failed`   | U moet een zone voor reverse lookup maken op de DNS-server en vervolgens een PTR-record van de AD-hostcomputer toevoegen in de zone voor reverse lookup. <br> Stel dat het IP-adres van de AD-machine is `10.1.1.4` , de hostnaam van de AD-machine (zoals gevonden met de opdracht hostname) `AD1` en de domein naam is `contoso.com` . De PTR-record die is toegevoegd aan de zone voor reverse lookup moet zijn `10.1.1.4 -> AD1.contoso.com` . |
|`Volume creation fails due to unreachable DNS server`  | Er zijn twee mogelijke oplossingen beschikbaar: <br> <ul><li> Deze fout geeft aan dat DNS niet bereikbaar is. De reden is mogelijk een onjuist DNS-IP-adres of een netwerk probleem. Controleer het DNS-IP-adres dat is ingevoerd in de AD-verbinding en controleer of het IP-adres juist is. </li> <li> Zorg ervoor dat de AD en het volume zich in dezelfde regio en in hetzelfde VNet bevinden. Als ze zich in verschillende VNets bevinden, moet u ervoor zorgen dat VNet-peering tot stand is gebracht tussen de twee VNets. </li></ul> |
|Het maken van een NFSv 4.1 Kerberos-volume mislukt met een fout die vergelijkbaar is met het volgende voor beeld: <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |Het KDC IP-adres is onjuist en het Kerberos-volume is gemaakt. Werk de KDC-IP bij met een juist adres. <br> Nadat u het KDC IP-adres hebt bijgewerkt, gaat de fout verloren. U moet het volume opnieuw maken. |

## <a name="next-steps"></a>Volgende stappen  

* [NFSv4.1 Kerberos-versleuteling configureren voor Azure NetApp Files](configure-kerberos-encryption.md)
