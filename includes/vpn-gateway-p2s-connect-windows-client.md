---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93041586"
---
>[!NOTE]
>U moet beheerdersrechten hebben op de Windows-clientcomputer waarmee u de verbinding tot stand brengt.
>

1. Als u verbinding wilt maken met uw VNet, gaat u op de client computer naar VPN-instellingen en zoekt u de VPN-verbinding die u hebt gemaakt. De naam heeft dezelfde naam als het virtuele netwerk. Selecteer **Verbinding maken**. Er verschijnt mogelijk een pop-upbericht dat verwijst naar het certificaat. Selecteer **door gaan** om verhoogde bevoegdheden te gebruiken.

1. Selecteer op de pagina **Verbindingsstatus** de optie **Verbinden** om de verbinding te starten. Als het scherm **Certificaat selecteren** wordt geopend, controleert u of het weergegeven clientcertificaat het certificaat is dat u voor de verbinding wilt gebruiken. Als dat niet het geval is, gebruikt u de vervolg keuze pijl om het juiste certificaat te selecteren en selecteert u **OK**.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="Verbinding maken vanaf een Windows-computer":::

1. De verbinding is tot stand gebracht.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="Verbinding maken tussen een computer en een Azure VNet-Point-to-site-verbindings diagram":::
