Pythonic chess

This program is a 2 player chess game.
Our goal was to make a playable chess game written in python. We thought that it would be fun and challenging to experience how a relatively simple game like chess would be to code. We were curios about how we would tackle moves like en passant, castling, pawn-promotion, which, when playing on a real board seem easy, but writing a program would be a whole other way of thinking. Furthermore, we thought it would be fun if we could add a chess engine that suggests moves to perform, so that the program would be a tool for improving your chess abilities.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

User guide

Steps:

1. You will need a code editor to run the program. We used Visual Studio Code and PyCharm, so we recommend them but any editor will work.

2. Download the latest version of python, to ensure the program works properly. You can find it here: https://www.python.org/downloads/

3. Open the unzipped chess folder in your editor.

3. Since we use PyGame to run the game, you will need to install PyGame in your text editor. In the terminal in your editor and write: 'pip install pygame'.

4. Installing Stockfish engine.Download stockfish engine from: https://stockfishchess.org/download/

Install stockfish using the terminal again " pip install stockfish"

Initialize stockfish class in your code: 

"from stockfish import Stockfish

stockfish = Stockfish(path="the path where you download stockfish to"), for example = (path="/Users/zhelyabuzhsky/Work/stockfish/stockfish-9-64")" 

Now stockfish is up and running, we use the line "stockfish.get_best_move()" to get the moves stockfish recommends. 

5. Now you should be able to run the program. A window should open with a chess board.

It works like classic chess. White moves first. If you wish to end the game prematurely press the "Resign" button. If it is your turn and you press resign, you forfeit the game. Press Enter to reset the board, restarting the game. 

On the right side, you will be able to keep track of what pieces have been captured. If you are promoting a pawn, you will be able to pick what piece you would like to promote to in the tab that will open as a pawn reaches the end of the board.

Good luck & have fun!!!

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

In terms of the program's implementation of OOP pillars, in the source code there are two major classes. These classes are called 'Checker' class and 'Drawer' class. The checker class is responsible for checking game pieces' valid moves, by checking their position and the selected piece's moving abilites. It first checks which piece is selected and then calculates the valid moves. The drawer, on the other hand, is resposible for drawing the board and everything to do with visual aspects of the program. It will, for example, draw a indicator of where pieces can move after the valid moves have been checked in the checker class.

For instance, check_king is a method in the checker class that checks valid moves that the king can perform. It checks who's piece it is (white or black's) and then appends the moves it can perform to the moves list.

def check_king(position, color):
        moves_list = []
        castle_moves = Checker.check_castling()
        if color == 'white':
            enemies_list = black_locations
            friends_list = white_locations
        else:
            friends_list = black_locations
            enemies_list = white_locations
        # 8 squares to check for kings, they can go one square any direction
        targets = [(1, 0), (1, 1), (1, -1), (-1, 0), (-1, 1), (-1, -1), (0, 1), (0, -1)]
        for i in range(8):
            target = (position[0] + targets[i][0], position[1] + targets[i][1])
            if target not in friends_list and 0 <= target[0] <= 7 and 0 <= target[1] <= 7:
                moves_list.append(target)
        return moves_list, castle_moves



As an example of the Drawer class, we can look at the indicator, that shows where pieces can move. It first checks whos turn it is to draw their respective colour as the indicator and then draws each move that has been appended to the moves it can perform. 

def draw_valid(moves):
        if turn_step < 2:
            color = 'white'
        else:
            color = 'black'
        for i in range(len(moves)):
            pygame.draw.circle(screen, color, (moves[i][0] * 100 + 50, moves[i][1] * 100 + 50), 5)


An example of polymorphism and inheritance can be seen in the move logger part of the code. This code logs each move that has been made. Inheritance is used when we created the parent class Logger, and the subclasses that inherit from it. Polymorphism in particular is demonstrated in the way that different types of loggers (TextFileLogger) and (ConsoleLogger) can be used interchangeably. Both subclasses override the 'log_move' and 'reset_log' methods of the base class. This allows the user to utilize an object of any of these subclasses without having to worry about which particular logging class is being used.

class Logger:
    def log_move(self, move):
        raise NotImplementedError("Subclasses must implement this method.")

    def reset_log(self):
        raise NotImplementedError("Subclasses must implement this method.")


class TextFileLogger(Logger):
    def __init__(self, file_path):
        self.file_path = file_path

    def log_move(self, move):
        with open(self.file_path, 'a') as file:
            file.write(move + "\n")

    def reset_log(self):
        with open(self.file_path, 'w') as file:
            file.write("")  # Clear existing content


class ConsoleLogger(Logger):
    def log_move(self, move):
        print("Move logged to console:", move)

    def reset_log(self):
        print("Console log reset")

Encapsulation is used through the definition of classes and methods that keep the attributes and the methods that manipulate the data together. For instance, the 'Checker' class encapsulates methods for determining valid chess moves based on piece type and position, and the 'Drawer' class encapsulates all the rendering logic. As an example, the 'Checker' class encapsulates all the methods related to checking the valid moves for different chess piece. This means that the logic for move checking is centralized within the class, and only the class methods are exposed for use by other parts of the program:

class Checker:
    def check_options(pieces, locations, turn):
        global castling_moves
        moves_list = []
        all_moves_list = []
        castling_moves = []
        for i in range((len(pieces))):
            location = locations[i]
            piece = pieces[i]
            if piece == 'pawn':
                moves_list = Checker.check_pawn(location, turn)
            elif piece == 'rook':
                moves_list = Checker.check_rook(location, turn)
            elif piece == 'knight':
                moves_list = Checker.check_knight(location, turn)
            elif piece == 'bishop':
                moves_list = Checker.check_bishop(location, turn)
            elif piece == 'queen':
                moves_list = Checker.check_queen(location, turn)
            elif piece == 'king':
                moves_list, castling_moves = Checker.check_king(location, turn)
            all_moves_list.append(moves_list)
        return all_moves_list


In the code we integrate stockfish to suggest the best move for whoever's turn it is. Using the singleton design pattern for the stockfish engine is beneficial for ensuring that only one instance of stockfish is running. This prevents unnecessary resource consumption. It also prevents inconsistency issues, as several instances of stockfish could lead to inconsistent states where different parts of the program sees different states of the chess engine. It ensures that all parts of the program interact with the same instance and therefore the same state of the chess engine.


class StockfishSingleton:
    _instance = None

    @staticmethod
    def get_instance(path="C:/Users/sino2/Desktop/stockfish/stockfish-windows-x86-64-avx2.exe"):
        if StockfishSingleton._instance is None:
            StockfishSingleton._instance = Stockfish(path)
        return StockfishSingleton._instance


stockfish = StockfishSingleton.get_instance()

Finally, we implemented a decorator for the move logger in order to add specific behaviors of the move logger without modifying the structure of the logger. It makes the move logger more flexible making it easier to modify logging behavior without introducing any bugs or uncertainties:

 def move_logger_decorator(log_func):
    def wrapper(*args, **kwargs):
        move = log_func(*args, **kwargs)
        return move
    return wrapper

@move_logger_decorator
def log_move(start_pos, end_pos):

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Results

One of the challenges faced in the programming was the implementation of abstraction. We felt that, since the program was fairly simple (because chess is a classic game that will never change) we could not find a proper way of implementing abstraction as it would add unnecessary complexity to the code. Abstraction is useful for modifications in the future, however, when there is a certainty in the game never being modified then abstraction was not necessary.
Otherwise, the program works well and the integration with stockfish was somewhat seemless and very comfortable. 

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Conclusion

When the program was finished and testing had been performed, an almost overwhelming sense of accomplishment overcame us. The feeling that we had created something from nothing just by typing in some computer code was extremely exhilarating. This is the first usable program we have ever conducted and the experience was great. During the process there were many annoying and seemingly impossible errors that we had to deal with, but when the solution was eventually found we felt unconquerable. Tackling moves like en passant, castling and pawn promotion had us completely rethink how chess works, as we had to look at it almost mathematically and in terms of a 8x8 grid and its respective positions. This program can be used for fun as well as, practice and honing chess capabilities, by analysing moves and positions with the help of stockfish. The program is the groundwork for our future prospects in programming and will certainly be a great experience. It has given us great insight into the world of programming and sense of the purpose of programming instead of doing unrelated examples of programming aspects.