---
title: 'Azure VPN Gateway: problemen met Point-to-site-verbindingen oplossen: Mac OS X-clients'
description: Meer informatie over het oplossen van problemen met Point-to-site-verbindingen van Mac OS X met behulp van de systeem eigen VPN-client en IKEv2.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 02/10/2021
ms.author: alzam
ms.openlocfilehash: 4c8657f8a40084a726280efe471c0791931294f7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100366633"
---
# <a name="troubleshoot-point-to-site-vpn-connections-from-mac-os-x-vpn-clients"></a>Problemen met punt-naar-site-VPN-verbindingen van Mac OS X VPN-clients oplossen

Dit artikel helpt u bij het oplossen van problemen met Point-to-site-verbindingen van Mac OS X met behulp van de systeem eigen VPN-client en IKEv2. De VPN-client in Mac voor IKEv2 is zeer eenvoudig en is niet in staat om veel aanpassingen aan te brengen. Er zijn slechts vier instellingen die moeten worden gecontroleerd:

* Server adres
* Externe ID
* Lokale ID
* Verificatie-instellingen
* Versie van besturings systeem (10,11 of hoger)


## <a name="troubleshoot-certificate-based-authentication"></a><a name="VPNClient"></a> Problemen met verificatie op basis van certificaten oplossen
1. Controleer de instellingen voor de VPN-client. Ga naar de **netwerk instelling** door op Command + Shift te drukken en typ vervolgens ' VPN ' om de instellingen voor de VPN-client te controleren. Klik in de lijst op de VPN-vermelding die moet worden onderzocht.

   ![Verificatie op basis van IKEv2-certificaten](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2cert1.jpg)
2. Controleer of het **server adres** de volledige FQDN is en de cloudapp.net bevat.
3. De **externe ID** moet hetzelfde zijn als het server adres (FQDN-naam van de gateway).
4. De **lokale id** moet hetzelfde zijn als het **onderwerp** van het client certificaat.
5. Klik op **verificatie-instellingen** om de pagina verificatie-instellingen te openen.

   ![Scherm afbeelding toont het dialoog venster verificatie-instellingen met geselecteerd certificaat.](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth2.jpg)
6. Controleer of het **certificaat** is geselecteerd in de vervolg keuzelijst.
7. Klik op de knop **selecteren** en controleer of het juiste certificaat is geselecteerd. Klik op **OK** om de wijzigingen op te slaan.

## <a name="troubleshoot-username-and-password-authentication"></a><a name="ikev2"></a>Problemen met gebruikers naam-en wachtwoord verificatie oplossen

1. Controleer de instellingen voor de VPN-client. Ga naar de **netwerk instelling** door op Command + Shift te drukken en typ vervolgens ' VPN ' om de instellingen voor de VPN-client te controleren. Klik in de lijst op de VPN-vermelding die moet worden onderzocht.

   ![Wacht woord voor IKEv2-gebruikers naam](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2user3.jpg)
2. Controleer of het **server adres** de volledige FQDN is en de cloudapp.net bevat.
3. De **externe ID** moet hetzelfde zijn als het server adres (FQDN-naam van de gateway).
4. De **lokale id** mag leeg zijn.
5. Klik op de knop **verificatie-instelling** en controleer of ' username ' is geselecteerd in de vervolg keuzelijst.

   ![Scherm afbeelding toont het dialoog venster verificatie-instellingen met de gebruikers naam geselecteerd.](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/ikev2auth4.png)
6. Controleer of de juiste referenties zijn ingevoerd.

## <a name="additional-steps"></a><a name="additional"></a>Aanvullende stappen

Als u de vorige stappen probeert uit te voeren en alles correct is geconfigureerd, downloadt u [wireshark](https://www.wireshark.org/#download) en voert u een pakket opname uit.

1. Filter op *ISAKMP* en bekijk de **IKE_SA** pakketten. U moet de details van het SA-voor stel kunnen bekijken onder de **beveiligings koppeling Payload: Security**. 
2. Controleer of de client en de server een gemeen schappelijke set hebben.

   ![pakket](./media/vpn-gateway-troubleshoot-point-to-site-osx-ikev2/packet5.jpg) 
  
3. Als er geen antwoord van de server is op de netwerk traceringen, controleert u of het IKEv2-protocol is ingeschakeld op de pagina Azure gateway-configuratie op de Azure Portal-website.

## <a name="next-steps"></a>Volgende stappen
Zie [Microsoft ondersteuning](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)voor meer informatie.
