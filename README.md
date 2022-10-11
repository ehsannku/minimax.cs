# minimax.cs
help you to find best move tiktoktoe
this is a translate from https://gist.github.com/marmo42/0fbc076f43527ee9570be1ff6340fb66
```ruby
public class MinimaxAi
{
	Random random = new Random();

	private double strength;

	/// <summary>
	/// Creates a minimax AI with a given strength
	/// that can play for both sides.
	/// </summary>
	/// <param name="strength">The ai strength from 0 to 1 (0: random; 1: perfect)</param>
	public MinimaxAi(double strength)
	{
		this.strength = strength;
	}

	/// <summary>This method is used to get the next move of the ai.</summary>
	/// <param name="board">The current state of the game as a flat int array (0: empty; 1: player 1; 2: player 2).
	/// 	</param>
	/// <param name="color">The color of the current player (ai) (1: player 1; 2: player 2).
	/// 	</param>
	/// <returns>int  Returns the index of the move inside the array</returns>
	public virtual int doMove(int[] board, int color)
	{
		if (strength != 1 && random.NextDouble() >= strength)
		{
			int emptyCells = 0;
			for (int i = 0; i < board.Length; i++)
			{
				if (board[i] == 0)
				{
					emptyCells++;
				}
			}
			int j = random.Next(emptyCells);
			for (int i_1 = 0; i_1 < board.Length; i_1++)
			{
				if (board[i_1] == 0)
				{
					if (j == 0)
					{
						return i_1;
					}
					else
					{
						j--;
					}
				}
			}
		}
		int bestMove = 0;
		int bestMoveScore = int.MinValue;
		for (int i_2 = 0; i_2 < board.Length; i_2++)
		{
			if (board[i_2] == 0)
			{
				board[i_2] = color;
				int score = min(board, color % 2 + 1, 1, bestMoveScore);
				if (score > bestMoveScore)
				{
					bestMoveScore = score;
					bestMove = i_2;
				}
				board[i_2] = 0;
			}
		}
		return bestMove;
	}

	private int min(int[] board, int color, int depth, int currentMaxScore)
	{
		int winner = checkForWinner(board);
		if (winner != 0)
		{
			int score = 100 - depth;
			return winner == color ? -score : score;
		}
		if (isFull(board))
		{
			return 0;
		}
		int bestMoveScore = int.MaxValue;
		for (int i = 0; i < board.Length; i++)
		{
			if (board[i] == 0)
			{
				board[i] = color;
				int score = max(board, color % 2 + 1, depth + 1, bestMoveScore);
				if (score < bestMoveScore)
				{
					bestMoveScore = score;
				}
				board[i] = 0;
				if (bestMoveScore <= currentMaxScore)
				{
					return bestMoveScore;
				}
			}
		}
		return bestMoveScore;
	}

	private int max(int[] board, int color, int depth, int currentMinScore)
	{
		int winner = checkForWinner(board);
		if (winner != 0)
		{
			int score = 100 - depth;
			return winner == color ? score : -score;
		}
		if (isFull(board))
		{
			return 0;
		}
		int bestMoveScore = int.MinValue;
		for (int i = 0; i < board.Length; i++)
		{
			if (board[i] == 0)
			{
				board[i] = color;
				int score = min(board, color % 2 + 1, depth + 1, bestMoveScore);
				if (score > bestMoveScore)
				{
					bestMoveScore = score;
				}
				board[i] = 0;
				if (bestMoveScore >= currentMinScore)
				{
					return bestMoveScore;
				}
			}
		}
		return bestMoveScore;
	}

	private static int checkForWinner(int[] board)
	{
		int w = checkHorizontally(board);
		if (w != 0)
		{
			return w;
		}
		w = checkVertically(board);
		if (w != 0)
		{
			return w;
		}
		w = checkDiagonally(board, 1);
		if (w != 0)
		{
			return w;
		}
		w = checkDiagonally(board, 2);
		if (w != 0)
		{
			return w;
		}
		return 0;
	}

	private static int checkVertically(int[] board)
	{
		for (int x = 0; x < 3; x++)
		{
			bool b1 = true;
			bool b2 = true;
			for (int y = 0; y < 3; y++)
			{
				if (board[y * 3 + x] != 1)
				{
					b1 = false;
				}
				if (board[y * 3 + x] != 2)
				{
					b2 = false;
				}
			}
			if (b1)
			{
				return 1;
			}
			if (b2)
			{
				return 2;
			}
		}
		return 0;
	}

	private static int checkHorizontally(int[] board)
	{
		for (int y = 0; y < 3; y++)
		{
			bool b1 = true;
			bool b2 = true;
			for (int x = 0; x < 3; x++)
			{
				if (board[y * 3 + x] != 1)
				{
					b1 = false;
				}
				if (board[y * 3 + x] != 2)
				{
					b2 = false;
				}
			}
			if (b1)
			{
				return 1;
			}
			if (b2)
			{
				return 2;
			}
		}
		return 0;
	}

	private static int checkDiagonally(int[] board, int color)
	{
		bool b1 = true;
		bool b2 = true;
		for (int x = 0; x < 3; x++)
		{
			if (board[x * 3 + x] != color)
			{
				b1 = false;
			}
			if (board[(2 - x) * 3 + x] != color)
			{
				b2 = false;
			}
		}
		if (b1 || b2)
		{
			return color;
		}
		return 0;
	}

	private static bool isFull(int[] board)
	{
		for (int i = 0; i < board.Length; i++)
		{
			if (board[i] == 0)
			{
				return false;
			}
		}
		return true;
	}

	public virtual double getStrength()
	{
		return strength;
	}

	public static void Main(string[] args)
	{
		MinimaxAi ai = new MinimaxAi(1);
		int[] board = new int[] { 0, 0, 0, 1, 1, 2, 0, 2, 0 };
		int move = ai.doMove(board, 1);
		System.Console.Out.WriteLine("move: " + move);
	}
}
```
