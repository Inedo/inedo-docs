---
title: "Maintenance"
order: 4
---

Romp requires no external dependencies and has a very light footprint, making installation and maintenance quite easy.

Romp stores data in two different places, and if they do not exist when Romp runs, they will be created.

### Local Package Registry

Romp uses the standardized local package registry specification, which allows Romp and other tools to see which packages are installed on the machine. By default, a Machine-level registry is used, which is stored in `%ProgramData%\upack`.

### Local Data Store

In addition to the default romp.json configuration file, Romp uses a small SQLite database that contains a history of jobs run on the machine with romp, including all execution logs and collected configuration. Resource credentials are also stored encrypted in this database. This database can be safely deleted if you want to purge logs and stored configuration.

By default, these are stored in the `%ProgramData%\romp` directory.