 ________  _______   ________  ________          _____ ______   _______           ________ ___  ___       _______      
|\   __  \|\  ___ \ |\   __  \|\   ___ \        |\   _ \  _   \|\  ___ \         |\  _____\\  \|\  \     |\  ___ \     
\ \  \|\  \ \   __/|\ \  \|\  \ \  \_|\ \       \ \  \\\__\ \  \ \   __/|        \ \  \__/\ \  \ \  \    \ \   __/|    
 \ \   _  _\ \  \_|/_\ \   __  \ \  \ \\ \       \ \  \\|__| \  \ \  \_|/__       \ \   __\\ \  \ \  \    \ \  \_|/__  
  \ \  \\  \\ \  \_|\ \ \  \ \  \ \  \_\\ \       \ \  \    \ \  \ \  \_|\ \       \ \  \_| \ \  \ \  \____\ \  \_|\ \ 
   \ \__\\ _\\ \_______\ \__\ \__\ \_______\       \ \__\    \ \__\ \_______\       \ \__\   \ \__\ \_______\ \_______\
    \|__|\|__|\|_______|\|__|\|__|\|_______|        \|__|     \|__|\|_______|        \|__|    \|__|\|_______|\|_______|
------------------------------------------------------------------------------------------------------------------------
Initial Approach:

I created an "is_valid" function at the start, as I decided this was much necessary for entering any numbers into squares.
"is_valid" would check the rows, columns and squares for the position that the cell was in with the confirmed values that
already existed (at this point the idea of holding possibilities for each cell was a bit redundant as I thought I could
calculate this on the fly). Then, it was necessary to find the next cell to change, as running through the entire 2D array
would be a repetitive task when looking for changing cells, so this function was implemented too. Hence the last thing that
needed to be implemented was a "solve" function. This code did not make it to the final version of the code so it was taken
out. It is however quoted below:

# def solve_sudoku(board):
#     position = find_next_empty_cell(board)
#     if position == -1:
#         return True
#     for i in range(1, 10):
#         if is_valid(i, position, board):
#             board[position[0], position[1]] = i
#             if solve_sudoku(board):
#                 return True
#             board[position[0], position[1]] = 0

**Figure 1: Old version of recursively solving the sudoku**

The function finds the next cell, iterates through the numbers 1 - 9 and sees if they fit, and if it does then it performs 
the same function on the new board. If the new sudoku breaks, then the cell is reset and the function calls itself on the
previous cell that was processed.

After implementing a checking algorithm that checks if the sum of all digits in the sudoku is 405 (because 1+2+3+4+5+6+7+8+9
=45, and 45 x 9 = 405, so a valid sudoku would satisfy this property). Another function that completely kills the board after
checking this was also created to detect invalid sudokus.

After doing all of this, the code was able to solve all puzzles up to hard with no difficulty and under a second. Hard puzzles
could sometimes stretch to 67 seconds, which was not acceptable, so a revamp to the code was necessary. So after some 
consideration, I decided that it would be best to store possibilities and use these so that "is_valid" would not be called as 
often, yet still retain "correctness" of code. This approach would need multiple values to be stored in elements of a 2D array, 
so I naturally thought that a 3D array would be the correct way to proceed. As such following (incomplete) code was written:

# Give all cells all possibilities of numbers
# def give_possibilities(new_board):
#     for y in range(0, 9):
#         for x in range(0, 9):
#             if new_board[0, y, x] == 0:
#                 for i in range(1, 10):
#                     new_board[i - 1, y, x] = i

# def eliminate_possibilities(board, new_board):
# for y in range(0, 9):
#     for x in range(0, 9):
#         if np.shape(new_board[0, y, x]) != 1:
#             for i in range(0, np.shape(new_board[0, y, x])):
#                 if not(is_valid(new_board[i, y, x], [y, x], board)):
#                     np.delete(new_board, i, 0)

# position = efficient_find_cell(board)
# if position == -1:
#     return True
#
# for i in range(0, np.size(board[position[0], position[1]])):
#     if not is_valid(board[position[0], position[1]][i]):
#         np.delete(board[position[0], position[1]], i)
# eliminate_possibilities(board)

# def create_efficient_board(board, new_board):
#     for y in range(0, 9):
#         for x in range(0, 9):
#             new_board[0, y, x] = board[y, x]
#
#     return new_board

# def taskmaster(board):
#     new_board = np.zeros((9, 9, 9))
#     create_efficient_board(board, new_board)
#     give_possibilities(new_board)
# eliminate_possibilities(board)
# efficient_solve(board)

# def efficient_find_cell(new_board):
#     for y in range(0, 9):
#         for x in range(0, 9):
#             if np.size(new_board[y, x]) != 1:
#                 return y, x
#     return -1

# def efficient_solve(board):
#     position = efficient_find_cell(board)
#     if position == -1:
#         return True
#     cell = board[position[0], position[1]]
#     for i in range(0, np.size(cell)):
#         if is_valid(cell[i], position, board):
#             board[position[0], position[1]] = cell[i]
#             if solve_sudoku(board):
#                 return True
#             board[position[0], position[1]] = cell

**Figure 2: Old code for processing a 3D array**

With the deadline close approaching it was imperative that I tried to get this to work. However, after realising that deleting
values in a 3D array was much more confusing than I realised, and I needed a different perspective. Knowing that I'd been 
avoiding the possiblity of storing values in a dictionary, I tried this way too. To my surprise, although this needed more 
work, the level of control over the array was much better, and using string manipulation, storing multiple values in a cell
became much easier. Having to code functions that return rows, columns and squares was extra work, but the payoff for extra
storage meant that the code would run much faster. 

Getting better versions of currently existing functions like "find_empty_cell" and turning them into functions that analysed
which cell has the fewest possiblities to go through, meant that when the depth-first search did occur, a significant amount of
time was saved (since the search tree was "pruned"). 

The implementation of "cut_possiblities" added more speed to the algorithm, as those elements where only one value could be inserted
were fixed easily, rather than iterating through 1-9 range. 

Reflecting upon the solution that I settled with, using a 2D array and using string manipulation rather than a dictionary may 
have been easier, especially since getting the row and column would not be more trouble and there would be no need to convert
the dictionary back to a numpy array. 

To summarise, the final approach became:
1. Creating all possible combinations of coordinates for a sudoku in the following format: A0, A1, A2,...
											   B0, B1, B2,...
											   ..., ..., ....
2. Using these combinations as keys in a dictionary which was created (during creation any 0s encountered are turned into '123456789')
3. Calling efficient_solve on the dictionary calls "taskmaster" first, which calls cut_possibilities until no more possibilities can be
removed. If removing possibilities creates a square with an empty value, we know that the sudoku is then unsolvable, so we can return a 
boolean if that is the case, and a "cut_down" dictionary if it is solvable. 
4. If all cells have been solved by cutting down, return new dictionary, otherwise start with the cell with the lowest number of 
possiblities.
5. For each number in list of possibilities for that cell, recursively solve as in the old code (since the system can keep track of 
branches now).
6. Function keeps calling itself until all possiblities (lengths of possibilites) are 1, at which point sudoku is solved, and we can 
return it.
7. Convert dictionary to 2d array and it's done!
8. Perform a last check, just in case there are erroneous values hidden by doing the sum of all digits in the sudoku board.


Overall, more could have been done in order to further speed up the algorithm to attain times under a second for even the most 
difficult puzzles, but I am pleased with the progress I have made so far and the decisions taken to create this. 
