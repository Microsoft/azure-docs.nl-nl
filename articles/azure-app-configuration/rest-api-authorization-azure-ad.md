---
title: Azure-app configuratie REST API-Azure Active Directory autorisatie
description: Gebruik Azure Active Directory voor autorisatie voor Azure-app configuratie met behulp van de REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: c028dcf6649da9abd196997d077b58386a5e6dd9
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092798"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory autorisatie-REST API verwijzing

Wanneer u gebruikmaakt van Azure Active Directory-verificatie (Azure AD), wordt autorisatie verwerkt door op rollen gebaseerd toegangs beheer (RBAC). RBAC vereist dat gebruikers worden toegewezen aan rollen om toegang tot resources te verlenen. Elke rol bevat een reeks acties die gebruikers hebben toegewezen aan de rol kunnen uitvoeren.

## <a name="roles"></a>Rollen

De volgende rollen zijn standaard beschikbaar in azure-abonnementen:

- **Azure-app eigenaar van de configuratie gegevens**: deze rol biedt volledige toegang tot alle bewerkingen.
- **Azure-app-configuratie gegevens lezer**: met deze rol kunnen Lees bewerkingen worden uitgevoerd.

## <a name="actions"></a>Acties

Rollen bevatten een lijst met acties die gebruikers aan deze rol kunnen uitvoeren. De configuratie van Azure-app ondersteunt de volgende acties:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Met deze actie wordt lees toegang tot app-configuratie sleutel-value-resources toegestaan, zoals/KV en/labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Met deze actie wordt schrijf toegang tot de resources van de app-configuratie sleutel waarde toegestaan.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Met deze actie kunnen de app-configuratie sleutel-value-resources worden verwijderd. Houd er rekening mee dat het verwijderen van een resource de sleutel waarde retourneert die is verwijderd.

## <a name="error"></a>Fout

```http
HTTP/1.1 403 Forbidden
```

**Reden:** De principal die de aanvraag maakt, beschikt niet over de vereiste machtigingen om de aangevraagde bewerking uit te voeren.
**Oplossing:** Wijs de vereiste rol toe om de aangevraagde bewerking uit te voeren op de principal die de aanvraag heeft ingediend.

## <a name="managing-role-assignments"></a>Roltoewijzingen beheren

U kunt roltoewijzingen beheren door gebruik te maken van [Azure RBAC-procedures](../role-based-access-control/overview.md) die standaard zijn opgenomen in alle Azure-Services. U kunt dit doen via de Azure CLI, Power shell en de Azure Portal. Zie [Azure-rollen toewijzen met behulp van de Azure Portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.