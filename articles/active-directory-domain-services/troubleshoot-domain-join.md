---
title: Problemen met domein deelname met Azure AD Domain Services oplossen | Microsoft Docs
description: Meer informatie over het oplossen van veelvoorkomende problemen wanneer u een VM probeert te koppelen of een toepassing verbindt met Azure Active Directory Domain Services en u geen verbinding kunt maken met of verifiëren voor het beheerde domein.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 86d6ca79a12e4706f558e92c3c83c5bddaa99b3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618600"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Problemen met domein deelname oplossen met een door Azure Active Directory Domain Services beheerd domein

Wanneer u probeert lid te worden van een virtuele machine (VM) of een toepassing verbindt met een beheerd domein van Azure Active Directory Domain Services (Azure AD DS), treedt er mogelijk een fout op waardoor u dit niet kunt doen. Als u problemen met domein deelname wilt oplossen, bekijkt u op welke van de volgende punten u een probleem hebt:

* Als u geen verificatie prompt ontvangt, kan de virtuele machine of toepassing geen verbinding maken met het door Azure AD DS beheerde domein.
    * Begin met het oplossen [van verbindings problemen voor domein deelname](#connectivity-issues-for-domain-join).
* Als er een fout optreedt tijdens de verificatie, is de verbinding met het beheerde domein geslaagd.
    * Begin met het oplossen van [problemen met betrekking tot referenties tijdens het toevoegen](#credentials-related-issues-during-domain-join)van een domein.

## <a name="connectivity-issues-for-domain-join"></a>Verbindings problemen voor domein deelname

Als de virtuele machine het beheerde domein niet kan vinden, is er meestal een netwerk verbinding of een configuratie probleem. Bekijk de volgende stappen voor probleem oplossing om het probleem op te sporen en op te lossen:

1. Zorg ervoor dat de virtuele machine is verbonden met hetzelfde of een gekoppeld virtueel netwerk als het beheerde domein. Als dat niet het geval is, kan de virtuele machine geen verbinding maken met het domein om lid te worden van.
    * Als de virtuele machine niet is verbonden met hetzelfde virtuele netwerk, controleert u of de peering of VPN-verbinding van het virtuele netwerk *actief* is of *verbinding* heeft om het verkeer goed te laten stromen.
1. Probeer het domein te pingen met behulp van de domein naam van het beheerde domein, zoals `ping aaddscontoso.com` .
    * Als het ping-antwoord mislukt, probeert u de IP-adressen voor het domein te pingen dat wordt weer gegeven op de pagina overzicht in de portal voor uw beheerde domein, zoals `ping 10.0.0.4` .
    * Als u het IP-adres, maar niet het domein, kunt pingen, is het mogelijk dat DNS onjuist is geconfigureerd. Zorg ervoor dat u [de DNS-servers van het beheerde domein hebt geconfigureerd voor het virtuele netwerk][configure-dns].
1. Probeer de cache van de DNS-resolver te leegmaken op de virtuele machine, zoals `ipconfig /flushdns` .

### <a name="network-security-group-nsg-configuration"></a>Configuratie van de netwerk beveiligings groep (NSG)

Wanneer u een beheerd domein maakt, wordt er ook een netwerk beveiligings groep gemaakt met de juiste regels voor een geslaagde domein bewerking. Als u extra regels voor de netwerk beveiligings groep bewerkt of maakt, blokkeert u mogelijk per ongeluk poorten die vereist zijn voor Azure AD DS om verbinding en verificatie services te bieden. Deze regels voor netwerk beveiligings groepen kunnen problemen veroorzaken, zoals het niet volt ooien van wachtwoord synchronisatie, gebruikers kunnen zich niet aanmelden of problemen met domein deelname.

Als er verbindings problemen blijven bestaan, raadpleegt u de volgende stappen voor probleem oplossing:

1. Controleer de integriteits status van uw beheerde domein in het Azure Portal. Als u een waarschuwing voor *AADDS001* hebt, wordt de toegang geblokkeerd door een regel voor de netwerk beveiligings groep.
1. Controleer de [vereiste poorten en de regels voor de netwerk beveiligings groep][network-ports]. Zorg ervoor dat er geen regels voor de netwerk beveiligings groep worden toegepast op de virtuele machine of het virtuele netwerk waarmee u verbinding maakt van deze netwerk poorten blok keren.
1. Zodra er problemen met de configuratie van de netwerk beveiligings groep zijn opgelost, verdwijnt de waarschuwing *AADDS001* in ongeveer 2 uur uit de status pagina. Probeer opnieuw verbinding te maken met de virtuele machine met netwerk connectiviteit die nu beschikbaar is.

## <a name="credentials-related-issues-during-domain-join"></a>Problemen met betrekking tot referenties tijdens domein koppeling

Als er een dialoog venster wordt weer geven waarin wordt gevraagd om referenties om lid te worden van het beheerde domein, kan de virtuele machine verbinding maken met het domein met behulp van Azure Virtual Network. Het proces voor domein deelname mislukt bij het verifiëren van het domein of de autorisatie voor het volt ooien van het proces voor het samen voegen van het domein met behulp van de referenties.

Raadpleeg de volgende stappen voor probleem oplossing om problemen met betrekking tot referenties op te lossen:

1. Gebruik de UPN-indeling om referenties op te geven, zoals `dee@contoso.onmicrosoft.com`. Zorg ervoor dat deze UPN juist is geconfigureerd in azure AD.
    * De *SAMAccountName* voor uw account kan automatisch worden gegenereerd als er meerdere gebruikers zijn met hetzelfde UPN-voor voegsel in uw Tenant of als uw UPN-voor voegsel langer is dan lang. Daarom kan de *SAMAccountName* -indeling voor uw account afwijken van wat u verwacht of gebruikt in uw on-premises domein.
1. Gebruik de referenties voor een gebruikers account dat deel uitmaakt van het beheerde domein om Vm's toe te voegen aan het beheerde domein.
1. Zorg ervoor dat u [wachtwoord synchronisatie hebt ingeschakeld][enable-password-sync] en lang genoeg hebt gewacht totdat de eerste wachtwoord synchronisatie is voltooid.

## <a name="next-steps"></a>Volgende stappen

Zie voor een diep gaande uitleg van de Active Directory processen als onderdeel van de bewerking voor domein deelname [samen werking en verificatie][join-authentication-issues].

Als u nog steeds problemen ondervindt met het toevoegen van uw VM aan het beheerde domein, [zoekt u hulp en opent u een ondersteunings ticket voor Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
