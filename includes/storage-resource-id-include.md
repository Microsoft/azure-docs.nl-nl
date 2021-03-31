---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "68249919"
---
Een Azure AD-resource-id geeft de doelgroep aan waarvoor een token dat wordt uitgegeven, kan worden gebruikt om toegang te bieden tot een Azure-resource. In het geval van Azure Storage kan de resource-id specifiek voor één opslagaccount zijn of van toepassing zijn op een willekeurig opslagaccount. In de volgende tabel worden de waarden beschreven die u kunt opgeven voor de resource-id:

|Resource-id  |Beschrijving  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | Het service-eindpunt voor een bepaald opslagaccount. Gebruik deze waarde om een token te verkrijgen voor het goedkeuren van aanvragen voor alleen dat specifieke Azure Storage-account en de service. Vervang de waarde tussen haakjes door de naam van uw opslagaccount.      |
|`https://storage.azure.com/`     | Gebruik deze naam om een token te verkrijgen voor het autoriseren van aanvragen voor een Azure Storage-account.        |
