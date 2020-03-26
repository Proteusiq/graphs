# Neo4j & Python
> Working with Graphs using Neo4j and Python

![neo4j python](images/show.png)

## How to Run
Everything is ready to run with a single command after repository cloning. Thanks to docker-compose, we can spin up Jupyter Lab and Neo4j services in no time.

```bash
git clone https://github.com/Proteusiq/graphs.git
cd graphs
docker-compose up
```

To shutdown, `docker-compose down`. We can add `-v` at the end of the command to remove also volumes, the datasets.

Docker-compose will log a link and token to Jupyter Lab. You can also get the link and token with `docker container logs jupyter_lab`. To connect to Neo4j, head to  `localhost:7474` and login in Neo4j browser. The connection url is `bolt://localhost:7687`, default username:`neo4j` and password:`test`.

Hurrah, you made it. Try out the test.ipynb to get the joy of Python and Neo4j.

## Settings
The docker-compose comes with default settings that can be overide. Example, the Jupyter Lab user is `danpra`. If you want to set your own user, edit the docker-compose.yml

Connection url in Jupyter Lab is `bolt://neo4j:test@neo4j:7687` as neo4j is the name of our service in docker-compose.yml.

## Simple Family Graph: Test of Cypher & Neo4j
Cypher is the Python of query languages. It is beautiful, intuitive and simple. Here an example of created dataset and start asking it, yes your dataset, questions.

```cypher

// Creating the graph: N.B: Enable multi statement query editor on your settings
//Create Person and Location
MERGE (p:Person {name:"Prayson",age:34, description:"The Matrix's Neo"});
MERGE (:Person {name:"Lea", age:32, description:"The awesome"});
MERGE (p:Person {name:"Eloise",age:8, description:"The dancer"});
MERGE (p:Person {name:"Nora",age:5, description:"The wise"});
MERGE (p:Person {name:"Mario",age:3, description:"The Jumper"});
MERGE (:Location {address:"Gurrevej 48"});


//Add p person relationship to location l
MATCH (p:Person),(l:Location {address:"Gurrevej 48"})
WHERE p.name in ['Prayson','Mario', 'Nora','Eloise', 'Lea']
MERGE (p)-[:LIVES_IN]->(l);

// Add marriage
MATCH (p:Person {name:"Lea"}),(o:Person {name:"Prayson"})
MERGE (p)<-[:MARRIED_TO]-(o)<-[:MARRIED_TO]-(p);

// Add childrens
MATCH (p:Person), (o:Person)
WHERE p.name = "Prayson" and o.name IN ["Eloise","Nora","Mario"]
MERGE (p)<-[:CHILD_OF]-(o);

MATCH (p:Person), (o:Person)
WHERE p.name ="Lea" and o.name IN ["Eloise","Nora","Mario"]
MERGE (p)<-[:CHILD_OF]-(o);
```

Show graph:
```cypher
MATCH (n) RETURN n LIMIT 6
```
![neo4j python](images/family.png)


Who is Prayson?:
<details><summary>Query</summary>
<p>

#### query for 'Who is Prayson?'

```cypher
MATCH (p:Person)
WHERE p.name = "Prayson"
RETURN p.description AS `Who is Prayson?`

// OR
MATCH (p:Person {name: "Prayson"})
RETURN p.description AS `Who is Prayson?`
```
</p>
</details>

![neo4j python](images/who.png)


What are the name and age of people leaving in Gurrevej 48 under 18 years old?:
<details><summary>Query</summary>
<p>

#### query for 'people leaving in Gurrevej 48 under 18 years old?'

```cypher
MATCH (under_age:Person),(location:Location)
WHERE under_age.age < 18 and location.address="Gurrevej 48"
RETURN under_age.name AS `Children`, under_age.age AS `Age`
```
</p>
</details>


![neo4j python](images/children.png)


Who lives in Gurrevej 48?:
<details><summary>Query</summary>
<p>

#### show, 'Who lives in Gurrevej 48?'

```cypher
MATCH (p:Person)-[:LIVES_IN]->(l:Location)
WHERE l.address = "Gurrevej 48"
RETURN collect(p.name) AS `Who lives in Gurrevej 48?`
```

</p>
</details>


![neo4j python](images/all_lives.png)


How many children does Prayson have?:
<details><summary>Query</summary>
<p>

#### show, 'How many children does Prayson have?'

```cypher
MATCH (child:Person)-[:CHILD_OF]->(parent:Person)
WHERE parent.name = "Prayson"
RETURN count(child) AS `How many children does Prayson have?`
```

</p>
</details>

![neo4j python](images/child_cnt.png)


## Power of Cypher over SQL: GraphDB > RelationalDB

So far, we could have performed the queries above with SQL in tabular data. Let's gear up. We know that in real world, relationship are more richer and wider. Let's introduce more relationships.

<details><summary>Getting Large</summary>
<p>

#### show, 'How many children does Prayson have?'
```cypher
//Add more members: My dad, brothers, nephew, and niece
MERGE (:Person {name:"Wilfred", age:59, description:"Babu"});
MERGE (:Person {name:"Eric", age:35, description: "First blood"});
MERGE (:Person {name:"Jimmy", age:29, description: "Quantum MD"});
MERGE (:Person {name:"Trace", age:10});
MERGE (:Person {name:"Trisher", age:3);

//Add relationship
MATCH (p:Person), (o:Person)
WHERE p.name ="Wilfred" and o.name IN ["Eric","Prayson","Jimmy"]
MERGE (p)<-[:CHILD_OF]-(o)

MATCH (p:Person), (o:Person)
WHERE p.name ="Eric" and o.name IN ["Trace","Trisher"]
MERGE (p)<-[:CHILD_OF]-(o)

//Did not include my mother to keep things simple
```
</p>
</details>

![extend relation](images/bigger_family.png)

### Cypher > SQL
The power of Cypher comes in query relationships. Imagine we wanted to know the names of `Wilfred` grandchildren. In SQL, you will have to perform a number of table joins to achieve these. The farther the link, the more joins you will have. With Cypher, two lines of code is all you need.

<details><summary>Two lines of Code</summary>
<p>

```
MATCH (grand_child:Person)-[:CHILD_OF*2]->(babu:Person {name: "Wilfred"})
RETURN collect(grand_child.name) AS `Wilfred's grand children are:`
```
</p>
</details>

![extend relation](images/grand_childs.png)

There is more. We can ask: What is the shortest `child-of path` relationship between `Mario` and `Jimmy`?

<details><summary>Query</summary>
<p>

```
MATCH (mario:Person { name: 'Mario' }),(jimmy:Person { name: 'Jimmy' }),
      path = shortestPath((mario)-[:CHILD_OF*]-(jimmy))
RETURN path
```
</p>
</details>

![extend relation](images/mario_jimmy.png)

_Remember_: You need to rebuild the services with `docker-compose up --build` for any new changes in requirements.txt, yml or Dockerfile to take effect.

Note: We are moutning `neomodel` to notebook as the current neomodel does not support Neo4J 4.X. Until fixed, this is a temporary hack-solution.
