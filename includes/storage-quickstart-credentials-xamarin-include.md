---
title: bestand opnemen
description: bestand opnemen
services: storage
author: codemillmatt
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: masoucou
ms.custom: include file
ms.openlocfilehash: 02586d38903c60ba8982753ca0bd3e15192d5deb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "83006235"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieer uw referenties van de Azure Portal

Wanneer met de voorbeeldtoepassing een aanvraag wordt ingediend bij Azure Storage, moet deze aanvraag worden geautoriseerd. Om een aanvraag te autoriseren voegt u de referenties van uw opslagaccount toe als een verbindingsreeks. U kunt de referenties van het opslagaccount weergeven door de volgende stappen te volgen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. Hier worden de toegangssleutels van uw account weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en selecteer de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

Nadat u uw verbindingsreeks hebt gekopieerd, stelt u deze in op een variabele op klasseniveau in het bestand *MainPage.xaml.cs*. Open *MainPaage.xaml.cs* en zoek de variabele `storageConnectionString`. Vervang `<yourconnectionstring>` door de feitelijke verbindingsreeks.

Hier volgt de code:

```csharp
string storageConnectionString = "<yourconnectionstring>";
```