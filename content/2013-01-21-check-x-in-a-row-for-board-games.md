---
layout: post
title: Check x-in-a-row for board games
author: Martin Thoma
date: 2013-01-21 21:59:28.000000000 +01:00
category: Code
tags: Programming, short-circuit evaluation
featured_image: 2013/01/queens-moves.png
---
In board games, you have quite often the situation that you want to check something in different directions. Most of the time, the implementation I see for situations like this is very redundant and prone to off-by-one errors. Some simple ideas can improve the quality of codes (code that is easier to understand and less <abbr title="lines of code">loc</abbr>) and reduce the probability of tiny mistakes.

<ul class="gallery mw-gallery-traditional">
   <li class="gallerybox" style="width: 155px">
      <div style="width: 155px">
         <div class="thumb" style="width: 150px;">
            <div style="margin:21px auto;height: 113px;line-height: 150px;">
               <a href="../images/2013/01/tic-tac-toe.png" class="image">
                  <img src="../images/2013/01/tic-tac-toe.png" alt="Tic Tac Toe" style="max-width: 120px; max-height: 120px;">
               </a>
            </div>
         </div>
         <div class="gallerytext">Tic Tac Toe</div>
      </div>
   </li>
   <li class="gallerybox" style="width: 155px">
      <div style="width: 155px">
         <div class="thumb" style="width: 150px;">
            <div style="margin:21px auto;height: 113px;line-height: 150px;">
               <a href="../images/2013/01/battleships.png" class="image">
                  <img src="../images/2013/01/battleships.png" alt="Battleships" style="max-width: 120px; max-height: 120px;">
               </a>
            </div>
         </div>
         <div class="gallerytext">Battleships</div>
      </div>
   </li>
   <li class="gallerybox" style="width: 155px">
      <div style="width: 155px">
         <div class="thumb" style="width: 150px;">
            <div style="margin:21px auto;height: 113px;line-height: 150px;">
               <a href="../images/2013/01/queens-moves.png" class="image">
                  <img src="../images/2013/01/queens-moves.png" alt="Moves of the queen in chess" style="max-width: 120px; max-height: 120px;">
               </a>
            </div>
         </div>
         <div class="gallerytext">Moves of the queen in chess</div>
      </div>
   </li>
</ul>

<h2>isOnBoard(int x, int y)</h2>
You should create a method that checks if a coordinate is on your board. This can be as simple as this:

```java
public boolean isOnBoard(int x, int y) {
    return 0 <= x && x < width && 0 <= y && y < height;
}
```

<h2>Diagonal, horizontal and vertical</h2>
You can create a method like this:

```java
/**
 * This method checks XYZ and does XYZ.
 *
 * @param player the current player
 * @param xDir -1 if you want to go to the left, 0 if you don't want
 *            to move in x-direction and 1 if you want to go to
 *            the right
 * @param yDir -1 if you want to go to the bottom, 0 if you don't
 *            want to move in y-direction and 1 if you want to go
 *            to the top
 */
private void myBoardAction(Player player, int xDir, int yDir) {
    for (int x = 0; x < board.width; x++) {
        for (int y = 0; y < board.height; y++) {
            for (int c = 0; c < SOME_CONSTANT; c++) {
                if (board.isOnBoard(x + c * xDir, y + c * yDir)
                   && board.checkXYZ(x + c * xDir, y + c * yDir)) {
                    doXYZ();
                }
            }
        }
    }
}
```

What's so special about it? Well, note how the `xDir` and `yDir` parameters change the behavior of the method. If you want to move only to the right, you will call `myBoardAction(player, 1, 0)`. If you want to go to the top left, you will call `myBoardAction(player, -1, 1)`. Of course, you can't simply take this piece of code and only change `doXYZ()` and `checkXYZ`. You will have to change the starting and and position and maybe add a break. But this thought can be applied to board games quite nice.

Please also note that I go from <code>(0|0)</code> to <code>(board.width|board.height)</code> and even add in the inner loop something. So some calls will be out of bound. But because of <a href="http://en.wikipedia.org/wiki/Short-circuit_evaluation">short-circuit evaluation</a> this works. I don't bother about ends, I simply include the critical parts. Most of the time, it is not much work to check if the call is within the boundary, but finding (and fixing) a bug is much work. Yes, I know, this is more efficient if you use the correct boundaries. But it's only a constant in time difference. And I guess this constant is very small for most games.

Ah, and if you want to check a condition for all diagonals, horizontals and verticals the hole board, you can call it like this:

```java
myBoardAction(player, 1, 1); // top right
myBoardAction(player,-1, 1); // top left
myBoardAction(player, 1, 0); // vertical
myBoardAction(player, 0, 1); // horizontal
```

This is enough. You don't need more, as you go through the whole board.
No need to write redundant code ☺
