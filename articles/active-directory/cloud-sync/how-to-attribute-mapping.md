---
title: Kenmerk toewijzing in Azure AD Connect Cloud synchronisatie
description: In dit artikel wordt beschreven hoe u de functie Cloud synchronisatie van Azure AD Connect gebruikt om kenmerken toe te wijzen.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555207"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Kenmerk toewijzing in Azure AD Connect Cloud synchronisatie

U kunt de Cloud synchronisatie functie van Azure Active Directory (Azure AD) gebruiken om kenmerken te koppelen tussen uw on-premises gebruikers-of groeps objecten en de objecten in azure AD. Deze functie is toegevoegd aan de Cloud synchronisatie configuratie.

U kunt de standaard kenmerk toewijzingen aanpassen (wijzigen, verwijderen of maken) op basis van de behoeften van uw bedrijf. Zie [kenmerken gesynchroniseerd met Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)voor een lijst met kenmerken die worden gesynchroniseerd.

## <a name="understand-types-of-attribute-mapping"></a>Informatie over typen kenmerk toewijzing
Met kenmerk toewijzing bepaalt u hoe kenmerken worden ingevuld in azure AD. Azure AD ondersteunt vier toewijzings typen:

- **Direct**: het doel kenmerk wordt gevuld met de waarde van een kenmerk van het gekoppelde object in Active Directory.
- **Constante**: het doel kenmerk wordt ingevuld met een specifieke teken reeks die u opgeeft.
- **Expressie**: het doel kenmerk wordt ingevuld op basis van het resultaat van een script achtige expressie. Zie [expressies schrijven voor kenmerk toewijzingen in azure Active Directory](reference-expressions.md)voor meer informatie.
- **Geen**: het doel kenmerk blijft ongewijzigd. Als het doel kenmerk echter ooit leeg is, wordt het ingevuld met de standaard waarde die u opgeeft.

Naast deze basis typen ondersteunen aangepaste kenmerk toewijzingen het concept van een optionele *standaard* waarde voor de toewijzing. De toewijzing van de standaard waarde zorgt ervoor dat een doel kenmerk wordt gevuld met een waarde als Azure AD of het doel object geen waarde heeft. De meest gangbare configuratie is om dit leeg te laten.

## <a name="understand-properties-of-attribute-mapping"></a>Eigenschappen van kenmerk toewijzing begrijpen

Samen met de eigenschap type ondersteunen kenmerk toewijzingen de volgende kenmerken:

- **Bron kenmerk**: het gebruikers kenmerk van het bron systeem (voor beeld: Active Directory).
- **Doel kenmerk**: het gebruikers kenmerk in het doel systeem (voor beeld: Azure Active Directory).
- **Standaard waarde indien Null (optioneel)**: de waarde die wordt door gegeven aan het doel systeem als het bron kenmerk null is. Deze waarde wordt alleen ingericht wanneer een gebruiker wordt gemaakt. Het wordt niet ingericht wanneer u een bestaande gebruiker bijwerkt.  
- **Deze toewijzing Toep assen**:
  - **Altijd**: pas deze toewijzing toe op acties voor het maken van de gebruiker en bij het bijwerken.
  - **Alleen tijdens het maken**: pas deze toewijzing alleen toe bij acties voor het maken van een gebruiker.

> [!NOTE]
> In dit artikel wordt beschreven hoe u de Azure Portal gebruikt om kenmerken toe te wijzen.  Zie trans [formaties](how-to-transformation.md)voor meer informatie over het gebruik van Microsoft Graph.

## <a name="add-an-attribute-mapping"></a>Een kenmerk toewijzing toevoegen

Voer de volgende stappen uit om de nieuwe mogelijkheid te gebruiken:

1.  Selecteer in Azure Portal **Azure Active Directory**.
2.  Selecteer **Azure AD Connect**.
3.  Selecteer **Cloud synchronisatie beheren**.

    ![Scherm afbeelding met de koppeling voor het beheren van Cloud synchronisatie.](media/how-to-install/install-6.png)

4. Selecteer uw configuratie onder **configuratie**.
5. Selecteer **klikken om toewijzingen te bewerken**.  Met deze koppeling opent u het scherm **kenmerk toewijzingen** .

    ![Scherm opname van de koppeling voor het toevoegen van kenmerken.](media/how-to-attribute-mapping/mapping-6.png)

6.  Selecteer **kenmerk toevoegen**.

    ![Scherm afbeelding met de knop voor het toevoegen van een kenmerk, samen met lijsten met kenmerken en toewijzings typen.](media/how-to-attribute-mapping/mapping-1.png)

7. Selecteer het toewijzings type. In dit voor beeld gebruiken we **Expression**.
8. Voer de expressie in het vak in. Voor dit voor beeld gebruiken we `Replace([mail], "@contoso.com", , ,"", ,)` .
9. Voer het doel kenmerk in. Voor dit voor beeld gebruiken we **ExtensionAttribute15**.
10. Selecteer wanneer deze toewijzing moet worden toegepast en selecteer vervolgens **Toep assen**.

    ![Scherm opname van de ingevulde vakken voor het maken van een kenmerk toewijzing.](media/how-to-attribute-mapping/mapping-2a.png)

11. In het scherm **kenmerk toewijzingen** ziet u de nieuwe kenmerk toewijzing.  
12. Selecteer **schema opslaan**.

    ![Scherm opname van de knop schema opslaan.](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>De kenmerk toewijzing testen

Als u de kenmerk toewijzing wilt testen, kunt u [inrichtings op aanvraag](how-to-on-demand-provision.md)gebruiken: 

1. Selecteer in Azure Portal **Azure Active Directory**.
2. Selecteer **Azure AD Connect**.
3. Selecteer **inrichting beheren**.
4. Selecteer uw configuratie onder **configuratie**.
5. Onder **valideren** selecteert u de knop **een gebruiker inrichten** . 
6. Op het scherm **inrichten op aanvraag** voert u de DN-naam van een gebruiker of groep in en selecteert u de knop **inrichten** . 

   In het scherm ziet u dat het inrichten wordt uitgevoerd.

   ![Scherm opname van de inrichting die wordt uitgevoerd.](media/how-to-attribute-mapping/mapping-4.png)

8. Nadat het inrichten is voltooid, wordt het scherm geslaagd weer gegeven met vier groene vinkjes. 

   Selecteer onder **actie uitvoeren** de optie **Details weer geven**. Aan de rechter kant ziet u dat het nieuwe kenmerk is gesynchroniseerd en dat de expressie wordt toegepast.

   ![Scherm opname van geslaagde en geëxporteerde gegevens.](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD Connect--cloudsynchronisatie?](what-is-cloud-sync.md)
- [Expressies schrijven voor kenmerktoewijzingen](reference-expressions.md)
- [Kenmerken gesynchroniseerd naar Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
