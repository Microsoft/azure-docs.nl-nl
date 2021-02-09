---
title: bestand opnemen
description: bestand opnemen
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 02/08/2021
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 3ee44509997a16fc7f06fd5a24e473d7011bde40
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99974632"
---
#### <a name="process-automation"></a>Procesautomatisering

| Resource | Limiet |Notities|
| --- | --- |---|
| Maximumaantal nieuwe taken dat elke 30 seconden per Azure Automation-account kan worden verzonden (niet-geplande taken) |100 |Wanneer deze limiet wordt bereikt, mislukken de volgende verzoeken om een taak te maken. De client ontvangt een foutmelding.|
| Maximumaantal gelijktijdige actieve taken om dezelfde tijd per Automation-account (niet-geplande taken) |200 |Wanneer deze limiet wordt bereikt, mislukken de volgende verzoeken om een taak te maken. De client ontvangt een foutmelding.|
| Maximale opslaggrootte van taakmetagegevens voor een doorlopende periode van 30 dagen | 10 GB (ongeveer 4 miljoen taken)|Wanneer deze limiet wordt bereikt, mislukken de volgende verzoeken om een taak te maken. |
| Maximale limiet voor taakstreams|1 MiB|Eén stroom kan niet groter zijn dan 1 MiB.|
| Maximumaantal modules dat elke 30 seconden per Automation-account kan worden geïmporteerd |5 ||
| Maximale grootte van een module |100 MB ||
| Maximale grootte van een configuratie bestand voor het knoop punt | 1 MB | Van toepassing op status configuratie |
| Taak-runtime, Gratis laag |500 minuten per abonnement per kalendermaand ||
| Maximale hoeveelheid schijfruimte die per sandbox is toegestaan<sup>1</sup> |1 GB |Alleen van toepassing op Azure-sandboxes.|
| Maximale hoeveelheid geheugen die aan een sandbox wordt gegeven<sup>1</sup> |400 MB |Alleen van toepassing op Azure-sandboxes.|
| Maximumaantal netwerksockets dat per sandbox is toegestaan<sup>1</sup> |1000 |Alleen van toepassing op Azure-sandboxes.|
| Maximale runtime die per runbook is toegestaan<sup>1</sup> |3 uur |Alleen van toepassing op Azure-sandboxes.|
| Maximumaantal Automation-accounts in een abonnement |Geen limiet ||
| Maximumaantal hybride werkrolgroepen per Automation-account|4000||
|Maximumaantal gelijktijdige taken dat op één hybride runbook-werkrol kan worden uitgevoerd|50 ||
| Maximale parametergrootte voor runbook-taken   | 512 KB||
| Maximumaantal runbook-parameters   | 50|Als u de limiet van 50 parameters hebt bereikt, kunt u een JSON- of XML-tekenreeks doorgeven aan een parameter en deze parseren met het runbook.|
| Maximale grootte voor webhookpayloads |  512 KB|
| Maximumaantal dagen dat taakgegevens worden bewaard|30 dagen|
| Maximale grootte voor PowerShell-werkstroomstatussen |5 MB| Van toepassing op PowerShell-werkstroom-runbooks wanneer controlepunten worden geplaatst in de werkstroom.|

<sup>1</sup> Een sandbox is een gedeelde omgeving die door meerdere taken kan worden gebruikt. Taken die dezelfde sandbox gebruiken, zijn gebonden door de resourcebeperkingen van de sandbox.

#### <a name="change-tracking-and-inventory"></a>Wijzigingen bijhouden en Inventaris

De volgende tabel toont de limieten voor bijgehouden items per machine voor het bijhouden van wijzigingen.

| **Resource** | **Limiet**| **Opmerkingen** |
|---|---|---|
|File|500||
|Register|250||
|Windows-software|250|Software-updates niet inbegrepen.|
|Linux-pakketten|1\.250||
|Services|250||
|Daemon|250||

#### <a name="update-management"></a>Updatebeheer

De volgende tabel toont de limieten voor Updatebeheer.

| **Resource** | **Limiet**| **Opmerkingen** |
|---|---|---|
|Aantal machines per update-implementatie|1000||