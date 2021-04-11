---
title: 'Azure Peering Service: Maken '
description: In deze zelfstudie leert u hoe u Azure Peering Service en een voorvoegsel registreert.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: 6f303f99374120522ebfcc13a2af50634a4f67b4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061941"
---
# <a name="tutorial-create-a-peering-service-connection"></a>Zelfstudie: Een Peering Service-verbinding maken

In deze zelfstudie wordt uitgelegd hoe u een Peering Service-resource maakt en een Peering Service-verbinding configureert. 

1. Als u een Peering Service-verbinding wilt registreren, selecteert u **Een resource maken** > **Peering Service**.

 
    ![Peering Service registreren](./media/peering-service-portal/peering-servicecreate.png)

2. Voer de volgende gegevens in op het tabblad **Basisbeginselen** op de pagina **Een Peering Service-verbinding maken**.
 
3. Selecteer het abonnement en de resourcegroep die aan het abonnement is gekoppeld.

    ![Basistabblad Peering Service registreren](./media/peering-service-portal/peering-servicebasics.png)

4. Voer een **Naam** in voor het Peering Service-exemplaar dat moet worden geregistreerd.

5. Selecteer vervolgens onderaan de pagina de knop **Volgende: configuratie**. De pagina **Configuratie** wordt weergegeven.
## <a name="configure-the-peering-service-connection"></a>De Peering Service-verbinding configureren

1. Selecteer op de pagina **Configuratie** de locatie waarop de Peering Service moet worden ingeschakeld door dezelfde locatie te selecteren in de vervolgkeuzelijst **Peering Service-locatie**.

1. Selecteer de serviceprovider waar de Peering Service moet worden verkregen door een providernaam te selecteren in de vervolgkeuzelijst **Peering Service-provider**.
 
1. Selecteer **Nieuw voorvoegsel maken** onderaan de sectie **Voorvoegsels**. Er worden nu tekstvakken weergegeven. Voer nu de naam in van de voorvoegselresource en van de voorvoegsels die zijn gekoppeld aan de serviceprovider.

1. Selecteer **Voorvoegselsleutel** en voeg de voorvoegselsleutel toe die u hebt ontvangen van uw provider (ISP of IXP). Met deze sleutel kan MS het voorvoegsel en de provider valideren die uw IP-voorvoegsel hebben toegewezen.

    ![Schermopname van het tabblad Configuratie van de pagina Een Peering Service-verbinding maken waar u de voorvoegselsleutel kunt invoeren.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Selecteer linksonder op de pagina de knop **Beoordelen en maken**. De pagina **Beoordelen en maken** wordt weergegeven en de configuratie wordt gevalideerd in Azure.

 1. Als u het bericht **Validatie geslaagd** ziet zoals weergegeven, selecteert u **Maken**.

> ![Schermopname van het tabblad Beoordelen en maken van de pagina Een Peering Service-verbinding maken.](./media/peering-service-portal/peering-service-prefix.png)

1. Nadat u een Peering Service-verbinding hebt geregistreerd, wordt aanvullende validatie uitgevoerd voor de meegeleverde voorvoegsels. U kunt de validatiestatus bekijken in de sectie **Voorvoegsels** van de resourcenaam. Als de validatie mislukt, wordt een van de volgende foutberichten weergegeven:

   - Ongeldig Peering Service-voorvoegsel. Het voorvoegsel moet een geldige indeling hebben. Alleen IPv4-voorvoegsels worden ondersteund.
   - Er is geen voorvoegsel ontvangen van de Peering Service-provider.
   - Aankondiging van voorvoegsel heeft geen geldige BGP-community. Neem contact op met de Peering Service-provider.
   - De back-uproute is niet gevonden. Neem contact op met de Peering Service-provider.
   - Voorvoegsel is ontvangen met een langer AS-pad. Neem contact op met de Peering Service-provider.
   - Voorvoegsel is ontvangen met een privé-AS in het pad. Neem contact op met de Peering Service-provider.

### <a name="add-or-remove-a-prefix"></a>Een voorvoegsel toevoegen of verwijderen

Selecteer **Voorvoegsels toevoegen** op de pagina **Voorvoegsels** om voorvoegsels toe te voegen.

Selecteer het beletselteken (...) naast het vermelde voorvoegsel, en selecteer de optie **Verwijderen**.

### <a name="delete-a-peering-service-connection"></a>Een Peering Service-verbinding verwijderen

Selecteer op de pagina **Alle resources** het selectievakje in de Peering Service, en selecteer bovenaan de pagina de optie **Verwijderen**.
## <a name="next-steps"></a>Volgende stappen

- Zie [Peering Service-verbinding](connection.md) voor meer informatie over de Peering Service-verbinding.
- Zie [Peering Service-verbindingstelemetrie](connection-telemetry.md) voor meer informatie over de Peering Service-verbindingstelemetrie.
- Zie [Verbindingstelemetrie meten](measure-connection-telemetry.md) om telemetrie te meten.
- Zie [Een Peering Service-verbinding registreren - Azure PowerShell](powershell.md) als u de verbinding wilt registreren met behulp van Azure PowerShell.
- Zie [Een Peering Service-verbinding registreren - Azure CLI](cli.md) als u de verbinding wilt registreren met behulp van de Azure CLI.