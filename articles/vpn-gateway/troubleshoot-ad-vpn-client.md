---
title: 'VPN Gateway: problemen met VPN-client oplossen-Azure AD-verificatie'
description: Problemen met VPN Gateway P2S Azure AD-verificatie-clients oplossen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 56a8514fc2531ba0b18925427814e5bfef7d64bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84988107"
---
# <a name="troubleshoot-an-azure-ad-authentication-vpn-client"></a>Problemen met een VPN-client voor Azure AD-verificatie oplossen

Dit artikel helpt u bij het oplossen van problemen met een VPN-client om verbinding te maken met een virtueel netwerk met behulp van punt-naar-site-VPN en Azure Active Directory-verificatie.

## <a name="view-status-log"></a><a name="status"></a>Status logboek weer geven

Het status logboek weer geven voor fout berichten.

![logboeken](./media/troubleshoot-ad-vpn-client/1.png)

1. Klik op het pictogram met de pijlen aan de rechter benedenhoek van het client venster om de **status logboeken** weer te geven.
2. Controleer de logboeken op fouten die mogelijk duiden op het probleem.
3. Fout berichten worden rood weer gegeven.

## <a name="clear-sign-in-information"></a><a name="clear"></a>Aanmeldings gegevens wissen

Wis de aanmeldings gegevens.

![aanmelden](./media/troubleshoot-ad-vpn-client/2.png)

1. Selecteer de... Naast het profiel dat u wilt oplossen. Selecteer **configureren-> opgeslagen account wissen**.
2. Selecteer **Opslaan**.
3. Probeer verbinding te maken.
4. Als de verbinding nog steeds mislukt, gaat u verder met de volgende sectie.

## <a name="run-diagnostics"></a><a name="diagnostics"></a>Diagnose uitvoeren

Voer diagnostische gegevens uit op de VPN-client.

![diagnostische gegevens](./media/troubleshoot-ad-vpn-client/3.png)

1. Klik op de **..** . Naast het profiel waarvoor u Diagnostische gegevens wilt uitvoeren. Selecteer diagnose **-> diagnose uitvoeren**.
2. Op de client wordt een reeks tests uitgevoerd en wordt het resultaat van de test weer gegeven

   * Internet toegang: controleert of de client verbinding heeft met Internet
   * Client Referenties: Controleer of het Azure Active Directory verificatie-eind punt bereikbaar is
   * Server omzettingable: contact op met de DNS-server om het IP-adres van de geconfigureerde VPN-server op te lossen
   * Bereikbaar server: controleert of de VPN-server reageert of niet
3. Als een van de tests mislukt, neemt u contact op met uw netwerk beheerder om het probleem op te lossen.
4. In de volgende sectie ziet u hoe u de logboeken kunt verzamelen, indien nodig.

## <a name="collect-client-log-files"></a><a name="logfiles"></a>Client logboek bestanden verzamelen

Verzamel de logboek bestanden van de VPN-client. De logboek bestanden kunnen worden verzonden naar ondersteuning/beheerder via een methode van uw keuze. Bijvoorbeeld e-mail.

1. Klik op de '... ' Naast het profiel waarvoor u Diagnostische gegevens wilt uitvoeren. Selecteer **Diagnostics-> map Logs weer geven**.

   ![logboeken weer geven](./media/troubleshoot-ad-vpn-client/4.png)
2. Windows Verkenner wordt geopend in de map met de logboek bestanden.

   ![bestand weer geven](./media/troubleshoot-ad-vpn-client/5.png)

## <a name="next-steps"></a>Volgende stappen

Zie [een Azure Active Directory-Tenant maken voor P2S open VPN-verbindingen die gebruikmaken van Azure AD-verificatie](openvpn-azure-ad-tenant.md)voor meer informatie.