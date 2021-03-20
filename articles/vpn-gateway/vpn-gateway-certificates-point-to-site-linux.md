---
title: 'Certificaten genereren en exporteren voor punt-naar-site: Linux: CLI'
description: Maak een zelfondertekend basis certificaat, Exporteer de open bare sleutel en Genereer client certificaten met behulp van de Linux-CLI (strongSwan).
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: alzam
ms.openlocfilehash: 0be5bb042649b0fe425077b5b3feb3cea728218c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89394003"
---
# <a name="generate-and-export-certificates"></a>Certificaten genereren en exporteren

Punt-naar-site-verbindingen gebruiken certificaten voor verificatie. In dit artikel wordt beschreven hoe u een zelfondertekend basis certificaat maakt en client certificaten genereert met behulp van de Linux CLI en strongSwan. Als u andere certificaat instructies zoekt, raadpleegt u de [Power shell](vpn-gateway-certificates-point-to-site.md) -of [makecert](vpn-gateway-certificates-point-to-site-makecert.md) -artikelen. Voor informatie over het installeren van strongSwan met behulp van de gebruikers interface in plaats van CLI, zie de stappen in het artikel [client configuratie](point-to-site-vpn-client-configuration-azure-cert.md#install) .

## <a name="install-strongswan"></a>StrongSwan installeren

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>Certificaten genereren en exporteren

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Volgende stappen

Ga door met uw punt-naar-site-configuratie om [configuratie bestanden voor VPN-clients te maken en te installeren](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).
