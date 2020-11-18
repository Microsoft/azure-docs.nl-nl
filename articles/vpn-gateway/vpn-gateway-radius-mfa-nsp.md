---
title: NPS integreren met VPN Gateway RADIUS-verificatie voor MFA
description: Hierin wordt de RADIUS-verificatie van Azure gateway geïntegreerd met de NPS-server voor Multi-Factor Authentication.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: e5456c53ca891a7c88797f9661a6a3b9b9935e08
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660904"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>RADIUS-verificatie van Azure VPN-gateway met NPS-server voor Multi-Factor Authentication integreren 

In dit artikel wordt beschreven hoe u Network Policy Server (NPS) integreert met RADIUS-verificatie van Azure VPN-gateway om Multi-Factor Authentication (MFA) te leveren voor punt-naar-site-VPN-verbindingen. 

## <a name="prerequisite"></a>Vereiste

Als u MFA wilt inschakelen, moeten de gebruikers zich in Azure Active Directory (Azure AD) bekomen, die moeten worden gesynchroniseerd vanuit de on-premises of in de cloud omgeving. Daarnaast moet de gebruiker het proces voor automatische inschrijving voor MFA al hebben voltooid.  Zie [Mijn account instellen voor verificatie in twee stappen](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md) voor meer informatie

## <a name="detailed-steps"></a>Gedetailleerde stappen

### <a name="step-1-create-a-virtual-network-gateway"></a>Stap 1: een virtuele netwerk gateway maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Selecteer **subnetten** in het virtuele netwerk dat als host moet fungeren voor de virtuele netwerk gateway en selecteer vervolgens **subnet gateway** om een subnet te maken. 

    ![De afbeelding over het toevoegen van een gateway-subnet](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Maak een virtuele netwerk gateway door de volgende instellingen op te geven:

    - **Gatewaytype**: selecteer **VPN**.
    - **VPN-type**: Selecteer **op route gebaseerd**.
    - **SKU**: Selecteer een SKU-type op basis van uw vereisten.
    - **Virtueel netwerk**: Selecteer het virtuele netwerk waarin u het gateway-subnet hebt gemaakt.

        ![De installatie kopie van de gateway-instellingen van het virtuele netwerk](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Stap 2 de NPS configureren voor Azure MFA

1. [Installeer de NPS-extensie voor Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension)op de NPS-server.
2. Open de NPS-console, klik met de rechter muisknop op **RADIUS-clients** en selecteer vervolgens **Nieuw**. Maak de RADIUS-client door de volgende instellingen op te geven:

    - **Beschrijvende naam**: Typ een naam.
    - **Adres (IP of DNS)**: Typ het gateway-subnet dat u in stap 1 hebt gemaakt.
    - **Gedeeld geheim**: Typ een geheime sleutel en onthoud deze voor later gebruik.

      ![De afbeelding over RADIUS-client instellingen](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Stel op het tabblad **Geavanceerd** de naam van de leverancier in op **RADIUS-standaard** en zorg ervoor dat de **extra opties** selectie vakje niet is ingeschakeld.

    ![De afbeelding over geavanceerde instellingen voor RADIUS-client](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Ga naar **beleids regels**  >  **netwerk beleid**, dubbel klik op **verbindingen naar micro soft Routing and Remote Access Server** -beleid, selecteer **toegang verlenen** en klik vervolgens op **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Stap 3 de gateway van het virtuele netwerk configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Open de gateway van het virtuele netwerk dat u hebt gemaakt. Zorg ervoor dat het gateway type is ingesteld op **VPN** en dat het VPN-type **op route gebaseerd** is.
3. Klik op **verwijzen naar site configuratie**  >  **nu configureren** en geef de volgende instellingen op:

    - **Adres groep**: Typ het gateway-subnet dat u in stap 1 hebt gemaakt.
    - **Verificatie type**: Selecteer **RADIUS-verificatie**.
    - **IP-adres van server**: Typ het IP-adres van de NPS-server.

      ![De afbeelding over punt-naar-site-instellingen](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Volgende stappen

- [Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)
- [Uw bestaande NPS-infrastructuur integreren met Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)