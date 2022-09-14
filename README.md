# GomokuWebApp

Usage
Example usage of this API:

Plays moves
Black's turn (inferred from gameboard - API plays Black unless White has more locations on the board)
```
Black ? ? ? ?
? White ? ? ?
? ? Black ? ?
? ? ? Black ?
? ? ? ? Black
```
White's is blocking by placing in the top right position, i.e. position 2)
```
curl -X POST 
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
	"gameBoard": [
        "Black",
        "?",
        "?",
        "?",
        "White",
        "?",
        "White",
        "?",
        "X"
    ]
}'
Response:

{
  "move": 2,
  "azurePlayerSymbol": "Black",
  "winner": "inconclusive",
  "winPositions": null,
  "gameBoard": [
    "Black",
    "?",
    "White",
    "?",
    "White",
    "?",
    "White",
    "?",
    "Black"
  ]
}
```
Which translates to:
```
Black  ?      Black
?      White  ?
White  ?      Black 
```
Black also has two threats in positions 1 and 5. White can't block both, so victory is all but assured for Black.

Checks for win conditions
This board has Black as the winner:
```
Black Black Black Black Black
White White ?     White White
?     ?     ?     Black ?
Request:

curl -X POST \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
	"gameBoard": [
        "Black",
        "Black",
        "Black",
        "Black",
        "Black",
        "White",
        "White",
        "?",
        "?",
        "?",
        "?"
    ]
}'
Response:

{
  "move": null,
  "azurePlayerSymbol": "O",
  "winner": "X",
  "winPositions": [
    0,
    1,
    2,
	3,
	4
  ],
  "gameBoard": [
    "Black",
     "Black",
     "Black",
     "Black",
     "Black",
     "White",
     "White",
     "?",
     "?",
     "?",
     "?"
  ]
}
```
This API detects that Black won using the top row (0, 1, 2,3,4), so it doesn't play and merely declares Black to be the winner.

Invalid game board
It is impossible for a game board like the one below to occur:
```
Black Black ? ? ?
?     ?     ? ? ? 
?     ?     ? ? ?
```
there are 2 Black's and no White's, That means White's turn must have been skipped once.


Sample bad request:
```
curl -X POST \
  -H 'cache-control: no-cache' \
  -H 'content-type: application/json' \
  -d '{
	"gameBoard": [
        "Black",
        "Black",
        "?",
        "?",
        "?",
        "?",
        "?",
        "?",
        "?"
    ]
}'
Response: 400 (Bad Request)

{
  "message": "Cannot skip turns. Black has 2 more plays than White, indicating skipping of the latter's turn(s).",
  "code": 1
}
```
