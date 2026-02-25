# RD/CT Cost Sharing Formal Spec (Subset)

This directory contains a formalized, implementation-focused subset of the ENTSO-E RD and CT cost-sharing specifications.

The goal is to make the source specifications directly usable for data modeling, exchange design, and validation.

## Top-down explanation

### 1) Business goal (why)

The formalization targets one business outcome:

- exchange remedial-action cost and activated-volume data between TSOs,
- calculate and validate cost sharing,
- produce and confirm settlement attachments,
- hand off validated settlement data for financial settlement.

This is the process described in the two source PDF documents, captured as a structured machine-readable model.

### 2) System behavior (who does what)

The main actors are:

- `SO` (System Operator, A04)
- `CSC` (Cost Sharing Calculator, A53)
- `SR` (Settlement Responsible, A54)
- `RAC` and `BA` are represented for end-to-end flow context, with out-of-scope parts explicitly marked.

Choreography is represented in `processModel.choreography` in `rdct_cost_sharing_formal_spec.json`.

### 3) Exchange contracts (what is exchanged)

The model formalizes four exchange artifacts:

- `RAS` (costs and activated volumes input)
- `Acknowledgement_MarketDocument`
- `Confirmation_MarketDocument`
- `RASettlement_MarketDocument`

These are described in `artifactSpecifications`.

### 4) Data model (how payloads are structured)

The subset data model captures the classes and relations required by RD/CT settlement exchange, including:

- `RemedialActionSchedule`
- `RemedialAction`
- `PowerScheduleAction` / `RedispatchScheduleAction`
- `PowerTimePoint`
- `RemedialActionCost`
- `RemedialActionScheduleAcceptance`
- `ProposingRemedialActionScheduleShare`

This is defined in `dataModel.entities`, with related enums in `dataModel.enumerations`.

### 5) Validation layer (what must hold)

Formal validation rules are listed in `validationRules` (for example mandatory associations, code lists, and zero-volume/zero-variable-cost behavior).

Two concrete JSON Schemas are provided for direct validator use:

- `schemas/RemedialActionCostInputRecord.schema.json`
- `schemas/RASettlementPoint.schema.json`

The main spec references these via `canonicalSchemas.$ref`.

## Repository structure

- `rdct_cost_sharing_formal_spec.json` - primary formal specification (subset mode)
- `schemas/RemedialActionCostInputRecord.schema.json` - schema for RD/CT cost input records
- `schemas/RASettlementPoint.schema.json` - schema for settlement time-series points
- `source_docs/RD_CT_Cost_Sharing_IG_v1.0.md` - local extracted markdown source
- `source_docs/RemedialActionSchedule_Profile_Specification_v2.2.md` - local extracted markdown source
- `source_docs/pdf/RD_CT_Cost_Sharing_IG_v1.0.pdf` - local source PDF
- `source_docs/pdf/RemedialActionSchedule_Profile_Specification_v2.2.pdf` - local source PDF
- `source_docs/*_png_pages/` - page image extractions used for diagram evidence
- `https://eepublicdownloads.entsoe.eu/clean-documents/CIM_documents/Grid_Model_CIM/RD_CT_Cost_Sharing_IG_v1.0.pdf` - source specification
- `https://eepublicdownloads.entsoe.eu/clean-documents/CIM_documents/Grid_Model_CIM/RemedialActionSchedule_Profile_Specification_v2.2.pdf` - source specification

Supporting diagram evidence used during formalization is listed in `meta.diagramEvidence`.

## Reading order

For a top-down walkthrough, read in this order:

1. `rdct_cost_sharing_formal_spec.json` -> `meta`, `normativeContext`
2. `rdct_cost_sharing_formal_spec.json` -> `roleModel`, `processModel`
3. `rdct_cost_sharing_formal_spec.json` -> `artifactSpecifications`
4. `rdct_cost_sharing_formal_spec.json` -> `dataModel`, `validationRules`
5. `schemas/*.schema.json` for executable payload constraints

## Quick usage

Inspect sections with `jq`:

```bash
jq '.processModel.choreography' rdct_cost_sharing_formal_spec.json
jq '.artifactSpecifications.RASettlement_MarketDocument' rdct_cost_sharing_formal_spec.json
```

Validate schema JSON syntax:

```bash
jq -e . rdct_cost_sharing_formal_spec.json >/dev/null
jq -e . schemas/RemedialActionCostInputRecord.schema.json >/dev/null
jq -e . schemas/RASettlementPoint.schema.json >/dev/null
```

If `ajv` is available, validate an instance:

```bash
ajv validate -s schemas/RemedialActionCostInputRecord.schema.json -d path/to/input.json
```

## Scope boundary

This work is intentionally a subset (`meta.coverageMode = subset-rdct-cost-sharing`).

It does not attempt a full class-by-class formalization of all RAS v2.2 classes outside the RD/CT cost-sharing exchange needs.

Known ambiguities from source extraction are tracked in `openIssuesForClarification`.
