---
title: Functies voor klant gegevens aanvragen voor Azure Digital Apparaatdubbels
titleSuffix: Azure Digital Twins
description: In dit artikel worden processen weer gegeven voor het exporteren en verwijderen van persoonlijke gegevens in azure Digital Apparaatdubbels.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 9942056e59a46c61b4e9da32aab191d4b72db968
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92461756"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Azure Digital Apparaatdubbels-functies voor klant gegevens aanvragen

Azure Digital Apparaatdubbels is een ontwikkel platform voor het maken van veilige digitale representaties van een bedrijfs omgeving. De weer gaven worden aangedreven door actuele status gegevens uit gegevens bronnen die door gebruikers zijn geselecteerd.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

De digitale representaties genaamd *Digital apparaatdubbels* in azure Digital apparaatdubbels vertegenwoordigen entiteiten in echte omgevingen en zijn gekoppeld aan id's. Micro soft onderhoudt geen informatie en heeft geen toegang tot gegevens waarmee id's kunnen worden gecorreleerd aan gebruikers. 

Veel van de digitale apparaatdubbels in azure Digital Apparaatdubbels vertegenwoordigen niet rechtstreeks persoonlijke entiteiten. typische objecten die worden weer gegeven, kunnen bijvoorbeeld een kantoor kamer of een fabrieks basis zijn. Gebruikers kunnen bijvoorbeeld een aantal entiteiten beschouwen als persoonlijk identificeerbaar, en kunnen hun eigen activa of inventaris tracerings methoden hand haven die digitale apparaatdubbels koppelen aan individuen. Met Azure Digital Apparaatdubbels worden alle gegevens die zijn gekoppeld aan digitale apparaatdubbels, beheerd en opgeslagen alsof deze persoons gegevens zijn.

Om persoons gegevens weer te geven, te exporteren en te verwijderen waarnaar kan worden verwezen in een aanvraag voor een gegevens onderwerp, kan een Azure Digital Apparaatdubbels-beheerder de [**Azure Portal**](https://portal.azure.com/) gebruiken voor gebruikers en rollen, of de [**Azure Digital Apparaatdubbels rest-api's**](/rest/api/azure-digitaltwins/) voor digitale apparaatdubbels. De Azure Portal-en REST-Api's bieden verschillende methoden voor gebruikers om dergelijke verzoeken van gegevens te verwerken.

## <a name="identifying-customer-data"></a>Klant gegevens identificeren

Azure Digital Apparaatdubbels beschouwt *persoonlijke gegevens* als gegevens die zijn gekoppeld aan de beheerders en gebruikers. 

Azure Digital Apparaatdubbels slaat de [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) *object-id* op van gebruikers die toegang hebben tot de omgeving. Met Azure Digital Apparaatdubbels in de Azure Portal worden e-mail adressen van gebruikers weer gegeven, maar deze e-mail adressen worden niet opgeslagen in azure Digital Apparaatdubbels. Ze worden dynamisch opgezocht in Azure Active Directory, met behulp van de Azure Active Directory object-ID.

## <a name="deleting-customer-data"></a>Klant gegevens verwijderen

Azure Digital Apparaatdubbels-beheerders kunnen de Azure Portal gebruiken om gegevens te verwijderen die betrekking hebben op gebruikers. Het is ook mogelijk om delete-bewerkingen uit te voeren op afzonderlijke digitale apparaatdubbels met behulp van de Azure Digital Apparaatdubbels REST-Api's. Zie [Azure Digital APPARAATDUBBELS rest api's documentation](/rest/api/azure-digitaltwins/)(Engelstalig) voor meer informatie over de beschik bare api's.

## <a name="exporting-customer-data"></a>Klant gegevens exporteren

Azure Digital Apparaatdubbels slaat gegevens op die betrekking hebben op digitale apparaatdubbels. Gebruikers kunnen deze gegevens ophalen en bekijken via REST-Api's en deze gegevens exporteren met behulp van kopiëren en plakken. 

Klant gegevens, met inbegrip van gebruikers rollen en roltoewijzingen, kunnen worden geselecteerd, gekopieerd en geplakt uit het Azure Portal. 

## <a name="links-to-additional-documentation"></a>Koppelingen naar aanvullende documentatie

Zie de [documentatie van Azure Digital APPARAATDUBBELS rest api's](/rest/api/azure-digitaltwins/)voor een volledige lijst met de api's van de Azure Digital apparaatdubbels-service.