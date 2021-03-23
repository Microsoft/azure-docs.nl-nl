---
title: Kerberos Key Distribution Center-proxy instellen Windows virtueel bureau blad-Azure
description: Een hostgroep voor virtuele Windows-Bureau bladen instellen voor gebruik van een Kerberos-Key Distribution Center proxy.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: bcf28fbc0d2f4ec9eeac5bcb8f0b2c9b65a62b6b
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775022"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Een Kerberos-Key Distribution Center proxy configureren (preview-versie)

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als openbare preview-versie.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Gebruikers met beveiligings bewuste klanten, zoals financiële of overheids organisaties, kunnen zich vaak aanmelden met Smart Cards. Smart Cards maken implementaties veiliger door multi-factor Authentication (MFA) te vereisen. Voor het RDP-gedeelte van een virtueel bureau blad-sessie van Windows is voor Smart Cards echter een rechtstreekse verbinding of een ' regel van het gezichts vermogen ' vereist, met een Active Directory-domein controller (AD) voor Kerberos-verificatie. Zonder deze directe verbinding kunnen gebruikers zich niet automatisch aanmelden bij het netwerk van de organisatie vanuit externe verbindingen. Gebruikers in een virtueel bureau blad-implementatie van Windows kunnen de KDC-proxy service gebruiken om dit verificatie verkeer te proxy en zich op afstand aan te melden. De KDC-proxy maakt verificatie mogelijk voor de Remote Desktop Protocol van een virtueel-bureaublad sessie van Windows, zodat de gebruiker veilig kan worden aangemeld. Hiermee werkt u veel eenvoudiger aan het werk en kunnen bepaalde scenario's voor herstel na nood gevallen soepeler worden uitgevoerd.

Voor het instellen van de KDC-proxy is het echter doorgaans nodig om de Windows Server-Gateway functie toe te wijzen in Windows Server 2016 of hoger. Hoe gebruikt u een Extern bureaublad-services-rol om u aan te melden bij het virtuele bureau blad van Windows? We gaan een kort overzicht van de onderdelen maken om te antwoorden.

Er zijn twee onderdelen van de virtueel bureau blad-service van Windows die moeten worden geverifieerd:

- De feed in de virtueel-bureaubladclient van Windows waarmee gebruikers een lijst krijgen met beschik bare Bureau bladen of toepassingen waartoe ze toegang hebben. Dit verificatie proces treedt op in Azure Active Directory, wat betekent dat dit onderdeel niet de focus van dit artikel is.
- De RDP-sessie die het resultaat is van een gebruiker om een van de beschik bare resources te selecteren. Dit onderdeel maakt gebruik van Kerberos-verificatie en vereist een KDC-proxy voor externe gebruikers.

In dit artikel wordt uitgelegd hoe u de feed in de Windows-client voor virtueel bureau blad kunt configureren in de Azure Portal. Zie [de RD-gateway rol implementeren](/windows-server/remote/rd-gateway-role)als u wilt weten hoe u de RD-gateway functie kunt configureren.

## <a name="requirements"></a>Vereisten

Als u een virtuele Windows-sessiehost met een KDC-proxy wilt configureren, hebt u het volgende nodig:

- Toegang tot de Azure Portal en een Azure-beheerders account.
- Op de externe client computers moet Windows 10 of Windows 7 worden uitgevoerd en de [Windows desktop-client](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) is geïnstalleerd.
- Er moet al een KDC-proxy op uw computer zijn geïnstalleerd. Zie [de RD-gateway rol instellen voor Windows Virtual Desktop](rd-gateway-role.md)voor meer informatie over hoe u dit doet.
- Het besturings systeem van de computer moet Windows Server 2016 of hoger zijn.

Zodra u hebt gecontroleerd of u aan deze vereisten voldoet, bent u klaar om aan de slag te gaan.

## <a name="how-to-configure-the-kdc-proxy"></a>De KDC-proxy configureren

De KDC-proxy configureren:

1. Meld u aan bij de Azure Portal als beheerder.

2. Ga naar de pagina virtueel bureau blad van Windows.

3. Selecteer de hostgroep waarvoor u de KDC-proxy wilt inschakelen en selecteer vervolgens **RDP-eigenschappen**.

    > [!div class="mx-imgBorder"]
    > ![Een scherm afbeelding van de Azure Portal pagina met een gebruiker die hostgroepen selecteert, gevolgd door de naam van de voor beeld-hostgroep en vervolgens RDP-eigenschappen.](media/rdp-properties.png)

4. Selecteer het tabblad **Geavanceerd** en geef een waarde op in de volgende notatie zonder spaties:

    
    > kdcproxyname: s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Een scherm opname waarin het tabblad Geavanceerd wordt weer gegeven, met de waarde die is ingevoerd, zoals beschreven in stap 4.](media/advanced-tab-selected.png)

5. Selecteer **Opslaan**.

6. De geselecteerde hostgroep moet nu beginnen met het verlenen van RDP-verbindings bestanden die de kdcproxyname-waarde bevatten die u in stap 4 hebt opgegeven.

## <a name="next-steps"></a>Volgende stappen

Zie [de rol RD-Gateway implementeren](/windows-server/remote/rd-gateway-role)voor meer informatie over het beheren van de Extern bureaublad-services-zijde van de KDC-proxy en het toewijzen van de RD-gateway-rol.

Als u geïnteresseerd bent in het schalen van uw KDC-proxy servers, leert u hoe u hoge Beschik baarheid voor KDC-proxy kunt instellen bij [hoge Beschik baarheid toevoegen aan de extern bureau blad-web-en gateway-Webfront](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).
