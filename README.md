[![MseeP.ai Security Assessment Badge](https://mseep.net/pr/theralabs-legion-mcp-badge.png)](https://mseep.ai/app/theralabs-legion-mcp)

# Multi-Database MCP Server (by Legion AI)

A server that helps people access and query data in databases using the Legion Query Runner with integration of the Model Context Protocol (MCP) Python SDK.

# Start Generation Here
This tool is provided by [Legion AI](https://thelegionai.com/). To use the full-fledged and fully powered AI data analytics tool, please visit the site. Email us if there is one database you want us to support.
# End Generation Here

## Why Choose Database MCP

Database MCP stands out from other database access solutions for several compelling reasons:

- **Unified Multi-Database Interface**: Connect to PostgreSQL, MySQL, SQL Server, and other databases through a single consistent API - no need to learn different client libraries for each database type.
- **AI-Ready Integration**: Built specifically for AI assistant interactions through the Model Context Protocol (MCP), enabling natural language database operations.
- **Zero-Configuration Schema Discovery**: Automatically discovers and exposes database schemas without manual configuration or mapping.
- **Database-Agnostic Tools**: Find tables, explore schemas, and execute queries with the same set of tools regardless of the underlying database technology.
- **Secure Credential Management**: Handles database authentication details securely, separating credentials from application code.
- **Simple Deployment**: Works with modern AI development environments like LangChain, FastAPI, and others with minimal setup.
- **Extensible Design**: Easily add custom tools and prompts to enhance functionality for specific use cases.

Whether you're building AI agents that need database access or simply want a unified interface to multiple databases, Database MCP provides a streamlined solution that dramatically reduces development time and complexity.

## Features

- Multi-database support - connect to multiple databases simultaneously
- Database access via Legion Query Runner
- Model Context Protocol (MCP) support for AI assistants
- Expose database operations as MCP resources, tools, and prompts
- Multiple deployment options (standalone MCP server, FastAPI integration)
- Query execution and result handling
- Flexible configuration via environment variables, command-line arguments, or MCP settings JSON
- User-driven database selection for multi-database setups

## Supported Databases

| Database | DB_TYPE code |
|----------|--------------|
| PostgreSQL | pg |
| Redshift | redshift |
| CockroachDB | cockroach |
| MySQL | mysql |
| RDS MySQL | rds_mysql |
| Microsoft SQL Server | mssql |
| Big Query | bigquery |
| Oracle DB | oracle |
| SQLite | sqlite |

We use Legion Query Runner library as connectors. You can find more info on their [api doc](https://theralabs.github.io/legion-database/docs/category/query-runners).

## What is MCP?

The Model Context Protocol (MCP) is a specification for maintaining context in AI applications. This server uses the [MCP Python SDK](https://github.com/modelcontextprotocol/python-sdk) to:

- Expose database operations as tools for AI assistants
- Provide database schemas and metadata as resources
- Generate useful prompts for database operations
- Enable stateful interactions with databases

## Installation & Configuration

### Required Parameters

For single database configuration:
- **DB_TYPE**: The database type code (see table above)
- **DB_CONFIG**: A JSON configuration string for database connection

For multi-database configuration:
- **DB_CONFIGS**: A JSON array of database configurations, each containing:
  - **db_type**: The database type code
  - **configuration**: Database connection configuration
  - **description**: A human-readable description of the database

The configuration format varies by database type. See the [API documentation](https://theralabs.github.io/legion-database/docs/category/query-runners) for database-specific configuration details.

### Installation Methods

#### Option 1: Using UV (Recommended)

When using [`uv`](https://docs.astral.sh/uv/), no specific installation is needed. We will use [`uvx`](https://docs.astral.sh/uv/guides/tools/) to directly run *database-mcp*.

**UV Configuration Example (Single Database):**

```json
REPLACE DB_TYPE and DB_CONFIG with your connection info.
{
    "mcpServers": {
      "database-mcp": {
        "command": "uvx",
        "args": [
          "database-mcp"
        ],
        "env": {
          "DB_TYPE": "pg",
          "DB_CONFIG": "{\"host\":\"localhost\",\"port\":5432,\"user\":\"user\",\"password\":\"pw\",\"dbname\":\"dbname\"}"
        },
        "disabled": true,
        "autoApprove": []
      }
    }
}
```

**UV Configuration Example (Multiple Databases):**

```json
{
    "mcpServers": {
      "database-mcp": {
        "command": "uvx",
        "args": [
          "database-mcp"
        ],
        "env": {
          "DB_CONFIGS": "[{\"id\":\"pg_main\",\"db_type\":\"pg\",\"configuration\":{\"host\":\"localhost\",\"port\":5432,\"user\":\"user\",\"password\":\"pw\",\"dbname\":\"postgres\"},\"description\":\"PostgreSQL Database\"},{\"id\":\"mysql_data\",\"db_type\":\"mysql\",\"configuration\":{\"host\":\"localhost\",\"port\":3306,\"user\":\"root\",\"password\":\"pass\",\"database\":\"mysql\"},\"description\":\"MySQL Database\"}]"
        },
        "disabled": true,
        "autoApprove": []
      }
    }
}
```

#### Option 2: Using PIP

Install via pip:

```bash
pip install database-mcp
```

**PIP Configuration Example (Single Database):**

```json
{
  "mcpServers": {
    "database": {
      "command": "python",
      "args": [
        "-m", "database_mcp", 
        "--repository", "path/to/git/repo"
      ],
      "env": {
        "DB_TYPE": "pg",
        "DB_CONFIG": "{\"host\":\"localhost\",\"port\":5432,\"user\":\"user\",\"password\":\"pw\",\"dbname\":\"dbname\"}"
      }
    }
  }
}
```

## Running the Server

### Production Mode

```bash
python mcp_server.py
```

### Configuration Methods

#### Environment Variables (Single Database)

```bash
export DB_TYPE="pg"  # or mysql, postgresql, etc.
export DB_CONFIG='{"host":"localhost","port":5432,"user":"username","password":"password","dbname":"database_name"}'
uv run src/database_mcp/mcp_server.py
```

#### Environment Variables (Multiple Databases)

```bash
export DB_CONFIGS='[{"id":"pg_main","db_type":"pg","configuration":{"host":"localhost","port":5432,"user":"username","password":"password","dbname":"database_name"},"description":"PostgreSQL Database"},{"id":"mysql_users","db_type":"mysql","configuration":{"host":"localhost","port":3306,"user":"root","password":"pass","database":"mysql"},"description":"MySQL Database"}]'
uv run src/database_mcp/mcp_server.py
```

If you don't specify an ID, the system will generate one automatically based on the database type and description:

```bash
export DB_CONFIGS='[{"db_type":"pg","configuration":{"host":"localhost","port":5432,"user":"username","password":"password","dbname":"database_name"},"description":"PostgreSQL Database"},{"db_type":"mysql","configuration":{"host":"localhost","port":3306,"user":"root","password":"pass","database":"mysql"},"description":"MySQL Database"}]'
# IDs will be generated as something like "pg_postgres_0" and "my_mysqldb_1"
uv run src/database_mcp/mcp_server.py
```

#### Command Line Arguments (Single Database)

```bash
python mcp_server.py --db-type pg --db-config '{"host":"localhost","port":5432,"user":"username","password":"password","dbname":"database_name"}'
```

#### Command Line Arguments (Multiple Databases)

```bash
python mcp_server.py --db-configs '[{"id":"pg_main","db_type":"pg","configuration":{"host":"localhost","port":5432,"user":"username","password":"password","dbname":"database_name"},"description":"PostgreSQL Database"},{"id":"mysql_users","db_type":"mysql","configuration":{"host":"localhost","port":3306,"user":"root","password":"pass","database":"mysql"},"description":"MySQL Database"}]'
```

Note that you can specify custom IDs for each database using the `id` field, or let the system generate them based on database type and description.

## Multi-Database Support

When connecting to multiple databases, you need to specify which database to use for each query:

1. Use the `list_databases` tool to see available databases with their IDs
2. Use `get_database_info` to view schema details of databases
3. Use `find_table` to locate a table across all databases
4. Provide the `db_id` parameter to tools like `execute_query`, `get_table_columns`, etc.

Database connections are managed internally as a dictionary of `DbConfig` objects, with each database having a unique ID. Schema information is represented as a list of table objects, where each table contains its name and column information.

The `select_database` prompt guides users through the database selection process.

## Schema Representation

Database schemas are represented as a list of table objects, with each table containing information about its columns:

```json
[
  {
    "name": "users",
    "columns": [
      {"name": "id", "type": "integer"},
      {"name": "username", "type": "varchar"},
      {"name": "email", "type": "varchar"}
    ]
  },
  {
    "name": "orders",
    "columns": [
      {"name": "id", "type": "integer"},
      {"name": "user_id", "type": "integer"},
      {"name": "product_id", "type": "integer"},
      {"name": "quantity", "type": "integer"}
    ]
  }
]
```

This representation makes it easy to programmatically access table and column information while keeping a clean hierarchical structure.

## Exposed MCP Capabilities

### Resources

| Resource | Description |
|----------|-------------|
| `resource://schema/{database_id}` | Get the schemas for one or all configured databases |

### Tools

| Tool | Description |
|------|-------------|
| `execute_query` | Execute a SQL query and return results as a markdown table |
| `execute_query_json` | Execute a SQL query and return results as JSON |
| `get_table_columns` | Get column names for a specific table |
| `get_table_types` | Get column types for a specific table |
| `get_query_history` | Get the recent query history |
| `list_databases` | List all available database connections |
| `get_database_info` | Get detailed information about a database including schema |
| `find_table` | Find which database contains a specific table |
| `describe_table` | Get detailed description of a table including column names and types |
| `get_table_sample` | Get a sample of data from a table |

All database-specific tools (like `execute_query`, `get_table_columns`, etc.) require a `db_id` parameter to specify which database to use.

### Prompts

| Prompt | Description |
|--------|-------------|
| `sql_query` | Create an SQL query against the database |
| `explain_query` | Explain what a SQL query does |
| `optimize_query` | Optimize a SQL query for better performance |
| `select_database` | Help user select which database to use |

## Development

### Using MCP Inspector

run this to start the inspector
```bash
npx @modelcontextprotocol/inspector uv run src/database_mcp/mcp_server.py
```

then in the command input field, set something like
```
run src/database_mcp/mcp_server.py --db-type pg --db-config '{"host":"localhost","port":5432,"user":"username","password":"password","dbname":"database_name"}'
```

### Testing

```bash
uv pip install -e ".[dev]"
pytest
```

### Publishing

```bash
# Clean up build artifacts
rm -rf dist/ build/ 
# Remove any .egg-info directories if they exist
find . -name "*.egg-info" -type d -exec rm -rf {} + 2>/dev/null || true
# Build the package
uv run python -m build
# Upload to PyPI
uv run python -m twine upload dist/*
```

## License

This repository is licensed under GPL