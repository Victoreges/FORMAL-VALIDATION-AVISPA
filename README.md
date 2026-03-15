# HLPSL Security Protocol: EV-CS-TA Interaction

## Overview
This project models a security protocol involving three agents: **EV (Electric Vehicle)**, **CS (Charging Station)**, and **TA (Trusted Authority)** using **HLPSL (High-Level Protocol Specification Language)**.  
The protocol ensures secure message exchanges between EV, CS, and TA, maintaining secrecy of session keys, identifiers, and other sensitive data.

The HLPSL code defines:

- Roles for each participant (`role_EV`, `role_CS`, `role_TA`)  
- Session role that composes the participant roles  
- Environment role defining initial knowledge and intruder model  
- Security goals to verify secrecy of multiple keys and identifiers

---

## Project Structure


---

## HLPSL Roles

### EV Role (`role_EV`)
- Represents the Electric Vehicle  
- Handles its internal state machine (State 0 → State 1 → State 2)  
- Uses hash functions `H2, H3, H4` and symmetric key `Sigev`  
- Secrets: `PKev`, `Rev`, `TIDev`, `Sigev`

### CS Role (`role_CS`)
- Represents the Charging Station  
- Handles states 0 → 1 → 2 → 3  
- Uses both `Sigev` and `Sigcs` for secure communication  
- Secrets: `PKcs`, `Rcs`, `TIDcs`, `Sigcs`, plus EV-related secrets

### TA Role (`role_TA`)
- Trusted Authority mediating EV and CS  
- Maintains secrecy of IDs, session keys, and approvals

### Session Role (`session`)
- Composes EV, CS, and TA roles into a single execution instance

### Environment Role (`environment`)
- Defines constants, public keys, symmetric keys, and protocol secrets  
- Configures intruder knowledge (empty by default)  
- Launches the session and defines **secrecy goals**

---

## Security Goals
The protocol verifies secrecy of sensitive elements:

- EV secrets: `sec_1`–`sec_4`  
- CS secrets: `sec_5`–`sec_12`  
- TA secrets: `sec_13`–`sec_19`  
- Session identifiers and approvals: `sec_20`–`sec_35`  

AVISPA will check that no intruder can learn these secrets during protocol execution.

---

## Usage

### 1. Install AVISPA
Download and install AVISPA from [http://www.avispa-project.org/](http://www.avispa-project.org/).

### 2. Convert HLPSL to IF format
```bash
hlpsl2if protocol.hlpsl -o results/protocol.if

avispa results/protocol.if

