# A T-SQL Example: Connect 4

Credits to [https://tomaztsql.wordpress.com/2023/11/06/connect-4-game-with-t-sql/](https://tomaztsql.wordpress.com/2023/11/06/connect-4-game-with-t-sql/)

T-SQL Code for the popular game of “Connect 4”, that can be played in Microsoft SQL Server, Azure Data Studio or any other T-SQL editor with support of query execution.

### **About the game**

Connect 4 is a classical board game, consisting of a board of 6 rows and 7 columns and 42 tokens (of two colours, each player with 21). The first player to get four tokens in a row, column or diagonally, wins!

Simple game rules:\
1\. only one token can be added at the time\
2\. each player have their own colour (sign) of tokens\
3\. the game is ended, when the first player connects four tokens in a row, column or diagonally

### T-SQL Procedures

Game consists of four T-SQL procedures that complete the game and can be played. Run the complete _Connect4.sql_ file (in [Github](https://github.com/tomaztk/Connect4_sql_game) repository) in to install the game on your database.

The procedures for the game are:

* Procedure **`dbo.Init`** to create and initialize the game (start procedure)
* Procedure **`dbo.DisplayResults`** to preetify the board and corresponding tokens (helper procedure)
* Procedure **`dbo.CheckWin`** to check for game stop and verify the user inputs (helper procedure)
* Procedure **`dbo.AddToken`** to play the game by both users (main procedure)

### **Generic code for playing the game**

Game is started by initializing the board and players. After the initialization, you can start playing the gaming by alternatively adding tokens from each player. The adding of tokens is done by using the following two procedures:

```sql
EXEC dbo.Init
EXEC dbo.AddToken 1, 5
EXEC dbo.AddToken 2, 4
```

And every turn, you will be prompted with the board result:

[![Gameplay](https://tomaztsql.wordpress.com/wp-content/uploads/2023/11/game_board2.png?w=345)](https://tomaztsql.wordpress.com/wp-content/uploads/2023/11/game_board2.png)

Figure 1: Gameplay

### T-SQL code and logic behind the procedures

**Procedure `dbo.Init`** initialise the game, creates helper tables to store the game steps and the game itself. The complete game is stored in table **dbo.con4t** and board consists of 6 rows and each row is a 7-characters string, that is initially populated with seven zeros (each zero will be later presented as a column).

```sql
CREATE OR ALTER PROCEDURE dbo.Init
AS
BEGIN
  DROP TABLE IF EXISTS  dbo.con4t;
  CREATE TABLE dbo.con4t
  (r int identity(6,-1) not null
  ,board char(7) default('0000000')
  );
  
  DROP TABLE IF exists con4t_steps;
  
  CREATE TABLE dbo.con4t_steps
  (id INT IDENTITY(1,1) NOT NULL
  ,player TINYINT NOT NULL
  ,col TINYINT NOT NULL
  )
  INSERT INTO dbo.con4t_steps(player, col) VALUES (0,0)
  
  -- populate the board   GO 6
    INSERT INTO dbo.con4t (board) SELECT '0000000'
    INSERT INTO dbo.con4t (board) SELECT '0000000'
    INSERT INTO dbo.con4t (board) SELECT '0000000'
    INSERT INTO dbo.con4t (board) SELECT '0000000'
    INSERT INTO dbo.con4t (board) SELECT '0000000'
    INSERT INTO dbo.con4t (board) SELECT '0000000'
    -- SELECT board FROM dbo.con4t
    EXEC dbo.DisplayResults

END;
GO
```

**Procedure `dbo.DisplayResults`** created a better presentation of the board and changes the users to sings as X or O. In addition, it builds a board with pipes and column enumeration.

```sql
-- Showing results Procedure
CREATE OR ALTER PROCEDURE dbo.DisplayResults
AS
BEGIN
  SELECT 'col: | 1 | 2 | 3 | 4 | 5 | 6 | 7 |' as Connect_4_Board
  union all
  SELECT 'row: | '  + substring(REPLACE(REPLACE(REPLACE(board, 1, 'X'), 5, 'O'), 0, ' '),1,1) +
              ' | ' + substring(REPLACE(REPLACE(REPLACE(board, 1, 'X'), 5, 'O'), 0, ' '),2,1) +
              ' | ' + substring(REPLACE(REPLACE(REPLACE(board, 1, 'X'), 5, 'O'), 0, ' '),3,1) +
              ' | ' + substring(REPLACE(REPLACE(REPLACE(board, 1, 'X'), 5, 'O'), 0, ' '),4,1) +
              ' | ' + substring(REPLACE(REPLACE(REPLACE(board, 1, 'X'), 5, 'O'), 0, ' '),5,1) +
              ' | ' + substring(REPLACE(REPLACE(REPLACE(board, 1, 'X'), 5, 'O'), 0, ' '),6,1) +
              ' | ' + substring(REPLACE(REPLACE(REPLACE(board, 1, 'X'), 5, 'O'), 0, ' '),7,1) + ' |'
  FROM [dbo].[con4t]
END;
```

**Procedure `dbo.CheckWin`** is far the most important and interesting one, since it holds the complete logic of checking if the game has ended or not (finding the winner). Internally, it creates a temporary table to store each column from the board in its temporary table column, for easier checks for the winner. Every move, it searches for the winner vertically or horizontally.

Here is a section from the procedure when the check is happening. By far the easiest is horizontal check, vertical on the other hand needs a string aggregation based on the values from the column.

```sql
-- vertical
 SELECT DISTINCT
 CASE
    when col like '%1111' or col like '%1111%' or col like '1111%' then 'Winner 1'
    when col like '%5555' or col like '%5555%' or col like '5555%' then 'Winner 2'
    else NULL end as res
  FROM
  (
      SELECT string_agg(col1, '') as col FROM  dbo.con4temp UNION ALL
      SELECT string_agg(col2, '') as col FROM  dbo.con4temp UNION ALL
      SELECT string_agg(col3, '') as col FROM  dbo.con4temp UNION ALL
      SELECT string_agg(col4, '') as col FROM  dbo.con4temp UNION ALL
      SELECT string_agg(col5, '') as col FROM  dbo.con4temp UNION ALL
      SELECT string_agg(col6, '') as col FROM  dbo.con4temp UNION ALL
      SELECT string_agg(col7, '') as col FROM  dbo.con4temp
  ) as x
-- horizontal
SELECT DISTINCT
CASE
    when board like '%1111' or board like '%1111%' or board like '1111%' then 'Winner 1'
    when board like '%5555' or board like '%5555%' or board like '5555%' then 'Winner 2'
    else NULL end as res
   from dbo.con4t
```

Yet, the beautiful part is finding the solution diagonally. To understand the logic of finding the solutions, here is the key (sorry for my utterly horrible hand drawing):

[![](https://tomaztsql.wordpress.com/wp-content/uploads/2023/11/image-1.png?w=1024)](https://tomaztsql.wordpress.com/wp-content/uploads/2023/11/image-1.png)

Figure 2: Showing diagonals from left to right (red), and from right to left (green)

The simplest and easiest way to find solutions in diagonal is to join the table against itself by adding the row number and concatenating the columns. The excerpt from the procedure (using CTE):

```sql
diag1A AS (
select
 concat(c1.col1,c2.col2,c3.col3, c4.col4, c5.col5, c6.col6, c7.col7) as r1_c1
,concat(c1.col2,c2.col3,c3.col4, c4.col5, c5.col6, c6.col7) as r1_c2
,concat(c1.col3,c2.col4,c3.col5, c4.col6, c5.col7) as r1_c3
,concat(c1.col4,c2.col5,c3.col6, c4.col7) as r1_c4
from con4temp as c1
 join con4temp as c2 on c1.r+1 = c2.r
 join con4temp as c3 on c2.r+1 = c3.r
 join con4temp as c4 on c3.r+1 = c4.r
 join con4temp as c5 on c4.r+1 = c5.r
 join con4temp as c6 on c5.r+1 = c6.r
left join con4temp as c7 on c6.r+1 = c7.r
), diag2A AS (
    select
    concat(c1.col1,c2.col2,c3.col3, c4.col4, c5.col5) as sol
    from con4temp as c1
    left join con4temp as c2 on c1.r+1 = c2.r
    left join con4temp as c3 on c2.r+1 = c3.r
    left join con4temp as c4 on c3.r+1 = c4.r
    left join con4temp as c5 on c4.r+1 = c5.r
    left join con4temp as c6 on c5.r+1 = c6.r
    left join con4temp as c7 on c6.r+1 = c7.r -- not exists!
where
c1.r = 2
), diag3A AS (
    select
    concat(c1.col1,c2.col2,c3.col3, c4.col4, c5.col5, c6.col6, c7.col7) as sol
    from con4temp as c1
    left join con4temp as c2 on c1.r+1 = c2.r
    left join con4temp as c3 on c2.r+1 = c3.r
    left join con4temp as c4 on c3.r+1 = c4.r
    left join con4temp as c5 on c4.r+1 = c5.r
    left join con4temp as c6 on c5.r+1 = c6.r
    left join con4temp as c7 on c6.r+1 = c7.r -- not exists!
    where
    c1.r = 3
)
```

We still need to check the solutions in the second and third row, hence additional where clauses.

**Procedure `dbo.AddToken`** is simple and straightforward wrapper for game continuation and game play. It has two input parameters (player and column where token will be placed) and it returns the result (from `dbo.CheckWin` procedure) if the game has ended, as well as displays board (from `dbo.DisplayResults` procedure). In addition, it can also serve some error messages, if inputs are wrong!

```sql
-- Add Token Procedure
CREATE OR ALTER PROCEDURE dbo.AddToken(
     @user tinyint -- 1, 2 ; player 1 = 1; player 2 = 5
    ,@col tinyint -- 1,2,3,4,5,6,7
) AS
BEGIN
    ----- ERROR coding
    ----- ERROR coding
    -- check for correct column input
    IF @col NOT IN (1,2,3,4,5,6,7)
    BEGIN
        SELECT 'Invalid Column number. Column number should be between 1 and 7.' AS Error_Message;
        -- show board
        EXEC dbo.DisplayResults
        RETURN; -- Exit the procedure
    END
  -- check for correct user input
    IF @user NOT IN (1,2)
    BEGIN
        SELECT 'Invalid player number. Players must be 1 (X) or 2 (O).' AS Error_Message;
        -- show board
        EXEC dbo.DisplayResults
        RETURN; -- Exit the procedure
    END
        -- check for column overflow!
    DECLARE @nof_tokens INT = (SELECT ISNULL(COUNT(col),0) FROM dbo.con4t_steps WHERE col = @col)
    IF (@nof_tokens >= 6)
        BEGIN
          SELECT CONCAT('Column ', @col, ' if kinda full :-)!')  AS Error_Message;
          -- show board
          EXEC dbo.display_results
          RETURN; -- Exit the procedure
      END
  -- check for correct order
    DECLARE @max_step_ID INT = (SELECT ISNULL(MAX(id),0) FROM dbo.con4t_steps)
    DECLARE @last_user TINYINT = (SELECT player FROM dbo.con4t_steps WHERE ID = @max_step_ID)
    IF (@user <> @last_user)
        BEGIN
          INSERT INTO con4t_steps (player, col)
          SELECT @user,@col
        END
    ELSE
        BEGIN
          SELECT CONCAT('Player ', @last_user, ' has just played!')  AS Error_Message;
          -- show board
          EXEC dbo.DisplayResults
          RETURN; -- Exit the procedure
      END
    ----- ERROR coding
    ----- ERROR coding
  DECLARE @token INT
  IF @user = 1  SET @token = 1
  ELSE SET @token = 5
  declare @max_r int = 1
  WHILE @max_r <= 6
  BEGIN
      DECLARE @board char(7)
      SET @board = (SELECT board from con4t where r = @max_r)
      PRINT @board
      IF substring(@board,@col,1) = 0
        BEGIN
          UPDATE con4t
            set board = STUFF(board,@col,1,@token)
            WHERE
              r = @max_r
          set @max_r = 100 -- to exit the while loop immediatelly
        END
      SET @max_r = @max_r + 1
  END
  -- SELECT board from dbo.con4t
  EXEC dbo.DisplayResults
  DECLARE @OutputParameter varchar(100)
  exec dbo.CheckWin @OutputParameter OUTPUT
  IF (@OutputParameter IS NOT NULL)
    BEGIN
      SELECT @OutputParameter
    END
END;
GO
```

As always, the complete code is available on [Github repository](https://github.com/tomaztk/Connect4_sql_game). And feel free to collaborate.

Happy T-SQLing!

If you are interested in more T-SQL games, check my other repositories and blog posts:

* 2048 Game with T-SQL: [BlogPost](https://tomaztsql.wordpress.com/2021/11/29/2048-game-with-t-sql/) and Github [repo](https://github.com/tomaztk/2048_sql_game)
* Wordle with T-SQL: [SQL Server Central Post](https://www.sqlservercentral.com/articles/playing-popular-game-of-wordle-using-t-sql-2) and Github [repo](https://github.com/tomaztk/tsqlwordle)
* Tower of Hanoi with T-SQL: [Blogpost](https://tomaztsql.wordpress.com/2021/12/28/tower-of-hanoi-game-with-t-sql/) and Github [repo](https://github.com/tomaztk/Tower_of_Hanoi_sql_game)
* SQL session on many T-SQL games: Github [repo](https://github.com/tomaztk/Games-with-TSQL)
