---
"aws-blocks-swift": patch
---

fix(swift): report the swapped pair when `generate-api` arguments are in the wrong order

`generate-api <spec.json> [output-directory] [codegen-config.json]` with the last two swapped
warned that `blocks.codegen.json` was "present but ignored" when it had been passed, just in
the output-directory slot, then failed with `Cannot read <directory>`. It now reports the slot
instead, and neither old line fires. What argument 2 spells is what is matched, so the report
is the same whether the path passed third exists, does not exist, or is absent. One shape
changes exit status: with no configuration file beside the spec and nothing passed third, the
generator used to create a directory named `blocks.codegen.json` and exit 0, and now refuses
and exits 1. An output directory given as `.`, or omitted, is unaffected, as is the documented
argument order.
