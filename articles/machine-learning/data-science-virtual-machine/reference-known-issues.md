---
title: 'Naslag informatie: bekende problemen bij het oplossen van problemen &'
titleSuffix: Azure Data Science Virtual  Machine
description: Een lijst met bekende problemen, tijdelijke oplossingen en probleem oplossing voor Azure Data Science Virtual Machine ophalen
services: machine-learning
ms.service: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: bcd75347f91109967ac48427ca0b8855c11b7d9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101656853"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Bekende problemen en het oplossen van problemen met de Azure-Data Science Virtual Machine

Dit artikel helpt u bij het vinden en corrigeren van fouten of fouten die u kunt tegen komen wanneer u de Azure-Data Science Virtual Machine gebruikt.

## <a name="python-package-installation-issues"></a>Problemen met installatie van python-pakket

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Installeren van pakketten met PIP-onderbrekingen afhankelijkheden op Linux

Gebruik `sudo pip install` in plaats van `pip install` Wanneer u pakketten installeert.

## <a name="disk-encryption-issues"></a>Schijfversleutelingsproblemen

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Schijf versleuteling mislukt op de Ubuntu-DSVM

Azure Disk Encryption (ADE) wordt momenteel niet ondersteund in de Ubuntu-DSVM. Als tijdelijke oplossing kunt u overwegen om [versleuteling aan de server zijde van Azure Managed disks](../../virtual-machines/disk-encryption.md)te configureren.

## <a name="tool-appears-disabled"></a>Hulp programma wordt uitgeschakeld weer gegeven

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V werkt niet op de Windows-DSVM

De werking van Hyper-V in eerste instantie werkt niet in Windows. Sommige services zijn uitgeschakeld voor de prestaties van het systeem. Hyper-V inschakelen:

1. De zoek balk openen in uw Windows-DSVM
1. Typ ' Services ',
1. Alle Hyper-V-Services instellen op hand matig
1. "Hyper-V virtual machine management" instellen op automatisch

Het laatste scherm moet er als volgt uitzien:

   ![Hyper-V inschakelen](./media/workaround/hyperv-enable-dsvm.png)