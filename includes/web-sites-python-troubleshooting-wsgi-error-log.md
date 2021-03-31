---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/11/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 020e59f029b09f3c7656f67039731e4141e68d31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176639"
---
Als python een fout tegen komt tijdens het starten van de toepassing, wordt er alleen een eenvoudige fout pagina geretourneerd (bijvoorbeeld: de pagina kan niet worden weer gegeven omdat er een interne server fout is opgetreden. ").

Python-toepassings fouten vastleggen:

1. Selecteer in de Azure Portal in uw web-app de optie **instellingen**.
2. Selecteer **Toepassings instellingen** op het tabblad **instellingen** .
3. Voer bij **app-instellingen** het volgende sleutel/waarde-paar in:
    * Sleutel: WSGI_LOG
    * Waarde: D:\home\site\wwwroot\logs.txt (Voer uw keuze in voor de bestands naam)

U ziet nu fouten in het logs.txt-bestand in de map wwwroot.
