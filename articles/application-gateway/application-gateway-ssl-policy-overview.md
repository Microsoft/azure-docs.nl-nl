---
title: Overzicht van het TLS-beleid voor Azure-toepassing gateway
description: Meer informatie over het configureren van TLS-beleid voor Azure-toepassing gateway en het verminderen van de overhead voor versleuteling en ontsleuteling van een back-endserver.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: amsriva
ms.openlocfilehash: 77239cd8586b8fb07abf6862be436979541bdb99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97631687"
---
# <a name="application-gateway-tls-policy-overview"></a>Overzicht van Application Gateway TLS-beleid

U kunt Azure-toepassing gateway gebruiken om TLS/SSL-certificaat beheer te centraliseren en de overhead voor versleuteling en ontsleuteling van een back-endserver te verminderen. Met deze gecentraliseerde TLS-verwerking kunt u ook een centraal TLS-beleid opgeven dat geschikt is voor de beveiligings vereisten van uw organisatie. Zo kunt u voldoen aan nalevings vereisten en beveiligings richtlijnen en aanbevolen procedures.

Het TLS-beleid bevat controle over de TLS-protocol versie, evenals de coderings suites en de volg orde waarin de code ringen worden gebruikt tijdens een TLS-handshake. Application Gateway biedt twee mechanismen voor het beheren van TLS-beleid. U kunt een vooraf gedefinieerd beleid of een aangepast beleid gebruiken.

## <a name="predefined-tls-policy"></a>Vooraf gedefinieerd TLS-beleid

Application Gateway heeft drie vooraf gedefinieerde beveiligings beleidsregels. U kunt uw gateway met een van deze beleids regels configureren om het juiste beveiligings niveau te verkrijgen. De beleids namen worden door het jaar en de maand waarin ze zijn geconfigureerd, gemarkeerd. Elk beleid biedt verschillende TLS-protocol versies en coderings suites. U wordt aangeraden het meest recente TLS-beleid te gebruiken om de beste TLS-beveiliging te garanderen.

## <a name="known-issue"></a>Bekend probleem
Application Gateway v2 biedt geen ondersteuning voor de volgende DHE-code ringen en deze worden niet gebruikt voor de TLS-verbindingen met clients, zelfs als ze worden vermeld in het vooraf gedefinieerde beleid. In plaats van DHE-code ringen worden veilige en snellere ECDHE-code ringen aanbevolen.

- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Eigenschap  |Waarde  |
|---|---|
|Naam     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Standaard| Waar (als er geen vooraf gedefinieerd beleid is opgegeven) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Eigenschap  |Waarde  |
|   ---      |  ---       |
|Naam     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Standaard| Niet waar |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Eigenschap  |Waarde  |
|---|---|
|Naam     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Standaard| Niet waar |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Aangepast TLS-beleid

Als er een vooraf gedefinieerd TLS-beleid moet worden geconfigureerd voor uw vereisten, moet u uw eigen aangepaste TLS-beleid definiëren. Met een aangepast TLS-beleid hebt u volledige controle over de minimale versie van het TLS-protocol voor ondersteuning, evenals de ondersteunde coderings suites en hun prioriteits volgorde.

> [!IMPORTANT]
> Als u een aangepast SSL-beleid gebruikt in Application Gateway v1 SKU (Standard of WAF), moet u de verplichte code TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 toevoegen aan de lijst. Deze versleuteling is vereist voor het inschakelen van metrische gegevens en logboek registratie in de Application Gateway v1-SKU.
> Dit is niet verplicht voor de SKU van Application Gateway v2 (Standard_v2 of WAF_v2).
 
### <a name="tlsssl-protocol-versions"></a>TLS/SSL-protocol versies

* SSL 2,0 en 3,0 zijn standaard uitgeschakeld voor alle toepassings gateways. Deze protocol versies kunnen niet worden geconfigureerd.
* Een aangepast TLS-beleid biedt u de mogelijkheid om een van de volgende drie protocollen te selecteren als de minimale TLS-protocol versie voor uw gateway: TLSv1_0, TLSv1_1 en TLSv1_2.
* Als er geen TLS-beleid is gedefinieerd, worden alle drie de protocollen (TLSv1_0, TLSv1_1 en TLSv1_2) ingeschakeld.

### <a name="cipher-suites"></a>Coderings suites

Application Gateway ondersteunt de volgende coderings suites van waaruit u uw aangepaste beleid kunt kiezen. De volg orde van de coderings suites bepaalt de prioriteits volgorde tijdens TLS-onderhandeling.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> TLS-coderings suites die voor de verbinding worden gebruikt, zijn ook gebaseerd op het type certificaat dat wordt gebruikt. In client-naar-toepassings gateway-verbindingen worden de gebruikte coderings suites gebaseerd op het type server certificaten op de Application Gateway-listener. In Application Gateway naar back-end-pool verbindingen worden de gebruikte coderings suites gebaseerd op het type server certificaten op de back-endservers.

## <a name="next-steps"></a>Volgende stappen

Zie [TLS-beleids versies en coderings suites configureren op Application Gateway](application-gateway-configure-ssl-policy-powershell.md)als u meer wilt weten over het configureren van een TLS-beleid.
