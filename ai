import random
import math

BLACK = 1
WHITE = 2
EMPTY = 0

board = [
    [0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0],
    [0, 0, 1, 2, 0, 0],
    [0, 0, 2, 1, 0, 0],
    [0, 0, 0, 0, 0, 0],
    [0, 0, 0, 0, 0, 0],
]

def can_place_x_y(board, stone, x, y):
    if board[y][x] != 0:
        return False  # 既に石がある場合は置けない

    opponent = 3 - stone  # 相手の石 (1なら2、2なら1)
    directions = [(-1, -1), (-1, 0), (-1, 1), (0, -1), (0, 1), (1, -1), (1, 0), (1, 1)]

    for dx, dy in directions:
        nx, ny = x + dx, y + dy
        found_opponent = False

        while 0 <= nx < len(board[0]) and 0 <= ny < len(board) and board[ny][nx] == opponent:
            nx += dx
            ny += dy
            found_opponent = True

        if found_opponent and 0 <= nx < len(board[0]) and 0 <= ny < len(board) and board[ny][nx] == stone:
            return True  # 石を置ける条件を満たす

    return False

def can_place(board, stone):
    for y in range(len(board)):
        for x in range(len(board[0])):
            if can_place_x_y(board, stone, x, y):
                return True
    return False

def random_place(board, stone):
    while True:
        x = random.randint(0, len(board[0]) - 1)
        y = random.randint(0, len(board) - 1)
        if can_place_x_y(board, stone, x, y):
            return x, y

class Node:
    def __init__(self, board, parent=None, player=BLACK):
        self.board = board
        self.parent = parent
        self.player = player  # プレイヤー: BLACKかWHITE
        self.visits = 0  # 訪問回数
        self.wins = 0  # 勝利回数
        self.children = []  # 子ノード
        self.untried_moves = self.get_all_possible_moves(board, player)

    def get_all_possible_moves(self, board, player):
        moves = []
        for y in range(len(board)):
            for x in range(len(board[0])):
                if can_place_x_y(board, player, x, y):
                    moves.append((x, y))
        return moves

    def get_child_node(self, move):
        new_board = [row.copy() for row in self.board]
        x, y = move
        new_board[y][x] = self.player
        return Node(new_board, parent=self, player=3 - self.player)  # 相手に交代

class MCTS:
    def __init__(self, simulations=1000):
        self.simulations = simulations

    def select(self, node):
        while len(node.untried_moves) == 0 and len(node.children) > 0:
            node = self.best_child(node)
        return node

    def best_child(self, node):
        best_score = -math.inf
        best_node = None
        for child in node.children:
            score = child.wins / (child.visits + 1e-6) + math.sqrt(2 * math.log(node.visits + 1) / (child.visits + 1e-6))
            if score > best_score:
                best_score = score
                best_node = child
        return best_node

    def expand(self, node):
        if len(node.untried_moves) == 0:
            return node
        move = node.untried_moves.pop(random.randint(0, len(node.untried_moves) - 1))
        child_node = node.get_child_node(move)
        node.children.append(child_node)
        return child_node

    def simulate(self, node):
        board = [row.copy() for row in node.board]
        player = node.player
        while can_place(board, player):
            x, y = random_place(board, player)
            board[y][x] = player
            player = 3 - player
        return self.evaluate(board)

    def evaluate(self, board):
        # 簡単な評価関数: 石の数で勝敗を決める
        black_count = sum(row.count(BLACK) for row in board)
        white_count = sum(row.count(WHITE) for row in board)
        return black_count - white_count

    def backpropagate(self, node, result):
        while node is not None:
            node.visits += 1
            node.wins += result
            node = node.parent

    def run(self, board, player):
        root = Node(board, player=player)
        for _ in range(self.simulations):
            node = self.select(root)
            if len(node.untried_moves) > 0:
                node = self.expand(node)
            result = self.simulate(node)
            self.backpropagate(node, result)

        # 最も訪問回数が多い子ノードを選択
        best_child = self.best_child(root)
        return best_child.board, best_child

class KonekoAI:
    def __init__(self, simulations=1000):
        self.mcts = MCTS(simulations)

    def face(self):
        return "😸"  # ここを猫の顔にしました

    def place(self, board, stone):
        _, best_move_node = self.mcts.run(board, stone)
        for y in range(len(board)):
            for x in range(len(board[0])):
                if best_move_node.board[y][x] != board[y][x]:
                    return x, y

def play_othello(ai):
    current_player = BLACK
    while True:
        if not can_place(board, current_player):
            if not can_place(board, 3 - current_player):
                break
            else:
                current_player = 3 - current_player
                continue
        
        x, y = ai.place(board, current_player)
        board[y][x] = current_player
        print(f"Player {current_player} places at ({x}, {y})")
        current_player = 3 - current_player  # プレイヤー交代

    print("Game over")
    print(board)

# 実行
play_othello(KonekoAI())
