# Depth-Padded Merkle Trees for Selective Disclosure

A depth-padded Merkle tree is a cryptographic commitment structure for selective disclosure (SD). It enables a verifier to validate disclosed claims against a single Merkle root without revealing undisclosed claims. The design prioritizes security, simplicity, and computational efficiency.

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

- Root Computation: The issuer computes a Merkle root from the complete, ordered set of leaves. The root serves as a commitment to the dataset.
- Authentication Path Generation: The disclosing party selects the leaves to reveal and generates their authentication paths. These paths provide the additional hash values and positional information needed to verify the disclosed leaves without revealing the remaining leaves.
- Root Reconstruction and Verification: The verifier uses the disclosed leaves and their authentication paths to reconstruct the Merkle root. The disclosed data is accepted as consistent with the original commitment if the reconstructed root matches the trusted root.

The algorithms enable the issuer to commit to a dataset, the disclosing party to reveal selected leaves, and the verifier to check them against the original commitment.

Correct. GitHub-flavored Markdown supports inline math only in environments that enable math rendering; for maximum compatibility, use plain-text notation in the specification.

### Root Computation

**Input:** An ordered sequence of `n >= 1` leaf values.

**Output:** The Merkle root.

1. Compute the tree depth as `d = ceil(log2(n))`, with `d = 0` when `n = 1`.
2. Hash each leaf as `H(LEAF_PREFIX || leaf)`, storing the resulting hashes in a single array.
3. Set the current `level size` to `n`.
4. While the current `level size` is greater than one:
   1. Process the current level in adjacent pairs, from left to right.
   2. For each pair, compute the parent hash as `H(NODE_PREFIX || left || right)`.
   3. If the final node has no right sibling, use the predefined padding hash for the current level as its right sibling.
   4. Store each parent hash in the same array, starting at index zero, `index >>> 1`, and overwriting the entries of the previous level from left to right.
   5. Update the current `level size` to `ceil(current_level_size / 2)`.
5. Return the hash at index zero as the Merkle root.

The algorithm reuses a single array to store successive tree levels, avoiding the need to allocate a separate array for each level. It terminates when the current level contains exactly one hash.

#### Complexity

Let `n` be the number of leaves and `d = ceil(log2(n))` the tree depth.

- **Time Complexity:** `O(n)`. The algorithm hashes each leaf once and computes one parent hash per node pair, including pairs completed using padding. The total number of internal-node hashes is `n - 1`.
- **Space Complexity:** `O(n)`. The algorithm stores leaf hashes in a single array and reuses that array for successive tree levels, avoiding additional arrays proportional to the number of leaves.
- **Tree Depth:** `O(log n)`. The number of levels processed grows logarithmically with the number of leaves.

These bounds assume fixed-size hash outputs and constant-time hash operations.
