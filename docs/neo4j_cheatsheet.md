# Neo4j Cypher Level 1 Cheatsheet

## Node Operations

### Creating Nodes

```cypher
CREATE (s:Station {name: "Bastille", lines: [1,5,8]})
```

### Finding Nodes

```cypher
MATCH (s:Station) RETURN s
MATCH (s:Station {name: "Bastille"}) RETURN s
MATCH (s:Station) WHERE s.name = "Bastille" RETURN s
```

### Updating Nodes

```cypher
SET s.zone = 1
SET s += {zone: 1, status: "active"}
REMOVE s.temporary_field
```

### Deleting Nodes

```cypher
DELETE s
DETACH DELETE s  // Deletes node and all relationships
```

## Relationship Operations

### Creating Relationships

```cypher
CREATE (a)-[:CONNECTS_TO]->(b)
CREATE (a)-[:CONNECTS_TO {distance: 500}]->(b)
```

### Finding Connected Nodes

```cypher
MATCH (a)-[:CONNECTS_TO]->(b) RETURN a,b
MATCH (a)-[r:CONNECTS_TO]->(b) RETURN a,b,r
MATCH (a)-[:CONNECTS_TO*1..3]->(b) RETURN a,b  // Path length 1-3
```

## Filtering

### Node Properties

```cypher
WHERE node.property = value
WHERE node.property IN [1,2,3]
WHERE node.property IS NOT NULL
WHERE node.name CONTAINS "Paris"
WHERE node.name STARTS WITH "East"
```

### Multiple Conditions

```cypher
WHERE condition1 AND condition2
WHERE condition1 OR condition2
```

## Return Clauses

### Basic Return

```cypher
RETURN node
RETURN node.property
RETURN DISTINCT node.property
```

### Aggregation

```cypher
RETURN count(*)
RETURN avg(n.value)
RETURN collect(n.property)
```

### Ordering

```cypher
ORDER BY node.property DESC
LIMIT 10
SKIP 10
```

## Pattern Variations

### Optional Matches

```cypher
OPTIONAL MATCH (a)-[:CONNECTS_TO]->(b)
```

### Multiple Patterns

```cypher
MATCH (a:Station), (b:BikeStation)
WHERE a.name = b.name
```

## Common Functions

### String Functions

```cypher
RETURN toLower(s.name)
RETURN substring(s.name, 0, 5)
```

### Collection Functions

```cypher
RETURN size(s.lines)
RETURN length(path)
```

## Tips

- Node labels are CamelCase: `:Station`
- Relationship types are UPPER_SNAKE_CASE: `:CONNECTS_TO`
- Property names are camelCase: `name`, `lineNumber`



