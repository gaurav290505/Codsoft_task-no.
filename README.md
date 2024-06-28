import math
print("WELCOME TO THE GAME OF TICTACTOE")

class TicTacToe:
    def __init__(self):
        self.board = [' ']*9
        self.current_player = 'X'

    def print_board(self):
        for i in range(0, 9, 3):
            print('|'.join(self.board[i:i+3]))
            if i < 6:
                print("-----")

    def is_winner(self, player):
        win_conditions = [(0, 1, 2), (3, 4, 5), (6, 7, 8),
                          (0, 3, 6), (1, 4, 7), (2, 5, 8),
                          (0, 4, 8), (2, 4, 6)]
        for condition in win_conditions:
            if all(self.board[i] == player for i in condition):
                return True
        return False

    def is_board_full(self):
        return all(cell != ' ' for cell in self.board)

    def get_empty_cells(self):
        return [i for i, cell in enumerate(self.board) if cell == ' ']

    def make_move(self, move, player):
        self.board[move] = player

    def switch_player(self):
        self.current_player = 'O' if self.current_player == 'X' else 'X'

    def minimax(self, depth, player):
        if player == 'X':
            best = [-1, -math.inf]
        else:
            best = [-1, math.inf]

        if depth == 0 or self.is_winner('X') or self.is_winner('O') or self.is_board_full():
            score = 0
            if self.is_winner('X'):
                score = 1
            elif self.is_winner('O'):
                score = -1
            return [-1, score]

        for move in self.get_empty_cells():
            self.make_move(move, player)
            score = self.minimax(depth - 1, 'O' if player == 'X' else 'X')
            self.make_move(move, ' ')
            score[0] = move

            if player == 'X':
                if score[1] > best[1]:
                    best = score
            else:
                if score[1] < best[1]:
                    best = score

        return best

    def play(self):
        while not self.is_winner('X') and not self.is_winner('O') and not self.is_board_full():
            self.print_board()
            if self.current_player == 'X':
                while True:
                    try:
                        move = int(input("It's your turn. Enter your move (0-8): "))
                        if self.board[move] == ' ':
                            break
                        else:
                            print("There move is already placed. Please choose an empty cell.")
                    except ValueError:
                        print("Invalid input. Please enter a number.")
                    except IndexError:
                        print("Invalid input. Please enter a number between 0 and 8.")
                self.make_move(move, self.current_player)
            else:
                move = self.minimax(len(self.get_empty_cells()), self.current_player)[0]
                self.make_move(move, self.current_player)
                print(f"The AI has choosed the move {move}")
            if self.is_winner(self.current_player):
                self.print_board()
                print(f"{self.current_player} Wins!")
                break
            elif self.is_board_full():
                self.print_board()
                print("It's a Draw!")
                break
            self.switch_player()

if __name__ == "__main__":
    game = TicTacToe()
    game.play()
