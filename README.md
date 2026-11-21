# Notes

## Key Generation

```
cd ~/Keys

openssl genrsa 2048 | openssl pkcs8 -topk8 -v2 aes-256-cbc -inform PEM -out snowflake.p8

openssl rsa -in snowflake.p8 -pubout -out snowflake.pub
```

```
awk '{printf "%s\\n", $0}' ~/Keys/snowflake.p8
```


## Snowflake

https://app.snowflake.com/vhgcfca/rec39553

### Credentials

`airbnb/profiles.yml` holds no secrets -- it reads everything from environment
variables, so it is safe to commit. Secrets live in `.env` at the repo root,
which is gitignored.

```
cp .env.example .env
# then edit .env with your account, key path and passphrase
```

`SNOWFLAKE_PRIVATE_KEY_PATH` must be an absolute path; dbt does not expand `~`.
The key file itself stays outside the repo (e.g. `~/Keys/snowflake.p8`).

dbt does not read `.env` on its own, so let `uv` inject it:

```
cd airbnb
uv run --env-file ../.env dbt debug
```

To avoid typing the flag every time, export it once per shell:

```
export UV_ENV_FILE=../.env   # from airbnb/
uv run dbt run
```


## uv
`curl -LsSf https://astral.sh/uv/install.sh | sh`

## dbt
```
uv sync

source .venv/bin/activate

dbt init --skip-profile-setup airbnb
```

```
cd airbnb
uv run --env-file ../.env dbt debug     # or: export UV_ENV_FILE=../.env

```