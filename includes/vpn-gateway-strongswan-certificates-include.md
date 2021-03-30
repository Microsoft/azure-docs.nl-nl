---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1c2525b352c25f470814ce909a8d10ff821d9e32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "70961595"
---
Genereer het CA-certificaat.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

Het CA-certificaat in Base64-indeling afdrukken. Dit is de indeling die wordt ondersteund door Azure. U uploadt dit certificaat als onderdeel van de [P2S-configuratie stappen](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)naar Azure.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

Genereer het gebruikers certificaat.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

Genereer een P12-bundel met het gebruikers certificaat. Deze bundel wordt in de volgende stappen gebruikt bij het werken met de client configuratie bestanden.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```