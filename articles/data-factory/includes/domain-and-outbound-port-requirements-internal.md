---
title: bestand opnemen
description: bestand opnemen
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 24a541080b580eab967987fa7c92cea64b99d65c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389490"
---
| Domeinnamen                                          | Uitgaande poorten | Beschrijving                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Open bare Cloud: `*.servicebus.windows.net` <br> Azure Government: `*.servicebus.usgovcloudapi.net` <br> China `*.servicebus.chinacloudapi.cn`   | 443            | Vereist door de zelf-hostende integration runtime voor interactief ontwerpen. |
| Open bare Cloud: `{datafactory}.{region}.datafactory.azure.net`<br> of `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> China `{datafactory}.{region}.datafactory.azure.cn` | 443            | Vereist door de zelf-hostende Integration Runtime om verbinding te maken met de Data Factory-service. <br>Voor nieuwe Data Factory die zijn gemaakt in de open bare Cloud, moet u de FQDN-code vinden op uw zelf-hostende Integration Runtime sleutel in de indeling {DataFactory}. {Region}. DataFactory. Azure. net. Gebruik voor een oude Data factory, als u de FQDN niet ziet in uw zelf-hostende Integration-sleutel, in plaats daarvan *.frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Vereist door de zelf-hostende Integration Runtime voor het downloaden van de updates. Als u automatische updates heb uitgeschakeld, kunt u het configureren van dit domein overslaan. |
| Sleutelkluis-URL | 443           | Vereist door Azure Key Vault als u de referentie opslaat in Key Vault. |
