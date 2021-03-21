---
title: Naslag informatie over SAP SuccessFactors-kenmerken
description: Meer informatie over de kenmerken van SuccessFactors die worden ondersteund door SuccessFactors-HR ingericht inrichten
services: active-directory
author: cmmdesai
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: reference
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.reviewer: celested
ms.openlocfilehash: 0d1a25f67d3550e79b2339ee060d37acaea0c1c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255539"
---
# <a name="sap-successfactors-attribute-reference"></a>Naslag informatie over SAP SuccessFactors-kenmerken

In dit artikel vindt u informatie over:

- [SuccessFactors entiteiten en kenmerken](#supported-successfactors-entities-and-attributes)
- [Toewijzing van standaard kenmerk](#default-attribute-mapping)

## <a name="supported-successfactors-entities-and-attributes"></a>Ondersteunde SuccessFactors-entiteiten en-kenmerken

In de onderstaande tabel wordt de lijst met SuccessFactors-kenmerken die standaard zijn opgenomen in de volgende twee Provisioning-apps vastgelegd:

- [SuccessFactors Active Directory gebruikers inrichten](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
- [SuccessFactors naar Azure AD-gebruikers inrichten](../saas-apps/sap-successfactors-inbound-provisioning-cloud-only-tutorial.md)

Raadpleeg de [Naslag informatie over SAP SuccessFactors-integratie](./sap-successfactors-integration-reference.md#retrieving-additional-attributes) om het schema uit te breiden voor extra kenmerken. 

| \# | SuccessFactors entiteit                  | SuccessFactors-kenmerk     | Type bewerking |
|----|----------------------------------------|------------------------------|----------------|
| 1  | PerPerson                              | personIdExternal             | Lezen           |
| 2  | PerPerson                              | personId                     | Lezen           |
| 3  | PerPerson                              | perPersonUuid                | Lezen           |
| 4  | PerPersonal                            | displayName                  | Lezen           |
| 5  | PerPersonal                            | voornaam                    | Lezen           |
| 6  | PerPersonal                            | geslacht                       | Lezen           |
| 7  | PerPersonal                            | achternaam                     | Lezen           |
| 8  | PerPersonal                            | middelste naam                   | Lezen           |
| 9  | PerPersonal                            | voorkeursnaam                | Lezen           |
| 10 | Gebruiker                                   | addressLine1                 | Lezen           |
| 11 | Gebruiker                                   | addressLine2                 | Lezen           |
| 12 | Gebruiker                                   | addressLIne3                 | Lezen           |
| 13 | Gebruiker                                   | Kaart Businessphone                | Lezen           |
| 14 | Gebruiker                                   | cellPhone                    | Lezen           |
| 15 | Gebruiker                                   | city                         | Lezen           |
| 16 | Gebruiker                                   | country                      | Lezen           |
| 17 | Gebruiker                                   | custom01                     | Lezen           |
| 18 | Gebruiker                                   | custom02                     | Lezen           |
| 19 | Gebruiker                                   | custom03                     | Lezen           |
| 20 | Gebruiker                                   | custom04                     | Lezen           |
| 21 | Gebruiker                                   | custom05                     | Lezen           |
| 22 | Gebruiker                                   | custom06                     | Lezen           |
| 23 | Gebruiker                                   | custom07                     | Lezen           |
| 24 | Gebruiker                                   | custom08                     | Lezen           |
| 25 | Gebruiker                                   | custom09                     | Lezen           |
| 26 | Gebruiker                                   | custom10                     | Lezen           |
| 27 | Gebruiker                                   | custom11                     | Lezen           |
| 28 | Gebruiker                                   | custom12                     | Lezen           |
| 29 | Gebruiker                                   | custom13                     | Lezen           |
| 30 | Gebruiker                                   | custom14                     | Lezen           |
| 31 | Gebruiker                                   | empId                        | Lezen           |
| 32 | Gebruiker                                   | homePhone                    | Lezen           |
| 33 | Gebruiker                                   | jobFamily                    | Lezen           |
| 34 | Gebruiker                                   | bijnaam                     | Lezen           |
| 35 | Gebruiker                                   | staat                        | Lezen           |
| 36 | Gebruiker                                   | timeZone                     | Lezen           |
| 37 | Gebruiker                                   | gebruikersnaam                     | Lezen           |
| 38 | Gebruiker                                   | zipCode                      | Lezen           |
| 39 | PerPhone                               | Nummer                     | Lezen           |
| 40 | PerPhone                               | countryCode                  | Lezen           |
| 41 | PerPhone                               | switch                    | Lezen           |
| 42 | PerPhone                               | phoneNumber                  | Lezen           |
| 43 | PerPhone                               | phoneType                    | Lezen           |
| 44 | PerEmail                               | emailAddress                 | Lezen, schrijven    |
| 45 | PerEmail                               | emailType                    | Lezen           |
| 46 | EmpEmployment                          | firstDateWorked              | Lezen           |
| 47 | EmpEmployment                          | lastDateWorked               | Lezen           |
| 48 | EmpEmployment                          | userId                       | Lezen           |
| 49 | EmpEmployment                          | isContingentWorker           | Lezen           |
| 50 | EmpJob                                 | countryOfCompany             | Lezen           |
| 51 | EmpJob                                 | emplStatus                   | Lezen           |
| 52 | EmpJob                                 | endDate                      | Lezen           |
| 53 | EmpJob                                 | Begin                    | Lezen           |
| 54 | EmpJob                                 | jobTitle                     | Lezen           |
| 55 | EmpJob                                 | position                     | Lezen           |
| 65 | EmpJob                                 | customString13               | Lezen           |
| 56 | EmpJob                                 | managerId                    | Lezen           |
| 57 | EmpJob \. BusinessUnit                   | businessUnit                 | Lezen           |
| 58 | EmpJob \. BusinessUnit                   | businessUnitId               | Lezen           |
| 59 | \.Bedrijf EmpJob                        | bedrijf                      | Lezen           |
| 60 | \.Bedrijf EmpJob                        | companyId                    | Lezen           |
| 61 | EmpJob \. Company \. CountryOfRegistration | twoCharCountryCode           | Lezen           |
| 62 | EmpJob \. CostCenter                     | costCenter                   | Lezen           |
| 63 | EmpJob \. CostCenter                     | costCenterId                 | Lezen           |
| 64 | EmpJob \. CostCenter                     | costCenterDescription        | Lezen           |
| 65 | EmpJob \. afdeling                     | department                   | Lezen           |
| 66 | EmpJob \. afdeling                     | departmentId                 | Lezen           |
| 67 | EmpJob- \. deling                       | division                     | Lezen           |
| 68 | EmpJob- \. deling                       | divisionId                   | Lezen           |
| 69 | EmpJob \. JobCode                        | jobCode                      | Lezen           |
| 70 | EmpJob \. JobCode                        | jobCodeId                    | Lezen           |
| 71 | Locatie van EmpJob \.                       | Locatie                 | Lezen           |
| 72 | Locatie van EmpJob \.                       | officeLocationAddress        | Lezen           |
| 73 | Locatie van EmpJob \.                       | officeLocationCity           | Lezen           |
| 74 | Locatie van EmpJob \.                       | officeLocationCustomString4  | Lezen           |
| 75 | Locatie van EmpJob \.                       | officeLocationZipCode        | Lezen           |
| 76 | EmpJob \. PayGrade                       | payGrade                     | Lezen           |
| 77 | EmpEmploymentTermination               | activeEmploymentsCount       | Lezen           |
| 78 | EmpEmploymentTermination               | latestTerminationDate        | Lezen           |

## <a name="default-attribute-mapping"></a>Toewijzing van standaard kenmerk

De volgende tabel bevat de standaard kenmerk toewijzing tussen de hierboven vermelde SuccessFactors-kenmerken en AD/Azure AD-kenmerken. In de Blade ' toewijzing ' van Azure AD-inrichtings toepassing kunt u deze standaard toewijzing wijzigen zodat deze kenmerken bevat uit de bovenstaande lijst. 

| \# | SuccessFactors entiteit                  | SuccessFactors-kenmerk | Standaard AD/Azure AD-kenmerk toewijzing   | Verwerkings Opmerking                                                                            |
|----|----------------------------------------|--------------------------|-----------------------------------------|----------------------------------------------------------------------------------------------|
| 1  | PerPerson                              | personIdExternal         | employeeId                              | Gebruikt als overeenkomend kenmerk                                                                   |
| 2  | PerPerson                              | perPersonUuid            | \[Niet toegewezen \- als bron anker\] | Tijdens de eerste synchronisatie koppelt de inrichtings service de personUuid aan bestaande objectGuid\.  |
| 3  | PerPersonal                            | displayName              | displayName                             | NA                                                                                           |
| 4  | PerPersonal                            | voornaam                | givenName                               | NA                                                                                           |
| 5  | PerPersonal                            | achternaam                 | sn                                      | NA                                                                                           |
| 6  | Gebruiker                                   | addressLine1             | streetAddress                           | NA                                                                                           |
| 7  | Gebruiker                                   | city                     | l                                       | NA                                                                                           |
| 8  | Gebruiker                                   | country                  | co                                      | NA                                                                                           |
| 9  | Gebruiker                                   | staat                    | st                                      | NA                                                                                           |
| 10 | Gebruiker                                   | gebruikersnaam                 | samAccountName                          | NA                                                                                           |
| 11 | Gebruiker                                   | zipCode                  | postalCode                              | NA                                                                                           |
| 12 | PerEmail                               | emailAddress             | mail                                    | NA                                                                                           |
| 13 | EmpJob                                 | jobTitle                 | titel                                   | NA                                                                                           |
| 14 | EmpJob                                 | managerId                | manager                                 | NA                                                                                           |
| 15 | EmpJob \. Company \. CountryOfRegistration | twoCharCountryCode       | c                                       | NA                                                                                           |
| 16 | EmpJob \. afdeling                     | department               | department                              | NA                                                                                           |
| 17 | EmpJob- \. deling                       | division                 | bedrijf                                 | NA                                                                                           |
| 18 | Locatie van EmpJob \.                       | officeLocationAddress    | streetAddress                           | NA                                                                                           |
| 19 | Locatie van EmpJob \.                       | officeLocationZipCode    | postalCode                              | NA                                                                                           |
| 20 | EmpEmploymentTermination               | activeEmploymentsCount   | accountEnabled                          | Als activeEmploymentsCount = 0, schakelt u de account\. uit                                           |
