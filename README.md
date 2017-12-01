# AdventOfCode-2017
My solutions to the 2017 [Advent of Code](http://adventofcode.com/2017) challenges, dumped into one list with explanations.

Each solution is written in Javascript, and executed in the browser console of the webpage that opens when you ask Advent of Code for your puzzle input.

## Day 1
### Part 1
`document.body.innerText.replace(/[^\d]+/g,'').split('').filter((d,i,a)=>d==a[(i+1)%a.length]).reduce((t,v)=>t+parseInt(v),0)`

Takes the text from the page, keeps only the numbers, splits into an array, keeps only the items where the next one in the list (wrapping from end to front if needed) is the same, and sums what remains.
### Part 2
`document.body.innerText.replace(/[^\d]+/g,'').split('').filter((d,i,a)=>d==a[(i+a.length/2)%a.length]).reduce((t,v)=>t+parseInt(v),0)`

Same trick, but look ahead by half of the length of the list, rather than looking one element ahead.
