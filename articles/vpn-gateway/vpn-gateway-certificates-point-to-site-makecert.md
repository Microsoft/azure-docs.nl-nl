---
title: 'Azure VPN Gateway: &-export certificaten genereren voor P2S: MakeCert'
description: Maak een zelfondertekend basis certificaat, Exporteer de open bare sleutel en Genereer client certificaten met behulp van MakeCert.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 55e22ebec5853d6b4f10b53be8e24f4dbebe4e1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659774"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Certificaten voor punt-naar-site-verbindingen genereren en exporteren met MakeCert

Punt-naar-site-verbindingen gebruiken certificaten voor verificatie. In dit artikel wordt beschreven hoe u een zelfondertekend basis certificaat maakt en client certificaten genereert met behulp van MakeCert. Zie [certificaten-Power shell](vpn-gateway-certificates-point-to-site.md) of [certificaten-Linux](vpn-gateway-certificates-point-to-site-linux.md)als u andere certificaat instructies zoekt.

We raden u aan om de [Windows 10 Power shell-stappen](vpn-gateway-certificates-point-to-site.md) te gebruiken om uw certificaten te maken, maar we bieden deze makecert-instructies als een optionele methode. De certificaten die u met een van beide methoden genereert, kunnen worden geïnstalleerd op [elk ondersteund client besturingssysteem](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq). MakeCert heeft echter de volgende beperking:

* MakeCert is afgeschaft. Dit betekent dat dit hulp programma op elk gewenst moment kan worden verwijderd. Alle certificaten die u al met MakeCert hebt gegenereerd, worden niet beïnvloed wanneer MakeCert niet meer beschikbaar is. MakeCert wordt alleen gebruikt om de certificaten te genereren, niet als een validatie mechanisme.

## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Een zelfondertekend basis certificaat maken

De volgende stappen laten zien hoe u een zelfondertekend certificaat maakt met behulp van MakeCert. Deze stappen zijn niet specifiek voor het implementatie model. Ze zijn geldig voor Resource Manager en klassiek.

1. Down load en Installeer [makecert](/windows/win32/seccrypto/makecert).
2. Na de installatie kunt u het hulp programma makecert.exe in dit pad vinden: C:\Program Files (x86) \Windows Kits\10\bin \<arch> . Het is echter wel mogelijk dat het is geïnstalleerd op een andere locatie. Open een opdracht prompt als beheerder en navigeer naar de locatie van het hulp programma MakeCert. U kunt het volgende voor beeld gebruiken om de juiste locatie aan te passen:

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. Een certificaat maken en installeren in het persoonlijke certificaat archief op uw computer. In het volgende voor beeld wordt een overeenkomstig *. CER* -bestand gemaakt dat u uploadt naar Azure bij het configureren van P2S. Vervang ' P2SRootCert ' en ' P2SRootCert. CER ' door de naam die u wilt gebruiken voor het certificaat. Het certificaat bevindt zich in de certificaten-huidige Gebruiker\persoonlijk\certificaten.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="export-the-public-key-cer"></a><a name="cer"></a>De open bare sleutel (. CER) exporteren

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

Het geëxporteerde CER-bestand moet naar Azure worden geüpload. Zie [een punt-naar-site-verbinding configureren](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)voor instructies. Zie [Dit gedeelte](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add) van het artikel om een extra vertrouwd basis certificaat toe te voegen.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>Het zelfondertekende certificaat en de persoonlijke sleutel exporteren om deze op te slaan (optioneel)

Mogelijk wilt u het zelfondertekende basis certificaat exporteren en veilig opslaan. Als dat het geval is, kunt u dit later op een andere computer installeren en meer client certificaten genereren of een ander CER-bestand exporteren. Als u het zelfondertekende basis certificaat wilt exporteren als. pfx, selecteert u het basis certificaat en gebruikt u dezelfde stappen zoals beschreven in [een client certificaat exporteren](#clientexport).

## <a name="create-and-install-client-certificates"></a>Client certificaten maken en installeren

U installeert zelf het zelfondertekende certificaat niet rechtstreeks op de client computer. U moet een client certificaat genereren op basis van het zelfondertekende certificaat. Vervolgens exporteert en installeert u het client certificaat op de client computer. De volgende stappen zijn niet specifiek voor het implementatie model. Ze zijn geldig voor Resource Manager en klassiek.

### <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Een clientcertificaat genereren

Op elke clientcomputer die via punt-naar-site verbinding maakt met een VNet, moet een clientcertificaat zijn geïnstalleerd. U genereert een client certificaat op basis van het zelfondertekende basis certificaat en exporteert en installeert vervolgens het client certificaat. Als het client certificaat niet is geïnstalleerd, mislukt de verificatie. 

De volgende stappen helpen u bij het genereren van een client certificaat van een zelfondertekend basis certificaat. U kunt meerdere client certificaten genereren uit hetzelfde basis certificaat. Wanneer u client certificaten genereert met behulp van de onderstaande stappen, wordt het client certificaat automatisch geïnstalleerd op de computer die u hebt gebruikt voor het genereren van het certificaat. Als u een client certificaat op een andere client computer wilt installeren, kunt u het certificaat exporteren.
 
1. Open een opdracht prompt als beheerder op de computer die u hebt gebruikt om het zelfondertekend certificaat te maken.
2. Wijzig en voer het voor beeld uit om een client certificaat te genereren.
   * Wijzig *"P2SRootCert"* in de naam van het zelfondertekende toegangs punt waarvan u het client certificaat wilt genereren. Zorg ervoor dat u de naam van het basis certificaat gebruikt. Dit is de waarde ' CN = ' die u hebt opgegeven tijdens het maken van het zelfondertekende toegangs punt.
   * Wijzig *P2SChildCert* in de naam waarvan u een client certificaat wilt genereren.

   Als u het volgende voor beeld uitvoert zonder het te wijzigen, is het resultaat een client certificaat met de naam P2SChildcert in uw persoonlijke certificaat archief dat is gegenereerd op basis van P2SRootCert.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="export-a-client-certificate"></a><a name="clientexport"></a>Een client certificaat exporteren

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install-an-exported-client-certificate"></a><a name="install"></a>Een geëxporteerd clientcertificaat installeren

Zie [een client certificaat installeren](point-to-site-how-to-vpn-client-install-azure-cert.md)als u een client certificaat wilt installeren.

## <a name="next-steps"></a>Volgende stappen

Ga door met uw punt-naar-site-configuratie. 

* Zie [P2S configureren met behulp van systeem eigen Azure-certificaat verificatie](vpn-gateway-howto-point-to-site-resource-manager-portal.md)voor de stappen van het **Resource Manager** -implementatie model.
* Zie [een punt-naar-site-VPN-verbinding naar een VNet (klassiek) configureren](vpn-gateway-howto-point-to-site-classic-azure-portal.md)voor de stappen van het **klassieke** implementatie model.

Voor informatie over probleemoplossing voor P2S bekijkt u [Troubleshooting Azure point-to-site connections](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md) (Problemen met punt-naar-site-verbindingen in Azure oplossen).