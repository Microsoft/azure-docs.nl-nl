---
title: Prestaties verbeteren door bestanden te comprimeren in Azure CDN | Microsoft Docs
description: Meer informatie over het verbeteren van de snelheid van bestands overdracht en het verg Roten van de prestaties van de pagina laden door uw bestanden in Azure CDN te comprimeren.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: ceed62d466627d6a23554229bd6f4b96c674c7e9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993666"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>De prestaties verbeteren door bestanden in Azure CDN te comprimeren
Bestands compressie is een eenvoudige en efficiënte methode om de snelheid van de bestands overdracht te verbeteren en de prestaties van de pagina belasting te verhogen door de grootte van een bestand te verkleinen voordat het wordt verzonden vanaf de-server. Bestands compressie kan de bandbreedte kosten verlagen en biedt uw gebruikers een meer reactie tijd.

Er zijn twee manieren om bestands compressie in te scha kelen:

- Schakel compressie in op uw oorspronkelijke server. In dit geval worden de gecomprimeerde bestanden door Azure CDN door gegeven en worden deze geleverd aan clients die deze aanvragen.
- Schakel compressie direct in op de CDN POP-servers (*compressie aan het vliegen*). In dit geval comprimeert het CDN de bestanden en worden ze naar de eind gebruikers gehandeld, zelfs als ze niet zijn gecomprimeerd door de oorspronkelijke server.

> [!IMPORTANT]
> Wijzigingen in de configuratie van Azure CDN kan enige tijd duren voordat het netwerk kan worden door gegeven: 
> - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
> - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
> - Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden normaal gesproken binnen 10 minuten doorgegeven. 
> 
> Als u voor de eerste keer compressie voor uw CDN-eind punt instelt, moet u 1-2 uur wachten voordat u het probleem oplost om te controleren of de compressie-instellingen zijn door gegeven aan de Pop's.

## <a name="enabling-compression"></a>Compressie inschakelen
De standaard-en Premium-CDN-laag bieden dezelfde compressie functionaliteit, maar de gebruikers interface verschilt. Zie [Azure CDN Overview](cdn-overview.md)voor meer informatie over de verschillen tussen de standaard-en Premium-CDN-lagen.

### <a name="standard-cdn-profiles"></a>Standaard CDN-profielen 
> [!NOTE]
> Deze sectie is van toepassing op **Azure CDN Standard van micro soft**, **Azure CDN Standard van Verizon** en **Azure CDN Standard van Akamai** Profiles.
> 
> 

1. Selecteer op de pagina CDN-profiel het CDN-eind punt dat u wilt beheren.

    ![Eind punten van CDN-profiel](./media/cdn-file-compression/cdn-endpoints.png)

    De pagina van het CDN-eind punt wordt geopend.
2. Selecteer **compressie**.

    ![Scherm afbeelding toont een eind punt met compressie geselecteerd in het menu van de portal.](./media/cdn-file-compression/cdn-compress-select-std.png)

    De compressie pagina wordt geopend.
3. Selecteer **aan om compressie in te** scha kelen.

    ![Scherm opname toont het inschakelen van compressie.](./media/cdn-file-compression/cdn-compress-standard.png)
4. Gebruik de standaard MIME-typen of wijzig de lijst door MIME-typen toe te voegen of te verwijderen.

   > [!TIP]
   > Hoewel het mogelijk is, wordt het niet aanbevolen compressie toe te passen op gecomprimeerde indelingen. Bijvoorbeeld: ZIP, MP3, MP4 of JPG.
   > 

   > [!NOTE]
   > Het wijzigen van de standaard lijst met MIME-typen wordt momenteel niet ondersteund in Azure CDN standaard van micro soft.
   > 

5. Nadat u de wijzigingen hebt aangebracht, selecteert u **Opslaan**.

### <a name="premium-cdn-profiles"></a>Premium CDN-profielen
> [!NOTE]
> Deze sectie is alleen van toepassing op **Azure CDN Premium van Verizon** -profielen.
> 

1. Selecteer op de pagina CDN-profiel de optie **beheren**.

    ![CDN beheren selecteren](./media/cdn-file-compression/cdn-manage-btn.png)

    De CDN-beheer portal wordt geopend.
2. Beweeg de muis aanwijzer over het tabblad **http large** en Beweeg vervolgens de muis aanwijzer over de flyout met de **cache-instellingen** . Selecteer **compressie**.

    ![CDN-compressie selectie](./media/cdn-file-compression/cdn-compress-select.png)

    De compressie opties worden weer gegeven.

    ![Opties voor compressie van CDN-bestand](./media/cdn-file-compression/cdn-compress-files.png)
3. Schakel compressie in door **compressie ingeschakeld** te selecteren. Voer in het vak **Bestands typen** de MIME-typen in die u wilt comprimeren als een door komma's gescheiden lijst (geen spaties).

   > [!TIP]
   > Hoewel het mogelijk is, wordt het niet aanbevolen compressie toe te passen op gecomprimeerde indelingen. Bijvoorbeeld: ZIP, MP3, MP4 of JPG.
   > 

4. Nadat u de wijzigingen hebt aangebracht, selecteert u **bijwerken**.

## <a name="compression-rules"></a>Compressie regels

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN standaard van micro soft-profielen

Voor **Azure CDN standaard van micro soft** -profielen worden alleen in aanmerking komende bestanden gecomprimeerd. Om in aanmerking te komen voor compressie, moet een bestand:
- Een MIME-type zijn dat is [geconfigureerd voor compressie](#enabling-compression).
- Groter zijn dan 1 KB
- Kleiner zijn dan 8 MB

Deze profielen ondersteunen de volgende compressie coderingen:
- gzip (GNU-zip)
- brotli 

Als de aanvraag meer dan één compressie type ondersteunt, heeft brotli-compressie prioriteit.

Wanneer een aanvraag voor een activum gzip-compressie specificeert en de aanvraag resulteert in een Missing in een cache, voert Azure CDN gzip-compressie van het element rechtstreeks op de POP-server uit. Daarna wordt het gecomprimeerde bestand vanuit de cache verwerkt.

Als de oorsprong gebruikmaakt van een CTE (gesegmenteerde overdrachts codering) voor het verzenden van gecomprimeerde gegevens naar de CDN-POP, worden de antwoord groottes die groter zijn dan 8 MB, niet ondersteund. 

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN van Verizon-profielen

Voor **Azure CDN standaard van Verizon** en **Azure CDN Premium van Verizon** -profielen, worden alleen de bestanden gecomprimeerd die in aanmerking komen. Om in aanmerking te komen voor compressie, moet een bestand:
- Groter zijn dan 128 bytes
- Kleiner zijn dan 3 MB

Deze profielen ondersteunen de volgende compressie coderingen:
- gzip (GNU-zip)
- DEFLATE
- bzip2
- brotli 

Als de aanvraag meer dan één compressie type ondersteunt, hebben die compressie typen voor rang op brotli-compressie.

Wanneer een aanvraag voor een Asset brotli compressie specificeert (HTTP-header is `Accept-Encoding: br` ) en de aanvraag resulteert in een Missing in de cache, voert Azure CDN brotli-compressie van de Asset rechtstreeks op de pop-server uit. Daarna wordt het gecomprimeerde bestand vanuit de cache verwerkt.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN standaard van Akamai-profielen

Voor **Azure CDN standaard van Akamai** -profielen, komen alle bestanden in aanmerking voor compressie. Een bestand moet echter een MIME-type zijn dat is [geconfigureerd voor compressie](#enabling-compression).

Deze profielen ondersteunen alleen gzip-compressie codering. Wanneer een profiel eindpunt een bestand met gzip-code ring aanvraagt, wordt het altijd aangevraagd vanuit de oorsprong, ongeacht de client aanvraag. 

## <a name="compression-behavior-tables"></a>Tabellen voor compressie gedrag
In de volgende tabellen wordt Azure CDN compressie gedrag voor elk scenario beschreven:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Compressie is uitgeschakeld of het bestand komt niet in aanmerking voor compressie
| Door de client aangevraagde indeling (via Accept-Encoding header) | Cache-bestands indeling | Het CDN-antwoord naar de client | &nbsp; &nbsp; &nbsp; &nbsp; Opmerkingen &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Gecomprimeerd |Gecomprimeerd |Gecomprimeerd | |
| Gecomprimeerd |Gecomprimeerde |Gecomprimeerde | |
| Gecomprimeerd |Niet in cache |Gecomprimeerd of niet-gecomprimeerd |Het antwoord van de oorsprong bepaalt of CDN een compressie uitvoert. |
| Gecomprimeerde |Gecomprimeerd |Gecomprimeerde | |
| Gecomprimeerde |Gecomprimeerde |Gecomprimeerde | |
| Gecomprimeerde |Niet in cache |Gecomprimeerde | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Compressie is ingeschakeld en het bestand komt in aanmerking voor compressie
| Door de client aangevraagde indeling (via Accept-Encoding header) | Cache-bestands indeling | CDN-antwoord naar de client | Notities |
| --- | --- | --- | --- |
| Gecomprimeerd |Gecomprimeerd |Gecomprimeerd |CDN transcodeert tussen ondersteunde indelingen. |
| Gecomprimeerd |Gecomprimeerde |Gecomprimeerd |CDN voert een compressie uit. |
| Gecomprimeerd |Niet in cache |Gecomprimeerd |CDN voert een compressie uit als de oorsprong een niet-gecomprimeerd bestand retourneert. <br/>**Azure CDN van Verizon** geeft het ongecomprimeerde bestand in de eerste aanvraag door en comprimeert het bestand voor volgende aanvragen en slaat het op in het cache geheugen. <br/>Bestanden met de `Cache-Control: no-cache` header worden nooit gecomprimeerd. |
| Gecomprimeerde |Gecomprimeerd |Gecomprimeerde |CDN voert een decompressie uit. |
| Gecomprimeerde |Gecomprimeerde |Gecomprimeerde | |
| Gecomprimeerde |Niet in cache |Gecomprimeerde | |

## <a name="media-services-cdn-compression"></a>Media Services CDN-compressie
Voor eind punten die zijn ingeschakeld voor Media Services CDN-streaming, wordt compressie standaard ingeschakeld voor de volgende MIME-typen: 
- application/vnd. MS-sstr + XML 
- toepassing/streepje + XML
- URL van de toepassing/vnd.apple.mpeg
- Application/F4M + XML 

## <a name="see-also"></a>Zie ook
* [Problemen met CDN-bestandscompressie oplossen](cdn-troubleshoot-compression.md)    

