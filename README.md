# DSA Interview Prep

This repository is organized for pattern-based DSA revision. Original sheets and combined notes are preserved in backup/archive folders, while interview-ready notes live under `patterns/`.

## Folder structure

```text
DSA-Interview-Prep/
├── sheets/
│   ├── two-pointers-sheet.md
│   ├── sliding-window-sheet.md
│   └── original-notebooks/
├── patterns/
│   ├── two-pointers/
│   ├── sliding-window/
│   └── binary-search/
│       ├── core-philosophy/
│       ├── decision-tree/
│       ├── classic-binary-search/
│       ├── lower-upper-bound/
│       ├── search-in-rotated-array/
│       ├── binary-search-on-answer/
│       ├── peak-valley-search/
│       ├── assets/
│       └── unsorted/
├── backup/
├── archive/
└── README.md
```

## Pattern indexes

- [Two Pointers](patterns/two-pointers/README.md)
- [Sliding Window](patterns/sliding-window/README.md)
- [Binary Search](patterns/binary-search/README.md)

## Sheets

- [Two Pointers Sheet](sheets/two-pointers-sheet.md)
- [Sliding Window Sheet](sheets/sliding-window-sheet.md)

Treat sheets and archived originals as the source of truth for the original order and wording.

## How to use this repo

1. Open the matching index under `patterns/`.
2. Pick one sub-pattern folder.
3. Read its `README.md` first to learn recognition clues and invariants.
4. Revise the corresponding `solutions.md` and re-code the problems without looking.
5. Mark gaps by adding TODOs next to missing or weak solutions.

## Revision strategy

- First pass: solve all MUST DO problems from each pattern.
- Second pass: solve variants that change one condition.
- Third pass: attempt advanced problems and explain the invariant out loud.
- Before interviews: review each sub-pattern README, then re-code one representative problem from memory.

## Notes

- Original combined Two Pointers and Sliding Window solution files are archived in `archive/original-combined-solutions/`.
- Original Binary Search material is backed up in `backup/binary-search-original/` and archived in `archive/binary-search-original/`.
- Some problems or visual references were outside the requested target structure; those are preserved with TODO notes.