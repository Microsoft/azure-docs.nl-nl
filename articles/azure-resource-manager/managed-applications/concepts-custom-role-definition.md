---
title: Overzicht van definities van aangepaste rollen
description: Hierin wordt het concept beschreven van het maken van aangepaste roldefinities voor beheerde toepassingen.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 7b7fff913c177703f959bfa103c8e310d01059e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81391828"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Definitie artefact voor aangepaste rollen in Azure Managed Applications

De definitie van een aangepaste rol is een optioneel artefact in beheerde toepassingen. De methode wordt gebruikt om te bepalen welke machtigingen de beheerde toepassing nodig heeft om de functies uit te voeren.

Dit artikel bevat een overzicht van het definitie artefact van aangepaste rollen en de mogelijkheden ervan.

## <a name="custom-role-definition-artifact"></a>Definitie artefact van aangepaste rol

U moet de aangepaste functie definitie artefact customRoleDefinition.jsop een naam hebben. Plaats het op hetzelfde niveau als createUiDefinition.jsop en mainTemplate.jsin het zip-pakket dat een definitie van een beheerde toepassing maakt. Zie [een definitie van een beheerde toepassing publiceren](publish-service-catalog-app.md) voor meer informatie over het maken van een zip-pakket en het publiceren van een definitie van een beheerde toepassing.

## <a name="custom-role-definition-schema"></a>Definitie schema voor aangepaste rol

De customRoleDefinition.jsin het bestand heeft een eigenschap op het hoogste niveau `roles` die een matrix met rollen is. Deze rollen zijn de machtigingen die door de beheerde toepassing moeten worden gebruikt. Op dit moment zijn alleen ingebouwde rollen toegestaan, maar u kunt meerdere rollen opgeven. Naar een rol kan worden verwezen met de ID van de roldefinitie of met de rolnaam.

Voor beeld-JSON voor aangepaste roldefinitie:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>Rollen

Een rol bestaat uit een of een `$.properties.roleName` `id` :

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> U kunt ofwel het `id` veld or gebruiken `roleName` . Er is slechts één vereist. Deze velden worden gebruikt voor het opzoeken van de roldefinitie die moet worden toegepast. Als beide zijn opgegeven, `id` wordt het veld gebruikt.

|Eigenschap|Vereist?|Beschrijving|
|---------|---------|---------|
|id|Ja|De ID van de ingebouwde rol. U kunt de volledige ID of alleen de GUID gebruiken.|
|roleName|Ja|De naam van de ingebouwde rol.|