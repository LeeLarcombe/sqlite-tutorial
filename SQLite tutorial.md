


## Introduction to SQL using SQLite

In this session you are going to be provided with two tab-separated files containing data extracted from UniProt and IntAct. 

You will use these to create a new SQLite database with two tables - one containing binary protein-protein interaction data and another containing annotation about proteins. 

Both can be related through the unique, protein-specific UniProt accession identifiers.

You will then use this database to learn about different ways to query the data using SQL. SQLite implements most of the same SQL commands and syntax as other database systems that also use SQL such as MySQL, PostgreSQL and Oracle. The main difference is that whilst the others all require a server and client setup, SQLite works with databases as simple local files. This makes them easy to set up on a variety of systems, and easily portable.

(If you have this as a paper handout, go to http://materials.nexastem.com/sqlite). Right-Click to download the files [***annotation.tab***](http://materials.nexastem.com/sqlite/annotation.tab) and [***interactome.tab***](http://materials.nexastem.com/sqlite/interactome.tab) to your desktop. 

>The data in the ***annotation.tab*** file was collected by using the UniProt API. Open the file in either the text editor or spreadsheet (Libre Office Sheets) application on your desktop and take a look at it.

Spend a few minutes looking at the data and thinking about what you heard in the lecture about database design. We are going to import this data into a database table soon - what can you spot as **good** or **bad** about this data? (does it *really* matter?)

---
### Part 1a. Navigating the system command line at the terminal (Linux or Mac)

The Linux or Unix (MacOS is a type of Unix underneath) command prompt, or shell, as it is also called, provides you with direct access to the operating system. It is a command interpreter: It interprets and executes all of your commands. The shell is really just another program that sits between your keyboard, the operating system, and other programs.

#### Navigating files and folders/directories

Now lets progress, and look at navigating the Linux file system from the shell. There are three main commands we can use to do this: **pwd**, **ls**, and **cd**.

The Linux filesystem is based on a file and directory structure which should be familiar to you. The first thing you'll want to know is where you are in the system. Since you just logged in you should be in your home directory. However, for learning purposes enter **pwd**

```bash
[prompt]$ pwd _press return_ (we will now dispense with the _press returns_)
```

**pwd** stands for “print working directory”, and so you should be rewarded with the absolute path to your current location. The absolute path is specified from the root (/) directory.

The next thing to do is list the files found in the current directory. This is done with the **ls** command, which stands for “list”. _Type_ **_ls_** _at the prompt now._

```bash
[prompt]$ ls
```

You should see a list of files in the current directory. There are some file indicators shown in the basic listing: * for programs or executable files (also often coloured green), / for directories (often blue), @ for symbolic links (often light blue, these are similar to windows shortcuts). The colours can vary depending on the configuration of your shell.

Now you can tell basic file types apart, **ls** can be used to provide more information. Experiment with the following:

**ls -a** this shows all files, even hidden files

**ls -l** this shows a long listing

**ls -s** this shows file sizes

These switches can also be used together: ie **ls -als**

Many shell commands have options like this. This is a good place to introduce perhaps the most important of all shell commands – **man**.

This command invokes “man(ual) pages” for a command – these are fairly comprehensive help files. Try it now with:

```bash
[prompt]$ man ls

[prompt]$  man pwd
```

If you ever need help with a command – try **man** first. Some commands will have more basic help by appending --**help** (two hyphens) as an option. Try this for **ls**:

```bash
[prompt]$ ls --help
```

With these at your disposal, you should never need a printed manual!

Now that you know how to find out where you are, and list the files in a directory, it is time to learn how to navigate through the file system's directory structure. To do this you use the **cd** command, which stands for “change directory”. To change directory simply type **cd** followed by the path of the directory you want to go to.

If you append a leading slash (/) to your path, it is absolute from the root directory. If you don't provide the leading slash then the path is relative to the current working directory. eg.

```bash
[prompt]$  cd /
```

This will take you to the root directory. _Generate a directory list_ with **ls** and **cd** to some of the other directories you see there. ie

```bash
[prompt]$  cd /etc
```

A useful addition to this is the use of leading **.** **(full stop)**  and **.\.** **(two full stops)** to the path. When specifying a relative path the **.** can be used to specify the current directory, and **.\.** can be used to specify the parent directory. eg.

```bash
[prompt]$ cd / (this goes to the root directory)

[prompt]$ cd /etc (this goes to the etc directory)

[prompt]$  cd .. (goes back to the root directory)
```

The last thing to note is that **cd** when typed alone, will return you to your home directory.

>**_Helpful tip:_** _Using the up arrow on your keyboard enables you to access the command history for your shell session. Try it out – it saves re-typing! Also, if you start typing a filename or directory name, hitting the TAB key should automatically complete the name for you._


#### Basic File Operations

_Make sure you are in your home directory._ In order to achieve anything useful with a computer system you must be able to create, erase and manipulate files. Now it is time to create a new file. To create a new, empty file, use the command **touch**.

```bash
[prompt]$ touch <filename> 
```

Some other important file operations are copying (**cp**), moving (**mv**), and deleting (**rm**). These are complimented by the command for making (**mkdir**) and removing (**rmdir**) directories.

>Note: **rmdir** only removes directories if they are empty. If you need to remove a directory and its contents you should use **rm -r** - the **-r** means "recursive".

Please spend some time experimenting further with all these commands. There is little point in examining the exact usage of these commands here, so investigate their function in more detail using **man**.

---
### Part 1b. Navigating the system command line at the terminal (Windows)

Windows users have a couple of options for shell access. On Windows 10 the system "command prompt" is likely a shell called "Windows Powershell". Alternatively on Windows 10 - and certainly on earlier versions - the default shell may be a version of DOS. The Windows 10 Powershell or DOS will understand some of the filesystem navigation commands given above for Linux (**ls**, **cd**, **mkdir** and **rmdir** for example). The older DOS system on Windows will not recognise these and, although equivalent functions are available, we won't cover them here. ***Let us know if you need some help with these***

>Alternatively, WIndows 10 now offers an embedded version of Linux that can be installed - call the Linux Subsystem for Windows. This will enable to you use Linux software on your Windows system, and recognises all of the commands introduced in the Linux section above. Installing the Linux Subsystem for Windows requires administrator access to you machine and is beyond the scope of this session - but if you are interested in learning more about it we can discuss it during one of the breaks.

---
### Part 2. SQLite command line - and creating a database

When we load the SQLite command line we can provide it with the name of the database file we want to work with. If this database does not exist, SQLite creates an new empty database for us. 

Load the SQLite command line and create a new database with:

```bash
prompt$ sqlite3 ./practical.db
```

If you look in your file browser you should now see that the **practical.db** file has been created.

#### First - two important commands

You will now be faced with a new prompt:

```bash
sqlite>
```

You can type commands to manipulate the database here, or make queries using SQL. All SQLite commands are preceded by a . (dot). The two most important commands are to get basic help, and to exit the SQLite command line!

```SQL
.help

.exit
```

If you tried the **.exit** command, reload your database - we are now going to make some new tables to hold our data.

#### Now create some tables

First, create a table to hold the interactome data. The interactome data file contains one binary pair per row - a protein A that binds a protein B. The proteins are identified by UniProt accessions, so we will create a new table with two fields - one for protein A and one for protein B - both of type *varchar* and a maximum length of 10.

To do this we will use an SQL command. These are generally standard between different database systems that use SQL. They do not begin with a ***.*** like SQLite commands do, but they do always end in a ***;*** (which SQLite commands do not!)

>You do not need to use all caps for the words in all caps - this is just to show you which words in the statement are key parts of the SQL statement, and which other parts you could change if you needed to

```SQL
CREATE TABLE interactome(
	proteinA varchar(10), 
	proteinB varchar(10)
	);
```

>*Special SQLite note: We have used varchar to define a maximum length for the fields in this example, because that is what you **should** do when creating an SQL database. However, SQLite does not actually enforce this maximum length.*

Now we can create the table for the annotation data - for each protein this will have: UniProt accession; the standard gene symbol; a list of PDB structures available; the protein name; sequence; and a list of gene ontology terms. 

For the short identifiers (UniProt and standard gene symbol) we will use ***varchar*** types - and longer data of variable length will use ***text*** types.

```SQL
CREATE TABLE annotation(
	uniprotID varchar(10), 
	geneSymbol varchar(10), 
	pdb text, 
	name text, 
	sequence text, 
	go text,
	reactome text
	);
```

#### Inspect our work so far

You can always check what tables are available in a database (and in this case check we have made them!) with:

```SQL
.tables
```

You can also check the fields and types contained in the database tables, the database ***schema*** with the command:

```SQL
.schema
```

Hopefully you will see the two tables you created (***annotation*** and ***interactome***) - and that they have the correct fields and data types. Now we can begin loading some data into the database...

#### Loading data into the database

As long as the number of fields in our data file matches the number of fields that have been created in a table, SQLite can very simply populate the database table. However, as data text files can come with different characters separating the data fields (comma-separated or tab-separated for example), we need to tell SQLite which separation character to expect. Your data files are tab-separated, so we need:

```SQL
.separator \t
```

Now we can give the commands to import the data

```SQL
.import ./interactome.tab interactome
```

```SQL
.import ./annotation.tab annotation
```
If importing the data goes badly, SQLite will tell you the errors. If it goes well, SQLite tells you nothing!

>Hopefully you did not get any error messages - shout for help if you do!


---
### Part 3. Basic Selections

If everything went wrong in the previous sections, you can download a pre-made version of the database here: [database download](http://materials.nexastem.com/sqlite/practical.db)

The benefit of using SQL is not the ability to just store information in a database but to be able to query the data in ways that help you gain insight into either what data you have, or the relationships between data.

All of the important SQL searches use a **SELECT** query - let's try a simple query to find annotation about the protein ***CD44*** or UniProt ID ***P16070***.

```SQL
SELECT * FROM annotation WHERE uniprotID = "P16070";
```
This query returns all of the fields for any records in the annotation table where the UniProt ID matches our protein P16070.

If you only want to return specific fields from a query, you can specify them as part of the select statement. Try:

```SQL
SELECT name,sequence FROM annotation WHERE uniprotID = "P16070";
```


#### Simple wildcard matching

Sometimes we want to query the data using a wildcard match - either because we do not know the specific matching text to use or because we want to return a set of results. Instead of using an **=** we use **LIKE** and include the wildcard symbols **%**. Try this wildcard match on the gene ontology field to return the names for any proteins that may be membrane-associated:

```SQL
SELECT name FROM annotation WHERE go LIKE "%membrane%";
```
>Tip: You do not need to use two **%** wildcard symbols in the match. ***%membrane%*** searches for the match anywhere in the field. ***%membrane*** would search for the match at the end of the field text and ***membrane%*** would search for it at the beginning.

Sometimes you will just want to take a quick look at what your query will return without running it for all the records in a dataset - or you might just want a limited set. You can limit (to 5) the number of records returned with:

```SQL
SELECT name FROM annotation WHERE go LIKE "%membrane%" LIMIT 5;
```

Sometimes it is also useful to be able to order your output - that might be on the field you used to search the data, or on any of the other output fields. Order can be **ASC**ending or **DESC**ending - text/string fields will be alphabetical and numerical fields in numerical order.

```SQL
SELECT geneSymbol,name FROM annotation WHERE go LIKE "%membrane%" ORDER BY name ASC;
```

Try the same query with **DESC**

Now let's change to look at the data in the ***interactome*** table.  Get a list of the first 50 proteins in the ***proteinA*** field in alphabetical order with:

```SQL
SELECT proteinA FROM interactome ORDER BY proteinA ASC LIMIT 50;
```

You should see that there are quite a few duplicates in this list, as each record is a single binary interaction (proteinA with a proteinB). If we want to see unique entries in the results we can use the **DISTINCT** modifier in the query:

```SQL
SELECT DISTINCT(proteinA) FROM interactome ORDER BY proteinA ASC LIMIT 50;
```

You should see a new list of 50 distinct UniProt identifiers returned in alphabetical order.


#### Counting & math

SQLite (and other SQL-based databases) provides mathematical functions that you can use in queries to summarise your data. You can return a **COUNT** of records and calculate the **LENGTH** of strings for example. Try the following example that will return you the number of records in the ***annotation*** table that have a protein sequence longer than ***400*** amino acids:

```SQL
SELECT COUNT(*) FROM annotation WHERE LENGTH(sequence) > 400;
```

You can use the **AVG** function to calculate the average length of the sequences in the database:

```SQL
SELECT AVG(LENGTH(sequence)) FROM annotation;
```
This is also a good point to introduce that you can add multiple conditions to the WHERE part of the query. For example, let's fetch a list of gene symbols for proteins that are membrane-associated (based on GO terms) and have sequences that are over 1000 amino acids long:

```SQL
SELECT COUNT(geneSymbol) FROM annotation 
	WHERE go LIKE "%membrane%" AND LENGTH(sequence) > 1000;
```

*Now try the following on your own:*

* How many proteins are covered by the annotation table?

* How many binary interactions are included in the interactome?

* Try exploring the **MAX** and **MIN** length of sequences in the database.

* Can you work out the name of the protein with the longest sequence?

* Try and work out the **SUM** function - how many amino acid residues are in the database?

*Now let's try a more complicated query:*

You have already learnt about **DISTINCT**, **COUNT** and **ORDER BY**. Now we are going to combine them to produce a frequency listing showing how many protein interactors each protein in the ***interactome*** table has - with the most connected first.

We only want **DISTINCT** copies of ***proteinA*** but we need to **COUNT** the numbers of ***proteinB*** connections. So that we can **ORDER BY** this number, we can assign the result of the **COUNT** to a new variable within the query statement - ***frequency***. This does not exist in the table of course - it only exists during the execution of the query.

So that the **DISTINCT** output of ***proteinA*** entries shows us one protein per line with its associated connection count, we also need to **GROUP** the results by ***proteinA***. Try out the query below to see what happens:

(this one has been broken over multiple lines to make it easier to read - you can still type it all on one line!)
```SQL
SELECT DISTINCT(proteinA),COUNT(proteinB) AS frequency 
	FROM interactome 
	GROUP BY proteinA 
	ORDER BY frequency DESC;
```

*Now try extending this query to return just the top 10 most connected proteins.*

---
### Part 4. More complex queries

#### Nested queries

SQL queries can be nested together so that the output from one query becomes part of the input or limitation for another.

Let's consider an example to answer the question: How many records contain sequences that are above average in length?

You have already seen a query that finds entries with sequences over a specified length. You also saw a query to find the average length of a sequence.

You could run the query to get the average length and then just plug that number into the second query (a perfectly acceptable thing to do!), or you might try including the **AVG** in the size comparison part of the **WHERE** clause:

```SQL
...WHERE LENGTH(sequence) > AVG(LENGTH(sequence));
```

**But this doesn't work**. You will get an error about an incorrect use of the summing function (**AVG**) - you can only use this as part of what is returned from the **SELECT** part of the statement. The solution to this problem is to use it correctly in its own **SELECT** statement but *nested* within the size comparison part of another query. Try: 

```SQL
SELECT COUNT(*) FROM annotation 
	WHERE LENGTH(sequence) > (SELECT AVG(LENGTH(sequence)) FROM annotation);
```

Now you will get a **COUNT** of how many records contain sequence strings that are longer than average!

#### JOIN example

If you think back to the introductory talk you had, you should remember that the benefit/point of this kind of database is that it is relational - there are connections between the data stored in the different tables.

Here we have binary interactions representing the Human interactome in the ***interactome*** table - all identified with UniProt accessions, and annotation about proteins from UniProt in the ***annotation*** table - also identified with UniProt accessions.

We didn't specify **keys** for the tables which, with more complex data, might be important. However, we can still connect data together from the tables using **SELECT** queries that **JOIN** between the tables on a data field.

Let's demonstrate this using a query that searches the ***interactome*** table for interaction partners to the protein with UniProt accession **"P16070"** - *but* the **SELECT** returns the ***name*** of the interaction partners from the ***annotation*** table by linking the UniProt accession of the interacting ***proteinB*** field in the ***interactome*** table with that in the ***uniprotID*** field in the ***annotation*** table.

>Notice how we can refer to which table a field is in by including the table name - ***interactome.proteinB*** and ***annotation.uniprotID*** for example.

Try the query:

```SQL
SELECT name FROM annotation 
	INNER JOIN interactome ON interactome.proteinB = annotation.uniprotID 
	WHERE interactome.proteinA = "P16070";
```

You should have two protein names returned - two interactors with your query protein.

#### JOIN with nested SELECT

We can also combine the idea of the nested **SELECT** with a **JOIN**. This time we are going to retrieve a list of interacting proteins as approved gene symbols (***annotation.geneSymbol***) - *but* use a gene symbol as the search term (in this case **CD44** - which is the symbol for **P16070**).

The ***interactome*** table does not contain gene symbols, so we need to search the ***annotation*** table for the gene symbol (***geneSymbol***) and return the uniprot accession (***uniprotID***) so that we can search the ***interactome*** table.

Try the following query that combines this **SELECT** nested with the **JOIN**:

```SQL
SELECT geneSymbol FROM annotation 
	INNER JOIN interactome ON interactome.proteinB = annotation.uniprotID 
	WHERE interactome.proteinA = (
	SELECT uniprotID FROM annotation WHERE geneSymbol = "CD44");
```
You should now have the gene symbols of the two interacting proteins returned, based on searching for the gene symbol **CD44**.


#### Select with a REGEX

In the section covering basic **SELECT** queries, we looked at using string wildcards in a **LIKE** clause. These are useful when you want to match a string in part of a larger string, but not very flexible if you need a loose match or to match a pattern.

Most programming and scripting languages can make use of a pattern matching approach called **Regular Expressions**. These enable simple - through to very complex/powerful - pattern matching operations. Fortunately most flavours of SQL will support regular expressions in some way - including SQLite!

A full session on regular expressions is beyond the scope of today, and there are many good reference examples and help pages online - but let's look at a couple of examples to illustrate why you might want to learn more:

Let's consider searching for proteins that contain the motif "***ISRTEAADLC***". Try the following statement:

```SQL
SELECT geneSymbol FROM annotation WHERE sequence LIKE "%ISRTEAADLC%";
```

You will find this motif is part of the sequence of our old friend **CD44**.

Now let's try with a regular expression - we need to load an add-on to SQLite for this:

```SQL
.load "/usr/lib/sqlite3/pcre.so"
```
Now try the same as before query - but this time using a regular expression (**REGEXP**):

```SQL
 SELECT geneSymbol FROM annotation WHERE sequence REGEXP('ISRTEAADLC');
```

You should get CD44 again. However, what if there was some variation in this motif? **LIKE** cannot help with this - but **REGEXP** can. We can use a dot **.** in the regex pattern to match any character at that position in the string:

```SQL
SELECT geneSymbol FROM annotation WHERE sequence REGEXP('ISR.E.A.L.');
```

Now you should find two matching proteins that contain this motif!

If we want to specify a limited variation at a position, we can provide the alternatives to match in square brackets **[]** using the *or* character **|** - try:

```SQL
SELECT geneSymbol FROM annotation WHERE sequence REGEXP('I[S|P]R.E.A.L.');
```

Now you should find three matching proteins that contain this motif - with either an **S** or a **P** in position 2.

You can also specify how many matches to make to a character - for example we could try **I[S|P]R...A.L** to expand the matches to the middle of the pattern - or we can use **I[S|P]R.{3}A.L** where we specify that we want **3** matches to any character (**.**) by using curly braces **{}** - try:

```SQL
SELECT geneSymbol FROM annotation WHERE sequence REGEXP('I[S|P]R.{3}A.L.');
```

You should now get about 20 hits. Don't forget you can also combine this with other things you have learnt:

```SQL
SELECT COUNT(geneSymbol) FROM annotation WHERE sequence REGEXP('I[S|P]R.{3}A.L.');
```
You did have 20 matches! What about:

```SQL
SELECT AVG(LENGTH(sequence)) FROM annotation WHERE sequence REGEXP('I[S|P]R.{3}A.L.');
```
The average length of a sequence that contains your motif.

**A final challenge:** Can you work out what this query is going to do (try and work it out before you run it)?

>Hint: there is something new here - a nested query, but we are using **IN** rather than an **=** for the **WHERE** clause. This is how we handle a nested **SELECT** that returns *multiple* results for the main query to cycle through!

```SQL
SELECT MAX(LENGTH(sequence)) FROM annotation 
	INNER JOIN interactome ON interactome.proteinB = annotation.uniprotID 
	WHERE interactome.proteinA IN (
	SELECT uniprotID FROM annotation WHERE sequence REGEXP('I[S|P]R.{3}A.L.')); 
```





