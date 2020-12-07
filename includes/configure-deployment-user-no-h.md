---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4ceae4e7e2d10c80a929a4a822c877da8d8478f0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748255"
---
FTP en lokale Git kunnen worden geïmplementeerd in een Azure-web-app met behulp van een *implementatiegebruikers*. Zodra u deze implementatiegebruiker hebt gemaakt, kunt u deze voor al uw Azure-implementaties gebruiken. Uw gebruikersnaam en wachtwoord voor implementatie op accountniveau verschillen van de referenties voor uw Azure-abonnement. 

Als u de implementatiegebruiker wilt configureren, voert u de opdracht [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) uit in Azure Cloud Shell. Vervang \<username> en \<password> door de gebruikersnaam en het wachtwoord van de gebruiker van de implementatie. 

- De gebruikersnaam moet uniek zijn binnen Azure en voor lokale Git-pushes en mag het symbool '\@' niet bevatten. 
- Het wachtwoord moet ten minste acht tekens lang zijn en minimaal twee van de volgende drie typen elementen bevatten: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

De JSON-uitvoer toont het wachtwoord als `null`. Als er een `'Conflict'. Details: 409`-fout optreedt, wijzigt u de gebruikersnaam. Als er een `'Bad Request'. Details: 400`-fout optreedt, kiest u een sterker wachtwoord. 

Noteer uw gebruikersnaam en wachtwoord om te gebruiken bij het implementeren van uw web-apps.
