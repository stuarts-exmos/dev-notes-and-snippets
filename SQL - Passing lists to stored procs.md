This examples assumes you want o pass a list of people objects to the db stored proc. A person contains a name (string) and an age (int).

First you need to create the type in the db to represet a Person.

```sql
create type [dbo].[People] as table
(
	Name varchar(50),
	Age int
)
```

You pass it to the stored proc like this:

```sql
create procedure [dbo].[addPeople]
	@People PeopleList readonly
as
```
	
## C# Code

A class to represent our Person object

```c#
public class Person
{
	public string Name { get; }
	public int Age { get; }
	public Person(string name, int age)
	{
		Name = name;
		Age = age;
	}
}
```

Now you need to populate the table parameter, which is of type SqlDbType.Structured:

```c#
public void InsertPeople(List<Person> people)
{
	using (var sqlConnection = new SqlConnection(ConnectionString))
	{
		sqlConnection.Open();
		using(var sqlCommand = new SqlCommand("[dbo].[addPeople]"))
		{
			sqlCommand.Connection = sqlConnection;
			sqlCommand.CommandType = CommandType.StoredProcedure;
			using (var table = new DataTable())
			{
				table.Columns.Add("Name", typeof(string));
				table.Columns.Add("Age", typeof(int));
				
				foreach(var person in people)
					table.Rows.Add(person.Name, person.Age);
					
				var parameters = new SqlParameter("@People", SqlDbType.Structured)
										{
											TypeName = "dbo.People",
											Value = table
										};
				sqlCommand.Parameters.Add(parameters);
				sqlCommand.ExecuteNonQuery();
			}
		}
	}
}
```

Obviously you want error handling etc.
