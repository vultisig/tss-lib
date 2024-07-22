# Swing Project Documentation

## `prepare.go`

### Setup

- Generate prime numbers `p` and `q` such that `p = 2q + 1` using `GetRandomSafePrimesConcurrent`.
- Verify that they were generated correctly with a large conditional check.
- Define `N = P * Q`.
- Define `P-1`, `Q-1`, and `phi(N) = (P-1)(Q-1)`.
- Calculate `lambda(N) = phi(N) / gcd(P-1, Q-1)`.
- Calculate Paillier secret key `{N = PQ, lambdaN, PhiN}`.

### Additional Calculations

- Define `Ntildei = PQ`.
- Question: Why is there different notation for `Ntildei`? Why "tilde" and why "i"?   Update : NTildeJ is Paillier blum modulus ! N is paillier modulus 
- What is the difference between a prime and a safe prime?
- Variables `f1`, `alpha`, `beta`, and `h` are undefined as of now.

## ROUND 1

### Operations

- <span style="color:blue;">Generate `ridi` and `ui` using `getRandomPositiveInt` (type is `big.Int`).</span>
- <span style="color:purple;">Compute VSS -> `xi`, `Xi`. </span>
- <span style="color:red;">Compute `Ai`, `tau` for Schnorr ZKP commitment `(Xi, xi)` (see figure 22.1).</span>

### Pre-signing (Fig 6 round1)

- Left shift `P` and `Q` to compute `P2`, `Q2` (multiply by 2).
- Compute `𝜑 = P2 * Q2`.
- Generate proof of ring Pedersen (see fig 17).
- Hash (proof, shares, `N`, `ridi`, `Xi.X()`, `Xi.Y()`, `Ai.X()`, `Ai.Y()`, `preParams.NTildei`, `preParams.H1i`, `preParams.H2i`).
- Send the hash with `newkground1message` and `round.out -> msg`.

## ROUND 2

### Data Exchange

- `(sid, i, ridi, Xi, Ai, ui)` -> fig 5.
- `(ssid, i, Xi, Ai, Yi, Bi, Ni, si, ti, ψˆi, ρi, ui)` -> fig 6.
- `(round.PartyID(), vs, PaillierSK.PublicKey, NTildei, H1i, H2i, ridi, Ai, Xi, 𝜓i)`.

## ROUND 3

### Verification and Computations

- <span style="color:pink;">`RID = ⊕ⱼ RIDⱼ` (XOR across all `ridj` from Round 2).</span>
- Verify Paillier `N` byte size.
- Check `NTildeJ` (Blum modulus) size.
- Verify ZK proof `𝜓j.Verify(H1j, H2j, NTildej)`.
- Recompute and verify hash for all parties.
- <span style="color:red;">Generate Paillier Blum modulus proof (fig 16) with `NtildeI, P, Q` (`zkmod`)..</span>
- <span style="color:blue;">Generate proof of NoSmallFactor (fig 28) and Paillier multiplication (fig 29) (`zkprm`)..</span>
- <span style="color:green;">Generate proof of commitment (fig 22) (`zkfac`)..</span>
- <span style="color:purple;">Encrypt share of `J` using Paillier PK of `J` and broadcast (`Pj, partyId, encrypted share, and the 3 proofs`)..</span>

## ROUND 4

### Final Verification and Reconstruction

- <span style="color:green;">Verify `zkp mod`</span>
- <span style="color:yellow;">verify `zkp sch` (ensure `A^j = Aj`)</span>
- <span style="color:green;">verify `zkp fac`.</span>
- <span style="color:red;">Reconstruct share and verify.</span>
- <span style="color:pink;">Add all shares together..</span>
- <span style="color:pink;">Combine vector commitment `VC`..</span>
- <span style="color:pink;">Compute and verify public key `BigXj` for each participant.</span>
- <span style="color:blue;">Generate Schnorr proof (`zksch`) of the computed public key.</span>

## Round Out

- Final processing and cleanup operations.
