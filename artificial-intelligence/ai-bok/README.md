# AI_BoK: AI Body of Knowledge

AI_BoK is the structured knowledge backbone for the SOCC AI curriculum. It enumerates, in a stable hierarchy with permanent IDs, everything the curriculum treats as knowledge worth teaching. Course content references these IDs so that every topic taught traces back to a defined unit of knowledge, and every unit of knowledge can be checked for coverage.

## Why it exists

A curriculum built page by page drifts: topics overlap, gaps open silently, and nothing tells you what is missing. AI_BoK inverts this. The knowledge is mapped first as a reference ontology; the course then traverses it. Coverage becomes a property you can measure rather than hope for.

## Structure

The BoK is a four-level hierarchy:

- **Domain**: the broadest grouping (for example, Core AI).
- **Knowledge Area**: a major field within a domain (for example, Machine Learning).
- **Knowledge Unit (KU)**: a coherent teachable cluster within an area (for example, Introduction to Artificial Intelligence).
- **Topic (T)**: a single teachable lesson, the atomic unit the course authors and maps against.

## ID scheme

IDs are hierarchical and permanent. Once assigned, an ID is never reused or renumbered.

- Domain: `COAI`
- Knowledge Area: `COAI-01`
- Knowledge Unit: `COAI-01-KU01`
- Topic: `COAI-01-KU01-T01`

The four current domains are:

- `COAI` Core AI
- `COMP` Computing
- `MAST` Mathematics & Statistics
- `SSCI` Supporting Sciences

## Files

- [`ai-bok.csv`](ai-bok.csv): the canonical source of record in the repository. Generated from the upstream Google Sheet, never edited by hand. Columns: ID hierarchy (Domain, Knowledge Area, Knowledge Unit, Topic), Difficulty, Prerequisites. Topic cells carry a link to the authored course topic where one exists.
- [`ai-bok.md`](ai-bok.md): a human-readable table view generated alongside the CSV. Never edited by hand.
- `README.md`: this file.

The upstream Google Sheet holds the full data, including the Description and Notes columns, which are kept out of the repository files to keep the table readable. The Sheet is the editing surface; the CSV and Markdown view are generated from it.

## Relationship to the course

The AI Agentic Developer Course is the traversal path through AI_BoK. The course drives; the BoK is filled just-in-time for the topics the course reaches. Each authored course topic carries the BoK Topic ID it fulfills, keeping course and knowledge map in lockstep. Areas and KUs with no Topics listed yet are defined but not yet expanded; they fill in as the course advances.

## Scope and future

AI_BoK currently holds everything the AI curriculum needs, including the Computing, Mathematics, and Supporting Sciences knowledge it depends on. These shared domains are expected to migrate to a broader CS_BoK at a later stage, from which AI_BoK and future siblings (for example, Blockchain and Quantum bodies of knowledge) will derive. Until then, this file is self-contained so that every course phase has a real, referenceable ID today.

## Updating

1. Edit the upstream Google Sheet. All content changes happen there, including Description and Notes.
2. Regenerate [`ai-bok.csv`](ai-bok.csv) and [`ai-bok.md`](ai-bok.md) from the Sheet.
3. Commit both together so the CSV and the readable view never drift from each other or from the Sheet.
