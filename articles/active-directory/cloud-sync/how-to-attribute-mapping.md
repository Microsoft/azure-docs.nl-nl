---
title: Azure AD Connect kenmerk editor voor Cloud synchronisatie
description: In dit artikel wordt beschreven hoe u de kenmerk editor gebruikt.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 80a035f30294449a024bbde76df2d42ddc23396e
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98622708"
---
# <a name="azure-ad-connect-cloud-sync-attribute-mapping"></a>Toewijzing van kenmerk van Cloud synchronisatie Azure AD Connect

Azure AD Connect Cloud Sync heeft een nieuwe functie geïntroduceerd, waarmee u eenvoudig kenmerken kunt toewijzen tussen uw on-premises gebruikers-en groeps objecten en de objecten in azure AD.  Deze functie is toegevoegd aan de Cloud synchronisatie configuratie.

U kunt de standaardtoewijzingen van kenmerken aanpassen op basis van de behoeften van uw bedrijf. Dit betekent dat u bestaande kenmerktoewijzingen kunt wijzigen of verwijderen, of nieuwe kenmerktoewijzingen kunt maken.  Zie [kenmerken die zijn gesynchroniseerd](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)voor een lijst met kenmerken die worden gesynchroniseerd.

## <a name="understanding-attribute-mapping-types"></a>Informatie over typen kenmerktoewijzingen
Met kenmerk toewijzingen bepaalt u hoe kenmerken worden ingevuld in azure AD.
Er worden vier verschillende toewijzingstypen ondersteund:

- **Direct** : het doel kenmerk wordt gevuld met de waarde van een kenmerk van het gekoppelde object in AD.
- **Constante**: het doelkenmerk wordt gevuld met een specifieke tekenreeks die u hebt opgegeven.
- **Expressie**: het doelkenmerk wordt ingevuld op basis van het resultaat van een scriptachtige expressie.
  Zie [expressies schrijven voor kenmerk toewijzingen](reference-expressions.md)voor meer informatie.
- **Geen**: het doelkenmerk blijft ongewijzigd. Als het doelkenmerk echter leeg is, wordt het ingevuld met de standaardwaarde die u opgeeft.

Naast deze vier basistypen kunt u via ondersteuning voor aangepaste kenmerktoewijzingen het concept van toewijzing van een optionele **standaardwaarde** gebruiken. De toewijzing van een standaardwaarde zorgt ervoor dat een doelkenmerk wordt ingevuld met een waarde als er geen waarde beschikbaar is in Azure AD of in het doelobject. De meest gangbare configuratie is om dit leeg te laten.

## <a name="understanding-attribute-mapping-properties"></a>Informatie over eigenschappen van kenmerktoewijzingen

In de vorige paragraaf hebt u kennisgemaakt met de verschillende typen kenmerktoewijzingen.
Hier beschrijven we de kenmerken die u kunt gebruiken met kenmerktoewijzingen:

- **Bron kenmerk** : het gebruikers kenmerk van het bron systeem (voor beeld: Active Directory).
- **Doel kenmerk** : het gebruikers kenmerk in het doel systeem (voor beeld: Azure Active Directory).
- **Standaardwaarde indien null (optioneel)** : de waarde die wordt doorgegeven aan het doelsysteem als het bronkenmerk null is. Deze waarde wordt alleen ingericht bij het maken van een gebruiker. 'Standaardwaarde indien null' wordt niet ingericht bij het bijwerken van een bestaande gebruiker.  
- **Deze toewijzing toepassen**
  - **Altijd**: deze toewijzing toepassen bij het maken en bijwerken van gebruikers.
  - **Alleen tijdens het maken van een object**: deze toewijzing alleen toepassen bij het maken van gebruikers.

> [!NOTE]
> In dit document wordt beschreven hoe u de Azure Portal gebruikt om kenmerken toe te wijzen.  Zie trans [formaties](how-to-transformation.md) voor meer informatie over het gebruik van Graph

## <a name="using-attribute-mapping"></a>Kenmerk toewijzing gebruiken

Volg de onderstaande stappen om de nieuwe functie te gebruiken.

1.  Selecteer in Azure Portal **Azure Active Directory**.
2.  Selecteer **Azure AD Connect**.
3.  Selecteer **Cloud synchronisatie beheren**.

    ![Inrichting beheren](media/how-to-install/install-6.png)

4. Selecteer uw configuratie onder **configuratie**.
5. Selecteer **klikken om toewijzingen te bewerken**.  Hiermee wordt het scherm kenmerk toewijzing geopend.

    ![Kenmerken toevoegen](media/how-to-attribute-mapping/mapping-6.png)

6.  Klik op **kenmerk toevoegen**.

    ![Toewijzings type](media/how-to-attribute-mapping/mapping-1.png)

7. Selecteer het **toewijzings type**.  In dit voor beeld gebruiken we Expression.
8.  Voer de expressie in het vak in.  Voor dit voor beeld gebruiken we: `Replace([mail], "@contoso.com", , ,"", ,).`
9.  Voer het doel kenmerk in.  In dit voor beeld gebruiken we ExtensionAttribute15.
10. Selecteer wanneer u dit wilt Toep assen en klik vervolgens op **Toep assen**

    ![Toewijzingen bewerken](media/how-to-attribute-mapping/mapping-2a.png)

11. Terug in het scherm kenmerk toewijzing ziet u de nieuwe kenmerk toewijzing.  
12. Klik op **schema opslaan**.

    ![Schema opslaan](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>De kenmerk toewijzing testen

Als u de kenmerk toewijzing wilt testen, kunt u [inrichten op aanvraag](how-to-on-demand-provision.md)gebruiken.  Van de 

1. Selecteer in Azure Portal **Azure Active Directory**.
2. Selecteer **Azure AD Connect**.
3. Selecteer **inrichting beheren**.
4. Selecteer uw configuratie onder **configuratie**.
5. Klik onder **valideren** op de knop **een gebruiker inrichten** . 
6. In het inrichtings scherm op aanvraag.  Voer de **DN-naam** van een gebruiker of groep in en klik op de knop **inrichten** .  
7. Zodra het proces is voltooid, ziet u een geslaagd scherm en vier groene selectie vakjes die aangeven dat het is ingericht.  

    ![Geslaagd voor inrichten](media/how-to-attribute-mapping/mapping-4.png)

8. Klik onder **actie uitvoeren** op **Details weer geven**.  Aan de rechter kant ziet u dat het nieuwe kenmerk is gesynchroniseerd en dat de expressie wordt toegepast.

  ![Actie uitvoeren](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD Connect Cloud synchronisatie?](what-is-cloud-sync.md)
- [Expressies schrijven voor kenmerktoewijzingen](reference-expressions.md)
- [Kenmerken die worden gesynchroniseerd](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
