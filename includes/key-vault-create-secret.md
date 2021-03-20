---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: d1a67a131a94bc017eaf2199546ef08f0291cd54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244801"
---
We gaan een geheim maken met de naam **mySecret** en de waarde **Success!** . Een geheim kan een wachtwoord zijn, een SQL-verbindingsreeks of andere gegevens die u zowel veilig als beschikbaar wilt houden voor de toepassing. 

Als u een geheim wilt toevoegen aan uw nieuwe sleutelkluis, gebruikt u de opdracht [az keyvault secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) van Azure CLI.

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```