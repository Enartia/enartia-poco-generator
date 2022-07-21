# enartia-poco-generator
A Plain Old CLR Objects (POCO) is a class, which doesn't depend on any framework-specific base class. It is like any other normal .NET class. Due to this, they are called Plain Old CLR Objects. These POCO entities (also known as persistence-ignorant objects) support most of the same LINQ queries as Entity Object derived entities. These classes (POCO classes) implements only the domain business logic of the Application.

Source Code Url: https://www.codeproject.com/Articles/892233/POCO-Generator

Introduction

There are plenty of ways to generate POCO classes from a database. The hard way is to handwrite them. This may be good for introductory/one-or-two classes scenarios, but is not applicable for production. There are codegen tools, like CodeSmith. The tool detects changes to the database and generates the appropriate POCO classes. There are script tools like T4 (Text Template Transformation Toolkit), which Visual Studio supports. The solution that I opt to create is a visual-centric stand-alone application, the POCO Generator, that traverses the SQL Server, and generates POCOs from various data objects. There are 5 types of database objects that the POCO Generator can handle:

Tables
Views
Stored Procedures
Table-valued Functions
User-Defined Table Types (TVP)
POCO Generator also detects primary keys, foreign keys, unique keys and indexes for tables.

POCO Generator
The SQL Server tree lists all the databases on that instance and each database lists its data objects - tables, views, procedures, functions & TVPs. The checkboxes on the tree are for picking specific objects for exporting to files. The upper right side of the window shows the current generated POCOs, based on what is selected in the tree. The panel at the bottom lets you manipulate how the POCOs look and handles exporting them to files. As you change these options, the POCO panel will be refreshed and you'll see immediately how the POCO looks.

POCO
The POCO section manages the structure of the POCO.

Properties/Data Members - Normally, a POCO is constructed with properties, but this option gives an option to use data members instead.
Virtual Properties - Adds a virtual modifier to the properties.
Override Properties - Adds an override modifier to the properties.
Partial Class - Adds a partial modifier to the class.
Struct Types Nullable - All the struct types will become nullable (int?, DateTime?) even if they are not nullable in the database.
Comments & Without null - A comment, for each property, of the original SQL Server type and whether it is nullable. Without null removes the nullable comments.
using - Adds using statements at the beginning of the POCO.
Namespace - Wraps the POCO with the specified namespace.
Inherit - Adds a comma-delimited list of inherit base class and interfaces.
Column Defaults - Adds properties initialization, based on the column default values in SQL Server. Default value that can't be handled properly will be commented.
New Line Between Members - If the POCOs become larger, especially with EF annotations, this option will add lines between the POCO properties.

Navigation Properties
The navigation properties connect decoupled POCOs by the foreign keys that they represent.

Navigation Properties - Adds navigation properties and constructor initialization if necessary.
Virtual - Adds a virtual modifier to the navigation properties.
Override - Adds an override modifier to the navigation properties.
Show Many-to-Many Join Table - In a Many-to-Many relationship, the join table is hidden by default. When this option is checked, the join table is forcefully rendered.
Comments - A comment of the original SQL Server foreign key.
List, ICollection, IEnumerable - The type of the navigation property when it is a collection of POCOs.

Class Name
By default, the name of the POCO class is the name of the data object, whether it is a C# valid name or not. The Class Name section manipulates that name.

Singular - Changes the name from plural to singular. Applicable only for tables, views & TVPs. I tried to do my best here, working with the singular rules of English grammar, but obviously it’s not fool-proof.
Include DB - Adds the database name.
DB Separator - Adds the specified separator after the database name.
Include Schema - Adds the schema name.
Ignore dbo Schema - If the schema name is "dbo", doesn't add the schema name.
Schema Separator - Adds the specified separator after the schema name.
Words Separator - Adds the specified separator between words in the class name. Word are defined as text between underscores or in a camel case.
The class name EmployeeDepartmentHistory has 3 words in it, Employee, Department & History. The class name Product_Category has 2 words, Product & Category.

CamelCase, UPPER CASE, lower case - Changes the case of the class name.
Replace, With, Ignore Case - Performs a search and replace on the class name.
Fixed Name - Ignores all the previous options and set the name of the class to the specified fixed name.
Prefix & Suffix - Adds prefix and suffix texts to the class name.
EF Code-First Annotations
EF Annotations section adds various EF attributes to the POCO class and its properties. Applicable only for tables. You can read more about EF annotations on this MSDN page Code First Data Annotations.

EF - Adds EF main attributes.
Table attribute on the class declaration. [Table("Production.Product")]
Key attribute on primary key properties. [Key]
Column attribute for composite primary key properties with the Order value set to the order of the key in the composite primary key. [Column(Order = 1)]
MaxLength attribute on string properties. [MaxLength(50)]
Timestamp attribute on timestamp properties. [Timestamp]
DatabaseGenerated attribute on Identity & Computed properties. [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
Column - Adds Column attribute with Name and TypeName values. [Column(Name = "ProductID", TypeName = "int")]
Required - Adds Required attribute for properties that are not nullable. [Required]
Required with ErrorMessage - Just like Required but also adds an error message. [Required(ErrorMessage = "Product ID is required")]
ConcurrencyCheck - Adds ConcurrencyCheck attribute on Timestamp and RowVersion properties. [ConcurrencyCheck]
StringLength - Adds StringLength attribute on string properties. This attribute has no bearing on the database, unlike MaxLength. It is used as a user input validation. [StringLength(50)]
Display - Adds Display attribute. [Display(Name = "Product ID")]
Description - Adds Description attribute, to table and columns, from SQL Server extended properties table (MS_Description). [Description("Description from MS_Description")]
ComplexType - Group properties into a ComplexType based on the first underscore in their SQL column name, ComplexType_Column [ComplexType]
Index (EF6) - Adds Index attribute for each index which the property participates in. If the Index is unique or clustered, it also sets the Index corresponding properties. EF doesn't generate clustered index but it can be added by hand through SSMS. Applicable from EF6 and above. [Index("IX_ProductName", IsUnique = true)]
ForeignKey & InverseProperty - Adds ForeignKey attribute for navigation properties. Adds InverseProperty attribute when there is more than one navigation property between two POCOs. [ForeignKey("ProductID")] [InverseProperty("Product")]
Export to Files
Exports one or more POCOs to one or more files.

Folder - Specifies the folder to export to.
Append to File - Useful if you want to export multiple POCOs into a single file. If this option is not checked, the POCO Generator will export each POCO to a different file.
Export Button - If there are checked checkboxes on the SQL Server tree, the POCO Generator will export just them. Otherwise, it will export the current selected POCO.
Other Buttons
Copy - Copies the current selected POCOs to the clipboard.
Command Line - A command line for the currently selected options and chekced database objects.
Type Mapping - A popup of SQL Server to .NET type mapping.

Command Line
The command line popup reflects the options that are currently selected and the database objects that are checked in the server tree. You can also edit the command line in the editor but it will be regenerated every time you open the popup. The help window describes the command line switches (short and long) and the execution return codes. You can also save it to a batch file.

Type Mapping
You can read more about type mappings in this MSDN page SQL Server Data Type Mappings.

Tables and Foreign Keys
Right-click on a table and the context menu will give you several options to check any other tables that are connected to that one through foreign keys.

The first option (FK This -> To) checks all the tables that the table references to by foreign keys. The foreign keys are in the table. The second option (FK From -> This) checks all the tables that reference to the table by foreign keys. The foreign keys are in the other tables. The third option is a combination of the first two. The recursive options do what the first three options do but will continue recursively to check tables that are connected to tables that are connected to tables and so on.

Filter Results
You can filter the results in each group (Tables, Views, ...) by right-clicking on a group and choosing Filter from the context menu. In the filter popup, choose what name and what schema you want to include or exclude.

Stored Procedures with Many Result Sets
There is no way to determine if a stored procedure returns more than one result set. During the process of retrieving the schema of a stored procedure, only the first result set is returned. There is no way to get to the schema of any result set after the first one.

The "solution" is more of a hack than anything else. In the stored procedure, remark the first select query and alter the stored procedure. Then, go to the UI and right-click on the stored procedure. Click on Refresh from the context menu. Once the new POCO shows up, copy it or export it for further use. Continue with this process up to the last result set. When you're done, undo the remarks and restore the stored procedure.

Schemas
The process of retrieving schema of SQL Server data objects is mainly done through GetSchema methods from DbConnection class. The class DbConnection, which SqlConnection inherits from, has several GetSchema methods which do exactly as their name suggests. They return the schema information from the specified data source. You can pass, to the GetSchema method, the type of object that you're looking for and list of restrictions which are usually used to filter on database name, schema name and the name of the object. A full list of object types and restricts can be found on these MSDN pages Schema Collections and Schema Restrictions.

Tables & Views
The schema type for both tables and views is "Tables". For tables, put the string "BASE TABLE" on the last restriction which is a table type restriction. For views, put the string "VIEW" on the table type restriction.

Tables:

C#
Copy Code
using (SqlConnection connection = new SqlConnection(connectionString))
{
    connection.Open();
    DataTable allTables = connection.GetSchema("Tables", 
        new string[] { database_name, null, null, "BASE TABLE" });
    DataTable specificTable = connection.GetSchema("Tables", 
        new string[] { database_name, schema_name, table_name, "BASE TABLE" });
}
and Views:

C#
Copy Code
using (SqlConnection connection = new SqlConnection(connectionString))
{
    connection.Open();
    DataTable allViews = connection.GetSchema("Tables", 
        new string[] { database_name, null, null, "VIEW" });
    DataTable specificView = connection.GetSchema("Tables", 
        new string[] { database_name, schema_name, view_name, "VIEW" });
}
User-Defined Table Types (TVP)
TVP schema can't be retrieved through GetSchema methods or at least not retrieved reliably. Getting TVP schemas require a little querying on the SQL Server side. This first query gets all the TVPs on the database.

SQL
Copy Code
select 
    tvp_schema = ss.name, 
    tvp_name = stt.name, 
    stt.type_table_object_id 
from sys.table_types stt 
inner join sys.schemas ss on stt.schema_id = ss.schema_id
and for each TVP, we get its list of columns. @tvp_id parameter is the type_table_object_id column from the previous query.

SQL
Copy Code
select 
    sc.*, 
    data_type = st.name 
from sys.columns sc 
inner join sys.types st on sc.system_type_id = st.system_type_id and sc.user_type_id = st.user_type_id
where sc.object_id = @tvp_id
Stored Procedures & Table-valued Functions
The schema type for both stored procedures and functions is "Procedures". For stored procedures, put the string "PROCEDURE" on the last restriction which is a routine type restriction. For functions, put the string "FUNCTION" on the routine type restriction.

Stored Procedures:

C#
Copy Code
using (SqlConnection connection = new SqlConnection(connectionString))
{
    connection.Open();
    DataTable allProcedures = connection.GetSchema("Procedures", 
        new string[] { database_name, null, null, "PROCEDURE" });
    DataTable specificProcedure = connection.GetSchema("Procedures", 
        new string[] { database_name, schema_name, procedure_name, "PROCEDURE" });
}
and Functions:

C#
Copy Code
using (SqlConnection connection = new SqlConnection(connectionString))
{
    connection.Open();
    DataTable allFunctions = connection.GetSchema("Procedures", 
        new string[] { database_name, null, null, "FUNCTION" });
    DataTable specificFunction = connection.GetSchema("Procedures", 
        new string[] { database_name, schema_name, function_name, "FUNCTION" });
}
For each routine, we need to get its parameters. The schema type is "ProcedureParameters".

C#
Copy Code
using (SqlConnection connection = new SqlConnection(connectionString))
{
    connection.Open();
    DataTable routineParameters = connection.GetSchema("ProcedureParameters", 
        new string[] { database_name, routine_schema, routine_name, null });
}
At this point, we can filter out anything that is not a Table-valued function, meaning we need to remove Scalar functions. A scalar function has a single return parameter which is the result of the function and that's how we find them.

Once we have the routine parameters, we will build an empty SqlParameter for each one. An empty SqlParameter is a parameter with DBNull.Value set as its value. For a TVP parameter, we will build a parameter with SqlDbType.Structured type and an empty DataTable as its value.

This is a very abridged code snippet of how a SqlParameter is built.

C#
Shrink ▲   Copy Code
SqlParameter sqlParameter = new SqlParameter();

// name
sqlParameter.ParameterName = parameter_name;

// empty value
sqlParameter.Value = DBNull.Value;

// type
switch (data_type)
{
    case "bigint": sqlParameter.SqlDbType = SqlDbType.BigInt; break;
    case "binary": sqlParameter.SqlDbType = SqlDbType.VarBinary; break;
    ....
    case "varchar": sqlParameter.SqlDbType = SqlDbType.VarChar; break;
    case "xml": sqlParameter.SqlDbType = SqlDbType.Xml; break;
}

// size for string type
// character_maximum_length comes from the parameter schem
if (data_type == "binary" || data_type == "char" || 
data_type == "nchar" || data_type == "nvarchar" || 
data_type == "varbinary" || data_type == "varchar")
{
    if (character_maximum_length == -1 || character_maximum_length > 0)
        sqlParameter.Size = character_maximum_length;
}

// direction
if (parameter_mode == "IN")
    sqlParameter.Direction = ParameterDirection.Input;
else if (parameter_mode == "INOUT")
    sqlParameter.Direction = ParameterDirection.InputOutput;
else if (parameter_mode == "OUT")
    sqlParameter.Direction = ParameterDirection.Output;
Now, we are ready to get the columns of the routine. When it comes to routines, we will use SqlDataReader.GetSchemaTable() method to get the routine schema with CommandBehavior.SchemaOnly flag.

For stored procedures, we can use CommandType.StoredProcedure.

C#
Copy Code
using (SqlConnection connection = new SqlConnection(connectionString))
{
    using (SqlCommand command = new SqlCommand())
    {
        command.Connection = connection;
        command.CommandText = string.Format("[{0}].[{1}]", routine_schema, routine_name);
        command.CommandType = CommandType.StoredProcedure;

        // for each routine parameter, build it and add it to command.Parameters

        using (SqlDataReader reader = command.ExecuteReader(CommandBehavior.SchemaOnly))
        {
            DataTable schemaTable = reader.GetSchemaTable();
        }
    }
}
and for Table-valued functions, we need to construct a query that selects all the columns from the function.

C#
Copy Code
using (SqlConnection connection = new SqlConnection(connectionString))
{
    using (SqlCommand command = new SqlCommand())
    {
        command.Connection = connection;
        command.CommandType = CommandType.Text;

        command.CommandText = string.Format("select * from [{0}].[{1}](", routine_schema, routine_name);
        
        // for each routine parameter, build it and add it 
        // to command.Parameters and add its name to command.CommandText
        
        command.CommandText += ")";

        using (SqlDataReader reader = command.ExecuteReader(CommandBehavior.SchemaOnly))
        {
            DataTable schemaTable = reader.GetSchemaTable();
        }
    }
}
Primary Keys and Foreign Keys
I use SQL queries to retrieve information about primary keys and foreign keys. The GetSchema method is not detailed enough for that.

Given below is the query for primary keys.

SQL
Copy Code
select 
    Name = kc.name,
    Schema_Name = ss.name,
    Table_Name = object_name(kc.parent_object_id),
    Ordinal = ic.key_ordinal,
    Column_Name = c.name,
    Is_Descending = ic.is_descending_key,
    Is_Identity = c.is_identity,
    Is_Computed = c.is_computed
from sys.key_constraints kc
inner join sys.index_columns ic 
    on kc.parent_object_id = ic.object_id and kc.unique_index_id = ic.index_id and kc.type = 'PK'
inner join sys.columns c on ic.object_id = c.object_id and ic.column_id = c.column_id
inner join sys.schemas ss on kc.schema_id = ss.schema_id
order by Schema_Name, Table_Name, Ordinal
Given below is the query for foreign keys.

SQL
Copy Code
select 
    Name = f.name,
    Foreign_Schema = ssf.name,
    Foreign_Table = object_name(f.parent_object_id),
    Foreign_Column = col_name(fc.parent_object_id, fc.parent_column_id),
    Primary_Schema = ssp.name,
    Primary_Table = object_name (f.referenced_object_id),
    Primary_Column = col_name(fc.referenced_object_id, fc.referenced_column_id),
    Ordinal = fc.constraint_column_id
from sys.foreign_keys f
inner join sys.foreign_key_columns fc on f.object_id = fc.constraint_object_id
inner join sys.schemas ssf on f.schema_id = ssf.schema_id
inner join sys.tables st on f.referenced_object_id = st.object_id
inner join sys.schemas ssp on st.schema_id = ssp.schema_id
order by Foreign_Schema, Foreign_Table, Ordinal
Navigation Properties
Navigation properties define the relationship between POCOs and are reflections of foreign keys between database tables. There are 3 types of relationships: One-to-Many, One-to-One, Many-to-Many.

I attached a SQL file Keys.sql to the source code as a solution item. When executed in any database, it will output the database's primary keys, unique keys & foreign keys. For the foreign keys, it will also output their type of relationship and some other useful properties.

Further reading: Entity Relationships, One-to-Many Relationship, One-to-One Relationship, Many-to-Many Relationship.

One-to-Many Relationship
A single foreign key, without any special constraints, is a database implementation of a One-to-Many relationship between two tables.

In this example, the foreign key is from Product.ProductModelID to ProductModel.ProductModelID. ProductModelID is the primary key of ProductModel table. This foreign key defines a One-to-Many relationship between Product and ProductModel. The Product POCO has a singular navigation property to ProductModel and the ProductModel POCO has a collection navigation property to Product.

C#
Copy Code
public class Product
{
    public int ProductID { get; set; } // primary key
    public int? ProductModelID { get; set; } // foreign key

    public virtual ProductModel ProductModel { get; set; }
}

public class ProductModel
{
    public ProductModel()
    {
        this.Products = new HashSet<Product>();
    }

    public int ProductModelID { get; set; } // primary key

    public virtual ICollection<Product> Products { get; set; }
}
One-to-One Relationship
A database implementation of One-to-One relationship is when the primary key of one table is also a foreign key to the primary key of another table. POCO Generator doesn't recognize unique key/unique index database implementation of One-to-One relationship. The SQL Server implementation of One-to-One relationship is technically 1-to-0 or 1.

In this example, the foreign key is from Employee.BusinessEntityID to Person.BusinessEntityID. Person.BusinessEntityID is the primary key of Person and Employee.BusinessEntityID is both the primary key of Employee and a foreign key to the primary key of Person.

C#
Copy Code
public class Employee
{
    public int BusinessEntityID { get; set; } // primary key, foreign key

    public virtual Person Person { get; set; }
}

public class Person
{
    public int BusinessEntityID { get; set; } // primary key

    public virtual Employee Employee { get; set; }
}
Many-to-Many Relationship
Many-to-Many relationship is when two or more entities have multiple references to all the other entities in the relationship. A database implementation of Many-to-Many relationship is a join table, or intuitively a table "in the middle", that is a construct of all the primary keys of all the tables that take part in the relationship. Every primary key in the join table is also a foreign key to the appropriate primary key in the other corresponding table. The tables in the Many-to-Many relationship don't reference each other directly but rather go through the join table, hence the table "in the middle".

If the join table has more columns than the foreign keys to the other primary keys, for example a create time column, then POCO Generator will treat this relationship as One-To-Many relationship between the join table and each of the other tables in the relationship. This will also take effect when the Show Many-to-Many Join Table option is checked.

In this example, a product can be in several warehouses and every warehouse stores many different products. The join table is WarehouseProducts. All the columns of WarehouseProducts are primary keys and each column is a foreign key to Product primary key or Warehouse primary key appropriately.

C#
Shrink ▲   Copy Code
public class Product
{
    public Product()
    {
        this.Warehouses = new HashSet<Warehouse>();
    }

    public int ProductID { get; set; } // primary key

    public virtual ICollection<Warehouse> Warehouses { get; set; }
}

// this poco is not rendered. only for illustration
public class WarehouseProducts
{
    public int ProductID { get; set; } // primary key, foreign key
    public int WarehouseID { get; set; } // primary key, foreign key
}

public class Warehouse
{
    public Warehouse()
    {
        this.Products = new HashSet<Product>();
    }

    public int WarehouseID { get; set; } // primary key

    public virtual ICollection<Product> Products { get; set; }
}
