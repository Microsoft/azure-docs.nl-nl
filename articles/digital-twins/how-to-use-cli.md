---
title: De Azure Digital Twins-CLI gebruiken
titleSuffix: Azure Digital Twins
description: Bekijk hoe u aan de slag gaat met en de Azure Digital Apparaatdubbels CLI gebruikt.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 261dceb70a6059c76dbe3bd1d7636eee5d9d77bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936288"
---
# <a name="use-the-azure-digital-twins-cli"></a>De Azure Digital Twins-CLI gebruiken

Naast het beheren van uw Azure Digital Apparaatdubbels-exemplaar in de Azure Portal, heeft Azure Digital Apparaatdubbels een **opdracht ingesteld voor de [Azure cli](/cli/azure/what-is-azure-cli)** die u kunt gebruiken om de meeste belang rijke acties uit te voeren met de-service, waaronder:
* Een Azure Digital Apparaatdubbels-exemplaar beheren
* Modellen beheren
* Digitale apparaatdubbels beheren
* Dubbele relaties beheren
* Eind punten configureren
* [Routes](concepts-route-events.md) beheren
* [Beveiliging](concepts-security.md) configureren via Azure op rollen gebaseerd toegangs beheer (Azure RBAC)

De opdrachtset heet **AZ DT** en maakt deel uit van de [Azure IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension). U kunt de volledige lijst met opdrachten en hun gebruik weer geven als onderdeel van de referentie documentatie voor de `az iot` opdrachtset: [ *AZ DT* opdracht verwijzing](/cli/azure/ext/azure-iot/dt).

## <a name="uses-deploy-and-validate"></a>Gebruikt (implementeren en valideren)

Naast het algemeen beheer van uw exemplaar, is de CLI ook een handig hulp middel voor implementatie en validatie.
* De opdrachten op het besturings element kunnen worden gebruikt om de implementatie van een nieuw exemplaar te herhalen of automatisch uit te voeren.
* De gegevenslaag opdrachten kunnen worden gebruikt om snel waarden in uw exemplaar te controleren en te controleren of de bewerkingen zijn voltooid zoals verwacht.

## <a name="get-the-command-set"></a>De opdracht set ophalen

De Azure Digital Apparaatdubbels-opdrachten maken deel uit van de [Azure IOT-extensie voor Azure cli (Azure-IOT)](https://github.com/Azure/azure-iot-cli-extension). Volg daarom de volgende stappen om ervoor te zorgen dat u beschikt over de meest recente `azure-iot` extensie met **AZ DT** -opdrachten.

### <a name="cli-version-requirements"></a>CLI-versie vereisten

Als u de Azure CLI gebruikt met Power shell, moet u voor het uitbreidings pakket de versie **2.3.1** of hoger van uw Azure CLI gebruiken.

U kunt de versie van uw Azure CLI controleren met deze CLI-opdracht:
```azurecli
az --version
```

Zie [*de Azure cli installeren*](/cli/azure/install-azure-cli)voor instructies over het installeren of bijwerken van de Azure cli naar een nieuwere versie.

### <a name="get-the-extension"></a>De uitbrei ding ophalen

De Azure CLI vraagt u automatisch om de uitbrei ding te installeren op het eerste gebruik van een opdracht waarvoor deze is vereist.

U kunt ook de volgende opdracht gebruiken om de uitbrei ding op elk gewenst moment te installeren (of de extensie bij te werken als deze al een oudere versie heeft). De opdracht kan worden uitgevoerd in de [Azure Cloud shell](../cloud-shell/overview.md) of op een [lokale Azure cli](/cli/azure/install-azure-cli).

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

## <a name="next-steps"></a>Volgende stappen

Verken de CLI en de volledige set opdrachten via de referentie documenten:
* [*AZ DT* -opdracht verwijzing](/cli/azure/ext/azure-iot/dt)