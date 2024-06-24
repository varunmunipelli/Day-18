# Difference btw JSON Path and Auto
- `Path` gives a nested neat structure



# Functions in SQL
~~~SQL
-- Functions
Select * from Movies;
Drop Function dbo.CalculateAge;

Create Function CalculateAge(@ReleaseDate Int)
Returns Int
As
Begin	
	Return Year(Getdate()) - @ReleaseDate;
End;

Select *, dbo.CalculateAge(Ryear) from Movies;

--Function Tasks
--Top 3 movies
Select Top(3) *, dbo.CalculateAge(Ryear) as three from Movies
Order by three desc

-- Top doesn't work with OFFSET
Select *, dbo.CalculateAge(Ryear) as three 
from Movies
Order by three
Offset 3 rows;
~~~

## Types of Functions
## - `Scalar Function`
~~~SQL
Go
Create Function dbo.CalculateAge(@ReleaseDate Int)
Returns Int
As
Begin
  Return Year(GetDate()) - @ReleaseDate;	
End
Go 
Select dbo.CalculateAge(2000);
~~~

## - `Table Fnctions` :- it is again into two types
## 1. Inline table valued Function (`ITVF`)
~~~SQL
Go
Create Function dbo.GetMovieByGenre(@Genre nvarchar(30))
returns table
As
Return(
Select * from Movies
Where Genre = @Genre
)
Go
~~~

## 2. Multiline table valued Function (`MTVF`)
~~~SQL 
Create Function dbo.GetMoviesAfter2015()
Returns @LatestDecadeMovies Table(Title varchar(100), ReleaseYear Int, Genre varchar(20))
As
Begin
	Insert Into @LatestDecadeMovies
	Select Title, ReleaseYear, Genre  from Movies where ReleaseYear > 2015 
	-- updates 
	-- delete
	Return;
End 
Select * from dbo.GetMoviesAfter2015() 
Select * from dbo.GetMovieByGenre('Action')
Select * from dbo.GetMovieByGenre('Action Comedy')
~~~

# View in SQL
- View follows Copy by Referece(changes in table effect view)
~~~SQL
-- Last decade Movies
Create View vWLastDecadeMovies
AS
Select MovieID, Title, Ryear from Movies
Where Ryear Between 2010 and 2020;

Select * from vWLastDecadeMovies --Virtual Table/Copy by References
~~~
1. Complex Statement - Create View - Easily  
2. Abstraction -Hiding unnecessary code  
3. Secure  

## Example task on View
~~~SQL
Create View RajamouliMovies
As
Select m.MovieID, m.Title, m.Ryear, d.Names from Movies m
INNER JOIN Directors d
ON m.DirectorID = d.DirectorID
Where d.Names = 'S.S.Rajamouli';

Select * from RajamouliMovies;
~~~

# Case Statements in SQL
~~~SQL
Select *, 
Case
	When ReleaseYear >= 2020 Then 'Latest'
	When ReleaseYear >=2010 Then 'Old'
	Else 'Classic'
	End as Category	
from Movies;

Select *,
Case
	When ReleaseYear >=2020 Then 'Latest'
	Else 'Old'
End as Category
From Movies;
~~~

# Store Procedures
~~~SQL
-- IF
Declare @OrderAmount Decimal(10, 2) = 1500.00
If @OrderAmount > 1000
Begin
	Print 'Applying 10% discount'
End
Else
Begin
	Print 'No Discount'
End
-- WHILE
Declare @Counter Int = 10
While @Counter > 0
Begin
	Print @Counter
	Set @Counter = @Counter - 1
End

-- New Store Procedure
Create Procedure spGetMoviesByGenre
	@Genre nvarchar(20)
As
Begin
Select * from Movies
	Where Genre = 'Action'
End

Execute spGetMoviesByGenre @Genre = 'Action'
~~~