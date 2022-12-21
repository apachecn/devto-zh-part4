# 你认为功能代码可读吗？

> 原文：<https://dev.to/voanhcuoc/do-you-think-functional-code-readable-4aln>

函数式编程范式的目标是正确和美观。它产生了具有流线型外观和感觉的最优雅的代码。

就个人而言，我可以毫不费力地编写函数式代码，并且愿意编写函数式代码，而不是过程式或面向对象的代码。对我来说更简单:我喜欢使用`map`而不是 for-loop。

但是因为我的大多数同事不懂函数式编程，他们声称我的代码不可读，不可维护。初学者害怕我的代码。

我的代码示例，大量使用了`lodash`:

```
const get_piece = (board, x, y) =>
  _.chain(board.pieces)
    .filter(piece => piece.current_x == x && piece.current_y == y)
    .first()
    .value();

const process_move = (move, board) => {
  // FIXME the move should be specified by absolute coeff of the piece to move
  const to_move_x = board.hole_x + move[0];
  const to_move_y = board.hole_y + move[1];

  const piece = get_piece(board, to_move_x, to_move_y);

  piece.current_x = board.hole_x;
  piece.current_y = board.hole_y;
  board.hole_x = to_move_x;
  board.hole_y = to_move_y;
};

const adjacent_offsets = Object.freeze([
  [0, 1], // up
  [1, 0], // left
  [0, -1], // down
  [-1, 0] // right
]);

// Check if co-effient is in bound of the board
const in_bound = (board, offset) =>
  (board.hole_x + offset[0] >= 0) &&
  (board.hole_y + offset[1] >= 0) &&
  (board.hole_x + offset[0] < board.split_x) &&
  (board.hole_y + offset[1] < board.split_y);

// Return a random element from an array, uniform distribution
const choice = array => array[Math.floor(Math.random() * array.length)];

const random_move = board => {
  const possible_moves = _.chain(adjacent_offsets)
    .filter(offset => in_bound(board, offset))
    .value();
  const move = choice(possible_moves);

  process_move(move, board);
};

const shuffle = (board, times) => {
  _.range(times).map(() => random_move(board));
};

const is_piece_in_position = piece =>
  piece.current_x == piece.result_x && piece.current_y == piece.result_y;

// return true if all pieces in right position
const check_result = async board =>
  _.chain(board.pieces)
    .map(is_piece_in_position)
    .every()
    .value(); 
```

你认为功能代码可读吗？