---
title: Lead beheer voor Dynamics 365 Customer engagement-micro soft Commercial Marketplace
description: Meer informatie over het instellen van de klant betrokkenheid van Dynamics 365 om leads van Microsoft AppSource en Azure Marketplace te beheren.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 067a30d8f16b06b1b29ad494363d180886d1ab8b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560077"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Lead beheer configureren voor Dynamics 365-klant betrokkenheid

In dit artikel wordt beschreven hoe u Dynamics 365-klant betrokkenheid instelt (voorheen Dynamics CRM Online genoemd). Lees meer over de wijziging in [configureren server-gebaseerde verificatie met klant engagement en share point online](/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) om verkoop leads van uw aanbieding voor commerciële Marketplace te verwerken.

>[!NOTE]
>Deze instructies zijn specifiek voor de micro soft-hostende cloud omgeving voor Dynamics 365-klant betrokkenheid. Het is momenteel niet mogelijk om rechtstreeks verbinding te maken met een on-premises Dynamics-omgeving. Er zijn andere opties voor het ontvangen van leads, zoals het configureren van een [https-eind punt](./commercial-marketplace-lead-management-instructions-https.md) of een [Azure-tabel](./commercial-marketplace-lead-management-instructions-azure-table.md).

## <a name="prerequisites"></a>Vereisten

De volgende gebruikers machtigingen zijn nodig om de stappen in dit artikel uit te voeren:

* Beheerders rechten voor uw Dynamics 365-exemplaar voor klant afspraken om een oplossing te kunnen installeren.
* Tenant beheerders rechten voor het maken van een nieuw service account voor de lead service die wordt gebruikt voor het verzenden van leads van commerciële Marketplace-aanbiedingen.
* Toegang tot de beheer Portal.
* Toegang tot de Azure Portal.

## <a name="install-the-solution"></a>De oplossing installeren

1. Down load de [Microsoft Marketplace lead Writer-oplossing](https://referralsflowpackages.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)en sla deze lokaal op uw computer op.

1. Open Dynamics 365-klant betrokkenheid door naar de URL voor uw Dynamics-exemplaar te gaan, zoals `https://tenant.crm.dynamics.com` .

1. Selecteer het tandwiel pictogram op de bovenste balk en selecteer vervolgens **Geavanceerde instellingen**.

    ![Menu-item Dynamics 365 Advanced Settings](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. Open op de pagina **instellingen** het menu **instellingen** op de bovenste balk en selecteer **oplossingen**.

    >[!NOTE]
    >Als de opties in het volgende scherm niet worden weer gegeven, hebt u niet de benodigde machtigingen om door te gaan. Neem contact op met een beheerder in uw Dynamics 365-exemplaar voor klant afspraken.

    ![Dynamics 365-oplossingen optie](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. Selecteer op de pagina **oplossingen** de optie **importeren** en ga naar de locatie waar u de **Microsoft Marketplace lead Writer** -oplossing hebt opgeslagen die u in stap 1 hebt gedownload.

    ![Knop importeren](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Voer de wizard oplossing importeren uit om de oplossing te volt ooien.

## <a name="configure-user-permissions"></a>Gebruikers machtigingen configureren

Als u leads naar uw Dynamics 365-exemplaar voor klant afspraken wilt schrijven, moet u een service account met micro soft delen en machtigingen voor het account configureren.

Gebruik de volgende stappen om het service account te maken en machtigingen toe te wijzen. U kunt Azure Active Directory of Office 365 gebruiken.

>[!NOTE]
>Ga door naar de overeenkomende instructies op basis van de verificatie optie die u selecteert. Zie [Azure Active Directory](#azure-active-directory) of [Office 365](#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

We raden u aan deze optie te gebruiken omdat u nooit uw gebruikers naam of wacht woord hoeft bij te werken om leads te blijven ontvangen. Als u de Azure Active Directory optie wilt gebruiken, geeft u de App-ID, toepassings sleutel en map-ID van uw Active Directory toepassing op.

Azure Active Directory voor Dynamics 365-klant betrokkenheid configureren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/). Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

1. Selecteer **Eigenschappen** en kopieer de waarde van de **Directory-id** op de pagina **Directory-eigenschappen** . Sla deze waarde op omdat u deze moet opgeven in de portal voor publiceren om leads voor uw Marketplace-aanbieding te ontvangen.

    ![Menu-item Azure Active Directory eigenschappen](media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. Selecteer **app-registraties** in het linkerdeel venster Azure Active Directory en selecteer vervolgens **nieuwe registratie** op die pagina.
1. Voer een beschrijvende naam in voor de naam van de toepassing.
1. Onder **ondersteunde account typen** selecteert u **accounts in elke organisatie Directory**.
1. Onder **omleidings-URI (optioneel)** selecteert u **Web** en voert u een URI in, zoals `https://contosoapp1/auth` .
1. Selecteer **Registreren**.

    ![Een toepassings pagina registreren](media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Nu uw toepassing is geregistreerd, opent u de overzichts pagina van de toepassing. Kopieer de waarde van de **toepassings-id (client)** op die pagina. Sla deze waarde op omdat u deze moet opgeven in de portal voor publiceren en in Dynamics 365 om leads voor uw Marketplace-aanbieding te ontvangen.

    ![Vak toepassing (client)-ID](media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. Selecteer **certificaten & geheimen** in het linkerdeel venster van de app en selecteer de knop **Nieuw client geheim** . Voer een duidelijke beschrijving in voor het client geheim en selecteer de optie **nooit** onder **Expires**. Selecteer **toevoegen** om het client geheim te maken.

    ![Menu-item certificaten & geheimen](media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Zodra het client geheim is gemaakt, kopieert u de waarde van het **client geheim** . U kunt de waarde niet ophalen nadat u de pagina verlaat. Sla deze waarde op omdat u deze moet opgeven in de portal voor publiceren om leads voor uw Marketplace-aanbieding te ontvangen. 
1. Selecteer **API-machtigingen** in het linkerdeel venster van de app en selecteer vervolgens **+ een machtiging toevoegen**.
1. Selecteer **micro soft-api's** en selecteer vervolgens **Dynamics CRM** als de API.
1. Zorg ervoor dat bij **het type machtigingen dat uw toepassing vereist?**, **gedelegeerde machtigingen** is geselecteerd.
1. Schakel onder **machtiging** het selectie vakje **User_impersonation** in voor **toegangs common data service als organisatie gebruikers**. Selecteer vervolgens **machtigingen toevoegen**.

    ![Knop machtigingen toevoegen](media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Nadat u de stappen 1 tot en met 14 in de Azure Portal hebt voltooid, gaat u naar uw Dynamics 365 Customer engagement-exemplaar door naar de URL te gaan, zoals `https://tenant.crm.dynamics.com` .
1. Selecteer het tandwiel pictogram op de bovenste balk en selecteer vervolgens **Geavanceerde instellingen**.
1. Open op de pagina **instellingen** het menu **instellingen** op de bovenste balk en selecteer **beveiliging**.
1. Selecteer op de pagina **beveiliging** de optie **gebruikers**. Selecteer op de pagina **gebruikers** de vervolg keuzelijst **ingeschakelde gebruikers** en selecteer vervolgens **toepassings gebruikers**.
1. Selecteer **Nieuw** om een nieuwe gebruiker te maken.

    ![Een nieuwe gebruiker maken](media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. Controleer in het deel venster **nieuwe gebruiker** of **gebruiker: toepassings gebruiker** is geselecteerd. Geef een gebruikers naam, de volledige naam en het e-mail adres op voor de gebruiker die u met deze verbinding wilt gebruiken. Plak ook de **toepassings-id** voor de app die u hebt gemaakt in de Azure Portal uit stap 8. Selecteer **opslaan & sluiten** om het toevoegen van de gebruiker te volt ooien.

    ![Deel venster nieuwe gebruiker](media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. Ga naar de sectie beveiligings instellingen in dit artikel om de configuratie van de verbinding voor deze gebruiker te volt ooien.

### <a name="office-365"></a>Office 365

Als u Azure Active Directory niet wilt gebruiken, kunt u een nieuwe gebruiker registreren op het Microsoft 365 beheer centrum. U moet uw gebruikers naam en wacht woord elke 90 dagen bijwerken om leads te kunnen blijven ontvangen.

Office 365 voor Dynamics 365 klant betrokkenheid configureren:

1. Meld u aan bij het [Microsoft 365-beheercentrum](https://admin.microsoft.com).

1. Selecteer **een gebruiker toevoegen**.

    ![Microsoft 365-beheer centrum een gebruikers optie toevoegen](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Maak een nieuwe gebruiker voor de lead Writer-service. Configureer de volgende instellingen:

    * Voer een gebruikers naam in.
    * Voer een wacht woord in en schakel de optie **deze gebruiker moet hun wacht woord wijzigen wanneer ze zich voor het eerst aanmelden** .
    * Selecteer **gebruiker (geen beheerders toegang)** als rol voor de gebruiker.
    * Selecteer **Dynamics 365 Customer engagement plan** als de product licentie, zoals wordt weer gegeven in het volgende scherm. Er worden kosten in rekening gebracht voor de licentie die u kiest. 

Sla deze waarden op omdat u de **gebruikers naam** en het **wacht woord** moet opgeven in de portal voor publiceren om leads voor uw Marketplace-aanbieding te ontvangen.

![Het deel venster Nieuw gebruikers Microsoft 365-beheer centrum](media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Beveiligingsinstellingen

De laatste stap bestaat uit het inschakelen van de gebruiker die u hebt gemaakt voor het schrijven van de leads.

1. Open Dynamics 365-klant betrokkenheid door naar de URL voor uw Dynamics-exemplaar te gaan, zoals `https://tenant.crm.dynamics.com` .
1. Selecteer het tandwiel pictogram op de bovenste balk en selecteer vervolgens **Geavanceerde instellingen**.
1. Open op de pagina **instellingen** het menu **instellingen** op de bovenste balk en selecteer **beveiliging**.
1. Selecteer op de pagina **beveiliging** de optie **gebruikers** en selecteer de gebruiker die u hebt gemaakt in de sectie gebruikers machtigingen configureren van dit document. Selecteer vervolgens **rollen beheren**.

    ![Tabblad rollen beheren](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Zoek naar de rolnaam **Microsoft Marketplace lead schrijver** en selecteer deze om de gebruiker de rol toe te wijzen.

    ![Deel venster gebruikers rollen beheren](media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Deze rol wordt gemaakt door de oplossing die u hebt geïmporteerd en heeft alleen machtigingen voor het schrijven van de leads en het bijhouden van de oplossings versie om compatibiliteit te garanderen.

1. Ga terug naar de pagina **beveiliging** en selecteer **beveiligings rollen**. Zoek naar de rol **Microsoft Marketplace lead schrijver** en selecteer deze.

    ![Deel venster beveiligings rollen](media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. Selecteer in de beveiligingsrol het tabblad **kern records** . Zoek naar het item **UI-instellingen voor gebruikers entiteit** . Schakel de machtigingen maken, lezen en schrijven in voor de gebruiker (1/4 Yellow cirkel) voor die entiteit door de bijbehorende keuze rondjes te selecteren.

    ![Microsoft Marketplace tabblad hoofd records van lead Writer](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. Zoek op het tabblad **aanpassingen** naar het item **systeem taak** . Schakel de machtigingen lezen, schrijven en AppendTo in voor de organisatie (effen groene keuze rondjes) voor die entiteit door de bijbehorende keuze rondjes te selecteren.

    ![Tabblad aanpassing van Microsoft Marketplace lead](media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Selecteer **Opslaan en sluiten**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Uw aanbieding configureren voor het verzenden van leads naar de klant betrokkenheid van Dynamics 365

De Lead beheer gegevens voor uw aanbieding configureren in de portal voor publiceren:

1. Ga naar de pagina voor het instellen van de **aanbieding** voor uw aanbieding.
1. Selecteer in het gedeelte **klant leads** de optie **verbinding maken**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="Leads van klanten":::

1. Selecteer in het pop-upvenster verbindings Details de optie **Dynamics 365-klant engagement** voor de doel locatie van de lead.

    ![Vak lead bestemming](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Voer de **URL** voor het Dynamics 365-exemplaar in, bijvoorbeeld `https://contoso.crm4.dynamics.com` .

1. Selecteer de **verificatie** methode, een Azure Active Directory of Office 365.
1. Als u **Azure Active Directory** hebt geselecteerd, voert u de **toepassings-id (client)** in (bijvoorbeeld `23456052-aaaa-bbbb-8662-1234df56788f` ), **map-id** (bijvoorbeeld `12345678-8af1-4asf-1234-12234d01db47` ) en **client geheim** (bijvoorbeeld `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=` ).

    ![Verificatie met Azure Active Directory geselecteerd](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. Als u **Office 365** hebt geselecteerd, voert u de **gebruikers naam** (bijvoorbeeld `contoso@contoso.onmicrosoft.com` ) en het **wacht woord** in (bijvoorbeeld `P@ssw0rd` ).

    ![Vak Office 365-gebruikers naam](media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. Voor **contact opnemen met e-mail** voert u e-mail adressen in voor personen in uw bedrijf die e-mail meldingen moeten ontvangen wanneer er een nieuwe lead wordt ontvangen. U kunt meerdere e-mail adressen opgeven door deze te scheiden met een punt komma.
1. Selecteer **OK**.

Selecteer de knop **valideren** om ervoor te zorgen dat u verbinding hebt gemaakt met een doel van een lead. Als dat lukt, hebt u een test lead in de doel locatie van de lead.

![Vak e-mail adres van contact persoon](media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>U moet de configuratie van de rest van de aanbieding volt ooien en publiceren voordat u leads voor de aanbieding kunt ontvangen.
