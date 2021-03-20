---
title: Azure peering-service registreren-Azure Portal
description: Meer informatie over het registreren van de Azure peering-service met behulp van de Azure Portal
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 62210105ba4ba84cff4b246c5b7cf8cc794fb5e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91534944"
---
# <a name="register-peering-service-by-using-the-azure-portal"></a>De peering-service registreren met behulp van de Azure Portal

Azure Peering Service is een netwerkservice die de klantconnectiviteit verbetert met Microsoft-cloudservices, zoals Microsoft 365, Dynamics 365, SaaS-services (Software as a Service), Azure of een andere Microsoft-service die toegankelijk is via openbaar internet.

In dit artikel leert u hoe u een peering service-verbinding kunt registreren met behulp van de Azure Portal.

Als u nog geen Azure-abonnement hebt, maakt u nu een [account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> 

## <a name="prerequisites"></a>Vereisten

U moet het volgende hebben:

### <a name="azure-account"></a>Azure-account

U moet beschikken over een geldig en actief Microsoft Azure-account. Dit account is vereist voor het instellen van de Peering Service-verbinding. Peering Service is een resource binnen Azure-abonnementen. 

### <a name="connectivity-provider"></a>Connectiveitsprovider

U kunt samenwerken met een internetprovider of een Internet Exchange-partner om Peering Service te verkrijgen om verbinding te maken tussen uw netwerk en het Microsoft-netwerk.

Zorg ervoor dat de [connectiviteits providers](location-partners.md) zijn gekoppeld aan micro soft.



## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Ga in een browser naar de Azure Portal en meld u aan met uw Azure-account.

## <a name="register-a-peering-service-connection"></a>Een Peering Service-verbinding registreren

1. Als u een Peering Service-verbinding wilt registreren, selecteert u **Een resource maken** > **Peering Service**.

    ![Peering Service registreren](./media/peering-service-portal/peering-servicecreate.png)
1. Voer de volgende gegevens in op het tabblad **Basisbeginselen** op de pagina **Een Peering Service-verbinding maken**.

 
1. Selecteer het abonnement en de resourcegroep die aan het abonnement is gekoppeld.

   ![Tabblad basis van peering registreren](./media/peering-service-portal/peering-servicebasics.png)

1. Voer een **Naam** in voor het Peering Service-exemplaar dat moet worden geregistreerd.
 
1. Selecteer vervolgens onderaan de pagina de knop **Volgende: configuratie**. De pagina **Configuratie** wordt weergegeven.

## <a name="configure-the-peering-service-connection"></a>De Peering Service-verbinding configureren

1. Selecteer op de pagina **Configuratie** de locatie waarop de Peering Service moet worden ingeschakeld door dezelfde locatie te selecteren in de vervolgkeuzelijst **Peering Service-locatie**.

1. Selecteer de serviceprovider waar de Peering Service moet worden verkregen door een providernaam te selecteren in de vervolgkeuzelijst **Peering Service-provider**.
 
1. Selecteer **Nieuw voorvoegsel maken** onderaan de sectie **Voorvoegsels**. Er worden nu tekstvakken weergegeven. Voer nu de naam in van de voorvoegselresource en van de voorvoegsels die zijn gekoppeld aan de serviceprovider.

1. Selecteer **Voorvoegselsleutel** en voeg de voorvoegselsleutel toe die u hebt ontvangen van uw provider (ISP of IXP). Met deze sleutel kan MS het voorvoegsel en de provider valideren die uw IP-voorvoegsel hebben toegewezen.
   > ![Schermopname van het tabblad Configuratie van de pagina Een Peering Service-verbinding maken waar u de voorvoegselsleutel kunt invoeren.](./media/peering-service-portal/peering-serviceconfiguration.png)

1. Selecteer linksonder op de pagina de knop **Beoordelen en maken**. De pagina **Beoordelen en maken** wordt weergegeven en de configuratie wordt gevalideerd in Azure.
    

1. Als u het bericht **Validatie geslaagd** ziet zoals weergegeven, selecteert u **Maken**.

   > ![Schermopname van het tabblad Beoordelen en maken van de pagina Een Peering Service-verbinding maken.](./media/peering-service-portal/peering-service-prefix.png)


1. Nadat u een Peering Service-verbinding hebt geregistreerd, wordt aanvullende validatie uitgevoerd voor de meegeleverde voorvoegsels. U kunt de validatiestatus bekijken in de sectie **Voorvoegsels** van de resourcenaam. Als de validatie mislukt, wordt een van de volgende foutberichten weergegeven:

   - Ongeldig Peering Service-voorvoegsel. Het voorvoegsel moet een geldige indeling hebben. Alleen IPv4-voorvoegsels worden ondersteund.
   - Er is geen voorvoegsel ontvangen van de Peering Service-provider.
   - De voorvoegsel aankondiging heeft geen geldige BGP-community. Neem contact op met de peering service provider.
   - De back-uproute is niet gevonden. Neem contact op met de peering service provider.
   - Het voor voegsel dat langer is ontvangen als pad, neemt contact op met de peering service provider.
   - Het voor voegsel dat is ontvangen met privé als in het pad, neemt contact op met de peering service provider.

### <a name="add-or-remove-a-prefix"></a>Een voorvoegsel toevoegen of verwijderen

Selecteer **Voorvoegsels toevoegen** op de pagina **Voorvoegsels** om voorvoegsels toe te voegen.

Selecteer het beletselteken (...) naast het vermelde voorvoegsel, en selecteer de optie **Verwijderen**.

### <a name="delete-a-peering-service-connection"></a>Een Peering Service-verbinding verwijderen

Selecteer op de pagina **Alle resources** het selectievakje in de Peering Service, en selecteer bovenaan de pagina de optie **Verwijderen**.

> [!NOTE]
> U kunt een bestaand voor voegsel niet wijzigen.
>

## <a name="next-steps"></a>Volgende stappen

- Zie [Peering Service-verbinding](connection.md) voor meer informatie over de Peering Service-verbinding.
- Zie [Peering Service-verbindingstelemetrie](connection-telemetry.md) voor meer informatie over de Peering Service-verbindingstelemetrie.
- Zie [Verbindingstelemetrie meten](measure-connection-telemetry.md) om telemetrie te meten.
- Zie [Een Peering Service-verbinding registreren - Azure PowerShell](powershell.md) als u de verbinding wilt registreren met behulp van Azure PowerShell.
- Zie [Een Peering Service-verbinding registreren - Azure CLI](cli.md) als u de verbinding wilt registreren met behulp van de Azure CLI.
