---
title: Vernieuwen met Logic Apps voor Azure Analysis Services-modellen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u asynchroon vernieuwen voor Azure Analysis Services codeert met behulp van Azure Logic Apps.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.custom: references_regions
ms.openlocfilehash: 8a8d434fca7cab4432f38fc64093cf1fe060bd5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92019083"
---
# <a name="refresh-with-logic-apps"></a>Vernieuwen met Logic Apps

U kunt met behulp van Logic Apps en REST-aanroepen automatische gegevens vernieuwings bewerkingen uitvoeren op de tabellaire modellen van Azure Analysis, waaronder synchronisatie van alleen-lezen replica's voor het uitbreiden van de query.

Zie voor meer informatie over het gebruik van REST-Api's met Azure Analysis Services [asynchroon vernieuwen met de rest API](analysis-services-async-refresh.md).

## <a name="authentication"></a>Verificatie

Alle aanroepen moeten worden geverifieerd met een geldig Azure Active Directory (OAuth 2)-token.  In de voor beelden in dit artikel wordt gebruikgemaakt van een service-principal (SPN) voor het verifiëren van Azure Analysis Services. Zie [een service-principal maken met behulp van Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie.

## <a name="design-the-logic-app"></a>De logische app ontwerpen

> [!IMPORTANT]
> In de volgende voor beelden wordt ervan uitgegaan dat de Azure Analysis Services firewall is uitgeschakeld. Als de firewall is ingeschakeld, moet het open bare IP-adres van de aanvrager van de aanvraag worden toegevoegd aan de lijst goedgekeurd in de Azure Analysis Services firewall. Zie [limieten en configuratie-informatie voor Azure Logic apps](../logic-apps/logic-apps-limits-and-config.md#configuration)voor meer informatie over Azure Logic apps IP-bereiken per regio.

### <a name="prerequisites"></a>Vereisten

#### <a name="create-a-service-principal-spn"></a>Een service-principal maken (SPN)

Zie [een service-principal maken met behulp van Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie over het maken van een service-principal.

#### <a name="configure-permissions-in-azure-analysis-services"></a>Machtigingen configureren in Azure Analysis Services
 
De service-principal die u maakt, moet Server Administrator-machtigingen hebben op de server. Zie [een Service-Principal toevoegen aan de rol Server beheerder](analysis-services-addservprinc-admins.md)voor meer informatie.

### <a name="configure-the-logic-app"></a>De logische app configureren

In dit voor beeld is de logische app ontworpen om te activeren wanneer een HTTP-aanvraag wordt ontvangen. Hiermee wordt het gebruik van een Orchestration-hulp programma, zoals Azure Data Factory, ingeschakeld om het Azure Analysis Services model vernieuwen te activeren.

Nadat u een logische app hebt gemaakt:

1. Kies in de ontwerp functie van de logische app de eerste actie zoals **Wanneer een HTTP-aanvraag wordt ontvangen**.

   ![HTTP-activiteit voor ontvangen toevoegen](./media/analysis-services-async-refresh-logic-app/1.png)

Deze stap wordt gevuld met de HTTP POST-URL zodra de logische app is opgeslagen.

2. Voeg een nieuwe stap toe en zoek naar **http**.  

   ![Scherm afbeelding van de sectie ' Kies een actie ' waarvoor de tegel ' HTTP ' is geselecteerd.](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Scherm opname van het venster ' HTTP ' waarvoor de tegel HTTP-HTTP is geselecteerd.](./media/analysis-services-async-refresh-logic-app/10.png)

3. Selecteer **http** om deze actie toe te voegen.

   ![HTTP-activiteit toevoegen](./media/analysis-services-async-refresh-logic-app/2.png)

Configureer de HTTP-activiteit als volgt:

|Eigenschap  |Waarde  |
|---------|---------|
|**Methode**     |POST         |
|**URI**     | https://*uw server regio*/servers/*aas server name*/Models/*your data base name*/refreshes <br /> <br /> Bijvoorbeeld: https: \/ /westus.asazure.Windows.net/servers/MyServer/models/AdventureWorks/refreshes|
|**Kopteksten**     |   Content-type, Application/JSON <br /> <br />  ![Kopteksten](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Hoofdtekst**     |   Zie voor meer informatie over het maken van de aanvraag tekst [asynchroon vernieuwen met de rest API-post/refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Verificatie**     |Active Directory OAuth         |
|**Bouw**     |Vul uw Azure Active Directory TenantId in         |
|**Doelgroep**     |https://*. asazure. Windows. net         |
|**Client ID**     |Voer uw service principal name ClientID in         |
|**Referentie type**     |Geheim         |
|**Gescheiden**     |Voer uw service principal name Secret in         |

Voorbeeld:

![Voltooide HTTP-activiteit](./media/analysis-services-async-refresh-logic-app/7.png)

Test nu de logische app.  Klik in de ontwerp functie van de logische app op **uitvoeren**.

![De logische app testen](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>De logische app gebruiken met Azure Data Factory

Zodra de logische app is opgeslagen, controleert u de activiteit **Wanneer een HTTP-aanvraag is ontvangen** en kopieert u de **URL voor http post** die nu is gegenereerd.  Dit is de URL die kan worden gebruikt door Azure Data Factory om de asynchrone aanroep te maken om de logische app te activeren.

Hier volgt een voor beeld Azure Data Factory webactiviteit die deze actie uitvoert.

![Webactiviteit Data Factory](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Een op zichzelf staande logische app gebruiken

Als u geen gebruik wilt maken van een Orchestration-hulp programma zoals Data Factory om het model vernieuwen te activeren, kunt u de logische app zo instellen dat de gegevens worden vernieuwd op basis van een schema.

Gebruik het bovenstaande voor beeld om de eerste activiteit te verwijderen en te vervangen door een **plannings** activiteit.

![Scherm afbeelding waarin de pagina ' Logic Apps ' wordt weer gegeven, waarbij de tegel schema is geselecteerd.](./media/analysis-services-async-refresh-logic-app/12.png)

![Scherm opname van de pagina "triggers".](./media/analysis-services-async-refresh-logic-app/13.png)

In dit voor beeld wordt **terugkeer patroon** gebruikt.

Nadat de activiteit is toegevoegd, configureert u het interval en de frequentie, voegt u een nieuwe para meter toe en kiest u **deze uren**.

![Scherm opname van de sectie ' recurrence ' met de para meter ' op deze uren ' geselecteerd.](./media/analysis-services-async-refresh-logic-app/16.png)

Selecteer de gewenste uren.

![Activiteit plannen](./media/analysis-services-async-refresh-logic-app/15.png)

Sla de logische app op.

## <a name="next-steps"></a>Volgende stappen

[Voorbeelden](analysis-services-samples.md)  
[REST API](/rest/api/analysisservices/servers)