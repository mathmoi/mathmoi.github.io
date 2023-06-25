---
# the default layout is 'page'
title: FEN Database
icon: fas fa-database
order: 3
---

Back in 2003, I collaborated with a [friend](http://stevenpigeon.org/) to generate a collection of chess positions in [FEN](http://en.wikipedia.org/wiki/Forsyth%E2%80%93Edwards_Notation) format. Back then we were creating this collection to test the efficiency of the Zobrist hash function for chess positions. Since we only needed the pieces positions on the board we only kept the first field of the FEN notation.

In 2005 [I mentioned](https://groups.google.com/d/msg/rec.games.chess.computer/AsqSx9OZDZs/UAqwhlaoFNsJ) on [rec.games.chess.computer](https://groups.google.com/forum/#!forum/rec.games.chess.computer) that I had such a database and since then a couple of persons contacted me by email asking me for a copy of the database. Unfortunately, most of them could not make use of it because important information about the positions (such as whose side is to play next) were missing.

This week, someone contacted me again asking for the database and I decided I would build it again, this time keeping all FEN fields.

The database is created using all the PGN files I have collected over time. I also updated my [TWIC](https://groups.google.com/forum/#!forum/rec.games.chess.computer) collection up to last week before I started. I used the pgn2fen.exe tool available on [Tim Foden’s site](http://www.7sun.com/chess/index.php) to create huge files containing every FEN positions of every games in my PGN files. I then used the GNU sort utility to sort the files and remove any duplicate lines. That gave me a file with 286,623,962 unique positions, weighting 16.8 gb. I used the GNU split tool to split this file in five more « manageable » files. I used the round robin distribution capabilities of split so that each file would contains position representative of the whole collection even if they are sorted. Lastly, I compressed the files with 7za, because it was the most effective and free compression method I found in my (limited) researches. The files went from 3.4 gb to 0.65 gb each for a total of 3.3 gb.

You can dowload each separate files below.

 - [unique00.zip](http://b.mathieupage.com/fendb/unique00.zip)
 - [unique01.zip](http://b.mathieupage.com/fendb/unique01.zip)
 - [unique02.zip](http://b.mathieupage.com/fendb/unique02.zip)
 - [unique03.zip](http://b.mathieupage.com/fendb/unique03.zip)
 - [unique04.zip](http://b.mathieupage.com/fendb/unique04.zip)