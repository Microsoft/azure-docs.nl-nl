---
title: 'Quickstart: Een Azure Purview-account maken in Azure Portal (preview)'
description: In deze quickstart wordt beschreven hoe u een Azure Purview-account maakt en machtigingen configureert om het te kunnen gaan gebruiken.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: c9e0b155a4cf34373bb6d851241dc62ddd661045
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602355"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>Quickstart: Een Azure Purview-account maken in Azure Portal

> [!IMPORTANT]
> Azure Purview is momenteel in PREVIEW. De [Aanvullende voorwaarden voor gebruik van Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) omvatten aanvullende juridische voorwaarden die van toepassing zijn op Azure-functies die in bèta of preview zijn of die anders nog niet algemeen beschikbaar zijn.

In deze quickstart gaat u een Azure Purview-account maken.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Uw eigen [Azure Active Directory-tenant](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

* Uw account moet gemachtigd zijn om resources te maken in het abonnement

* Als uw **Azure Policy** alle toepassingen ervan weerhoudt om een **Storage-account** en een **EventHub-naamruimte** te maken, moet u met behulp van tags uitzonderingen voor het beleid maken. Deze kunt u invoeren tijdens het proces voor het maken van een Purview-account. De belangrijkste reden hiervoor is dat voor elk gemaakt Purview-account een beheerde resourcegroep wordt gemaakt met daarin een Storage-account en een EventHub-naamruimte.
    1. Ga naar Azure Portal en zoek op **beleid**
    1. Volg [Een aangepaste beleidsdefinitie maken](https://docs.microsoft.com/azure/governance/policy/tutorials/create-custom-policy-definition) of wijzig bestaand beleid om twee uitzonderingen toe te voegen aan operator `not` en tag `resourceBypass`:

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > De tag kan alles behalve `resourceBypass` zijn, en het is aan u om een waarde te definiëren bij het maken van Purview in de laatste stappen, zolang het beleid de tag maar kan detecteren.

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="Schermopname die laat zien hoe u een beleidsdefinitie kunt maken.":::

    1. [Maak een beleidstoewijzing](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) met behulp van het aangepaste beleid dat is gemaakt.

        [ ![Schermopname die laat zien hoe u een beleidstoewijzing kunt maken](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij [Azure Portal](https://portal.azure.com).

## <a name="configure-your-subscription"></a>Uw abonnement configureren

Voer zo nodig de volgende stappen uit om uw abonnement te configureren zodat Azure Purview kan worden uitgevoerd in uw abonnement:

   1. Zoek en selecteer **Abonnementen** in Azure Portal.

   1. Selecteer in de lijst met abonnementen het abonnement dat u wilt gebruiken. Voor het abonnement zijn beheerdersmachtigingen vereist.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Schermopname van het selecteren van een abonnement in Azure Portal.":::

   1. Selecteer **Resourceproviders** voor uw abonnement. Zoek in het deelvenster **Resourceproviders** alle drie de resourceproviders en registreer deze: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Als ze niet zijn geregistreerd, registreert u ze alsnog door **Registreren** te selecteren.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Schermopname van het registreren van de Microsoft Azure Purview-resourceprovider in Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Een Azure Purview-account maken

1. Ga in Azure Portal naar de pagina **Purview-accounts** en selecteer vervolgens **Toevoegen** om een nieuw Azure Purview-account te maken. U kunt ook in Marketplace zoeken naar **Purview-accounts** en **Maken** selecteren. Houd er rekening mee dat u slechts één Azure Purview-account per keer kunt toevoegen.

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Schermopname die laat zien hoe u een instantie van een Azure Purview-account maakt in Azure Portal.":::

1. Op het tabblad **Basics** voert u de volgende handelingen uit:
    1. Selecteer een **resourcegroep**.
    1. Voer een **Purview-accountnaam** in voor uw catalogus. Spaties en symbolen zijn niet toegestaan.
    1. Kies een **locatie** en selecteer **Volgende: Configuratie**.
1. Selecteer op het tabblad **Configuratie** de gewenste **platformgrootte**: de toegestane waarden zijn 4 capaciteitseenheden (CU) en 16 CU. Selecteer **Volgende: Tags**.
1. Op het tabblad **Tags** kunt u desgewenst een of meer tags toevoegen. Deze tags zijn alleen voor gebruik in Azure Portal, niet in Azure Purview. 

    > [!Note] 
    > Als u **Azure Policy** hebt en uitzonderingen wilt toevoegen zoals in **Vereisten**, moet u de juiste tag toevoegen. U kunt bijvoorbeeld de tag `resourceBypass` toevoegen: :::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="Tag toevoegen aan Purview-account.":::

1. Selecteer **Controleren en maken** en selecteer vervolgens **Maken**. Het duurt enkele minuten voordat deze is gemaakt. De zojuist gemaakte instantie van het Azure Purview-account wordt weergegeven in de lijst op de pagina **Azure Purview-accounts**.
1. Wanneer de inrichting van het nieuwe account is voltooid, selecteert u **Naar de resource gaan**.

    > [!Note]
    > Als het inrichten is mislukt met de status `Conflict`, betekent dit dat een Azure-beleid Purview ervan weerhoudt om een **Storage-account** en **EventHub-naamruimte** te maken. U moet de stappen bij **Vereisten** volgen om uitzonderingen toe te voegen.
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Foutbericht over conflict in Purview":::

1. Selecteer **Purview-account starten**.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Schermopname van de selectie om het Azure Purview-account te starten.":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>Een beveiligingsprincipal aan een gegevensvlakrol toevoegen

Voordat u of uw team begint met het gebruik van Azure Purview, moeten een of meer beveiligingsprincipals worden toegevoegd aan een van de vooraf gedefinieerde gegevensvlakrollen: **Purview-gegevenslezer**, **Purview-gegevenscurator** of **Purview-gegevensbronbeheerder**. Zie [Catalogusmachtigingen](catalog-permissions.md) voor meer informatie over de machtigingen voor de Azure Purview-gegevenscatalogus.

Als u een beveiligingsprincipal wilt toevoegen aan de gegevensvlakrol **Purview-gegevenscurator** in een Azure Purview-account, voert u de volgende handelingen uit:

1. Ga naar de pagina [**Purview-accounts**](https://aka.ms/purviewportal) in Azure Portal.

1. Selecteer het Azure Purview-account dat u wilt wijzigen.

1. Selecteer op de pagina **Purview-account** het tabblad **Toegangsbeheer (IAM)**

1. Klik op **+ Toevoegen**

Als u op Toevoegen klikt en twee opties ziet die als uitgeschakeld zijn gemarkeerd, betekent dit dat u niet over de juiste machtigingen beschikt om iemand toe te voegen aan een gegevensvlakrol in het Azure Purview-account. U moet de hulp inroepen van een eigenaar, een beheerder van gebruikerstoegang of iemand anders die geautoriseerd is om rollen toe te wijzen in uw Azure Purview-account. U kunt naar de juiste mensen zoeken door het tabblad **Roltoewijzingen** te selecteren en vervolgens omlaag te schuiven op zoek naar de eigenaar of de beheerder van gebruikerstoegang, en vervolgens met deze mensen contact op te nemen.

1. Selecteer **Roltoewijzing toevoegen**.

1. Zie [Catalogusmachtigingen](catalog-permissions.md) voor meer informatie over het roltype **Purview-gegevenscurator** of **Purview-gegevensbronbeheerder**, afhankelijk van waar de service-principal voor wordt gebruikt.

1. Laat bij **Toegang toewijzen aan** de standaardwaarde **Gebruiker, groep of service-principal** staan.

1. Bij **Selecteren** voert u de naam van de gebruiker, van de Azure Active Directory-groep of van de service-principal in die u wilt toewijzen, en klikt u vervolgens op de naam ervan in het resultatenvenster.

1. Klik op **Opslaan**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u dit Azure Purview-account niet meer nodig hebt, verwijdert u dit door de volgende stappen uit te voeren:

1. Ga naar de pagina **Purview-accounts** in Azure Portal.

2. Selecteer het Azure Purview-account dat u aan het begin van deze quickstart hebt gemaakt. Selecteer **Verwijderen**, voer de naam van het account in en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Azure Purview-account maakt.

Ga naar het volgende artikel voor informatie over hoe u gebruikers toegang kunt geven tot uw Azure Purview-account. 

> [!div class="nextstepaction"]
> [Gebruikers toevoegen aan uw Azure Purview-account](catalog-permissions.md)
