# Installation Guide

This document provides step-by-step installation instructions for
running the OMOP → SPARQL tutorial using Ontop and DuckDB.

The tutorial supports two execution modes:

1.  Ontop CLI (standalone SPARQL endpoint)
2.  GraphDB Desktop with embedded Ontop

------------------------------------------------------------------------

# 1. System Requirements

Minimum requirements:

-   Java 17 or higher
-   8 GB RAM recommended
-   macOS, Linux, or Windows

------------------------------------------------------------------------

# 2. Install Java 17+

Check whether Java is already installed:

``` bash
java -version
```

You should see version 17 or higher.

## macOS (Homebrew)

``` bash
brew install openjdk@17
```

If necessary:

``` bash
sudo ln -sfn /opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```

## Ubuntu / Debian

``` bash
sudo apt update
sudo apt install openjdk-17-jdk
```

## Windows

Download OpenJDK 17 from:

https://adoptium.net/

After installation, open a new terminal and verify:

``` bash
java -version
```

------------------------------------------------------------------------

# 3. Install Ontop CLI (Required for CLI Mode)

Download Ontop from:

https://ontop-vkg.org/download/

Select the latest 5.x CLI distribution.

Unzip the archive:

``` bash
unzip ontop-cli-5.x.zip
```

Add the `bin` directory to your PATH (recommended):

macOS / Linux:

``` bash
export PATH=$PATH:/path/to/ontop-cli-5.x/bin
```

Windows:

Add the `bin` directory to your System PATH via Environment Variables.

Verify installation:

``` bash
ontop version
```

You should see:

    Ontop version 5.x

------------------------------------------------------------------------

# 4. Install DuckDB

DuckDB is required for GraphDB Desktop mode and optional for CLI mode.

## macOS

``` bash
brew install duckdb
```

## Ubuntu

``` bash
sudo apt install duckdb
```

## Windows

Download from:

https://duckdb.org/docs/installation/

Verify:

``` bash
duckdb --version
```

------------------------------------------------------------------------

# 5. Install GraphDB Desktop (Optional)

GraphDB Desktop is only required if you want to run the embedded Ontop
mode.

Download from:

https://www.ontotext.com/products/graphdb/

Follow vendor installation instructions for your operating system.

After installation, start GraphDB and verify that the Workbench opens
at:

http://localhost:7200

------------------------------------------------------------------------

# 6. Install DuckDB JDBC Driver (GraphDB Mode Only)

Download the DuckDB JDBC driver:

https://repo1.maven.org/maven2/org/duckdb/duckdb_jdbc/

Place the `.jar` file into the GraphDB `lib/ext` directory.

Examples:

-   macOS: `GraphDB.app/Contents/app/lib/ext/`
-   Linux: `graphdb/lib/ext/`
-   Windows: `graphdb\lib\ext\`

Restart GraphDB after placing the driver.

------------------------------------------------------------------------

# 7. Verify Everything

CLI mode test:

``` bash
ontop endpoint   --ontology ontology/OMOP.ttl   --mapping mappings/OMOP-parquet.obda   --properties mappings/OMOP.properties   --port=8081
```

Open:

http://localhost:8081/sparql

GraphDB mode test:

-   Start GraphDB Desktop
-   Create Ontop Virtual Repository
-   Use driver class:

```{=html}
<!-- -->
```
    org.duckdb.DuckDBDriver

If both modes start without errors, installation is complete.

------------------------------------------------------------------------

# Tested Versions

-   Ontop 5.5.x
-   GraphDB Desktop 10.4.x
-   DuckDB 0.10.x
-   Java 17+

------------------------------------------------------------------------

For architecture details, see ARCHITECTURE.md.
