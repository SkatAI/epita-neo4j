## Centrality Measures

Centrality measures how important or influential a node is within a network, based on its connections and position relative to other nodes.

- Degree Centrality: Number of direct connections. Identifies popular/hub nodes
- Betweenness Centrality: How often a node acts as a bridge between others. Finds bottlenecks/gatekeepers
- Closeness Centrality: Average distance to all other nodes. Shows nodes that can quickly reach others
- PageRank: Node importance based on importance of its connections (like Google's algorithm)




### Degree Centrality

measures the number of direct connections (relationships) a node has

- In-degree: Number of incoming relationships
- Out-degree: Number of outgoing relationships
- Total degree: Sum of both

In Northwind:
- High in-degree Product nodes → Popular products frequently ordered
- High out-degree Employee nodes → Active salespeople with many sales
- High total degree Order nodes → Key transaction hubs

### Betweenness Centrality

It measures, how often a node acts as a **bridge** along the shortest path between other nodes

which identifies what are the Nodes that **control** information/transaction flow

In Northwind:

- High betweenness Orders → Critical transaction points connecting Customers to Products
- High betweenness Employees → Key personnel who bridge different business processes
- Important for: Identifying potential bottlenecks or critical paths in business processes

### Closeness Centrality

- What it measures: Average length of shortest paths between a node and all other nodes
- Identifies: Nodes that can quickly interact with or reach other nodes

In Northwind:
- High closeness Products → Items easily accessible in the supply chain
- High closeness Employees → Staff members well-positioned to coordinate with different parts of the business
- Important for: Understanding efficiency of information or product flow

1. PageRank
- What it measures: Importance of a node based on the quantity and quality of links to it
- Identifies: Nodes that are important not just because of their connections, but because of who they're connected to

In Northwind:
- High PageRank Products → Products that are not just frequently ordered but ordered by important customers
- High PageRank Employees → Staff members connected to high-value transactions or important business relationships
- Important for: Identifying strategically important entities beyond simple connection counts

Business Applications:

1. Supply Chain Optimization:
- Betweenness → Identify potential supply chain bottlenecks
- Closeness → Find optimal warehouse locations or distribution paths

2. Sales Strategy:
- PageRank → Identify most influential products or customers
- Degree → Find most active salespeople or best-selling products

3. Risk Management:
- Betweenness → Identify critical points of failure
- Degree → Find dependencies and vulnerable nodes

4. Customer Relationship Management:
- PageRank → Identify key accounts
- Closeness → Find customers who could be brand advocates


## Formally

## 1. Degree Centrality

### Basic Degree Centrality

$C_D(v) = \frac{deg(v)}{|V| - 1}$

Where:

- $deg(v)$ is the number of edges connected to vertex $v$
- $|V|$ is the total number of vertices in the graph
- Division by $(|V| - 1)$ normalizes the measure to [0,1]

### Directed Graph Variants

In-degree: $C_{D}^{in}(v) = \frac{deg_{in}(v)}{|V| - 1}$

Out-degree: $C_{D}^{out}(v) = \frac{deg_{out}(v)}{|V| - 1}$

## 2. Betweenness Centrality

$C_B(v) = \sum_{s \neq v \neq t} \frac{\sigma_{st}(v)}{\sigma_{st}}$

Where:

- $\sigma_{st}$ is the total number of **shortest paths** from node $s$ to node $t$
- $\sigma_{st}(v)$ is the number of shortest paths from $s$ to $t$ that pass through $v$

Normalized betweenness centrality:

$C'_B(v) = \frac{C_B(v)}{((|V|-1)(|V|-2))/2}$

## 3. Closeness Centrality

$C_C(v) = \frac{|V| - 1}{\sum_{u \neq v} d(v,u)}$

Where:
- $d(v,u)$ is the shortest path distance between nodes $v$ and $u$
- $|V|$ is the total number of nodes in the graph

Alternative Harmonic Closeness:

$C_H(v) = \sum_{u \neq v} \frac{1}{d(v,u)}$

## 4. PageRank

$PR(v) = \frac{1-d}{|V|} + d\sum_{u \in N_{in}(v)} \frac{PR(u)}{|N_{out}(u)|}$

Where:

- $d$ is the damping factor (typically 0.85)
- $N_{in}(v)$ is the set of nodes that link to $v$
- $N_{out}(u)$ is the set of outgoing links from $u$
- $|V|$ is the total number of nodes

Iterative computation until convergence:

$PR_t(v) = \frac{1-d}{|V|} + d\sum_{u \in N_{in}(v)} \frac{PR_{t-1}(u)}{|N_{out}(u)|}$

Key Properties:

1. Values sum to 1: $\sum_{v \in V} PR(v) = 1$
2. Converges to stationary distribution of random walk
3. Reflects both direct and indirect importance

## In cypher

Let's calculate Degree Centrality in Cypher for the Northwind dataset, including both normalized and raw versions.

### 1. Basic Total Degree Centrality

**Basic Total Degree Centrality**:

- Counts total relationships for each node
- Normalizes by (total nodes - 1)
- Shows top 10 most central nodes


```cypher
MATCH (n)
WITH count(n) as totalNodes
MATCH (n)
OPTIONAL MATCH (n)-[r]-()
WITH n, count(r) as degree, totalNodes, labels(n)[0] as nodeType
RETURN
    nodeType,
    n.productName as Product,
    n.companyName as Company,
    n.firstName + ' ' + n.lastName as Employee,
    degree as rawDegree,
    toFloat(degree)/(totalNodes-1) as normalizedDegreeCentrality
ORDER BY normalizedDegreeCentrality DESC
LIMIT 10;
```

### 2. Separate In-Degree and Out-Degree Centrality


**Separate In/Out Degree Centrality**:

- Calculates both incoming and outgoing relationships
- Shows normalized values for both
- Includes node identification information
- Useful for understanding directional importance

```cypher
MATCH (n)
WITH count(n) as totalNodes
MATCH (n)
OPTIONAL MATCH (n)<-[in]-()
WITH n, count(in) as inDegree, totalNodes
OPTIONAL MATCH (n)-[out]->()
WITH n, inDegree, count(out) as outDegree, totalNodes, labels(n)[0] as nodeType
RETURN
    nodeType,
    CASE nodeType
        WHEN 'Product' THEN n.productName
        WHEN 'Customer' THEN n.companyName
        WHEN 'Employee' THEN n.firstName + ' ' + n.lastName
        ELSE toString(id(n))
    END as Name,
    inDegree as rawInDegree,
    outDegree as rawOutDegree,
    toFloat(inDegree)/(totalNodes-1) as normalizedInDegreeCentrality,
    toFloat(outDegree)/(totalNodes-1) as normalizedOutDegreeCentrality,
    toFloat(inDegree + outDegree)/(totalNodes-1) as normalizedTotalDegreeCentrality
ORDER BY normalizedTotalDegreeCentrality DESC
```

### 3. Average Degree Centrality by Node Type

**Average by Node Type**:

- Shows average centrality for each type of node
- Helps understand which types of nodes tend to be more central
- Includes node count for context


```cypher
MATCH (n)
WITH count(n) as totalNodes
MATCH (n)
OPTIONAL MATCH (n)-[r]-()
WITH labels(n)[0] as nodeType, count(r) as degree, totalNodes, count(n) as nodeCount
RETURN
    nodeType,
    avg(toFloat(degree)) as avgRawDegree,
    avg(toFloat(degree)/(totalNodes-1)) as avgNormalizedDegreeCentrality,
    nodeCount
ORDER BY avgNormalizedDegreeCentrality DESC;
```



Expected insights from Northwind:

- Products likely have high in-degree due to being ordered
- Orders connect multiple entity types, showing moderate degrees
- Employees might show balanced in/out degrees due to sales and reporting relationships
- Customers might show primarily out-degree due to making purchases

## Betweenness Centrality

For that we need the GDS library installed
Not available on aura
but available on Neo4J desktop


So let's switch to local

- download Neo4j desktop
- stop the database
- create new database
- get the northwind.dump file from the github repo

