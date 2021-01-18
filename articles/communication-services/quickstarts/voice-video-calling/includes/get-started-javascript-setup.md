---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: mikben
ms.openlocfilehash: 90bc9def9c1a78799f4931b58ad0a661602985e3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013189"
---
## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Open uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.

```console
mkdir calling-quickstart && cd calling-quickstart
```

Voer `npm init -y` uit om een **package.json**-bestand te maken met de standaardinstellingen.

```console
npm init -y
```

### <a name="install-the-package"></a>Het pakket installeren

Gebruik de opdracht `npm install` voor het installeren van de clientbibliotheek voor oproepen voor Javascript in Azure Communication Services.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

De volgende versies van webpack worden aanbevolen voor deze quickstart:

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

De optie `--save` geeft de bibliotheek weer als afhankelijkheid in het **package.json**-bestand.

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In deze snelstart wordt webpack gebruikt om de toepassingsassets te bundelen. Voer de volgende opdracht uit om de webpack-, webpack-CLI- en webpack-dev-server npm-pakketten te installeren en deze weer te geven als ontwikkelingsafhankelijkheden in uw **package.json**:

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Maak een **index. html**-bestand in de hoofdmap van uw project. Dit bestand wordt gebruikt voor het configureren van een basisindeling waarmee de gebruiker een oproep kan plaatsen.
