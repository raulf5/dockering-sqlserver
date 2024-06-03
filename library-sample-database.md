# Library sample database

### Intro

We will use a database called **library** whose entity-relation diagram is detailed in Fig. 1, and where each table stores the following:

* **authors**, **publishers**, **customers**, and **librarians**: unique identifiers for each record and the corresponding names.
* **books**: besides the unique identifier for each book and its name, books also have a 13-character ISBN, an edition number, and are related to an author and publisher through the use of foreign keys. As such, they point to the unique identifiers from those tables, thus ensuring that the author and publisher of a given book already exists in the database.

> The unique identifiers in the above tables act as primary keys. Particularly, in the **books** table, we are not using the ISBN for that purpose since we may have multiple copies of the same book and edition.

* **loans** stores each book loan that has been made. The use of **author\_id**, **customer\_id**, and **book\_id** as foreign keys are complemented by the loan date and current status (active or not).

![Figure 1 - Sample library database](https://i.imgur.com/IcTH5m6.png)

### Tasks

* [ ] Open Azure Data Studio.
* [ ] Create a database by right-click on your server, localhost, and select **New Query.**
* [ ] Paste the [Library script](library-script.md) in sql editor and select **Run.**
* [ ] Right-click the Databases node and select **Refresh** to view the new database.

