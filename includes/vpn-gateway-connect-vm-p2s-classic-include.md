---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4732abacc80a24d268f0e5cb7d100079e5a8d90b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105933"
---
Maak een verbinding met het externe bureaublad om verbinding te maken met een VM die op uw VNet is geïmplementeerd. De beste manier om te controleren of u verbinding met uw VM kunt maken is door verbinding te maken met het privé-IP-adres ervan in plaats van de computernaam. Op die manier test u of u verbinding kunt maken, niet of naamomzetting correct is geconfigureerd. 

1. Zoek het privé-IP-adres voor de VM. Als u het privé-IP-adres van een VM zoekt, bekijkt u de eigenschappen voor de VM in de Azure-portal of u gebruikt PowerShell.
2. Controleer of u met uw VNet bent verbonden met de punt-naar-site-VPN-verbinding. 
3. Als u Verbinding met extern bureaublad wilt openen, voert u *RDP* of *Verbinding met extern bureaublad* in het zoekvak op de taakbalk in en selecteert u **Verbinding met extern bureaublad**. U kunt deze ook openen via de opdracht **mstsc** in PowerShell. 
3. Voer in **verbinding met extern bureaublad** het privé-IP-adres van de virtuele machine in. Selecteer zo nodig **Opties weergeven** om aanvullende instellingen aan te passen en maak vervolgens verbinding.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Problemen met de RDP-verbinding met een VM oplossen

Als u problemen ondervindt bij het verbinding maken met een virtuele machine via de VPN-verbinding, kunt u een aantal zaken controleren. 

- Controleer of uw VPN-verbinding tot stand is gebracht.
- Controleer of u verbinding maakt met het privé-IP-adres voor de VM.
- Gebruik de opdracht **ipconfig** om het IPv4-adres te controleren dat is toegewezen aan de ethernetadapter op de computer waarmee u de verbinding tot stand brengt. Als het IP-adres zich binnen het adresbereik bevindt van het VNet waarmee u verbinding maakt of binnen het adresbereik van uw VPNClientAddressPool, heeft u te maken met een overlappende adresruimte. Als uw adresruimte op deze manier overlapt, kan het netwerkverkeer Azure niet bereiken en blijft het in het lokale netwerk.
- Als u verbinding met de VM kunt maken met behulp van het privé-IP-adres, maar niet met de computernaam, controleert u of DNS correct is geconfigureerd. Zie [Naamomzetting voor VM's](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voor meer informatie over de werking van naamomzetting voor VM's.
- Controleer of het configuratiepakket voor de VPN-client wordt gegenereerd nadat u de IP-adressen van de DNS-server heeft opgegeven voor het VNet. Als u de IP-adressen van de DNS-server bijwerkt, genereert en installeert u een nieuw configuratiepakket voor de VPN-client.

Zie [Troubleshoot Remote Desktop connections to a VM](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection) (Problemen met Extern-bureaubladverbindingen met een VM oplossen) voor meer informatie over het oplossen van problemen.