---
title: bestand opnemen
description: bestand opnemen
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: bcc55156ca1d03614a4ff9767d6cf3f2603c06ca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027064"
---
Voeg ondersteuning toe in uw voorkeursontwikkelomgeving met de volgende methoden.

| Ontwikkelomgeving  | Toepassingstype      | Ondersteuning toevoegen |
|--------------------------|-----------------------|----------------|
| Visual Studio            | C#-klassebibliotheek      | [Installeer het NuGet-pakket van](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Op basis van [core-hulpprogramma's](../articles/azure-functions/functions-run-local.md) | [De uitbreidingsbundel registreren](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Het wordt aanbevolen om de [Azure Tools-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) te installeren. |
| Een andere editor/IDE     | Op basis van [core-hulpprogramma's](../articles/azure-functions/functions-run-local.md) | [De uitbreidingsbundel registreren](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure Portal             | Alleen online in portal | Wordt geïnstalleerd wanneer u een binding toevoegt<br /><br /> Raadpleeg [Uw extensies bijwerken](../articles/azure-functions/functions-bindings-register.md) om bestaande bindingsextenties uit te breiden zonder dat u uw functie-app opnieuw moet publiceren. |