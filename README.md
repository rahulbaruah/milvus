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
