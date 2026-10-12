# Depth-Padded Merkle Trees for Selective Disclosure

A depth-padded Merkle tree is a cryptographic commitment structure for selective disclosure (SD). It enables a verifier to validate the integrity and membership of selected data elements against a single Merkle root without requiring disclosure of unselected elements. The design prioritizes security, structural simplicity, and computational efficiency through deterministic tree construction, domain-separated hashing, and compact authentication proofs.

## Core Design Objectives

- Secure Verification: Uses distinct domain-separation prefixes for leaf data, internal nodes, and padding values to distinguish hash inputs by type. Verification recomputes the commitment according to deterministic hashing rules and compares the resulting root with the trusted commitment.
- Structural Simplicity: Uses deterministic, depth-dependent padding to handle incomplete node pairs according to a fixed construction rule, avoiding ad hoc tree restructuring.
- Computational Efficiency: Supports bottom-up root computation and authentication path generation with limited intermediate storage. For multiple disclosed elements, shared authentication nodes can be reused to reduce proof size and verification overhead.
- Depth Determination: Derives the tree depth from the number of leaves, producing the minimum depth required to reach a single root under the prescribed binary construction. A configured maximum depth bounds the supported number of leaves and the maximum authentication path length.
- 
## Cryptographic Properties

- Deterministic Construction: Defines consistent leaf hashing, internal-node hashing, and padding rules, ensuring reproducible commitments for the same ordered input.
- Depth-Dependent Padding: Uses predefined padding hashes associated with tree levels to handle non-power-of-two leaf counts consistently.
- Efficient Membership Proofs: Represents each disclosed element with the sibling hashes and positional information required to reconstruct and verify the Merkle root. Proofs for multiple elements can share common authentication nodes.

These properties provide a deterministic and efficient basis for selective disclosure, enabling verification of disclosed data while keeping undisclosed leaf contents private, subject to the security of the underlying hash function and the confidentiality of the disclosed data itself.

## Algorithmic Scope

The selective disclosure process involves three core algorithms, used by the issuer, the disclosing party, and the verifier:

1. Root Computation: The issuer computes a Merkle root from the complete, ordered set of leaves. The root serves as a commitment to the dataset.
2. Authentication Path Generation: The disclosing party selects the leaves to reveal and generates their authentication paths. These paths provide the additional hash values and positional information needed to verify the disclosed leaves without revealing the remaining leaves.
3. Root Reconstruction and Verification: The verifier uses the disclosed leaves and their authentication paths to reconstruct the Merkle root. The disclosed data is accepted as consistent with the original commitment if the reconstructed root matches the trusted root.

The algorithms enable the issuer to commit to a dataset, the disclosing party to reveal selected leaves, and the verifier to check them against the original commitment.
