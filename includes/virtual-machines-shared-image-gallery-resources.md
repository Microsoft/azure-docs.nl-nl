---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 433e909563602a2ef32b7986959b428c9afaf9f4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015983"
---
| Resource | Beschrijving|
|----------|------------|
| **Bron van installatiekopie** | Dit is een resource die kan worden gebruikt om een **versie van een installatiekopie** in een galerie met installatiekopieën te maken. Een bron van een installatiekopie kan een bestaande VM van Azure zijn die [gegeneraliseerd of gespecialiseerd](../articles/virtual-machines/windows/shared-image-galleries.md#generalized-and-specialized-images) is, een beheerde installatiekopie, een momentopname of een versie van een installatiekopie in een andere galerie met installatiekopieën. |
| **Galerie met installatiekopieën** | Net als Azure Marketplace is een **galerie met installatiekopieën** een opslagplaats voor het beheren en delen van installatiekopieën, maar u bepaalt zelf wie er toegang heeft. |
| **Definitie van installatiekopie** | Definities van installatiekopieën worden in een galerie gemaakt en bevatten informatie over de installatiekopie en de vereisten voor intern gebruik. Dit houdt ook in of het om een Windows- of Linux-installatiekopie gaat. Daarnaast bevat de definitie releaseopmerkingen en de minimale en maximale geheugenvereisten. Het is een definitie van een type installatiekopie. |
| **Versie van installatiekopie** | U gebruikt een **versie van een installatiekopie** om een VM te maken wanneer u een galerie gebruikt. U kunt net zo veel versies van een installatiekopie voor uw omgeving gebruiken als u nodig hebt. Net als bij een beheerde installatiekopie, wanneer u een **versie van een installatiekopie** gebruikt om een VM te maken, wordt de versie van de installatiekopie gebruikt voor het maken van nieuwe schijven voor de VM. Versies van installatiekopieën kunnen meerdere keren worden gebruikt. |