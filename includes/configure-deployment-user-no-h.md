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
ms.openlocfilehash: a972b4738ce5646a1ee9eed6495bdc43a40826fd
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102219030"
---
FTP en lokale Git kunnen worden geïmplementeerd in een Azure-web-app met behulp van een *implementatiegebruikers*. Zodra u deze implementatiegebruiker hebt gemaakt, kunt u deze voor al uw Azure-implementaties gebruiken. Uw gebruikersnaam en wachtwoord voor implementatie op accountniveau verschillen van de referenties voor uw Azure-abonnement. 

Als u de implementatiegebruiker wilt configureren, voert u de opdracht [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) uit in Azure Cloud Shell. Vervang \<username> en \<password> door de gebruikersnaam en het wachtwoord van de gebruiker van de implementatie. 

- De gebruikersnaam moet uniek zijn binnen Azure en voor lokale Git-pushes en mag het symbool '\@' niet bevatten. 
- Het wachtwoord moet ten minste acht tekens lang zijn en minimaal twee van de volgende drie typen elementen bevatten: letters, cijfers en symbolen. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

De JSON-uitvoer toont het wachtwoord als `null`. Als er een `'Conflict'. Details: 409`-fout optreedt, wijzigt u de gebruikersnaam. Als er een `'Bad Request'. Details: 400`-fout optreedt, kiest u een sterker wachtwoord. 

Noteer uw gebruikersnaam en wachtwoord om te gebruiken bij het implementeren van uw web-apps.
