---
title: IVMS101 Guide
excerpt: >-
  InterVASP Messaging Standards 101 (hereinafter referred to as IVMS101)
  provides a standardized data model for the Originator and Beneficiary
  information that VASPs must exchange to meet the requirements of the Financial
  Action Task Force (FATF) Travel Rule.
deprecated: false
hidden: false
metadata:
  robots: index
---
Currently, many Travel Rule solutions, including VerifyVASP, have adopted IVMS101 as their messaging protocol, and it will become the minimum requirement to enable interoperability between Travel Rule solutions in the future. Therefore, **developers and operations managers of each VASP that intends to integrate with VerifyVASP must be well-versed in the IVMS101 specifications.**

> 📘 IVMS101 Official Specification Document
>
> The official specification document for IVMS101 can be downloaded from the following link
>
> * [https://intervasp.org/](https://intervasp.org/)
>
> Since the IVMS101 standard may be upgraded in the future, it is important to monitor any specification changes continuously.

<br />

## Table of Contents

The following content is provided in this guide.

### 1. Definitions of Terms

You can check the definitions of the terms used in IVMS101.

### 2. Handling of multiple character sets

You can find the rules for representing language in IVMS101.

### 3. Formatting Notations

You can find the rules for data notation in IVMS101.

### 4. Data Model

You can check the data model of the IVMS101 messaging protocol.

### 5. Data Types

You can check the types used in the data model of the IVMS101 messaging protocol.

### 6. IVMS101 Message Format Guide

You can refer to the data input guide for using the IVMS101 messaging protocol.

### 7. IVMS101 Personal Data Code

You can check the code types for personal information categories used in IVMS101.

<br />

## Definitions of Terms

<Table>
  <thead>
    <tr>
      <th>
        Term
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        beneficiary
      </td>

      <td>
        Refers to the natural or legal person or legal arrangement who is identified by the originator as the receiver of the requested VA transfer.
      </td>
    </tr>

    <tr>
      <td>
        beneficiary VASP
      </td>

      <td>
        Refers to the VASP which receives the transfer of a VA from the originating VASP directly or through an intermediary VASP and makes the funds available to the beneficiary.
      </td>
    </tr>

    <tr>
      <td>
        component
      </td>

      <td>
        Refers to a composite datatype that consists of one or more elements that can be reused across data entities.
      </td>
    </tr>

    <tr>
      <td>
        constraint
      </td>

      <td>
        Refers to a rule, limitation or control enforced on a datatype, element, component or entity that restricts the content being created or amended.
      </td>
    </tr>

    <tr>
      <td>
        country
      </td>

      <td>
        Refers to a nation with its own government.
      </td>
    </tr>

    <tr>
      <td>
        country subdivision
      </td>

      <td>
        Refers to an administrative subdivision of a country.
      </td>
    </tr>

    <tr>
      <td>
        data entity
      </td>

      <td>
        Refers to a single object in a data model that can be distinctly identified.
      </td>
    </tr>

    <tr>
      <td>
        data type
      </td>

      <td>
        Refers to an attribute of an element that reflects the possible values that can be represented by the element.
      </td>
    </tr>

    <tr>
      <td>
        element
      </td>

      <td>
        Refers to a unit of data that has precise meaning, serving to identify the attributes of a data entity.
      </td>
    </tr>

    <tr>
      <td>
        intermediary VASP
      </td>

      <td>
        Refers to a VASP in a serial chain that receives and retransmits a VA transfer on behalf of the originating VASP and the beneficiary VASP, or another intermediary VASP.
      </td>
    </tr>

    <tr>
      <td>
        legal person
      </td>

      <td>
        Refers to any entity other than a natural person that can establish a permanent customer relationship with an affected entity or otherwise own property. This can include companies, bodies corporate, foundations, anstalt, partnerships, or associations and other relevantly similar entities.
      </td>
    </tr>

    <tr>
      <td>
        natural person
      </td>

      <td>
        Refers to a uniquely distinguishable individual; one single person.
      </td>
    </tr>

    <tr>
      <td>
        originating VASP
      </td>

      <td>
        Refers to the VASP which initiates the VA transfer and transfers the VA upon receiving the request for a VA transfer on behalf of the originator.
      </td>
    </tr>

    <tr>
      <td>
        originator
      </td>

      <td>
        Refers to the account holder who allows the VA transfer from that account or, where there is no account, the natural or legal person that places the order with the originating VASP to perform the VA transfer.
      </td>
    </tr>

    <tr>
      <td>
        recommendation
      </td>

      <td>
        Refers to a FATF Recommendation.
      </td>
    </tr>

    <tr>
      <td>
        registration authority
      </td>

      <td>
        Refers to a corporate or business registry, or other national or local authority that maintains the authoritative source of information about legal entities operating in its jurisdiction.
      </td>
    </tr>

    <tr>
      <td>
        transfer
      </td>

      <td>
        Refers to conducting a transaction on behalf of another natural or legal person that moves a virtual asset from one virtual asset address or account to another.
      </td>
    </tr>

    <tr>
      <td>
        virtual asset
      </td>

      <td>
        Refers to a digital representation of value that can be digitally traded, or transferred, and can be used for payment or investment purposes. Virtual assets do not include digital representations of fiat currencies, securities and other financial assets that are already covered elsewhere in the FATF Recommendations.
      </td>
    </tr>

    <tr>
      <td>
        virtual asset service provider
      </td>

      <td>
        Refers to any natural or legal person who is not covered elsewhere under the FATF Recommendations, and as a business conducts one or more of the following activities or operations for or on behalf of another natural or legal person:

        i) exchange between virtual assets and fiat currencies;
        ii) exchange between one or more forms of virtual assets;
        iii) transfer of virtual assets;
        iv) safekeeping and/or administration of virtual assets or instruments enabling control over virtual assets;
        v) participation in and provision of financial services related to an issuer’s offer and/or sale of a virtual asset.
      </td>
    </tr>
  </tbody>
</Table>

<br />

## Handling of multiple character sets

The language used within IVMS101 must follow the following rules.

1. Data shall be submitted using UTF-8 character encoding.
2. Unless otherwise specified, data shall be represented in Latin script ( A-Z, a-z) and Arabic numerals (0-9).
3. Where data is in a national language that does not use Latin script, it must be either:

* Transliterated into Latin characters.
* Translated into English

4. If there is an element within the data type that starts with "local," it can be transmitted using the national language.
5. The following scripts shall be transliterated into Latin characters using the corresponding standards set out below:

| Script                     | Standard                       |
| -------------------------- | ------------------------------ |
| Arabic (Arabic language)   | ISO 233-2:1993                 |
| Arabic (Persian language)  | ISO 233-3:1999                 |
| Armenian                   | ISO 9985:1996                  |
| Cyrillic                   | ISO 9:1995                     |
| Devanagari & related Indic | ISO 15919:2001                 |
| Han (Hanzi, Kanji, Hanja)  | ISO 7098:2015                  |
| Hebrew                     | ISO 259-2:1994                 |
| Georgian                   | ISO 9984:1996                  |
| Greek                      | ISO 843:1997                   |
| Kana                       | ISO 3602:1989                  |
| Korean                     | Revised Romanization of Korean |
| Thai                       | ISO 11940-2:2007               |

<br />

## Formatting Notations

The rules for representing data in IVMS101 are as follows.

1. elementEntities and components will follow an upper camel case convention with no spaces, with the first word capitalized.
2. Every element in IVMS101 will follow the lower camel case convention with no spaces; the first word will not be capitalized.
3. Datatypes will be referred to in the upper camel case.
4. Any term used within an entity, component, or element name does not necessarily dictate the value of such an object. For example, buildingNumber and other elements where the name contains the word ‘Number’ do not have to consist solely of numerals.
5. Unless otherwise specified, all values are case insensitive.

<br />

## Data model

It includes the structures and constraints applicable to the following entities:

List of Data model

```json JSON
// IVMS101 Data Model Example 
{
  "Originator": {
    // Originator model
  },
  "Beneficiary": {
    // Beneficiary model
  },
  "OriginatingVASP": {
    // OriginatingVASP model
  },
  "BeneficiaryVASP": {
    // BeneficiaryVASP model
  },
  "TransferPath": {
    // TransferPath model
  },
  "PayloadMetadata": {
    // PayloadMetadata model
  }
}
```

<br />

### Originator model

Data Model Containing the Information of the Virtual Asset Originator.

```json
// Originator model Example

{
  "originatorPersons": [
    { ... } // Person type
  ],
  "accountNumber": [
    "xxxxxxx" // Text type
  ]
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        originatorPersons
      </td>

      <td>
        Array of Person
      </td>

      <td>
        Required
      </td>

      <td>
        Information of the Virtual Asset Transfer Requestor

        Must contain one or more values of the `Person` type.
      </td>
    </tr>

    <tr>
      <td>
        accountNumber
      </td>

      <td>
        Array of Text
      </td>

      <td>
        Required
      </td>

      <td>
        Wallet address used in the transaction

        Case-sensitive.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### Beneficiary model

Data Model Containing the Information of the Virtual Asset Beneficiary.

```json
//Beneficiary model Example

{
  "beneficiaryPersons": [
    { ... } // Person type
  ],
  "accountNumber": [
    "xxxxxxx"
  ]
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        beneficiaryPersons
      </td>

      <td>
        Array of Person
      </td>

      <td>
        Required
      </td>

      <td>
        Information of the Virtual Asset Transfer Requestor

        Information of the Virtual Asset Transfer Receiver
      </td>
    </tr>

    <tr>
      <td>
        accountNumber
      </td>

      <td>
        Array of Text
      </td>

      <td>
        Required
      </td>

      <td>
        Wallet address used in the transaction

        Case-sensitive.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### OriginatingVASP model

Data Model Containing the Information of the Originating VASP for Virtual Asset Transfers.

```json
// OriginatingVASP model Example

{
  "originatingVASP": {
    ...
  }  // Person type
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        originatingVASP
      </td>

      <td>
        Person
      </td>

      <td>
        Optional
      </td>

      <td>
        Information of the Virtual Asset Transfer Requestor

        Information of the VASP Sending the Virtual Asset

        <br />

        In the current VerifyVASP service structure, the information of the Originating VASP is automatically filled in by the VV Central Server managed by VerifyVASP during the verification request process.

        <br />

        Therefore, this field does not need to be manually entered into the IVMS101 structure when performing User Verification; it will function correctly without it.

        <br />

        However, information other than the OriginatingVASP (e.g., information about the Beneficiary) must be entered.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### BeneficiaryVASP model

Data Model Containing the Information of the Beneficiary VASP for Virtual Asset Transfers.

```json
// BeneficiaryVASP model Example

{
  "beneficiaryVASP": {
    ...
  }  // Person type
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        beneficiaryVASP
      </td>

      <td>
        Person
      </td>

      <td>
        Optional
      </td>

      <td>
        Information of the VASP Receiving the Virtual Asset

        In the current VerifyVASP service structure, the information of the Beneficiary VASP is automatically filled in by the VV Central Server managed by VerifyVASP during the verification request process.

        Therefore, this field does not need to be manually entered into the IVMS101 structure when performing User Verification; it will function correctly without it.

        However, information other than the
        BeneficiaryVASP (e.g., information about the Beneficiary) must be entered.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### TransferPath model

This is the data model containing information about intermediary VASPs involved in the virtual asset transfer. Currently, **VerifyVASP does not support TransferPath configuration.**

```json
//TransferPath model Example

{
  "transferPath": [
    { ... } // IntermediaryVASP type
  ]
}
```

<br />

### PayloadMetadata model

Data Model Containing Additional Metadata.

```json
// PayloadMetadata model Example

{
  "transliterationMethod": [
    "kore"
  ]
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        transliterationMethod
      </td>

      <td>
        Array of Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Field specifying the method used to convert non-Latin scripts into Latin scripts.

        You can enter one of the code values from the table below.
      </td>
    </tr>
  </tbody>
</Table>

<Image align="center" src="https://files.readme.io/ddf8d360f11360e3fa984e3f70c468b25c56ae11e3a52419d0da3e61ab863873-transliterationMethod.png" />

<br />

## Data type

You can verify the type of data object that will be used as a value in the IVMS101 data model.

* **List of IVMS101 Data type**
  * Person type
  * NaturalPerson type
  * LegalPerson type
  * NaturalPersonName type
  * LegalPersonName type
  * NaturalPersonNameID type, LocalNaturalPersonNameID type
  * LegalPersonNameID type, LocalLegalPersonNameID type
  * Address type
  * DateAndPlaceOfBirth type
  * NationalIdentification type
  * IntermediaryVASP type

<br />

### Person type

This is an object that verifies whether the entity is an individual or a legal entity. You can click here to check the input method in accordance with the IVMS101 format.

```json
// Person type Example

{
  "naturalPerson": {
    ...
  },  // NaturalPerson type
  "legalPerson": {
    ...
  },  // LegalPerson type
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        naturalPerson
      </td>

      <td>
        NaturalPerson
      </td>

      <td>
        Optional
        At least one of the two types (`naturalPerson`, `legalPerson`) must be present.
      </td>

      <td>
        Indicates that the object is an individual.
      </td>
    </tr>

    <tr>
      <td>
        legalPerson
      </td>

      <td>
        LegalPerson
      </td>

      <td>
        Optional
        At least one of the two types (`naturalPerson`, `legalPerson`) must be present.
      </td>

      <td>
        Indicates that the object is a legal entity.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### NaturalPerson type

This is an object that contains identifiable information about an individual.

```json
// NaturalPerson type Example

{
  "name": {
    ...
  },  // NaturalPersonName type
  "geographicAddress": [
    { ... }  //Address type
  ],
  "nationalIdentification": {
    ... 
  },  // NationalIdentification type
  "customerIdentification": "xxx",
  "dataAndPlaceOfBirth": {
    ...
  },  // DataAndPlaceOfBirth type
  "countryOfResidence": "KR",
  "nationality": "KR" 
}
```

> ⚠️ When using the `NaturalPerson type`, at least one of the following fields must be included in addition to the name field.
>
> 1. `geographicAddress`
> 2. `nationalIdentification`
> 3. `customerIdentification`
> 4. `dataAndPlaceOfBirth`

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        name
      </td>

      <td>
        NaturalPersonName
      </td>

      <td>
        Required
      </td>

      <td>
        Name of the individual.
      </td>
    </tr>

    <tr>
      <td>
        geographicAddress
      </td>

      <td>
        Array of Address
      </td>

      <td>
        Optional
      </td>

      <td>
        The geographic address of the individual's place of residence.
      </td>
    </tr>

    <tr>
      <td>
        nationalIdentification
      </td>

      <td>
        NationalIdentification
      </td>

      <td>
        Optional
      </td>

      <td>
        National identification number of the individual.
      </td>
    </tr>

    <tr>
      <td>
        customerIdentification
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        User ID within the VASP.
      </td>
    </tr>

    <tr>
      <td>
        dataAndPlaceOfBirth
      </td>

      <td>
        DataAndPlaceOfBirth
      </td>

      <td>
        Optional
      </td>

      <td>
        Date of birth and place of birth of the individual.
      </td>
    </tr>

    <tr>
      <td>
        countryOfResidence
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Current country of residence of the individual.
        You must enter the two-letter country code as per the ISO-3166-1 alpha-2 standard.
      </td>
    </tr>

    <tr>
      <td>
        nationality
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Nationality of the individual.
        You must enter the two-letter country code as per the ISO-3166-1 alpha-2 standard.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### LegalPerson type

This is an object that contains identifiable information about an individual.

```json
//LegalPerson type Example

{
  "name": { 
    ...
  },  // LegalPersonName type
  "geographicAddress": [
    { ... }  // Address type
  ],
  "customerIdentification": "xxx",
  "nationalIdentification": {
    ...
  },  // NationalIdentification type
  "countryOfRegistration": "KR",
  "dateOfIncorporation": "2019-03-27" 
}
```

> ⚠️ When using the `LegalPerson type`, at least one of the following fields must be included in addition to the `name` field.
>
> 1. `geographicAddress`
> 2. `nationalIdentification`
> 3. `customerIdentification`

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        name
      </td>

      <td>
        NaturalPersonName
      </td>

      <td>
        Required
      </td>

      <td>
        Name of the individual.
      </td>
    </tr>

    <tr>
      <td>
        geographicAddress
      </td>

      <td>
        Array of Address
      </td>

      <td>
        Optional
      </td>

      <td>
        The geographic address of the individual's place of residence.
      </td>
    </tr>

    <tr>
      <td>
        nationalIdentification
      </td>

      <td>
        NationalIdentification
      </td>

      <td>
        Optional
      </td>

      <td>
        National identification number of the individual.
      </td>
    </tr>

    <tr>
      <td>
        customerIdentification
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        User ID within the VASP.
      </td>
    </tr>

    <tr>
      <td>
        dataAndPlaceOfBirth
      </td>

      <td>
        DataAndPlaceOfBirth
      </td>

      <td>
        Optional
      </td>

      <td>
        Date of birth and place of birth of the individual.
      </td>
    </tr>

    <tr>
      <td>
        countryOfResidence
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Current country of residence of the individual.
        You must enter the two-letter country code as per the ISO-3166-1 alpha-2 standard.
      </td>
    </tr>

    <tr>
      <td>
        dateOfIncorporation
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Date of establishment of the legal entity.
        It must be entered in the format `YYYY-MM-DD`.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### NaturalPersonName type

This is an object that verifies whether the entity is an individual or a legal entity. You can click here to check the input method in accordance with the IVMS101 format.

```json
{
  "nameIdentifier": [ 
    { ... } // NaturalPersonNameID type
  ],
  "localNameIdentifier": [ 
    { ... } // LocalNaturalPersonNameID type
  ],
  "phoneticNameIdentifier": [ 
    { ... } // LocalNaturalPersonNameID type
  ]
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        nameIdentifier
      </td>

      <td>
        Array of NaturalPersonNameID
      </td>

      <td>
        Required
      </td>

      <td>
        Basic Name Field.
        You can enter an array of one or more `NaturalPersonNameID` types.
        One of the `NaturalPersonNameID` values must be of the `LEGL`(legal name) type.
      </td>
    </tr>

    <tr>
      <td>
        localNameIdentifier
      </td>

      <td>
        Array of LocalNaturalPersonNameID
      </td>

      <td>
        Optional
      </td>

      <td>
        Name expressed in the local language.
        You can enter an array of zero or more `LocalNaturalPersonNameID` types.
      </td>
    </tr>

    <tr>
      <td>
        phoneticNameIdentifier
      </td>

      <td>
        Array of LocalNaturalPersonNameID
      </td>

      <td>
        Optional
      </td>

      <td>
        Phonetic representation of the name.
        You can enter an array of zero or more `LocalNaturalPersonNameID` types.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### LegalPersonName type

```json
{
  "nameIdentifier": [ 
    { ... } // LegalPersonNameID type
  ],
  "localNameIdentifier": [ 
    { ... } // LocalLegalPersonNameID type
  ],
  "phoneticNameIdentifier": [ 
    { ... } // LocalLegalPersonNameID type
  ]
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        nameIdentifier
      </td>

      <td>
        Array of LegalPersonNameID
      </td>

      <td>
        Required
      </td>

      <td>
        Basic Name Field.
        You can enter an array of one or more `LegalPersonNameID` types.
        One of the `LegalPersonNameID` values must be of the `LEGL`(legal name) type.
      </td>
    </tr>

    <tr>
      <td>
        localNameIdentifier
      </td>

      <td>
        Array of LocalLegalPersonNameID
      </td>

      <td>
        Optional
      </td>

      <td>
        Name expressed in the local language.
        You can enter an array of zero or more `LocalLegalPersonNameID` types.
      </td>
    </tr>

    <tr>
      <td>
        phoneticNameIdentifier
      </td>

      <td>
        Array of LocalLegalPersonNameID
      </td>

      <td>
        Optional
      </td>

      <td>
        Phonetic representation of the name.
        You can enter an array of zero or more `LocalLegalPersonNameID` types.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### NaturalPersonNameID type, LocalNaturalPersonNameID type

The `NaturalPersonNameID` type and the `LocalNaturalPersonNameID` type share the same format.

```json
// NaturalPersonNameID, LocalNaturalPersonNameID type Example

{
  "primaryIdentifier": "Din",
  "secondaryIdentifier": "James",
  "nameIdentifierType": "LEGL"
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        primaryIdentifier
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        Surname of the Individual.

        If separating the surname from the given name is not feasible, you can enter the full name in this field.
      </td>
    </tr>

    <tr>
      <td>
        secondaryIdentifier
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Given Name of the Individual.
      </td>
    </tr>

    <tr>
      <td>
        nameIdentifierType
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        Type of Name.

        The possible values for this field are as follows:

        `ALIA`: An alias that is well-known besides the legally registered name

        `BIRT`: Birth name

        `MAID`: Original name used before changing surname due to marriage

        `LEGL`: Legally registered name

        `MISC`: Miscellaneous name that does not fall under any of the above categories
      </td>
    </tr>
  </tbody>
</Table>

<br />

### LegalPersonNameID type, LocalLegalPersonNameID type

The `LegalPersonNameID` type and the `LocalLegalPersonNameID` type share the same format.

```json
// LegalPersonNameID, LocalLegalPersonNameID type Example

{
  "legalPersonName": "Samsung Electronics Co., Ltd.",
  "legalPersonNameIdentifierType": "LEGL"
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        legalPersonName
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        Name of the Legal Entity.

        If separating the name into parts is not feasible, you can enter the full name in this field.
      </td>
    </tr>

    <tr>
      <td>
        legalPersonNameIdentifierType
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        Type of Legal Entity Name.

        <br />

        The possible values for this field are as follows:\
        `LEGL`: Legally registered name.

        `SHRT`: Abbreviated name of the legal entity.

        `TRAD`: Name used by the entity for commercial purposes.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### LegalPersonNameID type, LocalLegalPersonNameID type

The `LegalPersonNameID` type and the `LocalLegalPersonNameID` type share the same format.

```json
// LegalPersonNameID, LocalLegalPersonNameID type Example

{
  "legalPersonName": "Samsung Electronics Co., Ltd.",
  "legalPersonNameIdentifierType": "LEGL"
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        legalPersonName
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        Name of the Legal Entity.

        If separating the name into parts is not feasible, you can enter the full name in this field.
      </td>
    </tr>

    <tr>
      <td>
        legalPersonNameIdentifierType
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        Type of Legal Entity Name.

        <br />

        The possible values for this field are as follows:\
        `LEGL`: Legally registered name.

        `SHRT`: Abbreviated name of the legal entity.

        `TRAD`: Name used by the entity for commercial purposes.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### Address type

The `LegalPersonNameID` type and the `LocalLegalPersonNameID` type share the same format.

```json
{
  "addressType": "GEOG", 
  "department": "xxx...xxx",
  "subDepartment": "xxx...xxx",
  "streetName": "xxx...xxx",
  "buildingNumber": "xxx...xxx",
  "buildingName": "xxx...xxx",
  "floor": "xxx...xxx", 
  "postBox": "xxx...xxx",
  "room": "xxx...xxx", 
  "postcode": "xxx...xxx",
  "townName": "xxx...xxx",
  "townLocationName": "xxx...xxx",
  "districtName": "xxx...xxx", 
  "countrySubDivision": "xxx...xxx",
  "addressLine": ["xxx", "xxx"], 
  "country": "KR" 
}
```

> ⚠️ When using the `Address` type, at least one of the following combinations must be include
>
> 1. at least, more than one `addressLine`
> 2. `streetName` and `buildingName`
> 3. `streetName` and `buildingNumber`

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Type
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        addressType
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        Address Type.
        The possible values for this field are as follows:
        `HOME`: Home address.
        `BIZZ`: Business address.
        `GEO`: Geographic address.
      </td>
    </tr>

    <tr>
      <td>
        department
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Department identifier of a large organization or building.
      </td>
    </tr>

    <tr>
      <td>
        subDepartment
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Sub-department identifier of a large organization or building.
      </td>
    </tr>

    <tr>
      <td>
        streetName
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Street name.
      </td>
    </tr>

    <tr>
      <td>
        buildingNumber
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Building number.
      </td>
    </tr>

    <tr>
      <td>
        buildingName
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Building name.
      </td>
    </tr>

    <tr>
      <td>
        floor
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Floor number within the building.
      </td>
    </tr>

    <tr>
      <td>
        postBox
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        P.O. Box number.
      </td>
    </tr>

    <tr>
      <td>
        room
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Room number within the building.
      </td>
    </tr>

    <tr>
      <td>
        postcode
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Postal code.
      </td>
    </tr>

    <tr>
      <td>
        townName
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Village name.
      </td>
    </tr>

    <tr>
      <td>
        townLocationName
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Specific location name within the village.
      </td>
    </tr>

    <tr>
      <td>
        districtName
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        District name.
      </td>
    </tr>

    <tr>
      <td>
        countrySubDivision
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        State or province name.
      </td>
    </tr>

    <tr>
      <td>
        addressLine
      </td>

      <td>
        Array of Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Free-form address that can uniquely identify a specific location.
        Maximum length of this array is 7.
        If it is difficult to separate the address into components like state, city, or street name, you can use this field to enter the full address as a single string.
      </td>
    </tr>

    <tr>
      <td>
        country
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        Country of Residence.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### DateAndPlaceOfBirth type

```json
// DataAndPlaceOfBirth type Example

{
  "dateOfBirth": "YYYY-MM-DD",
  "placeOfBirth": "Gangnam-gu Seoul"
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        dateOfBirth
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        Date of Birth.

        It must be entered in the format
        `YYYY-MM-DD`.
      </td>
    </tr>

    <tr>
      <td>
        placeOfBirth
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        Place of Birth.

        May include information such as village, city, state, or country.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### NationalIdentification type

```json
// NationalIdentification type Example

{
  "nationalIdentifier": "198723-8971987",
  "nationalIdentifierType": "SOCS",
  "countryOfIssue": "KR",
  "registrationAuthority": "RA0000099"
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        nationalIdentifier
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        National Identification Number of an Individual or Legal Entity.

        If the value of `nationalIdentifierType` is `LEIX`, the nationalIdentifier field must contain a 20-character `LEIX` code.
      </td>
    </tr>

    <tr>
      <td>
        nationalIdentifierType
      </td>

      <td>
        Text
      </td>

      <td>
        Required
      </td>

      <td>
        Type of National Identifier for an Individual or Legal Entity.

        In the case of a legal entity, you must enter one of the following values: `RAID`, `LEIX`, `TXID`, or `MISC`.

        The values that can be entered in this field are as follows:

        1. `ARNU`: Alien Registration Number, a number assigned by the government to identify foreigners.

        2. `CCPT`: Passport number.

        3. `RAID`: Number assigned by a corporate registration authority, such as a business registration number. (This field is used only for legal entities.)

        4. `DRLC`: Driver's license number.

        5. `FIIN`: Foreign investor number, a number assigned to foreign investors.

        6. `TXID`: Number assigned by tax authorities.

        7. `SOCS`: Social security number, such as a resident registration number.

        8. `IDCD`: ID card number assigned by a government authority.

        9. `LEIX`: Global Legal Entity Identifier, LEI code assigned according to the ISO 17442 standard. (This field is used only for legal entities.)

        10. `MISC`: Other types of national identification numbers not defined above.

        May include information such as village, city, state, or country.
      </td>
    </tr>

    <tr>
      <td>
        countryOfIssue
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Country of Issue of National Identifier.

        1. In the case of a legal entity, this field should not be filled.

        2. For individuals, you must enter a two-letter country code as per the ISO-3166-1 alpha-2 standard.
      </td>
    </tr>

    <tr>
      <td>
        registrationAuthority
      </td>

      <td>
        Text
      </td>

      <td>
        Optional
      </td>

      <td>
        Issuing Authority Code for National Identifier.

        1. You must enter the code of the issuing authority as managed by the `Global Legal Entity Identifier Foundation (GLEIF)`. (The code consists of 8 characters: "RA" followed by a 6-digit number.)

        2. If the value of `nationalIdentifierType` is `LEIX`, this field should not be filled.
      </td>
    </tr>
  </tbody>
</Table>

<br />

### IntermediaryVASP type

```json
// IntermediaryVASP type

{
  "intermediaryVASP": {
    ...
  },  // Person type
  "sequence": 0
}
```

<Table align={["left","left","left","left"]}>
  <thead>
    <tr>
      <th>
        Field Name
      </th>

      <th>
        Model
      </th>

      <th>
        Condition
      </th>

      <th>
        Description
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        intermediaryVASP
      </td>

      <td>
        Person
      </td>

      <td>
        Required
      </td>

      <td>
        Information about intermediary VASPs in virtual asset transfers.
      </td>
    </tr>

    <tr>
      <td>
        sequence
      </td>

      <td>
        Number
      </td>

      <td>
        Required
      </td>

      <td>
        A field to determine which number the VASP written in the `intermediaryVASP` field is in the sequence of intermediary VASPs involved in the virtual asset transfer.

        The numbering should start from 0 and increment sequentially up to the last intermediary VASP.
      </td>
    </tr>
  </tbody>
</Table>

<br />

## IVMS101 Message Format Guide

IVMS101 is a messaging protocol for VASPs that defines data formats for transmitting various types of personal information. However, due to the lack of a clear definition of how to input personal information, it is often difficult to accurately interpret the transmitted personal data.

To address this issue, this document provides guidelines on how to input and interpret information when sending and receiving personal information using the IVMS101 messaging protocol.

> 📘 💡 Note:
>
> For the cases presented in this guide, adherence to the guidelines is mandatory.\
> For any cases not covered in this document, please raise a separate issue.

<br />

### General Principles

All verifications must involve information for only one transaction per verification.

<br />

### How to Fill in Individual or Legal Entity Information

* **General Requirements**
  * When filling in individual or legal entity information, **only one originator can be entered.** There cannot be multiple originators for a single verification.
  * When filling in individual or legal entity information, **only one beneficiary can be entered.** There cannot be multiple beneficiaries for a single verification.
* **When There Are Multiple Originators**
  * If there are two or more originators, you must send separate verification requests for each originator.
  * In the case of a legal entity, you'll probably need to fill in both corporate information and representative information, which means multiple entries can exist in `originatorPersons`. However, since all entries ultimately represent the same legal entity, it is still considered a single originator.
* **When There Are Multiple Beneficiaries**
  * If there are two or more beneficiaries, you must send separate verification requests for each beneficiary. Therefore, verifications must be requested for as many beneficiaries as there are.
  * In the case of a legal entity, you'll probably need to fill in both corporate information and representative information, which means multiple entries can exist in `beneficiaryPersons`. However, since all entries ultimately represent the same legal entity, it is still considered a single beneficiary.
* **If the Originator or Beneficiary Is a Legal Person**
  * For legal entities, you'll probably need to fill in both corporate information and representative information. For example, in South Korea, both corporate information and representative information must be included. This applies to both the originator and the beneficiary.
  * Representative information must be filled in as follows:
    * The first element in the `originatorPersons` or `beneficiaryPersons` array must contain information about the legal entity.
    * From the second element onward, the personal information of the legal representative(s) (natural person) must be entered.
    * If there are multiple representatives, you must continue adding their personal information in sequence within the array.

```json
// legal person Example

{
    "originatorPersons": [
        {
            "legalPerson": {
                ...  // legal entity information
            }  // LegalPerson type
        },
        {
            "naturalPerson": {
                ...  // representative information 
            }  // NaturalPerson type
        },
        {
            "naturalPerson": {
                ...  // add representative information if there are multiple representative
            }  // NaturalPerson type
        },
        ...
    ],
    "accountNumber": [
        "xxxxxxx"
    ]
},
```

<br />

### Guidelines for Wallet Address Entry

* **General Requirements**
  * The `accountNumber` is case-sensitive and must be an array of strings.
  * For addresses that have additional components, such as "parent address," "memo," or "destination tag" (e.g., XRP or EOS), the address should be formatted as follows:
    * "ParentAddress:memo" or "ParentAddress:destinationTag"
  * For more information on wallet address formats, refer to the [Wallet Address and Transaction ID Standard Guide]() .
* **`accountNumber`of the Originator**
  * The `accountNumber` for the originator should contain the wallet address where the originator's assets are held.
  * The blockchain account address of the originator must uniquely identify the originator.
  * If the blockchain account address for the originator has not been issued, you must create a deposit address for the originator before withdrawal and enter that address.
  * If the asset does not support a separate deposit address for the originator, enter the unique identifier assigned internally by the VASP to uniquely identify the customer.
* **`accountNumber`of the Beneficiary**
  * The `accountNumber` for the beneficiary should contain the blockchain account address where the beneficiary will receive the virtual assets from the originator.
  * If there are multiple receiving addresses, enter all of them.

<br />

### Guidelines for Name Entry

* **General Requirements**
  * The name of both NaturalPerson and LegalPerson is not an array but a single object. Although the specification may sometimes indicate it as an array or a single object, interpreting it as a single object is more appropriate.
  * The nameIdentifier element of name is an array. If you wish to include multiple names, you can add them to the nameIdentifier array. At least one of the elements in the nameIdentifier array must be a legal name (LEGL type).
* **For Transactions Between VASPs In the Same Country**
  * Names should be entered in local characters.
  * If the original name is in English (e.g., for foreigners or corporations), enter it in English.
* **For Transactions Between VASPs from Different Countries**
  * The name must be entered in English.
  * If there is no English name available in the member information, transliterate the name according to the Transliteration Standard of each language.

<Image align="center" border={false} caption="Transliteration Standards" src="https://files.readme.io/ea28ced6ab3d90e22f0c6153435ccef4e949363a380e73be0c3a867e49206eef-transliterationMethod.png" />

* If the legal English name is provided in the nameIdentifier element, you may enter the local name in the `localNameIdentifier` element.
* **If the Last Name and First Name Can Be Separated**
  * Enter the last name in `primaryIdentifier`.
  * Enter the first name in `secondaryIdentifier`.
* **If the Last Name and First Name Cannot Be Separated**
  * Enter the full name in `primaryIdentifier`.
  * Do not enter anything in `secondaryIdentifier`.

<br />

### Guidelines for Geographic Address Entry

* **When Address Information Is Stored Separately by Elements Such as Province, City, Street, and Building**
  * Enter the province, metropolitan city, or special city in the `countrySubDivision` element.
    * Example: Seoul, Gwangju, Gyeonggi, Jeju.
  * Enter the city, county, or district in the `districtName` element.
  * Enter the town or neighborhood in the `townName` element.
  * Enter the street name in the `streetName` element.
  * Enter the building name or number in the `buildingName` or `buildingNumber` element.
  * Enter the floor or room number in the `floor` or `room` element.
* **When Address Information Is Not Stored Separately by Elements Such as Province, City, etc.**
  * You can enter up to seven address components in the `addressLine` element as an array. If the address is stored in separate parts, include them as elements of the array.
  * If only the full address is available, enter it as the first element of the `addressLine` array.
* **When Entering the Address of a Legal Entity**
  * When entering the address of a legal entity, you must include both the headquarters and the business locations.
    * Enter the headquarters address as the first element in the geographic address array.
    * Enter the business location address as the second element in the geographic address array.
    * If there are multiple business locations, you can add them starting from the third element of the geographic address array.
    * If the headquarters address and the business location address are the same, enter them in both the first and second elements.

<br />

### Guidelines for Date and Place of Birth or Date of Incorporation Entry

* **General Requirements**
  * Fill in the `dateOfBirth` field for natural person
  * Fill in the `dateOfIncorporation` field for legal person
  * The date must be entered in the `dateOfBirth` or `dateOfIncorporation` element using the format `"YYYY-MM-DD"` (ISO 8601 format).
* **For Natural Person**
  * The `placeOfBirth` element should include the place of birth. However, if place of birth information is not available, enter geographic address information (current residence).
  * For customers from countries that do not have a national identification number system, such as a social security number, place of birth information must be provided.

<br />

### Guidelines for Nationality Entry

* **General Requirements**
  * A corporation does not need to input the nationality.
  * IVMS101 has the format for inputting current residence country, but there is no format for inputting nationality
  * To exchange of nationality information, VerifyVASP has modified the IVMS101 format by adding a `nationality` element to the NaturalPerson type. As a result, you can input the country code into the nationality element.

<br />

### IVMS101 Personal Information Code

The Beneficiary VASP must return the Beneficiary’s personal information as specified in the requiredBeneficiaryInfo field in the request.

* If the Beneficiary VASP cannot return the Beneficiary’s personal information for any reason, it must set the result field to DENIED, the reason field to `UNAVAILABLE-INFORMATION`, and the `message` field to a comma-separated string of the personal information codes that could not be returned. These fields must be included in the response.
* If the Originator’s personal information is insufficient to proceed with verification by the Beneficiary VASP, it must set the result field to `DENIED`, the reason field to`LACK-OF-INFORMATION`, and the message field to a comma-separated string listing the personal information codes required for verification. These fields must be included in the response.
* The Originating VASP that receives the error code `LACK-OF-INFORMATION` must call the new User Verification API by adding the missing Originator’s personal information listed in the response’s `message` field.

<Table align={["left","left","left"]}>
  <thead>
    <tr>
      <th>
        Code Name
      </th>

      <th>
        Description
      </th>

      <th>
        Related fields of IVMS101
      </th>
    </tr>
  </thead>

  <tbody>
    <tr>
      <td>
        ACCOUNT\_NUMBER\*
      </td>

      <td>
        Wallet Address (required).
      </td>

      <td>
        * Originator.accountNumber
        * Beneficiary.accountNumber
      </td>
    </tr>

    <tr>
      <td>
        NATURAL\_PERSON\_DATE\_AND\_PLACE\_OF\_BIRTH
      </td>

      <td>
        Date of birth and place of birth.\
        If there is no information about place of birth, please input your home address.
      </td>

      <td>
        * Originator.originatorPersons\[0].naturalPerson.dateAndPlaceOfBirth
        * Beneficiary.beneficiaryPersons\[0].naturalPerson.dataAndPlaceOfBirth
      </td>
    </tr>

    <tr>
      <td>
        NATURAL\_PERSON\_NAME\*
      </td>

      <td>
        Information of personal name.\
        For transactions between VASPs within South Korea, it is acceptable to provide names in Korean.
      </td>

      <td>
        * Originator.originatorPersons\[0].naturalPerson.name.nameIdentifier
        * Beneficiary.beneficiaryPersons\[0].naturalPerson.name.nameIdentifier
      </td>
    </tr>

    <tr>
      <td>
        LOCAL\_NATURAL\_PERSON\_NAME
      </td>

      <td>
        An individual’s name written in the language of the respective country.\
        To use this code, both the localNameIdentifier field and the nameIdentifier field must be filled in.
      </td>

      <td>
        * Originator.originatorPersons\[0].naturalPerson.name.localNameIdentifier
        * Beneficiary.beneficiaryPersons\[0].naturalPerson.name.localNameIdentifier
      </td>
    </tr>

    <tr>
      <td>
        NATURAL\_PERSON\_NATIONALITY
      </td>

      <td>
        Nationality
      </td>

      <td>
        * Originator.originatorPersons\[0].naturalPerson.nationality
        * Beneficiary.beneficiaryPersons\[0].naturalPerson.nationality
      </td>
    </tr>

    <tr>
      <td>
        NATURAL\_PERSON\_GEOGRAPHIC\_ADDRESS
      </td>

      <td>
        A geographic address in English.\
        For transactions between VASPs within South Korea, it is acceptable to provide in Korean.
      </td>

      <td>
        * Originator.originatorPersons\[0].naturalPerson.geographicAddress
        * Beneficiary.beneficiaryPersons\[0].naturalPerson.geographicAddress
      </td>
    </tr>

    <tr>
      <td>
        NATURAL\_PERSON\_NATIONAL\_IDENTIFICATION
      </td>

      <td>
        A national Identification number
      </td>

      <td>
        * Originator.originatorPersons\[0].naturalPerson.nationalIdentification
        * Beneficiary.beneficiaryPersons\[0].naturalPerson.nationalIdentification
      </td>
    </tr>

    <tr>
      <td>
        NATURAL\_PERSON\_CUSTOMER\_IDENTIFICATION
      </td>

      <td>
        A unique customer ID assigned within the VASP.
      </td>

      <td>
        * Originator.originatorPersons\[0].naturalPerson.customerIdentification
        * Beneficiary.beneficiaryPersons\[0].naturalPerson.customerIdentification
      </td>
    </tr>

    <tr>
      <td>
        NATURAL\_PERSON\_COUNTRY\_OF\_RESIDENCE
      </td>

      <td>
        The country of current residence.\
        Input it as a 2-letter country code.
      </td>

      <td>
        * Originator.originatorPersons\[0].naturalPerson.countryOfResidence
        * Beneficiary.beneficiaryPersons\[0].naturalPerson.countryOfResidence
      </td>
    </tr>

    <tr>
      <td>
        LEGAL\_PERSON\_NAME\*
      </td>

      <td>
        The corporate name in English.\
        For transactions between VASPs within South Korea, it is acceptable to provide the name in Korean.
      </td>

      <td>
        * Originator.originatorPersons\[0].legalPerson.name.nameIdentifier
        * Beneficiary.beneficiaryPersons\[0].legalPerson.name.nameIdentifier
      </td>
    </tr>

    <tr>
      <td>
        LOCAL\_LEGAL\_PERSON\_NAME
      </td>

      <td>
        The corporate name in the language of the respective country.\
        To use this code, both the localNameIdentifier field and the nameIdentifier field must be filled in.
      </td>

      <td>
        * Originator.originatorPersons\[0].legalPerson.name.localNameIdentifier
        * Beneficiary.beneficiaryPersons\[0].legalPerson.name.localNameIdentifier
      </td>
    </tr>

    <tr>
      <td>
        CORPORATE\_REPRESENTATIVE\_NAME\*
      </td>

      <td>
        The representative’s name in English.\
        For transactions between VASPs within South Korea, it is acceptable to provide the name in Korean.
        Input the representative’s information in the second element of beneficiaryPersons or originatorPersons.
      </td>

      <td>
        * Originator.originatorPersons\[1].naturalPerson.name.nameIdentifier
        * Beneficiary.beneficiaryPersons\[1].naturalPerson.name.nameIdentifier
      </td>
    </tr>

    <tr>
      <td>
        LOCAL\_CORPORATE\_REPRESENTATIVE\_NAME
      </td>

      <td>
        The representative’s name in the language of the respective country.\
        To use this code, both the localNameIdentifier field and the nameIdentifier field must be filled in.
      </td>

      <td>
        * Originator.originatorPersons\[1].naturalPerson.name.localNameIdentifier
        * Beneficiary.beneficiaryPersons\[1].naturalPerson.name.localNameIdentifier
      </td>
    </tr>

    <tr>
      <td>
        CORPORATE\_REPRESENTATIVE\_DATE\_AND\_PLACE\_OF\_BIRTH
      </td>

      <td>
        The date of birth and place of birth of the corporate representative.\
        If the place of birth information is unavailable, provide the home address instead.
      </td>

      <td>
        * Originator.originatorPersons\[1].naturalPerson.dateAndPlaceOfBirth
        * Beneficiary.beneficiaryPersons\[1].naturalPerson.dateAndPlaceOfBirth
      </td>
    </tr>

    <tr>
      <td>
        CORPORATE\_REPRESENTATIVE\_NATIONALITY
      </td>

      <td>
        The nationality of the corporate representative.\
        Enter it as a two-letter country code.
      </td>

      <td>
        * Originator.originatorPersons\[1].naturalPerson.nationality
        * Beneficiary.beneficiaryPersons\[1].naturalPerson.nationality
      </td>
    </tr>

    <tr>
      <td>
        HEAD\_OFFICE\_GEOGRAPHIC\_ADDRESS
      </td>

      <td>
        The address of the corporate headquarters.\
        Input it as the first element in the geographicAddress array.
      </td>

      <td>
        * Originator.originatorPersons\[0].legalPerson.geographicAddress\[0]
        * Beneficiary.beneficiaryPersons\[0].legalPerson.geographicAddress\[0]
      </td>
    </tr>

    <tr>
      <td>
        BRANCH\_OFFICE\_GEOGRAPHIC\_ADDRESS
      </td>

      <td>
        The address of the corporate business location.\
        Enter it as the second element in the geographicAddress array.
        If you want to include information for multiple business locations, you can add them starting from the third element in the geographicAddress array.
        Even if the headquarters address and business location address are the same, include them separately.
      </td>

      <td>
        * Originator.originatorPersons\[0].legalPerson.geographicAddress\[1]
        * Beneficiary.beneficiaryPersons\[0].legalPerson.geographicAddress\[1]
      </td>
    </tr>

    <tr>
      <td>
        LEGAL\_PERSON\_CUSTOMER\_IDENTIFICATION
      </td>

      <td>
        A unique customer ID assigned internally by the VASP.
      </td>

      <td>
        * Originator.originatorPersons\[0].legalPerson.customerIdentification
        * Beneficiary.beneficiaryPersons\[0].legalPerson.customerIdentification
      </td>
    </tr>

    <tr>
      <td>
        LEGAL\_PERSON\_NATIONAL\_IDENTIFICATION
      </td>

      <td>
        The corporate identification number, such as the business registration number.
      </td>

      <td>
        * Originator.originatorPersons\[0].legalPerson.nationalIdentification
        * Beneficiary.beneficiaryPersons\[0].legalPerson.nationalIdentification
      </td>
    </tr>

    <tr>
      <td>
        LEGAL\_PERSON\_COUNTRY\_OF\_REGISTRATION
      </td>

      <td>
        The country where the corporation is registered.\
        Enter it as a two-letter country code.
      </td>

      <td>
        * Originator.originatorPersons\[0].legalPerson.countryOfRegistration
        * Beneficiary.beneficiaryPersons\[0].legalPerson.countryOfRegistration
      </td>
    </tr>

    <tr>
      <td>
        LEGAL\_PERSON\_DATE\_OF\_INCORPORATION
      </td>

      <td>
        The established date of the corporation.\
        You must input “YYYY-MM-DD” format.
      </td>

      <td>
        * Originator.originatorPersons\[0].legalPerson.dateOfIncorporation
        * Beneficiary.beneficiaryPersons\[0].legalPerson.dateOfIncorporation
      </td>
    </tr>
  </tbody>
</Table>