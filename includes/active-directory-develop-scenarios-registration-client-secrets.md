---
title: bestand opnemen
description: bestand insluiten voor vertrouwelijke client scenario landings pagina's (daemon, Web-app, Web-API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/14/2020
ms.author: jmprieur
ms.openlocfilehash: 42102f38959911388cefcc141d949e59f24a2c31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996000"
---
## <a name="add-a-client-secret-or-certificate"></a>Een client geheim of-certificaat toevoegen

Net als bij elke vertrouwelijke client toepassing moet u een geheim of certificaat toevoegen om te fungeren als *referenties* van de toepassing, zodat het als zichzelf kan worden geverifieerd, zonder tussen komst van de gebruiker.

U kunt referenties toevoegen aan de registratie van uw client-app met behulp van de [Azure Portal](#add-client-credentials-by-using-the-azure-portal) of door gebruik te maken van een opdracht regel programma zoals [Power shell](#add-client-credentials-by-using-powershell).

### <a name="add-client-credentials-by-using-the-azure-portal"></a>Client Referenties toevoegen met behulp van de Azure Portal

Als u referenties wilt toevoegen aan de app-registratie van uw vertrouwelijke client toepassing, volgt u de stappen in [Quick Start: een toepassing registreren met het micro soft-identiteits platform](../articles/active-directory/develop/quickstart-register-app.md) voor het type referentie dat u wilt toevoegen:

* [Een clientgeheim toevoegen](../articles/active-directory/develop/quickstart-register-app.md#add-a-client-secret)
* [Een certificaat toevoegen](../articles/active-directory/develop/quickstart-register-app.md#add-a-certificate)

### <a name="add-client-credentials-by-using-powershell"></a>Client Referenties toevoegen met behulp van Power shell

U kunt ook referenties toevoegen wanneer u uw toepassing registreert bij het micro soft Identity-platform met behulp van Power shell.

Het code voorbeeld [Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) in github laat zien hoe u een toepassings geheim of certificaat toevoegt bij het registreren van een toepassing:

- Zie [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)voor meer informatie over het toevoegen van een **client geheim** met Power shell.
- Zie [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)voor meer informatie over het toevoegen van een **certificaat** met Power shell.
