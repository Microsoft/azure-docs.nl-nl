---
title: Beheer van gebruikers inrichten voor zakelijke apps in azure AD
description: Meer informatie over het beheren van het inrichten van gebruikers accounts voor zakelijke apps met behulp van de Azure Active Directory
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 02/04/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 02d415bd957b0490857081b996c592f90365f031
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555630"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Het inrichten van gebruikers accounts voor zakelijke apps beheren in de Azure Portal

In dit artikel worden de algemene stappen beschreven voor het beheren van het automatisch inrichten van gebruikers accounts en het ongedaan maken van de inrichting voor toepassingen die deze ondersteunen. Het inrichten van een *gebruikers account* is het maken, bijwerken en/of uitschakelen van gebruikers account records in het lokale gebruikers profiel archief van een toepassing. De meeste Cloud-en SaaS-toepassingen slaan de rollen en machtigingen van de gebruiker op in het eigen lokale gebruikers profiel archief en de aanwezigheid van een dergelijke gebruikers record in het lokale archief van de gebruiker is *vereist* voor eenmalige aanmelding en toegang tot het werk. Voor meer informatie over automatische toewijzing van gebruikers accounts raadpleegt [u het automatiseren van gebruikers inrichten en het ongedaan maken van de inrichting van SaaS-toepassingen met Azure Active Directory](user-provisioning.md).

> [!IMPORTANT]
> Azure Active Directory (Azure AD) bevat een galerie met duizenden vooraf geïntegreerde toepassingen die zijn ingeschakeld voor automatische inrichting met Azure AD. U moet beginnen met het vinden van de zelf studie voor het inrichten van de inrichting die specifiek is voor uw toepassing in de [lijst met zelf studies over het integreren van SaaS-apps met Azure Active Directory](../saas-apps/tutorial-list.md). U vindt waarschijnlijk stapsgewijze richt lijnen voor het configureren van zowel de app als Azure AD om de inrichtings verbinding te maken.

## <a name="finding-your-apps-in-the-portal"></a>Uw apps zoeken in de portal

Gebruik de Azure Active Directory portal voor het weer geven en beheren van alle toepassingen die zijn geconfigureerd voor eenmalige aanmelding in een directory. Zakelijke apps zijn apps die in uw organisatie worden geïmplementeerd en gebruikt. Volg deze stappen om uw bedrijfs toepassingen weer te geven en te beheren:

1. Open de [Azure Active Directory Portal](https://aad.portal.azure.com).
1. Selecteer **Enterprise-toepassingen** in het linkerdeelvenster. Er wordt een lijst met alle geconfigureerde apps weergegeven, met inbegrip van apps die zijn toegevoegd vanuit de galerie.
1. Selecteer een app om het resource venster te laden, waar u rapporten kunt weer geven en app-instellingen beheert.
1. Selecteer **Inrichten** om de instellingen voor het inrichten van gebruikersaccounts voor de geselecteerde app te beheren.

   ![Inrichtings scherm voor het beheren van inrichtings instellingen voor gebruikers accounts](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Inrichtings modi

Het **inrichtings** deel venster begint met een **modus** menu, waarin de inrichtings modi worden weer gegeven die worden ondersteund voor een bedrijfs toepassing, en die u kunt configureren. De beschik bare opties zijn onder andere:

* **Automatisch** : deze optie wordt weer gegeven als in azure AD automatische op API gebaseerde inrichting of het ongedaan maken van de inrichting van gebruikers accounts voor deze toepassing wordt ondersteund. Selecteer deze modus om een interface weer te geven die beheerders helpt:

  * Azure AD configureren om verbinding te maken met de gebruikers beheer-API van de toepassing
  * Account toewijzingen en werk stromen maken waarmee wordt gedefinieerd hoe gebruikers account gegevens moeten stromen tussen Azure AD en de app
  * De Azure AD-inrichtings service beheren

* **Hand matig** : deze optie wordt weer gegeven als het automatisch inrichten van gebruikers accounts aan deze toepassing niet door Azure AD wordt ondersteund. In dit geval moeten de in de toepassing opgeslagen gebruikers account records worden beheerd met behulp van een extern proces, op basis van de gebruikers beheer-en inrichtings mogelijkheden van die toepassing (die just-in-time-inrichting kunnen bevatten).

## <a name="configuring-automatic-user-account-provisioning"></a>Automatisch inrichten van gebruikers accounts configureren

Selecteer de optie **automatisch** om instellingen op te geven voor beheerders referenties, toewijzingen, starten en stoppen en synchronisatie.

### <a name="admin-credentials"></a>Referenties voor beheerder

Vouw de **beheerders referenties** uit om de referenties in te voeren die vereist zijn voor Azure AD om verbinding te maken met de gebruikers beheer-API van de toepassing. De vereiste invoer varieert, afhankelijk van de toepassing. Zie de [configuratie handleiding voor deze specifieke toepassing](user-provisioning.md)voor meer informatie over de referentie typen en vereisten voor specifieke toepassingen.

Selecteer **verbinding testen** om de referenties te testen door Azure ad te proberen verbinding te maken met de inrichtings-app van de app met behulp van de opgegeven referenties.

### <a name="mappings"></a>Toewijzingen

Vouw **toewijzingen** uit om de gebruikers kenmerken weer te geven en te bewerken die worden gestroomd tussen Azure AD en de doel toepassing wanneer gebruikers accounts worden ingericht of bijgewerkt.

Er is een vooraf geconfigureerde set met toewijzingen tussen Azure AD-gebruikers objecten en de gebruikers objecten van elke SaaS-app. Sommige apps beheren ook groeps objecten. Selecteer een toewijzing in de tabel om de toewijzings editor aan de rechter kant te openen, waar u deze kunt bekijken en aanpassen.

![Hiermee wordt het scherm kenmerk toewijzing weer gegeven](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

Ondersteunde aanpassingen zijn onder andere:

* Het inschakelen en uitschakelen van toewijzingen voor specifieke objecten, zoals het Azure AD-gebruikers object aan het gebruikers object van de SaaS-app.
* Het bewerken van de kenmerken die van het Azure AD-gebruikers object stromen naar het gebruikers object van de app. Zie [informatie over kenmerk toewijzings typen](customize-application-attributes.md#understanding-attribute-mapping-types)voor meer informatie over kenmerk toewijzing.
* De inrichtings acties die door Azure AD worden uitgevoerd, worden gefilterd op de doel toepassing. In plaats van de objecten volledig te synchroniseren met Azure AD, kunt u de uitgevoerde acties beperken.

  Als u bijvoorbeeld alleen **Update** en Azure AD selecteert, worden bestaande gebruikers accounts in een toepassing bijgewerkt, maar geen nieuwe. Selecteer alleen **maken** en Azure maakt alleen nieuwe gebruikers accounts, maar werkt er geen bestaande bij. Met deze functie kunnen beheerders verschillende toewijzingen maken voor het maken van accounts en het bijwerken van werk stromen.

* Een nieuwe kenmerk toewijzing toevoegen. Selecteer **nieuwe toewijzing toevoegen** onder aan het deel venster **kenmerk toewijzing** . Vul het formulier voor het bewerken van het **kenmerk** in en selecteer **OK** om de nieuwe toewijzing toe te voegen aan de lijst.

### <a name="settings"></a>Instellingen

U kunt de Azure AD-inrichtings service voor de geselecteerde toepassing starten en stoppen in het gebied **instellingen** van het **inrichtings** scherm. U kunt er ook voor kiezen om de inrichtings cache te wissen en de service opnieuw te starten.

Als het inrichten voor de eerste keer voor een toepassing wordt ingeschakeld, schakelt u de service in door de **inrichtings status** te wijzigen in **op aan**. Deze wijziging zorgt ervoor dat de Azure AD-inrichtings service een eerste cyclus uitvoert. Het leest de gebruikers die zijn toegewezen in de sectie **gebruikers en groepen** , vraagt de doel toepassing voor ze en voert vervolgens de inrichtings acties uit die zijn gedefinieerd in de sectie Azure AD- **toewijzingen** . Tijdens dit proces slaat de inrichtings service gegevens op in het cache geheugen over de gebruikers accounts die worden beheerd. niet-beheerde accounts in de doel toepassingen die nooit binnen het bereik van de toewijzing vallen, worden niet beïnvloed door de inrichtings bewerkingen. Na de eerste cyclus synchroniseert de inrichtings service automatisch gebruikers-en groeps objecten met een interval van 40 minuten.

Wijzig de **inrichtings status** in **uit**  om de inrichtings service te onderbreken. In deze status wordt in azure geen gebruikers-of groeps objecten in de app gemaakt, bijgewerkt of verwijderd. Wijzig de status weer **in op** en de service wordt opgehaald waar deze is gestopt.

De **huidige status wissen en de synchronisatie opnieuw starten** genereert een eerste cyclus. De service evalueert vervolgens alle gebruikers in het bron systeem opnieuw en bepaalt of ze binnen het bereik van de inrichting vallen. Dit kan handig zijn als uw toepassing zich momenteel in quarantaine bevindt of als u een wijziging wilt aanbrengen in uw kenmerk toewijzingen. Houd er rekening mee dat de eerste cyclus langer duurt dan de typische incrementele cyclus als gevolg van het aantal objecten dat moet worden geëvalueerd. [Hier](application-provisioning-when-will-provisioning-finish-specific-user.md)vindt u meer informatie over de prestaties van de eerste en incrementele cycli.
