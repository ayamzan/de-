# 1. create project structure
***mkdir project_name and cd to project_name***
***mkdir data, src, notebooks***

# 2. initialize python env
***pip install uv***
***uv init***
***uv add --dev pgcli jupyter ipykernel***

# 3. docker-compose.yml 
- Reproducible environments
- Easy collaboration
- Consistent configuration

# start docker compose
***docker compose up -d***

# 4. Verify connection
***uv run pgcli -h localhost -p 5432 -u root -d ny_taxi***
*** In pgcli: \dt to check tables, \q to exit***

# 5. Add development dependencies
***uv add sqlalchemy "psycopg[binary,pool]" pandas***

# 6. Create exploratory notebook
***uv run jupyter notebook***
***Develop ingestion logic in notebook***

# 7. Convert notebook to script (after prototyping)
***uv run jupyter nbconvert --to=script notebooks/exploration.ipynb***

# 8. Refactor ipynb into proper ingestion script

# 9. Create Dockerfile for ingestion script

# 10. build ingestion image
***docker build -t taxi_ingest:v001 .***

# 11. test ingestion with dock compose running
***docker run -it --rm --network=project_name_default(docker network ls) image_name:tag --pg-user=root --pg-pass=root --pg-host=pgdatabase --pg-port=5432 --pg-db=ny_taxi --target-table=yellow_taxi_trips --chunksize=100000***

# 12. run complete pipeline
***docker compose up --build***


### rules
Use .env file for secrets (never commit)
Add .dockerignore and .gitignore
Include README.md with setup instructions
Version your images properly (v001, v002, etc.)
Use healthcheck for service dependencies
Keep notebooks for exploration, scripts for production

### helpful commands
netstat -ano|findstr :5432
Get-Service *postgres*
powershell admin: Stop-Service -Name postgresql-x64-18


### if existing db
***run command to check on indexes, drop if not using***
select
	idx.name as IndexName,
	s.user_scans,
	s.last_user_scan
from sys.indexes idx
join sys.tables tbl
on	 idx.object_id = tbl.parent_object_id
left join sys.dm_db_index_usage_stats s
on s.object_id = idx.object_id
and s.index_id = idx.index_id
order by tbl.name, idx.name