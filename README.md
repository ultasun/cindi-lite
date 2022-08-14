# Docker Compose Pack for *cindi-lite*
---
[*CINDI*](https://github.com/ultasun/cindi) is a *Meta Database Management System* which provides a simple way for front-end applications to perform [*CRUD*](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) operations  with various back-end stores.  *CINDI* is written in *Python 3*, and is available in the [*PyPI Index* as a *PIP* package](https://pypi.org/project/cindi/).

This git repository makes available a *docker compose pack* in order for end users (like you!) to get running with *CINDI* quickly, to enable the tenet of supporting rapid application prototyping.

This *docker compose pack* utilizes one *Docker* image of the same name, called [*cindi-lite*](https://hub.docker.com/repository/docker/ultasun/cindi-lite). There are no other images utilized, and *docker compose* is used to orchestrate the setup.

**Only SQLite3 will be available as the backing store.**

*cindi-lite* uses significantly less resources (and electricity) than a fully enabled *CINDI* outfit. **If you would prefer to use the full outfit, then use the [*cindi-plus*](https://github.com/ultasun/cindi-plus) pack instead.**

---
## Setup

The following instructions assume a *UNIX*-like system.

1. Install [*Docker*](https://www.docker.com) for your system.
2. Clone this *git* repository, open a terminal and run:
   - `$ git clone https://github.com/ultasun/cindi-lite`
      - **Or, if you do not have *git* installed**, download the ZIP archive from this page, extract the ZIP archive, and `cd` your *Terminal* or *Console* into the directory created by extracting the archive.
3. *cindi-lite* only supports *SQLite3*, and you can see that configuration in `config/stores.txt`, it is a *Python dictionary*:
   - `{'sqlite3': {'db': 'db0', 'sqlite3_file_prefix': 'data/'}}`
      - The `'db'` key is used to dictate the filename of the SQLite3 database. The default name `db0` should be fine for you.
4. *CINDI* does not automatically generate *SQLite3* [*DDL*](https://en.wikipedia.org/wiki/Data_definition_language) statements. You must do this yourself, and altering the schema after the first start-up has not been tested.
   - Check the file `init-nonsense-sqlite3.sql` for an example, **and adjust this file to your liking!**
   - The restrictions are:
     - Each table **must** have an auto-incrementing primary key column called `id`.
     - No foreign key constraints for tables intended for *CINDI* registration.
     - Every other column in the table **must** be a string data type, such as `TEXT` in the case of *SQLite3*.
       - You are likely receiving *JSON* objects via *CINDI*'s *HTTP Flask* service, so this should not pose any practical limitations.
   - You may have 'background' tables, procedures, triggers, constraints, any data-type, and other features in the database.
     - These components must not be registered with *CINDI*.
     - There should be no problem with these other features manipulating the *CINDI*-aware tables.
       - Race conditions may occur if serving multiple users and your 'background' features become too elaborate.
       	 - This would be the point when you should discontinue utilizing *CINDI*.
     - As the *Docker* container starts, your defined (or the default 'nonsense') *DDL* statements will be executed with *SQlite3* for you, based on the contents of `init-nonsense-sqlite3.sql`.
5. *CINDI* does not automatically detect what is in the *SQLite3* database, you must register which tables are *CINDI* tables in `config/tables.txt`.
   - This is how the database may have extra 'background' features; such as tables, procedures, triggers, constraints between these non-registered objects, etc.
   - `config/tables.txt` *MUST* be a *Python list* (or *tuple*), for example:
     - `["nonsense"]`
   - *CINDI* will only manage the tables specified in this *list*.
6. That's it! In your terminal window, `cd` into the directory with the `docker-compose.yml` file, and run
   - `$ docker compose up`

**If you started *CINDI* with the default schema**, then, `docker exec -it` your way into the container (or, if using *Docker Desktop*, click on the little terminal icon in the container list), and run the following commands to get some action:
   - `$ python`
   - `>>> import cindi`
      - You will see a couple warnings about unavailable libraries for backing-storage systems you're not using. This is normal.
   - `>>> cindi.quick_unit_tests()`
     - This runs all of the [*INDI*](https://github.com/ultasun/cindi) expressions in `cindi.EXAMPLE_LIST`
     - You may pretty-print these using:
       - `>>> cindi.print_2d_list(cindi.EXAMPLE_LIST)`
   - `>>> print(cindi.EXAMPLE5)`
   - `>>> cindi.quick_cindi(cindi.EXAMPLE5)`

---

Check the full [*CINDI* README](https://github.com/ultasun/cindi) if you'd like more ideas of what to do next,  or why *CINDI* is helpful for supporting the rapid prototyping of web applications.

---

Thank you for utilizing *CINDI*!