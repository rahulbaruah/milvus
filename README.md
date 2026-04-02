# README

1. Copy Milvus.yaml

    `cp milvus.yaml.example milvus.yaml`

2. Start Milvus

    `docker compose up -d`

3. Milvus WebUI

    `http://127.0.0.1:9091/webui/`

4. Milvus Attu UI

   `http://127.0.0.1:8010`

5. Enable Milvus Authentication

   - To enable user authentication, you need to set `common.security.authorizationEnabled` as `true` in `milvus.yaml`.

---

## Milvus backup

`backup.yaml` — two things you must verify before starting:

bucketName — check your `milvus.yaml` for the actual value. Common defaults are a-bucket (Docker Compose installs) or milvus-bucket. Change it to match exactly.
rootPath — similarly check `milvus.yaml`. Default is usually files.

To find them quickly on the server:

```bash
grep -E "bucketName|rootPath" milvus.yaml
```

To bring up the new service:

```bash
docker compose up -d milvus-backup

# Verify it's healthy
curl http://localhost:8080/api/v1/check
```

These are simple one-liners you run on the server — no need for a file.

Backup all collections:

`docker exec milvus-backup /milvus-backup create -n my_backup`

Backup specific collections:

`docker exec milvus-backup /milvus-backup create -n my_backup -c collection_1,collection_2`

List all backups:
`docker exec milvus-backup /milvus-backup list`

Restore with a suffix (safe — creates new collections alongside existing ones):

```bash
docker exec milvus-backup /milvus-backup restore -n my_backup -s _recovered
# result: collection_1_recovered, collection_2_recovered
```

Restore to original name (overwrites — drop the collection first via Python client):

`docker exec milvus-backup /milvus-backup restore -n my_backup`

Delete a backup:

`docker exec milvus-backup /milvus-backup delete -n my_backup`

Or via the REST API (port 8080)

If you prefer curl from anywhere:

```bash
# Create backup
curl -X POST http://localhost:8080/api/v1/create \
  -H 'Content-Type: application/json' \
  -d '{"backup_name": "my_backup"}'

# List backups
curl http://localhost:8080/api/v1/list

# Restore
curl -X POST http://localhost:8080/api/v1/restore \
  -H 'Content-Type: application/json' \
  -d '{"backup_name": "my_backup", "collection_suffix": "_recovered", "async": true}'

# Check restore status (use id from restore response)
curl "http://localhost:8080/api/v1/get_restore?id=<restore_id>"
```

Restart milvus backup

`docker compose restart milvus-backup`
