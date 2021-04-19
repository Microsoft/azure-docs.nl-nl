---
title: storage-files-create-storage-account-portal
description: Een opslagaccount maken voor Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33159ab6dd014a153c8fd317fd291aeca033d6e9
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717831"
---
Een opslagaccount is een gedeelde opslaggroep waarin u Azure-bestandsshares of andere opslagresources, zoals blobs of wachtrijen, kunt implementeren. Een opslagaccount kan een onbeperkt aantal shares bevatten. Een share kan een onbeperkt aantal bestanden opslaan, tot de capaciteitslimiet van het opslagaccount.

Een opslagaccount maken:

1. Selecteer **+** om een resource te maken in het menu aan de linkerkant.
1. Selecteer **Opslagaccount om** een opslagaccount te maken.

    :::image type="content" source="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png" alt-text="Een schermopname van de optie voor het opslagaccount in de blade Een resource maken." lightbox="../articles/storage/files/media/storage-how-to-use-files-portal/create-storage-account-1.png":::

1. Typ *mystorageacct* in het vak **Naam**, gevolgd door een paar willekeurige cijfers totdat u een groen vinkje ziet en u weet dat dit een unieke naam is. De naam van een opslagaccount mag alleen kleine letters bevatten en moet globaal uniek zijn. Noteer de naam van het opslagaccount. U gebruikt dit later. 
1. Laat bij **Prestaties** de standaardwaarde **Standard** geselecteerd.
1. Selecteer **Lokaal redundante opslag (LRS)** bij **Replicatie**.
1. Selecteer bij **Abonnement** het abonnement waarin u het opslagaccount wilt maken. Als u slechts één abonnement hebt, wordt dit abonnement standaard weergegeven.
1. Selecteer voor **Resourcegroep** de optie **Nieuwe maken**. Voer als naam *myResourceGroup* in.
1. Selecteer bij **Locatie** **VS - oost**.
1. Als u klaar bent, klikt u op **Maken** om de implementatie te starten.