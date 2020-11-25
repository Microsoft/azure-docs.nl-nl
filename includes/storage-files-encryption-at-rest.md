---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 814bf085ca910ef07a13df779eef25e0e6510e57
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011735"
---
Alle gegevens die zijn opgeslagen in Azure Files worden inactief versleuteld met behulp van Azure Storage Service Encryption (SSE). Versleuteling van de opslagservice werkt op dezelfde manier als BitLocker op Windows: gegevens worden versleuteld onder het bestandssysteemniveau. Omdat gegevens worden versleuteld onder het bestandssysteem van de Azure-bestandsshare (vanwege codering naar de schijf) hoeft u geen toegang te hebben tot de onderliggende sleutel op de client om naar de Azure-bestandsshare te kunnen lezen of schrijven. Inactieve versleuteling geldt voor zowel SMB- als NFS-protocollen.

Standaard worden gegevens die zijn opgeslagen in Azure Files versleuteld met door Microsoft beheerde sleutels. Bij door Microsoft beheerde sleutels heeft Microsoft de sleutels voor het versleutelen/ontsleutelen van de gegevens en is Microsoft verantwoordelijk voor het regelmatig rouleren van de sleutels. U kunt er ook voor kiezen om uw eigen sleutels te beheren. Dit geeft u controle over het roulatieproces. Als u ervoor kiest om uw bestandsshares te versleutelen met door de klant beheerde sleutels, is Azure Files gemachtigd om toegang te krijgen tot uw sleutels om te voldoen aan lees- en schrijfaanvragen van uw klanten. Bij door de klant beheerde sleutels kunt u deze autorisatie op elk gewenst moment intrekken, maar dit houdt wel in dat uw Azure-bestandsshare niet langer toegankelijk is via SMB of de FileREST-API.

Azure Files gebruikt hetzelfde versleutelingsschema als de andere Azure-opslagservices zoals Azure Blob Storage. Raadpleeg [Azure storage encryption for data at rest](../articles/storage/common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (Azure Storage-versleuteling voor inactieve gegevens) voor meer informatie over Azure Storage Service Encryption (SSE).