---
title: Apparaatomleiding configureren-Azure
description: Apparaatomleiding configureren voor Windows virtueel bureau blad.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b60a6e53e8fecd71885204690231776ff69fc08f
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018372"
---
# <a name="configure-device-redirections"></a>Apparaatomleidingen configureren

Met het configureren van apparaatomleiding voor uw Windows Virtual Desktop-omgeving kunt u printers, USB-apparaten, micro telefoons en andere rand apparatuur in de externe sessie gebruiken. Voor sommige omleidingen van apparaten zijn wijzigingen in de eigenschappen van Remote Desktop Protocol (RDP) en groepsbeleid-instellingen nodig.

## <a name="supported-device-redirections"></a>Ondersteunde omleidingen van apparaten

Elke client ondersteunt verschillende omleidingen van apparaten. Bekijk [de clients](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) voor de volledige lijst met ondersteunde omleidingen voor apparaten voor elke client.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>RDP-eigenschappen voor een hostgroep aanpassen

Voor meer informatie over het aanpassen van RDP-eigenschappen voor een hostgroep met Power shell of de Azure Portal, raadpleegt u [RDP-eigenschappen](customize-rdp-properties.md). Zie [supported RDP file Settings](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)(Engelstalig) voor een volledige lijst met ondersteunde RDP-eigenschappen.

## <a name="setup-device-redirections"></a>Apparaatomleiding instellen

U kunt de volgende RDP-eigenschappen en groepsbeleid-instellingen gebruiken om apparaatomleiding te configureren.

### <a name="audio-input-microphone-redirection"></a>Omleiding van audio-invoer (microfoon)

Stel de volgende RDP-eigenschap in voor het configureren van omleiding van audio-invoer:

- `audiocapturemode:i:1` omleiding van audio-invoer inschakelen.
- `audiocapturemode:i:0` omleiding van audio-invoer uitschakelen.

### <a name="audio-output-speaker-redirection"></a>Omleiding van audio-uitvoer (spreker)

Stel de volgende RDP-eigenschap in om de omleiding van audio-uitvoer te configureren:

- `audiomode:i:0` Hiermee schakelt u de omleiding van audio-uitvoer in.
- `audiomode:i:1` of `audiomode:i:2` Schakel de omleiding van audio-uitvoer uit.

### <a name="camera-redirection"></a>Camera omleiding

Stel de volgende RDP-eigenschap in voor het configureren van camera omleiding:

- `camerastoredirect:s:*` alle camera's worden omgeleid.
- `camerastoredirect:s:` schakelt de omleiding van de camera uit.

>[!NOTE]
>Zelfs als de `camerastoredirect:s:` eigenschap is uitgeschakeld, kunnen lokale camera's worden omgeleid via de `devicestoredirect:s:` eigenschap. Voor het volledig uitschakelen van de omleidings voor de camera `camerastoredirect:s:` en `devicestoredirect:s:` het instellen of definiëren van een deel verzameling van Plug en Play-apparaten die geen camera bevatten.

U kunt ook specifieke camera's omleiden met een door punt komma's gescheiden lijst met KSCATEGORY_VIDEO_CAMERA interfaces, zoals `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` . 

### <a name="clipboard-redirection"></a>Klembord omleiding

Stel de volgende RDP-eigenschap in om de omleiding van het klem bord te configureren:

- `redirectclipboard:i:1` Hiermee schakelt u het omleiden van het klem bord.
- `redirectclipboard:i:0` Hiermee schakelt u de omleiding van het klem bord uit.

### <a name="com-port-redirections"></a>Omleidingen van COM-poort

Stel de volgende RDP-eigenschap in om de omleiding van COM-poort te configureren:

- `redirectcomports:i:1` Hiermee schakelt u de omleiding van COM-poort in.
- `redirectcomports:i:0` omleiding van COM-poort uitschakelen.

### <a name="usb-redirection"></a>USB-omleiding

Stel eerst de volgende RDP-eigenschap in om de omleiding van USB-apparaten in te scha kelen:

- `usbdevicestoredirect:s:*` Hiermee schakelt u de omleiding van USB-apparaten.
- `usbdevicestoredirect:s:` schakelt de omleiding van USB-apparaten uit.

Stel vervolgens de volgende groepsbeleid in op het lokale apparaat van de gebruiker:

- Navigeer naar **computer configuratie**  >  **beleid** >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **extern bureaublad-services**  >  **verbinding met extern bureaublad**  >  **USB-apparaatomleiding** van client RemoteFX.
- Selecteer **RDP-omleiding van andere ondersteunde RemoteFX USB-apparaten vanaf deze computer toestaan**.
- Selecteer de optie **ingeschakeld** en selecteer vervolgens het dialoog venster **beheerders en gebruikers in het vak toegangs rechten voor RemoteFX USB-omleiding** .
- Selecteer **OK**.

### <a name="plug-and-play-device-redirection"></a>Plug en Play-apparaatomleiding

Stel de volgende RDP-eigenschap in om de omleiding van Plug en Play-apparaten te configureren:

- `devicestoredirect:s:*` Hiermee schakelt u de omleiding van alle Plug en Play-apparaten.
- `devicestoredirect:s:` omleiding van Plug en Play-apparaten wordt uitgeschakeld.

U kunt ook specifieke plug en Play-apparaten selecteren met een door punt komma's gescheiden lijst, zoals `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Omleiding van lokale schijven

Stel de volgende RDP-eigenschap in om de omleiding van lokale schijven te configureren:

- `drivestoredirect:s:*` Hiermee schakelt u de omleiding van alle schijf stations.
- `drivestoredirect:s:` schakelt het omleiden van lokale schijven uit.

U kunt ook specifieke stations selecteren met een door punt komma's gescheiden lijst, zoals `drivestoredirect:s:C:;E:;` .

### <a name="printer-redirection"></a>Printer omleiding

Stel de volgende RDP-eigenschap in om printer omleiding te configureren:

- `redirectprinters:i:1` Hiermee schakelt u printer omleiding in.
- `redirectprinters:i:0` Hiermee schakelt u de omleiding van printers uit.

### <a name="smart-card-redirection"></a>Omleiding van Smart Cards

Stel de volgende RDP-eigenschap in om de omleiding van Smart Cards te configureren:

- `redirectsmartcards:i:1` schakelt de omleiding van Smart Cards in.
- `redirectsmartcards:i:0` schakelt de omleiding van de Smart Card uit.