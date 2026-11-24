# Punchlist

User-maintained, spans sessions. The user adds items as encountered and may
reorder freely. Order is the user's priority signal — do not re-sort it.
Items are open unless checked. When an item is done, check it and note where
(branch/commit); leave it in place unless the user says to prune.

- [ ] Check whether this fork's parser caps a zero's exponent at the format's
      limits, as `convertFromDecimalCharacter` does (IEEE 754-2019 §5.4.2, §5.2).
      A zero written with an exponent above the maximum should read as the
      maximum, and one below the minimum as the minimum, keeping its sign and
      raising no error:
      - decimal128: `+0E6879` → `0E+6111`, `-0E-9000` → `-0E-6176`;
      - decimal64: the limits are 369 and −398.

      The Cairo adapter (8561332808, `src/internal/bid/vectors_test.go`) does this
      cap in its own vector reader, which encodes operands through the reference
      codec. So the Cairo run does not show what the fork's Parse does.

      Check every text entry point: math/decimal's Parse, strconv, and decimal
      constants in the compiler. The IBM cases dqbas1075–1078 (toSci of ±0
      with an exponent of ±10000, expecting ±0E+6111 or ±0E-6176 and Clamped)
      go through the target's Parse; confirm they run and pass rather than
      being skipped. If the parser already caps, consider
      dropping the reader's own cap.

      The decimal128 ports do this cap in the core parse
      (decimal128-workorder CairoVectorsWorkOrder.md C-5). Added 2026-11-24.
