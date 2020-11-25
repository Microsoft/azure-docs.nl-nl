---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5c62c67cd401c043352b06e6e6070a7fc0f1296
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995422"
---
#### <a name="to-connect-through-the-serial-console"></a>Verbinding maken via de seriële console
1. Sluit de seriële kabel aan op het apparaat (rechtstreeks of via een USB-seriële adapter).
2. Open het **Configuratiescherm** en open vervolgens **Apparaatbeheer**.
3. Kijk waar de COM-poort zich bevindt, zoals weergegeven in de volgende afbeelding.
   
     ![Verbinding maken via een seriële console](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. Start PuTTY. 
5. Wijzig in het rechterdeelvenster **Verbindingstype** in **Serieel**.
6. Typ in het rechterdeelvenster de juiste COM-poort. Zorg ervoor dat de seriële configuratieparameters als volgt zijn ingesteld:
   
   * Snelheid: 115.200
   * Gegevensbits: 8
   * Stopbits: 1
   * Pariteit: Geen
   * Datatransportbesturing: Geen
     
     Deze instellingen worden weergegeven in de volgende afbeelding.
     
     ![Instellingen voor PuTTY](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > Als de standaardinstelling voor datatransportbesturing niet werkt, kunt u de datatransportbesturing instellen op XON/XOFF.
     > 
     > 
7. Klik op **Openen** om een seriële sessie te starten.

