

The following is the writeup I included with the assignment (September 2015):

The program is written in Python 2.7. The only non-base Python package used in the program is NumPy, which can be found here: http://www.scipy.org/scipylib/download.html. The executable file for the program is Main.py, which can simply be run from a terminal using the command “python Main.py”, assuming that the operating system is Unix-based. Once the program opens up, a banner should appear in the terminal window. First, it will ask you whether this is a test or not. Valid responses include “y”, “yes”, “Yes”, “n”, “no”, “No”, etc. Case-sensitivity is accounted for by using the built-in string method upper(). Next, the program will ask you to input the maximum number of moves the game can go until. This ensures that the game will result in a draw if the number of turns exceeds the given limit. If test mode was originally selected, then the program will read lines from testCase.txt and parse them to create a separate game for each line read. In testing mode, the program will play its own player X and player Y by itself, printing the moves as they occur into the terminal window as well as to a file named gameResult.txt. The parsing function for the test cases was written so that the testCase.txt file can have comments written into it (using the # symbol) and have empty lines, which will not interfere with the normal functioning of the program. However, the syntax of the test cases must adhere to the syntax as used in the assignment test case examples. For instance, x.K(1,4) will be understood as player X’s king at file 1, rank 4. Although the X and K are not case sensitive, the parsing function is expecting a dot between the letters to let it know that the first letter indicates the player and owner of the piece, while the second letter represents the actual piece itself.

In competition mode, the program will ask you whether it should be player X or player Y. Input for this query should be simply an X or a Y. Again, the input is case-insensitive since raw_input() is called with upper() to remove this issue. Then the program will ask the user to input the coordinates for player X’s king, player X’s rook, and player Y’s king. If coordinates are repeated for any of the pieces, or if the coordinates result in a game that would have been illegal (i.e. starting in check or checkmate), the program will notify you and ask you to re-enter the starting points for the pieces. Once the initial positions have been set, the game will begin. During competition mode, the program will make a move and then query the user for input. The input syntax is slightly different in the middle of the game as it requires the user to input the first letter of the piece to be moved, e.g. K(3,5) would move the user’s king to file 3 rank 5. This is to ensure that there is no ambiguity for which piece is moved, particularly for when user input is controlling player X.
The algorithm used by the program’s players is alpha-beta pruning mini-max search. The implementation found in the game is an adaptation from a Python implementation found on UC Berkeley’s website: http://aima.cs.berkeley.edu/python/games.html. One important adjustment I made was to ensure random selection in the event of multiple children with a maximum heuristic value. To do this, I created a list of tuples that included the child states and their corresponding heuristic values, sorted in non-increasing order by heuristic value. Then, I used a for loop to iterate over the sorted list, appending each state to a list called winners until the heuristic value changed. Subsequently, the function choice() from Python’s random module was used to randomly select a child state from the list, if necessary. Additionally, I made another adjustment to help lure player X away from situations where a repeating cycle of states occurs. I did this by having the alpha-beta algorithm treat states that have an immediate repeating series of ancestors between four and eight ancestors long like a leaf. This way, those states get directly evaluated by the heuristic function, which penalizes states for having cycle histories. However, the algorithm will only do this when it is player X searching through states since the algorithm is implemented as a method for the Player class.

For the heuristic functions, I initially based player X’s heuristic off of the mop-up evaluation of CHESS 4.5, as indicated in the following chess programming wiki: https://chessprogramming.wikispaces.com/Mop-up+evaluation. After testing different cases, I continued to adjust the heuristic until I started seeing more optimal decisions being made. In addition, I added a bonus and penalty feature. Bonus and penalties are given only under certain circumstances based on the condition statements controlling their flow. For player X’s heuristic, the value receives a bonus for checkmate states, which is weighted based on how far it is from the current node. Similarly, the heuristic implements a penalty for stalemate states, also weighted by distance from the current game state. This way, player X is guided towards checkmate positions and away from states that lead to a draw. For player Y’s heuristic, the central idea is to deduct points the further the bare king is from the center. This is due to the fact that checkmate is much easier to acquire when the king is cornered, staying near the middle of the board is the bare king’s best chance of pulling through a draw. However, a stalemate will most likely occur when the bare king is cornered as well, which is one of the most advantageous outcome for the bare king since it terminates the game early as a draw. Since it may be very easy for a game that is seemingly moving toward a stalemate outcome to result in checkmate, player Y’s heuristic does not advocate stalemates. The other optimal outcome besides dragging out a draw is for player Y’s king to take player X’s rook. Although this situation is unlikely to occur since it would require player X to make a very poor move, leaving it exposed to a diagonal attack from the bare king, it is a possibility. In order to provoke this possibility against another opponent player X, I have given player Y’s heuristic a bonus for states that allow player X’s rook to get taken out. This would lead the game to draw due to insufficient materials, which is the best possible outcome for player Y.
