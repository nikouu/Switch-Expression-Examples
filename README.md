# Switch Expression Examples

Big note: These are switch **expressions** not switch **statements**.

[Original documentation: switch expression (C# reference)](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/switch-expression)

[In depth release notes for the switch expressions feature](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#switch-expressions)

## Example 1
*Via original documentation*

```csharp
public static class SwitchExample
{
    public enum Direction
    {
        Up,
        Down,
        Right,
        Left
    }

    public enum Orientation
    {
        North,
        South,
        East,
        West
    }

    public static Orientation ToOrientation(Direction direction) => direction switch
    {
        Direction.Up    => Orientation.North,
        Direction.Right => Orientation.East,
        Direction.Down  => Orientation.South,
        Direction.Left  => Orientation.West,
        _ => throw new ArgumentOutOfRangeException(nameof(direction), $"Not expected direction value: {direction}"),
    };

    public static void Main()
    {
        var direction = Direction.Right;
        Console.WriteLine($"Map view direction is {direction}");
        Console.WriteLine($"Cardinal orientation is {ToOrientation(direction)}");
        // Output:
        // Map view direction is Right
        // Cardinal orientation is East
    }
}
```
Note:
- The variable that is evaluated by the `switch` is before the keyword
- There is no `case` or `break` 
- `default` is now handled by an underscore `_` via the [discard pattern](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/patterns#discard-pattern)

## Example 2

[Via David Fowler](https://twitter.com/davidfowl/status/1486600711211143168)

```csharp
int GetValue(bool a, bool b) => (a, b) switch
{
	(false, false) => 0,
	(false, true) => 1,
	(true, false) => 2,
	(true, true) => 3
}
```
Note:
- Here we flatten multiple `if` statements into a flat truth table
- This uses the [tuple pattern](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-8#tuple-patterns)

## Example 3
[Via David Fowler](https://twitter.com/davidfowl/status/1495866609130524679)
```csharp
var jwtKeyBytes = builder.Configuration["JwtKey"] switch
{
	null or "" => RandomNumberGenerator.GetBytes(32),
	var value => Convert.FromBase64String(value)
}
```
Note:
- Think of this as a [ternary expression](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/conditional-operator) where in C# we would make use of the conditional operator `?:` or in other words, the short one line `if` statement.
- While this syntax might look a little different (it did for me at first sight) this is the same as Example 1 where there the `direction` variable was being used in the `switch` and here it is `builder.Configuration["JwtKey"]` instead

## Example 4
[Via Demis Bellot](https://twitter.com/demisbellot/status/1237059450566930432) 

```csharp
Dictionary<char, int> charValues = new Dictionary<char, int>{
	['A'] = 1,
	['B'] = 2,
	['C'] = 3,
}

int CharValues(char c) => c switch {
	'A' => 1,
	'B' => 2,
	'C' => 3,
}
```
Note:
- While there are no fancy `switch` things happening, it's the **clarity** that the new expression gives us where we can replace mapping objects with equally readable and allocationless(!) expressions

## Example 5
[Via Andy Gocke](https://twitter.com/andygocke/status/1526653035748896768)
[Another via David Fowler with two parameters](https://twitter.com/davidfowl/status/1656504984140402688)

```csharp
int Sum(Span<int> s) => switch 
{
    [] => 0,
    [var x, .. var xs] => x + Sum(xs)
}
```
Note:
- The tweet comments how it's looking more like F# and off the top of my head reading this for the first time, I had no idea what's happening. 
- Also, [don't do recursion this way](https://twitter.com/andygocke/status/1526999437062438912), it's just neat looking

## Example 6
Via me
```csharp
bool isCorrectTable = (firstColumnHeader, secondColumnHeader) switch
{
    ("Tile", "Original") => true,
    _ => false
};
```
Note:
- This is just to create a variable while using tuples rather than use a Switch Expression as a function 

## Example 7
[Via David Fowler](https://twitter.com/davidfowl/status/1497820187386474496)
```csharp
Dictionary<string, string> Parse(string s) =>
	s.Split(',')
	.Select(s => s.Split('=') switch
	{
		[var k, var v] => (k, v),
		_ (null, null),
	})
	.Where(p => p.k != null)
	.ToDictionary(p => p.k, p => p.v);
```
Note:
- This is designed to take in a string like `a=b,c=d,e=f`
- We get to see a switch expression inside a LINQ `Select()` call

## Example 8
[James Newton-King](https://twitter.com/JamesNK/status/1633658140028964864)
```csharp
return items switch
{
	[var item] => item,
	[] => null,
	[..] => throw new InvalidOperationException("Multiple")
};
```
- Doing `GetSingleOrDefault()` without LINQ

