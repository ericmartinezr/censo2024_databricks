# Prompt: Complete Dataset Integration with Code Decoding

Create a comprehensive dataset by joining multiple related tables and decoding all coded fields using reference tables, producing a human-readable final dataset optimized for analysis.

## Objective

Join multiple related datasets and decode all coded fields using reference tables to create a comprehensive, human-readable final dataset optimized for analysis.

## Instructions

### 1. Analyze the Data Structure

- Identify all main datasets (e.g., personas, hogares, viviendas)
- Determine the relationship keys between datasets (e.g., `id_vivienda`, `id_hogar`, `id_persona`)
- Map out the hierarchical structure (e.g., vivienda → hogar → persona)

### 2. Identify ALL Code Reference Tables

- Look for tables with names like `codigos*`, `codes*`, `reference_*`, or `lookup_*`
- Check for both general code tables (e.g., `codigos_otros`) and specific code tables (e.g., `codigos_territoriales_especificos`)
- Verify which fields in the main datasets use which code tables
- **Don't assume you've found all code tables** — explicitly check for additional ones

### 3. Create an Optimized JOIN Query

- Use the main dataset with the most granular level as the base (e.g., personas)
- Use **LEFT JOIN** for related datasets using appropriate keys
- For code tables with multiple categories, use **subqueries with WHERE filters** to reduce JOIN overhead
- For specific code tables, join directly without subqueries if they're small
- Include **both** the original coded field AND the decoded description field

### 4. Naming Conventions

- Keep original field names for coded values (e.g., `sexo`, `region`)
- Add descriptive suffixes for decoded fields (e.g., `sexo_desc`, `region_nombre`)
- Use clear, business-friendly names for decoded fields
- Group related fields with comments for readability

### 5. Optimization Techniques

- Pre-filter code tables in subqueries before joining
- Use **CAST only once** per field in the JOIN condition
- Organize JOINs logically: main tables → territorial codes → general codes → specific codes
- Add section comments to make the query maintainable

### 6. Validation

- Test the query with a **LIMIT clause** first to verify JOINs work correctly
- Check that decoded fields are populated (not all NULL)
- Verify the row count matches the base table
- Sample a few records to ensure descriptions match codes

### 7. Deliverable

- Provide a complete **CREATE TABLE AS SELECT** statement
- Include clear section comments
- Document what each code table decodes
- Provide usage examples and expected output statistics

## Key Requirements

- Include **ALL** code reference tables (don't miss any)
- Decode **ALL** coded fields that have reference tables
- Optimize for performance with large datasets (millions of rows)
- Use clear, consistent naming conventions
- Add comprehensive comments for maintainability
- Test before delivering the final query

## Expected Output

A single, executable SQL query that creates a complete dataset with:

- All original fields from main datasets
- All decoded description fields
- Proper JOINs maintaining data integrity
- Optimized performance for large-scale execution
- Clear documentation and structure

## Query Structure Template

```sql
CREATE TABLE final_dataset AS
SELECT
  -- ============================================
  -- IDENTIFIERS
  -- ============================================
  main.id_field1,
  main.id_field2,

  -- ============================================
  -- TERRITORIAL INFORMATION
  -- ============================================
  main.region,
  reg.Territorio as region_nombre,
  main.provincia,
  prov.Territorio as provincia_nombre,

  -- ============================================
  -- MAIN FIELDS WITH DECODED DESCRIPTIONS
  -- ============================================
  main.coded_field,
  code_table.Descripcion as coded_field_desc,

  -- ... (continue for all fields)

FROM main_table main

-- ============================================
-- JOIN RELATED TABLES
-- ============================================
LEFT JOIN related_table1 r1
  ON main.key = r1.key

-- ============================================
-- TERRITORIAL CODES (OPTIMIZED)
-- ============================================
LEFT JOIN (
  SELECT `Codigo territorial`, Territorio
  FROM territorial_codes
  WHERE `Division Politica Administrativa` = 'Region'
) reg ON main.region = reg.`Codigo territorial`

-- ============================================
-- SPECIFIC TERRITORIAL CODES
-- ============================================
LEFT JOIN specific_territorial_codes spec
  ON main.specific_field = spec.`Codigo especifico`

-- ============================================
-- GENERAL CODE DESCRIPTIONS (OPTIMIZED)
-- ============================================
LEFT JOIN (
  SELECT Codigo, Descripcion
  FROM general_codes
  WHERE Campo = 'field_name'
) code_table
  ON CAST(main.coded_field AS STRING) = code_table.Codigo;
```

## Key Implementation Notes

### 1. Code Table Discovery (Critical)

- **Always check for multiple code tables** — don't stop at the first one you find
- Search for tables matching patterns like `codigos*`, `codes*`, `reference_*`, `lookup_*`
- Verify each coded field in the main datasets has a corresponding reference table
- Include territorial-specific code tables that may be separate from general codes

### 2. JOIN Ordering

- Add JOINs in batches and test each batch incrementally
- Order JOINs from largest to smallest where possible for performance
- Use **LEFT JOIN** to preserve all rows from the base table
- Document any fields that couldn't be decoded

### 3. Performance with Large Datasets

- Pre-filter code tables in subqueries before joining (critical for 10M+ rows)
- Use CAST only once per field in the JOIN condition
- Consider partitioning or clustering if supported by the platform
- Test with a LIMIT clause before running the full query

### 4. Naming Consistency

- **Original coded field:** Keep name as-is (e.g., `sexo`, `region`)
- **Decoded description:** Append `_desc` or `_nombre` suffix (e.g., `sexo_desc`, `region_nombre`)
- Use the same naming pattern throughout the entire query
- Group related fields with section comments for readability

### 5. Data Integrity

- Always use **LEFT JOIN** (not INNER JOIN) to avoid data loss
- Cast numeric codes to strings before joining if needed
- Verify row count after each batch of JOINs
- Sample records to ensure decoded descriptions match the original codes

## Verification Checklist

After generating the final query, verify:

- [ ] All code reference tables have been identified and included
- [ ] All coded fields have corresponding decoded description fields
- [ ] Row count matches the base table (no data loss from JOINs)
- [ ] Decoded fields are populated (not all NULL after JOINs)
- [ ] LEFT JOINs used throughout (no INNER JOIN causing data loss)
- [ ] Naming conventions are consistent (`_desc` / `_nombre` suffixes)
- [ ] Subqueries with WHERE filters are used for general code tables
- [ ] CAST applied appropriately for numeric-to-string code joins
- [ ] Section comments are present for readability
- [ ] Query executes successfully with a LIMIT test

## Tips for Success

1. **Start with a LIMIT query** to validate JOINs before running the full CTAS
2. **Test incrementally** — add JOINs in batches and verify after each batch
3. **Always check for more code tables** — there may be specialized ones you missed
4. **Document assumptions** about any fields that couldn't be decoded
5. **Use consistent naming** throughout to make the output easy to use
6. **Profile the data first** to understand which coded fields exist before writing JOINs

## Best Practices

- Always check for multiple code tables — don't stop at the first one you find
- Test incrementally — add JOINs in batches and test each batch
- Document assumptions — note any fields that couldn't be decoded
- Consider performance — for 10M+ rows, subquery filtering is critical
- Maintain consistency — use the same naming pattern throughout

## Common Pitfalls to Avoid

- Missing specialized code tables (e.g., territorial-specific codes)
- Not casting numeric codes to strings before joining
- Forgetting to use LEFT JOIN (causing data loss)
- Poor naming conventions making the output hard to use
- No comments or structure in the final query

## When to Use

This approach is ideal for:

- Census or survey data integration with multiple related tables
- Joining transactional data with reference/lookup tables
- Creating analytical datasets from normalized databases
- Building data warehouses or data marts
- Any scenario requiring code-to-description decoding at scale
