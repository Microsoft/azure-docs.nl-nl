---
title: Azure SQL Edge implementeren op turbines in een windmolenpark van Contoso
description: In deze zelfstudie gebruikt u Azure SQL Edge voor de detectie van zogturbulentie bij de turbines in een windmolenpark van Contoso.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97723470"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Azure SQL Edge gebruiken om slimmere hernieuwbare bronnen te bouwen

Deze demo van Azure SQL Edge is gebaseerd op hernieuwbare energiebronnen van Contoso in een windmolenpark dat gebruikmaakt van SQL DB Edge voor gegevensverwerking in de generator. 

Deze demo begeleidt u bij het oplossen van een waarschuwing die wordt gegenereerd doordat er windturbulentie bij het apparaat wordt gedetecteerd. U traint een model en implementeert dit naar SQL DB Edge dat de gedetecteerde windturbulentie corrigeert waardoor de stroomuitvoer uiteindelijk kan worden geoptimaliseerd.

Azure SQL Edge: demovideo over hernieuwbare energie op kanaal 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>De demo opzetten op uw lokale computer
Git wordt gebruikt om alle bestanden van de demo naar uw lokale computer te kopiëren. 

1. Installeer Git van [hier](https://git-scm.com/download).
2. Open een opdrachtprompt en navigeer naar een map waar de opslagplaats moet worden gedownload. 
3. Geef de opdracht https://github.com/microsoft/sql-server-samples.git.
4. Ga naar **'sql-server-samples\samples\demos\azure-sql-edge-demos\Wind Turbine Demo'** op de locatie waar de opslagplaats naar is gekloond.
5. Volg de instructies in README.md om de demo-omgeving op te zetten en de demo uit te voeren.