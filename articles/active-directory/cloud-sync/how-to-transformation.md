---
title: Cloud Sync-trans formaties Azure AD Connect
description: In dit artikel wordt beschreven hoe u trans formaties gebruikt om de standaard kenmerk toewijzingen te wijzigen.
author: billmath
ms.author: billmath
manager: davba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 1c81ee0ebace65e50cdab5b5b223d5e5eae4ff9a
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613258"
---
# <a name="transformations"></a>Transformaties

Met een trans formatie kunt u het standaard gedrag wijzigen van de manier waarop een kenmerk wordt gesynchroniseerd met Azure Active Directory (Azure AD) met behulp van Cloud synchronisatie.

Als u deze taak wilt uitvoeren, moet u het schema bewerken en vervolgens opnieuw verzenden via een webaanvraag.

Zie [informatie over het Azure AD-schema](concept-attributes.md)voor meer informatie over Cloud synchronisatie kenmerken.


## <a name="retrieve-the-schema"></a>Het schema ophalen
Volg de stappen in [het schema weer geven](concept-attributes.md#view-the-schema)om het schema op te halen. 

## <a name="custom-attribute-mapping"></a>Toewijzing van aangepast kenmerk
Voer de volgende stappen uit om een toewijzing van een aangepast kenmerk toe te voegen.

1. Kopieer het schema naar een tekst-of code-editor, zoals [Visual Studio code](https://code.visualstudio.com/).
1. Zoek het object dat u wilt bijwerken in het schema.

   ![Object in het schema](media/how-to-transformation/transform-1.png)</br>
1. Zoek de code voor `ExtensionAttribute3` onder het gebruikers object.

    ```
                            {
                                "defaultValue": null,
                                "exportMissingReferences": false,
                                "flowBehavior": "FlowWhenChanged",
                                "flowType": "Always",
                                "matchingPriority": 0,
                                "targetAttributeName": "ExtensionAttribute3",
                                "source": {
                                    "expression": "Trim([extensionAttribute3])",
                                    "name": "Trim",
                                    "type": "Function",
                                    "parameters": [
                                        {
                                            "key": "source",
                                            "value": {
                                                "expression": "[extensionAttribute3]",
                                                "name": "extensionAttribute3",
                                                "type": "Attribute",
                                                "parameters": []
                                            }
                                        }
                                    ]
                                }
                            },
    ```
1. Bewerk de code zodat het bedrijfs kenmerk wordt toegewezen aan `ExtensionAttribute3` .

   ```
                                    {
                                        "defaultValue": null,
                                        "exportMissingReferences": false,
                                        "flowBehavior": "FlowWhenChanged",
                                        "flowType": "Always",
                                        "matchingPriority": 0,
                                        "targetAttributeName": "ExtensionAttribute3",
                                        "source": {
                                            "expression": "Trim([company])",
                                            "name": "Trim",
                                            "type": "Function",
                                            "parameters": [
                                                {
                                                    "key": "source",
                                                    "value": {
                                                        "expression": "[company]",
                                                        "name": "company",
                                                        "type": "Attribute",
                                                        "parameters": []
                                                    }
                                                }
                                            ]
                                        }
                                    },
   ```
 1. Kopieer het schema terug naar Graph Explorer, wijzig het **aanvraag type** in **put** en selecteer **query uitvoeren**.

    ![Query uitvoeren](media/how-to-transformation/transform-2.png)

 1. Ga nu in het Azure Portal naar de configuratie van de Cloud synchronisatie en selecteer **inrichting opnieuw starten**.

    ![Inrichting opnieuw starten](media/how-to-transformation/transform-3.png)

 1. Na een tijdje kunt u controleren of de kenmerken worden ingevuld door de volgende query uit te voeren in Graph Explorer: `https://graph.microsoft.com/beta/users/{Azure AD user UPN}` .
 1. Nu ziet u de waarde.

    ![De waarde wordt weer gegeven](media/how-to-transformation/transform-4.png)

## <a name="custom-attribute-mapping-with-function"></a>Toewijzing van aangepast kenmerk met functie
Voor een geavanceerdere toewijzing kunt u functies gebruiken waarmee u de gegevens bewerkt en waarden kunt maken voor kenmerken die voldoen aan de behoeften van uw organisatie.

Volg de vorige stappen om deze taak uit te voeren en bewerk vervolgens de functie die wordt gebruikt om de uiteindelijke waarde te maken.

Zie [expressies schrijven voor kenmerk toewijzingen in azure Active Directory](reference-expressions.md)voor meer informatie over de syntaxis en voor beelden van expressies.


## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud synchronisatie?](what-is-cloud-sync.md)
