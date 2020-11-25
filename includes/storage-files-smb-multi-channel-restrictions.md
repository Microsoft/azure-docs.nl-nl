---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/16/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e2a1eb8524c9d9a4b0ae603da3c05fbb20900111
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995466"
---
SMB meerdere kanalen voor Azure-bestands shares heeft momenteel de volgende beperkingen:
- Kan alleen worden gebruikt met lokaal redundante FileStorage-accounts.
- Alleen ondersteund voor Windows-clients. 
- Het maximum aantal kanalen is vier.
- SMB direct wordt niet ondersteund.
- Voor opslag accounts met on-premises Active Directory Domain Services (AD DS) of Azure AD DS [verificatie op basis van identiteiten](../articles/storage/files/storage-files-active-directory-overview.md) die zijn ingeschakeld voor Azure files, kunnen SMB-clients Windows Verkenner niet gebruiken voor het configureren van NTFS-machtigingen voor mappen en bestanden.
    - Gebruik het Windows [icacls](/windows-server/administration/windows-commands/icacls) -hulp programma of de [set-ACL-](/powershell/module/microsoft.powershell.security/set-acl?view=powershell-7) opdracht in plaats daarvan om machtigingen te configureren.

