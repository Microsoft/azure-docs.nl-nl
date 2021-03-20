---
title: 'Probleem oplossing: ontbrekende gegevens in de gedownloade activiteiten logboeken | Microsoft Docs'
description: Biedt een oplossing voor ontbrekende gegevens in de gedownloade activiteitenlogboeken van Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5003d7b644a0c75401a17ed6a37f31acd8180aa9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85608072"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Ik kan niet alle gegevens vinden in de Azure Active Directory activiteiten logboeken die ik heb gedownload

## <a name="symptoms"></a>Symptomen

Ik heb de activiteitenlogboeken (audit of aanmeldingen) gedownload en ik zie niet alle records voor de tijd die ik heb geselecteerd. Hoe komt dat? 

 ![Rapportage](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Oorzaak

Wanneer u activiteiten Logboeken in de Azure Portal downloadt, beperken we de schaal tot 250.000 records, gesorteerd op meest recente eerst. 

## <a name="resolution"></a>Oplossing

U kunt gebruikmaken van [API's van Azure AD Reporting](concept-reporting-api.md) om maximaal een miljoen records op te halen.

## <a name="next-steps"></a>Volgende stappen

* [Veelgestelde vragen over Azure Active Directory rapporten](reports-faq.md)

