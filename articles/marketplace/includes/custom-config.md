---
title: bestand opnemen
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: emuench
ms.author: mingshen
ms.date: 10/09/2020
ms.openlocfilehash: a1bc7cf1fd339ca3660c7b39326f37d2763c74b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92284323"
---
Als u aanvullende configuratie nodig hebt, gebruikt u een geplande taak die wordt uitgevoerd bij het opstarten om definitieve wijzigingen aan te brengen in de virtuele machine nadat deze is geïmplementeerd. Houd ook rekening met het volgende:

- Als het een Run-Once-taak is, moet de taak zichzelf verwijderen nadat deze is voltooid.
- Configuraties mogen niet afhankelijk zijn van C of D, omdat alleen deze twee schijven altijd gegarandeerd bestaan (station C is de schijf met het besturings systeem en station D is de tijdelijke lokale schijf).

Zie [extensies en functies van virtuele machines voor Linux](../../virtual-machines/extensions/features-linux.md)voor meer informatie over Linux-aanpassingen.