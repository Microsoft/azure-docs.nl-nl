---
title: bestand opnemen
description: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a4a2034d44d98de9696b435c68c97a8a54a1c351
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027166"
---
Azure bevat de volgende ingebouwde Azure-rollen voor het autoriseren van toegang tot blob- en wachtrijgegevens met behulp van Azure AD en OAuth:

- [Eigenaar van opslagblobgegevens](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Gebruik om het eigendom in te stellen en POSIX-toegangsbeheer voor Azure Data Lake Storage Gen2 te regelen. Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md) voor meer informatie.
- [Bijdrager voor opslagblobgegevens](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Gebruik om lees-/schrijf-/verwijderrechten toe te kennen aan Blob-opslagresources.
- [Lezer voor opslagblobgegevens](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Gebruik om alleen-lezenrechten toe te kennen aan Blob-opslagresources.
- [Storage Blob-delegator](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Haal een sleutel voor gebruikersoverdracht op om te gebruiken voor het maken van een handtekening voor gedeelde toegang die is ondertekend met Azure AD-referenties voor een container of blob.
- [Bijdrager voor opslagwachtrijgegevens](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Gebruik om lees-/schrijf-/verwijderrechten toe te kennen voor Azure-wachtrijen.
- [Lezer voor opslagwachtrijgegevens](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Gebruik om alleen-lezenrechten toe te kennen voor Azure-wachtrijen.
- [Berichtenprocessor voor opslagwachtrijgegevens](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Gebruik om machtigingen voor weergeven, ophalen en verwijderen van berichten in Azure Storage-wachtrijen te verlenen.
- [Berichtenverzender voor opslagwachtrijgegevens](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Gebruik om machtigingen voor toevoegen van berichten in Azure Storage-wachtrijen te verlenen.

Alleen met rollen die expliciet zijn gedefinieerd voor toegang tot gegevens, kan een beveiligingsprincipal toegang krijgen tot blob- of wachtrijgegevens. Met ingebouwde rollen zoals **Eigenaar**, **Inzender**, en **Inzender voor opslagaccounts** kan een beveiligingsprincipal een opslagaccount te beheren, maar heeft geen toegang tot de blob- of wachtrijgegevens binnen dat account via Azure AD. Als een rol echter de **Microsoft.Storage/storageAccounts/listKeys/action** bevat, heeft een gebruiker aan wie die rol is toegewezen, toegang tot gegevens in het opslagaccount via gedeelde-sleutelautorisatie met de accounttoegangssleutels. Zie [Use the Azure portal to access blob or queue data](../articles/storage/blobs/authorize-data-operations-portal.md) (De Azure-portal gebruiken om toegang te krijgen tot blob- of wachtrijgegevens) voor meer informatie.

Voor gedetailleerde informatie over ingebouwde rollen van Azure voor Azure Storage voor zowel de gegevensservices als de beheerservice raadpleegt u de sectie **Storage** in [Ingebouwde Azure-rollen voor Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Zie voor meer informatie over de verschillende soorten rollen die machtigingen bieden in Azure [Klassieke abonnementsbeheerdersrollen, Azure-rollen en Azure AD-rollen](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Het kan vijf minuten duren voordat Azure-roltoewijzingen zijn doorgegeven.