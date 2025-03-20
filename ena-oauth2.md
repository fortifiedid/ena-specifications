![Logo](images/ena-logo.png)

# Ena OAuth 2.0 Interoperability Profile

### Version: 1.0 - draft 01 - 2025-03-20

## Abstract

> TODO

## Table of Contents

1. [**Introduction**](#introduction)

    1.1. [Requirements Notation and Conventions](#requirements-notation-and-conventions)
    
    1.2. [Terminology](#terminology)

    1.3. [Conformance](#conformance)

    1.4. [Limitations and Exclusions](#limitations-and-exclusions)

2. [**Client Profile**](#client_profile)

    2.1. [Types of OAuth Clients](#types-of-oauth-clients)

3. [**Authorization Server Profile**](#authorization-server-profile)

4. [**Resource Server Profile**](#resource-server-profile)

5. [**Grant Types**](#grant-types)

    5.1. [Authorization Code Grant](#authorization-code-grant)

    5.1.1. [Authorization Requests](#authorization-requests)

    5.1.2. [Authorization Responses](#authorization-responses)

    5.1.3. [Token Endpoint](#acg-token-endpoint)

    5.2. [Refresh Token Grant](#refresh-token-grant)

    5.2.1. [Token Endpoint](#rtg-token-endpoint)

    5.2.2. [Refresh Token Requirements and Recommendations](#refresh-token-requirements-and-recommendations)

    5.3. [Client Credentials Grant](#client-credentials-grant)

    5.4. [Other Grant Types](#other-grant-types)

6. [**Security Requirements and Considerations**](#security-requirements-and-considerations)

7. [**References**](#references)

    7.1. [Normative References](#normative-references)

    7.2. [Informational References](#informational-references)

---

<a name="introduction"></a>
## 1. Introduction

<a name="requirements-notation-and-conventions"></a>
### 1.1. Requirements Notation and Conventions

The keywords “MUST”, “MUST NOT”, “REQUIRED”, “SHALL”, “SHALL NOT”, “SHOULD”, “SHOULD NOT”, “RECOMMENDED”, “MAY”, and “OPTIONAL” are to be interpreted as described in \[[RFC2119](#rfc2119)\].

These keywords are capitalized when used to unambiguously specify requirements over protocol features and behavior that affect the interoperability and security of implementations. When these words are not capitalized, they are meant in their natural-language sense.

<a name="terminology"></a>
### 1.2. Terminology

<a name="conformance"></a>
### 1.3. Conformance

- OAuth2 Components (Resource Owner, Client, Authorization Server, Resource Server)

<a name="limitations-and-exclusions"></a>
### 1.4. Limitations and Exclusions

> - Features or use cases explicitly **not** covered in this profile  
- Assumptions about underlying infrastructure
- OAuth2 flows that are not recommended (e.g., Implicit Grant, Password Grant)  
- Scenarios requiring additional security mechanisms beyond OAuth2  
- Out-of-scope topics (e.g., end-user identity verification, federated identity governance)  

<a name="client_profile"></a>
## 2. Client Profile

<a name="types-of-oauth-clients"></a>
### 2.1. Types of OAuth Clients

<a name="authorization-server-profile"></a>
## 3. Authorization Server Profile

<a name="resource-server-profile"></a>
## 4. Resource Server Profile

<a name="grant-types"></a>
## 5. Grant Types

<a name="authorization-code-grant"></a>
### 5.1. Authorization Code Grant

<a name="authorization-requests"></a>
#### 5.1.1. Authorization Requests

<a name="authorization-responses"</a>
#### 5.1.2. Authorization Responses

<a name="acg-token-endpoint"></a>
#### 5.1.3. Token Endpoint

<a name="refresh-token-grant"></a>
### 5.2. Refresh Token Grant

<a name="rtg-token-endpoint"></a>
#### 5.2.1. Token Endpoint

<a name="refresh-token-requirements-and-recommendations"></a>
#### 5.2.2. Refresh Token Requirements and Recommendations

<a name="client-credentials-grant"></a>
### 5.3. Client Credentials Grant

<a name="other-grant-types"></a>
### 5.4. Other Grant Types

<a name="security-requirements-and-considerations"></a>
## 6. Security Requirements and Considerations

- client authentication
- PKCE
- sender constrained tokens
- ...

<a name="references"></a>
## 7. References

<a name="normative-references"></a>
### 7.1. Normative References

<a name="rfc2119"></a>
**\[RFC2119\]**
> [Bradner, S., Key words for use in RFCs to Indicate Requirement Levels, March 1997](https://www.ietf.org/rfc/rfc2119.txt).

<a name="informational-references"></a>
### 7.2. Informational References
