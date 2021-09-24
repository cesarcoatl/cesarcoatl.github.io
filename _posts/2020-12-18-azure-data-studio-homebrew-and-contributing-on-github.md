---
layout: post
title: Azure Data Studio, Homebrew, and contributing on GitHub
tags:
- azure data studio
- github
- homebrew
date: 2020-12-18 13:27 -0800
---
Let's start with GitHub.

It’s been said many times, it may daunt to contribute to an Open Source Project. Maybe you are not sure where to start, or how to even approach such a thing.

I am all too aware of this, but I also know how one’s input does matter. If you're interested, GitHub has a [detailed guide](https://opensource.guide/){:target="_blank"} about contributing to Open Source.

But what does that have to do with [Azure Data Studio](https://github.com/microsoft/azuredatastudio){:target="_blank"}, and [Homebrew](https://brew.sh/){:target="_blank"}?

Well, I bumped into Azure Data Studio when I started using my personal computer, a MacBook Air, and Microsoft SQL Server on a Docker container for some personal projects, although I also use it at work, such that it is the tool I recommend to my teammates given its low footprint, in comparison, and I find myself running Microsoft SQL Server Management Studio less and less.

Very recently I was talking with a colleague about SQL query formatting, and some useful extensions I've used. And that reminded me about a code snippet pre-bundled with Azure Data Studio that caught my attention; the `sqlCreateStoredProc` snippet.

Why that snippet? The main reason is because the body of the stored procedure is not inside a `BEGIN` and `END` block. So when you format the query by selecting `Format Document` from the context menu, the query gets formatted in a _weird_ way, and readability seems to go out the window.

So there were a couple of solutions.

**Option 1**. DIY, or fix-it-yourself. The file to modify is called `mssql.json` found at `/Applications/Azure\ Data\ Studio.app/Contents/Resources/app/extensions/mssql/snippets` on macOS, or `C:\Program Files\Azure Data Studio\resources\app\extensions\mssql\snippets` on Windows.

And it looks like this.

Lines 175-178:

```json
   "AS",
   "\t-- body of the stored procedure",
   "\tSELECT @param1, @param2",
   "GO",
```

My solution is to surround those lines with `BEGIN` and `END`, like this:

Lines 175-180:

```json
   "AS",
   "BEGIN",
   "\t-- body of the stored procedure",
   "\tSELECT @param1, @param2",
   "END",
   "GO",
```

The problem with this approach is that every time Azure Data Studio gets updated, the `mssql.json` file is replaced and must be re-edited.

**Option 2**. Create your own snippet.

**Option 3**. Report this on [GitHub](https://github.com/microsoft/azuredatastudio/issues/13783){:target="_blank"}, and achieve a more permanent solution.

Of course I've done all three, so coming soon the new snippet will be published on the stable release, or you can get it now by running Azure Data Studio - Insiders.

Which is a perfect segway to the last portion of this post; Homebrew.

I've used Homebrew in the past, but it has not been until recently that I've come to fully embrace it. So much that I've even become a Sponsor on GitHub as a "thank you" to all the maintainers.

And this week I've had my first PR approved for a `cask`. After doing my homework, going through their [guidelines](https://github.com/Homebrew/homebrew-cask-versions/blob/master/CONTRIBUTING.md){:target="_blank"}, and asking [a question](https://github.com/Homebrew/discussions/discussions/273){:target="_blank"} or [two](https://github.com/Homebrew/discussions/discussions/301){:target="_blank"}, I finally got the courage to create the cask for [Azure Data Studio - Insiders](https://github.com/Homebrew/homebrew-cask-versions/pull/10104){:target="_blank"}, and [updating it](https://github.com/Homebrew/homebrew-cask-versions/pull/10113){:target="_blank"}, too.

So, starting now you can run the following command:

```bash
brew install azure-data-studio-insiders
```

Azure Data Studio - Insiders will be installed on your machine, and, even better, when you choose the `sqlCreateStoredProc` snippet you'll get the following template:

```sql
-- Create a new stored procedure called 'StoredProcedureName' in schema 'dbo'
-- Drop the stored procedure if it already exists
IF EXISTS (
SELECT *
    FROM INFORMATION_SCHEMA.ROUTINES
WHERE SPECIFIC_SCHEMA = N'dbo'
    AND SPECIFIC_NAME = N'StoredProcedureName'
    AND ROUTINE_TYPE = N'PROCEDURE'
)
DROP PROCEDURE dbo.StoredProcedureName
GO
-- Create the stored procedure in the specified schema
CREATE PROCEDURE dbo.StoredProcedureName
    @param1 /*parameter name*/ int /*datatype_for_param1*/ = 0, /*default_value_for_param1*/
    @param2 /*parameter name*/ int /*datatype_for_param1*/ = 0 /*default_value_for_param2*/
-- add more stored procedure parameters here
AS
BEGIN
    -- body of the stored procedure
    SELECT @param1, @param2
END
GO
-- example to execute the stored procedure we just created
EXECUTE dbo.StoredProcedureName 1 /*value_for_param1*/, 2 /*value_for_param2*/
GO
```

Will you looke at that? A `BEGIN` and an `END`!

And with all this, the moral of the story is: Do your homework, and contribute.

Happy coding!
