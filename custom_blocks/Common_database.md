---
name: Common_database
---
<Callout icon="💡" theme="default">
  ### When Supporting Both TravelRule and VerifyName Protocols

  Depending on the Enclave mode settings, the VerifyVASP Enclave can support both the TravelRule and VerifyName protocols simultaneously. In such cases, both protocols will store their verification data in a shared set of tables.\
  VASP operators supporting both protocols must select a supported DBMS and run the shared schema creation queries only once to initialize the required tables.
</Callout>