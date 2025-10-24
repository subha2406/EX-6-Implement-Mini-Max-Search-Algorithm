<h1>ExpNo 6 : Implement Minimax Search Algorithm for a Simple TIC-TAC-TOE game</h1> 
<h3>Name: SUBHA SHREE U     </h3>
<h3>Register Number:  2305002025     </h3>
<H3>Aim:</H3>
<p>
    Implement Minimax Search Algorithm for a Simple TIC-TAC-TOE game
</p>

<H3>Theory and Procedure:</H3>
To begin, let's start by defining what it means to play a perfect game of tic tac toe:

If I play perfectly, every time I play I will either win the game, or I will draw the game. Furthermore if I play against another perfect player, I will always draw the game.

How might we describe these situations quantitatively? Let's assign a score to the "end game conditions:"

I win, hurray! I get 10 points!
I lose, shit. I lose 10 points (because the other player gets 10 points)
I draw, whatever. I get zero points, nobody gets any points.
So now we have a situation where we can determine a possible score for any game end state.

Looking at a Brief Example
To apply this, let's take an example from near the end of a game, where it is my turn. I am X. My goal here, obviously, is to maximize my end game score.


<img width="607" height="458" alt="image" src="https://github.com/user-attachments/assets/64011997-59d9-4b7b-89dd-9e4929d9c9eb" />



If the top of this image represents the state of the game I see when it is my turn, then I have some choices to make, there are three places I can play, one of which clearly results in me wining and earning the 10 points. If I don't make that move, O could very easily win. And I don't want O to win, so my goal here, as the first player, should be to pick the maximum scoring move.

But What About O?
What do we know about O? Well we should assume that O is also playing to win this game, but relative to us, the first player, O wants obviously wants to chose the move that results in the worst score for us, it wants to pick a move that would minimize our ultimate score. Let's look at things from O's perspective, starting with the two other game states from above in which we don't immediately win:

<img width="608" height="460" alt="image" src="https://github.com/user-attachments/assets/3a79efaf-38ef-45ad-9152-309d3945b5e3" />



The choice is clear, O would pick any of the moves that result in a score of -10.

Describing Minimax
The key to the Minimax algorithm is a back and forth between the two players, where the player whose "turn it is" desires to pick the move with the maximum score. In turn, the scores for each of the available moves are determined by the opposing player deciding which of its available moves has the minimum score. And the scores for the opposing players moves are again determined by the turn-taking player trying to maximize its score and so on all the way down the move tree to an end state.

A description for the algorithm, assuming X is the "turn taking player," would look something like:

If the game is over, return the score from X's perspective.
Otherwise get a list of new game states for every possible move
Create a scores list
For each of these states add the minimax result of that state to the scores list
If it's X's turn, return the maximum score from the scores list
If it's O's turn, return the minimum score from the scores list
You'll notice that this algorithm is recursive, it flips back and forth between the players until a final score is found.

Let's walk through the algorithm's execution with the full move tree, and show why, algorithmically, the instant winning move will be picked:

<img width="620" height="483" alt="image" src="https://github.com/user-attachments/assets/2bac8a36-3574-4748-bb42-131d4686c883" />


<ul>
<li>It's X's turn in state 1. X generates the states 2, 3, and 4 and calls minimax on those states.</li>
<li>State 2 pushes the score of +10 to state 1's score list, because the game is in an end state.</li>
<li>State 3 and 4 are not in end states, so 3 generates states 5 and 6 and calls minimax on them, while state 4 generates states 7 and 8 and calls minimax on them.</li>
<li>State 5 pushes a score of -10 onto state 3's score list, while the same happens for state 7 which pushes a score of -10 onto state 4's score list.</li>
<li>State 6 and 8 generate the only available moves, which are end states, and so both of them add the score of +10 to the move lists of states 3 and 4.</li>
<li>Because it is O's turn in both state 3 and 4, O will seek to find the minimum score, and given the choice between -10 and +10, both states 3 and 4 will yield -10.</li>
<li>>Finally the score list for states 2, 3, and 4 are populated with +10, -10 and -10 respectively, and state 1 seeking to maximize the score will chose the winning move with score +10, state 2.</li
</ul>
##A Coded Version of Minimax Hopefully by now you have a rough sense of how th e minimax algorithm determines the best move to play. Let's examine my implementation of the algorithm to solidify the understanding:

Here is the function for scoring the game:


## Program
```python
import math,time

def win(b,p):
    for i in range(3):
        if all(b[i][j]==p for j in range(3)) or all(b[j][i]==p for j in range(3)): return True
    return b[0][0]==b[1][1]==b[2][2]==p or b[0][2]==b[1][1]==b[2][0]==p

def full(b): return all(c!="." for r in b for c in r)

def show(b):
    for r in b: print("|".join(r))
    print()

def minimax(b,maxi):
    if win(b,"X"): return 1
    if win(b,"O"): return -1
    if full(b): return 0
    if maxi:
        best=-math.inf
        for i in range(3):
            for j in range(3):
                if b[i][j]==".":
                    b[i][j]="X"
                    best=max(best,minimax(b,False))
                    b[i][j]="."
        return best
    else:
        best=math.inf
        for i in range(3):
            for j in range(3):
                if b[i][j]==".":
                    b[i][j]="O"
                    best=min(best,minimax(b,True))
                    b[i][j]="."
        return best

def best_move(b):
    mv=(-1,-1); val=-math.inf
    for i in range(3):
        for j in range(3):
            if b[i][j]==".":
                b[i][j]="X"
                v=minimax(b,False)
                b[i][j]="."
                if v>val: mv,val=(i,j),v
    return mv

b=[["."]*3 for _ in range(3)]
while True:
    show(b)
    if win(b,"X"): print("X wins!"); break
    if win(b,"O"): print("O wins!"); break
    if full(b): print("It's a draw!"); break

    t=time.time()
    x,y=best_move(b)
    print(f"Evaluation time: {round(time.time()-t,4)}s")
    print(f"Recommended move: X = {x}, Y = {y}")
    b[x][y]="X"
    show(b)

    if win(b,"X"): print("X wins!"); break
    if full(b): print("It's a draw!"); break

    x=int(input("Insert the X coordinate: "))
    y=int(input("Insert the Y coordinate: "))
    if b[x][y]==".": b[x][y]="O"
```

<hr>
<h2>Input and Output</h2>
<img width="391" height="592" alt="image" src="https://github.com/user-attachments/assets/91afbcd3-8727-4602-adee-251d294183fc" />

<img width="426" height="383" alt="image" src="https://github.com/user-attachments/assets/fc093f32-e0c0-4167-aaba-5a79e7db8adc" />




<hr>
<h2>Result:</h2>
<p>Thus,Implementation of  Minimax Search Algorithm for a Simple TIC-TAC-TOE game wasa done successfully.</p>
