---
title: Azure Key Vault kluis verplaatsen naar een andere resourcegroep | Microsoft Docs
description: Richtlijnen voor het verplaatsen van een sleutelkluis naar een andere resourcegroep.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 14ecbcaa35153438601a3dabb70f5b38006ded1b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749560"
---
# <a name="moving-an-azure-key-vault-across-resource-groups"></a>Een Azure Key Vault verplaatsen naar een andere resourcegroep

## <a name="overview"></a>Overzicht

Het verplaatsen van een sleutelkluis tussen resourcegroepen is een ondersteunde sleutelkluisfunctie. Het verplaatsen van een sleutelkluis tussen resourcegroepen heeft geen invloed op de firewall van de sleutelkluis of configuraties van toegangsbeleid. Verbonden toepassingen en service-principals moeten blijven werken zoals bedoeld.

> [!IMPORTANT]
> **Sleutelkluizen die worden gebruikt voor schijfversleuteling, kunnen niet worden verplaatst.**
> Als u een sleutelkluis gebruikt met schijfversleuteling voor een VM, kan de sleutelkluis niet worden verplaatst naar een andere resourcegroep of een abonnement terwijl schijfversleuteling is ingeschakeld. U moet schijfversleuteling uitschakelen voordat u de sleutelkluis naar een nieuwe resourcegroep of een nieuw abonnement verplaatst. 

## <a name="design-considerations"></a>Overwegingen bij het ontwerp

Uw organisatie heeft mogelijk een Azure Policy met afdwinging of uitsluitingen op het niveau van de resourcegroep. Er is mogelijk een andere set beleidstoewijzingen in de resourcegroep waarin uw sleutelkluis momenteel bestaat en de resourcegroep waarin u de sleutelkluis verplaatst. Een conflict in beleidsvereisten kan uw toepassingen breken.

### <a name="example"></a>Voorbeeld

U hebt een toepassing die is verbonden met de sleutelkluis die certificaten maakt die twee jaar geldig zijn. De resourcegroep waarin u uw sleutelkluis wilt verplaatsen, heeft een beleidstoewijzing die het maken van certificaten blokkeert die langer dan één jaar geldig zijn. Nadat u de sleutelkluis naar de nieuwe resourcegroep hebt verplaatst, wordt de bewerking voor het maken van een certificaat dat twee jaar geldig is, geblokkeerd door een Azure-beleidstoewijzing.

### <a name="solution"></a>Oplossing

Zorg ervoor dat u naar de pagina Azure Policy op de Azure Portal gaat en bekijk de beleidstoewijzingen voor uw huidige resourcegroep en de resourcegroep waar u naartoe gaat en zorg ervoor dat er geen verschil is.

## <a name="procedure"></a>Procedure

1. Aanmelden bij Azure Portal
2. Navigeer naar uw sleutelkluis
3. Klik op het tabblad Overzicht
4. Selecteer de knop Verplaatsen
5. Selecteer Verplaatsen naar een andere resourcegroep in de vervolgkeuzeopties
6. Selecteer de resourcegroep waarin u de sleutelkluis wilt verplaatsen
7. De waarschuwing met betrekking tot het verplaatsen van resources bevestigen
8. Selecteer OK

Key Vault evalueert nu de geldigheid van de resource verplaatsen en waarschuwt u voor eventuele fouten. Als er geen fouten worden gevonden, wordt de resource verplaatst. 
