Download Link: https://assignmentchef.com/product/solved-tetris-brain
<br>
<a id="user-content-learning-objectives" class="anchor" href="https://github.com/Grimlek/Course-Work/tree/master/Tetris%20Brain#learning-objectives" aria-hidden="true"></a>Learning Objectives

<ul>

 <li>Use a simple interface</li>

 <li>Use new library classes</li>

 <li>Read and interpret library class documentation</li>

 <li>Use if statements and boolean conditions</li>

 <li>Use loop statements</li>

 <li>Write JUnit tests</li>

</ul>

<h3><a id="user-content-overview" class="anchor" href="https://github.com/Grimlek/Course-Work/tree/master/Tetris%20Brain#overview" aria-hidden="true"></a>Overview</h3>

In this assignment, you will be writing the core features of a computerized Tetris player. For us old-timers who grew up before the popularization of video game consoles like Xbox One and PlayStation 4, or even their predecessors, Tetris was one of the coolest things around. Try playing it for 27 hours and see if you don’t agree. If you play Tetris enough, you may begin to have Tetris dreams (Links to an external site.).

If you have never played Tetris before, you should try it. Play Tetris online (Links to an external site.). You can also play it from the jar provided for this assignment on your own computer.

Play is simple. Use the keyboard to move and rotate each piece as it falls. Once a horizontal row is completely filled with blocks, it will be erased to make more room. You continue to play until blocks build up too deeply.

<h3><a id="user-content-the-tetris-architecture" class="anchor" href="https://github.com/Grimlek/Course-Work/tree/master/Tetris%20Brain#the-tetris-architecture" aria-hidden="true"></a>The Tetris Architecture</h3>

This program assignment involves writing the “AI” for a computerized Tetris player using classes from an existing Java implementation of Tetris. The Tetris implementation we are using consists of four classes and one interface, all located in the cs5044.tetris package:

<ul>

 <li>Piece–represents a single Tetris piece</li>

 <li>Board–represents the Tetris game board</li>

 <li>JTetris–implements the GUI for Tetris in a window and handles all the animation</li>

 <li>Brain–an interface that defines the methods that any class must support in order to act as the “Brain” for an automated Tetris player</li>

 <li>Move–represents a single move chosen by a brain These Tetris classes provide a framework for a complete Tetris application. Writing your own “brain” AI code turns out to be surprisingly easy, and fun too! The JTetris GUI allows you to play Tetris yourself, or load in your own brain class to see how it plays on its own. The remainder of the Tetris classes require a bit more experience to implement, so we won’t be doing that in this assignment. At the same time, however, they provide a good example of how one can dividing a rather large and difficult problem, like implementing Tetris, into several classes that cooperate to solve the whole thing, and that can be implemented and tested separately.</li>

</ul>

<h4><a id="user-content-the-brain-interface" class="anchor" href="https://github.com/Grimlek/Course-Work/tree/master/Tetris%20Brain#the-brain-interface" aria-hidden="true"></a>The Brain Interface</h4>

You will be implementing a Tetris brain. All brains must provide a common set of features, that are defined by the Brain interface:

You can call your brain class anything you want, but it must implement the Brain interface, like this:

In Java, an interface is a sort of like a class with no implementation–it contains only declarations of methods and constants, but no code. When you implement an interface, you are promising that your class provides implementations of all the methods declared in the interface.

So all a brain really needs to do is provide one method to choose the “best move”, given the current state of the board and the current piece that is falling. Before we look at brains in more detail, lets look at the core classes that brains have to deal with in order to do their job.

<h4><a id="user-content-the-piece-class" class="anchor" href="https://github.com/Grimlek/Course-Work/tree/master/Tetris%20Brain#the-piece-class" aria-hidden="true"></a>The Piece Class</h4>

There are seven pieces in standard Tetris.

<ol>

 <li>The “T”</li>

 <li>The Square</li>

 <li>The Stick</li>

 <li>The L (Left and Right Isomers)</li>

 <li>The Dog (Left and Right Isomers)</li>

</ol>

Each standard piece is composed of four blocks. The two “L” and “dog” pieces are mirror images of each other, but we’ll just think of them as similar but distinct pieces. A chemist might say that they where “isomers” or more accurately “enantiomers” (Not that I actually know that word–I looked it up to make the handout more impressive).

A piece can be rotated 90° counter-clockwise to yield another piece. Enough rotations get you back to the original piece–for example rotating a dog twice brings you back to the original state. Essentially, each Tetris piece belongs to a family of between one and four distinct rotations. The square has one, the dogs have two, and the L’s have four. For example, here are the four rotations (going counter-clockwise) of the left hand L:

Our abstraction will be that a Piece object represents a single Tetris piece in a single rotation, so the above diagram shows four different piece objects.

Each piece is defined by a number of blocks known as its “body”. The body is represented by the (x, y) coordinates of its blocks, with the origin in the lower-left corner.

<a href="https://i0.wp.com/cloud.githubusercontent.com/assets/6447538/11915567/8747d07a-a671-11e5-84fc-c68eec34276b.gif?ssl=1" target="_blank" rel="noopener"><img decoding="async" alt="Tetris Piece Example" data-recalc-dims="1" data-src="https://i0.wp.com/cloud.githubusercontent.com/assets/6447538/11915567/8747d07a-a671-11e5-84fc-c68eec34276b.gif?w=980&amp;ssl=1" class="lazyload" src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==">

  <noscript>

   <img decoding="async" src="https://i0.wp.com/cloud.githubusercontent.com/assets/6447538/11915567/8747d07a-a671-11e5-84fc-c68eec34276b.gif?w=980&amp;ssl=1" alt="Tetris Piece Example" data-recalc-dims="1">

  </noscript></a>

So the body of this piece is defined by the (x, y) points : (0, 0), (1, 0), (1, 1), (2, 1).

The Piece class and the Board class (below) both measure things in this way–block by block with the origin in the lower-left corner. As a design decision, the Piece and Board classes do not know about pixels–they measure everything block by block. Or put another way, all the knowledge of pixels is isolated in the JTetris class.

Each piece responds to messages like getWidth(), getHeight(), and getBody() that allow the client to see the state of the piece. Fortunately, our brain code won’t need to worry about the individual bodies of particular pieces (if you are an advanced student and are comfortable using arrays, feel free to browse the Piece documentation on-line).

To allow the client to access the various piece rotations that are available, the Piece class provides a nextRotation() method. Starting with any piece, the nextRotation() message returns the “next” piece object that represents the next counter-clockwise rotation of the piece receiving the message. Enough calls to nextRotation() gets the caller back to the original piece.

<h4><a id="user-content-the-board-class" class="anchor" href="https://github.com/Grimlek/Course-Work/tree/master/Tetris%20Brain#the-board-class" aria-hidden="true"></a>The Board Class</h4>

An instance of the Board class stores the state of the 2-dimensional Tetris board. The client uses the place() message to add the blocks of a piece into the board. Once the blocks are in the board, they are not connected to each other as a piece any more; they are just 4 adjacent blocks that will eventually get separated by row-clearing. The two basic methods provided by Board to modify its contents are:

<ul>

 <li>place(piece, x, y)–add a piece into the board with its lower-left corner at the given (x, y) location.</li>

 <li>boolean clearRows()–compact the board downwards by clearing any filled rows (returns true if any were cleared).</li>

</ul>

Board also provides many methods that allow the client to look at a Board’s state.

<ul>

 <li>int getWidth()–how many blocks wide is the board.</li>

 <li>int getHeight()–how many blocks high is the board.</li>

 <li>int getBlocksInRow(y)–the number of filled blocks in the given horizontal row.</li>

 <li>int getColumnHeight(x)–the height the board is filled in the given column (this is 1 + the y value of the highest filled block).</li>

 <li>int getLargestHeight()–the max of the getColumnHeight() values across all columns.</li>

 <li>int rowAfterDrop(piece, x)–the y value where the origin (lower left corner) of the given piece would come to rest if the piece were dropped straight down at the given x.</li>

 <li>boolean hasBlockAt(int x, int y)–returns true if the given location is currently occupied by a block.</li>

</ul>

<h4><a id="user-content-the-move-class" class="anchor" href="https://github.com/Grimlek/Course-Work/tree/master/Tetris%20Brain#the-move-class" aria-hidden="true"></a>The Move Class</h4>

Brains need an easy way to talk about what move they want to make, and that role is filled by the Move class. Each time a new piece appears on the board, the brain is asked what move it would like to make–that is, what final position does the brain want to steer the piece toward? Brains don’t have to worry about the little details of how many times to “press keys”, or actually moving the piece one step at a time. Instead, a brain just says where it wants to place the piece when it finally comes to rest, and the remainder of the program takes care of the other details.

So a Move object represents where a brain wants to place a piece. Conceptually, a Move object holds four related pieces of information: the piece to be placed (which reflects the final rotation, of course), the x and y coordinates on the board where the piece’s origin should land, and a score. The score is really a measure of how costly this move will be, with a higher score reflecting a move that will make the board position worse.

Move objects respond to four accessor messages that let you read the information stored inside:

<ul>

 <li>int x()–get the x-coordinate for the destination.</li>

 <li>int y()–get the y-coordinate for the destination.</li>

 <li>Piece piece()–get this move’s piece at its final desired rotation.</li>

 <li>double score()–get the score that has been assigned for this move.</li>

</ul>

Move objects also provide four mutator methods that allow you to change the information stored inside:

<ul>

 <li>setX(int newX)–set the x-coordinate for the destination.</li>

 <li>setY(int newY)–set the y-coordinate for the destination.</li>

 <li>setPiece(Piece newPiece)–set this move’s piece at its final desired rotation.</li>

 <li>setScore(double newScore)–get the score that has been assigned for this move.</li>

</ul>

<h3><a id="user-content-brain-strategies" class="anchor" href="https://github.com/Grimlek/Course-Work/tree/master/Tetris%20Brain#brain-strategies" aria-hidden="true"></a>Brain Strategies</h3>

One nice property of the Brain interface is that it completely separates the “brain” work of figuring out what move to make next from everything else that is going on inside the program. When you write a brain class, you do not need to worry about how the board is being maintained, how the animation happens, how pieces are chosen, or anything else. Instead, when you are writing a brain, you only need to be concerned about one thing: given the current board and the current piece, where do you want that piece to end up?

When you first start JTetris, it already has a brain loaded called the LameBrain. This brain is a truly bad player that simply lets pieces drop right where they fall. You can watch it in action by starting JTetris. Before starting the game, click the “Brain active” check box before clicking the “Start” button, and then watch this AI play by itself. When letting a brain play automatically, you can turn up the speed slider to move things along, and also uncheck the “Animate fall” if you want even more speed.

The LameBrain class is implemented like this:

When the brain is activated, the given brain’s bestMove() method will be called once each time a new piece appears on the board. The brain computes a “move” which identifies the desired final position and rotation for the given piece, along with an estimate of the “cost” of this move (higher scores make the board worse for the player).

It’s pretty easy to write better brain logic, and that is your goal in this assignment. Here are some suggestions for building a better brain (or don’t look at these if you want to puzzle it out yourself):

<ul>

 <li>Height is bad.</li>

 <li>Holes are bad.</li>

 <li>Stacking more blocks on top of holes is bad.</li>

 <li>Holes that are horizontally adjacent to other holes are not quite as bad.</li>

 <li>Holes that are vertically aligned with other holes are not quite as bad.</li>

 <li>Tall 1-wide troughs are bad.</li>

 <li>1-wide troughs are not so bad if they are only 1 or 2 deep. Think about which pieces could fill a 2-deep trough–1, 2, or 3 out of the 7 pieces depending on the two sides of the trough.</li>

 <li>Concentrate on issues that are near the current top of the pile. Holes that are 10 levels below the top edge are not as important as holes that are immediately below the top edge.</li>

 <li>At some point, your brain code may end up with some arbitrary constants like 1.54 or -0.76 in it that can only be roughly optimized by hand. That is perfectly OK. If you think you’re already an expert programmer and you want to get the best possible brain, you can look into using a separate genetic algorithm to optimize the constants. And no, don’t bother asking for extra credit–if you think you can do this, you probably don’t need any extra credit to get an A in this course!</li>

</ul>

<h3><a id="user-content-testing" class="anchor" href="https://github.com/Grimlek/Course-Work/tree/master/Tetris%20Brain#testing" aria-hidden="true"></a>Testing</h3>

Unlike some previous assignments, there is no single, “right” answer that must be produced by all student brain classes. Instead, you will have to judge the value of your own brain class by how well you think it performs “in action” in real games. Nevertheless, once you get a basic brain functioning, you will notice poor moves that you want it to avoid. Use these as the basis for test cases–with the desired move as the expected outcome.

By the time you are done, you will have to thoroughly test all aspects of the logic you have implemented in your CleverBrain. In writing test cases, you may find the following Piece methods useful:

<ul>

 <li>int style()–returns the kind of piece this is, which will be one of the following predefined values:

  <ol>

   <li>Piece.T</li>

   <li>Piece.SQUARE</li>

   <li>Piece.STICK</li>

   <li>Piece.LEFT_L</li>

   <li>Piece.RIGHT_L</li>

   <li>Piece.LEFT_DOG</li>

   <li>Piece.RIGHT_DOG</li>

  </ol></li>

</ul>

These names represent the numbers 0 through 6 and correspond to the seven Tetris pieces described earlier. The symbolic names makes it easier to write more readable code than if we just used numbers alone.

<ul>

 <li>Piece Piece.getPiece(int style, int rotation)–returns the specified piece, where style is one of the 7 predefined piece styles listed above, and rotation is the number of 90° counterclockwise rotations to apply (starting with zero). Note that this method is static (just like the play() method you wrote earlier), which means you don’t have to have a piece object to call it; instead you prefix the method with the class name (myPiece = Piece.getPiece( … );).</li>

</ul>

In addition, you might find the following Board methods useful:

<ul>

 <li>int apply(Move m)–apply a given move to a board to generate the corresponding final board state.</li>

 <li>Board(int width, int height, String…)–this constructor takes a board size plus a series of strings as its arguments, and generates the corresponding board state. It makes it very easy to write board descriptions in test cases.</li>

</ul>

Although we have not discussed methods that take variable numbers of arguments yet, don’t let their appearance in this Board constructor scare you–it is really just a convenient shorthand to allow us to “write out” board positions in a simple way.

<h3><a id="user-content-requirements" class="anchor" href="https://github.com/Grimlek/Course-Work/tree/master/Tetris%20Brain#requirements" aria-hidden="true"></a>Requirements</h3>

<ul>

 <li>Make sure you have used the name CleverBrain for your solution class.</li>

 <li>Your brain class must implement Brain.</li>

 <li>You must provide a default constructor for your brain (that is, a constructor that takes absolutely no parameters).</li>

 <li>Your brain class must provide an implementation of bestMove() that matches the signature defined in the Brain interface.</li>

 <li>Your brain implementation must include a loop that plays a meaningful role in your strategy, and must include an if statement that also plays a meaningful role. If your code only uses what is shown in this assignment without providing any useful logic of your own devising, do not expect to receive full credit. However, you aren’t being graded on the strength of your brain as a Tetris player–only your expression of your own unique logic written within your brain class. Don’t worry about testing or commenting the LameBrain or Main classes in your code–these won’t be included when you submit to Web-CAT.</li>

</ul>

5/5 - (2 votes)

<pre><span class="pl-k">public</span> <span class="pl-k">interface</span> <span class="pl-en">Brain</span>{    <span class="pl-k">public</span> <span class="pl-k">void</span> <span class="pl-en">bestMove</span>(        <span class="pl-smi">Board</span> <span class="pl-v">board</span>, <span class="pl-smi">Piece</span> <span class="pl-v">piece</span>, <span class="pl-k">int</span> <span class="pl-v">heightLmit</span>, <span class="pl-smi">Move</span> <span class="pl-v">move</span>);}</pre>

<pre><span class="pl-k">import</span> <span class="pl-smi">cs5044.tetris.*</span>;<span class="pl-k">public</span> <span class="pl-k">class</span> <span class="pl-en">NoBrainer</span>    <span class="pl-k">implements</span> <span class="pl-e">Brain</span>{    <span class="pl-k">public</span> <span class="pl-k">void</span> <span class="pl-en">bestMove</span>(        <span class="pl-smi">Board</span> <span class="pl-v">board</span>, <span class="pl-smi">Piece</span> <span class="pl-v">piece</span>, <span class="pl-k">int</span> <span class="pl-v">heightLimit</span>, <span class="pl-smi">Move</span> <span class="pl-v">move</span>)    {        <span class="pl-c">// ... some code here to chose the right move ...</span>    }}</pre>