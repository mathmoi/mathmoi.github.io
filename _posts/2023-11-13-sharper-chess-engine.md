# A sharper chess engine

Over the years, I have developed several chess engines and related applications. Generally, I have used C++ for performance reasons and because I do enjoy the language. C and C++ are the two main languages used for developing chess engines because, if used well, they can extract the most performance from a computer. The C++ Zero-overhead principle is partly responsible for this. However, the performance of C++ (or even C to a lesser extent) comes at the cost of significant complexity. The language is commonly recognized as being difficult to master. The code can easily become hard to read or maintain if one is not careful (and even when one is). I have frequently had to rewrite parts of my engines because they had become too complex. While these disadvantages affect all languages to some extent, C++, due to its complexity, is particularly affected.

C and C++ are not the only languages that can be used to develop a chess engine. In fact, almost all languages can and have been used to write engines. Recently, in an online discussion on the Computer Chess Club, someone demonstrated that it was possible to implement chess algorithms in C# that are only 20% slower than the same algorithm implemented in C++. This has made me want to experiment with C# and maybe implement a new version of m8 (my still private engine) in C#.

## The experiment

Since one has to start somewhere, I began by creating basic types useful in the development of chess-related applications. This led me to my first experiment. Generally, in a chess engine, there are several types that are represented by integers whose values sometimes represent a concept from the domain of chess (a column, a square, a piece, etc.) and sometimes represent several combined values in a single integer (a move, for example).

Generally, these "types" are not enforced by the language's typing system. They are simply integers that are passed as parameters to various functions to transform or decode their content. It is therefore easy to make type errors, like passing a Piece to a method expecting a Move. Also, the use of static methods to create/modify/exploit these values makes the code a bit harder to read.

For example, here is a class that contains methods for creating a byte that represents a square on the chessboard and extracting the column and row index:

```csharp
public static class StaticSquare
{
    public static byte CreateSquare(byte column, byte row) => (byte)(column << 3 + row);
    public static byte GetColumn(byte square) => (byte)(square >> 3);
    public static byte GetRow(byte square) => (byte)(square & 0x07);
}
```

These methods can be used to generate a string representing the square:

```csharp
public string GetF6Classic()
{
    byte sq = StaticSquare.CreateSquare(5, 5);
    string col = ('a' + StaticSquare.GetColumn(sq)).ToString();
    string row = (StaticSquare.GetRow(sq) + 1).ToString();
    return col + row;
}
```

## The solution

The solution I wanted to test was to encapsulate the integer in a struct to transform the various functions into constructors, properties, and methods, making the code using this structure more expressive and easier to write and read. Here is what such a struct could look like:

```csharp
public struct Square
{
    private byte value_;

    [MethodImpl(MethodImplOptions.AggressiveInlining)]
    public Square(byte column, byte row)
    {
        value_ = (byte)(column << 3 + row);            
    }
            
    public Column Column
    {
        [MethodImpl(MethodImplOptions.AggressiveInlining)]
        get { return new Column((byte)(value_ >> 3)); }
    }

    public Row Row
    {
        [MethodImpl(MethodImplOptions.AggressiveInlining)]
        get { return new Row((byte)(value_ & 0x07)); }
    }

    [MethodImpl(MethodImplOptions.AggressiveInlining)]
    public override string ToString() => Column.ToString() + Row.ToString();
}
```

And creating a string becomes simply this:

```csharp
public string GetF6Clean()
{
    Square sq = new(5, 5);
    return sq.ToString();
}
```

## Benchmark

Of course, for this to be viable, the encapsulation of the integer must be at least as performant as the use of a primitive type with static methods. I compared the two methods above using Benchmark.NET.

| Method       | Mean      | Error     | StdDev    | Ratio | Gen0   | Allocated | Alloc Ratio |
|------------- |----------:|----------:|----------:|------:|-------:|----------:|------------:|
| GetF6Classic | 10.415 ns | 0.1426 ns | 0.1191 ns |  1.00 | 0.0019 |      32 B |        1.00 |
| GetF6Clean   |  9.502 ns | 0.0710 ns | 0.0630 ns |  0.91 | 0.0019 |      32 B |        1.00 |

It seems that the encapsulated version is equivalent or even sometimes a bit faster.

## Conclusion

I will therefore go ahead with this technique for all types that are normally represented by integers (File, Rank, Square, PieceType, Piece, Move, Bitboard) while continuing to test the performance to ensure that I am not paying a penalty.
