---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 07/09/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 612e7dc2f3d1ef6aa5d3598999aa214a2f377e1f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016358"
---
## <a name="overview-of-ssh-and-keys"></a>Overzicht van SSH en sleutels

[SSH](https://www.ssh.com/ssh/) is een versleuteld verbindings protocol dat veilige aanmeldingen via niet-beveiligde verbindingen biedt. SSH is het standaard verbindings protocol voor Linux-Vm's die worden gehost in Azure. Hoewel SSH een versleutelde verbinding biedt, blijft de virtuele machine met behulp van wacht woorden met SSH-verbindingen nog steeds kwetsbaar voor aanvallen met brute kracht. Het is raadzaam om via SSH verbinding te maken met een virtuele machine met behulp van een openbaar-persoonlijk sleutel paar, ook wel *SSH-sleutels* genoemd. 

- De *open bare sleutel* wordt op uw virtuele Linux-machine geplaatst.

- De *persoonlijke sleutel* blijft op het lokale systeem. Houd deze privésleutel geheim. Deel deze niet.

Wanneer u een SSH-client gebruikt om verbinding te maken met uw virtuele Linux-machine (met de open bare sleutel), test de externe VM de client om te controleren of deze de juiste persoonlijke sleutel heeft. Als de client de persoonlijke sleutel heeft, wordt de toegang tot de virtuele machine verleend. 

Afhankelijk van het beveiligings beleid van uw organisatie, kunt u één enkel openbaar persoonlijk sleutel paar gebruiken om toegang te krijgen tot meerdere Azure-Vm's en-services. U hebt geen afzonderlijk paar sleutels nodig voor elke VM of service die u wilt gebruiken. 

Uw open bare sleutel kan worden gedeeld met iedereen, maar alleen u (of uw lokale beveiligings infrastructuur) moet toegang hebben tot uw persoonlijke sleutel.