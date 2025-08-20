---
title: Scenarios and Flows
excerpt: >-
  This section introduces the Post-Verification and Pre-Verification scenarios
  for the VerifyName 2.0 protocol, along with the related API flows. Each VASP
  can choose the appropriate scenario based on the counterparty VASP’s Travel
  Rule regulatory obligations.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Post-Verification - From Non-Obliged VASP to Travel Rule Obliged VASP

In a **Post-Verification** scenario, the blockchain transaction is executed first, and the Beneficiary VASP detects the incoming transaction before requesting verification.\
This scenario typically occurs when a non-obliged VASP sends assets without pre-verification.
The Beneficiary VASP can use the VerifyName protocol to confirm whether the originator and beneficiary account owners are the same before crediting the deposit.

<br />

<Image align="center" border={false} caption="Sequence Diagram 1. VerifyName integration flow for unregulated VASP originating withdrawal" src="https://files.readme.io/7e75c4995f2b8b686ba210d9793debccd5c7b8a14dd71545b324fff0d665092b-Post_Verification.svg" />

<HTMLBlock>{`
<style>
  .scenario-section {
    border: 1px dashed #ccc;
    border-radius: 8px;
    padding: 20px;
    margin-bottom: 32px;
    background-color: #fdfdfd;
  }

  .scenario-title {
    font-weight: bold;
    font-size: 16px;
    margin-bottom: 12px;
  }

  .step-list {
    list-style: none;
    padding: 0;
    margin: 0;
  }

  .step-item {
    display: flex;
    align-items: flex-start;
    margin-bottom: 12px;
  }

  .step-badge {
    background-color: #000;
    color: #fff;
    font-weight: bold;
    border-radius: 50%;
    width: 24px;
    height: 24px;
    text-align: center;
    line-height: 24px;
    font-size: 13px;
    margin-right: 5px;
    flex-shrink: 0;
  }

  .step-content {
    flex: 1;
    font-size: 14px;
    line-height: 1.6;
  }

  .subsection-title {
    font-weight: 600;
    font-size: 14px;
    color: #333;
    margin: 24px 0 12px 0;
    padding-left: 4px;
    border-left: 4px solid #007bff;
  }
</style>

<div class="scenario-section">
  <div class="scenario-title">1. Withdrawal Request & Transaction Execution</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">1</div>
      <div class="step-content">The originator requests a withdrawal from the Ordering VASP.</div></li>
    <li class="step-item"><div class="step-badge">2</div>
      <div class="step-content">The non-obliged Ordering VASP executes the withdrawal transaction on the blockchain without performing beneficiary/account verification.</div></li>
  </ol>
</div>
<div class="scenario-section">
  <div class="scenario-title">2. Post-Verification Request by Beneficiary VASP</div>
  <ol>
    <li class="step-item"><div class="step-badge">3</div>
      <div class="step-content">Once the transaction is confirmed, the Beneficiary VASP detects the incoming deposit to the beneficiary address.</div></li>
    <div class="subsection-title">Identify the Ordering VASP</div>
    <li class="step-item"><div class="step-badge">4</div>
      <div class="step-content">The obliged Beneficiary VASP calls the Enclave’s <code>List VASP API</code> to identify the Ordering VASP before applying the deposit.</div></li>
    <li class="step-item"><div class="step-badge">5</div>
      <div class="step-content">The Beneficiary VASP’s Enclave requests the VASP list from the Central Server.</div></li>
    <li class="step-item"><div class="step-badge">6</div>
      <div class="step-content">The Central Server returns the list of VASPs.</div></li>
    <li class="step-item"><div class="step-badge">7</div>
      <div class="step-content">The Enclave forwards the list to the Beneficiary VASP backend.</div></li>
    <li class="step-item"><div class="step-badge">8</div><div class="step-badge">9</div>
      <div class="step-content">The beneficiary (user) selects the Ordering VASP from the list provided by the Beneficiary VASP.</div></li>
    
    <div class="subsection-title">Send Verification Request</div>
    <li class="step-item"><div class="step-badge">10</div>
      <div class="step-content">The Beneficiary VASP calls the <code>Owner Verification API</code> to request that the Ordering VASP confirm whether the originator’s name and date of birth match the beneficiary account owner’s.</div></li>
    <li class="step-item"><div class="step-badge">11</div><div class="step-badge">12</div>
      <div class="step-content">The Beneficiary VASP’s Enclave generates a random salt and, per the VerifyName protocol, creates a hash using the salt, name, and DOB.</div></li>
    <li class="step-item"><div class="step-badge">13</div>
      <div class="step-content">The Enclave encrypts the salt with the Ordering VASP’s public key. If the public key is not cached or is unavailable, the Enclave performs key exchange and caching as in the same way as <a herf="travelrule-scenarios-and-flows">TravelRule protocol.</a></div></li>
    <li class="step-item"><div class="step-badge">14</div>
      <div class="step-badge">15</div><div class="step-content"> The verification request, including the generated hash, is sent via the Central Server to the Ordering VASP’s Enclave.</div></li>
    
    <div class="subsection-title">Transaction and Originator Data Matching</div>
    <li class="step-item"><div class="step-badge">16</div>
      <div class="step-content"> The Ordering VASP’s Enclave calls the backend’s <code>VerifyName API</code> to request transaction verification. The request does not include personal data—only the transaction hash, asset ticker, and network information.</div></li>
    <li class="step-item"><div class="step-badge">17</div>
      <div class="step-content">The Ordering VASP checks its database for a matching transaction record.</div></li>
    <li class="step-item"><div class="step-badge">18</div>
      <div class="step-content">If found, the Ordering VASP identifies the originator and retrieves their name and DOB from the database.</div></li>
    <li class="step-item"><div class="step-badge">19</div>
      <div class="step-content">The Ordering VASP returns the transaction details and originator information to the Enclave.</div></li>
    <li class="step-item"><div class="step-badge">20</div><div class="step-badge">21</div>
      <div class="step-content">The Enclave decrypts the salt using the Ordering VASP’s private key and generates a hash from the salt, name, and DOB.</div></li>
    <li class="step-item"><div class="step-badge">22</div>
      <div class="step-content">The hash is compared with the one received from the Beneficiary VASP..</div></li>
    <li class="step-item"><div class="step-badge">23</div>
      <div class="step-badge">24</div><div class="step-badge">25</div>
      <div class="step-content">The transaction verification result and hash comparison result are returned via the Central Server to the Beneficiary VASP.</div></li>
	</ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">3. Verification Result Report</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">26</div>
      <div class="step-content">The Beneficiary VASP determines the account ownership verification result and whether to credit the deposit.</div></li>
    <li class="step-item"><div class="step-badge">27</div><div class="step-badge">28</div>
      <div class="step-badge">29</div><div class="step-badge">30</div><div class="step-content">The Beneficiary VASP sends the result via the Enclave’s <code>Owner Verification Result Report API</code> to the Ordering VASP. The Ordering VASP’s Enclave calls the backend’s <code>Callback API(OWNER_VERIFICATION_RESULT_REPORT)</code> to deliver the result.</div></li>
    <li class="step-item"><div class="step-badge">31</div>
      <div class="step-badge">32</div><div class="step-content"><strong>[Optional]</strong> Based on the final shared result, both VASPs may finalize deposit/withdrawal status and notify the user.</div></li>
  </ol>
</div>
`}</HTMLBlock>

<br />

<br />

## Pre-Verification - From Travel Rule Obliged VASP to Travel Rule Non-Obliged VASP

In a Pre-Verification scenario, the Ordering VASP is obliged under the Travel Rule and performs ownership verification before executing the blockchain transaction.\
The Ordering VASP can decide whether to proceed with the withdrawal based on the verification result.

<Image align="center" border={false} caption="The Beneficiary VASP can confirm the deposit and notify the user based on the transaction status." src="https://files.readme.io/704a1473f6993c17679876446ed395a7b6985a1ff3cdf303fdd318ecf6be619d-sequence_diagram_2-Pre_Verification.drawio.svg" />

<HTMLBlock>{`
<div class="scenario-section">
  <div class="scenario-title">1. Verify Beneficiary Account Ownership</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">1</div>
      <div class="step-content">The originator requests a digital asset withdrawal from the Ordering VASP.</div></li>
    <li class="step-item"><div class="step-badge">2</div>
      <div class="step-content">The Ordering VASP calls the Enclave’s <code>Owner Verification API</code> to check whether the originator and the beneficiary account owner are the same. The request includes asset details, beneficiary name, DOB, and address.</div></li>
    <li class="step-item"><div class="step-badge">3</div><div class="step-badge">4</div>
      <div class="step-content">The Ordering VASP’s Enclave generates a random salt and creates a hash using the salt, name, and DOB.</div></li>
    <li class="step-item"><div class="step-badge">5</div>
      <div class="step-content">The salt is encrypted with the Beneficiary VASP’s public key.</div></li>
    <li class="step-item"><div class="step-badge">6</div><div class="step-badge">7</div>
      <div class="step-content">The verification request, including the hash and encrypted salt, is sent via the Central Server to the Beneficiary VASP.</div></li>
    <li class="step-item"><div class="step-badge">8</div>
      <div class="step-content">The Beneficiary VASP’s Enclave calls the backend’s <code>VerifyName API</code> to start verification. Only the beneficiary address, asset ticker, and network info (excluding the hash) are passed to the backend.</div></li>
    <li class="step-item"><div class="step-badge">9</div>
      <div class="step-content">The Beneficiary VASP checks whether the address is valid and belongs to its VASP.</div></li>
    <li class="step-item"><div class="step-badge">10</div>
      <div class="step-content">If valid, the Beneficiary VASP identifies the account owner and retrieves their name and DOB from the database.</div></li>
    <li class="step-item"><div class="step-badge">11</div>
      <div class="step-content">The backend returns the address verification result and user details to the Enclave.</div></li>
    <li class="step-item"><div class="step-badge">12</div>
      <div class="step-content">The Enclave decrypts the salt using its private key.</div></li>
    <li class="step-item"><div class="step-badge">13</div>
      <div class="step-content">A hash is generated from the decrypted salt, name, and DOB.</div></li>
    <li class="step-item"><div class="step-badge">14</div>
      <div class="step-content">The hash is compared with the one received from the Ordering VASP.</div></li>
    <li class="step-item"><div class="step-badge">15</div><div class="step-badge">16</div><div class="step-badge">17</div>
      <div class="step-content">The address verification result and hash comparison result are sent via the Central Server to the Ordering VASP.</div></li>
  </ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">2. Verification Result Report</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">18</div>
      <div class="step-content">The Ordering VASP decides whether to proceed with the transaction based on the verification result and reports the result to the Beneficiary VASP.</div></li>
    <li class="step-item"><div class="step-badge">19</div>
      <div class="step-content">The <code>Owner Verification Result Report API</code> is called to share the result.</div></li>
    <li class="step-item"><div class="step-badge">20</div><div class="step-badge">21</div><div class="step-badge">22</div>
      <div class="step-content">The Beneficiary VASP’s Enclave calls the backend’s <code>Callback API (OWNER_VERIFICATION_RESULT_REPORT)</code> to deliver the result.</div></li>
    <li class="step-item"><div class="step-badge">23</div>
      <div class="step-content"><strong>[Optional]</strong> If the verification <code>fails</code>, the Ordering VASP may notify the user that the withdrawal has been canceled and explain the reason.</div></li>
    <li class="step-item"><div class="step-badge">24</div>
      <div class="step-content">If verification <code>succeeds</code>, the Ordering VASP executes the blockchain transaction.</div></li>
  </ol>
</div>

<div class="scenario-section">
  <div class="scenario-title">3. Transaction Execution Result Report</div>
  <ol class="step-list">
    <li class="step-item"><div class="step-badge">25</div>
      <div class="step-content">After execution, a transaction hash is generated.</div></li>
    <li class="step-item"><div class="step-badge">26</div>
      <div class="step-content">The <code>Owner Verification Transaction Report API</code> is called to report the execution result.</div></li>
    <li class="step-item"><div class="step-badge">27</div><div class="step-badge">28</div><div class="step-badge">29</div>
      <div class="step-content">The Beneficiary VASP’s Enclave calls the backend’s <code>Callback API (OWNER_VERIFICATION_TX_REPORT)</code> to deliver the report.</div></li>
    <li class="step-item"><div class="step-badge">30</div>
      <div class="step-content">The Beneficiary VASP finalizes the deposit decision and may notify the user.</div></li>
  </ol>
</div>
`}</HTMLBlock>