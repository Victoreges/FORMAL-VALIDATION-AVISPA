# HLPSL Model for a V2G Authentication and Key Agreement Protocol

This repository contains an HLPSL specification of a lightweight authentication and key agreement protocol for Vehicle-to-Grid (V2G) networks. The protocol models the interaction among an Electric Vehicle (EV), a Charging Station (CS), and a Trusted Authority (TA), aiming to verify secrecy and mutual authentication properties using the AVISPA tool.

## Overview

The protocol is specified in HLPSL and follows the Dolev-Yao adversarial model, in which the intruder can intercept, modify, replay, and inject messages over public communication channels. Long-term shared secrets and sensitive parameters are kept outside the intruder's initial knowledge.

The modeled entities are:

- **EV**: Electric Vehicle requesting authentication and session establishment.
- **CS**: Charging Station participating in authentication and session-key agreement.
- **TA**: Trusted Authority responsible for validating authentication data and distributing protected parameters.

## Protocol Structure

The HLPSL specification is organized into the following roles:

- `role_EV`: Models the behavior of the Electric Vehicle.
- `role_CS`: Models the behavior of the Charging Station.
- `role_TA`: Models the behavior of the Trusted Authority.
- `session`: Instantiates and composes the EV, CS, and TA roles.
- `environment`: Defines constants, public parameters, intruder knowledge, and the session instance.

## Main Cryptographic Elements

The model uses the following abstract cryptographic primitives:

- `H1`, `H2`, `H3`, `H4`: Hash functions used for authentication tokens, validation values, and session-key confirmation.
- `DH`: Abstract Diffie-Hellman operation used to represent elliptic-curve-based key agreement.
- `SigEV`: Long-term symmetric secret shared between EV and TA.
- `SigCS`: Long-term symmetric secret shared between CS and TA.
- `PKev`, `PKcs`, `PKta`: Public keys of EV, CS, and TA.
- `TIDev`, `TIDcs`: Temporary identities used to preserve privacy.
- `Rev`, `Rcs`, `Rta`: Random values used during protocol execution.
- `ATIDevcs` and `Ac`: Protected parameters generated or distributed by the TA.

## Communication Flow

The modeled protocol can be summarized as follows:

1. **EV to TA authentication token**  
   The EV sends an authentication token based on its shared secret, temporary identity, challenge, nonce, and timestamp.

2. **CS to TA authentication token**  
   The CS sends a similar authentication token based on its own shared secret and authentication parameters.

3. **EV and CS encrypted authentication material**  
   The EV and CS send protected authentication data encrypted under their respective long-term symmetric secrets shared with the TA.

4. **TA validation and parameter distribution**  
   The TA validates the authentication material and sends protected parameters to the CS, including information that allows EV-CS session establishment.

5. **EV-CS session-key derivation**  
   The EV and CS derive a shared session key based on the Diffie-Hellman result and the protected values received through the TA.

6. **Session-key confirmation**  
   EV and CS exchange hash-based confirmation messages to verify that both parties derived the same session key.

## Security Goals

The specification verifies the following secrecy properties:

```hlpsl
secrecy_of sec_ev_ta
secrecy_of sec_cs_ta
secrecy_of sec_9
secrecy_of sec_10
secrecy_of sec_11
secrecy_of sec_12
secrecy_of sec_sk
```

These goals ensure the confidentiality of:

- EV-TA long-term secret material.
- CS-TA long-term secret material.
- TA identity-related protected values.
- TA random values.
- Temporary EV-CS authentication parameters.
- Auxiliary authentication values.
- The final EV-CS session key.

The specification also verifies the following authentication properties:

```hlpsl
authentication_on auth_ev_ta
authentication_on auth_cs_ta
authentication_on auth_ev_cs
authentication_on auth_cs_ev
```

These authentication goals represent:

- Authentication of EV by TA.
- Authentication of CS by TA.
- Authentication of EV by CS.
- Authentication of CS by EV.

## Intruder Knowledge

The intruder is initialized with only public parameters:

```hlpsl
intruder_knowledge = {ev,cs,ta,h1,h2,h3,h4,dh,p,pkev,pkcs,pkta}
```

Therefore, the intruder knows the public agents, public hash functions, the public Diffie-Hellman abstraction, the public base point, and public keys. Long-term symmetric secrets, temporary identities, random values, and session-key material are not initially disclosed.

## Running the Model in AVISPA

To analyze the specification, save the HLPSL code as a file, for example:

```bash
protocol.hlpsl
```

Then execute the AVISPA tool using one of the available backends, such as OFMC or CL-AtSe:

```bash
avispa protocol.hlpsl --ofmc
```

or:

```bash
avispa protocol.hlpsl --cl-atse
```

Expected successful verification output:

```text
SUMMARY
SAFE
```

A `SAFE` result indicates that no attack was found for the modeled secrecy and authentication goals under the selected backend and bounded verification assumptions.

## Notes

This HLPSL model abstracts cryptographic operations as symbolic functions. Therefore, the verification focuses on logical protocol flaws, such as replay, impersonation, message manipulation, and secrecy violations, rather than implementation-level vulnerabilities or computational cryptanalysis.

## License

This project is intended for academic and research purposes. Add a license file if the code will be publicly released or reused by other researchers.
