---
title: Specifieke RDP-fout berichten voor virtuele Azure-machines | Microsoft Docs
description: Meer informatie over specifieke fout berichten die kunnen worden weer gegeven wanneer u probeert Extern bureaublad verbinding te gebruiken met een virtuele Windows-machine in azure
keywords: Fout met extern bureau blad, fout met verbinding met extern bureau blad, kan geen verbinding maken met VM, problemen oplossen met extern bureau blad
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 808443cb805b2dee2426198f9cd4f7ba502d8999
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197503"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Specifieke RDP-foutberichten oplossen naar een virtuele Windows-machine in Azure
Er wordt mogelijk een specifiek fout bericht weer gegeven wanneer Extern bureaublad verbinding met een virtuele machine van Windows (VM) in azure wordt gebruikt. In dit artikel worden enkele veelvoorkomende fout berichten beschreven, samen met de stappen voor probleem oplossing om deze op te lossen. Als u problemen ondervindt met het maken van verbinding met uw virtuele machine via RDP, maar geen specifiek fout bericht tegen komt, raadpleegt u de [hand leiding voor het oplossen van problemen voor extern bureaublad](troubleshoot-rdp-connection.md).

Voor informatie over specifieke fout berichten raadpleegt u het volgende:

* [De externe sessie is beëindigd omdat er geen extern bureaublad licentie servers beschikbaar zijn om een licentie te bieden](#rdplicense).
* [Extern bureaublad kan de computer naam niet vinden](#rdpname).
* [Er is een verificatie fout opgetreden. Kan geen verbinding maken met de lokale beveiligings autoriteit](#rdpauth).
* [Windows-beveiligings fout: uw referenties werken niet](#wincred).
* [Deze computer kan geen verbinding maken met de externe computer](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>De externe sessie is beëindigd omdat er geen Extern bureaublad licentie servers beschikbaar zijn om een licentie te bieden.
Oorzaak: de licentie respijt periode van 120 dagen voor de Extern bureaublad serverrol is verlopen en u moet licenties installeren.

U kunt dit probleem verhelpen door een lokale kopie van het RDP-bestand op te slaan vanuit de portal en deze opdracht uit te voeren vanaf een Power shell-opdracht prompt om verbinding te maken. Met deze stap wordt de licentie verlening voor alleen die verbinding uitgeschakeld:

```powershell
mstsc <File name>.RDP /admin
```

Als u niet echt meer dan twee gelijktijdige Extern bureaublad verbindingen met de virtuele machine nodig hebt, kunt u Serverbeheer gebruiken om de Extern bureaublad server functie te verwijderen.

Zie het blog bericht [Azure VM mislukt met ' geen extern bureaublad licentie servers beschikbaar '](/archive/blogs/mast/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available)voor meer informatie.

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Extern bureaublad kan de computer naam niet vinden.
Oorzaak: de Extern bureaublad-client op uw computer kan de naam van de computer in de instellingen van het RDP-bestand niet omzetten.

Mogelijke oplossingen:

* Als u zich op het intranet van een organisatie bevindt, moet u ervoor zorgen dat de computer toegang heeft tot de proxy server en daar HTTPS-verkeer naar kunnen verzenden.
* Als u een lokaal opgeslagen RDP-bestand gebruikt, probeert u het te gebruiken dat is gegenereerd door de portal. Met deze stap zorgt u ervoor dat u de juiste DNS-naam voor de virtuele machine of de Cloud service en de eindpunt poort van de VM hebt. Hier volgt een voor beeld van een RDP-bestand dat door de portal wordt gegenereerd:

    ```output
    full address:s:tailspin-azdatatier.cloudapp.net:55919
    prompt for credentials:i:1
    ```

Het adres gedeelte van dit RDP-bestand heeft:

* De Fully Qualified Domain Name van de Cloud service die de virtuele machine bevat (' tailspin-azdatatier.cloudapp.net ' in dit voor beeld).
* De externe TCP-poort van het eind punt voor Extern bureaublad verkeer (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Er is een verificatie fout opgetreden. Kan geen verbinding maken met de lokale beveiligings autoriteit.
Oorzaak: de doel-VM kan de beveiligings instantie niet vinden in het gedeelte met de gebruikers naam van uw referenties.

Als uw gebruikers naam in de vorm *SecurityAuthority* \\ *username* (voor beeld: corp\gebruiker1.) is, is het gedeelte *SecurityAuthority* de computer naam van de virtuele machine (voor de lokale beveiligings instantie) of een Active Directory domein naam.

Mogelijke oplossingen:

* Als het account lokaal is voor de virtuele machine, moet u ervoor zorgen dat de naam van de virtuele machine correct is gespeld.
* Als het account zich op een Active Directory domein bevindt, controleert u de spelling van de domein naam.
* Als het een Active Directory domein account is en de domein naam correct is gespeld, controleert u of er een domein controller beschikbaar is in dat domein. Het is een veelvoorkomend probleem in virtuele Azure-netwerken dat domein controllers bevat die geen domein controller is, omdat deze niet is gestart. Als tijdelijke oplossing kunt u een lokaal Administrator-account gebruiken in plaats van een domein account.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Windows-beveiligings fout: uw referenties werken niet.
Oorzaak: de doel-VM kan de account naam en het wacht woord niet valideren.

Een op Windows gebaseerde computer kan de referenties van een lokaal account of een domein account valideren.

* Voor lokale accounts gebruikt u de syntaxis *computer* \\ *naam username* (bijvoorbeeld: SQL1\Admin4798).
* Gebruik voor domein accounts de syntaxis van de *domein* \\ *naam username* (bijvoorbeeld: CONTOSO\peterodman).

Als u uw VM hebt gepromoveerd naar een domein controller in een nieuw Active Directory-forest, wordt het lokale beheerders account waarmee u zich hebt aangemeld, geconverteerd naar een gelijkwaardig account met hetzelfde wacht woord in het nieuwe forest en domein. Het lokale account wordt verwijderd.

Als u zich bijvoorbeeld hebt aangemeld met het lokale account DC1\DCAdmin en vervolgens de virtuele machine hebt gepromoveerd als een domein controller in een nieuw forest voor het domein corp.contoso.com, wordt het lokale account DC1\DCAdmin verwijderd en wordt er een nieuw domein account (CORP\DCAdmin) gemaakt met hetzelfde wacht woord.

Zorg ervoor dat de account naam een naam is die door de virtuele machine kan worden geverifieerd als een geldig account en dat het wacht woord juist is.

Als u het wacht woord van het lokale beheerders account wilt wijzigen, raadpleegt [u het opnieuw instellen van een wacht woord of de Extern bureaublad-service voor virtuele Windows-machines](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Deze computer kan geen verbinding maken met de externe computer.
Oorzaak: het account dat wordt gebruikt om verbinding te maken, beschikt niet over Extern bureaublad aanmeldings rechten.

Elke Windows-computer heeft een lokale groep Extern bureaublad gebruikers, die de accounts en groepen bevat waarmee deze extern kan worden aangemeld. Leden van de lokale groep Administrators hebben ook toegang, zelfs als deze accounts niet worden weer gegeven in de lokale groep Extern bureaublad gebruikers. Voor computers die lid zijn van een domein, bevat de lokale beheerders groep ook de domein beheerders voor het domein.

Zorg ervoor dat het account dat u gebruikt om verbinding te maken, Extern bureaublad aanmeldings rechten heeft. Als tijdelijke oplossing kunt u een domein-of lokale beheerders account gebruiken om verbinding te maken via Extern bureaublad. Als u het gewenste account wilt toevoegen aan de lokale groep Extern bureaublad gebruikers, gebruikt u de micro soft Management Console-module (**systeem hulpprogramma's > lokale gebruikers en groepen > groepen > extern bureaublad gebruikers**).

## <a name="next-steps"></a>Volgende stappen
Als er geen fouten zijn opgetreden en u een onbekend probleem hebt met het maken van verbinding met RDP, raadpleegt u de [hand leiding voor het oplossen van problemen voor extern bureaublad](troubleshoot-rdp-connection.md).

* Zie [problemen oplossen met toegang tot een toepassing die wordt uitgevoerd op](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)een virtuele machine van Azure voor informatie over het oplossen van problemen bij het openen van toepassingen die op een VM worden uitgevoerd.
* Zie [problemen met ssh-verbindingen met een virtuele Linux-machine in azure oplossen](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)als u problemen ondervindt met het gebruik van Secure Shell (SSH) om verbinding te maken met een virtuele Linux-machine in Azure.
