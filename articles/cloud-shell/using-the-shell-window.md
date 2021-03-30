---
title: Het Azure Cloud Shell venster gebruiken | Microsoft Docs
description: Overzicht van het gebruik van het Azure Cloud Shell-venster.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "70860316"
---
# <a name="using-the-azure-cloud-shell-window"></a>Het Azure Cloud Shell venster gebruiken

In dit document wordt uitgelegd hoe u het Cloud Shell-venster gebruikt.

## <a name="swap-between-bash-and-powershell-environments"></a>Wisselen tussen bash-en Power shell-omgevingen

Gebruik de omgevings kiezer op de Cloud Shell werk balk om te wisselen tussen de bash-en Power shell-omgevingen.  
![Omgeving selecteren](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Opnieuw opstarten Cloud Shell
Klik op het pictogram opnieuw opstarten op de werk balk Cloud Shell om de machine status opnieuw in te stellen.  
![Opnieuw opstarten Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Als Cloud Shell opnieuw wordt gestart, wordt de machine status opnieuw ingesteld en worden alle bestanden die niet worden bewaard door de Azure-bestands share, verloren gegaan.

## <a name="change-the-text-size"></a>De teken grootte wijzigen
Klik op het pictogram instellingen in de linkerbovenhoek van het venster, plaats de muis aanwijzer op de optie tekst grootte en selecteer de gewenste tekst grootte. Uw selectie wordt opgeslagen in alle sessies.
![Teken grootte](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Het letter type wijzigen
Klik op het pictogram instellingen in de linkerbovenhoek van het venster, beweeg de muis aanwijzer over de optie letter type en selecteer het gewenste letter type.  Uw selectie wordt opgeslagen in alle sessies.
![Lettertype](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Bestanden uploaden en downloaden
Klik op het pictogram bestanden uploaden/downloaden in de linkerbovenhoek van het venster en selecteer vervolgens uploaden of downloaden.  
![Bestanden uploaden/downloaden](media/using-the-shell-window/uploaddownload.png)
* Voor het uploaden van bestanden gebruikt u de pop-up om naar het bestand op uw lokale computer te bladeren, selecteert u het gewenste bestand en klikt u op de knop openen.  Het bestand wordt geüpload naar de `/home/user` map.
* Voor het downloaden van het bestand voert u het volledig gekwalificeerde bestandspad in het pop-upvenster in (dat wil zeggen, in principe een pad in de `/home/user` map die standaard wordt weer gegeven) en selecteert u de knop downloaden.  
> [!NOTE] 
> Bestanden en bestands paden zijn hoofdletter gevoelig in Cloud Shell. Controleer uw behuizing in het bestandspad.

## <a name="open-another-cloud-shell-window"></a>Een ander Cloud Shell venster openen
Met Cloud Shell kunt u meerdere gelijktijdige sessies over browser tabbladen door toestaan dat elke sessie als een afzonderlijk proces bestaat.
Als u een sessie afsluit, moet u ervoor zorgen dat elk sessie venster wordt afgesloten, hoewel elk proces onafhankelijk wordt uitgevoerd op dezelfde computer.  
Klik op het pictogram nieuwe sessie openen in de linkerbovenhoek van het venster. Er wordt een nieuw tabblad geopend met een andere sessie die is verbonden met de bestaande container.
![Nieuwe sessie openen](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell editor
* Raadpleeg de pagina [met de Azure Cloud shell-editor](using-cloud-shell-editor.md) .

## <a name="web-preview"></a>Web preview
Klik op het pictogram webvoorbeeld in de linkerbovenhoek van het venster, selecteer configureren, geef de gewenste poort op om te openen.  Selecteer ' poort openen ' om de poort alleen te openen, of open en bladeren om de poort te openen en een voor beeld van de poort op een nieuw tabblad te bekijken.  
![Web preview](media/using-the-shell-window/preview.png)  
<br>
![Poort configureren](media/using-the-shell-window/preview-configure.png)  
Klik op het pictogram webvoorbeeld in de linkerbovenhoek van het venster, selecteer ' poort voorvertonen... ' voor een voor beeld van een open poort op een nieuw tabblad. Klik op het pictogram webvoorbeeld in de linkerbovenhoek van het venster en selecteer poort sluiten... om de open poort te sluiten.  
![Poort bekijken/sluiten](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Cloud Shell venster minimaliseren & maximaliseren
Klik op het pictogram minimaliseren in de rechter bovenhoek van het venster om het te verbergen. Klik nogmaals op het pictogram Cloud Shell om het weer zichtbaar te maken.
Klik op het pictogram maximaliseren om het venster in te stellen op maximum hoogte. Als u de vorige grootte van het venster wilt herstellen, klikt u op herstellen.  
![Het venster minimaliseren of maximaliseren](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopiëren en plakken
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Venster formaat wijzigen Cloud Shell
Klik en sleep de bovenrand van de werk balk omhoog of omlaag om het formaat van het Cloud Shell venster te wijzigen.

## <a name="scrolling-text-display"></a>Lichtkrant tekst weer geven
Schuif door de muis of touchpad om Terminal tekst te verplaatsen.

## <a name="exit-command"></a>Opdracht Afsluiten
`exit`De actieve sessie wordt beëindigd. Dit gedrag treedt standaard na 20 minuten zonder interactie op.

## <a name="next-steps"></a>Volgende stappen

[Bash in Cloud Shell Quick Start](quickstart.md) <br>
[Power shell in Cloud Shell Quick Start](quickstart-powershell.md)
