---
title: IVMS101 Guide
excerpt: >-
  InterVASP Messaging Standards 101 (hereinafter referred to as IVMS101)
  provides a standardized data model for the Originator and Beneficiary
  information that VASPs must exchange to meet the requirements of the Financial
  Action Task Force (FATF) Travel Rule.
deprecated: false
hidden: true
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

<Table>
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
        Information of the Virtual Asset Transfer Requestor

        Information of the VASP Sending the Virtual Asset

        <br />

        In the current VerifyVASP service structure, the information of the Originating VASP is automatically filled in by the VV Central Server managed by VerifyVASP during the verification request process.

        <br />

        Therefore, this field does not need to be manually entered into the IVMS101 structure when performing User Verification; it will function correctly without it.

        <br />

        Information of the VASP Receiving the Virtual Asset

        <br />

        In the current VerifyVASP service structure, the information of the Beneficiary VASP is automatically filled in by the VV Central Server managed by VerifyVASP during the verification request process.

        <br />

        Therefore, this field does not need to be manually entered into the IVMS101 structure when performing User Verification; it will function correctly without it.

        <br />

        Information of the VASP Receiving the Virtual Asset

        <br />

        In the current VerifyVASP service structure, the information of the Beneficiary VASP is automatically filled in by the VV Central Server managed by VerifyVASP during the verification request process.

        <br />

        Therefore, this field does not need to be manually entered into the IVMS101 structure when performing User Verification; it will function correctly without it.

        <br />

        However, information other than the\\
        BeneficiaryVASP (e.g., information about the Beneficiary) must be entered.
      </td>
    </tr>
  </tbody>
</Table>