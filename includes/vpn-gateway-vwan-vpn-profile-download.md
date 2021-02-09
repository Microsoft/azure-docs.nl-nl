---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979063"
---
## <a name="extract-the-zip-file"></a>Het zip-bestand uitpakken

Pak het gecomprimeerde bestand uit. Het bestand bevat de volgende mappen:

* AzureVPN
* Algemeen
* OpenVPN (als u de OpenVPN hebt ingeschakeld met **Azure** -certificaat **-of RADIUS-verificatie** -instellingen op de gateway). Selecteer het betreffende artikel dat overeenkomt met uw configuratie om een Tenant te maken.

  * [VPN gateway: een Tenant maken](../articles/vpn-gateway/openvpn-azure-ad-tenant.md).
  * [Virtueel WAN: een Tenant maken](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="retrieve-information"></a>Gegevens ophalen

Ga in de map **AzureVPN** naar het **_azurevpnconfig.xml_** -bestand en open het met Klad blok. Noteer de tekst tussen de volgende tags.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profiel Details

Wanneer u een verbinding toevoegt, gebruikt u de gegevens die u in de vorige stap hebt verzameld voor de pagina Profiel Details. De velden komen overeen met de volgende informatie:

* **Doel groep:** Hiermee wordt de bron van de ontvanger aangegeven waarvoor het token is bedoeld.
* **Verlener:** Identificeert de Security Token Service (STS) die het token heeft verzonden, evenals de Azure AD-Tenant.
* **Tenant:** Bevat een onveranderbare, unieke id van de Directory-Tenant die het token heeft uitgegeven.
* **FQDN:** De Fully Qualified Domain Name (FQDN) op de Azure VPN-gateway.
* **ServerSecret:** De vooraf gedeelde VPN gateway-sleutel.

## <a name="folder-contents"></a>Mapinhoud

* De **algemene map** bevat het open bare-server certificaat en het VpnSettings.xml-bestand. Het VpnSettings.xml bestand bevat informatie die nodig is voor het configureren van een algemene client.

* Het gedownloade zip-bestand kan ook **WindowsAmd64** -en **WindowsX86** -mappen bevatten. Deze mappen bevatten het installatie programma voor SSTP en IKEv2 voor Windows-clients. U moet beheerders rechten hebben op de client om ze te installeren.
