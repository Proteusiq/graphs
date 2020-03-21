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

Docker-compose will log a link and token to Jupyter Lab. You can also get the link and token with `docker container logs jupyter_lab`. To connect to Neo4j, head to  `localhost:7474` and login in Neo4j broswer connection url `bolt://localhost:7687`, default username:`neo4j` and password:`test`.

Hurrah, you made it. Try out the test.ipynb to get the joy of Python and Neo4j.

## Settings
The docker-compose comes with default settings that can be overide. Example, the Jupyter Lab user is `danpra`. If you want to set your own user, edit the docker-compose.yml

Connection url in Jupyter Lab is 'bolt://neo4j:test@neo4j:7687' às neo4j is the name of our service in docker-compose.yml.

_Remember_: We need to rebuild the services with `docker-compose up --build` for any new changes in yml or Dockerfile to take effect.

Note: We are moutning `neomodel` to notebook as the current neomodel does not support Neo4J 4.X. Until fixed, this is a temporary hack-solution.
