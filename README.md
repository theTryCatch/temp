CREATE PROCEDURE ExecuteForEachValue
    @CommaSeparatedValues VARCHAR(MAX),
    @AnotherParam INT
AS
BEGIN
    -- Declare the variables
    DECLARE @Value NVARCHAR(MAX);
    DECLARE @SplitValues TABLE (Value NVARCHAR(MAX));

    -- Use STRING_SPLIT function to split the input string and populate the table variable
    INSERT INTO @SplitValues (Value)
    SELECT value
    FROM STRING_SPLIT(@CommaSeparatedValues, ',')
    WHERE RTRIM(LTRIM(value)) <> ''; -- Filter out empty strings

    -- Declare a cursor to iterate through the values
    DECLARE cur CURSOR LOCAL FOR
        SELECT Value
        FROM @SplitValues;

    -- Open the cursor
    OPEN cur;

    -- Fetch the first row
    FETCH NEXT FROM cur INTO @Value;

    -- Loop through each value
    WHILE @@FETCH_STATUS = 0
    BEGIN
        -- Execute the stored procedure with the current value and another parameter
        EXEC YourStoredProcedure @Value, @AnotherParam;

        -- Fetch the next row
        FETCH NEXT FROM cur INTO @Value;
    END

    -- Close and deallocate the cursor
    CLOSE cur;
    DEALLOCATE cur;
END;
