---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5ef67580928a45609f50d3fe798eb9d054265c0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93375941"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="what-should-i-do-if-im-getting-a-certificate-mismatch-when-connecting-using-certificate-authentication"></a>Wat moet ik doen als een certificaat niet overeenkomt wanneer ik verbinding maak met certificaatverificatie?

Schakel het vakje **De identiteit van de server verifiëren door het certificaat te valideren** of **voeg de server-FQDN toe met het certificaat** uit wanneer u handmatig een profiel maakt. U doet dit door **rasphone** uit te voeren vanuit de opdrachtprompt en het profiel te selecteren uit het vervolgkeuzemenu.

Het is in het algemeen niet aan te raden om de validatie van de serveridentiteit over te slaan, maar met Azure-certificaatverificatie wordt hetzelfde certificaat gebruikt voor servervalidatie in het VPN-tunnelprotocol (IKEv2/SSTP) en het EAP-protocol. Aangezien het servercertificaat en de FQDN al gevalideerd zijn door het VPN-tunnelprotocol, is het overbodig om deze nogmaals te valideren in de EAP.

![Verificatie van punt-naar-site](./media/vpn-gateway-faq-p2s-all-include/servercert.png "Servercertificaat")

### <a name="can-i-use-my-own-internal-pki-root-ca-to-generate-certificates-for-point-to-site-connectivity"></a>Kan ik mijn eigen interne PKI basis-CA gebruiken om certificaten te genereren voor een punt-naar-site-verbinding?

Ja. Voorheen konen alleen zelfondertekende basiscertificaten worden gebruikt. U kunt nog steeds 20 basiscertificaten uploaden.

### <a name="can-i-use-certificates-from-azure-key-vault"></a>Kan ik certificaten gebruiken uit Azure Key Vault?

Nee.

### <a name="what-tools-can-i-use-to-create-certificates"></a>Welke hulpprogramma's kan ik gebruiken om certificaten te maken?

U kunt uw Enterprise PKI-oplossing (uw interne PKI), Azure PowerShell, MakeCert en OpenSSL gebruiken.

### <a name="are-there-instructions-for-certificate-settings-and-parameters"></a><a name="certsettings"></a>Zijn er instructies voor het instellen van het certificaat en de parameters?

* **Interne PKI/Enterprise PKI-oplossing:** zie de stappen om [certificaten te genereren](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#generatecert).

* **Azure PowerShell:** zie het [Azure PowerShell](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md)-artikel voor een stappenplan.

* **MakeCert:** zie het [MakeCert](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md)-artikel voor een stappenplan.

* **OpenSSL:** 

    * Bij het exporteren van certificaten, moet u het basiscertificaat naar Base64 converteren.

    * Voor het clientcertificaat:

      * Bij het maken van de persoonlijke sleutel, moet u de lengte 4096 opgeven.
      * Bij het maken van het certificaat moet u voor de paramter *-extensies* de waarde *usr_cert* opgeven.
