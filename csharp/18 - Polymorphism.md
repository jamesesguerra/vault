Say we wanted to make a chess game. We could create a `ChessPiece` base class and let `King`, `Pawn`, `Rook` etc. inherit from it. If we wanted to create a method we can inherit from `ChessPiece` that checks whether a move is legal, we could only put in the common rule for what makes a move legal for every chess piece since each piece has rules for determining what's legal or not.

For example, if we wanted to implement this method and inherit it from the `King` class while also trying to extend it with what makes a move legal for a king piece:

```cs
class ChessPiece {
	public bool IsLegalMove(int row, int column) {
		// check if piece isn't moving off the 8x8 board
		// check if piece isn't staying put
	}
}

class King : ChessPiece {
	public bool IsLegalKingMove(int row, int column) {
		if (!IsLegalMove(row, column)) return false; // first do shared check for all chess pieces

		// check if piece isn't moving by more than 1 row or column -> do king specific check
	}
}
```

This poses a problem as you'd have to remember the name of this piece-specific move check for every piece that inherits from `ChessPiece` ie `king.IsLegalKingMove()`, `pawn.IsLegalPawnMove()` and so on. Ideally, you'd like to just have 1 `IsLegalMove` method that you can call on any chess piece and depending on the type of object, it would call that type's specific implementation of the method at runtime. 

This is polymorphism. The `ChessPiece` can support operations such as `IsLegalMove` without knowing until runtime what kind of object is being dealt with and what version of the method will be run.

### Using polymorphism
To allow a method to leverage polymorphism, it must indicate it by placing the `virtual` keyword, giving derived classes permission to replace it. You can also call the base class' version of the method by calling `base.Method()` to reuse logic.

```cs
class ChessPiece {
	public virtual bool IsLegalMove(int row, int column) {...}
}

class King : ChessPiece {
	public override bool IsLegalMove(int row, int column) {
		base.IsLegalMove(row, column);
		// do king specific check here
	}
}
```

### Abstract Methods and Classes
If a base class can't provide any implementation for a method but requires that every derived class have it, it can mark it as `abstract`, specifying the method's signature without providing a body or implementation. And if a class has at least 1 abstract member, it must be marked as an abstract class as well

```cs
abstract class ChessPiece {
	public abstract bool IsLegalMove(int row, int column);
}
```

Although, abstract classes *don't* need any abstract members to be marked as abstract. Marking it with `abstract` also prevents it from being instantiated, which is also something you'd want. Abstract classes can contain abstract, virtual, or non-virtual members.


