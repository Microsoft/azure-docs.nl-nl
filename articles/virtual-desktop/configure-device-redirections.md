---
title: Apparaatomleidingen configureren - Azure
description: Apparaatomleidingen configureren voor Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 37ecd06c4e3e71234e8fb1b6bad0cd05482dd31b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727843"
---
# <a name="configure-device-redirections"></a>Apparaatomleidingen configureren

Als u apparaatomleidingen configureert voor Windows Virtual Desktop omgeving, kunt u printers, USB-apparaten, microfoons en andere randapparatuur gebruiken in de externe sessie. Sommige apparaatomleidingen vereisen wijzigingen in zowel de eigenschappen Remote Desktop Protocol (RDP) als groepsbeleid instellingen.

## <a name="supported-device-redirections"></a>Ondersteunde apparaatomleidingen

Elke client ondersteunt verschillende apparaatomleidingen. Bekijk De [clients vergelijken voor](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) de volledige lijst met ondersteunde apparaatomleidingen voor elke client.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>RDP-eigenschappen voor een hostgroep aanpassen

Zie RDP-eigenschappen voor meer informatie over het aanpassen van [RDP-eigenschappen](customize-rdp-properties.md)voor een hostgroep met behulp van PowerShell of de Azure Portal. Zie Ondersteunde RDP-bestandsinstellingen voor de volledige lijst met ondersteunde [RDP-eigenschappen.](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)

## <a name="setup-device-redirections"></a>Apparaatomleidingen instellen

U kunt de volgende RDP-eigenschappen en instellingen groepsbeleid om apparaatomleidingen te configureren.

### <a name="audio-input-microphone-redirection"></a>Omleiding van audio-invoer (microfoon)

Stel de volgende RDP-eigenschap in om audio-invoeromleiding te configureren:

- `audiocapturemode:i:1` schakelt omleiding van audio-invoer in.
- `audiocapturemode:i:0` schakelt omleiding van audio-invoer uit.

### <a name="audio-output-speaker-redirection"></a>Omleiding van audio-uitvoer (spreker)

Stel de volgende RDP-eigenschap in om omleiding van audio-uitvoer te configureren:

- `audiomode:i:0` schakelt omleiding van audio-uitvoer in.
- `audiomode:i:1` of `audiomode:i:2` schakel omleiding van audio-uitvoer uit.

### <a name="camera-redirection"></a>Camera-omleiding

Stel de volgende RDP-eigenschap in om camera-omleiding te configureren:

- `camerastoredirect:s:*` leidt alle camera's om.
- `camerastoredirect:s:` schakelt camera-omleiding uit.

>[!NOTE]
>Zelfs als de `camerastoredirect:s:` eigenschap is uitgeschakeld, kunnen lokale camera's worden omgeleid via de `devicestoredirect:s:` eigenschap . Om de camera-omleidingsset volledig uit te schakelen en een subset van plug en play-apparaten in te stellen of te definiëren die `camerastoredirect:s:` `devicestoredirect:s:` geen camera bevatten.

U kunt ook specifieke camera's omleiden met behulp van een door puntkomma's KSCATEGORY_VIDEO_CAMERA interfaces, zoals `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` .

### <a name="clipboard-redirection"></a>Klembordomleiding

Stel de volgende RDP-eigenschap in om klembordomleiding te configureren:

- `redirectclipboard:i:1` schakelt klembordomleiding in.
- `redirectclipboard:i:0` schakelt klembordomleiding uit.

### <a name="com-port-redirections"></a>COM-poortomleidingen

Stel de volgende RDP-eigenschap in om com-poortomleiding te configureren:

- `redirectcomports:i:1` schakelt COM-poortomleiding in.
- `redirectcomports:i:0` com-poortomleiding uitgeschakeld.

### <a name="usb-redirection"></a>USB-omleiding

Stel eerst de volgende RDP-eigenschap in om usb-apparaatomleiding in te stellen:

- `usbdevicestoredirect:s:*` schakelt usb-apparaatomleiding in.
- `usbdevicestoredirect:s:` schakelt omleiding van USB-apparaat uit.

Stel ten tweede de volgende groepsbeleid in op het lokale apparaat van de gebruiker:

- **Navigeer naar Computerconfiguratiebeleid**  >   >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **Extern bureaublad-services**  >  **Verbinding met extern bureaublad Client**  >  **RemoteFX USB-apparaatomleiding.**
- Selecteer **RDP-omleiding van andere ondersteunde RemoteFX USB-apparaten vanaf deze computer.**
- Selecteer de **optie Ingeschakeld** en selecteer vervolgens het vak Beheerders en gebruikers **in RemoteFX USB-omleidingstoegangsrechten.**
- Selecteer **OK**.

### <a name="plug-and-play-device-redirection"></a>Apparaatomleiding voor Plug en Play

Stel de volgende RDP-eigenschap in om omleiding van plug en play-apparaten te configureren:

- `devicestoredirect:s:*` maakt omleiding van alle Plug en Play-apparaten mogelijk.
- `devicestoredirect:s:` schakelt omleiding van plug en play-apparaten uit.

U kunt ook specifieke plug en play-apparaten selecteren met behulp van een lijst met puntkomma's met scheidingstekens, zoals `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Omleiding van lokale station

Stel de volgende RDP-eigenschap in om omleiding van lokale station te configureren:

- `drivestoredirect:s:*` maakt omleiding van alle schijfstations mogelijk.
- `drivestoredirect:s:` schakelt omleiding van lokale station.

U kunt ook specifieke stations selecteren met behulp van een lijst met puntkomma's met scheidingstekens, zoals `drivestoredirect:s:C:;E:;` .

Als u de bestandsoverdracht van de webclient wilt configureren, stelt u `drivestoredirect:s:*` in.

### <a name="printer-redirection"></a>Printeromleiding

Stel de volgende RDP-eigenschap in om printeromleiding te configureren:

- `redirectprinters:i:1` schakelt printeromleiding in.
- `redirectprinters:i:0` schakelt printeromleiding.

### <a name="smart-card-redirection"></a>Smartcardomleiding

Stel de volgende RDP-eigenschap in om smartcardomleiding te configureren:

- `redirectsmartcards:i:1` schakelt smartcardomleiding in.
- `redirectsmartcards:i:0` schakelt smartcardomleiding uit.
