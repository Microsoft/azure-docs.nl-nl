---
title: Gebruikers en rollen beheren in Azure IoT Central-| Microsoft Docs
description: Als beheerder gebruikers en rollen beheren in uw Azure IoT Central toepassing
author: vishwam
ms.author: vishwams
ms.date: 04/16/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 928a9fbad7bde7fe7f1cfaf181bff2a7b9ce458b
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599057"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gebruikers en rollen in uw IoT Central-toepassing beheren

In dit artikel wordt beschreven hoe u als beheerder gebruikers kunt toevoegen, bewerken en verwijderen in Azure IoT Central toepassing. In dit artikel wordt ook beschreven hoe u rollen beheert in uw toepassing.

## <a name="add-users"></a>Gebruikers toevoegen

Elke gebruiker moet een gebruikersaccount hebben voordat deze zich kan aanmelden en toegang kan krijgen tot een toepassing. IoT Central ondersteunt momenteel Microsoft-accounts en Azure Active Directory-accounts, maar niet Azure Active Directory groepen.

Zie help en [snelstart: Nieuwe gebruikers toevoegen aan Microsoft-account](https://support.microsoft.com/products/microsoft-account?category=manage-account) voor meer [Azure Active Directory.](../../active-directory/fundamentals/add-users-azure-active-directory.md)

1. Als u een gebruiker wilt toevoegen aan IoT Central toepassing, gaat u naar de **pagina Gebruikers** in **de sectie** Beheer.
    
    > [!div class="mx-imgBorder"]
    >![Gebruikers beheren](media/howto-manage-users-roles/manage-users-pnp.png)

1. Als u een gebruiker wilt toevoegen, kiest u op **de pagina** Gebruikers de optie + Gebruiker **toevoegen.**

1. Kies een rol voor de gebruiker in **het** vervolgkeuzemenu Rol. Meer informatie over rollen kunt u lezen in de sectie [Rollen](#manage-roles) beheren van dit artikel.

    > [!div class="mx-imgBorder"]
    >![Gebruiker toevoegen en een rol selecteren](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Een gebruiker met een aangepaste rol die hen de machtigingen verleent om andere gebruikers toe te voegen, kan alleen gebruikers toevoegen aan een rol met dezelfde of minder machtigingen dan hun eigen rol.
    > 
    > Als een gebruiker wordt verwijderd uit Azure Active Directory en vervolgens weer wordt toegevoegd, kan deze zich niet automatisch aanmelden bij IoT Central toepassing. Als u de toegang opnieuw wilt inschakelen, moet de beheerder van de toepassing de gebruiker ook verwijderen en opnieuw toevoegen aan de toepassing.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>De rollen bewerken die zijn toegewezen aan gebruikers

Rollen kunnen niet worden gewijzigd nadat ze zijn toegewezen. Als u de rol wilt wijzigen die aan een gebruiker is toegewezen, verwijdert u de gebruiker en voegt u de gebruiker vervolgens opnieuw toe met een andere rol.

> [!NOTE]
> De toegewezen rollen zijn specifiek voor de IoT Central en kunnen niet worden beheerd vanuit de Azure-portal.

## <a name="delete-users"></a>Gebruikers verwijderen

Als u gebruikers wilt verwijderen, selecteert u een of meer selectievakjes op de **pagina** Gebruikers. Selecteer vervolgens **Verwijderen**.

## <a name="manage-roles"></a>Rollen beheren

Met rollen kunt u bepalen wie binnen uw organisatie verschillende taken mag uitvoeren in IoT Central. Er zijn drie ingebouwde rollen die u kunt toewijzen aan gebruikers van uw toepassing. U kunt ook [aangepaste rollen maken als](#create-a-custom-role) u meer controle nodig hebt.

> [!div class="mx-imgBorder"]
> ![Rollen selecteren beheren](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Beheerder

Gebruikers met de **rol Beheerder** kunnen elk deel van de toepassing beheren en beheren, inclusief facturering.

De gebruiker die een toepassing maakt, wordt automatisch toegewezen aan de **rol Administrator.** De rol Administrator moet altijd ten minste één **gebruiker** hebben.

### <a name="builder"></a>Opbouwfunctie

Gebruikers met de **rol Builder** kunnen elk onderdeel van de app beheren, maar kunnen geen wijzigingen aanbrengen op de tabbladen Beheer of Continue gegevensexport.

### <a name="operator"></a>Operator

Gebruikers met de **rol Operator** kunnen de apparaatstatus en -status bewaken. Ze mogen geen wijzigingen aanbrengen in apparaatsjablonen of de toepassing beheren. Operators kunnen apparaten toevoegen en verwijderen, apparaatsets beheren en analyses en taken uitvoeren. 

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

Als voor uw oplossing fijner toegangsbesturingselementen zijn vereist, kunt u rollen maken met aangepaste sets machtigingen. Als u een aangepaste rol wilt maken, gaat u **naar de pagina Rollen** in de sectie **Beheer** van uw toepassing. Selecteer vervolgens **+ Nieuwe rol** en voeg een naam en beschrijving toe voor uw rol. Selecteer de machtigingen die uw rol vereist en selecteer vervolgens **Opslaan.**

U kunt gebruikers op dezelfde manier aan uw aangepaste rol toevoegen als gebruikers aan een ingebouwde rol.

> [!div class="mx-imgBorder"]
> ![Een aangepaste rol bouwen](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opties voor aangepaste rollen

Wanneer u een aangepaste rol definieert, kiest u de set machtigingen die aan een gebruiker wordt verleend als deze lid is van de rol. Sommige machtigingen zijn afhankelijk van andere. Als u bijvoorbeeld de machtiging **Toepassingsdashboards** bijwerken toevoegt aan een rol, hebt u ook de machtiging **Toepassingsdashboards** weergeven nodig. De volgende tabellen geven een overzicht van de beschikbare machtigingen en de afhankelijkheden die u kunt gebruiken bij het maken van aangepaste rollen.

#### <a name="managing-devices"></a>Apparaten beheren

**Machtigingen voor apparaatsjabloon**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Beheren | Weergave <br/> Andere afhankelijkheden: Apparaat-exemplaren weergeven  |
| Volledig beheer | Weergeven, Beheren <br/> Andere afhankelijkheden: Apparaat-exemplaren weergeven |

**Machtigingen voor apparaat-exemplaren**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaatgroepen weergeven |
| Bijwerken | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaatgroepen weergeven  |
| Maken | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaatgroepen weergeven  |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaatgroepen weergeven  |
| Opdrachten uitvoeren | Bijwerken, Weergeven <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaatgroepen weergeven  |
| Onbewerkte gegevens weergeven | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaatgroepen weergeven  |
| Volledig beheer | Weergeven, Bijwerken, Maken, Verwijderen, Opdrachten uitvoeren, Onbewerkte gegevens weergeven <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaatgroepen weergeven  |

**Machtigingen voor apparaatgroepen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaat-exemplaren weergeven |
| Bijwerken | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaat-exemplaren weergeven   |
| Maken | Weergeven, Bijwerken <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaat-exemplaren weergeven   |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaat-exemplaren weergeven  |
| Volledig beheer | Weergeven, Bijwerken, Maken, Verwijderen <br/> Andere afhankelijkheden: Apparaatsjablonen en apparaat-exemplaren weergeven |

**Beheermachtigingen voor apparaatconnectiviteit**

| Name | Afhankelijkheden |
| ---- | -------- |
| Lees-exemplaar | Geen <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaatgroepen en apparaat-exemplaren weergeven |
| Exemplaar beheren | Lees-exemplaar <br /> Andere afhankelijkheden: Apparaatsjablonen, apparaatgroepen en apparaat-exemplaren weergeven |
| Algemeen lezen | Geen   |
| Wereldwijd beheren | Algemeen lezen |
| Volledig beheer | Exemplaar lezen, Exemplaar beheren, Algemeen lezen, Globaal beheren <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaatgroepen en apparaat-exemplaren weergeven |

**Takenmachtigingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaat-exemplaren en apparaatgroepen weergeven |
| Bijwerken | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaat-exemplaren en apparaatgroepen weergeven |
| Maken | Weergeven, Bijwerken <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaat-exemplaren en apparaatgroepen weergeven |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaat-exemplaren en apparaatgroepen weergeven |
| Uitvoeren | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaat-exemplaren en apparaatgroepen weergeven; Apparaat-exemplaren bijwerken; Opdrachten uitvoeren op apparaat-exemplaren |
| Volledig beheer | Weergeven, Bijwerken, Maken, Verwijderen, Uitvoeren <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaat-exemplaren en apparaatgroepen weergeven; Apparaat-exemplaren bijwerken; Opdrachten uitvoeren op apparaat-exemplaren |

**Machtigingen voor regels**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: Apparaatsjablonen weergeven |
| Bijwerken | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen weergeven |
| Maken | Weergeven, Bijwerken <br/> Andere afhankelijkheden: Apparaatsjablonen weergeven |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen weergeven |
| Volledig beheer | Weergeven, Bijwerken, Maken, Verwijderen <br/> Andere afhankelijkheden: Apparaatsjablonen weergeven |

#### <a name="managing-the-app"></a>De app beheren

**Machtigingen voor toepassingsinstellingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Kopiëren | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaatexeports, apparaatgroepen, dashboards, gegevensexport, huisstijl, Help-koppelingen, aangepaste rollen, regels weergeven |
| Verwijderen | Weergave   |
| Volledig beheer | Weergeven, Bijwerken, Kopiëren, Verwijderen <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaatgroepen, toepassingsdashboards, gegevensexport, huisstijl, Help-koppelingen, aangepaste rollen, regels weergeven |

**Exportmachtigingen voor toepassingssjabloon**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Exporteren | Weergave <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaatexeports, apparaatgroepen, dashboards, gegevensexport, huisstijl, Help-koppelingen, aangepaste rollen, regels weergeven |
| Volledig beheer | Weergeven, Exporteren <br/> Andere afhankelijkheden: Apparaatsjablonen, apparaatgroepen, toepassingsdashboards, gegevensexport, huisstijl, Help-koppelingen, aangepaste rollen, regels weergeven |

**Uploadmachtigingen voor apparaatbestand**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Beheren | Weergave   |
| Volledig beheer | Weergeven, Beheren |

**Factureringsmachtigingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Beheren | Geen     |
| Volledig beheer | Beheren |

#### <a name="managing-users-and-roles"></a>Gebruikers en rollen beheren

**Machtigingen voor aangepaste rollen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen |
| Bijwerken | Weergave |
| Maken | Weergeven, Bijwerken |
| Verwijderen | Weergave |
| Volledig beheer | Weergeven, Bijwerken, Maken, Verwijderen |

**Machtigingen voor gebruikersbeheer**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: Aangepaste rollen weergeven |
| Toevoegen | Weergave <br/> Andere afhankelijkheden: Aangepaste rollen weergeven |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: Aangepaste rollen weergeven |
| Volledig beheer | Weergeven, Toevoegen, Verwijderen <br/> Andere afhankelijkheden: Aangepaste rollen weergeven |

> [!NOTE]
> Een gebruiker met een aangepaste rol die hen de machtigingen geeft om andere gebruikers toe te voegen, kan alleen gebruikers toevoegen aan een rol met dezelfde of minder machtigingen dan hun eigen rol.

#### <a name="customizing-the-app"></a>De app aanpassen

**Machtigingen voor toepassingsdashboards**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Maken | Weergeven, Bijwerken |
| Verwijderen | Weergave   |
| Volledig beheer | Weergeven, Bijwerken, Maken, Verwijderen |

**Machtigingen voor persoonlijke dashboards**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Maken | Weergeven, Bijwerken   |
| Verwijderen | Weergave   |
| Volledig beheer | Weergeven, Bijwerken, Maken, Verwijderen |

**Machtigingen voor huisstijl, omgeving en kleuren**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Volledig beheer | Weergeven, Bijwerken |

**Machtigingen voor Help-koppelingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Volledig beheer | Weergeven, Bijwerken |

#### <a name="extending-the-app"></a>De app uitbreiden

**Machtigingen voor gegevensexport**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Maken | Weergeven, Bijwerken  |
| Verwijderen | Weergave   |
| Volledig beheer | Weergeven, Bijwerken, Maken, Verwijderen |

**API-tokenmachtigingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen  <br/> Andere afhankelijkheden: Aangepaste rollen weergeven |
| Maken | Weergave <br/> Andere afhankelijkheden: Aangepaste rollen weergeven |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: Aangepaste rollen weergeven |
| Volledig beheer | Weergeven, Maken, Verwijderen <br/> Andere afhankelijkheden: Aangepaste rollen weergeven |

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u gebruikers en rollen beheert in uw IoT Central toepassing, is de voorgestelde volgende stap het beheren [van uw factuur.](howto-view-bill.md)