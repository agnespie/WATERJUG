# WATERJUG
# Resolving the water-jug problem with prolog
:office: Universidad de Huelva (UHU)  
:calendar: Curso 2019-2020  
:mortar_board: Knowledge Representation  
:octocat: Agnes Pieczonka
## State Problems- Problem of water gallons 
<p align="center">
  <img width="300" height="300" src="waterjug.png">

How to obtain exactly 4 gallons of water available:
5 gallons	and 3 gallons.

To find the state of the problem, of the water jug, we need to find the
minimum information of the fill state of the two different water gallons.
G5 represents the number of gallons in 5 gallons bottle.
G3 represents the number of gallons in 3 gallons bottle.

## States(G5, G3)
1. Represent the state.
*state( G5, G3 ).*                              
Always focus on the order. G5 is mentioned first. This means you have to stick to that order in the following. Using state definition we need to define what is the initial state. 
The initial state indicates empty bottles.
```
state(0, 0). 
```
After fill G5 the state will be (5, 0). Now you can see bottle G5 is filled to it’s fullest G3 is still empty.
```
state(5, 0).            
```
## Movements
This problem is only able to be solved by having states such as movements.      
Here are the movements which are all possible.
*[fillG5,fillG3,emptyG5,putG3inG5,fillG3,putG3inG5, emptyG5,putG3inG5,fillG3,putG3inG5]*
And these movements need to be defined.


             1. Fill G5
		 2. Fill G3
		 3. Empty G5
		 4. Empty G3
		 5. Put G5 content in G3
		 6. Put G3 content in G5
## Filling movements      
The structure of the movement will always be the same. That’s the general way of describing any movement. mov(MovName, StateBefore, StateAfter). Starting with a movement by filling G5. Notice, that the solution for this problem will be a list of movements to reach the final state it is a big sequence of movements. The state before filling G5 is “whatever” because we do not know what exactly was in that bottle. That’s why it is kept empty in the brackets with this (_ ) symbol . The state after is known, the bottle is full now. That’s why we can put a 5 at that place.     
```
         mov(fillG5, state(_, G3), state(5, G3) ).   
```
Here the same thing appears only for the bottle G3.    
```
         mov(fillG3, state(G5, _),  state(G5, 3) ).      
```
## Emptying movements
The next movement of this is to empty G5.
First you tell the program what movement you want to do, next step the two movements (before and after). You do not know what content was in G5 before that’s why you put “whatever” again, but you know the state afterwards. It must be 0.     
```
         mov(emptyG5, state(_, G3), state(0, G3) ).     
```
Here again it is the other way around, we do the “same” movement only with G3.
```
         mov(emptyG3, state(G5, _), state(G5, 0) ).    
```
## Shifting of contents
For the next movement to put the content of G5 in G3 we have two different options. Either when the content of both bottles will be more or equal than the 3 gallons ((G5+G3) > 3) or the option it will be less ((G5+G3) =< 3). Focusing now on the G5 + G3 =< 3. Again in the “before” state we can put “whatever” (_,_) or we just put the variable of the two bottles. The “after” state has to be 0 at the place of the G5 bottle and at the place of the G3 bottle G5+G3 occurs named with a different variable T.
```
        mov(putG5inG3, state(G5, G3), state(0, T) ):-
        T is G3 + G5, 
        T =< 3. 
```    
In the other option ((G5+G3) > 3) In G5 remains the total of G3+G5-3, because whatever content was in G3 plus we fill whatever content was in G5 in the bottle G3 (there only fit 3 gallon in there) that’s why we subtract from the total the 3 and whatever remains then, is the content which is to find in G5. The variable T describes the total content of G5 and G3. Have the rest what didn’t fit in the bottle of G3 (clarified with the variable G52, T-3 or (G3+G5)-3) remaining in G5.
``` 
        mov(putG5inG3, state(G5, G3), state(G52, 3) ):-
        T is G3 + G5,       
        T > 3,         
        G52 is T - 3.                                    
``` 
in this next coming problem, we have it the other way around. Here we need to put the content of G3 in G5. This means if T is smaller than 5 it all fits into G5.
``` 
         mov(putG3inG5, state(G5, G3), state(T, 0) ):-
         T is G3 + G5,                                                      
           T =< 5.
``` 
But if T is greater than 5 we have the same logical problem as before with an remaining part. To solve as before we fill all the contents from G3 to G5. Left in G3 is T-5 because 5 is the Maximum what will fit in the G5 bottle.
``` 
          mov(putG3inG5, state(G5, G3), state(5, G32) ):-
          T is G3 + G5,
          T > 5,   
          G32 is T - 5.          
``` 
## Final State
Final State the goal is to obtain exactly 4 gallons by shifting the contents around. Accomplishing this there will be (movements -> new states-> movements…) till the final state is reached.
```
          state(4, _).
```
For going the path (the path is a list of movements (Path = [fillG5, putG5inG3, emptyG5, ...])) to end up having the final state, the order is to start by a state with a movement, to not end up in a same state we need to indicate the term “visited” to let the program know, we do not want to have a state we have already had/ visited. And this we let the program now by the term “\+ member” which means “not” as the new state is not already been seen/visited. the first row shows the minimalized movements which are necessary to reach the final state. If I start in initial state by “final state” I do not need a movement that’s why it is hold empty ([]) and the visited position is “whatever”. In the last row we can see how the program solves the problem when not starting from the final state. it will let us know the whole path (list of movement which are necessary to reach the final state(4, 0).
```
          path(FinalState, FinalState, _, []).
          path(InitialState, FinalState, Visited, [Mov|Path] ):-
          mov(Mov, InitialState, NewState),
           \+ member(NewState, Visited), /* \+ = not */
           path(NewState, FinalState, [NewState|Visited], Path).
```           
