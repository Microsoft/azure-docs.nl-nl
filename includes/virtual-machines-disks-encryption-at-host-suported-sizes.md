---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5a811620de8336abd3e0df6d72db761ce18b2b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86230969"
---
Alle nieuwste generatie VM-grootten ondersteunen versleuteling op de host:

|Type  |Niet ondersteund  |Ondersteund  |
|---------|---------|---------|
|Algemeen doel     | Dv3, Dav4, dv2, Av2        | B, DSv2, Dsv3, DC, DCv2, Dasv4        |
|Geoptimaliseerde rekenkracht     |         | Fsv2        |
|Geoptimaliseerd geheugen     | Ev3, Eav4        | DSv2, Esv3, M, Mv2, Easv4        |
|Geoptimaliseerde opslag     |         | Ls, Lsv2 (NVMe-schijven niet versleuteld)        |
|GPU     | NC, NV        | NCv2, NCv3, ND, NVv3, NVv4, NDv2 (preview-versie)        |
|Krachtig rekenvermogen     | H        | HB, HC, HBv2        |
|Vorige generaties     | F, A, D, L, G        | DS, GS, FS, NVv2        |

Bij de upgrade van de VM-grootte wordt een validatie uitgevoerd om te controleren of de nieuwe VM-grootte de EncryptionAtHost-functie ondersteunt.
