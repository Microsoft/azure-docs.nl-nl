---
title: Dynamische DNS gebruiken om hostnamen te registreren in azure | Microsoft Docs
description: Meer informatie over het instellen van dynamische DNS voor het registreren van hostnamen in uw eigen DNS-servers.
services: dns
documentationcenter: na
author: subsarma
manager: vitinnan
editor: ''
ms.assetid: c315961a-fa33-45cf-82b9-4551e70d32dd
ms.service: dns
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2017
ms.author: subsarma
ms.openlocfilehash: ad91eb94aedcdd0e4e715162e3ae064a1d2fb1ea
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220419"
---
# <a name="use-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Dynamische DNS gebruiken om hostnamen te registreren op uw eigen DNS-server

[Azure biedt naam omzetting](virtual-networks-name-resolution-for-vms-and-role-instances.md) voor virtuele machines (VM) en rolinstanties. Als uw naam omzetting de mogelijkheden van de standaard-DNS van Azure overschrijdt, kunt u uw eigen DNS-servers opgeven. Met uw eigen DNS-servers hebt u de mogelijkheid om uw DNS-oplossing aan uw eigen specifieke behoeften aan te passen. U moet bijvoorbeeld toegang hebben tot on-premises resources via uw Active Directory-domein controller.

Wanneer uw aangepaste DNS-servers worden gehost als virtuele Azure-machines, kunt u hostname-query's voor hetzelfde virtuele netwerk naar Azure sturen om hostnamen op te lossen. Als u deze optie niet wilt gebruiken, kunt u uw VM-hostnamen registreren in uw DNS-server met behulp van dynamische DNS (DDNS). Azure beschikt niet over de referenties om rechtstreeks records te maken op uw DNS-servers, waardoor alternatieve voorzieningen vaak nodig zijn. Enkele veelvoorkomende scenario's, met alternatieven:

## <a name="windows-clients"></a>Windows-clients
Windows-clients die niet lid zijn van een domein, proberen onbeveiligde DDNS-updates uit te voeren wanneer ze worden opgestart of wanneer hun IP-adres wordt gewijzigd. De DNS-naam is de hostnaam plus het achtervoegsel van de primaire DNS. Azure laat het achtervoegsel voor de primaire DNS leeg, maar u kunt het achtervoegsel instellen in de virtuele machine via de [gebruikers interface](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc794784(v=ws.10)) of [Power shell](/powershell/module/dnsclient/set-dnsclient).

Windows-clients die lid zijn van een domein registreren hun IP-adressen bij de domein controller met behulp van beveiligde DDNS. Het proces voor het samen voegen van een domein stelt het achtervoegsel van de primaire DNS-server in op de client en maakt en onderhoudt de vertrouwens relatie.

## <a name="linux-clients"></a>Linux-clients
Linux-clients registreren zich over het algemeen niet bij de DNS-server bij het opstarten, maar ze nemen de DHCP-server op. De DHCP-servers van Azure beschikken niet over de referenties voor het registreren van records in uw DNS-server. U kunt een hulp programma met `nsupdate` de naam, dat is opgenomen in het BIND pakket, gebruiken om DDNS-updates te verzenden. Omdat het DDNS-protocol is gestandaardiseerd, kunt u `nsupdate` ook gebruiken als u geen binding op de DNS-server gebruikt.

U kunt de door de DHCP-client opgegeven hooks gebruiken om de hostnaam in de DNS-server te maken en te onderhouden. Tijdens de DHCP-cyclus voert de client de scripts in */etc/DHCP/dhclient-exit-hooks.d/* uit. U kunt de hooks gebruiken om het nieuwe IP-adres te registreren met `nsupdate` . Bijvoorbeeld:

```bash
#!/bin/sh
requireddomain=mydomain.local

# only execute on the primary nic
if [ "$interface" != "eth0" ]
then
    return
fi

# When you have a new IP, perform nsupdate
if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
   [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
then
   host=`hostname`
   nsupdatecmds=/var/tmp/nsupdatecmds
     echo "update delete $host.$requireddomain a" > $nsupdatecmds
     echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
     echo "send" >> $nsupdatecmds

     nsupdate $nsupdatecmds
fi
```

U kunt ook de `nsupdate` opdracht gebruiken om beveiligde DDNS-updates uit te voeren. Wanneer u bijvoorbeeld een BIND-DNS-server gebruikt, wordt er een openbaar-persoonlijk sleutel paar gegenereerd ( `http://linux.yyz.us/nsupdate/` ). De DNS-server is geconfigureerd ( `http://linux.yyz.us/dns/ddns-server.html` ) met het open bare deel van de sleutel, zodat de hand tekening op de aanvraag kan worden geverifieerd. Als u het sleutel paar wilt opgeven `nsupdate` , gebruikt u de `-k` optie om de DDNS-update aanvraag te ondertekenen.

Wanneer u een Windows DNS-server gebruikt, kunt u Kerberos-verificatie gebruiken met de `-g` para meter in `nsupdate` , maar deze is niet beschikbaar in de Windows-versie van `nsupdate` . Als u Kerberos wilt gebruiken, gebruikt `kinit` u om de referenties te laden. U kunt bijvoorbeeld referenties laden vanuit een keytab- [bestand](https://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)) en vervolgens `nsupdate -g` de referenties ophalen uit de cache.

Indien nodig kunt u een achtervoegsel voor DNS-Zoek opdrachten toevoegen aan uw Vm's. Het DNS-achtervoegsel is opgegeven in het */etc/resolv.conf* -bestand. De meeste Linux-distributies beheren automatisch de inhoud van dit bestand, zodat u deze meestal niet kunt bewerken. U kunt het achtervoegsel echter vervangen door de opdracht van de DHCP-client te gebruiken `supersede` . Als u het achtervoegsel wilt overschrijven, voegt u de volgende regel toe aan het */etc/DHCP/dhclient.conf* -bestand:

```
supersede domain-name <required-dns-suffix>;
```