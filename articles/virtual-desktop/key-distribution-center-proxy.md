---
title: Kerberos-proxy-Key Distribution Center instellen Windows Virtual Desktop - Azure
description: Het instellen van een Windows Virtual Desktop hostgroep voor het gebruik van een Kerberos-Key Distribution Center proxy.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 9dce264b7f2c88aed11f5b82a61f83cbac6c9697
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785105"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Een Kerberos-proxy Key Distribution Center configureren (preview)

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als openbare preview-versie.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Beveiligingsbewuste klanten, zoals financiële organisaties of overheidsorganisaties, melden zich vaak aan met smartcards. Smartcards maken implementaties veiliger door meervoudige verificatie (MFA) te vereisen. Voor het RDP-gedeelte van een Windows Virtual Desktop-sessie is voor smartcards echter een directe verbinding of 'zichtlijn' vereist met een Active Directory-domeincontroller (AD) voor Kerberos-verificatie. Zonder deze directe verbinding kunnen gebruikers zich niet automatisch via externe verbindingen aanmelden bij het netwerk van de organisatie. Gebruikers in een Windows Virtual Desktop kunnen de KDC-proxyservice gebruiken om dit verificatieverkeer via proxy te proxy's en zich op afstand aan te melden. De KDC-proxy staat verificatie toe voor de Remote Desktop Protocol van Windows Virtual Desktop sessie, zodat de gebruiker zich veilig kan aanmelden. Hierdoor is het veel eenvoudiger om thuis te werken en kunnen bepaalde noodherstelscenario's soepeler worden uitgevoerd.

Het instellen van de KDC-proxy omvat echter meestal het toewijzen van de Windows Server Gateway-rol in Windows Server 2016 of hoger. Hoe gebruikt u een Extern bureaublad-services om u aan te melden bij Windows Virtual Desktop? Om dat te beantwoorden, gaan we kort naar de onderdelen kijken.

De service Windows Virtual Desktop twee onderdelen die moeten worden geverifieerd:

- De feed in de Windows Virtual Desktop-client die gebruikers een lijst geeft met beschikbare desktops of toepassingen waar ze toegang toe hebben. Dit verificatieproces vindt plaats in Azure Active Directory, wat betekent dat dit onderdeel niet de focus van dit artikel is.
- De RDP-sessie die het resultaat is van een gebruiker die een van deze beschikbare resources selecteert. Dit onderdeel maakt gebruik van Kerberos-verificatie en vereist een KDC-proxy voor externe gebruikers.

In dit artikel wordt beschreven hoe u de feed configureert in de Windows Virtual Desktop client in de Azure Portal. Zie Deploy the RD-gateway role (De rol RD-gateway implementeren) als u wilt weten hoe u de RD-gateway [configureert.](/azure/virtual-desktop/rd-gateway-role)

## <a name="requirements"></a>Vereisten

Als u een Windows Virtual Desktop sessiehost wilt configureren met een KDC-proxy, hebt u het volgende nodig:

- Toegang tot de Azure Portal en een Azure-beheerdersaccount.
- Op de externe clientcomputers moet Windows 10 of Windows 7 worden uitgevoerd en moet de [Windows Desktop-client zijn](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) geïnstalleerd. Momenteel wordt de webclient niet ondersteund.
- U moet al een KDC-proxy op uw computer hebben geïnstalleerd. Zie Set up the [RD-gateway role for Windows Virtual Desktop (De](rd-gateway-role.md)rol RD-gateway instellen voor de Windows Virtual Desktop).
- Het besturingssysteem van de computer moet Windows Server 2016 of hoger zijn.

Zodra u zeker weet dat u aan deze vereisten voldoet, bent u klaar om aan de slag te gaan.

## <a name="how-to-configure-the-kdc-proxy"></a>De KDC-proxy configureren

De KDC-proxy configureren:

1. Meld u aan bij Azure Portal beheerder.

2. Ga naar de Windows Virtual Desktop pagina.

3. Selecteer de hostgroep voor wie u de KDC-proxy wilt inschakelen en selecteer **vervolgens RDP-eigenschappen.**

    > [!div class="mx-imgBorder"]
    > ![Een schermopname van de Azure Portal met een gebruiker die hostgroepen selecteert, vervolgens de naam van de voorbeeldhostgroep en vervolgens RDP-eigenschappen.](media/rdp-properties.png)

4. Selecteer het **tabblad** Geavanceerd en voer een waarde in de volgende notatie in zonder spaties:

    
    > kdcproxyname:s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Een schermopname met het tabblad Geavanceerd geselecteerd, met de waarde die is ingevoerd zoals beschreven in stap 4.](media/advanced-tab-selected.png)

5. Selecteer **Opslaan**.

6. De geselecteerde hostgroep moet nu beginnen met het uitgeven van RDP-verbindingsbestanden die de waarde kdcproxyname bevatten die u in stap 4 hebt ingevoerd.

## <a name="next-steps"></a>Volgende stappen

Zie Deploy the RD-gateway role (De RD-gateway implementeren) voor meer informatie over het beheren van de Extern bureaublad-services van de KDC-proxy en het toewijzen van de [RD-gateway-rol.](rd-gateway-role.md)

Als u geïnteresseerd bent in het schalen van uw KDC-proxyservers, kunt u leren hoe u hoge beschikbaarheid voor de KDC-proxy in kunt stellen via Hoge beschikbaarheid toevoegen aan het [RD-web](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)en gatewayweb.
