---
title: Gebruikers en rollen beheren in azure IoT Central-toepassing | Microsoft Docs
description: Als Administrator, het beheren van gebruikers en rollen in uw Azure IoT Central-toepassing
author: lmasieri
ms.author: lmasieri
ms.date: 12/05/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: f6c45b8d9804f16c4e59d259f562cc03f187e6a0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92122974"
---
# <a name="manage-users-and-roles-in-your-iot-central-application"></a>Gebruikers en rollen beheren in uw IoT Central-toepassing

In dit artikel wordt beschreven hoe u, als beheerder, gebruikers kunt toevoegen, bewerken en verwijderen in uw Azure IoT Central-toepassing. In dit artikel wordt ook beschreven hoe u rollen beheert in uw Azure IoT Central-toepassing.

Voor toegang tot en gebruik van de sectie **beheer** moet u de rol **beheerder** hebben voor een Azure IOT Central-toepassing. Als u een Azure IoT Central-toepassing maakt, wordt u automatisch toegevoegd aan de **beheerdersrol** voor die toepassing.

## <a name="add-users"></a>Gebruikers toevoegen

Elke gebruiker moet een gebruikers account hebben voordat ze zich kunnen aanmelden en toegang hebben tot een Azure IoT Central-toepassing. Micro soft-accounts en-Azure Active Directory accounts worden ondersteund in azure IoT Central. Azure Active Directory-groepen worden momenteel niet ondersteund in azure IoT Central.

Zie [Microsoft-account Help](https://support.microsoft.com/products/microsoft-account?category=manage-account) en  [Quick Start: nieuwe gebruikers toevoegen aan Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md)voor meer informatie.

1. Als u een gebruiker aan een IoT Central-toepassing wilt toevoegen, gaat u naar de pagina **gebruikers** in de sectie **beheer** .
    
    > [!div class="mx-imgBorder"]
    >![Gebruikers beheren](media/howto-manage-users-roles/manage-users-pnp.png)

1. Als u een gebruiker wilt toevoegen, klikt u op de pagina **gebruikers** op **+ gebruiker toevoegen**.

1. Kies een rol voor de gebruiker in de vervolg keuzelijst **rol** . Meer informatie over rollen vindt u in de sectie [rollen beheren](#manage-roles) van dit artikel.

    > [!div class="mx-imgBorder"]
    >![Gebruiker toevoegen en een rol selecteren](media/howto-manage-users-roles/add-user-pnp.png)

    > [!NOTE]
    > Een gebruiker die zich in een aangepaste rol bevindt die de machtiging verleent om andere gebruikers toe te voegen, kan alleen gebruikers toevoegen aan een rol met dezelfde of minder machtigingen dan hun eigen rol.

Als een IoT Central gebruikers-ID uit Azure Active Directory wordt verwijderd en vervolgens opnieuw wordt toegevoegd, kan de gebruiker zich niet aanmelden bij de IoT Central toepassing. Als u de toegang opnieuw wilt inschakelen, moet de beheerder van de IoT Central de gebruiker in de toepassing verwijderen en lezen.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>De rollen bewerken die aan gebruikers zijn toegewezen

Rollen kunnen niet worden gewijzigd nadat ze zijn toegewezen. Als u de rol die is toegewezen aan een gebruiker wilt wijzigen, verwijdert u de gebruiker en voegt u de gebruiker opnieuw toe met een andere rol.

> [!NOTE]
> De toegewezen rollen zijn specifiek voor IoT Central toepassing en kunnen niet worden beheerd vanuit de Azure-Portal.

## <a name="delete-users"></a>Gebruikers verwijderen

Als u gebruikers wilt verwijderen, schakelt u een of meer selectie vakjes op de pagina **gebruikers** in. Selecteer vervolgens **Verwijderen**.

## <a name="manage-roles"></a>Rollen beheren

Met rollen kunt u bepalen wie binnen uw organisatie verschillende taken mag uitvoeren in IoT Central. Er zijn drie ingebouwde rollen die u kunt toewijzen aan gebruikers van uw toepassing. U kunt ook [aangepaste rollen maken](#create-a-custom-role) als u een nauw keurig besturings element nodig hebt.

> [!div class="mx-imgBorder"]
> ![Rollen selecteren beheren](media/howto-manage-users-roles/manage-roles-pnp.png)

### <a name="administrator"></a>Beheerder

Gebruikers met de rol **beheerder** kunnen elk deel van de toepassing beheren en beheersen, met inbegrip van facturering.

De gebruiker die een toepassing maakt, wordt automatisch toegewezen aan de rol **beheerder** . Er moet altijd ten minste één gebruiker **in de beheerdersrol** zijn.

### <a name="builder"></a>Opbouwfunctie

Gebruikers met de rol **Builder** kunnen elk deel van de app beheren, maar kunnen geen wijzigingen aanbrengen op de tabbladen beheer of doorlopend gegevens exporteren.

### <a name="operator"></a>Operator

Gebruikers met de **operator** rol kunnen de status en status van het apparaat bewaken. Ze mogen geen wijzigingen aanbrengen in apparaatprofielen of de toepassing beheren. Opera tors kunnen apparaten toevoegen en verwijderen, Apparaatsets beheren en analyses en taken uitvoeren. 

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

Als uw oplossing nauw keurige toegangs controles vereist, kunt u aangepaste rollen maken met aangepaste sets machtigingen. Als u een aangepaste rol wilt maken, gaat u naar de pagina **rollen** in het gedeelte **beheer** van uw toepassing. Selecteer vervolgens **+ nieuwe rol** en voeg een naam en beschrijving voor uw rol toe. Selecteer de machtigingen die uw rol vereist en selecteer vervolgens **Opslaan**.

U kunt gebruikers toevoegen aan uw aangepaste rol op dezelfde manier als u gebruikers toevoegt aan een ingebouwde rol.

> [!div class="mx-imgBorder"]
> ![Een aangepaste rol bouwen](media/howto-manage-users-roles/create-custom-role-pnp.png)

### <a name="custom-role-options"></a>Opties voor aangepaste rollen

Wanneer u een aangepaste rol definieert, kiest u de set machtigingen die een gebruiker verleent als deze lid is van de rol. Sommige machtigingen zijn afhankelijk van andere. Als u bijvoorbeeld de machtiging **toepassings dashboarden bijwerken** aan een rol toevoegt, wordt de machtiging **app-Dash boards weer geven** automatisch toegevoegd. In de volgende tabellen ziet u een overzicht van de beschik bare machtigingen en hun afhankelijkheden, kunt u gebruiken bij het maken van aangepaste rollen.

#### <a name="managing-devices"></a>Apparaten beheren

**Machtigingen voor de apparaataccount**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Beheren | Weergave <br/> Andere afhankelijkheden: instanties van apparaten weer geven  |
| Volledig beheer | Weer geven, beheren <br/> Andere afhankelijkheden: instanties van apparaten weer geven |

**Machtigingen voor apparaatexemplaar**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: Device-sjablonen en apparaatgroepen weer geven |
| Bijwerken | Weergave <br/> Andere afhankelijkheden: Device-sjablonen en apparaatgroepen weer geven  |
| Maken | Weergave <br/> Andere afhankelijkheden: Device-sjablonen en apparaatgroepen weer geven  |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: Device-sjablonen en apparaatgroepen weer geven  |
| Opdrachten uitvoeren | Bijwerken, weer geven <br/> Andere afhankelijkheden: Device-sjablonen en apparaatgroepen weer geven  |
| Volledig beheer | Weer geven, bijwerken, maken, verwijderen, opdrachten uitvoeren <br/> Andere afhankelijkheden: Device-sjablonen en apparaatgroepen weer geven  |

**Machtigingen voor apparaatgroepen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: Apparaatbeheer en instanties van apparaten weer geven |
| Bijwerken | Weergave <br/> Andere afhankelijkheden: Apparaatbeheer en instanties van apparaten weer geven   |
| Maken | Weer geven, bijwerken <br/> Andere afhankelijkheden: Apparaatbeheer en instanties van apparaten weer geven   |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: Apparaatbeheer en instanties van apparaten weer geven   |
| Volledig beheer | Weer geven, bijwerken, maken, verwijderen <br/> Andere afhankelijkheden: Apparaatbeheer en instanties van apparaten weer geven |

**Machtigingen voor connectiviteits beheer voor apparaten**

| Name | Afhankelijkheden |
| ---- | -------- |
| Exemplaar lezen | Geen <br/> Andere afhankelijkheden: Apparaatinstellingen weer geven, apparaatgroepen, exemplaren van apparaten |
| Exemplaar beheren | Geen |
| Algemeen lezen | Geen   |
| Wereld wijd beheren | Algemeen lezen |
| Volledig beheer | Lees instantie, instantie beheren, algemeen lezen, algemeen beheren. <br/> Andere afhankelijkheden: Apparaatinstellingen weer geven, apparaatgroepen, exemplaren van apparaten |

**Taken machtigingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: Apparaatinstellingen weer geven, exemplaren van apparaten en apparaatgroepen |
| Bijwerken | Weergave <br/> Andere afhankelijkheden: Apparaatinstellingen weer geven, exemplaren van apparaten en apparaatgroepen |
| Maken | Weer geven, bijwerken <br/> Andere afhankelijkheden: Apparaatinstellingen weer geven, exemplaren van apparaten en apparaatgroepen |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: Apparaatinstellingen weer geven, exemplaren van apparaten en apparaatgroepen |
| Uitvoeren | Weergave <br/> Andere afhankelijkheden: Apparaatinstellingen weer geven, exemplaren van apparaten en apparaatgroepen; Exemplaren van apparaten bijwerken; Opdrachten uitvoeren op instanties van apparaten |
| Volledig beheer | Weer geven, bijwerken, maken, verwijderen, uitvoeren <br/> Andere afhankelijkheden: Apparaatinstellingen weer geven, exemplaren van apparaten en apparaatgroepen; Exemplaren van apparaten bijwerken; Opdrachten uitvoeren op instanties van apparaten |

**Machtigingen voor regels**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: Apparaatbeheer weer geven |
| Bijwerken | Weergave <br/> Andere afhankelijkheden: Apparaatbeheer weer geven |
| Maken | Weer geven, bijwerken <br/> Andere afhankelijkheden: Apparaatbeheer weer geven |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: Apparaatbeheer weer geven |
| Volledig beheer | Weer geven, bijwerken, maken, verwijderen <br/> Andere afhankelijkheden: Apparaatbeheer weer geven |

#### <a name="managing-the-app"></a>De app beheren

**Machtigingen voor toepassings instellingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Kopiëren | Weergave <br/> Andere afhankelijkheden: Device-sjablonen weer geven, instanties van apparaten, apparaatgroepen, Dash boards, gegevens exporteren, huis stijl, Help-koppelingen, aangepaste rollen, regels |
| Verwijderen | Weergave   |
| Volledig beheer | Weer geven, bijwerken, kopiëren, verwijderen <br/> Andere afhankelijkheden: Device-sjablonen weer geven, apparaatgroepen, toepassings dashboards, gegevens exporteren, huis stijl, Help-koppelingen, aangepaste rollen, regels |

**Export machtigingen voor de toepassings sjabloon**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Exporteren | Weergave <br/> Andere afhankelijkheden: Device-sjablonen weer geven, instanties van apparaten, apparaatgroepen, Dash boards, gegevens exporteren, huis stijl, Help-koppelingen, aangepaste rollen, regels |
| Volledig beheer | Weer geven, exporteren <br/> Andere afhankelijkheden: Device-sjablonen weer geven, apparaatgroepen, toepassings dashboards, gegevens exporteren, huis stijl, Help-koppelingen, aangepaste rollen, regels |

**Facturerings machtigingen**

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
| Maken | Weer geven, bijwerken |
| Verwijderen | Weergave |
| Volledig beheer | Weer geven, bijwerken, maken, verwijderen |

**Machtigingen voor gebruikers beheer**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen <br/> Andere afhankelijkheden: aangepaste rollen weer geven |
| Toevoegen | Weergave <br/> Andere afhankelijkheden: aangepaste rollen weer geven |
| Verwijderen | Weergave <br/> Andere afhankelijkheden: aangepaste rollen weer geven |
| Volledig beheer | Weer geven, toevoegen, verwijderen <br/> Andere afhankelijkheden: aangepaste rollen weer geven |

> [!NOTE]
> Een gebruiker die zich in een aangepaste rol bevindt die de machtiging verleent om andere gebruikers toe te voegen, kan alleen gebruikers toevoegen aan een rol met dezelfde of minder machtigingen dan hun eigen rol.

#### <a name="customizing-the-app"></a>De App aanpassen

**Toepassings dashboard machtigingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Maken | Weer geven, bijwerken |
| Verwijderen | Weergave   |
| Volledig beheer | Weer geven, bijwerken, maken, verwijderen |

**Machtigingen voor persoonlijke Dash boards**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Maken | Weer geven, bijwerken   |
| Verwijderen | Weergave   |
| Volledig beheer | Weer geven, bijwerken, maken, verwijderen |

**Machtigingen voor huis stijl, favicon en kleuren**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Volledig beheer | Weer geven, bijwerken |

**Machtigingen voor Help links**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Volledig beheer | Weer geven, bijwerken |

#### <a name="extending-the-app"></a>De app uitbreiden

**Machtigingen voor gegevens export**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Bijwerken | Weergave   |
| Maken | Weer geven, bijwerken  |
| Verwijderen | Weergave   |
| Volledig beheer | Weer geven, bijwerken, maken, verwijderen |

**API-token machtigingen**

| Name | Afhankelijkheden |
| ---- | -------- |
| Weergave | Geen     |
| Maken | Weergave   |
| Verwijderen | Weergave   |
| Volledig beheer | Weer geven, maken, verwijderen |

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u gebruikers en rollen in uw Azure IoT Central-toepassing kunt beheren, is de voorgestelde volgende stap om te leren hoe u [uw factuur beheert](howto-view-bill.md).