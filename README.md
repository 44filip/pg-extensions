# pg_extensions

Modular Ansible playbook that installs and enables PostgreSQL extensions on RHEL hosts.

---

## What it does

1. Probes SSH connectivity and builds a dynamic group of reachable hosts.
2. Detects whether PostgreSQL is installed by checking packages and services.
3. Extracts the PostgreSQL major version from the running service name.
4. Locks repos to an authorized set (RHEL base, AppStream, CRB, EPEL, PGDG).
5. Stops PostgreSQL, installs requested extension packages, then restarts.
6. Discovers target databases (all non-template DBs, or a named list).
7. Runs `CREATE EXTENSION IF NOT EXISTS` for each extension on each database.

Hosts where PostgreSQL is not detected are skipped cleanly via `meta: end_host`.

---

## Requirements

- `community.general` collection
- `python3-psycopg2` (installed automatically by the playbook)
- AWX execution node needs network access to the target hosts and to Satellite (for repo management)

---

## Variables

### vars.yml

| Variable | Description | Default |
|---|---|---|
| `db_exclude_regex` | Regex of DB names to skip in auto mode | `^(template0\|template1\|postgres)$` |
| `repo_custom_prefix` | Prefix for org-managed EPEL and PGDG repos in Satellite | `EXANPLE` |
| `validate_certs` | Validate TLS certs for Satellite API calls | `true` |

### AWX survey vars

| Variable | Description |
|---|---|
| `vmname` | Inventory host or group to target |
| `pg_extensions_enabled` | Third-party extensions to install (e.g. `postgis`, `pgvector`) |
| `native_pg_extensions_enabled` | Native extensions to enable (e.g. `hstore`, `btree_gist`) |
| `db_mode` | Database targeting mode (`auto` or `named`) |
| `database_names` | Comma-separated DB names (only used when `db_mode: named`) |

---

## Supported extensions

### Third-party (`pg_extensions_enabled`)

| Key | Packages installed | SQL name |
|---|---|---|
| `postgis` | `postgis`, `postgis-client`, `postgis-utils` | `postgis` |
| `pgvector` | `pgvector_<version>` | `vector` |

### Native (`native_pg_extensions_enabled`)

| Key | SQL name |
|---|---|
| `hstore` | `hstore` |
| `btree_gist` | `btree_gist` |

To add a new extension, add an entry to `pg_extensions` or `native_pg_extensions` in the playbook vars block.

### AWX group_vars

| Variable | Description |
|---|---|
| `debugmode` | Enable verbose debug output |
