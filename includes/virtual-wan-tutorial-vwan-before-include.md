---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023517"
---
Controleer voordat u met de configuratie begint of u aan de volgende criteria voldoet:

* Als u al een virtueel netwerk hebt waarmee u verbinding wilt maken, controleert u of geen van de subnetten van uw on-premises netwerk het overlapt. Het virtuele netwerk heeft geen gateway-subnet nodig en kan geen virtuele netwerk gateways hebben. Als u geen virtueel netwerk hebt, kunt u er een maken met behulp van de stappen in dit artikel.
* Zorg dat u een IP-adresbereik krijgt voor uw hubregio. De hub is een virtueel netwerk en het adres bereik dat u opgeeft voor de hub-regio, mag niet overlappen met een bestaand virtueel netwerk waarmee u verbinding maakt. Het kan ook niet overlappen met de adresbereiken die u met on-premises verbinding maakt. Als u niet bekend bent met de IP-adresbereiken die zich in uw on-premises netwerk configuratie bevinden, coördineert u met iemand die deze gegevens voor u kan opgeven.
* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.