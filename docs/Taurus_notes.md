# Taurus Project Documentation

## `keygen.go`

### Overview
- **VSS (Verifiable Secret Sharing)**: Only VSS secret sharing is implemented here.

## ROUND 1

- **Paillier Cryptosystem**:
  - Generate prime numbers `p` and `q` such that `p = 2q + 1` using `sample.paillier`.
  - Calculate `N = P * Q`, `N+1`.
  - Determine `P-1`, `Q-1` and `phi = (P-1)(Q-1)`.
  - Compute the inverse `phi^-1`.
  - Calculate squared values `P²`, `Q²`, and `N² = P²Q²`.
  - Define the secret key with components `{p, q, phi, phiInv, PublicKey{N, N², N+1}}`.

- **Pedersen Commitments**:
  - Random `lambda = rand mod phi`.
  - Define `tau = unitModN`.
  - Calculate `t = tau² mod N`.
  - Compute `s = exp(t, lambda, n)`.

- **El Gamal**:

- **VSS Setup**:
  - <span style="color:purple;">Set `Fᵢ(X) = fᵢ(X)•G`.</span>

- **<span style="color:red;">Generate Schnorr randomness</span>**:

- **<span style="color:blue;">Random Identifiers and Commitments</span>**:
  - Generate `ridi` and `ui` using `types.NewRID(rand.Reader)` (type is byte).
  - Commit to data after generating Schnorr randomness.

- **Hashing**:
- commit to data after sch randomness
  - `r.HashForID(RID, ui, VSSPolynomial, SchnorrRand.Commitment(), ElGamalPublic, SelfPedersenPublic.N(), SelfPedersenPublic.S(), SelfPedersenPublic.T())`.

## ROUND 2

### Data Exchange

- Exchange `rid`, `c`, `vss`, `schnorr comm`, `elgamal`, `N`, `S`, `T`, `Decom`.

## ROUND 3

### Verification and Finalization

- **Verification**:
  - Check the length of both `rid` and decommitment.
  - Verify constant coefficient of polynomial.
  - Check degree of polynomial.
  - Validate Paillier's `N` and Pedersen parameters.

- **Decommitment**:
  - Verify decommitments.
  - Store `ridⱼ`, `Cⱼ`, `Nⱼ`, `Sⱼ`, `Tⱼ`, `Fⱼ(X)`, `Aⱼ`.

- **Proofs and Broadcast**:
  - <span style="color:pink;">Compute XOR of `c` and `RID` : c = ⊕ⱼ cⱼ, RID = ⊕ⱼ RIDⱼ </span>
  - <span style="color:red;">Prove `N` is a Blum prime with `zkmod`.</span>
  - <span style="color:blue;">Prove `s`, `t` are correct using `zkprm`.</span>
  - Broadcast these proofs 
  - <span style="color:green;">`zkfac proof`.</span>

- **Encrypted Shares**:
  - <span style="color:purple;">Create P2P messages with encrypted shares using Paillier PK of `J` </span> <span style="color:green;">and `zkfac proof`. </span>
  - Prove that the factors of `N` are relatively large.
  - Compute `fᵢ(j)` and encrypt shares.
  - Write `rid` to the hash state.

## ROUND 4

### Validation and Finalization

- **Verification**:
  - <span style="color:green;">Verify `zkmod`, `zkprm`. </span>
  - <span style="color:yellow;">Validate Paillier ciphertext. </span>
  - <span style="color:green;">Verify `zkfac`. </span>

- **Share Handling**:
  - <span style="color:red;">Decrypt your share.</span>
  - <span style="color:red;">Check for overflow.</span>
  - <span style="color:red;">Verify share with VSS.</span>
  - <span style="color:red;">Save the decrypted share.</span>

- **Key Share Computation**:
  - <span style="color:purple;">Consider any previous ECDSA secret if applicable.</span>
  - <span style="color:purple;">Add all shares.</span>
  - <span style="color:purple;">Compile all Shamir public polynomials into a list `[F₁(X), …, Fₙ(X)]`.</span>
  - <span style="color:purple;">Aggregate them to form Shamir public polynomial `F(X) = ∑Fⱼ(X)`.</span>
  - <span style="color:purple;">Compute the new public key share `Xⱼ = F(j) (+X'ⱼ if doing a refresh)`.</span>

- **Hash and Broadcast**:
  - Write new `ssid` to hash, binding the Schnorr proof to this new configuration.
  - Write `SSID`, `selfID` to temporary hash.
  - <span style="color:blue;">Write Schnorr randomness proof and broadcast it.</span>
  - Update hash state.

## ROUND 5

### Proof Verification

- Verify Schnorr proof.
